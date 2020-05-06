---
title: ASP.NET Core için ' de SignalR MessagePack hub protokolünü kullanın
author: bradygaster
description: ASP.NET Core SignalRIçin MessagePack hub protokolünü ekleyin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 8db7598d978848f13bf5b21a873340b38154e9a8
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777208"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="edd98-103">ASP.NET Core için ' de SignalR MessagePack hub protokolünü kullanın</span><span class="sxs-lookup"><span data-stu-id="edd98-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="edd98-104">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="edd98-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="edd98-105">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="edd98-105">What is MessagePack?</span></span>

<span data-ttu-id="edd98-106">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="edd98-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="edd98-107">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="edd98-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="edd98-108">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="edd98-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="edd98-109">, MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="edd98-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="edd98-110">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="edd98-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="edd98-111">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="edd98-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="edd98-112">`Startup.ConfigureServices` Yönteminde, sunucuda MessagePack `AddMessagePackProtocol` desteğini etkinleştirmek `AddSignalR` için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="edd98-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="edd98-113">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="edd98-113">JSON is enabled by default.</span></span> <span data-ttu-id="edd98-114">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="edd98-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="edd98-115">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="edd98-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="edd98-116">Bu temsilcisinde, `SerializerOptions` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="edd98-117">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="edd98-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="edd98-118">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="edd98-119">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="edd98-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="edd98-120">Örneğin, öğesini değiştirirken `.WithSecurity(MessagePackSecurity.UntrustedData)` çağırma `SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="edd98-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="edd98-121">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="edd98-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="edd98-122">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="edd98-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="edd98-123">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="edd98-124">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="edd98-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="edd98-125">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="edd98-125">.NET client</span></span>

<span data-ttu-id="edd98-126">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip üzerine `AddMessagePackProtocol` `HubConnectionBuilder`çağırın.</span><span class="sxs-lookup"><span data-stu-id="edd98-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="edd98-127">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="edd98-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="edd98-128">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="edd98-128">JavaScript client</span></span>

<span data-ttu-id="edd98-129">JavaScript istemcisi için MessagePack desteği [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="edd98-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="edd98-130">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="edd98-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="edd98-131">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="edd98-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="edd98-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="edd98-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="edd98-133">Bir tarayıcıda, `msgpack5` kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="edd98-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="edd98-134">Başvuru oluşturmak `<script>` için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="edd98-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="edd98-135">Kitaplık, *node_modules \msgpack5\dist\msgpack5,JS*konumunda bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="edd98-136">`<script>` Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="edd98-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="edd98-137">*SignalR-Protocol-msgpack. js* ' *msgpack5. js*' den önce başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="edd98-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="edd98-138">*SignalR. js* , *SignalR-Protocol-msgpack. js*' den önce de gereklidir.</span><span class="sxs-lookup"><span data-stu-id="edd98-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="edd98-139">Uygulamasına `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` eklemek, Istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="edd98-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="edd98-140">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="edd98-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="edd98-141">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="edd98-141">MessagePack quirks</span></span>

<span data-ttu-id="edd98-142">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="edd98-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="edd98-143">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="edd98-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="edd98-144">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="edd98-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="edd98-145">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="edd98-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="edd98-146">JavaScript istemcisinden gönderirken, büyük/küçük harf C# sınıfıyla `PascalCased` tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="edd98-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="edd98-147">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="edd98-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="edd98-148">Adların `camelCased` kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="edd98-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="edd98-149">MessagePack özelliği için farklı bir ad belirtmek `Key` üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="edd98-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="edd98-150">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="edd98-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="edd98-151">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="edd98-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="edd98-152">MessagePack protokolü, öğesinin `Kind` değerini kodlamak için bir yol sağlamaz. `DateTime`</span><span class="sxs-lookup"><span data-stu-id="edd98-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="edd98-153">Sonuç olarak, bir tarihin serisi kaldırılırken MessagePack hub Protokolü UTC biçimine `DateTime.Kind` dönüştürülür, `DateTimeKind.Local` Aksi takdirde, zaman içinde dokunmaz ve olduğu gibi geçireceğiz.</span><span class="sxs-lookup"><span data-stu-id="edd98-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="edd98-154">`DateTime` Değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="edd98-155">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="edd98-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="edd98-156">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="edd98-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="edd98-157">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) kitaplığı MessagePack içindeki `timestamp96` türü desteklemiyor. SignalR</span><span class="sxs-lookup"><span data-stu-id="edd98-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="edd98-158">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="edd98-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="edd98-159">`DateTime.MinValue` Değeri `January 1, 0001`, bir `timestamp96` değerde kodlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="edd98-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="edd98-160">Bu nedenle, JavaScript istemcisine `DateTime.MinValue` gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="edd98-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="edd98-161">`DateTime.MinValue` JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="edd98-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="edd98-162">Genellikle, `DateTime.MinValue` "eksik" veya `null` değeri kodlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="edd98-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="edd98-163">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir `DateTime` bir değer (`DateTime?`) kullanın veya tarihin mevcut olup olmadığını belirten `bool` ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="edd98-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="edd98-164">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="edd98-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="edd98-165">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="edd98-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="edd98-166">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="edd98-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="edd98-167">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="edd98-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="edd98-168">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="edd98-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="edd98-169">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="edd98-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="edd98-170">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="edd98-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="edd98-171">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="edd98-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="edd98-172">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="edd98-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="edd98-173">Örneğin, gelen nesnenin bir sayı olan (`{ foo: 42 }`) bir özellik değeri varsa, ancak .net sınıfındaki özelliği tür `string`ise, değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="edd98-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="edd98-174">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="edd98-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="edd98-175">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="edd98-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="edd98-176">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="edd98-176">Related resources</span></span>

* [<span data-ttu-id="edd98-177">Kullanmaya başlayın</span><span class="sxs-lookup"><span data-stu-id="edd98-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="edd98-178">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="edd98-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="edd98-179">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="edd98-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="edd98-180">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="edd98-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="edd98-181">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="edd98-181">What is MessagePack?</span></span>

<span data-ttu-id="edd98-182">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="edd98-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="edd98-183">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="edd98-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="edd98-184">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="edd98-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="edd98-185">, MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="edd98-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="edd98-186">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="edd98-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="edd98-187">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="edd98-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="edd98-188">`Startup.ConfigureServices` Yönteminde, sunucuda MessagePack `AddMessagePackProtocol` desteğini etkinleştirmek `AddSignalR` için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="edd98-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="edd98-189">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="edd98-189">JSON is enabled by default.</span></span> <span data-ttu-id="edd98-190">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="edd98-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="edd98-191">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="edd98-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="edd98-192">Bu temsilcisinde, `FormatterResolvers` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="edd98-193">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="edd98-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="edd98-194">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="edd98-195">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="edd98-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="edd98-196">Örneğin, `MessagePackSecurity.Active` statik özelliği olarak `MessagePackSecurity.UntrustedData`ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="edd98-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="edd98-197">Öğesinin ayarlanması `MessagePackSecurity.Active` Için [MessagePack 'in bir 1.9. x sürümünün](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="edd98-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="edd98-198">Sürümün `MessagePack` SignalR kullandığı 1.9. x yükseltmeleri yükleniyor.</span><span class="sxs-lookup"><span data-stu-id="edd98-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="edd98-199">`MessagePackSecurity.Active` , Olarak `MessagePackSecurity.UntrustedData`ayarlanmamışsa kötü amaçlı bir istemci hizmet reddine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="edd98-200">Aşağıdaki `MessagePackSecurity.Active` kodda `Program.Main`gösterildiği gibi içinde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="edd98-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="edd98-201">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="edd98-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="edd98-202">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="edd98-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="edd98-203">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="edd98-204">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="edd98-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="edd98-205">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="edd98-205">.NET client</span></span>

<span data-ttu-id="edd98-206">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip üzerine `AddMessagePackProtocol` `HubConnectionBuilder`çağırın.</span><span class="sxs-lookup"><span data-stu-id="edd98-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="edd98-207">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="edd98-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="edd98-208">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="edd98-208">JavaScript client</span></span>

<span data-ttu-id="edd98-209">JavaScript istemcisi için MessagePack desteği [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="edd98-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="edd98-210">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="edd98-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="edd98-211">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="edd98-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="edd98-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="edd98-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="edd98-213">Bir tarayıcıda, `msgpack5` kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="edd98-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="edd98-214">Başvuru oluşturmak `<script>` için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="edd98-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="edd98-215">Kitaplık, *node_modules \msgpack5\dist\msgpack5,JS*konumunda bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="edd98-216">`<script>` Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="edd98-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="edd98-217">*SignalR-Protocol-msgpack. js* ' *msgpack5. js*' den önce başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="edd98-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="edd98-218">*SignalR. js* , *SignalR-Protocol-msgpack. js*' den önce de gereklidir.</span><span class="sxs-lookup"><span data-stu-id="edd98-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="edd98-219">Uygulamasına `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` eklemek, Istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="edd98-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="edd98-220">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="edd98-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="edd98-221">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="edd98-221">MessagePack quirks</span></span>

<span data-ttu-id="edd98-222">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="edd98-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="edd98-223">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="edd98-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="edd98-224">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="edd98-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="edd98-225">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="edd98-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="edd98-226">JavaScript istemcisinden gönderirken, büyük/küçük harf C# sınıfıyla `PascalCased` tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="edd98-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="edd98-227">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="edd98-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="edd98-228">Adların `camelCased` kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="edd98-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="edd98-229">MessagePack özelliği için farklı bir ad belirtmek `Key` üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="edd98-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="edd98-230">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="edd98-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="edd98-231">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="edd98-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="edd98-232">MessagePack protokolü, öğesinin `Kind` değerini kodlamak için bir yol sağlamaz. `DateTime`</span><span class="sxs-lookup"><span data-stu-id="edd98-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="edd98-233">Sonuç olarak, bir tarih serisi kaldırılırken, MessagePack hub protokolü gelen tarihin UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="edd98-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="edd98-234">Yerel saat içinde `DateTime` değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="edd98-235">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="edd98-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="edd98-236">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="edd98-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="edd98-237">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="edd98-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="edd98-238">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) kitaplığı MessagePack içindeki `timestamp96` türü desteklemiyor. SignalR</span><span class="sxs-lookup"><span data-stu-id="edd98-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="edd98-239">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="edd98-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="edd98-240">`DateTime.MinValue` Değeri `January 1, 0001`, bir `timestamp96` değerde kodlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="edd98-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="edd98-241">Bu nedenle, JavaScript istemcisine `DateTime.MinValue` gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="edd98-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="edd98-242">`DateTime.MinValue` JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="edd98-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="edd98-243">Genellikle, `DateTime.MinValue` "eksik" veya `null` değeri kodlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="edd98-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="edd98-244">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir `DateTime` bir değer (`DateTime?`) kullanın veya tarihin mevcut olup olmadığını belirten `bool` ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="edd98-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="edd98-245">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="edd98-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="edd98-246">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="edd98-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="edd98-247">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="edd98-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="edd98-248">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="edd98-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="edd98-249">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="edd98-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="edd98-250">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="edd98-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="edd98-251">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="edd98-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="edd98-252">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="edd98-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="edd98-253">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="edd98-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="edd98-254">Örneğin, gelen nesnenin bir sayı olan (`{ foo: 42 }`) bir özellik değeri varsa, ancak .net sınıfındaki özelliği tür `string`ise, değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="edd98-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="edd98-255">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="edd98-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="edd98-256">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="edd98-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="edd98-257">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="edd98-257">Related resources</span></span>

* [<span data-ttu-id="edd98-258">Kullanmaya başlayın</span><span class="sxs-lookup"><span data-stu-id="edd98-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="edd98-259">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="edd98-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="edd98-260">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="edd98-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="edd98-261">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="edd98-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="edd98-262">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="edd98-262">What is MessagePack?</span></span>

<span data-ttu-id="edd98-263">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="edd98-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="edd98-264">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="edd98-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="edd98-265">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="edd98-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="edd98-266">, MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="edd98-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="edd98-267">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="edd98-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="edd98-268">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="edd98-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="edd98-269">`Startup.ConfigureServices` Yönteminde, sunucuda MessagePack `AddMessagePackProtocol` desteğini etkinleştirmek `AddSignalR` için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="edd98-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="edd98-270">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="edd98-270">JSON is enabled by default.</span></span> <span data-ttu-id="edd98-271">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="edd98-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="edd98-272">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="edd98-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="edd98-273">Bu temsilcisinde, `FormatterResolvers` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="edd98-274">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="edd98-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="edd98-275">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="edd98-276">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="edd98-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="edd98-277">Örneğin, `MessagePackSecurity.Active` statik özelliği olarak `MessagePackSecurity.UntrustedData`ayarlanıyor.</span><span class="sxs-lookup"><span data-stu-id="edd98-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="edd98-278">Öğesinin ayarlanması `MessagePackSecurity.Active` Için [MessagePack 'in bir 1.9. x sürümünün](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="edd98-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="edd98-279">Sürümün `MessagePack` SignalR kullandığı 1.9. x yükseltmeleri yükleniyor.</span><span class="sxs-lookup"><span data-stu-id="edd98-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="edd98-280">`MessagePackSecurity.Active` , Olarak `MessagePackSecurity.UntrustedData`ayarlanmamışsa kötü amaçlı bir istemci hizmet reddine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="edd98-281">Aşağıdaki `MessagePackSecurity.Active` kodda `Program.Main`gösterildiği gibi içinde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="edd98-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="edd98-282">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="edd98-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="edd98-283">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="edd98-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="edd98-284">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="edd98-285">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="edd98-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="edd98-286">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="edd98-286">.NET client</span></span>

<span data-ttu-id="edd98-287">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip üzerine `AddMessagePackProtocol` `HubConnectionBuilder`çağırın.</span><span class="sxs-lookup"><span data-stu-id="edd98-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="edd98-288">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="edd98-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="edd98-289">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="edd98-289">JavaScript client</span></span>

<span data-ttu-id="edd98-290">JavaScript istemcisi için MessagePack desteği [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="edd98-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="edd98-291">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="edd98-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="edd98-292">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="edd98-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="edd98-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="edd98-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="edd98-294">Bir tarayıcıda, `msgpack5` kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="edd98-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="edd98-295">Başvuru oluşturmak `<script>` için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="edd98-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="edd98-296">Kitaplık, *node_modules \msgpack5\dist\msgpack5,JS*konumunda bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="edd98-297">`<script>` Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="edd98-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="edd98-298">*SignalR-Protocol-msgpack. js* ' *msgpack5. js*' den önce başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="edd98-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="edd98-299">*SignalR. js* , *SignalR-Protocol-msgpack. js*' den önce de gereklidir.</span><span class="sxs-lookup"><span data-stu-id="edd98-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="edd98-300">Uygulamasına `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` eklemek, Istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="edd98-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="edd98-301">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="edd98-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="edd98-302">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="edd98-302">MessagePack quirks</span></span>

<span data-ttu-id="edd98-303">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="edd98-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="edd98-304">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="edd98-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="edd98-305">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="edd98-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="edd98-306">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="edd98-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="edd98-307">JavaScript istemcisinden gönderirken, büyük/küçük harf C# sınıfıyla `PascalCased` tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="edd98-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="edd98-308">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="edd98-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="edd98-309">Adların `camelCased` kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="edd98-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="edd98-310">MessagePack özelliği için farklı bir ad belirtmek `Key` üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="edd98-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="edd98-311">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="edd98-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="edd98-312">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="edd98-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="edd98-313">MessagePack protokolü, öğesinin `Kind` değerini kodlamak için bir yol sağlamaz. `DateTime`</span><span class="sxs-lookup"><span data-stu-id="edd98-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="edd98-314">Sonuç olarak, bir tarih serisi kaldırılırken, MessagePack hub protokolü gelen tarihin UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="edd98-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="edd98-315">Yerel saat içinde `DateTime` değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="edd98-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="edd98-316">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="edd98-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="edd98-317">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="edd98-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="edd98-318">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="edd98-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="edd98-319">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) kitaplığı MessagePack içindeki `timestamp96` türü desteklemiyor. SignalR</span><span class="sxs-lookup"><span data-stu-id="edd98-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="edd98-320">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="edd98-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="edd98-321">Değeri `DateTime.MinValue` `January 1, 0001` , bir `timestamp96` değerde kodlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="edd98-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="edd98-322">Bu nedenle, JavaScript istemcisine `DateTime.MinValue` gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="edd98-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="edd98-323">`DateTime.MinValue` JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="edd98-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="edd98-324">Genellikle, `DateTime.MinValue` "eksik" veya `null` değeri kodlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="edd98-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="edd98-325">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir `DateTime` bir değer (`DateTime?`) kullanın veya tarihin mevcut olup olmadığını belirten `bool` ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="edd98-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="edd98-326">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="edd98-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="edd98-327">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="edd98-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="edd98-328">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="edd98-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="edd98-329">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="edd98-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="edd98-330">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="edd98-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="edd98-331">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="edd98-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="edd98-332">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="edd98-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="edd98-333">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="edd98-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="edd98-334">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="edd98-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="edd98-335">Örneğin, gelen nesnenin bir sayı olan (`{ foo: 42 }`) bir özellik değeri varsa, ancak .net sınıfındaki özelliği tür `string`ise, değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="edd98-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="edd98-336">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="edd98-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="edd98-337">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="edd98-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="edd98-338">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="edd98-338">Related resources</span></span>

* [<span data-ttu-id="edd98-339">Kullanmaya başlayın</span><span class="sxs-lookup"><span data-stu-id="edd98-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="edd98-340">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="edd98-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="edd98-341">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="edd98-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
