---
title: Test ASP.NET Core ara yazılımı
author: tratcher
description: ASP.NET Core ara yazılımını TestServer ile test etme hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 06ff7167e32fbd613c18709e31ecd078b3dfc926
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876439"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="6a0e8-103">Test ASP.NET Core ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="6a0e8-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="6a0e8-104">[Chris](https://github.com/Tratcher) 'e göre</span><span class="sxs-lookup"><span data-stu-id="6a0e8-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="6a0e8-105">Ara yazılım ile <xref:Microsoft.AspNetCore.TestHost.TestServer>yalıtımta test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="6a0e8-106">Şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="6a0e8-106">It allows you to:</span></span>

* <span data-ttu-id="6a0e8-107">Yalnızca test etmeniz gereken bileşenleri içeren bir uygulama işlem hattı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="6a0e8-108">Ara yazılım davranışını doğrulamak için özel istekler gönderin.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="6a0e8-109">Artılar:</span><span class="sxs-lookup"><span data-stu-id="6a0e8-109">Advantages:</span></span>

* <span data-ttu-id="6a0e8-110">İstekler ağ üzerinden serileştirilmesi yerine bellek içinde gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="6a0e8-111">Bu, bağlantı noktası yönetimi ve HTTPS sertifikaları gibi ek sorunları önler.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="6a0e8-112">Ara yazılım içindeki özel durumlar doğrudan çağıran teste akabilir.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="6a0e8-113">Doğrudan testte gibi sunucu veri yapılarını <xref:Microsoft.AspNetCore.Http.HttpContext>özelleştirmek mümkündür.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="6a0e8-114">TestServer 'ı ayarlama</span><span class="sxs-lookup"><span data-stu-id="6a0e8-114">Set up the TestServer</span></span>

<span data-ttu-id="6a0e8-115">Test projesinde bir test oluşturun:</span><span class="sxs-lookup"><span data-stu-id="6a0e8-115">In the test project, create a test:</span></span>

* <span data-ttu-id="6a0e8-116">Tarafından kullanılan <xref:Microsoft.AspNetCore.TestHost.TestServer>bir konak oluşturun ve başlatın.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="6a0e8-117">Ara yazılım tarafından kullanılan gerekli hizmetleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="6a0e8-118">Test için ara yazılımı kullanmak üzere işleme işlem hattını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="6a0e8-119">HttpClient ile istek gönderme</span><span class="sxs-lookup"><span data-stu-id="6a0e8-119">Send requests with HttpClient</span></span>
<span data-ttu-id="6a0e8-120">Şunu kullanarak <xref:System.Net.Http.HttpClient>bir istek gönder:</span><span class="sxs-lookup"><span data-stu-id="6a0e8-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="6a0e8-121">Sonucu onaylama.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-121">Assert the result.</span></span> <span data-ttu-id="6a0e8-122">İlk olarak, bir onaylama işlemi beklediğinizi sonucun tersini yapın.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="6a0e8-123">Hatalı pozitif bir onaylama işlemi olan ilk çalıştırma, ara yazılım doğru çalışırken testin başarısız olduğunu onaylar.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="6a0e8-124">Testi çalıştırın ve testin başarısız olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="6a0e8-125">Aşağıdaki örnekte, kök uç noktası istendiğinde, ara yazılım 404 durum kodu döndürmelidir (*bulunamadı*).</span><span class="sxs-lookup"><span data-stu-id="6a0e8-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="6a0e8-126">Başarısız olması gereken ilk testi çalıştırın `Assert.NotEqual( ... );`:</span><span class="sxs-lookup"><span data-stu-id="6a0e8-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="6a0e8-127">Normal çalışma koşullarında ara yazılımı test etmek için onayı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="6a0e8-128">Son test kullanır `Assert.Equal( ... );`.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="6a0e8-129">Başarılı olduğunu onaylamak için testi yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="6a0e8-130">HttpContext ile istek gönderme</span><span class="sxs-lookup"><span data-stu-id="6a0e8-130">Send requests with HttpContext</span></span>

<span data-ttu-id="6a0e8-131">Bir test uygulaması, [Sendadsync (eylem\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A)kullanarak da bir istek gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="6a0e8-132">Aşağıdaki örnekte, ara yazılım tarafından işlendiğinde birkaç denetim yapılır `https://example.com/A/Path/?and=query` :</span><span class="sxs-lookup"><span data-stu-id="6a0e8-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

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

<span data-ttu-id="6a0e8-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>soyutlamalar kullanmak yerine bir <xref:Microsoft.AspNetCore.Http.HttpContext> nesnenin doğrudan yapılandırılmasına izin verir. <xref:System.Net.Http.HttpClient></span><span class="sxs-lookup"><span data-stu-id="6a0e8-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="6a0e8-134">Yalnızca <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> sunucuda kullanılabilir olan, [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) veya [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features)gibi yapıları işlemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="6a0e8-135">Önceki örnekte olduğu gibi, *404-olmayan* bir yanıt için test edilmiş, önceki testteki her `Assert` bir deyimin tersini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="6a0e8-136">Denetim, ara yazılım normal olarak çalışırken testin doğru şekilde başarısız olduğunu onaylar.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="6a0e8-137">Yanlış pozitif testin çalışıp çalışmadığını onayladıktan sonra, beklenen koşulların ve test değerlerinin son `Assert` deyimlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="6a0e8-138">Testin başarılı olduğunu onaylamak için yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6a0e8-138">Run it again to confirm that the test passes.</span></span>
