---
title: ASP.NET Core hub 'ları kullanma SignalR
author: bradygaster
description: ASP.NET Core ' de hub 'ları kullanmayı öğrenin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/hubs
ms.openlocfilehash: 71ca0896bc645b7625f60c3a9e8fe321079d524a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631283"
---
# <a name="use-hubs-in-no-locsignalr-for-aspnet-core"></a>ASP.NET Core için hub 'ları kullanın SignalR

, [Rachel Appel](https://twitter.com/rachelappel) ve [Kevin Griffin](https://twitter.com/1kevgriff) tarafından

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-no-locsignalr-hub"></a>Hub nedir? SignalR

SignalRHub 'lar API 'si, bağlı istemcilerdeki yöntemleri sunucudan çağırmanızı sağlar. Sunucu kodunda, istemci tarafından çağrılan yöntemleri tanımlarsınız. İstemci kodunda, sunucudan çağrılan yöntemleri tanımlarsınız. SignalR gerçek zamanlı istemciden sunucuya ve sunucudan istemciye iletişimleri mümkün kılan arka planda her şeyi ele alır.

## <a name="configure-no-locsignalr-hubs"></a>SignalRHub 'ları yapılandırma

SignalRAra yazılım, çağırarak yapılandırılan bazı hizmetler gerektirir `services.AddSignalR` .

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

SignalRBir ASP.NET Core uygulamasına işlevsellik eklenirken, yöntem geri çağırmada SignalR çağırarak yollar ayarlayın `endpoint.MapHub` `Startup.Configure` `app.UseEndpoints` .

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

SignalRBir ASP.NET Core uygulamasına işlevsellik eklenirken, SignalR yöntemini çağırarak yollar ayarlayın `app.UseSignalR` `Startup.Configure` .

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>Hub 'lar oluşturma ve kullanma

Öğesinden devralan bir sınıf bildirerek bir hub oluşturun `Hub` ve ona ortak yöntemler ekleyin. İstemcileri, olarak tanımlanan yöntemleri çağırabilir `public` .

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Herhangi bir C# yönteminde olduğu gibi, karmaşık türler ve diziler dahil olmak üzere bir dönüş türü ve parametreleri belirtebilirsiniz. SignalR parametrelerinizin ve dönüş değerlerindeki karmaşık nesne ve dizilerin serileştirilmesi ve serisini kaldırma işlemi uygular.

> [!NOTE]
> Hub 'lar geçicidir:
>
> * Durumu hub sınıfının bir özelliğinde depolamayın. Her hub yöntemi çağrısı yeni bir hub örneğinde yürütülür.
> * `await`Hub 'a bağlı olan zaman uyumsuz yöntemleri çağırırken kullanım etkin kalmakta. Örneğin, gibi bir yöntem, `Clients.All.SendAsync(...)` olmadan çağrılırsa `await` ve hub yöntemi bitmeden önce tamamlandığında başarısız olabilir `SendAsync` .

## <a name="the-context-object"></a>Bağlam nesnesi

`Hub`Sınıfı, `Context` bağlantıyla ilgili bilgiler içeren aşağıdaki özellikleri içeren bir özelliğe sahiptir:

| Özellik | Açıklama |
| ------ | ----------- |
| `ConnectionId` | Tarafından atanan bağlantının benzersiz KIMLIĞINI alır SignalR . Her bağlantı için bir bağlantı KIMLIĞI vardır.|
| `UserIdentifier` | [Kullanıcı tanımlayıcısını](xref:signalr/groups)alır. Varsayılan olarak, SignalR `ClaimTypes.NameIdentifier` `ClaimsPrincipal` Kullanıcı tanımlayıcısı olarak bağlantıyla ilişkili öğesinden öğesini kullanır. |
| `User` | `ClaimsPrincipal`Geçerli kullanıcıyla ilişkili öğesini alır. |
| `Items` | Bu bağlantının kapsamındaki verileri paylaşmak için kullanılabilecek bir anahtar/değer koleksiyonu alır. Veriler bu koleksiyonda depolanabilir ve farklı hub yöntemi etkinleştirmeleri arasında bağlantı için kalıcı hale gelir. |
| `Features` | Bağlantıda kullanılabilen özelliklerin koleksiyonunu alır. Şimdilik bu koleksiyon Çoğu senaryoda gerekli değildir, bu nedenle henüz ayrıntılı olarak açıklanmamıştır. |
| `ConnectionAborted` | `CancellationToken`Bağlantı iptal edildiğinde bir bildirim alır. |

`Hub.Context` Aşağıdaki yöntemleri de içerir:

| Yöntem | Açıklama |
| ------ | ----------- |
| `GetHttpContext` | `HttpContext`Bağlantı için veya `null` bağlantı bir HTTP isteğiyle ilişkilendirilmediği takdirde. HTTP bağlantılarında, HTTP üstbilgileri ve sorgu dizeleri gibi bilgileri almak için bu yöntemi kullanabilirsiniz. |
| `Abort` | Bağlantıyı iptal eder. |

## <a name="the-clients-object"></a>Istemciler nesnesi

`Hub`Sınıfı, `Clients` sunucu ve istemci arasındaki iletişim için aşağıdaki özellikleri içeren bir özelliğe sahiptir:

| Özellik | Açıklama |
| ------ | ----------- |
| `All` | Tüm bağlı istemcilerde bir yöntemi çağırır |
| `Caller` | İstemciye, hub yöntemini çağıran bir yöntemi çağırır |
| `Others` | Yöntemi çağıran istemci hariç tüm bağlı istemcilerde bir yöntemi çağırır |

`Hub.Clients` Aşağıdaki yöntemleri de içerir:

| Yöntem | Açıklama |
| ------ | ----------- |
| `AllExcept` | Belirtilen bağlantılar hariç tüm bağlı istemcilerde bir yöntemi çağırır |
| `Client` | Belirli bir bağlı istemcide bir yöntemi çağırır |
| `Clients` | Belirli bağlı istemcilerde bir yöntemi çağırır |
| `Group` | Belirtilen gruptaki tüm bağlantılarda bir yöntemi çağırır  |
| `GroupExcept` | Belirtilen bağlantılar dışında, belirtilen gruptaki tüm bağlantılarda bir yöntemi çağırır |
| `Groups` | Birden çok bağlantı grubunda bir yöntemi çağırır  |
| `OthersInGroup` | Hub yöntemini çağıran istemci hariç bir bağlantı grubu üzerinde bir yöntemi çağırır  |
| `User` | Belirli bir kullanıcıyla ilişkili tüm bağlantılarda bir yöntemi çağırır |
| `Users` | Belirtilen kullanıcılarla ilişkili tüm bağlantılarda bir yöntemi çağırır |

Yukarıdaki tablolardaki her bir özellik veya yöntem bir yöntemine sahip bir nesne döndürür `SendAsync` . `SendAsync`Yöntemi, çağrılacak istemci yönteminin adını ve parametrelerini girmenize olanak sağlar.

## <a name="send-messages-to-clients"></a>İstemcilere ileti gönderme

Belirli istemcilere çağrı yapmak için nesnesinin özelliklerini kullanın `Clients` . Aşağıdaki örnekte, üç hub yöntemi vardır:

* `SendMessage` kullanarak tüm bağlı istemcilere bir ileti gönderir `Clients.All` .
* `SendMessageToCaller` kullanarak, çağırana geri bir ileti gönderir `Clients.Caller` .
* `SendMessageToGroups` gruptaki tüm istemcilere bir ileti gönderir `SignalR Users` .

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Türü kesin belirlenmiş hub 'lar

Kullanmanın bir dezavantajı, `SendAsync` çağrılacak istemci yöntemini belirtmek için bir sihirli dize kullanır. Bu, yöntem adı yanlış yazıldığında veya istemcide eksikse kodu, çalışma zamanı hatalarına açık bırakır.

Kullanmanın alternatifi `SendAsync` , ile kesin bir şekilde yazmak için `Hub` kullanılır <xref:Microsoft.AspNetCore.SignalR.Hub%601> . Aşağıdaki örnekte, `ChatHub` istemci yöntemleri adlı bir arabirimde çıkarılmıştır `IChatClient` .

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Bu arabirim, önceki örneği yeniden düzenleme için kullanılabilir `ChatHub` .

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

Kullanımı `Hub<IChatClient>` , istemci yöntemlerinin derleme zamanı denetimini sunar. Bu, `Hub<T>` yalnızca arabirimde tanımlanan yöntemlere erişim sağlayabileceğinizden, sihirli dizeler kullanılarak oluşan sorunları önler.

Türü kesin belirlenmiş kullanılması `Hub<T>` , kullanma yeteneğini devre dışı bırakır `SendAsync` . Arabirim üzerinde tanımlanan Yöntemler hala zaman uyumsuz olarak tanımlanabilir. Aslında, bu yöntemlerin her biri bir döndürmelidir `Task` . Bir arabirim olduğundan `async` anahtar sözcüğünü kullanmayın. Örnek:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> `Async`Sonek, yöntem adından çıkarılır. İstemci yönteminiz ile tanımlanmadıysa `.on('MyMethodAsync')` , `MyMethodAsync` ad olarak kullanmamalısınız.

## <a name="change-the-name-of-a-hub-method"></a>Bir hub yönteminin adını değiştirme

Varsayılan olarak, bir sunucu hub 'ı Yöntem adı .NET yönteminin adıdır. Ancak, bu Varsayılanı değiştirmek ve yöntemi için el ile bir ad belirtmek için [Hubmethodname](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) özniteliğini kullanabilirsiniz. İstemci, yöntemi çağırırken .NET Yöntem adı yerine bu adı kullanmalıdır.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Bir bağlantı için olayları işleyin

SignalRHub 'lar API 'si, `OnConnectedAsync` `OnDisconnectedAsync` bağlantıları yönetmek ve izlemek için ve sanal yöntemler sağlar. `OnConnectedAsync`Bir Istemci hub 'a bağlanırken bir gruba ekleme gibi işlemleri gerçekleştirmek için sanal yöntemi geçersiz kılın.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

`OnDisconnectedAsync`Bir istemcinin bağlantısı kesildiğinde işlemleri gerçekleştirmek için sanal yöntemi geçersiz kılın. İstemci kasıtlı olarak bağlantı kesildiğinde ( `connection.stop()` Örneğin, çağırarak), `exception` parametresi olur `null` . Ancak, istemcinin bağlantısı bir hata nedeniyle kesildiyse (örneğin, bir ağ arızası), bu parametre hatayı `exception` açıklayan bir özel durum içerir.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a>Hataları işleme

Hub yöntemleriniz içinde oluşturulan özel durumlar, yöntemi çağıran istemciye gönderilir. JavaScript istemcisinde, `invoke` yöntemi bir [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)döndürür. İstemci, Promise öğesine eklenmiş bir işleyiciyle bir hata aldığında `catch` , çağrılır ve bir JavaScript nesnesi olarak geçirilir `Error` .

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Hub 'ınız bir özel durum oluşturursa, bağlantılar kapanmamıştır. Varsayılan olarak, SignalR istemciye genel bir hata iletisi döndürür. Örnek:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Beklenmeyen özel durumlar genellikle veritabanı bağlantısı başarısız olduğunda tetiklenen bir özel durumda veritabanı sunucusunun adı gibi hassas bilgiler içerir. SignalR Bu ayrıntılı hata iletilerini varsayılan olarak bir güvenlik ölçüsü olarak kullanıma sunmaz. Özel durum ayrıntılarının neden bastırıldığına ilişkin daha fazla bilgi için [güvenlik konuları makalesine](xref:signalr/security#exceptions) bakın.

İstemciye *yaymak istediğiniz olağanüstü* bir koşulunuz varsa, `HubException` sınıfını kullanabilirsiniz. `HubException`Hub yönteinizden bir oluşturduysanız, SignalR **will** tüm iletiyi değiştirilmemiş olarak istemciye gönderir.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR yalnızca `Message` özel durumun özelliğini istemciye gönderir. Özel durumun yığın izlemesi ve diğer özellikleri istemci tarafından kullanılamaz.

## <a name="related-resources"></a>İlgili kaynaklar

* [ASP.NET Core giriş SignalR](xref:signalr/introduction)
* [JavaScript istemcisi](xref:signalr/javascript-client)
* [Azure’da Yayımlama](xref:signalr/publish-to-azure-web-app)
