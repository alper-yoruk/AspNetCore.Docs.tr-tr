---
title: ASP.NET Core kimlik doğrulaması ve yetkilendirmeSignalR
author: bradygaster
description: ASP.NET Core 'da kimlik doğrulama ve yetkilendirmeyi nasıl kullanacağınızı öğrenin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 91b251e44f6534f002705afb360b8a7855a5e435
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755826"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="5412e-103">ASP.NET Core kimlik doğrulaması ve yetkilendirmeSignalR</span><span class="sxs-lookup"><span data-stu-id="5412e-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="5412e-104">, [Andrew Stanton-nurte](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="5412e-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="5412e-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5412e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="5412e-106">Bir hub 'a bağlanan kullanıcıların kimliğini doğrulama SignalR</span><span class="sxs-lookup"><span data-stu-id="5412e-106">Authenticate users connecting to a SignalR hub</span></span>

SignalR<span data-ttu-id="5412e-107">, bir kullanıcıyı her bağlantıyla ilişkilendirmek için [ASP.NET Core kimlik doğrulamasıyla](xref:security/authentication/identity) birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-107"> can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="5412e-108">Bir hub 'da, [Hubconnectioncontext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) özelliğinden kimlik doğrulama verilerine erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="5412e-109">Kimlik doğrulaması, hub 'ın bir kullanıcıyla ilişkili tüm bağlantılar üzerinde Yöntemler çağırmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="5412e-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="5412e-110">Daha fazla bilgi için bkz. [kullanıcıları ve grupları ' SignalR de yönetme ](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="5412e-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="5412e-111">Birden çok bağlantı tek bir kullanıcıyla ilişkilendirilebilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="5412e-112">Aşağıda, `Startup.Configure` ASP.NET Core kimlik doğrulaması kullanan bir örnek verilmiştir SignalR :</span><span class="sxs-lookup"><span data-stu-id="5412e-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="5412e-113">SignalRVe ASP.NET Core kimlik doğrulama ara yazılımını kaydetme sırası önemli.</span><span class="sxs-lookup"><span data-stu-id="5412e-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="5412e-114">`UseAuthentication` `UseSignalR` SignalR Üzerinde bir kullanıcı olması için önce her zaman ' i çağırın `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="5412e-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="5412e-115">Tanımlama bilgisi kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="5412e-115">Cookie authentication</span></span>

<span data-ttu-id="5412e-116">Tarayıcı tabanlı bir uygulamada, tanımlama bilgisi kimlik doğrulaması, mevcut kullanıcı kimlik bilgilerinizin otomatik olarak bağlantılara akmasını sağlar SignalR .</span><span class="sxs-lookup"><span data-stu-id="5412e-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="5412e-117">Tarayıcı istemcisi kullanılırken ek yapılandırma gerekmez.</span><span class="sxs-lookup"><span data-stu-id="5412e-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="5412e-118">Kullanıcı uygulamanızda oturum açtıysa, SignalR bağlantı bu kimlik doğrulamasını otomatik olarak devralır.</span><span class="sxs-lookup"><span data-stu-id="5412e-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="5412e-119">Tanımlama bilgileri, erişim belirteçleri göndermek için tarayıcıya özgü bir yoldur, ancak tarayıcı olmayan istemciler bunları gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="5412e-120">[.NET istemcisi](xref:signalr/dotnet-client)kullanılırken, `Cookies` özelliği `.WithUrl` bir tanımlama bilgisi sağlamak için çağrısında yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="5412e-121">Ancak, .NET istemcisinden tanımlama bilgisi kimlik doğrulamasını kullanmak, uygulamanın bir tanımlama bilgisi için kimlik doğrulama verilerini Exchange için bir API sağlamasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="5412e-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="5412e-122">Taşıyıcı belirteç kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="5412e-122">Bearer token authentication</span></span>

<span data-ttu-id="5412e-123">İstemci, tanımlama bilgisi kullanmak yerine bir erişim belirteci sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="5412e-124">Sunucu belirteci doğrular ve kullanıcıyı tanımlamak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="5412e-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="5412e-125">Bu doğrulama yalnızca bağlantı kurulduunda yapılır.</span><span class="sxs-lookup"><span data-stu-id="5412e-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="5412e-126">Bağlantının kullanım ömrü boyunca, sunucu otomatik olarak yeniden doğrulamadan belirteç iptalini kontrol etmez.</span><span class="sxs-lookup"><span data-stu-id="5412e-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="5412e-127">Sunucusunda, taşıyıcı belirteç kimlik doğrulaması [JWT taşıyıcı ara yazılımı](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5412e-127">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="5412e-128">JavaScript istemcisinde, belirteç [Accesstokenfactory](xref:signalr/configuration#configure-bearer-authentication) seçeneği kullanılarak sağlanabilirler.</span><span class="sxs-lookup"><span data-stu-id="5412e-128">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="5412e-129">.NET istemcisinde, belirteci yapılandırmak için kullanılabilecek, benzer bir [Accesstokenprovider](xref:signalr/configuration#configure-bearer-authentication) özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="5412e-129">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="5412e-130">Sağladığınız erişim belirteci işlevi, tarafından yapılan **her** http isteğinin önüne çağırılır SignalR .</span><span class="sxs-lookup"><span data-stu-id="5412e-130">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="5412e-131">Bağlantıyı etkin tutmak için belirteci yenilemeniz gerekiyorsa (bağlantı sırasında süresi dolarsa), bunu bu işlevin içinden yapın ve güncelleştirilmiş belirteci döndürün.</span><span class="sxs-lookup"><span data-stu-id="5412e-131">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="5412e-132">Standart Web API 'Lerinde, taşıyıcı belirteçleri bir HTTP üst bilgisinde gönderilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-132">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="5412e-133">Ancak, SignalR bazı aktarımlar kullanılırken tarayıcılarda bu üst bilgileri ayarlayamamalıdır.</span><span class="sxs-lookup"><span data-stu-id="5412e-133">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="5412e-134">WebSockets ve sunucu tarafından gönderilen olaylar kullanılırken, belirteç bir sorgu dizesi parametresi olarak iletilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-134">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="5412e-135">Bunu sunucuda desteklemek için ek yapılandırma gerekir:</span><span class="sxs-lookup"><span data-stu-id="5412e-135">To support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="5412e-136">Sorgu dizesi tarayıcı API 'SI sınırlamaları nedeniyle WebSockets ve sunucu tarafından gönderilen olaylarla bağlantı kurulurken tarayıcılarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5412e-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="5412e-137">HTTPS kullanılırken sorgu dizesi değerleri TLS bağlantısıyla korunmuş hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="5412e-138">Ancak, birçok sunucu günlük sorgu dizesi değerlerini günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="5412e-138">However, many servers log query string values.</span></span> <span data-ttu-id="5412e-139">Daha fazla bilgi için [ SignalR ASP.NET Core güvenlik konuları ](xref:signalr/security)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="5412e-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> SignalR<span data-ttu-id="5412e-140">, bunları destekleyen ortamlarda (.NET ve Java istemcileri gibi) belirteçleri iletmek için üst bilgileri kullanır.</span><span class="sxs-lookup"><span data-stu-id="5412e-140"> uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="5412e-141">Tanımlama bilgileri ve taşıyıcı belirteçleri karşılaştırması</span><span class="sxs-lookup"><span data-stu-id="5412e-141">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="5412e-142">Tanımlama bilgileri tarayıcılara özeldir.</span><span class="sxs-lookup"><span data-stu-id="5412e-142">Cookies are specific to browsers.</span></span> <span data-ttu-id="5412e-143">Diğer istemci türlerinden gönderilmesi, taşıyıcı belirteçlerinin gönderilmesine kıyasla karmaşıklık ekler.</span><span class="sxs-lookup"><span data-stu-id="5412e-143">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="5412e-144">Sonuç olarak, uygulamanın yalnızca tarayıcı istemcisinden kullanıcıların kimliğini doğrulaması gerekmediği takdirde tanımlama bilgisi kimlik doğrulaması önerilmez.</span><span class="sxs-lookup"><span data-stu-id="5412e-144">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="5412e-145">Taşıyıcı belirteç kimlik doğrulaması, tarayıcı istemcisi dışındaki istemcileri kullanırken önerilen yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="5412e-145">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="5412e-146">Windows kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="5412e-146">Windows authentication</span></span>

<span data-ttu-id="5412e-147">Uygulamanızda [Windows kimlik doğrulaması](xref:security/authentication/windowsauth) yapılandırıldıysa, SignalR hub 'ları güvenli hale getirmek için bu kimliği kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-147">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="5412e-148">Ancak, bireysel kullanıcılara ileti göndermek için özel bir kullanıcı KIMLIĞI sağlayıcısı eklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="5412e-148">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="5412e-149">Windows kimlik doğrulama sistemi, "ad tanımlayıcı" talebi sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="5412e-149">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> SignalR<span data-ttu-id="5412e-150">Kullanıcı adını öğrenmek için talebi kullanır.</span><span class="sxs-lookup"><span data-stu-id="5412e-150"> uses the claim to determine the user name.</span></span>

<span data-ttu-id="5412e-151">`IUserIdProvider`Kullanıcı tarafından tanımlayıcı olarak kullanılacak taleplerden birini uygulayan ve alan yeni bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5412e-151">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="5412e-152">Örneğin, "ad" talebini (formdaki Windows Kullanıcı adı) kullanmak için `[Domain]\[Username]` aşağıdaki sınıfı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="5412e-152">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="5412e-153">Yerine, `ClaimTypes.Name` herhangi bir değeri `User` (örneğin, Windows SID tanımlayıcısı, vb.) kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5412e-153">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="5412e-154">Seçtiğiniz değer, sisteminizdeki tüm kullanıcılar arasında benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5412e-154">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="5412e-155">Aksi takdirde, bir kullanıcı için tasarlanan bir ileti farklı bir kullanıcıya gidiyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-155">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="5412e-156">Bu bileşeni yönteminizin içine kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5412e-156">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="5412e-157">.NET Istemcisinde, [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) özelliği ayarlanarak Windows kimlik doğrulamasının etkinleştirilmesi gerekir:</span><span class="sxs-lookup"><span data-stu-id="5412e-157">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="5412e-158">Windows kimlik doğrulaması, Internet Explorer ve Microsoft Edge 'de desteklenir, ancak tüm tarayıcılarda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5412e-158">Windows authentication is supported in Internet Explorer and Microsoft Edge, but not in all browsers.</span></span> <span data-ttu-id="5412e-159">Örneğin, Chrome ve Safari 'de, Windows kimlik doğrulaması ve WebSockets kullanma girişimi başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="5412e-159">For example, in Chrome and Safari, attempting to use Windows authentication and WebSockets fails.</span></span> <span data-ttu-id="5412e-160">Windows kimlik doğrulaması başarısız olduğunda, istemci, işe gerekebilecek diğer aktarımlara geri dönmesine çalışır.</span><span class="sxs-lookup"><span data-stu-id="5412e-160">When Windows authentication fails, the client attempts to fall back to other transports which might work.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="5412e-161">Kimlik işlemeyi özelleştirmek için talepler kullanma</span><span class="sxs-lookup"><span data-stu-id="5412e-161">Use claims to customize identity handling</span></span>

<span data-ttu-id="5412e-162">Kullanıcıların kimliğini doğrulayan bir uygulama, Kullanıcı SignalR taleplerinden Kullanıcı kimliklerini türetebilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-162">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="5412e-163">SignalRKullanıcı kimliklerini nasıl oluşturduğunu belirtmek için, `IUserIdProvider` uygulamayı uygulayın ve kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5412e-163">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="5412e-164">Örnek kod, tanımlama özelliği olarak kullanıcının e-posta adresini seçmek için talepleri nasıl kullanacağınızı gösterir.</span><span class="sxs-lookup"><span data-stu-id="5412e-164">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="5412e-165">Seçtiğiniz değer, sisteminizdeki tüm kullanıcılar arasında benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5412e-165">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="5412e-166">Aksi takdirde, bir kullanıcı için tasarlanan bir ileti farklı bir kullanıcıya gidiyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-166">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="5412e-167">Hesap kaydı, ASP.NET Identity veritabanına türünde bir talep ekler `ClaimsTypes.Email` .</span><span class="sxs-lookup"><span data-stu-id="5412e-167">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="5412e-168">Bu bileşeni ' ye kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5412e-168">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="5412e-169">Kullanıcılara, hub 'lara ve hub yöntemlerine erişim yetkisi verme</span><span class="sxs-lookup"><span data-stu-id="5412e-169">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="5412e-170">Varsayılan olarak, bir hub 'daki tüm yöntemler kimliği doğrulanmamış bir kullanıcı tarafından çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-170">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="5412e-171">Kimlik doğrulaması gerektirmek için, [Yetkilendir](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) özniteliğini hub 'a uygulayın:</span><span class="sxs-lookup"><span data-stu-id="5412e-171">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="5412e-172">`[Authorize]`Yalnızca belirli [Yetkilendirme ilkeleriyle](xref:security/authorization/policies)eşleşen kullanıcılara erişimi kısıtlamak için özniteliğinin Oluşturucu bağımsız değişkenlerini ve özelliklerini kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5412e-172">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="5412e-173">Örneğin, adlı bir özel yetkilendirme ilkeniz varsa, `MyAuthorizationPolicy` aşağıdaki kodu kullanarak yalnızca bu ilkeyle eşleşen kullanıcıların hub 'a erişmesini sağlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="5412e-173">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="5412e-174">Tek bir hub yöntemi `[Authorize]` özniteliğe de uygulanmış olabilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-174">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="5412e-175">Geçerli Kullanıcı, yönteme uygulanan ilkeyle eşleşmezse, çağırana bir hata döndürülür:</span><span class="sxs-lookup"><span data-stu-id="5412e-175">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="5412e-176">Hub yöntemi yetkilendirmesini özelleştirmek için yetkilendirme işleyicilerini kullanma</span><span class="sxs-lookup"><span data-stu-id="5412e-176">Use authorization handlers to customize hub method authorization</span></span>

SignalR<span data-ttu-id="5412e-177">bir hub yöntemi yetkilendirme gerektirdiğinde, yetkilendirme işleyicilerine özel bir kaynak sağlar.</span><span class="sxs-lookup"><span data-stu-id="5412e-177"> provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="5412e-178">Kaynak bir örneğidir `HubInvocationContext` .</span><span class="sxs-lookup"><span data-stu-id="5412e-178">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="5412e-179">, `HubInvocationContext` `HubCallerContext` Çağrılan hub yönteminin adını ve hub yönteminin bağımsız değişkenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="5412e-179">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="5412e-180">Azure Active Directory aracılığıyla birden çok kuruluşun oturum açmasına izin veren bir sohbet odası örneğini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="5412e-180">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="5412e-181">Microsoft hesabı olan herkes sohbet için oturum açabilir, ancak yalnızca sahip olunan kuruluşun üyeleri kullanıcıları yasaklatabilecek veya kullanıcıların sohbet geçmişlerini görüntüleyebilmelidir.</span><span class="sxs-lookup"><span data-stu-id="5412e-181">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="5412e-182">Ayrıca, belirli kullanıcılardan belirli işlevleri kısıtlamak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5412e-182">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="5412e-183">ASP.NET Core 3,0 ' deki güncelleştirilmiş özellikleri kullanarak bu tamamen mümkündür.</span><span class="sxs-lookup"><span data-stu-id="5412e-183">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="5412e-184">Nasıl `DomainRestrictedRequirement` özel olarak işlev gördüğüne göz önünde kalın `IAuthorizationRequirement` .</span><span class="sxs-lookup"><span data-stu-id="5412e-184">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="5412e-185">`HubInvocationContext`Kaynak parametresi geçirilmeye hazır olduğuna göre, iç mantık hub 'ın çağrıldığı bağlamı inceleyebilir ve kullanıcının bireysel hub yöntemlerini yürütmesine izin verirken kararlar verebilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-185">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="5412e-186">İçinde `Startup.ConfigureServices` , `DomainRestrictedRequirement` ilkeyi oluşturmak için özel gereksinimi bir parametre olarak sağlayarak yeni ilkeyi ekleyin `DomainRestricted` .</span><span class="sxs-lookup"><span data-stu-id="5412e-186">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="5412e-187">Yukarıdaki örnekte, `DomainRestrictedRequirement` sınıfı, `IAuthorizationRequirement` Bu gereksinim için hem bir ve kendisi olur `AuthorizationHandler` .</span><span class="sxs-lookup"><span data-stu-id="5412e-187">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="5412e-188">Bu iki bileşeni birbirinden ayrı ayrı sınıflara bölmek kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="5412e-188">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="5412e-189">Örneğin yaklaşımın bir avantajı, `AuthorizationHandler` gereksinim ve işleyicinin aynı şey olduğu için başlangıç sırasında ekleme zorunluluktur.</span><span class="sxs-lookup"><span data-stu-id="5412e-189">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5412e-190">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5412e-190">Additional resources</span></span>

* [<span data-ttu-id="5412e-191">ASP.NET Core 'de taşıyıcı belirteç kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="5412e-191">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="5412e-192">Kaynak tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="5412e-192">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
