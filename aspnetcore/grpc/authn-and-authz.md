---
title: ASP.NET Core için gRPC 'de kimlik doğrulaması ve yetkilendirme
author: jamesnk
description: ASP.NET Core için gRPC 'de kimlik doğrulama ve yetkilendirmeyi nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/authn-and-authz
ms.openlocfilehash: 833114a12c8c1ac67097b3592cf410d7a69bb628
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673984"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="a0796-103">ASP.NET Core için gRPC 'de kimlik doğrulaması ve yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="a0796-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="a0796-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a0796-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="a0796-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="a0796-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="a0796-106">GRPC hizmetini çağıran kullanıcıların kimliğini doğrulama</span><span class="sxs-lookup"><span data-stu-id="a0796-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="a0796-107">gRPC, bir kullanıcıyı her çağrıyla ilişkilendirmek için [ASP.NET Core kimlik doğrulamasıyla](xref:security/authentication/identity) birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a0796-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="a0796-108">Aşağıda, `Startup.Configure` gRPC ve ASP.NET Core kimlik doğrulaması kullanan bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="a0796-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="a0796-109">ASP.NET Core kimlik doğrulama ara yazılımını kaydetme sırası önemli.</span><span class="sxs-lookup"><span data-stu-id="a0796-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="a0796-110">Her zaman `UseAuthentication` `UseAuthorization` ve sonra `UseRouting` ve sonra çağırın `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="a0796-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="a0796-111">Bir çağrı sırasında uygulamanızın kullandığı kimlik doğrulama mekanizması yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a0796-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="a0796-112">Kimlik doğrulama yapılandırması ' de eklenir `Startup.ConfigureServices` ve uygulamanızın kullandığı kimlik doğrulama mekanizmasına bağlı olarak farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="a0796-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="a0796-113">ASP.NET Core uygulamaları güvenli hale getirmeye yönelik örnekler için bkz. [kimlik doğrulama örnekleri](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="a0796-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="a0796-114">Kimlik doğrulaması kurulduktan sonra, Kullanıcı aracılığıyla bir gRPC hizmeti yöntemleriyle erişilebilir `ServerCallContext` .</span><span class="sxs-lookup"><span data-stu-id="a0796-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="a0796-115">Taşıyıcı belirteç kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="a0796-115">Bearer token authentication</span></span>

<span data-ttu-id="a0796-116">İstemci, kimlik doğrulaması için bir erişim belirteci sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="a0796-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="a0796-117">Sunucu belirteci doğrular ve kullanıcıyı tanımlamak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="a0796-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="a0796-118">Sunucusunda, taşıyıcı belirteç kimlik doğrulaması [JWT taşıyıcı ara yazılımı](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="a0796-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="a0796-119">.NET gRPC istemcisinde, belirteç, koleksiyon kullanılarak çağrılarla gönderilebilir `Metadata` .</span><span class="sxs-lookup"><span data-stu-id="a0796-119">In the .NET gRPC client, the token can be sent with calls by using the `Metadata` collection.</span></span> <span data-ttu-id="a0796-120">`Metadata`Koleksiyondaki girişler, http üstbilgileri olarak bir gRPC çağrısıyla gönderilir:</span><span class="sxs-lookup"><span data-stu-id="a0796-120">Entries in the `Metadata` collection are sent with a gRPC call as HTTP headers:</span></span>

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

<span data-ttu-id="a0796-121">`ChannelCredentials`Kanalı üzerinde yapılandırmak, belirteci gRPC çağrılarıyla hizmete göndermenin alternatif bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="a0796-121">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="a0796-122">`ChannelCredentials` `CallCredentials` , Otomatik olarak ayarlanması için bir yol sağlayan, içerebilir `Metadata` .</span><span class="sxs-lookup"><span data-stu-id="a0796-122">A `ChannelCredentials` can include `CallCredentials`, which provide a way to automatically set `Metadata`.</span></span>

<span data-ttu-id="a0796-123">`CallCredentials` Her bir gRPC çağrısı yapıldığında çalıştırılır ve bu, belirteci kendiniz geçirmek için birden çok yere kod yazma ihtiyacını önler.</span><span class="sxs-lookup"><span data-stu-id="a0796-123">`CallCredentials` is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span> <span data-ttu-id="a0796-124">`CallCredentials`Yalnızca KANALıN TLS ile güvenliği sağlanmasıyla uygulandığını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="a0796-124">Note that `CallCredentials` are only applied if the channel is secured with TLS.</span></span> <span data-ttu-id="a0796-125">`CallCredentials` güvenli olmayan TLS olmayan kanallarda uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="a0796-125">`CallCredentials` aren't applied on unsecured non-TLS channels.</span></span>

<span data-ttu-id="a0796-126">Aşağıdaki örnekteki kimlik bilgileri, kanalı her gRPC çağrısıyla birlikte gönderecek şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="a0796-126">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="a0796-127">İstemci sertifikası kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="a0796-127">Client certificate authentication</span></span>

<span data-ttu-id="a0796-128">İstemci alternatif olarak kimlik doğrulaması için bir istemci sertifikası sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="a0796-128">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="a0796-129">[Sertifika kimlik doğrulaması](https://tools.ietf.org/html/rfc5246#section-7.4.4) TLS düzeyinde gerçekleşir ve bu süre ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a0796-129">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="a0796-130">İstek ASP.NET Core girdiğinde, [istemci sertifikası kimlik doğrulama paketi](xref:security/authentication/certauth) , sertifikayı bir ile çözmenize olanak tanır `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="a0796-130">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="a0796-131">Sunucuyu istemci sertifikalarını kabul edecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="a0796-131">Configure the server to accept client certificates.</span></span> <span data-ttu-id="a0796-132">Kestrel, IIS ve Azure 'da istemci sertifikalarını kabul etme hakkında bilgi için bkz <xref:security/authentication/certauth#configure-your-server-to-require-certificates> ..</span><span class="sxs-lookup"><span data-stu-id="a0796-132">For information on accepting client certificates in Kestrel, IIS, and Azure, see <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.</span></span>

<span data-ttu-id="a0796-133">.NET gRPC istemcisinde, `HttpClientHandler` daha sonra gRPC istemcisini oluşturmak için kullanılan istemci sertifikası eklenir:</span><span class="sxs-lookup"><span data-stu-id="a0796-133">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="a0796-134">Diğer kimlik doğrulama mekanizmaları</span><span class="sxs-lookup"><span data-stu-id="a0796-134">Other authentication mechanisms</span></span>

<span data-ttu-id="a0796-135">Desteklenen birçok ASP.NET Core kimlik doğrulama mekanizması gRPC ile çalışır:</span><span class="sxs-lookup"><span data-stu-id="a0796-135">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="a0796-136">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="a0796-136">Azure Active Directory</span></span>
* <span data-ttu-id="a0796-137">İstemci sertifikası</span><span class="sxs-lookup"><span data-stu-id="a0796-137">Client Certificate</span></span>
* <span data-ttu-id="a0796-138">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="a0796-138">IdentityServer</span></span>
* <span data-ttu-id="a0796-139">JWT belirteci</span><span class="sxs-lookup"><span data-stu-id="a0796-139">JWT Token</span></span>
* <span data-ttu-id="a0796-140">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="a0796-140">OAuth 2.0</span></span>
* <span data-ttu-id="a0796-141">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="a0796-141">OpenID Connect</span></span>
* <span data-ttu-id="a0796-142">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="a0796-142">WS-Federation</span></span>

<span data-ttu-id="a0796-143">Sunucuda kimlik doğrulamasını yapılandırma hakkında daha fazla bilgi için, [ASP.NET Core kimlik doğrulaması](xref:security/authentication/identity)' na bakın.</span><span class="sxs-lookup"><span data-stu-id="a0796-143">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="a0796-144">GRPC istemcisini kimlik doğrulaması kullanacak şekilde yapılandırmak, kullanmakta olduğunuz kimlik doğrulama mekanizmasına bağlı olarak değişir.</span><span class="sxs-lookup"><span data-stu-id="a0796-144">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="a0796-145">Önceki taşıyıcı belirteci ve istemci sertifikası örnekleri, GRPC istemcisinin, gRPC çağrılarına yönelik kimlik doğrulama meta verilerini gönderecek şekilde yapılandırılabilmesinin birkaç yolunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="a0796-145">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="a0796-146">Türü kesin belirlenmiş gRPC istemcileri `HttpClient` dahili olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a0796-146">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="a0796-147">Kimlik doğrulaması, [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)üzerinde veya öğesine özel [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) örnekleri eklenerek yapılandırılabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="a0796-147">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="a0796-148">Her gRPC çağrısının isteğe bağlı bir `CallOptions` bağımsız değişkeni vardır.</span><span class="sxs-lookup"><span data-stu-id="a0796-148">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="a0796-149">Özel üstbilgiler, seçeneğin üstbilgiler koleksiyonu kullanılarak gönderilebilir.</span><span class="sxs-lookup"><span data-stu-id="a0796-149">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="a0796-150">Windows kimlik doğrulaması (NTLM/Kerberos/Negotiate), gRPC ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="a0796-150">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="a0796-151">gRPC için HTTP/2 ve HTTP/2 Windows kimlik doğrulamasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="a0796-151">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="a0796-152">Kullanıcılara hizmetlere ve hizmet yöntemlerine erişim yetkisi verme</span><span class="sxs-lookup"><span data-stu-id="a0796-152">Authorize users to access services and service methods</span></span>

<span data-ttu-id="a0796-153">Varsayılan olarak, bir hizmette tüm yöntemler kimliği doğrulanmamış kullanıcılar tarafından çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="a0796-153">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="a0796-154">Kimlik doğrulaması gerektirmek için, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özelliği hizmetine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="a0796-154">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="a0796-155">`[Authorize]`Yalnızca belirli [Yetkilendirme ilkeleriyle](xref:security/authorization/policies)eşleşen kullanıcılara erişimi kısıtlamak için özniteliğinin Oluşturucu bağımsız değişkenlerini ve özelliklerini kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a0796-155">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="a0796-156">Örneğin, adlı bir özel yetkilendirme ilkeniz varsa `MyAuthorizationPolicy` , aşağıdaki kodu kullanarak yalnızca bu ilkeyle eşleşen kullanıcıların hizmete erişebildiğinden emin olun:</span><span class="sxs-lookup"><span data-stu-id="a0796-156">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="a0796-157">Bağımsız hizmet yöntemlerinin `[Authorize]` özniteliği de uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="a0796-157">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="a0796-158">Geçerli Kullanıcı hem yönteme hem **de** sınıfa uygulanan ilkelerle eşleşmezse, çağırana bir hata döndürülür:</span><span class="sxs-lookup"><span data-stu-id="a0796-158">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="a0796-159">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a0796-159">Additional resources</span></span>

* [<span data-ttu-id="a0796-160">ASP.NET Core 'de taşıyıcı belirteç kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="a0796-160">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="a0796-161">ASP.NET Core 'de Istemci sertifikası kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a0796-161">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
