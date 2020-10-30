---
title: "ASP.NET Core hub 'ları kullanma :::no-loc(SignalR):::"
author: bradygaster
description: "ASP.NET Core ' de hub 'ları kullanmayı öğrenin :::no-loc(SignalR)::: ."
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/hubs
ms.openlocfilehash: 4a31c16eb44e2244574d0df49c30e7a44b2bba6e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050946"
---
# <a name="use-hubs-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="435de-103">ASP.NET Core için hub 'ları kullanın :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="435de-103">Use hubs in :::no-loc(SignalR)::: for ASP.NET Core</span></span>

<span data-ttu-id="435de-104">, [Rachel Appel](https://twitter.com/rachelappel) ve [Kevin Griffin](https://twitter.com/1kevgriff) tarafından</span><span class="sxs-lookup"><span data-stu-id="435de-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="435de-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="435de-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-no-locsignalr-hub"></a><span data-ttu-id="435de-106">Hub nedir? :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="435de-106">What is a :::no-loc(SignalR)::: hub</span></span>

<span data-ttu-id="435de-107">:::no-loc(SignalR):::Hub 'lar API 'si, bağlı istemcilerdeki yöntemleri sunucudan çağırmanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="435de-107">The :::no-loc(SignalR)::: Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="435de-108">Sunucu kodunda, istemci tarafından çağrılan yöntemleri tanımlarsınız.</span><span class="sxs-lookup"><span data-stu-id="435de-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="435de-109">İstemci kodunda, sunucudan çağrılan yöntemleri tanımlarsınız.</span><span class="sxs-lookup"><span data-stu-id="435de-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="435de-110">:::no-loc(SignalR)::: gerçek zamanlı istemciden sunucuya ve sunucudan istemciye iletişimleri mümkün kılan arka planda her şeyi ele alır.</span><span class="sxs-lookup"><span data-stu-id="435de-110">:::no-loc(SignalR)::: takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-no-locsignalr-hubs"></a><span data-ttu-id="435de-111">:::no-loc(SignalR):::Hub 'ları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="435de-111">Configure :::no-loc(SignalR)::: hubs</span></span>

<span data-ttu-id="435de-112">:::no-loc(SignalR):::Ara yazılım, çağırarak yapılandırılan bazı hizmetler gerektirir `services.Add:::no-loc(SignalR):::` .</span><span class="sxs-lookup"><span data-stu-id="435de-112">The :::no-loc(SignalR)::: middleware requires some services, which are configured by calling `services.Add:::no-loc(SignalR):::`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="435de-113">:::no-loc(SignalR):::Bir ASP.NET Core uygulamasına işlevsellik eklenirken, yöntem geri çağırmada :::no-loc(SignalR)::: çağırarak yollar ayarlayın `endpoint.MapHub` `Startup.Configure` `app.UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="435de-113">When adding :::no-loc(SignalR)::: functionality to an ASP.NET Core app, setup :::no-loc(SignalR)::: routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="435de-114">:::no-loc(SignalR):::Bir ASP.NET Core uygulamasına işlevsellik eklenirken, :::no-loc(SignalR)::: yöntemini çağırarak yollar ayarlayın `app.Use:::no-loc(SignalR):::` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="435de-114">When adding :::no-loc(SignalR)::: functionality to an ASP.NET Core app, setup :::no-loc(SignalR)::: routes by calling `app.Use:::no-loc(SignalR):::` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="435de-115">Hub 'lar oluşturma ve kullanma</span><span class="sxs-lookup"><span data-stu-id="435de-115">Create and use hubs</span></span>

<span data-ttu-id="435de-116">Öğesinden devralan bir sınıf bildirerek bir hub oluşturun `Hub` ve ona ortak yöntemler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="435de-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="435de-117">İstemcileri, olarak tanımlanan yöntemleri çağırabilir `public` .</span><span class="sxs-lookup"><span data-stu-id="435de-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="435de-118">Herhangi bir C# yönteminde olduğu gibi, karmaşık türler ve diziler dahil olmak üzere bir dönüş türü ve parametreleri belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="435de-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="435de-119">:::no-loc(SignalR)::: parametrelerinizin ve dönüş değerlerindeki karmaşık nesne ve dizilerin serileştirilmesi ve serisini kaldırma işlemi uygular.</span><span class="sxs-lookup"><span data-stu-id="435de-119">:::no-loc(SignalR)::: handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="435de-120">Hub 'lar geçicidir:</span><span class="sxs-lookup"><span data-stu-id="435de-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="435de-121">Durumu hub sınıfının bir özelliğinde depolamayın.</span><span class="sxs-lookup"><span data-stu-id="435de-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="435de-122">Her hub yöntemi çağrısı yeni bir hub örneğinde yürütülür.</span><span class="sxs-lookup"><span data-stu-id="435de-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="435de-123">`await`Hub 'a bağlı olan zaman uyumsuz yöntemleri çağırırken kullanım etkin kalmakta.</span><span class="sxs-lookup"><span data-stu-id="435de-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="435de-124">Örneğin, gibi bir yöntem, `Clients.All.SendAsync(...)` olmadan çağrılırsa `await` ve hub yöntemi bitmeden önce tamamlandığında başarısız olabilir `SendAsync` .</span><span class="sxs-lookup"><span data-stu-id="435de-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="435de-125">Bağlam nesnesi</span><span class="sxs-lookup"><span data-stu-id="435de-125">The Context object</span></span>

<span data-ttu-id="435de-126">`Hub`Sınıfı, `Context` bağlantıyla ilgili bilgiler içeren aşağıdaki özellikleri içeren bir özelliğe sahiptir:</span><span class="sxs-lookup"><span data-stu-id="435de-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="435de-127">Özellik</span><span class="sxs-lookup"><span data-stu-id="435de-127">Property</span></span> | <span data-ttu-id="435de-128">Açıklama</span><span class="sxs-lookup"><span data-stu-id="435de-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="435de-129">Tarafından atanan bağlantının benzersiz KIMLIĞINI alır :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="435de-129">Gets the unique ID for the connection, assigned by :::no-loc(SignalR):::.</span></span> <span data-ttu-id="435de-130">Her bağlantı için bir bağlantı KIMLIĞI vardır.</span><span class="sxs-lookup"><span data-stu-id="435de-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="435de-131">[Kullanıcı tanımlayıcısını](xref:signalr/groups)alır.</span><span class="sxs-lookup"><span data-stu-id="435de-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="435de-132">Varsayılan olarak, :::no-loc(SignalR)::: `ClaimTypes.NameIdentifier` `ClaimsPrincipal` Kullanıcı tanımlayıcısı olarak bağlantıyla ilişkili öğesinden öğesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="435de-132">By default, :::no-loc(SignalR)::: uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="435de-133">`ClaimsPrincipal`Geçerli kullanıcıyla ilişkili öğesini alır.</span><span class="sxs-lookup"><span data-stu-id="435de-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="435de-134">Bu bağlantının kapsamındaki verileri paylaşmak için kullanılabilecek bir anahtar/değer koleksiyonu alır.</span><span class="sxs-lookup"><span data-stu-id="435de-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="435de-135">Veriler bu koleksiyonda depolanabilir ve farklı hub yöntemi etkinleştirmeleri arasında bağlantı için kalıcı hale gelir.</span><span class="sxs-lookup"><span data-stu-id="435de-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="435de-136">Bağlantıda kullanılabilen özelliklerin koleksiyonunu alır.</span><span class="sxs-lookup"><span data-stu-id="435de-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="435de-137">Şimdilik bu koleksiyon Çoğu senaryoda gerekli değildir, bu nedenle henüz ayrıntılı olarak açıklanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="435de-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="435de-138">`CancellationToken`Bağlantı iptal edildiğinde bir bildirim alır.</span><span class="sxs-lookup"><span data-stu-id="435de-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="435de-139">`Hub.Context` Aşağıdaki yöntemleri de içerir:</span><span class="sxs-lookup"><span data-stu-id="435de-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="435de-140">Yöntem</span><span class="sxs-lookup"><span data-stu-id="435de-140">Method</span></span> | <span data-ttu-id="435de-141">Açıklama</span><span class="sxs-lookup"><span data-stu-id="435de-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="435de-142">`HttpContext`Bağlantı için veya `null` bağlantı bir HTTP isteğiyle ilişkilendirilmediği takdirde.</span><span class="sxs-lookup"><span data-stu-id="435de-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="435de-143">HTTP bağlantılarında, HTTP üstbilgileri ve sorgu dizeleri gibi bilgileri almak için bu yöntemi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="435de-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="435de-144">Bağlantıyı iptal eder.</span><span class="sxs-lookup"><span data-stu-id="435de-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="435de-145">Istemciler nesnesi</span><span class="sxs-lookup"><span data-stu-id="435de-145">The Clients object</span></span>

<span data-ttu-id="435de-146">`Hub`Sınıfı, `Clients` sunucu ve istemci arasındaki iletişim için aşağıdaki özellikleri içeren bir özelliğe sahiptir:</span><span class="sxs-lookup"><span data-stu-id="435de-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="435de-147">Özellik</span><span class="sxs-lookup"><span data-stu-id="435de-147">Property</span></span> | <span data-ttu-id="435de-148">Açıklama</span><span class="sxs-lookup"><span data-stu-id="435de-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="435de-149">Tüm bağlı istemcilerde bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="435de-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="435de-150">İstemciye, hub yöntemini çağıran bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="435de-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="435de-151">Yöntemi çağıran istemci hariç tüm bağlı istemcilerde bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="435de-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="435de-152">`Hub.Clients` Aşağıdaki yöntemleri de içerir:</span><span class="sxs-lookup"><span data-stu-id="435de-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="435de-153">Yöntem</span><span class="sxs-lookup"><span data-stu-id="435de-153">Method</span></span> | <span data-ttu-id="435de-154">Açıklama</span><span class="sxs-lookup"><span data-stu-id="435de-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="435de-155">Belirtilen bağlantılar hariç tüm bağlı istemcilerde bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="435de-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="435de-156">Belirli bir bağlı istemcide bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="435de-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="435de-157">Belirli bağlı istemcilerde bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="435de-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="435de-158">Belirtilen gruptaki tüm bağlantılarda bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="435de-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="435de-159">Belirtilen bağlantılar dışında, belirtilen gruptaki tüm bağlantılarda bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="435de-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="435de-160">Birden çok bağlantı grubunda bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="435de-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="435de-161">Hub yöntemini çağıran istemci hariç bir bağlantı grubu üzerinde bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="435de-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="435de-162">Belirli bir kullanıcıyla ilişkili tüm bağlantılarda bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="435de-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="435de-163">Belirtilen kullanıcılarla ilişkili tüm bağlantılarda bir yöntemi çağırır</span><span class="sxs-lookup"><span data-stu-id="435de-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="435de-164">Yukarıdaki tablolardaki her bir özellik veya yöntem bir yöntemine sahip bir nesne döndürür `SendAsync` .</span><span class="sxs-lookup"><span data-stu-id="435de-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="435de-165">`SendAsync`Yöntemi, çağrılacak istemci yönteminin adını ve parametrelerini girmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="435de-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="435de-166">İstemcilere ileti gönderme</span><span class="sxs-lookup"><span data-stu-id="435de-166">Send messages to clients</span></span>

<span data-ttu-id="435de-167">Belirli istemcilere çağrı yapmak için nesnesinin özelliklerini kullanın `Clients` .</span><span class="sxs-lookup"><span data-stu-id="435de-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="435de-168">Aşağıdaki örnekte, üç hub yöntemi vardır:</span><span class="sxs-lookup"><span data-stu-id="435de-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="435de-169">`SendMessage` kullanarak tüm bağlı istemcilere bir ileti gönderir `Clients.All` .</span><span class="sxs-lookup"><span data-stu-id="435de-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="435de-170">`SendMessageToCaller` kullanarak, çağırana geri bir ileti gönderir `Clients.Caller` .</span><span class="sxs-lookup"><span data-stu-id="435de-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="435de-171">`SendMessageToGroups` gruptaki tüm istemcilere bir ileti gönderir `:::no-loc(SignalR)::: Users` .</span><span class="sxs-lookup"><span data-stu-id="435de-171">`SendMessageToGroups` sends a message to all clients in the `:::no-loc(SignalR)::: Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="435de-172">Türü kesin belirlenmiş hub 'lar</span><span class="sxs-lookup"><span data-stu-id="435de-172">Strongly typed hubs</span></span>

<span data-ttu-id="435de-173">Kullanmanın bir dezavantajı, `SendAsync` çağrılacak istemci yöntemini belirtmek için bir sihirli dize kullanır.</span><span class="sxs-lookup"><span data-stu-id="435de-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="435de-174">Bu, yöntem adı yanlış yazıldığında veya istemcide eksikse kodu, çalışma zamanı hatalarına açık bırakır.</span><span class="sxs-lookup"><span data-stu-id="435de-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="435de-175">Kullanmanın alternatifi `SendAsync` , ile kesin bir şekilde yazmak için `Hub` kullanılır <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub%601> .</span><span class="sxs-lookup"><span data-stu-id="435de-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Hub%601>.</span></span> <span data-ttu-id="435de-176">Aşağıdaki örnekte, `ChatHub` istemci yöntemleri adlı bir arabirimde çıkarılmıştır `IChatClient` .</span><span class="sxs-lookup"><span data-stu-id="435de-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="435de-177">Bu arabirim, önceki örneği yeniden düzenleme için kullanılabilir `ChatHub` .</span><span class="sxs-lookup"><span data-stu-id="435de-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="435de-178">Kullanımı `Hub<IChatClient>` , istemci yöntemlerinin derleme zamanı denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="435de-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="435de-179">Bu, `Hub<T>` yalnızca arabirimde tanımlanan yöntemlere erişim sağlayabileceğinizden, sihirli dizeler kullanılarak oluşan sorunları önler.</span><span class="sxs-lookup"><span data-stu-id="435de-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="435de-180">Türü kesin belirlenmiş kullanılması `Hub<T>` , kullanma yeteneğini devre dışı bırakır `SendAsync` .</span><span class="sxs-lookup"><span data-stu-id="435de-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="435de-181">Arabirim üzerinde tanımlanan Yöntemler hala zaman uyumsuz olarak tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="435de-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="435de-182">Aslında, bu yöntemlerin her biri bir döndürmelidir `Task` .</span><span class="sxs-lookup"><span data-stu-id="435de-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="435de-183">Bir arabirim olduğundan `async` anahtar sözcüğünü kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="435de-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="435de-184">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="435de-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="435de-185">`Async`Sonek, yöntem adından çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="435de-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="435de-186">İstemci yönteminiz ile tanımlanmadıysa `.on('MyMethodAsync')` , `MyMethodAsync` ad olarak kullanmamalısınız.</span><span class="sxs-lookup"><span data-stu-id="435de-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="435de-187">Bir hub yönteminin adını değiştirme</span><span class="sxs-lookup"><span data-stu-id="435de-187">Change the name of a hub method</span></span>

<span data-ttu-id="435de-188">Varsayılan olarak, bir sunucu hub 'ı Yöntem adı .NET yönteminin adıdır.</span><span class="sxs-lookup"><span data-stu-id="435de-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="435de-189">Ancak, bu Varsayılanı değiştirmek ve yöntemi için el ile bir ad belirtmek için [Hubmethodname](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubMethodNameAttribute) özniteliğini kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="435de-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="435de-190">İstemci, yöntemi çağırırken .NET Yöntem adı yerine bu adı kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="435de-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="435de-191">Bir bağlantı için olayları işleyin</span><span class="sxs-lookup"><span data-stu-id="435de-191">Handle events for a connection</span></span>

<span data-ttu-id="435de-192">:::no-loc(SignalR):::Hub 'lar API 'si, `OnConnectedAsync` `OnDisconnectedAsync` bağlantıları yönetmek ve izlemek için ve sanal yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="435de-192">The :::no-loc(SignalR)::: Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="435de-193">`OnConnectedAsync`Bir Istemci hub 'a bağlanırken bir gruba ekleme gibi işlemleri gerçekleştirmek için sanal yöntemi geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="435de-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="435de-194">`OnDisconnectedAsync`Bir istemcinin bağlantısı kesildiğinde işlemleri gerçekleştirmek için sanal yöntemi geçersiz kılın.</span><span class="sxs-lookup"><span data-stu-id="435de-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="435de-195">İstemci kasıtlı olarak bağlantı kesildiğinde ( `connection.stop()` Örneğin, çağırarak), `exception` parametresi olur `null` .</span><span class="sxs-lookup"><span data-stu-id="435de-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="435de-196">Ancak, istemcinin bağlantısı bir hata nedeniyle kesildiyse (örneğin, bir ağ arızası), bu parametre hatayı `exception` açıklayan bir özel durum içerir.</span><span class="sxs-lookup"><span data-stu-id="435de-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a><span data-ttu-id="435de-197">Hataları işleme</span><span class="sxs-lookup"><span data-stu-id="435de-197">Handle errors</span></span>

<span data-ttu-id="435de-198">Hub yöntemleriniz içinde oluşturulan özel durumlar, yöntemi çağıran istemciye gönderilir.</span><span class="sxs-lookup"><span data-stu-id="435de-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="435de-199">JavaScript istemcisinde, `invoke` yöntemi bir [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)döndürür.</span><span class="sxs-lookup"><span data-stu-id="435de-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="435de-200">İstemci, Promise öğesine eklenmiş bir işleyiciyle bir hata aldığında `catch` , çağrılır ve bir JavaScript nesnesi olarak geçirilir `Error` .</span><span class="sxs-lookup"><span data-stu-id="435de-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="435de-201">Hub 'ınız bir özel durum oluşturursa, bağlantılar kapanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="435de-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="435de-202">Varsayılan olarak, :::no-loc(SignalR)::: istemciye genel bir hata iletisi döndürür.</span><span class="sxs-lookup"><span data-stu-id="435de-202">By default, :::no-loc(SignalR)::: returns a generic error message to the client.</span></span> <span data-ttu-id="435de-203">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="435de-203">For example:</span></span>

```
Microsoft.AspNetCore.:::no-loc(SignalR):::.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="435de-204">Beklenmeyen özel durumlar genellikle veritabanı bağlantısı başarısız olduğunda tetiklenen bir özel durumda veritabanı sunucusunun adı gibi hassas bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="435de-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="435de-205">:::no-loc(SignalR)::: Bu ayrıntılı hata iletilerini varsayılan olarak bir güvenlik ölçüsü olarak kullanıma sunmaz.</span><span class="sxs-lookup"><span data-stu-id="435de-205">:::no-loc(SignalR)::: doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="435de-206">Özel durum ayrıntılarının neden bastırıldığına ilişkin daha fazla bilgi için [güvenlik konuları makalesine](xref:signalr/security#exceptions) bakın.</span><span class="sxs-lookup"><span data-stu-id="435de-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="435de-207">İstemciye *yaymak istediğiniz olağanüstü* bir koşulunuz varsa, `HubException` sınıfını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="435de-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="435de-208">`HubException`Hub yönteinizden bir oluşturduysanız, :::no-loc(SignalR)::: **will** tüm iletiyi değiştirilmemiş olarak istemciye gönderir.</span><span class="sxs-lookup"><span data-stu-id="435de-208">If you throw a `HubException` from your hub method, :::no-loc(SignalR)::: **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> <span data-ttu-id="435de-209">:::no-loc(SignalR)::: yalnızca `Message` özel durumun özelliğini istemciye gönderir.</span><span class="sxs-lookup"><span data-stu-id="435de-209">:::no-loc(SignalR)::: only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="435de-210">Özel durumun yığın izlemesi ve diğer özellikleri istemci tarafından kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="435de-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="435de-211">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="435de-211">Related resources</span></span>

* [<span data-ttu-id="435de-212">ASP.NET Core giriş :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="435de-212">Intro to ASP.NET Core :::no-loc(SignalR):::</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="435de-213">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="435de-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="435de-214">Azure 'da yayımlama</span><span class="sxs-lookup"><span data-stu-id="435de-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
