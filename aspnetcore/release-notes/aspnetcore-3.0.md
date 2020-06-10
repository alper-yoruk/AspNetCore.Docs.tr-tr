---
title: ASP.NET Core 3,0 ' deki yenilikler
author: rick-anderson
description: ASP.NET Core 3,0 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 38a75e9577bab6bbdb5accf161659e8e301947b1
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105941"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="8d63c-103">ASP.NET Core 3,0 ' deki yenilikler</span><span class="sxs-lookup"><span data-stu-id="8d63c-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="8d63c-104">Bu makalede, ASP.NET Core 3,0 ' deki en önemli değişiklikler ilgili belgelerin bağlantılarıyla vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

Blazor<span data-ttu-id="8d63c-105">, .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmak için ASP.NET Core yeni bir çerçevedir:</span><span class="sxs-lookup"><span data-stu-id="8d63c-105"> is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="8d63c-106">JavaScript yerine C# kullanarak zengin etkileşimli uo 'lar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8d63c-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="8d63c-107">.NET ' te yazılmış sunucu tarafı ve istemci tarafı uygulama mantığını paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8d63c-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="8d63c-108">Mobil tarayıcılar dahil olmak üzere geniş tarayıcı desteği için Kullanıcı arabirimini HTML ve CSS olarak işleme.</span><span class="sxs-lookup"><span data-stu-id="8d63c-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

Blazor<span data-ttu-id="8d63c-109">çerçeve tarafından desteklenen senaryolar:</span><span class="sxs-lookup"><span data-stu-id="8d63c-109"> framework supported scenarios:</span></span>

* <span data-ttu-id="8d63c-110">Yeniden kullanılabilir kullanıcı arabirimi bileşenleri ( Razor bileşenler)</span><span class="sxs-lookup"><span data-stu-id="8d63c-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="8d63c-111">İstemci tarafı yönlendirme</span><span class="sxs-lookup"><span data-stu-id="8d63c-111">Client-side routing</span></span>
* <span data-ttu-id="8d63c-112">Bileşen düzenleri</span><span class="sxs-lookup"><span data-stu-id="8d63c-112">Component layouts</span></span>
* <span data-ttu-id="8d63c-113">Bağımlılık ekleme desteği</span><span class="sxs-lookup"><span data-stu-id="8d63c-113">Support for dependency injection</span></span>
* <span data-ttu-id="8d63c-114">Formlar ve doğrulama</span><span class="sxs-lookup"><span data-stu-id="8d63c-114">Forms and validation</span></span>
* <span data-ttu-id="8d63c-115">Sınıf kitaplıklarıyla bileşen kitaplıkları derleme Razor</span><span class="sxs-lookup"><span data-stu-id="8d63c-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="8d63c-116">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="8d63c-116">JavaScript interop</span></span>

<span data-ttu-id="8d63c-117">Daha fazla bilgi için bkz. <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="8d63c-117">For more information, see <xref:blazor/index>.</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="8d63c-118">Server</span><span class="sxs-lookup"><span data-stu-id="8d63c-118"> Server</span></span>

Blazor<span data-ttu-id="8d63c-119">Kullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-119"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="8d63c-120">Sunucu Razor , bir ASP.NET Core uygulamasındaki sunucuda bileşenleri barındırmak için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="8d63c-120"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="8d63c-121">Kullanıcı Arabirimi güncelleştirmeleri bir bağlantı üzerinden işlenir SignalR .</span><span class="sxs-lookup"><span data-stu-id="8d63c-121">UI updates are handled over a SignalR connection.</span></span> Blazor<span data-ttu-id="8d63c-122">Sunucu ASP.NET Core 3,0 ' de desteklenir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-122"> Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="blazor-webassembly-preview"></a>Blazor<span data-ttu-id="8d63c-123">WebAssembly (Önizleme)</span><span class="sxs-lookup"><span data-stu-id="8d63c-123"> WebAssembly (Preview)</span></span>

Blazor<span data-ttu-id="8d63c-124">uygulamalar, bir WebAssembly tabanlı .NET çalışma zamanı kullanarak doğrudan tarayıcıda da çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-124"> apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> Blazor<span data-ttu-id="8d63c-125">WebAssembly Önizleme *aşamasındadır ve ASP.NET Core 3,0 ' de* desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="8d63c-125"> WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> Blazor<span data-ttu-id="8d63c-126">WebAssembly ASP.NET Core gelecek bir sürümünde desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-126"> WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a>Razor<span data-ttu-id="8d63c-127">bileşenleri</span><span class="sxs-lookup"><span data-stu-id="8d63c-127"> components</span></span>

Blazor<span data-ttu-id="8d63c-128">uygulamalar bileşenlerden oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="8d63c-128"> apps are built from components.</span></span> <span data-ttu-id="8d63c-129">Bileşenler, bir sayfa, iletişim kutusu veya form gibi kullanıcı arabirimi (UI) için kendi içinde yer alan öbeklerdir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-129">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="8d63c-130">Bileşenler, Kullanıcı arabirimi işleme mantığını ve istemci tarafı olay işleyicilerini tanımlayan normal .NET sınıflarıdır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-130">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="8d63c-131">JavaScript olmadan zengin etkileşimli Web uygulamaları oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8d63c-131">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="8d63c-132">İçindeki bileşenler Blazor genellıkle Razor HTML ve C# ' nin doğal bir karışımı olan sözdizimi kullanılarak yazılır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-132">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> Razor<span data-ttu-id="8d63c-133">bileşenler, Razor her ikisi de kullandıkları sayfalara ve MVC görünümlerine benzerdir Razor .</span><span class="sxs-lookup"><span data-stu-id="8d63c-133"> components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="8d63c-134">Bir istek-yanıt modelini temel alan sayfaların ve görünümlerin aksine, bileşenler Kullanıcı arabirimi oluşturmayı işlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-134">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="8d63c-135">gRPC</span><span class="sxs-lookup"><span data-stu-id="8d63c-135">gRPC</span></span>

<span data-ttu-id="8d63c-136">[GRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="8d63c-136">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="8d63c-137">Popüler, yüksek performanslı bir RPC (uzak yordam çağrısı) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-137">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="8d63c-138">, API geliştirmeye yönelik olarak yapılan bir sözleşmenin ilk yaklaşımını sağlar.</span><span class="sxs-lookup"><span data-stu-id="8d63c-138">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="8d63c-139">, Gibi modern teknolojiler kullanır:</span><span class="sxs-lookup"><span data-stu-id="8d63c-139">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="8d63c-140">Taşıma için HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8d63c-140">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="8d63c-141">Arabirim açıklaması dili olarak protokol arabellekleri.</span><span class="sxs-lookup"><span data-stu-id="8d63c-141">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="8d63c-142">İkili serileştirme biçimi.</span><span class="sxs-lookup"><span data-stu-id="8d63c-142">Binary serialization format.</span></span>
* <span data-ttu-id="8d63c-143">Şöyle özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="8d63c-143">Provides features such as:</span></span>

  * <span data-ttu-id="8d63c-144">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="8d63c-144">Authentication</span></span>
  * <span data-ttu-id="8d63c-145">Çift yönlü akış ve akış denetimi.</span><span class="sxs-lookup"><span data-stu-id="8d63c-145">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="8d63c-146">İptal ve zaman aşımları.</span><span class="sxs-lookup"><span data-stu-id="8d63c-146">Cancellation and timeouts.</span></span>

<span data-ttu-id="8d63c-147">ASP.NET Core 3,0 ' deki gRPC işlevselliği şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="8d63c-147">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="8d63c-148">[GRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): GRPC hizmetlerini barındırmak için bir ASP.NET Core çerçevesi.</span><span class="sxs-lookup"><span data-stu-id="8d63c-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="8d63c-149">gRPC on ASP.NET Core, günlüğe kaydetme, bağımlılık ekleme (dı), kimlik doğrulama ve yetkilendirme gibi standart ASP.NET Core özelliklerle tümleştirilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-149">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="8d63c-150">[GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client): .NET Core için tanıdık bir GRPC istemcisi `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client): A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="8d63c-151">[GRPC .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): GRPC istemcisi ile tümleştirme `HttpClientFactory` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="8d63c-152">Daha fazla bilgi için bkz. <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="8d63c-152">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="8d63c-153">Bkz. geçiş yönergeleri için [ SignalR kodu güncelleştirme](xref:migration/22-to-30#signalr) .</span><span class="sxs-lookup"><span data-stu-id="8d63c-153">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> SignalR<span data-ttu-id="8d63c-154">Artık `System.Text.Json` JSON iletilerini seri hale getirmek/seri durumdan çıkarmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-154"> now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="8d63c-155">Tabanlı serileştiriciyi geri yükleme yönergeleri için bkz. [Newtonsoft. JSON öğesine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson) `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-155">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="8d63c-156">İçin JavaScript ve .NET Istemcilerinde SignalR , otomatik yeniden bağlanma için destek eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-156">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="8d63c-157">Varsayılan olarak, istemci hemen yeniden bağlanmaya çalışır ve gerekirse 2, 10 ve 30 saniye sonra yeniden dener.</span><span class="sxs-lookup"><span data-stu-id="8d63c-157">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="8d63c-158">İstemci başarıyla yeniden bağlanırsa, yeni bir bağlantı KIMLIĞI alır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-158">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="8d63c-159">Otomatik yeniden bağlanma kabul etme:</span><span class="sxs-lookup"><span data-stu-id="8d63c-159">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="8d63c-160">Yeniden bağlantı aralıkları bir dizi milisaniyelik tabanlı süre geçirerek belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="8d63c-160">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="8d63c-161">Yeniden bağlantı aralıklarının tam denetimi için özel bir uygulama geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-161">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="8d63c-162">Son yeniden bağlanma aralığından sonra yeniden bağlantı başarısız olursa:</span><span class="sxs-lookup"><span data-stu-id="8d63c-162">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="8d63c-163">İstemci, bağlantının çevrimdışı olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="8d63c-163">The client considers the connection is offline.</span></span>
* <span data-ttu-id="8d63c-164">İstemci yeniden bağlanmayı denemeyi durduruyor.</span><span class="sxs-lookup"><span data-stu-id="8d63c-164">The client stops trying to reconnect.</span></span>

<span data-ttu-id="8d63c-165">Yeniden bağlanma denemeleri sırasında, kullanıcıya yeniden bağlantı denenmekte olduğunu bildirmek için uygulama kullanıcı arabirimini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="8d63c-165">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="8d63c-166">Bağlantı kesildiğinde UI geri bildirimi sağlamak için, SignalR istemci API 'si aşağıdaki olay işleyicilerini içerecek şekilde genişletilir:</span><span class="sxs-lookup"><span data-stu-id="8d63c-166">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="8d63c-167">`onreconnecting`: Geliştiricilere Kullanıcı arabirimini devre dışı bırakma veya uygulamanın çevrimdışı olduğunu bilmesini sağlayan bir fırsat sağlar.</span><span class="sxs-lookup"><span data-stu-id="8d63c-167">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="8d63c-168">`onreconnected`: Bağlantı kurulduktan sonra geliştiricilere Kullanıcı arabirimini güncelleştirme fırsatı verir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-168">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="8d63c-169">Aşağıdaki kod, `onreconnecting` bağlanmaya çalışırken kullanıcı arabirimini güncelleştirmek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="8d63c-169">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="8d63c-170">Aşağıdaki kod, `onreconnected` bağlantıda Kullanıcı arabirimini güncelleştirmek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="8d63c-170">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR<span data-ttu-id="8d63c-171">3,0 ve üzeri, bir hub yöntemi yetkilendirme gerektirdiğinde, yetkilendirme işleyicilerine özel bir kaynak sağlar.</span><span class="sxs-lookup"><span data-stu-id="8d63c-171"> 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="8d63c-172">Kaynak bir örneğidir `HubInvocationContext` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-172">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="8d63c-173">`HubInvocationContext`Şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="8d63c-173">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="8d63c-174">Çağrılan hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="8d63c-174">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="8d63c-175">Hub yöntemi için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="8d63c-175">Arguments to the hub method.</span></span>

<span data-ttu-id="8d63c-176">Azure Active Directory aracılığıyla birden çok kuruluşun oturum açmasına izin veren bir sohbet odası uygulamasının aşağıdaki örneğini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="8d63c-176">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="8d63c-177">Microsoft hesabı herkes sohbet için oturum açabilir, ancak yalnızca sahip olunan kuruluşun üyeleri kullanıcıları veya kullanıcıların sohbet geçmişlerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-177">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="8d63c-178">Uygulama belirli kullanıcılardan belirli işlevleri kısıtlayabilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-178">The app could restrict certain functionality from specific users.</span></span>

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

<span data-ttu-id="8d63c-179">Yukarıdaki kodda `DomainRestrictedRequirement` özel işlevi görür `IAuthorizationRequirement` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-179">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="8d63c-180">`HubInvocationContext`Kaynak parametresi geçirildiğinden iç mantık şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="8d63c-180">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="8d63c-181">Hub 'ın çağrıldığı bağlamı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="8d63c-181">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="8d63c-182">Kullanıcının bireysel hub yöntemlerini yürütmesine izin verirken kararlar alın.</span><span class="sxs-lookup"><span data-stu-id="8d63c-182">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="8d63c-183">Tek tek hub yöntemleri, çalışma zamanında kodun denetlediği ilkenin adıyla işaretlenebilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-183">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="8d63c-184">İstemciler tek tek hub yöntemlerini çağırmayı denediğinden, `DomainRestrictedRequirement` işleyici çalışır ve yöntemlere erişimi denetler.</span><span class="sxs-lookup"><span data-stu-id="8d63c-184">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="8d63c-185">`DomainRestrictedRequirement`Denetimlerin erişim yöntemine göre:</span><span class="sxs-lookup"><span data-stu-id="8d63c-185">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="8d63c-186">Tüm oturum açmış kullanıcılar `SendMessage` yöntemini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-186">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="8d63c-187">Yalnızca bir `@jabbr.net` e-posta adresiyle oturum açan kullanıcılar, kullanıcıların geçmişlerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-187">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="8d63c-188">Yalnızca `bob42@jabbr.net` sohbet odasından kullanıcıları Ban yapabilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-188">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

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

<span data-ttu-id="8d63c-189">İlkeyi oluşturmak `DomainRestricted` şunları içerebilir:</span><span class="sxs-lookup"><span data-stu-id="8d63c-189">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="8d63c-190">*Startup.cs*' de, yeni ilkeyi ekleme.</span><span class="sxs-lookup"><span data-stu-id="8d63c-190">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="8d63c-191">Özel `DomainRestrictedRequirement` gereksinimi parametre olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="8d63c-191">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="8d63c-192">`DomainRestricted`Yetkilendirme ara yazılımı ile kaydetme.</span><span class="sxs-lookup"><span data-stu-id="8d63c-192">Registering `DomainRestricted` with the authorization middleware.</span></span>

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

SignalR<span data-ttu-id="8d63c-193">Hub [uç nokta yönlendirme](xref:fundamentals/routing)kullanır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-193"> hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> SignalR<span data-ttu-id="8d63c-194">Hub bağlantısı daha önce açık olarak yapıldı:</span><span class="sxs-lookup"><span data-stu-id="8d63c-194"> hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="8d63c-195">Önceki sürümde, geliştiriciler, Razor Sayfalar ve hub 'ları çeşitli yerlerde bağlamak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-195">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="8d63c-196">Açık bağlantı, neredeyse aynı bir dizi yönlendirme segmentine neden olur:</span><span class="sxs-lookup"><span data-stu-id="8d63c-196">Explicit connection results in a series of nearly-identical routing segments:</span></span>

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

SignalR<span data-ttu-id="8d63c-197">3,0 hub 'ları, uç nokta yönlendirme aracılığıyla yönlendirilebilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-197"> 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="8d63c-198">Uç nokta yönlendirme ile, genellikle tüm yönlendirme ' de yapılandırılabilir `UseRouting` :</span><span class="sxs-lookup"><span data-stu-id="8d63c-198">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="8d63c-199">ASP.NET Core 3,0 SignalR eklendi:</span><span class="sxs-lookup"><span data-stu-id="8d63c-199">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="8d63c-200">İstemciden sunucuya akış.</span><span class="sxs-lookup"><span data-stu-id="8d63c-200">Client-to-server streaming.</span></span> <span data-ttu-id="8d63c-201">İstemciden sunucuya akışla, sunucu tarafı yöntemleri veya örneklerinden birini alabilir `IAsyncEnumerable<T>` `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-201">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="8d63c-202">Aşağıdaki C# örneğinde, `UploadStream` hub 'daki Yöntem istemciden dizelerin akışını alacaktır:</span><span class="sxs-lookup"><span data-stu-id="8d63c-202">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="8d63c-203">.NET istemci uygulamaları, `IAsyncEnumerable<T>` `ChannelReader<T>` `stream` Yukarıdaki hub yönteminin bağımsız değişkeni olarak bir ya da örneği geçirebilir `UploadStream` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-203">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="8d63c-204">Döngü tamamlandıktan `for` ve yerel işlev çıktıktan sonra, akış tamamlama gönderilir:</span><span class="sxs-lookup"><span data-stu-id="8d63c-204">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

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

<span data-ttu-id="8d63c-205">JavaScript istemci uygulamaları, SignalR `Subject` Yukarıdaki hub yönteminin bağımsız değişkeni olarak (veya bir [Rxjs konusu](https://rxjs.dev/api/index/class/Subject)) kullanır `stream` `UploadStream` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-205">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="8d63c-206">JavaScript kodu, `subject.next` yakalandıkları ve sunucuya gönderilmeye hazırlanıyor gibi dizeleri işlemek için yöntemini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-206">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="8d63c-207">Önceki iki kod parçacığı gibi kodu kullanarak gerçek zamanlı akış deneyimleri oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-207">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="8d63c-208">Yeni JSON serileştirmesi</span><span class="sxs-lookup"><span data-stu-id="8d63c-208">New JSON serialization</span></span>

<span data-ttu-id="8d63c-209">ASP.NET Core 3,0 artık <xref:System.Text.Json> JSON serileştirme için varsayılan olarak kullanır:</span><span class="sxs-lookup"><span data-stu-id="8d63c-209">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="8d63c-210">JSON 'yi zaman uyumsuz olarak okur ve yazar.</span><span class="sxs-lookup"><span data-stu-id="8d63c-210">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="8d63c-211">UTF-8 metni için iyileştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-211">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="8d63c-212">Genellikle daha yüksek performans `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-212">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="8d63c-213">ASP.NET Core 3,0 ' ye Json.NET eklemek için bkz. [Newtonsoft. JSON tabanlı JSON biçimi desteği ekleme](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="8d63c-213">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="8d63c-214">Yeni Razor yönergeler</span><span class="sxs-lookup"><span data-stu-id="8d63c-214">New Razor directives</span></span>

<span data-ttu-id="8d63c-215">Aşağıdaki listede yeni yönergeler yer almaktadır Razor :</span><span class="sxs-lookup"><span data-stu-id="8d63c-215">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="8d63c-216">[`@attribute`](xref:mvc/views/razor#attribute): `@attribute` Yönergesi verilen özniteliği oluşturulan sayfanın veya görünümün sınıfına uygular.</span><span class="sxs-lookup"><span data-stu-id="8d63c-216">[`@attribute`](xref:mvc/views/razor#attribute): The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="8d63c-217">Örneğin, `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="8d63c-217">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="8d63c-218">[`@implements`](xref:mvc/views/razor#implements): `@implements` Yönerge, oluşturulan sınıf için bir arabirim uygular.</span><span class="sxs-lookup"><span data-stu-id="8d63c-218">[`@implements`](xref:mvc/views/razor#implements): The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="8d63c-219">Örneğin, `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="8d63c-219">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="8d63c-220">Identityserver4, Web API 'Leri ve maça 'Ları için kimlik doğrulama ve yetkilendirmeyi destekler</span><span class="sxs-lookup"><span data-stu-id="8d63c-220">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="8d63c-221">ASP.NET Core 3,0, Web API yetkilendirmesi desteğini kullanarak tek sayfalı uygulamalarda (Spaon) kimlik doğrulaması sunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-221">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="8d63c-222">IdentityKimlik doğrulama ve depolama için ASP.NET Core, açık kimlik bağlamayı uygulamak Için [ıdentityserver4](https://identityserver.io/) ile birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-222">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="8d63c-223">Identityserver4, ASP.NET Core 3,0 için bir OpenID Connect ve OAuth 2,0 çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-223">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="8d63c-224">Aşağıdaki güvenlik özelliklerini sunar:</span><span class="sxs-lookup"><span data-stu-id="8d63c-224">It enables the following security features:</span></span>

* <span data-ttu-id="8d63c-225">Hizmet olarak kimlik doğrulaması (AaaS)</span><span class="sxs-lookup"><span data-stu-id="8d63c-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="8d63c-226">Birden çok uygulama türü üzerinde çoklu oturum açma/kapatma (SSO)</span><span class="sxs-lookup"><span data-stu-id="8d63c-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="8d63c-227">API 'Ler için erişim denetimi</span><span class="sxs-lookup"><span data-stu-id="8d63c-227">Access control for APIs</span></span>
* <span data-ttu-id="8d63c-228">Federasyon ağ geçidi</span><span class="sxs-lookup"><span data-stu-id="8d63c-228">Federation Gateway</span></span>

<span data-ttu-id="8d63c-229">Daha fazla bilgi için bkz. [ıdentityserver4 belgeleri](http://docs.identityserver.io/en/latest/index.html) veya [kimlik doğrulaması ve kimlik doğrulama ve yetkilendirme](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="8d63c-229">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="8d63c-230">Sertifika ve Kerberos kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="8d63c-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="8d63c-231">Sertifika kimlik doğrulaması şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="8d63c-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="8d63c-232">Sunucu, sertifikaları kabul edecek şekilde yapılandırılıyor.</span><span class="sxs-lookup"><span data-stu-id="8d63c-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="8d63c-233">Kimlik doğrulama ara yazılımı ' ye ekleme `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="8d63c-234">' De sertifika kimlik doğrulama hizmeti ekleniyor `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="8d63c-235">Sertifika kimlik doğrulaması seçenekleri şunları yapabilme:</span><span class="sxs-lookup"><span data-stu-id="8d63c-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="8d63c-236">Otomatik olarak imzalanan sertifikaları kabul edin.</span><span class="sxs-lookup"><span data-stu-id="8d63c-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="8d63c-237">Sertifika iptali olup olmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="8d63c-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="8d63c-238">Profili oluşturulan sertifikanın, içinde doğru kullanım bayrakları olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="8d63c-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="8d63c-239">Varsayılan bir Kullanıcı sorumlusu, sertifika özelliklerinden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8d63c-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="8d63c-240">Kullanıcı sorumlusu, sorumlunun takıma veya değiştirilmesine izin veren bir olay içerir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="8d63c-241">Daha fazla bilgi için bkz. <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="8d63c-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="8d63c-242">[Windows kimlik doğrulaması](/windows-server/security/windows-authentication/windows-authentication-overview) , Linux ve MacOS üzerine genişletildi.</span><span class="sxs-lookup"><span data-stu-id="8d63c-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="8d63c-243">Önceki sürümlerde, Windows kimlik doğrulaması [IIS](xref:host-and-deploy/iis/index) ve [httpsys](xref:fundamentals/servers/httpsys)ile sınırlandırıldı.</span><span class="sxs-lookup"><span data-stu-id="8d63c-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="8d63c-244">ASP.NET Core 3,0 ' de [Kestrel](xref:fundamentals/servers/kestrel) , Windows etki alanına katılmış konaklar için Windows, Linux ve MacOS üzerinde Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)ve [NTLM](/windows-server/security/kerberos/ntlm-overview)kullanma olanağına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="8d63c-245">Bu kimlik doğrulama düzenlerinin Kestrel desteği [Microsoft. AspNetCore. Authentication. Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="8d63c-246">Diğer kimlik doğrulama hizmetlerinde olduğu gibi, kimlik doğrulama uygulaması genelinde yapılandırın ve ardından hizmeti yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="8d63c-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

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

<span data-ttu-id="8d63c-247">Ana bilgisayar gereksinimleri:</span><span class="sxs-lookup"><span data-stu-id="8d63c-247">Host requirements:</span></span>

* <span data-ttu-id="8d63c-248">Windows Konakları, uygulamayı barındıran Kullanıcı hesabına eklenmiş [hizmet sorumlusu adlarına](/windows/win32/ad/service-principal-names) (SPN) sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="8d63c-249">Linux ve macOS makineleri etki alanına katılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="8d63c-250">Web işlemi için SPN oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="8d63c-251">Ana makinede [keytab dosyalarının](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) oluşturulup yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="8d63c-252">Daha fazla bilgi için bkz. <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="8d63c-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="8d63c-253">Şablon değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="8d63c-253">Template changes</span></span>

<span data-ttu-id="8d63c-254">Web UI şablonları ( Razor Sayfalar, denetleyici ve GÖRÜNÜMLERLE Mvc) aşağıdaki gibi kaldırılmıştır:</span><span class="sxs-lookup"><span data-stu-id="8d63c-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="8d63c-255">Tanımlama bilgisi onayı Kullanıcı arabirimi artık dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="8d63c-256">ASP.NET Core 3,0 şablon tarafından oluşturulan bir uygulamada tanımlama bilgisi onay özelliğini etkinleştirmek için, bkz <xref:security/gdpr> ..</span><span class="sxs-lookup"><span data-stu-id="8d63c-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="8d63c-257">Betiklerin ve ilgili statik varlıkların artık CDNs kullanmak yerine yerel dosyalar olarak başvuruluyor.</span><span class="sxs-lookup"><span data-stu-id="8d63c-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="8d63c-258">Daha fazla bilgi için, bkz. [betiklerin ve ilgili statik varlıkların artık geçerli ortama göre CDNs kullanmak yerine yerel dosyalar olarak başvuruluyor (ASPNET/AspNetCore. Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="8d63c-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="8d63c-259">Angular şablonu, angular 8 ' i kullanacak şekilde güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="8d63c-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="8d63c-260">RazorSınıf kitaplığı (RCL) şablonu Razor Varsayılan olarak varsayılan olarak bileşen geliştirme olur.</span><span class="sxs-lookup"><span data-stu-id="8d63c-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="8d63c-261">Visual Studio 'da yeni bir şablon seçeneği, sayfalar ve görünümler için şablon desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="8d63c-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="8d63c-262">Bir komut kabuğunda şablondan RCL oluştururken, `--support-pages-and-views` () seçeneğini geçirin `dotnet new razorclasslib --support-pages-and-views` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-262">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="8d63c-263">Genel Konak</span><span class="sxs-lookup"><span data-stu-id="8d63c-263">Generic Host</span></span>

<span data-ttu-id="8d63c-264">ASP.NET Core 3,0 şablonları kullanılır <xref:fundamentals/host/generic-host> .</span><span class="sxs-lookup"><span data-stu-id="8d63c-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="8d63c-265">Önceki sürümler kullanıldı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="8d63c-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="8d63c-266">.NET Core Genel Host () kullanımı, <xref:Microsoft.Extensions.Hosting.HostBuilder> Web 'e özgü olmayan diğer sunucu senaryolarıyla ASP.NET Core uygulamaları daha iyi tümleştirme sağlar.</span><span class="sxs-lookup"><span data-stu-id="8d63c-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="8d63c-267">Daha fazla bilgi için bkz. [Hostbuilder WebHostBuilder 'ın yerini alır](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="8d63c-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="8d63c-268">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="8d63c-268">Host configuration</span></span>

<span data-ttu-id="8d63c-269">ASP.NET Core 3,0 ' nin yayınlanmasından önce, `ASPNETCORE_` Web konağının ana bilgisayar yapılandırması için, öneki olan ortam değişkenleri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="8d63c-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="8d63c-270">3,0 ' de, `AddEnvironmentVariables` `DOTNET_` ile konak yapılandırması için önekli ortam değişkenlerini yüklemek için kullanılır `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="8d63c-271">Başlangıç Oluşturucu Ekleme değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="8d63c-271">Changes to Startup constructor injection</span></span>

<span data-ttu-id="8d63c-272">Genel konak yalnızca Oluşturucu ekleme için aşağıdaki türleri destekler `Startup` :</span><span class="sxs-lookup"><span data-stu-id="8d63c-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="8d63c-273">Tüm hizmetler yine de yönteme bağımsız değişken olarak eklenebilir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="8d63c-274">Daha fazla bilgi için bkz. [genel ana bilgisayar başlangıç Oluşturucu Ekleme (ASPNET/duyurular #353)](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="8d63c-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="8d63c-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="8d63c-275">Kestrel</span></span>

* <span data-ttu-id="8d63c-276">Kestrel yapılandırması, genel konağa geçiş için güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="8d63c-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="8d63c-277">3,0 ' de, Kestrel tarafından sağlanmış Web ana bilgisayar Oluşturucu üzerinde yapılandırılır `ConfigureWebHostDefaults` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="8d63c-278">Bağlantı bağdaştırıcıları, Kestrel adresinden kaldırılmıştır ve bağlantı ara yazılımı ile değiştirilmiştir ve bu, ASP.NET Core işlem hattındaki HTTP ara hattına benzer ancak alt düzey bağlantılar için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="8d63c-279">Kestrel aktarım katmanı, içinde ortak bir arabirim olarak kullanıma sunuldu `Connections.Abstractions` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="8d63c-280">Sondaki üstbilgiler yeni bir koleksiyona taşınarak üstbilgiler ve tanıtımları arasındaki belirsizlik çözüldü.</span><span class="sxs-lookup"><span data-stu-id="8d63c-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="8d63c-281">Gibi zaman uyumlu g/ç API 'Leri `HttpRequest.Body.Read` , uygulama kilitlenmelerine neden olan yaygın bir iş parçacığı kaynağıdır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-281">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="8d63c-282">3,0 ' de `AllowSynchronousIO` Varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="8d63c-283">Daha fazla bilgi için bkz. <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="8d63c-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="8d63c-284">Varsayılan olarak etkin HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8d63c-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="8d63c-285">HTTP/2, HTTPS uç noktaları için Kestrel içinde varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="8d63c-286">IIS veya HTTP. sys için HTTP/2 desteği, işletim sistemi tarafından desteklendikleri zaman etkindir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="8d63c-287">İstek üzerine EventCounters</span><span class="sxs-lookup"><span data-stu-id="8d63c-287">EventCounters on request</span></span>

<span data-ttu-id="8d63c-288">EventSource barındırma, `Microsoft.AspNetCore.Hosting` <xref:System.Diagnostics.Tracing.EventCounter> gelen isteklerle ilgili aşağıdaki yeni türleri yayar:</span><span class="sxs-lookup"><span data-stu-id="8d63c-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="8d63c-289">Uç nokta yönlendirme</span><span class="sxs-lookup"><span data-stu-id="8d63c-289">Endpoint routing</span></span>

<span data-ttu-id="8d63c-290">Çerçeveler 'in (örneğin, MVC) ara yazılım ile iyi çalışmasına izin veren uç nokta yönlendirme geliştirildi:</span><span class="sxs-lookup"><span data-stu-id="8d63c-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="8d63c-291">Ara yazılım ve uç noktaların sırası istek işleme ardışık düzeninde yapılandırılabilir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="8d63c-292">Uç noktalar ve ara yazılımlar, sistem durumu denetimleri gibi diğer ASP.NET Core tabanlı teknolojilerle birlikte.</span><span class="sxs-lookup"><span data-stu-id="8d63c-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="8d63c-293">Uç noktalar, hem yazılım hem de MVC 'de CORS veya yetkilendirme gibi bir ilke uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="8d63c-294">Filtreler ve öznitelikler, denetleyicilerde yöntemler üzerine yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="8d63c-295">Daha fazla bilgi için bkz. <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="8d63c-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="8d63c-296">Sistem durumu denetimleri</span><span class="sxs-lookup"><span data-stu-id="8d63c-296">Health Checks</span></span>

<span data-ttu-id="8d63c-297">Sistem durumu denetimleri, genel ana bilgisayar ile Endpoint Routing kullanır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="8d63c-298">' De, uç nokta `Startup.Configure` `MapHealthChecks` URL 'si veya göreli yol ile Endpoint Builder ' ı çağırın:</span><span class="sxs-lookup"><span data-stu-id="8d63c-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="8d63c-299">Durum denetimleri uç noktaları şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="8d63c-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="8d63c-300">İzin verilen bir veya daha fazla Konakları/bağlantı noktasını belirtin.</span><span class="sxs-lookup"><span data-stu-id="8d63c-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="8d63c-301">Yetkilendirme gerektir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-301">Require authorization.</span></span>
* <span data-ttu-id="8d63c-302">CORS gerektir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-302">Require CORS.</span></span>

<span data-ttu-id="8d63c-303">Daha fazla bilgi için aşağıdaki makaleleri inceleyin:</span><span class="sxs-lookup"><span data-stu-id="8d63c-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="8d63c-304">HttpContext üzerindeki kanallar</span><span class="sxs-lookup"><span data-stu-id="8d63c-304">Pipes on HttpContext</span></span>

<span data-ttu-id="8d63c-305">Artık istek gövdesini okumak ve yanıt gövdesini API kullanarak yazmak mümkündür <xref:System.IO.Pipelines> .</span><span class="sxs-lookup"><span data-stu-id="8d63c-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="8d63c-306">Bu</span><span class="sxs-lookup"><span data-stu-id="8d63c-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="8d63c-307">`HttpRequest.BodyReader`özelliği <xref:System.IO.Pipelines.PipeReader> , istek gövdesini okumak için kullanılabilecek bir sağlar.</span><span class="sxs-lookup"><span data-stu-id="8d63c-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="8d63c-308">Bu</span><span class="sxs-lookup"><span data-stu-id="8d63c-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="8d63c-309">`HttpResponse.BodyWriter`özelliği <xref:System.IO.Pipelines.PipeWriter> , yanıt gövdesini yazmak için kullanılabilecek bir sağlar.</span><span class="sxs-lookup"><span data-stu-id="8d63c-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="8d63c-310">`HttpRequest.BodyReader`akışın analog bir `HttpRequest.Body` kadır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="8d63c-311">`HttpResponse.BodyWriter`akışın analog bir `HttpResponse.Body` kadır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="8d63c-312">IIS 'de geliştirilmiş hata raporlama</span><span class="sxs-lookup"><span data-stu-id="8d63c-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="8d63c-313">IIS 'de ASP.NET Core uygulamalar barındırırken başlatma hataları artık daha zengin Tanılama verileri oluşturuyor.</span><span class="sxs-lookup"><span data-stu-id="8d63c-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="8d63c-314">Bu hatalar, geçerli her yerde yığın izlemelerle Windows olay günlüğü 'ne bildirilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="8d63c-315">Ayrıca, tüm uyarılar, hatalar ve işlenmemiş özel durumlar Windows olay günlüğü 'ne kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="8d63c-316">Çalışan hizmeti ve çalışan SDK 'Sı</span><span class="sxs-lookup"><span data-stu-id="8d63c-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="8d63c-317">.NET Core 3,0 yeni çalışan hizmeti uygulama şablonunu tanıtır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="8d63c-318">Bu şablon, .NET Core 'da uzun süre çalışan hizmetler yazmak için bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="8d63c-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="8d63c-319">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="8d63c-319">For more information, see:</span></span>

* [<span data-ttu-id="8d63c-320">Windows Hizmetleri olarak .NET Core çalışanları</span><span class="sxs-lookup"><span data-stu-id="8d63c-320">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="8d63c-321">İletilen üstbilgiler ara yazılımı geliştirmeleri</span><span class="sxs-lookup"><span data-stu-id="8d63c-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="8d63c-322">Önceki ASP.NET Core sürümlerinde, <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> bir Azure Linux 'a DAĞıTıLıRKEN veya IIS dışında herhangi bir ters proxy 'nin arkasında sorun.</span><span class="sxs-lookup"><span data-stu-id="8d63c-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="8d63c-323">Önceki sürümlere yönelik düzeltmeler, [Linux ve IIS olmayan ters proxy 'lerin düzenini iletme](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)bölümünde belgelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="8d63c-324">Bu senaryo ASP.NET Core 3,0 ' de düzeltilmiştir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="8d63c-325">Ortam değişkeni olarak ayarlandığında, ana bilgisayar [Iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) sağlar `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` .</span><span class="sxs-lookup"><span data-stu-id="8d63c-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="8d63c-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`, `true` kapsayıcı görüntülerimizde olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="8d63c-327">Performans iyileştirmeleri</span><span class="sxs-lookup"><span data-stu-id="8d63c-327">Performance improvements</span></span>

<span data-ttu-id="8d63c-328">ASP.NET Core 3,0, bellek kullanımını azaltan ve üretilen işi geliştiren birçok geliştirme içerir:</span><span class="sxs-lookup"><span data-stu-id="8d63c-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="8d63c-329">Kapsamlı hizmetler için yerleşik bağımlılık ekleme kapsayıcısını kullanırken bellek kullanımında azaltma.</span><span class="sxs-lookup"><span data-stu-id="8d63c-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="8d63c-330">Ara yazılım senaryoları ve yönlendirme dahil olmak üzere çerçeve genelinde ayırmalarda azaltma.</span><span class="sxs-lookup"><span data-stu-id="8d63c-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="8d63c-331">WebSocket bağlantıları için bellek kullanımında azaltma.</span><span class="sxs-lookup"><span data-stu-id="8d63c-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="8d63c-332">HTTPS bağlantıları için bellek azaltma ve verimlilik geliştirmeleri.</span><span class="sxs-lookup"><span data-stu-id="8d63c-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="8d63c-333">Yeni iyileştirilmiş ve tamamen zaman uyumsuz JSON serileştiricisi.</span><span class="sxs-lookup"><span data-stu-id="8d63c-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="8d63c-334">Form ayrıştırılırken bellek kullanımı ve üretilen iş iyileştirmeleri azaltılması.</span><span class="sxs-lookup"><span data-stu-id="8d63c-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="8d63c-335">ASP.NET Core 3,0 yalnızca .NET Core 3,0 üzerinde çalışır</span><span class="sxs-lookup"><span data-stu-id="8d63c-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="8d63c-336">ASP.NET Core 3,0 itibariyle, .NET Framework artık desteklenen bir hedef çerçeve değildir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="8d63c-337">.NET Framework hedefleyen projeler [.NET Core 2,1 LTS sürümünü](https://dotnet.microsoft.com/download/dotnet-core/2.1)kullanarak tam olarak desteklenen bir biçimde devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="8d63c-338">ASP.NET Core 2.1. x ile ilgili paketlerin çoğu, .NET Core 2,1 için üç yıllık LTS döneminin ötesinde süresiz olarak desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="8d63c-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="8d63c-339">Geçiş bilgileri için bkz. [kodunuzu .NET Core 'a .NET Framework](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="8d63c-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="8d63c-340">ASP.NET Core paylaşılan çerçevesini kullanma</span><span class="sxs-lookup"><span data-stu-id="8d63c-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="8d63c-341">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunan ASP.NET Core 3,0 paylaşılan çerçevesi artık `<PackageReference />` Proje dosyasında açık bir öğe gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="8d63c-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="8d63c-342">Proje dosyasında SDK kullanılırken paylaşılan çerçeveye otomatik olarak başvurulur `Microsoft.NET.Sdk.Web` :</span><span class="sxs-lookup"><span data-stu-id="8d63c-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="8d63c-343">ASP.NET Core paylaşılan çerçevesinden kaldırılan derlemeler</span><span class="sxs-lookup"><span data-stu-id="8d63c-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="8d63c-344">ASP.NET Core 3,0 paylaşılan çerçevesinden çıkarılan en önemli derlemeler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="8d63c-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="8d63c-345">[Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.net).</span><span class="sxs-lookup"><span data-stu-id="8d63c-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="8d63c-346">ASP.NET Core 3,0 ' ye Json.NET eklemek için bkz. [Newtonsoft. JSON tabanlı JSON biçimi desteği ekleme](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="8d63c-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="8d63c-347">ASP.NET Core 3,0 `System.Text.Json` , JSON okuma ve yazma için tanıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="8d63c-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="8d63c-348">Daha fazla bilgi için bu belgede [yenı JSON serileştirmesi](#new-json-serialization) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8d63c-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="8d63c-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8d63c-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="8d63c-350">Paylaşılan çerçeveden kaldırılan derlemelerin tamamen listesi için bkz [. Microsoft. AspNetCore. App 3,0 ' den kaldırılan derlemeler](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="8d63c-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="8d63c-351">Bu değişiklik için mosyon hakkında daha fazla bilgi için bkz. [3,0 'de Microsoft. AspNetCore. app 'e yönelik son değişiklikler](https://github.com/aspnet/Announcements/issues/325) ve [ASP.NET Core 3,0 ' de gelen değişikliklere ilk bakış](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="8d63c-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 