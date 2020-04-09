---
title: ASP.NET Core 3.0'daki yenilikler
author: rick-anderson
description: ASP.NET Core 3.0'daki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 4886673a9b16b8be8d9a0b0d5c7002a91760544e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976982"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="8f98c-103">ASP.NET Core 3.0'daki yenilikler</span><span class="sxs-lookup"><span data-stu-id="8f98c-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="8f98c-104">Bu makalede, ilgili belgelere bağlantılar ile ASP.NET Core 3.0 en önemli değişiklikleri vurgulamaktadır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

Blazor<span data-ttu-id="8f98c-105">.NET ile etkileşimli istemci tarafı web Web Web Web Web'i oluşturmak için ASP.NET Core'da yeni bir çerçevedir:</span><span class="sxs-lookup"><span data-stu-id="8f98c-105"> is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="8f98c-106">JavaScript yerine C# kullanarak zengin etkileşimli web'ler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8f98c-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="8f98c-107">.NET'te yazılı sunucu ve istemci tarafı uygulama mantığını paylaşın.</span><span class="sxs-lookup"><span data-stu-id="8f98c-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="8f98c-108">Mobil tarayıcılar da dahil olmak üzere geniş tarayıcı desteği için UI'yi HTML ve CSS olarak işleyin.</span><span class="sxs-lookup"><span data-stu-id="8f98c-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

Blazor<span data-ttu-id="8f98c-109">çerçeve desteklenen senaryolar:</span><span class="sxs-lookup"><span data-stu-id="8f98c-109"> framework supported scenarios:</span></span>

* <span data-ttu-id="8f98c-110">Yeniden kullanılabilir UI bileşenleri (Jilet bileşenleri)</span><span class="sxs-lookup"><span data-stu-id="8f98c-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="8f98c-111">İstemci tarafı yönlendirme</span><span class="sxs-lookup"><span data-stu-id="8f98c-111">Client-side routing</span></span>
* <span data-ttu-id="8f98c-112">Bileşen düzenleri</span><span class="sxs-lookup"><span data-stu-id="8f98c-112">Component layouts</span></span>
* <span data-ttu-id="8f98c-113">Bağımlılık enjeksiyonu desteği</span><span class="sxs-lookup"><span data-stu-id="8f98c-113">Support for dependency injection</span></span>
* <span data-ttu-id="8f98c-114">Formlar ve doğrulama</span><span class="sxs-lookup"><span data-stu-id="8f98c-114">Forms and validation</span></span>
* <span data-ttu-id="8f98c-115">Razor sınıf kitaplıkları içeren bileşen kitaplıkları oluşturma</span><span class="sxs-lookup"><span data-stu-id="8f98c-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="8f98c-116">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="8f98c-116">JavaScript interop</span></span>

<span data-ttu-id="8f98c-117">Daha fazla bilgi için bkz. <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="8f98c-117">For more information, see <xref:blazor/index>.</span></span>

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="8f98c-118">Sunucu</span><span class="sxs-lookup"><span data-stu-id="8f98c-118"> Server</span></span>

Blazor<span data-ttu-id="8f98c-119">ui güncelleştirmelerinin nasıl uygulandığından bileşen oluşturma mantığını ayırır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-119"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="8f98c-120">Sunucu, ASP.NET Core uygulamasında Razor bileşenlerini sunucuda barındırma desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="8f98c-120"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="8f98c-121">UI güncelleştirmeleri bir SignalR bağlantı üzerinden işlenir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-121">UI updates are handled over a SignalR connection.</span></span> Blazor<span data-ttu-id="8f98c-122">Sunucu ASP.NET Core 3.0 desteklenir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-122"> Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="opno-locblazor-webassembly-preview"></a>Blazor<span data-ttu-id="8f98c-123">WebAssembly (Önizleme)</span><span class="sxs-lookup"><span data-stu-id="8f98c-123"> WebAssembly (Preview)</span></span>

Blazor<span data-ttu-id="8f98c-124">uygulamalar, WebAssembly tabanlı .NET çalışma zamanı kullanılarak doğrudan tarayıcıda çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-124"> apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> Blazor<span data-ttu-id="8f98c-125">WebAssembly önizlemededir ve Core 3.0ASP.NET de *desteklenmez.*</span><span class="sxs-lookup"><span data-stu-id="8f98c-125"> WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> Blazor<span data-ttu-id="8f98c-126">WebAssembly ASP.NET Core gelecekteki sürümünde desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-126"> WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="8f98c-127">Jilet bileşenleri</span><span class="sxs-lookup"><span data-stu-id="8f98c-127">Razor components</span></span>

Blazor<span data-ttu-id="8f98c-128">uygulamalar bileşenlerden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8f98c-128"> apps are built from components.</span></span> <span data-ttu-id="8f98c-129">Bileşenler, sayfa, iletişim kutusu veya form gibi kullanıcı arabiriminin (UI) kendi kendine yeten parçalarıdır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-129">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="8f98c-130">Bileşenler, UI oluşturma mantığını ve istemci tarafı olay işleyicilerini tanımlayan normal .NET sınıflarıdır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-130">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="8f98c-131">JavaScript olmadan zengin etkileşimli web uygulamaları oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8f98c-131">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="8f98c-132">Bileşenler Blazor genellikle HTML ve C#'ın doğal bir karışımı olan Razor sözdizimi kullanılarak yazılır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-132">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="8f98c-133">Jilet bileşenleri, her ikisinin de Razor kullandığı için Razor Pages ve MVC görünümlerine benzer.</span><span class="sxs-lookup"><span data-stu-id="8f98c-133">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="8f98c-134">İstek yanıt modeline dayanan sayfa ve görünümlerin aksine, bileşenler özellikle Web-yüksek ödenşeyini işlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-134">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="8f98c-135">gRPC</span><span class="sxs-lookup"><span data-stu-id="8f98c-135">gRPC</span></span>

<span data-ttu-id="8f98c-136">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="8f98c-136">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="8f98c-137">Popüler, yüksek performanslı bir RPC (uzaktan yordam çağrısı) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-137">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="8f98c-138">API geliştirme için dik kafalı bir sözleşme ilk yaklaşım sunar.</span><span class="sxs-lookup"><span data-stu-id="8f98c-138">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="8f98c-139">Şu lar gibi modern teknolojileri kullanır:</span><span class="sxs-lookup"><span data-stu-id="8f98c-139">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="8f98c-140">Ulaşım için HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8f98c-140">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="8f98c-141">Arabirim açıklama dili olarak protokol arabellekleri.</span><span class="sxs-lookup"><span data-stu-id="8f98c-141">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="8f98c-142">İkili serileştirme biçimi.</span><span class="sxs-lookup"><span data-stu-id="8f98c-142">Binary serialization format.</span></span>
* <span data-ttu-id="8f98c-143">Şu gibi özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="8f98c-143">Provides features such as:</span></span>

  * <span data-ttu-id="8f98c-144">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="8f98c-144">Authentication</span></span>
  * <span data-ttu-id="8f98c-145">Çift yönlü akış ve akış kontrolü.</span><span class="sxs-lookup"><span data-stu-id="8f98c-145">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="8f98c-146">İptal ve zaman zaman ları.</span><span class="sxs-lookup"><span data-stu-id="8f98c-146">Cancellation and timeouts.</span></span>

<span data-ttu-id="8f98c-147">core 3.0 ASP.NET gRPC işlevselliği içerir:</span><span class="sxs-lookup"><span data-stu-id="8f98c-147">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="8f98c-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; gRPC hizmetleri barındırma için ASP.NET Core çerçeve.</span><span class="sxs-lookup"><span data-stu-id="8f98c-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="8f98c-149">ASP.NET Core'daki gRPC, günlük ASP.NET, bağımlılık enjeksiyonu (DI), kimlik doğrulama ve yetkilendirme gibi standart ASP.NET Core özellikleriyle bütünleşir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-149">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="8f98c-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; tanıdık `HttpClient`üzerine inşa .NET Core için bir gRPC istemcisi .</span><span class="sxs-lookup"><span data-stu-id="8f98c-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="8f98c-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC istemci `HttpClientFactory`entegrasyonu ile .</span><span class="sxs-lookup"><span data-stu-id="8f98c-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="8f98c-152">Daha fazla bilgi için bkz. <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="8f98c-152">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="8f98c-153">Geçiş yönergeleri için [ SignalR güncelleştirme koduna](xref:migration/22-to-30#signalr) bakın.</span><span class="sxs-lookup"><span data-stu-id="8f98c-153">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> SignalR<span data-ttu-id="8f98c-154">şimdi `System.Text.Json` JSON iletilerini serihale/deserialize etmek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-154"> now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="8f98c-155">Bkz. Tabanlı serileştiriciyi geri yüklemek `Newtonsoft.Json`için talimatlar için [Newtonsoft.Json'a geç.](xref:migration/22-to-30#switch-to-newtonsoftjson)</span><span class="sxs-lookup"><span data-stu-id="8f98c-155">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="8f98c-156">JavaScript ve .NET Istemcileri için SignalR, destek otomatik yeniden bağlantı için eklendi.</span><span class="sxs-lookup"><span data-stu-id="8f98c-156">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="8f98c-157">Varsayılan olarak, istemci hemen yeniden bağlanmaya çalışır ve gerekirse 2, 10 ve 30 saniye sonra yeniden deneyin.</span><span class="sxs-lookup"><span data-stu-id="8f98c-157">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="8f98c-158">İstemci başarılı bir şekilde yeniden bağlanırsa, yeni bir bağlantı kimliği alır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-158">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="8f98c-159">Otomatik yeniden bağlanma devre dışı bırakma dır:</span><span class="sxs-lookup"><span data-stu-id="8f98c-159">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="8f98c-160">Yeniden bağlantı aralıkları milisaniye tabanlı süreler dizisi geçerek belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="8f98c-160">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="8f98c-161">Yeniden bağlantı aralıklarının tam denetimi için özel bir uygulama geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-161">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="8f98c-162">Son yeniden bağlanma aralığından sonra yeniden bağlantı başarısız olursa:</span><span class="sxs-lookup"><span data-stu-id="8f98c-162">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="8f98c-163">İstemci, bağlantının çevrimdışı olduğunu düşünüyor.</span><span class="sxs-lookup"><span data-stu-id="8f98c-163">The client considers the connection is offline.</span></span>
* <span data-ttu-id="8f98c-164">İstemci yeniden bağlanmaya çalışmayı durdurur.</span><span class="sxs-lookup"><span data-stu-id="8f98c-164">The client stops trying to reconnect.</span></span>

<span data-ttu-id="8f98c-165">Yeniden bağlantı denemeleri sırasında, kullanıcıya yeniden bağlantının denendiğini bildirmek için uygulama Kullanıcı Arabirimi'ni güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="8f98c-165">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="8f98c-166">Bağlantı kesildiğinde UI geri bildirimi SignalR sağlamak için istemci API'sı aşağıdaki olay işleyicilerini içerecek şekilde genişletildi:</span><span class="sxs-lookup"><span data-stu-id="8f98c-166">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="8f98c-167">`onreconnecting`: Geliştiricilere Kullanıcı Arabirimi'ni devre dışı bırakma veya kullanıcılara uygulamanın çevrimdışı olduğunu bilmeleri için bir fırsat verir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-167">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="8f98c-168">`onreconnected`: Bağlantı yeniden kurulduktan sonra geliştiricilere Kullanıcı Arabirimi'ni güncelleştirme fırsatı verir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-168">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="8f98c-169">Aşağıdaki kod, `onreconnecting` bağlanmaya çalışırken Kullanıcı Arabirimi'ni güncelleştirmek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="8f98c-169">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="8f98c-170">Bağlantıda Kullanıcı `onreconnected` Arabirimi'ni güncelleştirmek için aşağıdaki kod kullanır:</span><span class="sxs-lookup"><span data-stu-id="8f98c-170">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR<span data-ttu-id="8f98c-171">3.0 ve daha sonra bir hub yöntemi yetkilendirme gerektirdiğinde yetkilendirme işleyicileri için özel bir kaynak sağlar.</span><span class="sxs-lookup"><span data-stu-id="8f98c-171"> 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="8f98c-172">Kaynak bir `HubInvocationContext`örneğidir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-172">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="8f98c-173">Aşağıdakileri `HubInvocationContext` içerir:</span><span class="sxs-lookup"><span data-stu-id="8f98c-173">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="8f98c-174">Çağrılan hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="8f98c-174">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="8f98c-175">Hub yöntemine bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="8f98c-175">Arguments to the hub method.</span></span>

<span data-ttu-id="8f98c-176">Azure Active Directory üzerinden birden çok kuruluş oturum açma izni veren bir sohbet odası uygulamasının aşağıdaki örneğini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="8f98c-176">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="8f98c-177">Microsoft hesabı olan herkes sohbet etmek için oturum açabilir, ancak yalnızca sahip olan kuruluşun üyeleri kullanıcıları yasaklayabilir veya kullanıcıların sohbet geçmişlerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-177">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="8f98c-178">Uygulama belirli kullanıcılardan belirli işlevleri kısıtlayabilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-178">The app could restrict certain functionality from specific users.</span></span>

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

<span data-ttu-id="8f98c-179">Önceki kodda, `DomainRestrictedRequirement` bir özel `IAuthorizationRequirement`olarak hizmet vermektedir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-179">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="8f98c-180">Kaynak `HubInvocationContext` parametresi geçirildiği için, iç mantık şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="8f98c-180">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="8f98c-181">Hub'ın çağrıldığı bağlamı denetleyin.</span><span class="sxs-lookup"><span data-stu-id="8f98c-181">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="8f98c-182">Kullanıcının tek tek Hub yöntemlerini yürütmesine izin verme konusunda kararlar verin.</span><span class="sxs-lookup"><span data-stu-id="8f98c-182">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="8f98c-183">Tek tek Hub yöntemleri, kodun çalışma zamanında denetleyebileceği ilkenin adı ile işaretlenebilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-183">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="8f98c-184">İstemciler tek tek Hub `DomainRestrictedRequirement` yöntemlerini çağırmaya çalıştıkça, işleyici metotlara erişimi çalıştırAr ve denetler.</span><span class="sxs-lookup"><span data-stu-id="8f98c-184">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="8f98c-185">Denetimlerin erişim `DomainRestrictedRequirement` şekline bağlı olarak:</span><span class="sxs-lookup"><span data-stu-id="8f98c-185">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="8f98c-186">Tüm oturum açmış kullanıcılar `SendMessage` yöntemi arayabilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-186">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="8f98c-187">Yalnızca bir `@jabbr.net` e-posta adresiyle giriş yapmış olan kullanıcılar kullanıcıların geçmişlerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-187">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="8f98c-188">Kullanıcıları `bob42@jabbr.net` yalnızca sohbet odasından yasaklayabilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-188">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

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

<span data-ttu-id="8f98c-189">İlke `DomainRestricted` oluşturma şunları içerebilir:</span><span class="sxs-lookup"><span data-stu-id="8f98c-189">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="8f98c-190">*Startup.cs,* yeni ilke ekleyerek.</span><span class="sxs-lookup"><span data-stu-id="8f98c-190">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="8f98c-191">Parametre `DomainRestrictedRequirement` olarak özel gereksinimi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="8f98c-191">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="8f98c-192">Yetkilendirme `DomainRestricted` aracıile kayıt.</span><span class="sxs-lookup"><span data-stu-id="8f98c-192">Registering `DomainRestricted` with the authorization middleware.</span></span>

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

SignalR<span data-ttu-id="8f98c-193">hub'lar [Endpoint Yönlendirme'yi](xref:fundamentals/routing)kullanır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-193"> hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> SignalR<span data-ttu-id="8f98c-194">hub bağlantısı daha önce açıkça yapıldı:</span><span class="sxs-lookup"><span data-stu-id="8f98c-194"> hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="8f98c-195">Önceki sürümde, geliştiricilerin denetleyicileri, Razor sayfalarını ve hub'ları çeşitli yerlere bağlamaları gerekiyordu.</span><span class="sxs-lookup"><span data-stu-id="8f98c-195">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="8f98c-196">Açık bağlantı, neredeyse aynı yönlendirme segmentleri bir dizi sonuçlanır:</span><span class="sxs-lookup"><span data-stu-id="8f98c-196">Explicit connection results in a series of nearly-identical routing segments:</span></span>

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

SignalR<span data-ttu-id="8f98c-197">3.0 hub'ları uç nokta yönlendirme yoluyla yönlendirilebilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-197"> 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="8f98c-198">Uç nokta yönlendirmeile, genellikle tüm yönlendirme şu şekilde `UseRouting`yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="8f98c-198">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="8f98c-199">ASP.NET Core 3.0 SignalR ekledi:</span><span class="sxs-lookup"><span data-stu-id="8f98c-199">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="8f98c-200">İstemciden sunucuya akış.</span><span class="sxs-lookup"><span data-stu-id="8f98c-200">Client-to-server streaming.</span></span> <span data-ttu-id="8f98c-201">İstemciden sunucuya akışla, sunucu tarafı yöntemleri bir `IAsyncEnumerable<T>` `ChannelReader<T>`veya .</span><span class="sxs-lookup"><span data-stu-id="8f98c-201">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="8f98c-202">Aşağıdaki C# örneğinde, `UploadStream` Hub'daki yöntem istemciden bir dize akışı alır:</span><span class="sxs-lookup"><span data-stu-id="8f98c-202">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="8f98c-203">.NET istemci uygulamaları, `IAsyncEnumerable<T>` yukarıdaki `ChannelReader<T>` `UploadStream` Hub `stream` yönteminin bağımsız değişkeni olarak bir veya örnek ten geçebilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-203">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="8f98c-204">`for` Döngü tamamlandıktan ve yerel işlev çıktıktan sonra akış tamamlama gönderilir:</span><span class="sxs-lookup"><span data-stu-id="8f98c-204">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

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

<span data-ttu-id="8f98c-205">JavaScript istemci uygulamaları SignalR `Subject` yukarıdaki `stream` `UploadStream` Hub yönteminin bağımsız değişkeni için (veya [RxJS Konusu)](https://rxjs.dev/api/index/class/Subject)kullanır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-205">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="8f98c-206">JavaScript kodu, dizeleri yakalanan ve sunucuya gönderilmeye hazır olarak işlemek için `subject.next` yöntemi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8f98c-206">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="8f98c-207">Önceki iki parçacık gibi kod kullanılarak, gerçek zamanlı akış deneyimleri oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-207">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="8f98c-208">Yeni JSON serileştirme</span><span class="sxs-lookup"><span data-stu-id="8f98c-208">New JSON serialization</span></span>

<span data-ttu-id="8f98c-209">ASP.NET Core 3.0 <xref:System.Text.Json> şimdi Varsayılan olarak JSON serileştirme için kullanır:</span><span class="sxs-lookup"><span data-stu-id="8f98c-209">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="8f98c-210">Okur ve JSON asynchronously yazıyor.</span><span class="sxs-lookup"><span data-stu-id="8f98c-210">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="8f98c-211">UTF-8 metni için optimize edi.</span><span class="sxs-lookup"><span data-stu-id="8f98c-211">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="8f98c-212">Genellikle `Newtonsoft.Json`daha yüksek performans .</span><span class="sxs-lookup"><span data-stu-id="8f98c-212">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="8f98c-213">Core 3.0'ASP.NET Json.NET eklemek için Bkz. [Newtonsoft.Json tabanlı JSON format desteği ekle.](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)</span><span class="sxs-lookup"><span data-stu-id="8f98c-213">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="8f98c-214">Yeni Razor direktifleri</span><span class="sxs-lookup"><span data-stu-id="8f98c-214">New Razor directives</span></span>

<span data-ttu-id="8f98c-215">Aşağıdaki liste yeni Razor yönergeleri içerir:</span><span class="sxs-lookup"><span data-stu-id="8f98c-215">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="8f98c-216">[`@attribute`](xref:mvc/views/razor#attribute)&ndash; Yönerge, `@attribute` verilen özniteliği oluşturulan sayfa veya görünümün sınıfına uygular.</span><span class="sxs-lookup"><span data-stu-id="8f98c-216">[`@attribute`](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="8f98c-217">Örneğin, `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="8f98c-217">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="8f98c-218">[`@implements`](xref:mvc/views/razor#implements)&ndash; Yönerge, `@implements` oluşturulan sınıf için bir arabirim uygular.</span><span class="sxs-lookup"><span data-stu-id="8f98c-218">[`@implements`](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="8f98c-219">Örneğin, `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="8f98c-219">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="8f98c-220">IdentityServer4 web API'leri ve SP'leri için kimlik doğrulamayı ve yetkilendirmeyi destekler</span><span class="sxs-lookup"><span data-stu-id="8f98c-220">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="8f98c-221">ASP.NET Core 3.0, web API yetkilendirme desteğini kullanarak Tek Sayfalı Uygulamalarda (SPA' lar) kimlik doğrulaması sunar.</span><span class="sxs-lookup"><span data-stu-id="8f98c-221">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="8f98c-222">ASP.NET Kullanıcıların kimlik doğrulaması ve depolanması için Temel Kimlik, Open ID Connect'i uygulamak için [IdentityServer4](https://identityserver.io/) ile birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-222">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="8f98c-223">IdentityServer4 Core 3.0 ASP.NET için bir OpenID Connect ve OAuth 2.0 çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-223">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="8f98c-224">Aşağıdaki güvenlik özelliklerini sağlar:</span><span class="sxs-lookup"><span data-stu-id="8f98c-224">It enables the following security features:</span></span>

* <span data-ttu-id="8f98c-225">Hizmet Olarak Kimlik Doğrulama (AaaS)</span><span class="sxs-lookup"><span data-stu-id="8f98c-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="8f98c-226">Birden fazla uygulama türü üzerinde tek oturum açma/kapatma (SSO)</span><span class="sxs-lookup"><span data-stu-id="8f98c-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="8f98c-227">API'ler için erişim denetimi</span><span class="sxs-lookup"><span data-stu-id="8f98c-227">Access control for APIs</span></span>
* <span data-ttu-id="8f98c-228">Federasyon Ağ Geçidi</span><span class="sxs-lookup"><span data-stu-id="8f98c-228">Federation Gateway</span></span>

<span data-ttu-id="8f98c-229">Daha fazla bilgi [için, IdentityServer4 belgelerine](http://docs.identityserver.io/en/latest/index.html) veya [SP'ler için kimlik doğrulama ve yetkilendirmeye](xref:security/authentication/identity/spa)bakın.</span><span class="sxs-lookup"><span data-stu-id="8f98c-229">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="8f98c-230">Sertifika ve Kerberos kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="8f98c-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="8f98c-231">Sertifika kimlik doğrulaması şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="8f98c-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="8f98c-232">Sunucuyu sertifika kabul etmek üzere yapılandırma.</span><span class="sxs-lookup"><span data-stu-id="8f98c-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="8f98c-233">Kimlik doğrulama ara sını `Startup.Configure`ekleme.</span><span class="sxs-lookup"><span data-stu-id="8f98c-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="8f98c-234">Sertifika kimlik doğrulama hizmetini `Startup.ConfigureServices`'ye ekleme</span><span class="sxs-lookup"><span data-stu-id="8f98c-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="8f98c-235">Sertifika kimlik doğrulaması için seçenekler şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="8f98c-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="8f98c-236">İmzalı sertifikaları kabul edin.</span><span class="sxs-lookup"><span data-stu-id="8f98c-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="8f98c-237">Sertifika iptalini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="8f98c-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="8f98c-238">Sunulan sertifikanın içinde doğru kullanım bayrakları olduğundan kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="8f98c-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="8f98c-239">Varsayılan kullanıcı ilkesi sertifika özelliklerinden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8f98c-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="8f98c-240">Kullanıcı ilkesi, anaparanın eklanmasını veya değiştirilmesini sağlayan bir olay içerir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="8f98c-241">Daha fazla bilgi için bkz. <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="8f98c-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="8f98c-242">[Windows Kimlik Doğrulama](/windows-server/security/windows-authentication/windows-authentication-overview) Linux ve macOS üzerine genişletildi.</span><span class="sxs-lookup"><span data-stu-id="8f98c-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="8f98c-243">Önceki sürümlerde, Windows Kimlik Doğrulama [IIS](xref:host-and-deploy/iis/index) ve [HttpSys](xref:fundamentals/servers/httpsys)ile sınırlıydı.</span><span class="sxs-lookup"><span data-stu-id="8f98c-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="8f98c-244">Core 3.0ASP.NET, [Kestrel](xref:fundamentals/servers/kestrel) Windows, Linux ve MacOS Windows etki alanı birleştirilmiş ana bilgisayarlar için Müzakere, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)ve [NTLM](/windows-server/security/kerberos/ntlm-overview)kullanma yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="8f98c-245">Bu kimlik doğrulama şemalarının kerkenez desteği [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="8f98c-246">Diğer kimlik doğrulama hizmetlerinde olduğu gibi, kimlik doğrulama uygulamasını geniş bir şekilde yapılandırın ve hizmeti yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="8f98c-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

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

<span data-ttu-id="8f98c-247">Ana bilgisayar gereksinimleri:</span><span class="sxs-lookup"><span data-stu-id="8f98c-247">Host requirements:</span></span>

* <span data-ttu-id="8f98c-248">Windows ana bilgisayarlarının, uygulamayı barındıran kullanıcı hesabına [Hizmet Ana](/windows/win32/ad/service-principal-names) Adı (SPNs) eklenmeleri gerekir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="8f98c-249">Linux ve macOS makineleri etki alanına birleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="8f98c-250">Web işlemi için SPN'ler oluşturulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="8f98c-251">[Anahtar sekme dosyaları](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) ana bilgisayarda oluşturulmalı ve yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="8f98c-252">Daha fazla bilgi için bkz. <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="8f98c-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="8f98c-253">Şablon değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="8f98c-253">Template changes</span></span>

<span data-ttu-id="8f98c-254">Web UI şablonları (Razor Pages, MVC denetleyici ve görünümler) aşağıdaki kaldırılmış var:</span><span class="sxs-lookup"><span data-stu-id="8f98c-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="8f98c-255">Çerez onayı UI artık dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="8f98c-256">ASP.NET Core 3.0 şablonu oluşturulan bir uygulamada çerez <xref:security/gdpr>onayı özelliğini etkinleştirmek için bkz.</span><span class="sxs-lookup"><span data-stu-id="8f98c-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="8f98c-257">Komut dosyaları ve ilgili statik varlıklar artık CDN'ler kullanmak yerine yerel dosyalar olarak başvurulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="8f98c-258">Daha fazla bilgi için, bkz [#14350.](https://github.com/dotnet/AspNetCore.Docs/issues/14350)</span><span class="sxs-lookup"><span data-stu-id="8f98c-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="8f98c-259">Açısal 8'i kullanmak üzere güncelleştirilmiş Açısal şablon.</span><span class="sxs-lookup"><span data-stu-id="8f98c-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="8f98c-260">Razor sınıf kitaplığı (RCL) şablonu varsayılan olarak Razor bileşen geliştirme için varsayılan dır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="8f98c-261">Visual Studio'da yeni bir şablon seçeneği, sayfalar ve görünümler için şablon desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="8f98c-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="8f98c-262">Komut kabuğundaki şablondan Bir RCL oluştururken, `--support-pages-and-views` `dotnet new razorclasslib --support-pages-and-views`seçeneği ().</span><span class="sxs-lookup"><span data-stu-id="8f98c-262">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="8f98c-263">Genel Konak</span><span class="sxs-lookup"><span data-stu-id="8f98c-263">Generic Host</span></span>

<span data-ttu-id="8f98c-264">Core 3.0 şablonlarının <xref:fundamentals/host/generic-host>ASP.NET kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="8f98c-265">Önceki sürümler kullanılır. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder></span><span class="sxs-lookup"><span data-stu-id="8f98c-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="8f98c-266">.NET Core Generic Host<xref:Microsoft.Extensions.Hosting.HostBuilder>() kullanarak web'e özgü olmayan diğer sunucu senaryoları ile ASP.NET Core uygulamaları daha iyi entegrasyon sağlar.</span><span class="sxs-lookup"><span data-stu-id="8f98c-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="8f98c-267">Daha fazla bilgi için, [HostBuilder WebHostBuilder yerini](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder)bakın.</span><span class="sxs-lookup"><span data-stu-id="8f98c-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="8f98c-268">Ana bilgisayar yapılandırması</span><span class="sxs-lookup"><span data-stu-id="8f98c-268">Host configuration</span></span>

<span data-ttu-id="8f98c-269">ASP.NET Core 3.0 yayımlanmasından önce, önceden belirlenmiş `ASPNETCORE_` ortam değişkenleri Web Barındıran Kişi'nin ana bilgisayar yapılandırması için yüklenmiş.</span><span class="sxs-lookup"><span data-stu-id="8f98c-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="8f98c-270">3.0'da, `AddEnvironmentVariables` ana bilgisayar yapılandırması `DOTNET_` için önceden belirlenmiş `CreateDefaultBuilder`ortam değişkenlerini yüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="8f98c-271">Başlangıç yapıcı enjeksiyonunda yapılan değişiklikler</span><span class="sxs-lookup"><span data-stu-id="8f98c-271">Changes to Startup constructor injection</span></span>

<span data-ttu-id="8f98c-272">Genel Ana Bilgisayar yalnızca yapıcı `Startup` enjeksiyon için aşağıdaki türleri destekler:</span><span class="sxs-lookup"><span data-stu-id="8f98c-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="8f98c-273">Tüm hizmetler yine de `Startup.Configure` doğrudan yönteme bağımsız değişken olarak enjekte edilebilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="8f98c-274">Daha fazla bilgi için bkz [#353.](https://github.com/aspnet/Announcements/issues/353)</span><span class="sxs-lookup"><span data-stu-id="8f98c-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="8f98c-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="8f98c-275">Kestrel</span></span>

* <span data-ttu-id="8f98c-276">Kerkenez yapılandırması Genel Ana Bilgisayar'a geçiş için güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="8f98c-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="8f98c-277">3.0 yılında, Kerkenez tarafından `ConfigureWebHostDefaults`sağlanan web barındırma oluşturucu üzerinde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="8f98c-278">Bağlantı Bağdaştırıcıları Kerkenez'den kaldırıldı ve ASP.NET Core ardışık ASP.NET'daki ANCAK alt düzey bağlantılar için BENZER Olan Bağlantı Middleware ile değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="8f98c-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="8f98c-279">Kerkenez aktarım katmanı, 'de `Connections.Abstractions`genel bir arayüz olarak ortaya çıkmıştır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="8f98c-280">Üstbilgi ve römorklar arasındaki belirsizlik, izleyen üstbilginin yeni bir koleksiyona taşınmasıyla giderildi.</span><span class="sxs-lookup"><span data-stu-id="8f98c-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="8f98c-281">Senkron G/Ç API'leri, `HttpRequest.Body.Read`örneğin, uygulama çökmelerine yol açan iş parçacığı açlığı için ortak bir kaynaktır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-281">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="8f98c-282">3.0'da, `AllowSynchronousIO` varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="8f98c-283">Daha fazla bilgi için bkz. <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="8f98c-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="8f98c-284">VARSAYıLAN OLARAK ETKINLEŞTIRILEN HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8f98c-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="8f98c-285">HTTPS uç noktaları için Kestrel'de varsayılan olarak HTTP/2 etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="8f98c-286">İşletim sistemi tarafından desteklendiğinde IIS veya HTTP.sys için HTTP/2 desteği etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="8f98c-287">İstek üzerine EventCounters</span><span class="sxs-lookup"><span data-stu-id="8f98c-287">EventCounters on request</span></span>

<span data-ttu-id="8f98c-288">Hosting EventSource, `Microsoft.AspNetCore.Hosting`gelen isteklerle ilgili <xref:System.Diagnostics.Tracing.EventCounter> aşağıdaki yeni türleri yayır:</span><span class="sxs-lookup"><span data-stu-id="8f98c-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="8f98c-289">Uç nokta yönlendirme</span><span class="sxs-lookup"><span data-stu-id="8f98c-289">Endpoint routing</span></span>

<span data-ttu-id="8f98c-290">Çerçevelerin (örneğin, MVC) ara yazılımla iyi çalışmasını sağlayan Uç Nokta Yönlendirmesi geliştirildi:</span><span class="sxs-lookup"><span data-stu-id="8f98c-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="8f98c-291">Ara yazılım ve uç noktaların `Startup.Configure`sırası, istek işleme ardışık düzeninde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="8f98c-292">Uç noktalar ve ara yazılımlar, Sağlık Denetimleri gibi diğer ASP.NET Çekirdek tabanlı teknolojiler ile iyi bir şekilde oluşur.</span><span class="sxs-lookup"><span data-stu-id="8f98c-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="8f98c-293">Uç noktalar, hem ara yazılımda hem de MVC'de CORS veya yetkilendirme gibi bir ilke uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="8f98c-294">Filtreler ve öznitelikler denetleyicilerde yöntemler üzerine yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="8f98c-295">Daha fazla bilgi için bkz. <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="8f98c-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="8f98c-296">Sistem durumu denetimleri</span><span class="sxs-lookup"><span data-stu-id="8f98c-296">Health Checks</span></span>

<span data-ttu-id="8f98c-297">Sistem Durumu Denetimleri, Genel Ana Bilgisayar ile uç nokta yönlendirmeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="8f98c-298">In, `Startup.Configure` `MapHealthChecks` bitiş noktası URL'si veya göreli yolu olan bitiş noktası oluşturucuyu arayın:</span><span class="sxs-lookup"><span data-stu-id="8f98c-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="8f98c-299">Sistem Durumu Denetimleri bitiş noktaları şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="8f98c-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="8f98c-300">İzin verilen bir veya daha fazla ana bilgisayar/bağlantı noktası belirtin.</span><span class="sxs-lookup"><span data-stu-id="8f98c-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="8f98c-301">Yetkilendirme gerektirir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-301">Require authorization.</span></span>
* <span data-ttu-id="8f98c-302">CORS gerektirir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-302">Require CORS.</span></span>

<span data-ttu-id="8f98c-303">Daha fazla bilgi için aşağıdaki makalelere bakın:</span><span class="sxs-lookup"><span data-stu-id="8f98c-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="8f98c-304">HttpContext üzerinde Borular</span><span class="sxs-lookup"><span data-stu-id="8f98c-304">Pipes on HttpContext</span></span>

<span data-ttu-id="8f98c-305">Artık istek gövdesini okumak ve <xref:System.IO.Pipelines> YANıT gövdesini API'yi kullanarak yazmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="8f98c-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="8f98c-306">Sanal Makineye (VM) bağlı bir veya birden çok işletim sistemi diski içerdiği için</span><span class="sxs-lookup"><span data-stu-id="8f98c-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="8f98c-307">`HttpRequest.BodyReader`özelliği istek <xref:System.IO.Pipelines.PipeReader> gövdesini okumak için kullanılabilecek bir özellik sağlar.</span><span class="sxs-lookup"><span data-stu-id="8f98c-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="8f98c-308">Sanal Makineye (VM) bağlı bir veya birden çok işletim sistemi diski içerdiği için</span><span class="sxs-lookup"><span data-stu-id="8f98c-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="8f98c-309">`HttpResponse.BodyWriter`özelliği yanıt <xref:System.IO.Pipelines.PipeWriter> gövdesi yazmak için kullanılabilecek bir sağlar.</span><span class="sxs-lookup"><span data-stu-id="8f98c-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="8f98c-310">`HttpRequest.BodyReader`akımın `HttpRequest.Body` bir analogudur.</span><span class="sxs-lookup"><span data-stu-id="8f98c-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="8f98c-311">`HttpResponse.BodyWriter`akımın `HttpResponse.Body` bir analogudur.</span><span class="sxs-lookup"><span data-stu-id="8f98c-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="8f98c-312">IIS'de geliştirilmiş hata raporlaması</span><span class="sxs-lookup"><span data-stu-id="8f98c-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="8f98c-313">IIS'de ASP.NET Core uygulamalarını barındırırken ki başlangıç hataları artık daha zengin tanılama verileri üretir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="8f98c-314">Bu hatalar, uygun olan her yerde yığın izleriyle Birlikte Windows Olay Günlüğü'ne bildirilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="8f98c-315">Ayrıca, tüm uyarılar, hatalar ve işlenmemiş özel durumlar Windows Olay Günlüğü'ne günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="8f98c-316">İşçi Hizmeti ve İşçi SDK</span><span class="sxs-lookup"><span data-stu-id="8f98c-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="8f98c-317">.NET Core 3.0 yeni İşçi Hizmeti uygulaması şablonu tanıttı.</span><span class="sxs-lookup"><span data-stu-id="8f98c-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="8f98c-318">Bu şablon, .NET Core'da uzun süre çalışan hizmetleri yazmak için bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="8f98c-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="8f98c-319">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="8f98c-319">For more information, see:</span></span>

* [<span data-ttu-id="8f98c-320">.NET Çekirdek İşçiler Windows Hizmetleri olarak</span><span class="sxs-lookup"><span data-stu-id="8f98c-320">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="8f98c-321">İleşli Üstbilgi Ara ware geliştirmeleri</span><span class="sxs-lookup"><span data-stu-id="8f98c-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="8f98c-322">ASP.NET Core'un önceki <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> sürümlerinde, bir Azure Linux'a veya IIS dışındaki herhangi bir ters proxy'nin arkasına dağıtıldığında arama yapmak ve <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> sorunludur.</span><span class="sxs-lookup"><span data-stu-id="8f98c-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="8f98c-323">Önceki sürümleri için düzeltme [İleri Linux ve Non-IIS ters yakınlıklar için düzeni](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)belgelenmiştir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="8f98c-324">Bu senaryo Core 3.0 ASP.NET düzeltilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="8f98c-325">Ana bilgisayar, [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` ortam değişkeni `true`.</span><span class="sxs-lookup"><span data-stu-id="8f98c-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="8f98c-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`bizim konteyner `true` görüntüleri ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8f98c-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="8f98c-327">Performans iyileştirmeleri</span><span class="sxs-lookup"><span data-stu-id="8f98c-327">Performance improvements</span></span>

<span data-ttu-id="8f98c-328">ASP.NET Core 3.0 bellek kullanımını azaltmak ve iş verimi geliştirmek birçok iyileştirmeler içerir:</span><span class="sxs-lookup"><span data-stu-id="8f98c-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="8f98c-329">Kapsamlı hizmetler için yerleşik bağımlılık enjeksiyon konteyneri kullanırken bellek kullanımında azalma.</span><span class="sxs-lookup"><span data-stu-id="8f98c-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="8f98c-330">Ara yazılım senaryoları ve yönlendirme de dahil olmak üzere çerçeve genelinde ayırmalarda azalma.</span><span class="sxs-lookup"><span data-stu-id="8f98c-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="8f98c-331">WebSocket bağlantıları için bellek kullanımında azalma.</span><span class="sxs-lookup"><span data-stu-id="8f98c-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="8f98c-332">HTTPS bağlantıları için bellek azaltma ve iş geliştirmeleri.</span><span class="sxs-lookup"><span data-stu-id="8f98c-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="8f98c-333">Yeni optimize edilmiş ve tamamen asynchronous JSON serializer.</span><span class="sxs-lookup"><span data-stu-id="8f98c-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="8f98c-334">Form ayrıştırma bellek kullanımı ve iş geliştirmeleri azalma.</span><span class="sxs-lookup"><span data-stu-id="8f98c-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="8f98c-335">ASP.NET Core 3.0 yalnızca .NET Core 3.0 üzerinde çalışır</span><span class="sxs-lookup"><span data-stu-id="8f98c-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="8f98c-336">Core 3.0ASP.NET itibariyle .NET Framework artık desteklenen bir hedef çerçevesi değildir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="8f98c-337">.NET Framework'u hedefleyen projeler [.NET Core 2.1 LTS sürümü](https://dotnet.microsoft.com/download/dotnet-core/2.1)kullanılarak tam destekli bir şekilde devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="8f98c-338">Core 2.1.x ile ilgili paketlerin çoğu ASP.NET .NET Core 2.1 için üç yıllık LTS döneminin ötesinde süresiz olarak desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="8f98c-339">Geçiş bilgileri için [kodunuzun .NET Framework'den .NET Core'a geçişine](/dotnet/core/porting/)bakın.</span><span class="sxs-lookup"><span data-stu-id="8f98c-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="8f98c-340">ASP.NET Core paylaşılan çerçevesini kullanma</span><span class="sxs-lookup"><span data-stu-id="8f98c-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="8f98c-341">ASP.NET Core 3.0 paylaşılan çerçeve, [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)bulunan, `<PackageReference />` artık proje dosyasında açık bir öğe gerektirir.</span><span class="sxs-lookup"><span data-stu-id="8f98c-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="8f98c-342">Paylaşılan çerçeve, proje dosyasında `Microsoft.NET.Sdk.Web` SDK kullanırken otomatik olarak başvurulur:</span><span class="sxs-lookup"><span data-stu-id="8f98c-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="8f98c-343">ASP.NET Core paylaşılan çerçevesinden kaldırılan derlemeler</span><span class="sxs-lookup"><span data-stu-id="8f98c-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="8f98c-344">Core 3.0 paylaşılan ASP.NET'nden kaldırılan en önemli derlemeler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="8f98c-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="8f98c-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span><span class="sxs-lookup"><span data-stu-id="8f98c-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="8f98c-346">Core 3.0'ASP.NET Json.NET eklemek için Bkz. [Newtonsoft.Json tabanlı JSON format desteği ekle.](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)</span><span class="sxs-lookup"><span data-stu-id="8f98c-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="8f98c-347">ASP.NET Core 3.0 `System.Text.Json` okuma ve JSON yazmak için tanıttı.</span><span class="sxs-lookup"><span data-stu-id="8f98c-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="8f98c-348">Daha fazla bilgi için bu belgede [Yeni JSON serileştirme](#new-json-serialization) bakın.</span><span class="sxs-lookup"><span data-stu-id="8f98c-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="8f98c-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8f98c-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="8f98c-350">Paylaşılan çerçeveden kaldırılan derlemelerin tam listesi için [bkz.](https://github.com/dotnet/AspNetCore/issues/3755)</span><span class="sxs-lookup"><span data-stu-id="8f98c-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="8f98c-351">Bu değişikliğin motivasyonu hakkında daha fazla bilgi için, [3.0'da Microsoft.AspNetCore.App'teki Breaking değişiklikleri](https://github.com/aspnet/Announcements/issues/325) ve [Core 3.0'ASP.NET gelen değişikliklere ilk bakışta](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/)bakın.</span><span class="sxs-lookup"><span data-stu-id="8f98c-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 