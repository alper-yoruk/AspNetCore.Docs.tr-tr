---
title: ASP.NET Core için gRPC'de kimlik doğrulama ve yetkilendirme
author: jamesnk
description: ASP.NET Core için gRPC'de kimlik doğrulama ve yetkilendirmeyi nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: c0312b186bbb35e3b802984484b7213016d8bf04
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78964435"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>ASP.NET Core için gRPC'de kimlik doğrulama ve yetkilendirme

Yazar: [James Newton-King](https://twitter.com/jamesnk)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(nasıl indirilir)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>gRPC hizmetini çağıran kullanıcıların kimliğini doğrula

gRPC, bir kullanıcıyı her aramayla ilişkilendirmek için [ASP.NET Çekirdek kimlik doğrulaması](xref:security/authentication/identity) ile kullanılabilir.

Aşağıda gRPC `Startup.Configure` ve ASP.NET Core kimlik doğrulaması kullanan bir örnek verilmiştir:

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> ASP.NET Core kimlik doğrulama aracını kaydetme sırası önemlidir. Her `UseAuthentication` zaman `UseAuthorization` `UseRouting` arayın `UseEndpoints`ve sonra ve önce .

Uygulamanızın arama sırasında kullandığı kimlik doğrulama mekanizmasının yapılandırılması gerekir. Kimlik doğrulama yapılandırması `Startup.ConfigureServices` eklenir ve uygulamanızın kullandığı kimlik doğrulama mekanizmasına bağlı olarak farklı olacaktır. ASP.NET Core uygulamalarının nasıl güvenli hale ASP.NET örnekler için kimlik [doğrulama örneklerine](xref:security/authentication/samples)bakın.

Kimlik doğrulama kurulduktan sonra, kullanıcıya gRPC hizmet yöntemleri yle `ServerCallContext`erişilebiliyor.

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Taşıyıcı belirteç kimlik doğrulaması

İstemci kimlik doğrulaması için bir erişim belirteci sağlayabilir. Sunucu belirteci doğrular ve kullanıcıtanımlamak için kullanır.

Sunucuda, taşıyıcı belirteç kimlik doğrulaması [JWT Bearer ara ware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)kullanılarak yapılandırılır.

.NET gRPC istemcisinde, belirteç üstbilgi olarak çağrılarla gönderilebilir:

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

Bir `ChannelCredentials` kanalda yapılandırma, gRPC çağrıları ile hizmete belirteci göndermek için alternatif bir yoldur. Kimlik bilgileri, gRPC çağrısı her yapıldığında çalıştırılır ve bu da belirteci kendiniz geçirmek için birden çok yerde kod yazma gereksinimini önler.

Aşağıdaki örnekteki kimlik bilgisi, her gRPC çağrısıyla belirteci gönderecek şekilde kanalı yapılandırır:

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>İstemci sertifikası kimlik doğrulaması

İstemci alternatif olarak kimlik doğrulaması için bir istemci sertifikası sağlayabilir. [Sertifika kimlik doğrulaması](https://tools.ietf.org/html/rfc5246#section-7.4.4) TLS düzeyinde, ASP.NET Core'a gelmeden çok önce gerçekleşir. İstek ASP.NET Core'a girdiğinde, [istemci sertifikası kimlik doğrulama paketi](xref:security/authentication/certauth) `ClaimsPrincipal`sertifikayı bir .

> [!NOTE]
> Ana bilgisayar, istemci sertifikalarını kabul edecek şekilde yapılandırılmalıdır. Kerkenez, IIS ve Azure'da istemci sertifikalarını kabul etme hakkında bilgi almak için [ev sahibinizi sertifika gerektirecek şekilde yapılandırın.](xref:security/authentication/certauth#configure-your-host-to-require-certificates)

.NET gRPC istemcisinde, istemci sertifikası `HttpClientHandler` daha sonra gRPC istemcisini oluşturmak için kullanılır:

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>Diğer kimlik doğrulama mekanizmaları

Birçok ASP.NET Core destekli kimlik doğrulama mekanizmaları gRPC ile çalışır:

* Azure Active Directory
* Müşteri Sertifikası
* IdentityServer
* JWT Belirteci
* OAuth 2.0
* OpenID Connect
* WS-Federation

Sunucuda kimlik doğrulamayapılandırma hakkında daha fazla bilgi için [ASP.NET Core kimlik doğrulaması'na](xref:security/authentication/identity)bakın.

gRPC istemcisini kimlik doğrulaması kullanacak şekilde yapılandırmak, kullandığınız kimlik doğrulama mekanizmasına bağlıdır. Önceki taşıyıcı belirteci ve istemci sertifikası örnekleri, gRPC istemcisinin gRPC çağrılarıyla kimlik doğrulama meta verilerini göndermek üzere yapılandırılabildiği birkaç yolu gösterir:

* Güçlü bir şekilde yazılan gRPC istemcileri dahili olarak kullanır. `HttpClient` Kimlik [doğrulama, HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)üzerinde veya özel [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) örnekleri ekleyerek `HttpClient`yapılandırılabilir.
* Her gRPC araması `CallOptions` isteğe bağlı bir bağımsız değişkene sahiptir. Özel üstbilgi seçeneğin üstbilgi koleksiyonu kullanılarak gönderilebilir.

> [!NOTE]
> Windows Kimlik Doğrulama (NTLM/Kerberos/Negotiate) gRPC ile kullanılamaz. gRPC HTTP/2 gerektirir ve HTTP/2 Windows Kimlik Doğrulama'yı desteklemez.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Kullanıcılara hizmetlere ve hizmet yöntemlerine erişim yetkisi verme

Varsayılan olarak, bir hizmetteki tüm yöntemler kimliği doğrulanmamış kullanıcılar tarafından çağrılabilir. Kimlik doğrulaması gerektirmek [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) için hizmete özniteliği uygulayın:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Yalnızca belirli yetkilendirme ilkeleriyle eşleşen `[Authorize]` kullanıcılara erişimi kısıtlamak için öznitelik oluşturucu bağımsız [değişkenlerini](xref:security/authorization/policies)ve özelliklerini kullanabilirsiniz. Örneğin, adı verilen `MyAuthorizationPolicy`özel bir yetkilendirme ilkesiniz varsa, yalnızca bu iii eşleşen kullanıcıların aşağıdaki kodu kullanarak hizmete erişebilmesini sağlayın:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Tek tek hizmet `[Authorize]` yöntemleri özniteliği de uygulanabilir. Geçerli kullanıcı hem yönteme **hem de** sınıfa uygulanan ilkelerle eşleşmiyorsa, arayana bir hata döndürülür:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core'da Taşıyıcı Belirteç kimlik doğrulaması](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [İstemci Sertifikası kimlik doğrulamasını ASP.NET Core'da yapılandırma](xref:security/authentication/certauth)
