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
# <a name="test-aspnet-core-middleware"></a>Test ASP.NET Core ara yazılımı

[Chris](https://github.com/Tratcher) 'e göre

Ara yazılım ile yalıtımta test edilebilir <xref:Microsoft.AspNetCore.TestHost.TestServer> . Şunları yapmanıza olanak sağlar:

* Yalnızca test etmeniz gereken bileşenleri içeren bir uygulama işlem hattı oluşturun.
* Ara yazılım davranışını doğrulamak için özel istekler gönderin.

Artılar:

* İstekler ağ üzerinden serileştirilmesi yerine bellek içinde gönderilir.
* Bu, bağlantı noktası yönetimi ve HTTPS sertifikaları gibi ek sorunları önler.
* Ara yazılım içindeki özel durumlar doğrudan çağıran teste akabilir.
* Doğrudan testte gibi sunucu veri yapılarını özelleştirmek mümkündür <xref:Microsoft.AspNetCore.Http.HttpContext> .

## <a name="set-up-the-testserver"></a>TestServer 'ı ayarlama

Test projesinde bir test oluşturun:

* Tarafından kullanılan bir konak oluşturun ve başlatın <xref:Microsoft.AspNetCore.TestHost.TestServer> .
* Ara yazılım tarafından kullanılan gerekli hizmetleri ekleyin.
* Projeye [Microsoft. AspNetCore. TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet paketini ekleyin:
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* Test için ara yazılımı kullanmak üzere işleme işlem hattını yapılandırın.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>HttpClient ile istek gönderme
Şunu kullanarak bir istek gönder <xref:System.Net.Http.HttpClient> :

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

Sonucu onaylama. İlk olarak, bir onaylama işlemi beklediğinizi sonucun tersini yapın. Hatalı pozitif bir onaylama işlemi olan ilk çalıştırma, ara yazılım doğru çalışırken testin başarısız olduğunu onaylar. Testi çalıştırın ve testin başarısız olduğunu onaylayın.

Aşağıdaki örnekte, kök uç noktası istendiğinde, ara yazılım 404 durum kodu döndürmelidir (*bulunamadı*). Başarısız olması gereken ilk testi çalıştırın `Assert.NotEqual( ... );` :

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

Normal çalışma koşullarında ara yazılımı test etmek için onayı değiştirin. Son test kullanır `Assert.Equal( ... );` . Başarılı olduğunu onaylamak için testi yeniden çalıştırın.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>HttpContext ile istek gönderme

Bir test uygulaması, [Sendadsync (Action \<HttpContext> , CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A)kullanarak da bir istek gönderebilir. Aşağıdaki örnekte, `https://example.com/A/Path/?and=query` Ara yazılım tarafından işlendiğinde birkaç denetim yapılır:

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

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A><xref:Microsoft.AspNetCore.Http.HttpContext>soyutlamalar kullanmak yerine bir nesnenin doğrudan yapılandırılmasına izin verir <xref:System.Net.Http.HttpClient> . <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>Yalnızca sunucuda kullanılabilir olan, [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) veya [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features)gibi yapıları işlemek için kullanın.

Önceki örnekte olduğu gibi, *404-olmayan* bir yanıt için test edilmiş, önceki testteki her bir deyimin tersini denetleyin `Assert` . Denetim, ara yazılım normal olarak çalışırken testin doğru şekilde başarısız olduğunu onaylar. Yanlış pozitif testin çalışıp çalışmadığını onayladıktan sonra, `Assert` beklenen koşulların ve test değerlerinin son deyimlerini ayarlayın. Testin başarılı olduğunu onaylamak için yeniden çalıştırın.

## <a name="testserver-limitations"></a>TestServer sınırlamaları

TestServer:

* , Sunucu davranışlarını test eden yazılım için çoğaltmak üzere oluşturulmuştur.
* Tüm davranışları ***çoğaltmayı denemez*** <xref:System.Net.Http.HttpClient> .
* İstemci erişimine mümkün olduğunca çok denetim sağlamak ve sunucu üzerinde mümkün olduğunca fazla görünürlük sağlamak için denemeler yapmaya çalışır. Örneğin, `HttpClient` sunucu durumunu doğrudan iletmek için normalde tarafından oluşturulan özel durumlar oluşturabilir.
* Genellikle ara yazılım ile ilgili olmadığından, bazı aktarıma özgü üstbilgiler varsayılan olarak ayarlanamaz. Daha fazla bilgi için sonraki bölüme bakın.

### <a name="content-length-and-transfer-encoding-headers"></a>Content-Length ve Transfer-Encoding üstbilgileri

TestServer, taşıma ile ilgili istek ya da [Içerik uzunluğu](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) veya [aktarım kodlaması](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding)gibi yanıt üst ***bilgileri ayarlanmamış.*** Kullanımları istemci, senaryo ve protokole göre değiştiğinden uygulamalar bu üstbilgilere bağlı olarak kaçınmalıdır. `Content-Length`Ve `Transfer-Encoding` belirli bir senaryoyu test etmek için gerekliyse, veya oluştururken test içinde belirtilenebilir <xref:System.Net.Http.HttpRequestMessage> <xref:Microsoft.AspNetCore.Http.HttpContext> . Daha fazla bilgi için bkz. aşağıdaki GitHub sorunları:

* [DotNet/aspnetcore # 21677](https://github.com/dotnet/aspnetcore/issues/21677)
* [DotNet/aspnetcore # 18463](https://github.com/dotnet/aspnetcore/issues/18463)
* [DotNet/aspnetcore # 13273](https://github.com/dotnet/aspnetcore/issues/13273)
