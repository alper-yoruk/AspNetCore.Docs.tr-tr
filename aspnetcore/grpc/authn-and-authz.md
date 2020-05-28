---
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---

# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>ASP.NET Core için gRPC 'de kimlik doğrulaması ve yetkilendirme

, [James bAyKiNg](https://twitter.com/jamesnk)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>GRPC hizmetini çağıran kullanıcıların kimliğini doğrulama

gRPC, bir kullanıcıyı her çağrıyla ilişkilendirmek için [ASP.NET Core kimlik doğrulamasıyla](xref:security/authentication/identity) birlikte kullanılabilir.

Aşağıda, `Startup.Configure` gRPC ve ASP.NET Core kimlik doğrulaması kullanan bir örnek verilmiştir:

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
> ASP.NET Core kimlik doğrulama ara yazılımını kaydetme sırası önemli. Her zaman `UseAuthentication` `UseAuthorization` ve sonra `UseRouting` ve sonra çağırın `UseEndpoints` .

Bir çağrı sırasında uygulamanızın kullandığı kimlik doğrulama mekanizması yapılandırılmalıdır. Kimlik doğrulama yapılandırması ' de eklenir `Startup.ConfigureServices` ve uygulamanızın kullandığı kimlik doğrulama mekanizmasına bağlı olarak farklı olacaktır. ASP.NET Core uygulamaları güvenli hale getirmeye yönelik örnekler için bkz. [kimlik doğrulama örnekleri](xref:security/authentication/samples).

Kimlik doğrulaması kurulduktan sonra, Kullanıcı aracılığıyla bir gRPC hizmeti yöntemleriyle erişilebilir `ServerCallContext` .

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Taşıyıcı belirteç kimlik doğrulaması

İstemci, kimlik doğrulaması için bir erişim belirteci sağlayabilir. Sunucu belirteci doğrular ve kullanıcıyı tanımlamak için kullanır.

Sunucusunda, taşıyıcı belirteç kimlik doğrulaması [JWT taşıyıcı ara yazılımı](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)kullanılarak yapılandırılır.

.NET gRPC istemcisinde, belirteç aramalar ile üst bilgi olarak gönderilebilir:

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

`ChannelCredentials`Kanalı üzerinde yapılandırmak, belirteci gRPC çağrılarıyla hizmete göndermenin alternatif bir yoludur. Kimlik bilgisi her bir gRPC çağrısının yapılışında çalıştırılır. Bu, belirteci kendi kendinize geçirmek için birden çok yere kod yazma gereksinimini ortadan kaldırır.

Aşağıdaki örnekteki kimlik bilgileri, kanalı her gRPC çağrısıyla birlikte gönderecek şekilde yapılandırır:

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

İstemci alternatif olarak kimlik doğrulaması için bir istemci sertifikası sağlayabilir. [Sertifika kimlik doğrulaması](https://tools.ietf.org/html/rfc5246#section-7.4.4) TLS düzeyinde gerçekleşir ve bu süre ASP.NET Core. İstek ASP.NET Core girdiğinde, [istemci sertifikası kimlik doğrulama paketi](xref:security/authentication/certauth) , sertifikayı bir ile çözmenize olanak tanır `ClaimsPrincipal` .

> [!NOTE]
> Konağın istemci sertifikalarını kabul edecek şekilde yapılandırılması gerekir. Bkz. Kestrel, IIS ve Azure 'da istemci sertifikalarını kabul etme hakkında bilgi için bkz. [ana bilgisayarınızı yapılandırma](xref:security/authentication/certauth#configure-your-host-to-require-certificates) .

.NET gRPC istemcisinde, `HttpClientHandler` daha sonra gRPC istemcisini oluşturmak için kullanılan istemci sertifikası eklenir:

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
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>Diğer kimlik doğrulama mekanizmaları

Desteklenen birçok ASP.NET Core kimlik doğrulama mekanizması gRPC ile çalışır:

* Azure Active Directory
* İstemci sertifikası
* IdentityServer
* JWT belirteci
* OAuth 2.0
* OpenID Connect
* WS-Federation

Sunucuda kimlik doğrulamasını yapılandırma hakkında daha fazla bilgi için, [ASP.NET Core kimlik doğrulaması](xref:security/authentication/identity)' na bakın.

GRPC istemcisini kimlik doğrulaması kullanacak şekilde yapılandırmak, kullanmakta olduğunuz kimlik doğrulama mekanizmasına bağlı olarak değişir. Önceki taşıyıcı belirteci ve istemci sertifikası örnekleri, GRPC istemcisinin, gRPC çağrılarına yönelik kimlik doğrulama meta verilerini gönderecek şekilde yapılandırılabilmesinin birkaç yolunu gösterir:

* Türü kesin belirlenmiş gRPC istemcileri `HttpClient` dahili olarak kullanılır. Kimlik doğrulaması, [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)üzerinde veya öğesine özel [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) örnekleri eklenerek yapılandırılabilir `HttpClient` .
* Her gRPC çağrısının isteğe bağlı bir `CallOptions` bağımsız değişkeni vardır. Özel üstbilgiler, seçeneğin üstbilgiler koleksiyonu kullanılarak gönderilebilir.

> [!NOTE]
> Windows kimlik doğrulaması (NTLM/Kerberos/Negotiate), gRPC ile kullanılamaz. gRPC için HTTP/2 ve HTTP/2 Windows kimlik doğrulamasını desteklemez.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Kullanıcılara hizmetlere ve hizmet yöntemlerine erişim yetkisi verme

Varsayılan olarak, bir hizmette tüm yöntemler kimliği doğrulanmamış kullanıcılar tarafından çağrılabilir. Kimlik doğrulaması gerektirmek için, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özelliği hizmetine uygulayın:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

`[Authorize]`Yalnızca belirli [Yetkilendirme ilkeleriyle](xref:security/authorization/policies)eşleşen kullanıcılara erişimi kısıtlamak için özniteliğinin Oluşturucu bağımsız değişkenlerini ve özelliklerini kullanabilirsiniz. Örneğin, adlı bir özel yetkilendirme ilkeniz varsa `MyAuthorizationPolicy` , aşağıdaki kodu kullanarak yalnızca bu ilkeyle eşleşen kullanıcıların hizmete erişebildiğinden emin olun:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Bağımsız hizmet yöntemlerinin `[Authorize]` özniteliği de uygulanabilir. Geçerli Kullanıcı hem yönteme hem **de** sınıfa uygulanan ilkelerle eşleşmezse, çağırana bir hata döndürülür:

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

* [ASP.NET Core 'de taşıyıcı belirteç kimlik doğrulaması](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [ASP.NET Core 'de Istemci sertifikası kimlik doğrulamasını yapılandırma](xref:security/authentication/certauth)
