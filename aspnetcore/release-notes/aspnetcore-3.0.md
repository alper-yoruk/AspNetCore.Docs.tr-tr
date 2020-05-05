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
ms.openlocfilehash: aa0b2b51632cff729449018f9823c38cc26ce612
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774099"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="12e01-103">ASP.NET Core 3,0 ' deki yenilikler</span><span class="sxs-lookup"><span data-stu-id="12e01-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="12e01-104">Bu makalede, ASP.NET Core 3,0 ' deki en önemli değişiklikler ilgili belgelerin bağlantılarıyla vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="12e01-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

Blazor<span data-ttu-id="12e01-105">, .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmak için ASP.NET Core yeni bir çerçevedir:</span><span class="sxs-lookup"><span data-stu-id="12e01-105"> is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="12e01-106">JavaScript yerine C# kullanarak zengin etkileşimli uo 'lar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="12e01-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="12e01-107">.NET ' te yazılmış sunucu tarafı ve istemci tarafı uygulama mantığını paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="12e01-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="12e01-108">Mobil tarayıcılar dahil olmak üzere geniş tarayıcı desteği için Kullanıcı arabirimini HTML ve CSS olarak işleme.</span><span class="sxs-lookup"><span data-stu-id="12e01-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

Blazor<span data-ttu-id="12e01-109">çerçeve tarafından desteklenen senaryolar:</span><span class="sxs-lookup"><span data-stu-id="12e01-109"> framework supported scenarios:</span></span>

* <span data-ttu-id="12e01-110">Yeniden kullanılabilir kullanıcı arabirimiRazor bileşenleri (bileşenler)</span><span class="sxs-lookup"><span data-stu-id="12e01-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="12e01-111">İstemci tarafı yönlendirme</span><span class="sxs-lookup"><span data-stu-id="12e01-111">Client-side routing</span></span>
* <span data-ttu-id="12e01-112">Bileşen düzenleri</span><span class="sxs-lookup"><span data-stu-id="12e01-112">Component layouts</span></span>
* <span data-ttu-id="12e01-113">Bağımlılık ekleme desteği</span><span class="sxs-lookup"><span data-stu-id="12e01-113">Support for dependency injection</span></span>
* <span data-ttu-id="12e01-114">Formlar ve doğrulama</span><span class="sxs-lookup"><span data-stu-id="12e01-114">Forms and validation</span></span>
* <span data-ttu-id="12e01-115">Razor Sınıf kitaplıklarıyla bileşen kitaplıkları derleme</span><span class="sxs-lookup"><span data-stu-id="12e01-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="12e01-116">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="12e01-116">JavaScript interop</span></span>

<span data-ttu-id="12e01-117">Daha fazla bilgi için bkz. <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="12e01-117">For more information, see <xref:blazor/index>.</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="12e01-118">Server</span><span class="sxs-lookup"><span data-stu-id="12e01-118"> Server</span></span>

Blazor<span data-ttu-id="12e01-119">Kullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır.</span><span class="sxs-lookup"><span data-stu-id="12e01-119"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="12e01-120">Sunucu, bir ASP.NET Core uygulamasındaki Razor sunucuda bileşenleri barındırmak için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="12e01-120"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="12e01-121">Kullanıcı Arabirimi güncelleştirmeleri bir SignalR bağlantı üzerinden işlenir.</span><span class="sxs-lookup"><span data-stu-id="12e01-121">UI updates are handled over a SignalR connection.</span></span> Blazor<span data-ttu-id="12e01-122">Sunucu ASP.NET Core 3,0 ' de desteklenir.</span><span class="sxs-lookup"><span data-stu-id="12e01-122"> Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="blazor-webassembly-preview"></a>Blazor<span data-ttu-id="12e01-123">WebAssembly (Önizleme)</span><span class="sxs-lookup"><span data-stu-id="12e01-123"> WebAssembly (Preview)</span></span>

Blazor<span data-ttu-id="12e01-124">uygulamalar, bir WebAssembly tabanlı .NET çalışma zamanı kullanarak doğrudan tarayıcıda da çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-124"> apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> Blazor<span data-ttu-id="12e01-125">WebAssembly Önizleme *aşamasındadır ve ASP.NET Core 3,0 ' de* desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="12e01-125"> WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> Blazor<span data-ttu-id="12e01-126">WebAssembly ASP.NET Core gelecek bir sürümünde desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="12e01-126"> WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a>Razor<span data-ttu-id="12e01-127">bileşenleri</span><span class="sxs-lookup"><span data-stu-id="12e01-127"> components</span></span>

Blazor<span data-ttu-id="12e01-128">uygulamalar bileşenlerden oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="12e01-128"> apps are built from components.</span></span> <span data-ttu-id="12e01-129">Bileşenler, bir sayfa, iletişim kutusu veya form gibi kullanıcı arabirimi (UI) için kendi içinde yer alan öbeklerdir.</span><span class="sxs-lookup"><span data-stu-id="12e01-129">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="12e01-130">Bileşenler, Kullanıcı arabirimi işleme mantığını ve istemci tarafı olay işleyicilerini tanımlayan normal .NET sınıflarıdır.</span><span class="sxs-lookup"><span data-stu-id="12e01-130">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="12e01-131">JavaScript olmadan zengin etkileşimli Web uygulamaları oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="12e01-131">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="12e01-132">İçindeki Blazor BILEŞENLER genellikle HTML ve C# Razor ' nin doğal bir karışımı olan sözdizimi kullanılarak yazılır.</span><span class="sxs-lookup"><span data-stu-id="12e01-132">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> Razor<span data-ttu-id="12e01-133">bileşenler, her ikisi Razor de kullandıkları Razorsayfalara ve MVC görünümlerine benzerdir.</span><span class="sxs-lookup"><span data-stu-id="12e01-133"> components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="12e01-134">Bir istek-yanıt modelini temel alan sayfaların ve görünümlerin aksine, bileşenler Kullanıcı arabirimi oluşturmayı işlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="12e01-134">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="12e01-135">gRPC</span><span class="sxs-lookup"><span data-stu-id="12e01-135">gRPC</span></span>

<span data-ttu-id="12e01-136">[GRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="12e01-136">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="12e01-137">Popüler, yüksek performanslı bir RPC (uzak yordam çağrısı) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="12e01-137">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="12e01-138">, API geliştirmeye yönelik olarak yapılan bir sözleşmenin ilk yaklaşımını sağlar.</span><span class="sxs-lookup"><span data-stu-id="12e01-138">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="12e01-139">, Gibi modern teknolojiler kullanır:</span><span class="sxs-lookup"><span data-stu-id="12e01-139">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="12e01-140">Taşıma için HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="12e01-140">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="12e01-141">Arabirim açıklaması dili olarak protokol arabellekleri.</span><span class="sxs-lookup"><span data-stu-id="12e01-141">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="12e01-142">İkili serileştirme biçimi.</span><span class="sxs-lookup"><span data-stu-id="12e01-142">Binary serialization format.</span></span>
* <span data-ttu-id="12e01-143">Şöyle özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="12e01-143">Provides features such as:</span></span>

  * <span data-ttu-id="12e01-144">Kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="12e01-144">Authentication</span></span>
  * <span data-ttu-id="12e01-145">Çift yönlü akış ve akış denetimi.</span><span class="sxs-lookup"><span data-stu-id="12e01-145">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="12e01-146">İptal ve zaman aşımları.</span><span class="sxs-lookup"><span data-stu-id="12e01-146">Cancellation and timeouts.</span></span>

<span data-ttu-id="12e01-147">ASP.NET Core 3,0 ' deki gRPC işlevselliği şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="12e01-147">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="12e01-148">[GRPC. aspnetcore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; , GRPC hizmetlerini barındırmak için bir ASP.NET Core çerçevesi.</span><span class="sxs-lookup"><span data-stu-id="12e01-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="12e01-149">gRPC on ASP.NET Core, günlüğe kaydetme, bağımlılık ekleme (dı), kimlik doğrulama ve yetkilendirme gibi standart ASP.NET Core özelliklerle tümleştirilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-149">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="12e01-150">[GRPC .net. istemci](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; .NET Core için bir GRPC istemcisini tanıdık `HttpClient`bir şekilde oluşturur.</span><span class="sxs-lookup"><span data-stu-id="12e01-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="12e01-151">[GRPC .net. clientfactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; GRPC istemci tümleştirmesi ile `HttpClientFactory`tümleştirme.</span><span class="sxs-lookup"><span data-stu-id="12e01-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="12e01-152">Daha fazla bilgi için bkz. <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="12e01-152">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="12e01-153">Bkz. geçiş yönergeleri için [kodu güncelleştirme SignalR ](xref:migration/22-to-30#signalr) .</span><span class="sxs-lookup"><span data-stu-id="12e01-153">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> SignalR<span data-ttu-id="12e01-154">Artık JSON `System.Text.Json` iletilerini seri hale getirmek/seri durumdan çıkarmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="12e01-154"> now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="12e01-155">Tabanlı serileştiriciyi geri yükleme `Newtonsoft.Json`yönergeleri için bkz. [Newtonsoft. JSON öğesine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson) .</span><span class="sxs-lookup"><span data-stu-id="12e01-155">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="12e01-156">İçin SignalRJavaScript ve .net istemcilerinde, otomatik yeniden bağlanma için destek eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="12e01-156">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="12e01-157">Varsayılan olarak, istemci hemen yeniden bağlanmaya çalışır ve gerekirse 2, 10 ve 30 saniye sonra yeniden dener.</span><span class="sxs-lookup"><span data-stu-id="12e01-157">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="12e01-158">İstemci başarıyla yeniden bağlanırsa, yeni bir bağlantı KIMLIĞI alır.</span><span class="sxs-lookup"><span data-stu-id="12e01-158">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="12e01-159">Otomatik yeniden bağlanma kabul etme:</span><span class="sxs-lookup"><span data-stu-id="12e01-159">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="12e01-160">Yeniden bağlantı aralıkları bir dizi milisaniyelik tabanlı süre geçirerek belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="12e01-160">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="12e01-161">Yeniden bağlantı aralıklarının tam denetimi için özel bir uygulama geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-161">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="12e01-162">Son yeniden bağlanma aralığından sonra yeniden bağlantı başarısız olursa:</span><span class="sxs-lookup"><span data-stu-id="12e01-162">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="12e01-163">İstemci, bağlantının çevrimdışı olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="12e01-163">The client considers the connection is offline.</span></span>
* <span data-ttu-id="12e01-164">İstemci yeniden bağlanmayı denemeyi durduruyor.</span><span class="sxs-lookup"><span data-stu-id="12e01-164">The client stops trying to reconnect.</span></span>

<span data-ttu-id="12e01-165">Yeniden bağlanma denemeleri sırasında, kullanıcıya yeniden bağlantı denenmekte olduğunu bildirmek için uygulama kullanıcı arabirimini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="12e01-165">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="12e01-166">Bağlantı kesildiğinde UI geri bildirimi sağlamak için, SignalR istemci API 'si aşağıdaki olay işleyicilerini içerecek şekilde genişletilir:</span><span class="sxs-lookup"><span data-stu-id="12e01-166">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="12e01-167">`onreconnecting`: Geliştiricilere Kullanıcı arabirimini devre dışı bırakma veya uygulamanın çevrimdışı olduğunu bilmesini sağlayan bir fırsat sağlar.</span><span class="sxs-lookup"><span data-stu-id="12e01-167">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="12e01-168">`onreconnected`: Bağlantı kurulduktan sonra geliştiricilere Kullanıcı arabirimini güncelleştirme fırsatı verir.</span><span class="sxs-lookup"><span data-stu-id="12e01-168">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="12e01-169">Aşağıdaki kod, bağlanmaya `onreconnecting` çalışırken kullanıcı arabirimini güncelleştirmek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="12e01-169">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="12e01-170">Aşağıdaki kod, bağlantıda `onreconnected` Kullanıcı arabirimini güncelleştirmek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="12e01-170">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR<span data-ttu-id="12e01-171">3,0 ve üzeri, bir hub yöntemi yetkilendirme gerektirdiğinde, yetkilendirme işleyicilerine özel bir kaynak sağlar.</span><span class="sxs-lookup"><span data-stu-id="12e01-171"> 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="12e01-172">Kaynak bir örneğidir `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="12e01-172">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="12e01-173">`HubInvocationContext` Şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="12e01-173">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="12e01-174">Çağrılan hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="12e01-174">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="12e01-175">Hub yöntemi için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="12e01-175">Arguments to the hub method.</span></span>

<span data-ttu-id="12e01-176">Azure Active Directory aracılığıyla birden çok kuruluşun oturum açmasına izin veren bir sohbet odası uygulamasının aşağıdaki örneğini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="12e01-176">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="12e01-177">Microsoft hesabı herkes sohbet için oturum açabilir, ancak yalnızca sahip olunan kuruluşun üyeleri kullanıcıları veya kullanıcıların sohbet geçmişlerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-177">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="12e01-178">Uygulama belirli kullanıcılardan belirli işlevleri kısıtlayabilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-178">The app could restrict certain functionality from specific users.</span></span>

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

<span data-ttu-id="12e01-179">Yukarıdaki kodda özel `DomainRestrictedRequirement` `IAuthorizationRequirement`işlevi görür.</span><span class="sxs-lookup"><span data-stu-id="12e01-179">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="12e01-180">`HubInvocationContext` Kaynak parametresi geçirildiğinden iç mantık şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="12e01-180">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="12e01-181">Hub 'ın çağrıldığı bağlamı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="12e01-181">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="12e01-182">Kullanıcının bireysel hub yöntemlerini yürütmesine izin verirken kararlar alın.</span><span class="sxs-lookup"><span data-stu-id="12e01-182">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="12e01-183">Tek tek hub yöntemleri, çalışma zamanında kodun denetlediği ilkenin adıyla işaretlenebilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-183">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="12e01-184">İstemciler tek tek hub yöntemlerini çağırmayı denediğinden, `DomainRestrictedRequirement` işleyici çalışır ve yöntemlere erişimi denetler.</span><span class="sxs-lookup"><span data-stu-id="12e01-184">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="12e01-185">`DomainRestrictedRequirement` Denetimlerin erişim yöntemine göre:</span><span class="sxs-lookup"><span data-stu-id="12e01-185">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="12e01-186">Tüm oturum açmış kullanıcılar `SendMessage` yöntemini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-186">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="12e01-187">Yalnızca bir `@jabbr.net` e-posta adresiyle oturum açan kullanıcılar, kullanıcıların geçmişlerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-187">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="12e01-188">Yalnızca `bob42@jabbr.net` sohbet odasından kullanıcıları Ban yapabilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-188">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

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

<span data-ttu-id="12e01-189">`DomainRestricted` İlkeyi oluşturmak şunları içerebilir:</span><span class="sxs-lookup"><span data-stu-id="12e01-189">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="12e01-190">*Startup.cs*' de, yeni ilkeyi ekleme.</span><span class="sxs-lookup"><span data-stu-id="12e01-190">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="12e01-191">Özel `DomainRestrictedRequirement` gereksinimi parametre olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="12e01-191">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="12e01-192">Yetkilendirme `DomainRestricted` ara yazılımı ile kaydetme.</span><span class="sxs-lookup"><span data-stu-id="12e01-192">Registering `DomainRestricted` with the authorization middleware.</span></span>

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

SignalR<span data-ttu-id="12e01-193">Hub [uç nokta yönlendirme](xref:fundamentals/routing)kullanır.</span><span class="sxs-lookup"><span data-stu-id="12e01-193"> hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> SignalR<span data-ttu-id="12e01-194">Hub bağlantısı daha önce açık olarak yapıldı:</span><span class="sxs-lookup"><span data-stu-id="12e01-194"> hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="12e01-195">Önceki sürümde, geliştiriciler, Razor sayfalar ve hub 'ları çeşitli yerlerde bağlamak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="12e01-195">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="12e01-196">Açık bağlantı, neredeyse aynı bir dizi yönlendirme segmentine neden olur:</span><span class="sxs-lookup"><span data-stu-id="12e01-196">Explicit connection results in a series of nearly-identical routing segments:</span></span>

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

SignalR<span data-ttu-id="12e01-197">3,0 hub 'ları, uç nokta yönlendirme aracılığıyla yönlendirilebilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-197"> 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="12e01-198">Uç nokta yönlendirme ile, genellikle tüm yönlendirme ' de `UseRouting`yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="12e01-198">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="12e01-199">ASP.NET Core 3,0 SignalR eklendi:</span><span class="sxs-lookup"><span data-stu-id="12e01-199">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="12e01-200">İstemciden sunucuya akış.</span><span class="sxs-lookup"><span data-stu-id="12e01-200">Client-to-server streaming.</span></span> <span data-ttu-id="12e01-201">İstemciden sunucuya akışla, sunucu tarafı yöntemleri `IAsyncEnumerable<T>` veya `ChannelReader<T>`örneklerinden birini alabilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-201">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="12e01-202">Aşağıdaki C# örneğinde, hub 'daki `UploadStream` Yöntem istemciden dizelerin akışını alacaktır:</span><span class="sxs-lookup"><span data-stu-id="12e01-202">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="12e01-203">`IAsyncEnumerable<T>` .NET istemci uygulamaları, yukarıdaki `ChannelReader<T>` `stream` `UploadStream` hub yönteminin bağımsız değişkeni olarak bir ya da örneği geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-203">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="12e01-204">`for` Döngü tamamlandıktan ve yerel işlev çıktıktan sonra, akış tamamlama gönderilir:</span><span class="sxs-lookup"><span data-stu-id="12e01-204">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

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

<span data-ttu-id="12e01-205">JavaScript istemci uygulamaları, yukarıdaki SignalR `Subject` `stream` `UploadStream` hub yönteminin bağımsız değişkeni olarak (veya bir [rxjs konusu](https://rxjs.dev/api/index/class/Subject)) kullanır.</span><span class="sxs-lookup"><span data-stu-id="12e01-205">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="12e01-206">JavaScript kodu, yakalandıkları ve `subject.next` sunucuya gönderilmeye hazırlanıyor gibi dizeleri işlemek için yöntemini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-206">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="12e01-207">Önceki iki kod parçacığı gibi kodu kullanarak gerçek zamanlı akış deneyimleri oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-207">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="12e01-208">Yeni JSON serileştirmesi</span><span class="sxs-lookup"><span data-stu-id="12e01-208">New JSON serialization</span></span>

<span data-ttu-id="12e01-209">ASP.NET Core 3,0 artık JSON <xref:System.Text.Json> serileştirme için varsayılan olarak kullanır:</span><span class="sxs-lookup"><span data-stu-id="12e01-209">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="12e01-210">JSON 'yi zaman uyumsuz olarak okur ve yazar.</span><span class="sxs-lookup"><span data-stu-id="12e01-210">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="12e01-211">UTF-8 metni için iyileştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="12e01-211">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="12e01-212">Genellikle daha yüksek performans `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="12e01-212">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="12e01-213">ASP.NET Core 3,0 ' ye Json.NET eklemek için bkz. [Newtonsoft. JSON tabanlı JSON biçimi desteği ekleme](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="12e01-213">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="12e01-214">Yeni Razor yönergeler</span><span class="sxs-lookup"><span data-stu-id="12e01-214">New Razor directives</span></span>

<span data-ttu-id="12e01-215">Aşağıdaki listede yeni Razor yönergeler yer almaktadır:</span><span class="sxs-lookup"><span data-stu-id="12e01-215">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="12e01-216">[`@attribute`](xref:mvc/views/razor#attribute)&ndash; Yönergesi verilen özniteliği oluşturulan sayfanın veya görünümün sınıfına `@attribute` uygular.</span><span class="sxs-lookup"><span data-stu-id="12e01-216">[`@attribute`](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="12e01-217">Örneğin, `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="12e01-217">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="12e01-218">[`@implements`](xref:mvc/views/razor#implements)&ndash; Yönergesi, oluşturulan sınıf için bir arabirim `@implements` uygular.</span><span class="sxs-lookup"><span data-stu-id="12e01-218">[`@implements`](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="12e01-219">Örneğin, `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="12e01-219">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="12e01-220">Identityserver4, Web API 'Leri ve maça 'Ları için kimlik doğrulama ve yetkilendirmeyi destekler</span><span class="sxs-lookup"><span data-stu-id="12e01-220">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="12e01-221">ASP.NET Core 3,0, Web API yetkilendirmesi desteğini kullanarak tek sayfalı uygulamalarda (Spaon) kimlik doğrulaması sunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="12e01-221">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="12e01-222">Kimlik Identity doğrulama ve depolama için ASP.NET Core, açık kimlik bağlamayı uygulamak için [ıdentityserver4](https://identityserver.io/) ile birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-222">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="12e01-223">Identityserver4, ASP.NET Core 3,0 için bir OpenID Connect ve OAuth 2,0 çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="12e01-223">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="12e01-224">Aşağıdaki güvenlik özelliklerini sunar:</span><span class="sxs-lookup"><span data-stu-id="12e01-224">It enables the following security features:</span></span>

* <span data-ttu-id="12e01-225">Hizmet olarak kimlik doğrulaması (AaaS)</span><span class="sxs-lookup"><span data-stu-id="12e01-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="12e01-226">Birden çok uygulama türü üzerinde çoklu oturum açma/kapatma (SSO)</span><span class="sxs-lookup"><span data-stu-id="12e01-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="12e01-227">API 'Ler için erişim denetimi</span><span class="sxs-lookup"><span data-stu-id="12e01-227">Access control for APIs</span></span>
* <span data-ttu-id="12e01-228">Federasyon ağ geçidi</span><span class="sxs-lookup"><span data-stu-id="12e01-228">Federation Gateway</span></span>

<span data-ttu-id="12e01-229">Daha fazla bilgi için bkz. [ıdentityserver4 belgeleri](http://docs.identityserver.io/en/latest/index.html) veya [kimlik doğrulaması ve kimlik doğrulama ve yetkilendirme](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="12e01-229">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="12e01-230">Sertifika ve Kerberos kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="12e01-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="12e01-231">Sertifika kimlik doğrulaması şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="12e01-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="12e01-232">Sunucu, sertifikaları kabul edecek şekilde yapılandırılıyor.</span><span class="sxs-lookup"><span data-stu-id="12e01-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="12e01-233">Kimlik doğrulama ara yazılımı ' `Startup.Configure`ye ekleme.</span><span class="sxs-lookup"><span data-stu-id="12e01-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="12e01-234">' De `Startup.ConfigureServices`sertifika kimlik doğrulama hizmeti ekleniyor.</span><span class="sxs-lookup"><span data-stu-id="12e01-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="12e01-235">Sertifika kimlik doğrulaması seçenekleri şunları yapabilme:</span><span class="sxs-lookup"><span data-stu-id="12e01-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="12e01-236">Otomatik olarak imzalanan sertifikaları kabul edin.</span><span class="sxs-lookup"><span data-stu-id="12e01-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="12e01-237">Sertifika iptali olup olmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="12e01-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="12e01-238">Profili oluşturulan sertifikanın, içinde doğru kullanım bayrakları olduğunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="12e01-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="12e01-239">Varsayılan bir Kullanıcı sorumlusu, sertifika özelliklerinden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="12e01-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="12e01-240">Kullanıcı sorumlusu, sorumlunun takıma veya değiştirilmesine izin veren bir olay içerir.</span><span class="sxs-lookup"><span data-stu-id="12e01-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="12e01-241">Daha fazla bilgi için bkz. <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="12e01-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="12e01-242">[Windows kimlik doğrulaması](/windows-server/security/windows-authentication/windows-authentication-overview) , Linux ve MacOS üzerine genişletildi.</span><span class="sxs-lookup"><span data-stu-id="12e01-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="12e01-243">Önceki sürümlerde, Windows kimlik doğrulaması [IIS](xref:host-and-deploy/iis/index) ve [httpsys](xref:fundamentals/servers/httpsys)ile sınırlandırıldı.</span><span class="sxs-lookup"><span data-stu-id="12e01-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="12e01-244">ASP.NET Core 3,0 ' de [Kestrel](xref:fundamentals/servers/kestrel) , Windows etki alanına katılmış konaklar için Windows, Linux ve MacOS üzerinde Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)ve [NTLM](/windows-server/security/kerberos/ntlm-overview)kullanma olanağına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="12e01-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="12e01-245">Bu kimlik doğrulama düzenlerinin Kestrel desteği [Microsoft. AspNetCore. Authentication. Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="12e01-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="12e01-246">Diğer kimlik doğrulama hizmetlerinde olduğu gibi, kimlik doğrulama uygulaması genelinde yapılandırın ve ardından hizmeti yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="12e01-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

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

<span data-ttu-id="12e01-247">Ana bilgisayar gereksinimleri:</span><span class="sxs-lookup"><span data-stu-id="12e01-247">Host requirements:</span></span>

* <span data-ttu-id="12e01-248">Windows Konakları, uygulamayı barındıran Kullanıcı hesabına eklenmiş [hizmet sorumlusu adlarına](/windows/win32/ad/service-principal-names) (SPN) sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="12e01-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="12e01-249">Linux ve macOS makineleri etki alanına katılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="12e01-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="12e01-250">Web işlemi için SPN oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="12e01-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="12e01-251">Ana makinede [keytab dosyalarının](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) oluşturulup yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="12e01-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="12e01-252">Daha fazla bilgi için bkz. <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="12e01-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="12e01-253">Şablon değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="12e01-253">Template changes</span></span>

<span data-ttu-id="12e01-254">Web UI şablonları (Razor sayfalar, denetleyici ve görünümlerle Mvc) aşağıdaki gibi kaldırılmıştır:</span><span class="sxs-lookup"><span data-stu-id="12e01-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="12e01-255">Tanımlama bilgisi onayı Kullanıcı arabirimi artık dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="12e01-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="12e01-256">ASP.NET Core 3,0 şablon tarafından oluşturulan bir uygulamada tanımlama bilgisi onay özelliğini etkinleştirmek için, bkz <xref:security/gdpr>..</span><span class="sxs-lookup"><span data-stu-id="12e01-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="12e01-257">Betiklerin ve ilgili statik varlıkların artık CDNs kullanmak yerine yerel dosyalar olarak başvuruluyor.</span><span class="sxs-lookup"><span data-stu-id="12e01-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="12e01-258">Daha fazla bilgi için, bkz. [betiklerin ve ilgili statik varlıkların artık geçerli ortama göre CDNs kullanmak yerine yerel dosyalar olarak başvuruluyor (ASPNET/AspNetCore. Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="12e01-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="12e01-259">Angular şablonu, angular 8 ' i kullanacak şekilde güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="12e01-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="12e01-260">Razor Sınıf kitaplığı (RCL) şablonu varsayılan olarak varsayılan Razor olarak bileşen geliştirme olur.</span><span class="sxs-lookup"><span data-stu-id="12e01-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="12e01-261">Visual Studio 'da yeni bir şablon seçeneği, sayfalar ve görünümler için şablon desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="12e01-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="12e01-262">Bir komut kabuğunda şablondan RCL oluştururken, ( `--support-pages-and-views` `dotnet new razorclasslib --support-pages-and-views`) seçeneğini geçirin.</span><span class="sxs-lookup"><span data-stu-id="12e01-262">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="12e01-263">Genel Konak</span><span class="sxs-lookup"><span data-stu-id="12e01-263">Generic Host</span></span>

<span data-ttu-id="12e01-264">ASP.NET Core 3,0 şablonları kullanılır <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="12e01-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="12e01-265">Önceki sürümler kullanıldı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="12e01-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="12e01-266">.NET Core Genel Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) kullanımı, Web 'e özgü olmayan diğer sunucu senaryolarıyla ASP.NET Core uygulamaları daha iyi tümleştirme sağlar.</span><span class="sxs-lookup"><span data-stu-id="12e01-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="12e01-267">Daha fazla bilgi için bkz. [Hostbuilder WebHostBuilder 'ın yerini alır](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="12e01-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="12e01-268">Konak yapılandırması</span><span class="sxs-lookup"><span data-stu-id="12e01-268">Host configuration</span></span>

<span data-ttu-id="12e01-269">ASP.NET Core 3,0 ' nin yayınlanmasından önce, Web konağının ana bilgisayar `ASPNETCORE_` yapılandırması için, öneki olan ortam değişkenleri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="12e01-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="12e01-270">3,0 ' de `AddEnvironmentVariables` , ile `DOTNET_` `CreateDefaultBuilder`konak yapılandırması için önekli ortam değişkenlerini yüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="12e01-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="12e01-271">Başlangıç Oluşturucu Ekleme değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="12e01-271">Changes to Startup constructor injection</span></span>

<span data-ttu-id="12e01-272">Genel konak yalnızca Oluşturucu ekleme için `Startup` aşağıdaki türleri destekler:</span><span class="sxs-lookup"><span data-stu-id="12e01-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="12e01-273">Tüm hizmetler yine de `Startup.Configure` yönteme bağımsız değişken olarak eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="12e01-274">Daha fazla bilgi için bkz. [genel ana bilgisayar başlangıç Oluşturucu Ekleme (ASPNET/duyurular #353)](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="12e01-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="12e01-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="12e01-275">Kestrel</span></span>

* <span data-ttu-id="12e01-276">Kestrel yapılandırması, genel konağa geçiş için güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="12e01-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="12e01-277">3,0 ' de, Kestrel tarafından `ConfigureWebHostDefaults`sağlanmış Web ana bilgisayar Oluşturucu üzerinde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="12e01-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="12e01-278">Bağlantı bağdaştırıcıları, Kestrel adresinden kaldırılmıştır ve bağlantı ara yazılımı ile değiştirilmiştir ve bu, ASP.NET Core işlem hattındaki HTTP ara hattına benzer ancak alt düzey bağlantılar için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="12e01-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="12e01-279">Kestrel aktarım katmanı, içinde `Connections.Abstractions`ortak bir arabirim olarak kullanıma sunuldu.</span><span class="sxs-lookup"><span data-stu-id="12e01-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="12e01-280">Sondaki üstbilgiler yeni bir koleksiyona taşınarak üstbilgiler ve tanıtımları arasındaki belirsizlik çözüldü.</span><span class="sxs-lookup"><span data-stu-id="12e01-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="12e01-281">Gibi zaman uyumlu g/ç API 'Leri `HttpRequest.Body.Read`, uygulama kilitlenmelerine neden olan yaygın bir iş parçacığı kaynağıdır.</span><span class="sxs-lookup"><span data-stu-id="12e01-281">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="12e01-282">3,0 ' `AllowSynchronousIO` de varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="12e01-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="12e01-283">Daha fazla bilgi için bkz. <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="12e01-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="12e01-284">Varsayılan olarak etkin HTTP/2</span><span class="sxs-lookup"><span data-stu-id="12e01-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="12e01-285">HTTP/2, HTTPS uç noktaları için Kestrel içinde varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="12e01-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="12e01-286">IIS veya HTTP. sys için HTTP/2 desteği, işletim sistemi tarafından desteklendikleri zaman etkindir.</span><span class="sxs-lookup"><span data-stu-id="12e01-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="12e01-287">İstek üzerine EventCounters</span><span class="sxs-lookup"><span data-stu-id="12e01-287">EventCounters on request</span></span>

<span data-ttu-id="12e01-288">EventSource `Microsoft.AspNetCore.Hosting`barındırma, gelen isteklerle ilgili aşağıdaki yeni <xref:System.Diagnostics.Tracing.EventCounter> türleri yayar:</span><span class="sxs-lookup"><span data-stu-id="12e01-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="12e01-289">Uç nokta yönlendirme</span><span class="sxs-lookup"><span data-stu-id="12e01-289">Endpoint routing</span></span>

<span data-ttu-id="12e01-290">Çerçeveler 'in (örneğin, MVC) ara yazılım ile iyi çalışmasına izin veren uç nokta yönlendirme geliştirildi:</span><span class="sxs-lookup"><span data-stu-id="12e01-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="12e01-291">Ara yazılım ve uç noktaların sırası istek işleme ardışık düzeninde yapılandırılabilir `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="12e01-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="12e01-292">Uç noktalar ve ara yazılımlar, sistem durumu denetimleri gibi diğer ASP.NET Core tabanlı teknolojilerle birlikte.</span><span class="sxs-lookup"><span data-stu-id="12e01-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="12e01-293">Uç noktalar, hem yazılım hem de MVC 'de CORS veya yetkilendirme gibi bir ilke uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="12e01-294">Filtreler ve öznitelikler, denetleyicilerde yöntemler üzerine yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="12e01-295">Daha fazla bilgi için bkz. <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="12e01-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="12e01-296">Sistem durumu denetimleri</span><span class="sxs-lookup"><span data-stu-id="12e01-296">Health Checks</span></span>

<span data-ttu-id="12e01-297">Sistem durumu denetimleri, genel ana bilgisayar ile Endpoint Routing kullanır.</span><span class="sxs-lookup"><span data-stu-id="12e01-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="12e01-298">' `Startup.Configure`De, `MapHealthChecks` uç nokta URL 'si veya göreli yol ile Endpoint Builder ' ı çağırın:</span><span class="sxs-lookup"><span data-stu-id="12e01-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="12e01-299">Durum denetimleri uç noktaları şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="12e01-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="12e01-300">İzin verilen bir veya daha fazla Konakları/bağlantı noktasını belirtin.</span><span class="sxs-lookup"><span data-stu-id="12e01-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="12e01-301">Yetkilendirme gerektir.</span><span class="sxs-lookup"><span data-stu-id="12e01-301">Require authorization.</span></span>
* <span data-ttu-id="12e01-302">CORS gerektir.</span><span class="sxs-lookup"><span data-stu-id="12e01-302">Require CORS.</span></span>

<span data-ttu-id="12e01-303">Daha fazla bilgi için aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="12e01-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="12e01-304">HttpContext üzerindeki kanallar</span><span class="sxs-lookup"><span data-stu-id="12e01-304">Pipes on HttpContext</span></span>

<span data-ttu-id="12e01-305">Artık istek gövdesini okumak ve yanıt gövdesini <xref:System.IO.Pipelines> API kullanarak yazmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="12e01-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="12e01-306">Sanal Makineye (VM) bağlı bir veya birden çok işletim sistemi diski içerdiği için</span><span class="sxs-lookup"><span data-stu-id="12e01-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="12e01-307">`HttpRequest.BodyReader`özelliği, istek <xref:System.IO.Pipelines.PipeReader> gövdesini okumak için kullanılabilecek bir sağlar.</span><span class="sxs-lookup"><span data-stu-id="12e01-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="12e01-308">Sanal Makineye (VM) bağlı bir veya birden çok işletim sistemi diski içerdiği için</span><span class="sxs-lookup"><span data-stu-id="12e01-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="12e01-309">`HttpResponse.BodyWriter`özelliği, yanıt <xref:System.IO.Pipelines.PipeWriter> gövdesini yazmak için kullanılabilecek bir sağlar.</span><span class="sxs-lookup"><span data-stu-id="12e01-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="12e01-310">`HttpRequest.BodyReader``HttpRequest.Body` akışın analog bir kadır.</span><span class="sxs-lookup"><span data-stu-id="12e01-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="12e01-311">`HttpResponse.BodyWriter``HttpResponse.Body` akışın analog bir kadır.</span><span class="sxs-lookup"><span data-stu-id="12e01-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="12e01-312">IIS 'de geliştirilmiş hata raporlama</span><span class="sxs-lookup"><span data-stu-id="12e01-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="12e01-313">IIS 'de ASP.NET Core uygulamalar barındırırken başlatma hataları artık daha zengin Tanılama verileri oluşturuyor.</span><span class="sxs-lookup"><span data-stu-id="12e01-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="12e01-314">Bu hatalar, geçerli her yerde yığın izlemelerle Windows olay günlüğü 'ne bildirilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="12e01-315">Ayrıca, tüm uyarılar, hatalar ve işlenmemiş özel durumlar Windows olay günlüğü 'ne kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="12e01-316">Çalışan hizmeti ve çalışan SDK 'Sı</span><span class="sxs-lookup"><span data-stu-id="12e01-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="12e01-317">.NET Core 3,0 yeni çalışan hizmeti uygulama şablonunu tanıtır.</span><span class="sxs-lookup"><span data-stu-id="12e01-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="12e01-318">Bu şablon, .NET Core 'da uzun süre çalışan hizmetler yazmak için bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="12e01-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="12e01-319">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="12e01-319">For more information, see:</span></span>

* [<span data-ttu-id="12e01-320">Windows Hizmetleri olarak .NET Core çalışanları</span><span class="sxs-lookup"><span data-stu-id="12e01-320">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="12e01-321">İletilen üstbilgiler ara yazılımı geliştirmeleri</span><span class="sxs-lookup"><span data-stu-id="12e01-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="12e01-322">Önceki ASP.NET Core sürümlerinde, <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> bir Azure LINUX 'a dağıtılırken veya IIS dışında herhangi bir ters proxy 'nin arkasında sorun.</span><span class="sxs-lookup"><span data-stu-id="12e01-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="12e01-323">Önceki sürümlere yönelik düzeltmeler, [Linux ve IIS olmayan ters proxy 'lerin düzenini iletme](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)bölümünde belgelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="12e01-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="12e01-324">Bu senaryo ASP.NET Core 3,0 ' de düzeltilmiştir.</span><span class="sxs-lookup"><span data-stu-id="12e01-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="12e01-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` Ortam değişkeni olarak `true`ayarlandığında, ana bilgisayar [iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) sağlar.</span><span class="sxs-lookup"><span data-stu-id="12e01-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="12e01-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`, kapsayıcı görüntülerimizde olarak `true` ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="12e01-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="12e01-327">Performans iyileştirmeleri</span><span class="sxs-lookup"><span data-stu-id="12e01-327">Performance improvements</span></span>

<span data-ttu-id="12e01-328">ASP.NET Core 3,0, bellek kullanımını azaltan ve üretilen işi geliştiren birçok geliştirme içerir:</span><span class="sxs-lookup"><span data-stu-id="12e01-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="12e01-329">Kapsamlı hizmetler için yerleşik bağımlılık ekleme kapsayıcısını kullanırken bellek kullanımında azaltma.</span><span class="sxs-lookup"><span data-stu-id="12e01-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="12e01-330">Ara yazılım senaryoları ve yönlendirme dahil olmak üzere çerçeve genelinde ayırmalarda azaltma.</span><span class="sxs-lookup"><span data-stu-id="12e01-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="12e01-331">WebSocket bağlantıları için bellek kullanımında azaltma.</span><span class="sxs-lookup"><span data-stu-id="12e01-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="12e01-332">HTTPS bağlantıları için bellek azaltma ve verimlilik geliştirmeleri.</span><span class="sxs-lookup"><span data-stu-id="12e01-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="12e01-333">Yeni iyileştirilmiş ve tamamen zaman uyumsuz JSON serileştiricisi.</span><span class="sxs-lookup"><span data-stu-id="12e01-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="12e01-334">Form ayrıştırılırken bellek kullanımı ve üretilen iş iyileştirmeleri azaltılması.</span><span class="sxs-lookup"><span data-stu-id="12e01-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="12e01-335">ASP.NET Core 3,0 yalnızca .NET Core 3,0 üzerinde çalışır</span><span class="sxs-lookup"><span data-stu-id="12e01-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="12e01-336">ASP.NET Core 3,0 itibariyle, .NET Framework artık desteklenen bir hedef çerçeve değildir.</span><span class="sxs-lookup"><span data-stu-id="12e01-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="12e01-337">.NET Framework hedefleyen projeler [.NET Core 2,1 LTS sürümünü](https://dotnet.microsoft.com/download/dotnet-core/2.1)kullanarak tam olarak desteklenen bir biçimde devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="12e01-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="12e01-338">ASP.NET Core 2.1. x ile ilgili paketlerin çoğu, .NET Core 2,1 için üç yıllık LTS döneminin ötesinde süresiz olarak desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="12e01-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="12e01-339">Geçiş bilgileri için bkz. [kodunuzu .NET Core 'a .NET Framework](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="12e01-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="12e01-340">ASP.NET Core paylaşılan çerçevesini kullanma</span><span class="sxs-lookup"><span data-stu-id="12e01-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="12e01-341">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunan ASP.NET Core 3,0 paylaşılan çerçevesi artık proje dosyasında açık `<PackageReference />` bir öğe gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="12e01-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="12e01-342">Proje dosyasında `Microsoft.NET.Sdk.Web` SDK kullanılırken paylaşılan çerçeveye otomatik olarak başvurulur:</span><span class="sxs-lookup"><span data-stu-id="12e01-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="12e01-343">ASP.NET Core paylaşılan çerçevesinden kaldırılan derlemeler</span><span class="sxs-lookup"><span data-stu-id="12e01-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="12e01-344">ASP.NET Core 3,0 paylaşılan çerçevesinden çıkarılan en önemli derlemeler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="12e01-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="12e01-345">[Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.net).</span><span class="sxs-lookup"><span data-stu-id="12e01-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="12e01-346">ASP.NET Core 3,0 ' ye Json.NET eklemek için bkz. [Newtonsoft. JSON tabanlı JSON biçimi desteği ekleme](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="12e01-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="12e01-347">ASP.NET Core 3,0, `System.Text.Json` JSON okuma ve yazma için tanıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="12e01-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="12e01-348">Daha fazla bilgi için bu belgede [yenı JSON serileştirmesi](#new-json-serialization) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="12e01-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="12e01-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="12e01-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="12e01-350">Paylaşılan çerçeveden kaldırılan derlemelerin tamamen listesi için bkz [. Microsoft. AspNetCore. App 3,0 ' den kaldırılan derlemeler](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="12e01-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="12e01-351">Bu değişiklik için mosyon hakkında daha fazla bilgi için bkz. [3,0 'de Microsoft. AspNetCore. app 'e yönelik son değişiklikler](https://github.com/aspnet/Announcements/issues/325) ve [ASP.NET Core 3,0 ' de gelen değişikliklere ilk bakış](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="12e01-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 