---
title: ASP.NET Core 3,0 ' deki yenilikler
author: rick-anderson
description: ASP.NET Core 3,0 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-3.0
ms.openlocfilehash: a53ba22c301b04aafa1eeb53d91f82f90dab0bad
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631556"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="df93f-103">ASP.NET Core 3,0 ' deki yenilikler</span><span class="sxs-lookup"><span data-stu-id="df93f-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="df93f-104">Bu makalede, ASP.NET Core 3,0 ' deki en önemli değişiklikler ilgili belgelerin bağlantılarıyla vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="df93f-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

<span data-ttu-id="df93f-105">Blazor , .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmak için ASP.NET Core yeni bir çerçevedir:</span><span class="sxs-lookup"><span data-stu-id="df93f-105">Blazor is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="df93f-106">JavaScript yerine C# kullanarak zengin etkileşimli uo 'lar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="df93f-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="df93f-107">.NET ' te yazılmış sunucu tarafı ve istemci tarafı uygulama mantığını paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="df93f-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="df93f-108">Mobil tarayıcılar dahil olmak üzere geniş tarayıcı desteği için Kullanıcı arabirimini HTML ve CSS olarak işleme.</span><span class="sxs-lookup"><span data-stu-id="df93f-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="df93f-109">Blazor çerçeve tarafından desteklenen senaryolar:</span><span class="sxs-lookup"><span data-stu-id="df93f-109">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="df93f-110">Yeniden kullanılabilir kullanıcı arabirimi bileşenleri ( Razor bileşenler)</span><span class="sxs-lookup"><span data-stu-id="df93f-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="df93f-111">İstemci tarafı yönlendirme</span><span class="sxs-lookup"><span data-stu-id="df93f-111">Client-side routing</span></span>
* <span data-ttu-id="df93f-112">Bileşen düzenleri</span><span class="sxs-lookup"><span data-stu-id="df93f-112">Component layouts</span></span>
* <span data-ttu-id="df93f-113">Bağımlılık ekleme desteği</span><span class="sxs-lookup"><span data-stu-id="df93f-113">Support for dependency injection</span></span>
* <span data-ttu-id="df93f-114">Formlar ve doğrulama</span><span class="sxs-lookup"><span data-stu-id="df93f-114">Forms and validation</span></span>
* <span data-ttu-id="df93f-115">Sınıf kitaplıklarıyla bileşen kitaplıkları derleme Razor</span><span class="sxs-lookup"><span data-stu-id="df93f-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="df93f-116">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="df93f-116">JavaScript interop</span></span>

<span data-ttu-id="df93f-117">Daha fazla bilgi için bkz. <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="df93f-117">For more information, see <xref:blazor/index>.</span></span>

### Blazor Server

<span data-ttu-id="df93f-118">Blazor Kullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır.</span><span class="sxs-lookup"><span data-stu-id="df93f-118">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="df93f-119">Blazor Server ASP.NET Core uygulamasında sunucuda barındırma bileşenleri için destek sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="df93f-119">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="df93f-120">Kullanıcı Arabirimi güncelleştirmeleri bir bağlantı üzerinden işlenir SignalR .</span><span class="sxs-lookup"><span data-stu-id="df93f-120">UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="df93f-121">Blazor Server ASP.NET Core 3,0 ' de desteklenir.</span><span class="sxs-lookup"><span data-stu-id="df93f-121">Blazor Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="no-locblazor-webassembly-preview"></a><span data-ttu-id="df93f-122">Blazor WebAssembly Önizle</span><span class="sxs-lookup"><span data-stu-id="df93f-122">Blazor WebAssembly (Preview)</span></span>

<span data-ttu-id="df93f-123">Blazor uygulamalar, bir WebAssembly tabanlı .NET çalışma zamanı kullanarak doğrudan tarayıcıda da çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-123">Blazor apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> <span data-ttu-id="df93f-124">Blazor WebAssembly önizlemededir *ve ASP.NET Core 3,0 ' de* desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="df93f-124">Blazor WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> <span data-ttu-id="df93f-125">Blazor WebAssembly ASP.NET Core gelecek bir sürümünde desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="df93f-125">Blazor WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="no-locrazor-components"></a><span data-ttu-id="df93f-126">Razor bileşenleri</span><span class="sxs-lookup"><span data-stu-id="df93f-126">Razor components</span></span>

<span data-ttu-id="df93f-127">Blazor uygulamalar bileşenlerden oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="df93f-127">Blazor apps are built from components.</span></span> <span data-ttu-id="df93f-128">Bileşenler, bir sayfa, iletişim kutusu veya form gibi kullanıcı arabirimi (UI) için kendi içinde yer alan öbeklerdir.</span><span class="sxs-lookup"><span data-stu-id="df93f-128">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="df93f-129">Bileşenler, Kullanıcı arabirimi işleme mantığını ve istemci tarafı olay işleyicilerini tanımlayan normal .NET sınıflarıdır.</span><span class="sxs-lookup"><span data-stu-id="df93f-129">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="df93f-130">JavaScript olmadan zengin etkileşimli Web uygulamaları oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="df93f-130">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="df93f-131">İçindeki bileşenler Blazor genellıkle Razor HTML ve C# ' nin doğal bir karışımı olan sözdizimi kullanılarak yazılır.</span><span class="sxs-lookup"><span data-stu-id="df93f-131">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="df93f-132">Razor bileşenler, Razor her ikisi de kullandıkları sayfalara ve MVC görünümlerine benzerdir Razor .</span><span class="sxs-lookup"><span data-stu-id="df93f-132">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="df93f-133">Bir istek-yanıt modelini temel alan sayfaların ve görünümlerin aksine, bileşenler Kullanıcı arabirimi oluşturmayı işlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="df93f-133">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="df93f-134">gRPC</span><span class="sxs-lookup"><span data-stu-id="df93f-134">gRPC</span></span>

<span data-ttu-id="df93f-135">[GRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="df93f-135">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="df93f-136">Popüler, yüksek performanslı bir RPC (uzak yordam çağrısı) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="df93f-136">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="df93f-137">, API geliştirmeye yönelik olarak yapılan bir sözleşmenin ilk yaklaşımını sağlar.</span><span class="sxs-lookup"><span data-stu-id="df93f-137">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="df93f-138">, Gibi modern teknolojiler kullanır:</span><span class="sxs-lookup"><span data-stu-id="df93f-138">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="df93f-139">Taşıma için HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="df93f-139">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="df93f-140">Arabirim açıklaması dili olarak protokol arabellekleri.</span><span class="sxs-lookup"><span data-stu-id="df93f-140">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="df93f-141">İkili serileştirme biçimi.</span><span class="sxs-lookup"><span data-stu-id="df93f-141">Binary serialization format.</span></span>
* <span data-ttu-id="df93f-142">Şöyle özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="df93f-142">Provides features such as:</span></span>

  * <span data-ttu-id="df93f-143">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="df93f-143">Authentication</span></span>
  * <span data-ttu-id="df93f-144">Çift yönlü akış ve akış denetimi.</span><span class="sxs-lookup"><span data-stu-id="df93f-144">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="df93f-145">İptal ve zaman aşımları.</span><span class="sxs-lookup"><span data-stu-id="df93f-145">Cancellation and timeouts.</span></span>

<span data-ttu-id="df93f-146">ASP.NET Core 3,0 ' deki gRPC işlevselliği şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="df93f-146">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="df93f-147">[GRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): GRPC hizmetlerini barındırmak için bir ASP.NET Core çerçevesi.</span><span class="sxs-lookup"><span data-stu-id="df93f-147">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="df93f-148">gRPC on ASP.NET Core, günlüğe kaydetme, bağımlılık ekleme (dı), kimlik doğrulama ve yetkilendirme gibi standart ASP.NET Core özelliklerle tümleştirilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-148">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="df93f-149">[GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client): .NET Core için tanıdık bir GRPC istemcisi `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="df93f-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client): A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="df93f-150">[GRPC .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): GRPC istemcisi ile tümleştirme `HttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="df93f-150">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="df93f-151">Daha fazla bilgi için bkz. <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="df93f-151">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="df93f-152">Bkz. geçiş yönergeleri için [ SignalR kodu güncelleştirme](xref:migration/22-to-30#signalr) .</span><span class="sxs-lookup"><span data-stu-id="df93f-152">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> <span data-ttu-id="df93f-153">SignalR Artık `System.Text.Json` JSON iletilerini seri hale getirmek/seri durumdan çıkarmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="df93f-153">SignalR now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="df93f-154">Tabanlı seri hale getirici 'yi geri yükleme yönergeleri için bkz. [Newtonsoft.Js'ye geçme](xref:migration/22-to-30#switch-to-newtonsoftjson) `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="df93f-154">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="df93f-155">İçin JavaScript ve .NET Istemcilerinde SignalR , otomatik yeniden bağlanma için destek eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="df93f-155">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="df93f-156">Varsayılan olarak, istemci hemen yeniden bağlanmaya çalışır ve gerekirse 2, 10 ve 30 saniye sonra yeniden dener.</span><span class="sxs-lookup"><span data-stu-id="df93f-156">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="df93f-157">İstemci başarıyla yeniden bağlanırsa, yeni bir bağlantı KIMLIĞI alır.</span><span class="sxs-lookup"><span data-stu-id="df93f-157">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="df93f-158">Otomatik yeniden bağlanma kabul etme:</span><span class="sxs-lookup"><span data-stu-id="df93f-158">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="df93f-159">Yeniden bağlantı aralıkları bir dizi milisaniyelik tabanlı süre geçirerek belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="df93f-159">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="df93f-160">Yeniden bağlantı aralıklarının tam denetimi için özel bir uygulama geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-160">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="df93f-161">Son yeniden bağlanma aralığından sonra yeniden bağlantı başarısız olursa:</span><span class="sxs-lookup"><span data-stu-id="df93f-161">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="df93f-162">İstemci, bağlantının çevrimdışı olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="df93f-162">The client considers the connection is offline.</span></span>
* <span data-ttu-id="df93f-163">İstemci yeniden bağlanmayı denemeyi durduruyor.</span><span class="sxs-lookup"><span data-stu-id="df93f-163">The client stops trying to reconnect.</span></span>

<span data-ttu-id="df93f-164">Yeniden bağlanma denemeleri sırasında, kullanıcıya yeniden bağlantı denenmekte olduğunu bildirmek için uygulama kullanıcı arabirimini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="df93f-164">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="df93f-165">Bağlantı kesildiğinde UI geri bildirimi sağlamak için, SignalR istemci API 'si aşağıdaki olay işleyicilerini içerecek şekilde genişletilir:</span><span class="sxs-lookup"><span data-stu-id="df93f-165">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="df93f-166">`onreconnecting`: Geliştiricilere Kullanıcı arabirimini devre dışı bırakma veya uygulamanın çevrimdışı olduğunu bilmesini sağlayan bir fırsat sağlar.</span><span class="sxs-lookup"><span data-stu-id="df93f-166">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="df93f-167">`onreconnected`: Bağlantı kurulduktan sonra geliştiricilere Kullanıcı arabirimini güncelleştirme fırsatı verir.</span><span class="sxs-lookup"><span data-stu-id="df93f-167">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="df93f-168">Aşağıdaki kod, `onreconnecting` bağlanmaya çalışırken kullanıcı arabirimini güncelleştirmek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="df93f-168">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="df93f-169">Aşağıdaki kod, `onreconnected` bağlantıda Kullanıcı arabirimini güncelleştirmek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="df93f-169">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="df93f-170">SignalR 3,0 ve üzeri, bir hub yöntemi yetkilendirme gerektirdiğinde, yetkilendirme işleyicilerine özel bir kaynak sağlar.</span><span class="sxs-lookup"><span data-stu-id="df93f-170">SignalR 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="df93f-171">Kaynak bir örneğidir `HubInvocationContext` .</span><span class="sxs-lookup"><span data-stu-id="df93f-171">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="df93f-172">`HubInvocationContext`Şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="df93f-172">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="df93f-173">Çağrılan hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="df93f-173">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="df93f-174">Hub yöntemi için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="df93f-174">Arguments to the hub method.</span></span>

<span data-ttu-id="df93f-175">Azure Active Directory aracılığıyla birden çok kuruluşun oturum açmasına izin veren bir sohbet odası uygulamasının aşağıdaki örneğini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="df93f-175">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="df93f-176">Microsoft hesabı herkes sohbet için oturum açabilir, ancak yalnızca sahip olunan kuruluşun üyeleri kullanıcıları veya kullanıcıların sohbet geçmişlerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-176">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="df93f-177">Uygulama belirli kullanıcılardan belirli işlevleri kısıtlayabilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-177">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="df93f-178">Yukarıdaki kodda `DomainRestrictedRequirement` özel işlevi görür `IAuthorizationRequirement` .</span><span class="sxs-lookup"><span data-stu-id="df93f-178">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="df93f-179">`HubInvocationContext`Kaynak parametresi geçirildiğinden iç mantık şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="df93f-179">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="df93f-180">Hub 'ın çağrıldığı bağlamı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="df93f-180">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="df93f-181">Kullanıcının bireysel hub yöntemlerini yürütmesine izin verirken kararlar alın.</span><span class="sxs-lookup"><span data-stu-id="df93f-181">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="df93f-182">Tek tek hub yöntemleri, çalışma zamanında kodun denetlediği ilkenin adıyla işaretlenebilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-182">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="df93f-183">İstemciler tek tek hub yöntemlerini çağırmayı denediğinden, `DomainRestrictedRequirement` işleyici çalışır ve yöntemlere erişimi denetler.</span><span class="sxs-lookup"><span data-stu-id="df93f-183">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="df93f-184">`DomainRestrictedRequirement`Denetimlerin erişim yöntemine göre:</span><span class="sxs-lookup"><span data-stu-id="df93f-184">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="df93f-185">Tüm oturum açmış kullanıcılar `SendMessage` yöntemini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-185">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="df93f-186">Yalnızca bir `@jabbr.net` e-posta adresiyle oturum açan kullanıcılar, kullanıcıların geçmişlerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-186">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="df93f-187">Yalnızca `bob42@jabbr.net` sohbet odasından kullanıcıları Ban yapabilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-187">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

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
```

<span data-ttu-id="df93f-188">İlkeyi oluşturmak `DomainRestricted` şunları içerebilir:</span><span class="sxs-lookup"><span data-stu-id="df93f-188">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="df93f-189">*Startup.cs*' de, yeni ilkeyi ekleme.</span><span class="sxs-lookup"><span data-stu-id="df93f-189">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="df93f-190">Özel `DomainRestrictedRequirement` gereksinimi parametre olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="df93f-190">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="df93f-191">`DomainRestricted`Yetkilendirme ara yazılımı ile kaydetme.</span><span class="sxs-lookup"><span data-stu-id="df93f-191">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

<span data-ttu-id="df93f-192">SignalR Hub [uç nokta yönlendirme](xref:fundamentals/routing)kullanır.</span><span class="sxs-lookup"><span data-stu-id="df93f-192">SignalR hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="df93f-193">SignalR Hub bağlantısı daha önce açık olarak yapıldı:</span><span class="sxs-lookup"><span data-stu-id="df93f-193">SignalR hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="df93f-194">Önceki sürümde, geliştiriciler, Razor Sayfalar ve hub 'ları çeşitli yerlerde bağlamak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="df93f-194">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="df93f-195">Açık bağlantı, neredeyse aynı bir dizi yönlendirme segmentine neden olur:</span><span class="sxs-lookup"><span data-stu-id="df93f-195">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

<span data-ttu-id="df93f-196">SignalR 3,0 hub 'ları, uç nokta yönlendirme aracılığıyla yönlendirilebilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-196">SignalR 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="df93f-197">Uç nokta yönlendirme ile, genellikle tüm yönlendirme ' de yapılandırılabilir `UseRouting` :</span><span class="sxs-lookup"><span data-stu-id="df93f-197">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="df93f-198">ASP.NET Core 3,0 SignalR eklendi:</span><span class="sxs-lookup"><span data-stu-id="df93f-198">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="df93f-199">İstemciden sunucuya akış.</span><span class="sxs-lookup"><span data-stu-id="df93f-199">Client-to-server streaming.</span></span> <span data-ttu-id="df93f-200">İstemciden sunucuya akışla, sunucu tarafı yöntemleri veya örneklerinden birini alabilir `IAsyncEnumerable<T>` `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="df93f-200">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="df93f-201">Aşağıdaki C# örneğinde, `UploadStream` hub 'daki Yöntem istemciden dizelerin akışını alacaktır:</span><span class="sxs-lookup"><span data-stu-id="df93f-201">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="df93f-202">.NET istemci uygulamaları, `IAsyncEnumerable<T>` `ChannelReader<T>` `stream` Yukarıdaki hub yönteminin bağımsız değişkeni olarak bir ya da örneği geçirebilir `UploadStream` .</span><span class="sxs-lookup"><span data-stu-id="df93f-202">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="df93f-203">Döngü tamamlandıktan `for` ve yerel işlev çıktıktan sonra, akış tamamlama gönderilir:</span><span class="sxs-lookup"><span data-stu-id="df93f-203">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="df93f-204">JavaScript istemci uygulamaları, SignalR `Subject` Yukarıdaki hub yönteminin bağımsız değişkeni olarak (veya bir [Rxjs konusu](https://rxjs.dev/api/index/class/Subject)) kullanır `stream` `UploadStream` .</span><span class="sxs-lookup"><span data-stu-id="df93f-204">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="df93f-205">JavaScript kodu, `subject.next` yakalandıkları ve sunucuya gönderilmeye hazırlanıyor gibi dizeleri işlemek için yöntemini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-205">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="df93f-206">Önceki iki kod parçacığı gibi kodu kullanarak gerçek zamanlı akış deneyimleri oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-206">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="df93f-207">Yeni JSON serileştirmesi</span><span class="sxs-lookup"><span data-stu-id="df93f-207">New JSON serialization</span></span>

<span data-ttu-id="df93f-208">ASP.NET Core 3,0 artık <xref:System.Text.Json> JSON serileştirme için varsayılan olarak kullanır:</span><span class="sxs-lookup"><span data-stu-id="df93f-208">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="df93f-209">JSON 'yi zaman uyumsuz olarak okur ve yazar.</span><span class="sxs-lookup"><span data-stu-id="df93f-209">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="df93f-210">UTF-8 metni için iyileştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="df93f-210">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="df93f-211">Genellikle daha yüksek performans `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="df93f-211">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="df93f-212">ASP.NET Core 3,0 ' ye Json.NET eklemek için bkz. [Newtonsoft.Jstabanlı JSON biçimi desteği ekleme](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="df93f-212">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-no-locrazor-directives"></a><span data-ttu-id="df93f-213">Yeni Razor yönergeler</span><span class="sxs-lookup"><span data-stu-id="df93f-213">New Razor directives</span></span>

<span data-ttu-id="df93f-214">Aşağıdaki listede yeni yönergeler yer almaktadır Razor :</span><span class="sxs-lookup"><span data-stu-id="df93f-214">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="df93f-215">[`@attribute`](xref:mvc/views/razor#attribute): `@attribute` Yönergesi verilen özniteliği oluşturulan sayfanın veya görünümün sınıfına uygular.</span><span class="sxs-lookup"><span data-stu-id="df93f-215">[`@attribute`](xref:mvc/views/razor#attribute): The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="df93f-216">Örneğin, `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="df93f-216">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="df93f-217">[`@implements`](xref:mvc/views/razor#implements): `@implements` Yönerge, oluşturulan sınıf için bir arabirim uygular.</span><span class="sxs-lookup"><span data-stu-id="df93f-217">[`@implements`](xref:mvc/views/razor#implements): The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="df93f-218">Örneğin, `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="df93f-218">For example, `@implements IDisposable`.</span></span>

## <a name="no-locidentityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="df93f-219">IdentityServer4, Web API 'Leri ve maça 'Ları için kimlik doğrulama ve yetkilendirmeyi destekler</span><span class="sxs-lookup"><span data-stu-id="df93f-219">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="df93f-220">ASP.NET Core 3,0, Web API yetkilendirmesi desteğini kullanarak tek sayfalı uygulamalarda (Spaon) kimlik doğrulaması sunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="df93f-220">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="df93f-221">ASP.NET Core Identitykimlik doğrulama ve depolama kullanıcıları için OpenID Connect 'i uygulamak üzere [ Identity Server4](https://identityserver.io/) ile birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-221">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="df93f-222">IdentityServer4, ASP.NET Core 3,0 için bir OpenID Connect ve OAuth 2,0 çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="df93f-222">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="df93f-223">Aşağıdaki güvenlik özelliklerini sunar:</span><span class="sxs-lookup"><span data-stu-id="df93f-223">It enables the following security features:</span></span>

* <span data-ttu-id="df93f-224">Hizmet olarak kimlik doğrulaması (AaaS)</span><span class="sxs-lookup"><span data-stu-id="df93f-224">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="df93f-225">Birden çok uygulama türü üzerinde çoklu oturum açma/kapatma (SSO)</span><span class="sxs-lookup"><span data-stu-id="df93f-225">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="df93f-226">API 'Ler için erişim denetimi</span><span class="sxs-lookup"><span data-stu-id="df93f-226">Access control for APIs</span></span>
* <span data-ttu-id="df93f-227">Federasyon ağ geçidi</span><span class="sxs-lookup"><span data-stu-id="df93f-227">Federation Gateway</span></span>

<span data-ttu-id="df93f-228">Daha fazla bilgi için bkz [. Identity Server4 belgeleri](http://docs.identityserver.io/en/latest/index.html) veya [kimlik doğrulaması ve kimlik doğrulama ve yetkilendirme](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="df93f-228">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="df93f-229">Sertifika ve Kerberos kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="df93f-229">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="df93f-230">Sertifika kimlik doğrulaması şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="df93f-230">Certificate authentication requires:</span></span>

* <span data-ttu-id="df93f-231">Sunucu, sertifikaları kabul edecek şekilde yapılandırılıyor.</span><span class="sxs-lookup"><span data-stu-id="df93f-231">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="df93f-232">Kimlik doğrulama ara yazılımı ' ye ekleme `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="df93f-232">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="df93f-233">' De sertifika kimlik doğrulama hizmeti ekleniyor `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="df93f-233">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="df93f-234">Sertifika kimlik doğrulaması seçenekleri şunları yapabilme:</span><span class="sxs-lookup"><span data-stu-id="df93f-234">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="df93f-235">Otomatik olarak imzalanan sertifikaları kabul edin.</span><span class="sxs-lookup"><span data-stu-id="df93f-235">Accept self-signed certificates.</span></span>
* <span data-ttu-id="df93f-236">Sertifika iptali olup olmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="df93f-236">Check for certificate revocation.</span></span>
* <span data-ttu-id="df93f-237">Profili oluşturulan sertifikanın, içinde doğru kullanım bayrakları olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="df93f-237">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="df93f-238">Varsayılan bir Kullanıcı sorumlusu, sertifika özelliklerinden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="df93f-238">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="df93f-239">Kullanıcı sorumlusu, sorumlunun takıma veya değiştirilmesine izin veren bir olay içerir.</span><span class="sxs-lookup"><span data-stu-id="df93f-239">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="df93f-240">Daha fazla bilgi için bkz. <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="df93f-240">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="df93f-241">[Windows kimlik doğrulaması](/windows-server/security/windows-authentication/windows-authentication-overview) , Linux ve MacOS üzerine genişletildi.</span><span class="sxs-lookup"><span data-stu-id="df93f-241">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="df93f-242">Önceki sürümlerde, Windows kimlik doğrulaması [IIS](xref:host-and-deploy/iis/index) ve [httpsys](xref:fundamentals/servers/httpsys)ile sınırlandırıldı.</span><span class="sxs-lookup"><span data-stu-id="df93f-242">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="df93f-243">ASP.NET Core 3,0 ' de [Kestrel](xref:fundamentals/servers/kestrel) , Windows etki alanına katılmış konaklar için Windows, Linux ve MacOS üzerinde Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)ve [NTLM](/windows-server/security/kerberos/ntlm-overview)kullanma olanağına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="df93f-243">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="df93f-244">Bu kimlik doğrulama düzenlerinin Kestrel desteği [Microsoft. AspNetCore. Authentication. Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="df93f-244">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="df93f-245">Diğer kimlik doğrulama hizmetlerinde olduğu gibi, kimlik doğrulama uygulaması genelinde yapılandırın ve ardından hizmeti yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="df93f-245">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="df93f-246">Ana bilgisayar gereksinimleri:</span><span class="sxs-lookup"><span data-stu-id="df93f-246">Host requirements:</span></span>

* <span data-ttu-id="df93f-247">Windows Konakları, uygulamayı barındıran Kullanıcı hesabına eklenmiş [hizmet sorumlusu adlarına](/windows/win32/ad/service-principal-names) (SPN) sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="df93f-247">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="df93f-248">Linux ve macOS makineleri etki alanına katılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="df93f-248">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="df93f-249">Web işlemi için SPN oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="df93f-249">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="df93f-250">Ana makinede [keytab dosyalarının](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) oluşturulup yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="df93f-250">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="df93f-251">Daha fazla bilgi için bkz. <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="df93f-251">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="df93f-252">Şablon değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="df93f-252">Template changes</span></span>

<span data-ttu-id="df93f-253">Web UI şablonları ( Razor Sayfalar, denetleyici ve GÖRÜNÜMLERLE Mvc) aşağıdaki gibi kaldırılmıştır:</span><span class="sxs-lookup"><span data-stu-id="df93f-253">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="df93f-254">cookieOnay Kullanıcı arabirimi artık dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="df93f-254">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="df93f-255">cookieASP.NET Core 3,0 şablon tarafından oluşturulan bir uygulamada izin özelliğini etkinleştirmek için, bkz <xref:security/gdpr> ..</span><span class="sxs-lookup"><span data-stu-id="df93f-255">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="df93f-256">Betiklerin ve ilgili statik varlıkların artık CDNs kullanmak yerine yerel dosyalar olarak başvuruluyor.</span><span class="sxs-lookup"><span data-stu-id="df93f-256">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="df93f-257">Daha fazla bilgi için, bkz. [betikler ve ilgili statik varlıklar artık geçerli ortama (ASPNET/AspNetCore.Docs #14350) göre CDNs kullanmak yerine yerel dosyalar olarak başvuruluyor](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="df93f-257">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="df93f-258">Angular şablonu, angular 8 ' i kullanacak şekilde güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="df93f-258">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="df93f-259">RazorSınıf kitaplığı (RCL) şablonu Razor Varsayılan olarak varsayılan olarak bileşen geliştirme olur.</span><span class="sxs-lookup"><span data-stu-id="df93f-259">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="df93f-260">Visual Studio 'da yeni bir şablon seçeneği, sayfalar ve görünümler için şablon desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="df93f-260">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="df93f-261">Bir komut kabuğunda şablondan RCL oluştururken, `--support-pages-and-views` () seçeneğini geçirin `dotnet new razorclasslib --support-pages-and-views` .</span><span class="sxs-lookup"><span data-stu-id="df93f-261">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="df93f-262">Genel Konak</span><span class="sxs-lookup"><span data-stu-id="df93f-262">Generic Host</span></span>

<span data-ttu-id="df93f-263">ASP.NET Core 3,0 şablonları kullanılır <xref:fundamentals/host/generic-host> .</span><span class="sxs-lookup"><span data-stu-id="df93f-263">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="df93f-264">Önceki sürümler kullanıldı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="df93f-264">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="df93f-265">.NET Core Genel Host () kullanımı, <xref:Microsoft.Extensions.Hosting.HostBuilder> Web 'e özgü olmayan diğer sunucu senaryolarıyla ASP.NET Core uygulamaları daha iyi tümleştirme sağlar.</span><span class="sxs-lookup"><span data-stu-id="df93f-265">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="df93f-266">Daha fazla bilgi için bkz. [Hostbuilder WebHostBuilder 'ın yerini alır](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="df93f-266">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="df93f-267">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="df93f-267">Host configuration</span></span>

<span data-ttu-id="df93f-268">ASP.NET Core 3,0 ' nin yayınlanmasından önce, `ASPNETCORE_` Web konağının ana bilgisayar yapılandırması için, öneki olan ortam değişkenleri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="df93f-268">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="df93f-269">3,0 ' de, `AddEnvironmentVariables` `DOTNET_` ile konak yapılandırması için önekli ortam değişkenlerini yüklemek için kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="df93f-269">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="df93f-270">Başlangıç Oluşturucu Ekleme değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="df93f-270">Changes to Startup constructor injection</span></span>

<span data-ttu-id="df93f-271">Genel konak yalnızca Oluşturucu ekleme için aşağıdaki türleri destekler `Startup` :</span><span class="sxs-lookup"><span data-stu-id="df93f-271">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="df93f-272">Tüm hizmetler yine de yönteme bağımsız değişken olarak eklenebilir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="df93f-272">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="df93f-273">Daha fazla bilgi için bkz. [genel ana bilgisayar başlangıç Oluşturucu Ekleme (ASPNET/duyurular #353)](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="df93f-273">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="df93f-274">Kestrel</span><span class="sxs-lookup"><span data-stu-id="df93f-274">Kestrel</span></span>

* <span data-ttu-id="df93f-275">Kestrel yapılandırması, genel konağa geçiş için güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="df93f-275">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="df93f-276">3,0 ' de, Kestrel tarafından sağlanmış Web ana bilgisayar Oluşturucu üzerinde yapılandırılır `ConfigureWebHostDefaults` .</span><span class="sxs-lookup"><span data-stu-id="df93f-276">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="df93f-277">Bağlantı bağdaştırıcıları, Kestrel adresinden kaldırılmıştır ve bağlantı ara yazılımı ile değiştirilmiştir ve bu, ASP.NET Core işlem hattındaki HTTP ara hattına benzer ancak alt düzey bağlantılar için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="df93f-277">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="df93f-278">Kestrel aktarım katmanı, içinde ortak bir arabirim olarak kullanıma sunuldu `Connections.Abstractions` .</span><span class="sxs-lookup"><span data-stu-id="df93f-278">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="df93f-279">Sondaki üstbilgiler yeni bir koleksiyona taşınarak üstbilgiler ve tanıtımları arasındaki belirsizlik çözüldü.</span><span class="sxs-lookup"><span data-stu-id="df93f-279">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="df93f-280">Gibi zaman uyumlu g/ç API 'Leri `HttpRequest.Body.Read` , uygulama kilitlenmelerine neden olan yaygın bir iş parçacığı kaynağıdır.</span><span class="sxs-lookup"><span data-stu-id="df93f-280">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="df93f-281">3,0 ' de `AllowSynchronousIO` Varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="df93f-281">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="df93f-282">Daha fazla bilgi için bkz. <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="df93f-282">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="df93f-283">Varsayılan olarak etkin HTTP/2</span><span class="sxs-lookup"><span data-stu-id="df93f-283">HTTP/2 enabled by default</span></span>

<span data-ttu-id="df93f-284">HTTP/2, HTTPS uç noktaları için Kestrel içinde varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="df93f-284">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="df93f-285">IIS veya HTTP.sys için HTTP/2 desteği, işletim sistemi tarafından desteklendikleri zaman etkindir.</span><span class="sxs-lookup"><span data-stu-id="df93f-285">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="df93f-286">İstek üzerine EventCounters</span><span class="sxs-lookup"><span data-stu-id="df93f-286">EventCounters on request</span></span>

<span data-ttu-id="df93f-287">EventSource barındırma, `Microsoft.AspNetCore.Hosting` <xref:System.Diagnostics.Tracing.EventCounter> gelen isteklerle ilgili aşağıdaki yeni türleri yayar:</span><span class="sxs-lookup"><span data-stu-id="df93f-287">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="df93f-288">Uç nokta yönlendirme</span><span class="sxs-lookup"><span data-stu-id="df93f-288">Endpoint routing</span></span>

<span data-ttu-id="df93f-289">Çerçeveler 'in (örneğin, MVC) ara yazılım ile iyi çalışmasına izin veren uç nokta yönlendirme geliştirildi:</span><span class="sxs-lookup"><span data-stu-id="df93f-289">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="df93f-290">Ara yazılım ve uç noktaların sırası istek işleme ardışık düzeninde yapılandırılabilir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="df93f-290">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="df93f-291">Uç noktalar ve ara yazılımlar, sistem durumu denetimleri gibi diğer ASP.NET Core tabanlı teknolojilerle birlikte.</span><span class="sxs-lookup"><span data-stu-id="df93f-291">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="df93f-292">Uç noktalar, hem yazılım hem de MVC 'de CORS veya yetkilendirme gibi bir ilke uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-292">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="df93f-293">Filtreler ve öznitelikler, denetleyicilerde yöntemler üzerine yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-293">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="df93f-294">Daha fazla bilgi için bkz. <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="df93f-294">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="df93f-295">Sistem durumu denetimleri</span><span class="sxs-lookup"><span data-stu-id="df93f-295">Health Checks</span></span>

<span data-ttu-id="df93f-296">Sistem durumu denetimleri, genel ana bilgisayar ile Endpoint Routing kullanır.</span><span class="sxs-lookup"><span data-stu-id="df93f-296">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="df93f-297">' De, uç nokta `Startup.Configure` `MapHealthChecks` URL 'si veya göreli yol ile Endpoint Builder ' ı çağırın:</span><span class="sxs-lookup"><span data-stu-id="df93f-297">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="df93f-298">Durum denetimleri uç noktaları şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="df93f-298">Health Checks endpoints can:</span></span>

* <span data-ttu-id="df93f-299">İzin verilen bir veya daha fazla Konakları/bağlantı noktasını belirtin.</span><span class="sxs-lookup"><span data-stu-id="df93f-299">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="df93f-300">Yetkilendirme gerektir.</span><span class="sxs-lookup"><span data-stu-id="df93f-300">Require authorization.</span></span>
* <span data-ttu-id="df93f-301">CORS gerektir.</span><span class="sxs-lookup"><span data-stu-id="df93f-301">Require CORS.</span></span>

<span data-ttu-id="df93f-302">Daha fazla bilgi için aşağıdaki makaleleri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="df93f-302">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="df93f-303">HttpContext üzerindeki kanallar</span><span class="sxs-lookup"><span data-stu-id="df93f-303">Pipes on HttpContext</span></span>

<span data-ttu-id="df93f-304">Artık istek gövdesini okumak ve yanıt gövdesini API kullanarak yazmak mümkündür <xref:System.IO.Pipelines> .</span><span class="sxs-lookup"><span data-stu-id="df93f-304">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="df93f-305">Bu</span><span class="sxs-lookup"><span data-stu-id="df93f-305">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="df93f-306">`HttpRequest.BodyReader` özelliği <xref:System.IO.Pipelines.PipeReader> , istek gövdesini okumak için kullanılabilecek bir sağlar.</span><span class="sxs-lookup"><span data-stu-id="df93f-306">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="df93f-307">Bu</span><span class="sxs-lookup"><span data-stu-id="df93f-307">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="df93f-308">`HttpResponse.BodyWriter` özelliği <xref:System.IO.Pipelines.PipeWriter> , yanıt gövdesini yazmak için kullanılabilecek bir sağlar.</span><span class="sxs-lookup"><span data-stu-id="df93f-308">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="df93f-309">`HttpRequest.BodyReader` akışın analog bir `HttpRequest.Body` kadır.</span><span class="sxs-lookup"><span data-stu-id="df93f-309">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="df93f-310">`HttpResponse.BodyWriter` akışın analog bir `HttpResponse.Body` kadır.</span><span class="sxs-lookup"><span data-stu-id="df93f-310">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="df93f-311">IIS 'de geliştirilmiş hata raporlama</span><span class="sxs-lookup"><span data-stu-id="df93f-311">Improved error reporting in IIS</span></span>

<span data-ttu-id="df93f-312">IIS 'de ASP.NET Core uygulamalar barındırırken başlatma hataları artık daha zengin Tanılama verileri oluşturuyor.</span><span class="sxs-lookup"><span data-stu-id="df93f-312">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="df93f-313">Bu hatalar, geçerli her yerde yığın izlemelerle Windows olay günlüğü 'ne bildirilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-313">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="df93f-314">Ayrıca, tüm uyarılar, hatalar ve işlenmemiş özel durumlar Windows olay günlüğü 'ne kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-314">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="df93f-315">Çalışan hizmeti ve çalışan SDK 'Sı</span><span class="sxs-lookup"><span data-stu-id="df93f-315">Worker Service and Worker SDK</span></span>

<span data-ttu-id="df93f-316">.NET Core 3,0 yeni çalışan hizmeti uygulama şablonunu tanıtır.</span><span class="sxs-lookup"><span data-stu-id="df93f-316">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="df93f-317">Bu şablon, .NET Core 'da uzun süre çalışan hizmetler yazmak için bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="df93f-317">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="df93f-318">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="df93f-318">For more information, see:</span></span>

* [<span data-ttu-id="df93f-319">Windows Hizmetleri olarak .NET Core çalışanları</span><span class="sxs-lookup"><span data-stu-id="df93f-319">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="df93f-320">İletilen üstbilgiler ara yazılımı geliştirmeleri</span><span class="sxs-lookup"><span data-stu-id="df93f-320">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="df93f-321">Önceki ASP.NET Core sürümlerinde, <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> bir Azure Linux 'a DAĞıTıLıRKEN veya IIS dışında herhangi bir ters proxy 'nin arkasında sorun.</span><span class="sxs-lookup"><span data-stu-id="df93f-321">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="df93f-322">Önceki sürümlere yönelik düzeltmeler, [Linux ve IIS olmayan ters proxy 'lerin düzenini iletme](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)bölümünde belgelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="df93f-322">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="df93f-323">Bu senaryo ASP.NET Core 3,0 ' de düzeltilmiştir.</span><span class="sxs-lookup"><span data-stu-id="df93f-323">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="df93f-324">Ortam değişkeni olarak ayarlandığında, ana bilgisayar [Iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) sağlar `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="df93f-324">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="df93f-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` , `true` kapsayıcı görüntülerimizde olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="df93f-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="df93f-326">Performans geliştirmeleri</span><span class="sxs-lookup"><span data-stu-id="df93f-326">Performance improvements</span></span>

<span data-ttu-id="df93f-327">ASP.NET Core 3,0, bellek kullanımını azaltan ve üretilen işi geliştiren birçok geliştirme içerir:</span><span class="sxs-lookup"><span data-stu-id="df93f-327">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="df93f-328">Kapsamlı hizmetler için yerleşik bağımlılık ekleme kapsayıcısını kullanırken bellek kullanımında azaltma.</span><span class="sxs-lookup"><span data-stu-id="df93f-328">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="df93f-329">Ara yazılım senaryoları ve yönlendirme dahil olmak üzere çerçeve genelinde ayırmalarda azaltma.</span><span class="sxs-lookup"><span data-stu-id="df93f-329">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="df93f-330">WebSocket bağlantıları için bellek kullanımında azaltma.</span><span class="sxs-lookup"><span data-stu-id="df93f-330">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="df93f-331">HTTPS bağlantıları için bellek azaltma ve verimlilik geliştirmeleri.</span><span class="sxs-lookup"><span data-stu-id="df93f-331">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="df93f-332">Yeni iyileştirilmiş ve tamamen zaman uyumsuz JSON serileştiricisi.</span><span class="sxs-lookup"><span data-stu-id="df93f-332">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="df93f-333">Form ayrıştırılırken bellek kullanımı ve üretilen iş iyileştirmeleri azaltılması.</span><span class="sxs-lookup"><span data-stu-id="df93f-333">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="df93f-334">ASP.NET Core 3,0 yalnızca .NET Core 3,0 üzerinde çalışır</span><span class="sxs-lookup"><span data-stu-id="df93f-334">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="df93f-335">ASP.NET Core 3,0 itibariyle, .NET Framework artık desteklenen bir hedef çerçeve değildir.</span><span class="sxs-lookup"><span data-stu-id="df93f-335">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="df93f-336">.NET Framework hedefleyen projeler [.NET Core 2,1 LTS sürümünü](https://dotnet.microsoft.com/download/dotnet-core/2.1)kullanarak tam olarak desteklenen bir biçimde devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="df93f-336">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="df93f-337">ASP.NET Core 2.1. x ile ilgili paketlerin çoğu, .NET Core 2,1 için üç yıllık LTS döneminin ötesinde süresiz olarak desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="df93f-337">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="df93f-338">Geçiş bilgileri için bkz. [kodunuzu .NET Core 'a .NET Framework](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="df93f-338">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="df93f-339">ASP.NET Core paylaşılan çerçevesini kullanma</span><span class="sxs-lookup"><span data-stu-id="df93f-339">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="df93f-340">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunan ASP.NET Core 3,0 paylaşılan çerçevesi artık `<PackageReference />` Proje dosyasında açık bir öğe gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="df93f-340">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="df93f-341">Proje dosyasında SDK kullanılırken paylaşılan çerçeveye otomatik olarak başvurulur `Microsoft.NET.Sdk.Web` :</span><span class="sxs-lookup"><span data-stu-id="df93f-341">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="df93f-342">ASP.NET Core paylaşılan çerçevesinden kaldırılan derlemeler</span><span class="sxs-lookup"><span data-stu-id="df93f-342">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="df93f-343">ASP.NET Core 3,0 paylaşılan çerçevesinden çıkarılan en önemli derlemeler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="df93f-343">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="df93f-344">[Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.net).</span><span class="sxs-lookup"><span data-stu-id="df93f-344">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="df93f-345">ASP.NET Core 3,0 ' ye Json.NET eklemek için bkz. [Newtonsoft.Jstabanlı JSON biçimi desteği ekleme](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="df93f-345">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="df93f-346">ASP.NET Core 3,0 `System.Text.Json` , JSON okuma ve yazma için tanıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="df93f-346">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="df93f-347">Daha fazla bilgi için bu belgede [yenı JSON serileştirmesi](#new-json-serialization) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="df93f-347">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="df93f-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="df93f-348">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="df93f-349">Paylaşılan çerçeveden kaldırılan derlemelerin tamamen listesi için bkz [. Microsoft. AspNetCore. App 3,0 ' den kaldırılan derlemeler](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="df93f-349">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="df93f-350">Bu değişiklik için mosyon hakkında daha fazla bilgi için bkz. [3,0 'de Microsoft. AspNetCore. app 'e yönelik son değişiklikler](https://github.com/aspnet/Announcements/issues/325) ve [ASP.NET Core 3,0 ' de gelen değişikliklere ilk bakış](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="df93f-350">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 