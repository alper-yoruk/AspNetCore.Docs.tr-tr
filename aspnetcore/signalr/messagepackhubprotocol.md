---
title: ASP.NET Core için ' de MessagePack hub protokolünü kullanın SignalR
author: bradygaster
description: ASP.NET Core için MessagePack hub protokolünü ekleyin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 15ae8911e2ffba43c7bb885efd153d8b6803bc2a
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393684"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="e7d84-103">ASP.NET Core için ' de MessagePack hub protokolünü kullanın SignalR</span><span class="sxs-lookup"><span data-stu-id="e7d84-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e7d84-104">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="e7d84-105">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="e7d84-105">What is MessagePack?</span></span>

<span data-ttu-id="e7d84-106">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="e7d84-107">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="e7d84-108">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="e7d84-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="e7d84-109">SignalR , MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="e7d84-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="e7d84-110">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e7d84-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="e7d84-111">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="e7d84-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="e7d84-112">`Startup.ConfigureServices`Yönteminde, `AddMessagePackProtocol` `AddSignalR` sunucuda MessagePack desteğini etkinleştirmek için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e7d84-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="e7d84-113">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-113">JSON is enabled by default.</span></span> <span data-ttu-id="e7d84-114">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="e7d84-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="e7d84-115">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="e7d84-116">Bu temsilcisinde, `SerializerOptions` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="e7d84-117">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="e7d84-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="e7d84-118">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="e7d84-119">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="e7d84-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="e7d84-120">Örneğin, öğesini `.WithSecurity(MessagePackSecurity.UntrustedData)` değiştirirken çağırma `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="e7d84-121">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e7d84-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="e7d84-122">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="e7d84-123">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="e7d84-124">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="e7d84-125">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="e7d84-125">.NET client</span></span>

<span data-ttu-id="e7d84-126">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip `AddMessagePackProtocol` üzerine çağırın `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="e7d84-127">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="e7d84-128">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="e7d84-128">JavaScript client</span></span>

<span data-ttu-id="e7d84-129">JavaScript istemcisi için MessagePack desteği [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="e7d84-130">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="e7d84-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="e7d84-131">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="e7d84-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="e7d84-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="e7d84-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="e7d84-133">Bir tarayıcıda, `msgpack5` Kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="e7d84-134">`<script>`Başvuru oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="e7d84-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="e7d84-135">Kitaplık *node_modules\msgpack5\dist\msgpack5.js*bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="e7d84-136">`<script>`Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="e7d84-137">*msgpack5.js*önce *signalr-protocol-msgpack.js* başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="e7d84-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="e7d84-138">*signalr-protocol-msgpack.js*önce de *signalr.js* gerekir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="e7d84-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Uygulamasına eklemek, `HubConnectionBuilder` istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="e7d84-140">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="e7d84-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="e7d84-141">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="e7d84-141">MessagePack quirks</span></span>

<span data-ttu-id="e7d84-142">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="e7d84-143">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="e7d84-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="e7d84-144">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="e7d84-145">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="e7d84-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="e7d84-146">JavaScript istemcisinden gönderirken, büyük/ `PascalCased` küçük harf C# sınıfıyla tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="e7d84-147">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e7d84-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="e7d84-148">`camelCased`Adların kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="e7d84-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="e7d84-149">`Key`MessagePack özelliği için farklı bir ad belirtmek üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e7d84-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="e7d84-150">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="e7d84-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="e7d84-151">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="e7d84-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="e7d84-152">MessagePack protokolü, öğesinin değerini kodlamak için bir yol sağlamaz `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="e7d84-153">Sonuç olarak, bir tarihin serisi kaldırılırken MessagePack hub Protokolü UTC biçimine dönüştürülür, `DateTime.Kind` `DateTimeKind.Local` Aksi takdirde, zaman içinde dokunmaz ve olduğu gibi geçireceğiz.</span><span class="sxs-lookup"><span data-stu-id="e7d84-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="e7d84-154">`DateTime`Değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="e7d84-155">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="e7d84-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="e7d84-156">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="e7d84-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="e7d84-157">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) Kitaplığı SignalR `timestamp96` MessagePack içindeki türü desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="e7d84-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="e7d84-158">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="e7d84-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="e7d84-159">Değeri `DateTime.MinValue` `January 1, 0001` , bir değerde kodlanmalıdır `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="e7d84-160">Bu nedenle, `DateTime.MinValue` JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="e7d84-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="e7d84-161">`DateTime.MinValue`JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="e7d84-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="e7d84-162">Genellikle, `DateTime.MinValue` "eksik" veya değeri kodlamak için kullanılır `null` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="e7d84-163">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir bir `DateTime` değer ( `DateTime?` ) kullanın veya `bool` tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="e7d84-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="e7d84-164">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="e7d84-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="e7d84-165">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="e7d84-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="e7d84-166">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="e7d84-167">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="e7d84-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="e7d84-168">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="e7d84-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="e7d84-169">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="e7d84-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="e7d84-170">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="e7d84-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="e7d84-171">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="e7d84-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="e7d84-172">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="e7d84-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="e7d84-173">Örneğin, gelen nesnenin bir sayı olan () bir özellik değeri varsa `{ foo: 42 }` , ancak .net sınıfındaki özelliği tür ise `string` , değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="e7d84-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="e7d84-174">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e7d84-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="e7d84-175">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="e7d84-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="e7d84-176">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e7d84-176">Related resources</span></span>

* [<span data-ttu-id="e7d84-177">Başlarken</span><span class="sxs-lookup"><span data-stu-id="e7d84-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="e7d84-178">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="e7d84-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="e7d84-179">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="e7d84-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="e7d84-180">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="e7d84-181">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="e7d84-181">What is MessagePack?</span></span>

<span data-ttu-id="e7d84-182">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="e7d84-183">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="e7d84-184">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="e7d84-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="e7d84-185">SignalR , MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="e7d84-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="e7d84-186">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e7d84-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="e7d84-187">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="e7d84-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="e7d84-188">`Startup.ConfigureServices`Yönteminde, `AddMessagePackProtocol` `AddSignalR` sunucuda MessagePack desteğini etkinleştirmek için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e7d84-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="e7d84-189">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-189">JSON is enabled by default.</span></span> <span data-ttu-id="e7d84-190">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="e7d84-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="e7d84-191">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="e7d84-192">Bu temsilcisinde, `FormatterResolvers` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="e7d84-193">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="e7d84-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="e7d84-194">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="e7d84-195">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="e7d84-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="e7d84-196">Örneğin, `MessagePackSecurity.Active` statik özelliği olarak ayarlanıyor `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="e7d84-197">Öğesinin ayarlanması için `MessagePackSecurity.Active` [MessagePack 'in bir 1.9. x sürümünün](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="e7d84-198">`MessagePack`Sürümün kullandığı 1.9. x yükseltmeleri yükleniyor SignalR .</span><span class="sxs-lookup"><span data-stu-id="e7d84-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="e7d84-199">`MessagePack` sürüm 2. x, büyük değişiklikler sunmuştur ve SignalR 3,1 ve önceki sürümleriyle uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="e7d84-199">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="e7d84-200">`MessagePackSecurity.Active`, Olarak ayarlanmadıysa `MessagePackSecurity.UntrustedData` kötü amaçlı bir istemci hizmet reddine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-200">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="e7d84-201">`MessagePackSecurity.Active` `Program.Main` Aşağıdaki kodda gösterildiği gibi içinde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="e7d84-201">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="e7d84-202">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e7d84-202">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="e7d84-203">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-203">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="e7d84-204">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-204">Clients can only support a single protocol.</span></span> <span data-ttu-id="e7d84-205">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-205">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="e7d84-206">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="e7d84-206">.NET client</span></span>

<span data-ttu-id="e7d84-207">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip `AddMessagePackProtocol` üzerine çağırın `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-207">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="e7d84-208">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-208">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="e7d84-209">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="e7d84-209">JavaScript client</span></span>

<span data-ttu-id="e7d84-210">JavaScript istemcisi için MessagePack desteği [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-210">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="e7d84-211">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="e7d84-211">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="e7d84-212">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="e7d84-212">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="e7d84-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="e7d84-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="e7d84-214">Bir tarayıcıda, `msgpack5` Kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-214">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="e7d84-215">`<script>`Başvuru oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="e7d84-215">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="e7d84-216">Kitaplık *node_modules\msgpack5\dist\msgpack5.js*bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-216">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="e7d84-217">`<script>`Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-217">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="e7d84-218">*msgpack5.js*önce *signalr-protocol-msgpack.js* başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="e7d84-218">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="e7d84-219">*signalr-protocol-msgpack.js*önce de *signalr.js* gerekir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-219">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="e7d84-220">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Uygulamasına eklemek, `HubConnectionBuilder` istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-220">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="e7d84-221">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="e7d84-221">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="e7d84-222">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="e7d84-222">MessagePack quirks</span></span>

<span data-ttu-id="e7d84-223">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-223">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="e7d84-224">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="e7d84-224">MessagePack is case-sensitive</span></span>

<span data-ttu-id="e7d84-225">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-225">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="e7d84-226">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="e7d84-226">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="e7d84-227">JavaScript istemcisinden gönderirken, büyük/ `PascalCased` küçük harf C# sınıfıyla tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-227">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="e7d84-228">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e7d84-228">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="e7d84-229">`camelCased`Adların kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="e7d84-229">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="e7d84-230">`Key`MessagePack özelliği için farklı bir ad belirtmek üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e7d84-230">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="e7d84-231">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="e7d84-231">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="e7d84-232">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="e7d84-232">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="e7d84-233">MessagePack protokolü, öğesinin değerini kodlamak için bir yol sağlamaz `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-233">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="e7d84-234">Sonuç olarak, bir tarih serisi kaldırılırken, MessagePack hub protokolü gelen tarihin UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="e7d84-234">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="e7d84-235">`DateTime`Yerel saat içinde değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-235">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="e7d84-236">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="e7d84-236">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="e7d84-237">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="e7d84-237">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="e7d84-238">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="e7d84-238">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="e7d84-239">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) Kitaplığı SignalR `timestamp96` MessagePack içindeki türü desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="e7d84-239">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="e7d84-240">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="e7d84-240">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="e7d84-241">Değeri `DateTime.MinValue` `January 1, 0001` , bir değerde kodlanmalıdır `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-241">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="e7d84-242">Bu nedenle, `DateTime.MinValue` JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="e7d84-242">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="e7d84-243">`DateTime.MinValue`JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="e7d84-243">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="e7d84-244">Genellikle, `DateTime.MinValue` "eksik" veya değeri kodlamak için kullanılır `null` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-244">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="e7d84-245">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir bir `DateTime` değer ( `DateTime?` ) kullanın veya `bool` tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="e7d84-245">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="e7d84-246">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="e7d84-246">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="e7d84-247">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="e7d84-247">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="e7d84-248">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-248">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="e7d84-249">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="e7d84-249">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="e7d84-250">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="e7d84-250">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="e7d84-251">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="e7d84-251">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="e7d84-252">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="e7d84-252">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="e7d84-253">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="e7d84-253">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="e7d84-254">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="e7d84-254">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="e7d84-255">Örneğin, gelen nesnenin bir sayı olan () bir özellik değeri varsa `{ foo: 42 }` , ancak .net sınıfındaki özelliği tür ise `string` , değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="e7d84-255">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="e7d84-256">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e7d84-256">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="e7d84-257">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="e7d84-257">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="e7d84-258">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e7d84-258">Related resources</span></span>

* [<span data-ttu-id="e7d84-259">Başlarken</span><span class="sxs-lookup"><span data-stu-id="e7d84-259">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="e7d84-260">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="e7d84-260">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="e7d84-261">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="e7d84-261">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e7d84-262">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-262">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="e7d84-263">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="e7d84-263">What is MessagePack?</span></span>

<span data-ttu-id="e7d84-264">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-264">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="e7d84-265">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-265">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="e7d84-266">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="e7d84-266">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="e7d84-267">SignalR , MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="e7d84-267">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="e7d84-268">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e7d84-268">Configure MessagePack on the server</span></span>

<span data-ttu-id="e7d84-269">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="e7d84-269">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="e7d84-270">`Startup.ConfigureServices`Yönteminde, `AddMessagePackProtocol` `AddSignalR` sunucuda MessagePack desteğini etkinleştirmek için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e7d84-270">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="e7d84-271">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-271">JSON is enabled by default.</span></span> <span data-ttu-id="e7d84-272">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="e7d84-272">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="e7d84-273">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-273">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="e7d84-274">Bu temsilcisinde, `FormatterResolvers` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-274">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="e7d84-275">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="e7d84-275">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="e7d84-276">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-276">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="e7d84-277">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="e7d84-277">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="e7d84-278">Örneğin, `MessagePackSecurity.Active` statik özelliği olarak ayarlanıyor `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-278">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="e7d84-279">Öğesinin ayarlanması için `MessagePackSecurity.Active` [MessagePack 'in bir 1.9. x sürümünün](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-279">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="e7d84-280">`MessagePack`Sürümün kullandığı 1.9. x yükseltmeleri yükleniyor SignalR .</span><span class="sxs-lookup"><span data-stu-id="e7d84-280">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="e7d84-281">`MessagePackSecurity.Active`, Olarak ayarlanmamışsa `MessagePackSecurity.UntrustedData` kötü amaçlı bir istemci hizmet reddine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-281">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="e7d84-282">`MessagePackSecurity.Active` `Program.Main` Aşağıdaki kodda gösterildiği gibi içinde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="e7d84-282">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="e7d84-283">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e7d84-283">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="e7d84-284">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-284">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="e7d84-285">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-285">Clients can only support a single protocol.</span></span> <span data-ttu-id="e7d84-286">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-286">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="e7d84-287">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="e7d84-287">.NET client</span></span>

<span data-ttu-id="e7d84-288">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip `AddMessagePackProtocol` üzerine çağırın `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-288">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="e7d84-289">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-289">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="e7d84-290">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="e7d84-290">JavaScript client</span></span>

<span data-ttu-id="e7d84-291">JavaScript istemcisi için MessagePack desteği [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-291">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="e7d84-292">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="e7d84-292">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="e7d84-293">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="e7d84-293">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="e7d84-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="e7d84-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="e7d84-295">Bir tarayıcıda, `msgpack5` Kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-295">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="e7d84-296">`<script>`Başvuru oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="e7d84-296">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="e7d84-297">Kitaplık *node_modules\msgpack5\dist\msgpack5.js*bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-297">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="e7d84-298">`<script>`Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-298">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="e7d84-299">*msgpack5.js*önce *signalr-protocol-msgpack.js* başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="e7d84-299">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="e7d84-300">*signalr-protocol-msgpack.js*önce de *signalr.js* gerekir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-300">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="e7d84-301">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Uygulamasına eklemek, `HubConnectionBuilder` istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-301">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="e7d84-302">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="e7d84-302">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="e7d84-303">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="e7d84-303">MessagePack quirks</span></span>

<span data-ttu-id="e7d84-304">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-304">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="e7d84-305">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="e7d84-305">MessagePack is case-sensitive</span></span>

<span data-ttu-id="e7d84-306">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-306">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="e7d84-307">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="e7d84-307">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="e7d84-308">JavaScript istemcisinden gönderirken, büyük/ `PascalCased` küçük harf C# sınıfıyla tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-308">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="e7d84-309">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e7d84-309">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="e7d84-310">`camelCased`Adların kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="e7d84-310">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="e7d84-311">`Key`MessagePack özelliği için farklı bir ad belirtmek üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e7d84-311">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="e7d84-312">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="e7d84-312">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="e7d84-313">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="e7d84-313">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="e7d84-314">MessagePack protokolü, öğesinin değerini kodlamak için bir yol sağlamaz `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-314">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="e7d84-315">Sonuç olarak, bir tarih serisi kaldırılırken, MessagePack hub protokolü gelen tarihin UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="e7d84-315">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="e7d84-316">`DateTime`Yerel saat içinde değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="e7d84-316">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="e7d84-317">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="e7d84-317">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="e7d84-318">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="e7d84-318">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="e7d84-319">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="e7d84-319">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="e7d84-320">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) Kitaplığı SignalR `timestamp96` MessagePack içindeki türü desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="e7d84-320">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="e7d84-321">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="e7d84-321">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="e7d84-322">Değeri, `DateTime.MinValue` `January 1, 0001` bir değerde kodlanmalıdır `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-322">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="e7d84-323">Bu nedenle, `DateTime.MinValue` JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="e7d84-323">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="e7d84-324">`DateTime.MinValue`JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="e7d84-324">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="e7d84-325">Genellikle, `DateTime.MinValue` "eksik" veya değeri kodlamak için kullanılır `null` .</span><span class="sxs-lookup"><span data-stu-id="e7d84-325">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="e7d84-326">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir bir `DateTime` değer ( `DateTime?` ) kullanın veya `bool` tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="e7d84-326">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="e7d84-327">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="e7d84-327">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="e7d84-328">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="e7d84-328">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="e7d84-329">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="e7d84-329">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="e7d84-330">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="e7d84-330">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="e7d84-331">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="e7d84-331">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="e7d84-332">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="e7d84-332">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="e7d84-333">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="e7d84-333">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="e7d84-334">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="e7d84-334">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="e7d84-335">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="e7d84-335">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="e7d84-336">Örneğin, gelen nesnenin bir sayı olan () bir özellik değeri varsa `{ foo: 42 }` , ancak .net sınıfındaki özelliği tür ise `string` , değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="e7d84-336">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="e7d84-337">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e7d84-337">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="e7d84-338">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="e7d84-338">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="e7d84-339">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e7d84-339">Related resources</span></span>

* [<span data-ttu-id="e7d84-340">Başlarken</span><span class="sxs-lookup"><span data-stu-id="e7d84-340">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="e7d84-341">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="e7d84-341">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="e7d84-342">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="e7d84-342">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
