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
# <a name="test-aspnet-core-middleware"></a>Test ASP.NET Core ara yazılımı

[Chris](https://github.com/Tratcher) 'e göre

Ara yazılım ile <xref:Microsoft.AspNetCore.TestHost.TestServer>yalıtımta test edilebilir. Şunları yapmanıza olanak sağlar:

* Yalnızca test etmeniz gereken bileşenleri içeren bir uygulama işlem hattı oluşturun.
* Ara yazılım davranışını doğrulamak için özel istekler gönderin.

Artılar:

* İstekler ağ üzerinden serileştirilmesi yerine bellek içinde gönderilir.
* Bu, bağlantı noktası yönetimi ve HTTPS sertifikaları gibi ek sorunları önler.
* Ara yazılım içindeki özel durumlar doğrudan çağıran teste akabilir.
* Doğrudan testte gibi sunucu veri yapılarını <xref:Microsoft.AspNetCore.Http.HttpContext>özelleştirmek mümkündür.

## <a name="set-up-the-testserver"></a>TestServer 'ı ayarlama

Test projesinde bir test oluşturun:

* Tarafından kullanılan <xref:Microsoft.AspNetCore.TestHost.TestServer>bir konak oluşturun ve başlatın.
* Ara yazılım tarafından kullanılan gerekli hizmetleri ekleyin.
* Test için ara yazılımı kullanmak üzere işleme işlem hattını yapılandırın.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>HttpClient ile istek gönderme
Şunu kullanarak <xref:System.Net.Http.HttpClient>bir istek gönder:

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

Sonucu onaylama. İlk olarak, bir onaylama işlemi beklediğinizi sonucun tersini yapın. Hatalı pozitif bir onaylama işlemi olan ilk çalıştırma, ara yazılım doğru çalışırken testin başarısız olduğunu onaylar. Testi çalıştırın ve testin başarısız olduğunu onaylayın.

Aşağıdaki örnekte, kök uç noktası istendiğinde, ara yazılım 404 durum kodu döndürmelidir (*bulunamadı*). Başarısız olması gereken ilk testi çalıştırın `Assert.NotEqual( ... );`:

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

Normal çalışma koşullarında ara yazılımı test etmek için onayı değiştirin. Son test kullanır `Assert.Equal( ... );`. Başarılı olduğunu onaylamak için testi yeniden çalıştırın.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>HttpContext ile istek gönderme

Bir test uygulaması, [Sendadsync (eylem\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A)kullanarak da bir istek gönderebilir. Aşağıdaki örnekte, ara yazılım tarafından işlendiğinde birkaç denetim yapılır `https://example.com/A/Path/?and=query` :

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

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>soyutlamalar kullanmak yerine bir <xref:Microsoft.AspNetCore.Http.HttpContext> nesnenin doğrudan yapılandırılmasına izin verir. <xref:System.Net.Http.HttpClient> Yalnızca <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> sunucuda kullanılabilir olan, [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) veya [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features)gibi yapıları işlemek için kullanın.

Önceki örnekte olduğu gibi, *404-olmayan* bir yanıt için test edilmiş, önceki testteki her `Assert` bir deyimin tersini denetleyin. Denetim, ara yazılım normal olarak çalışırken testin doğru şekilde başarısız olduğunu onaylar. Yanlış pozitif testin çalışıp çalışmadığını onayladıktan sonra, beklenen koşulların ve test değerlerinin son `Assert` deyimlerini ayarlayın. Testin başarılı olduğunu onaylamak için yeniden çalıştırın.
