---
title: ASP.NET Core için ' de MessagePack hub protokolünü kullanın SignalR
author: bradygaster
description: ASP.NET Core için MessagePack hub protokolünü ekleyin SignalR .
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
ms.openlocfilehash: 79a8c98d276738f687ef484795818897f18ceded
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755813"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="5c85d-103">ASP.NET Core için ' de MessagePack hub protokolünü kullanın SignalR</span><span class="sxs-lookup"><span data-stu-id="5c85d-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5c85d-104">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="5c85d-105">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="5c85d-105">What is MessagePack?</span></span>

<span data-ttu-id="5c85d-106">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="5c85d-107">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="5c85d-108">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="5c85d-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="5c85d-109">, MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="5c85d-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="5c85d-110">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5c85d-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="5c85d-111">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="5c85d-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="5c85d-112">`Startup.ConfigureServices`Yönteminde, `AddMessagePackProtocol` `AddSignalR` sunucuda MessagePack desteğini etkinleştirmek için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5c85d-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="5c85d-113">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-113">JSON is enabled by default.</span></span> <span data-ttu-id="5c85d-114">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="5c85d-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="5c85d-115">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="5c85d-116">Bu temsilcisinde, `SerializerOptions` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="5c85d-117">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="5c85d-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="5c85d-118">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="5c85d-119">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="5c85d-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="5c85d-120">Örneğin, öğesini `.WithSecurity(MessagePackSecurity.UntrustedData)` değiştirirken çağırma `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="5c85d-121">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5c85d-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="5c85d-122">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="5c85d-123">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="5c85d-124">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="5c85d-125">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="5c85d-125">.NET client</span></span>

<span data-ttu-id="5c85d-126">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip `AddMessagePackProtocol` üzerine çağırın `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="5c85d-127">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="5c85d-128">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="5c85d-128">JavaScript client</span></span>

<span data-ttu-id="5c85d-129">JavaScript istemcisi için MessagePack desteği [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="5c85d-130">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="5c85d-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="5c85d-131">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5c85d-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="5c85d-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="5c85d-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="5c85d-133">Bir tarayıcıda, `msgpack5` Kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="5c85d-134">`<script>`Başvuru oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="5c85d-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="5c85d-135">Kitaplık *node_modules\msgpack5\dist\msgpack5.js*bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="5c85d-136">`<script>`Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="5c85d-137">*msgpack5.js*önce *signalr-protocol-msgpack.js* başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="5c85d-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="5c85d-138">*signalr-protocol-msgpack.js*önce de *signalr.js* gerekir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="5c85d-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Uygulamasına eklemek, `HubConnectionBuilder` istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="5c85d-140">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="5c85d-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="5c85d-141">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="5c85d-141">MessagePack quirks</span></span>

<span data-ttu-id="5c85d-142">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="5c85d-143">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="5c85d-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="5c85d-144">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="5c85d-145">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="5c85d-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="5c85d-146">JavaScript istemcisinden gönderirken, büyük/ `PascalCased` küçük harf C# sınıfıyla tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="5c85d-147">Örnek:</span><span class="sxs-lookup"><span data-stu-id="5c85d-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="5c85d-148">`camelCased`Adların kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="5c85d-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="5c85d-149">`Key`MessagePack özelliği için farklı bir ad belirtmek üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5c85d-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="5c85d-150">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="5c85d-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="5c85d-151">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="5c85d-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="5c85d-152">MessagePack protokolü, öğesinin değerini kodlamak için bir yol sağlamaz `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="5c85d-153">Sonuç olarak, bir tarihin serisi kaldırılırken MessagePack hub Protokolü UTC biçimine dönüştürülür, `DateTime.Kind` `DateTimeKind.Local` Aksi takdirde, zaman içinde dokunmaz ve olduğu gibi geçireceğiz.</span><span class="sxs-lookup"><span data-stu-id="5c85d-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="5c85d-154">`DateTime`Değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="5c85d-155">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="5c85d-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="5c85d-156">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="5c85d-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="5c85d-157">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) Kitaplığı SignalR `timestamp96` MessagePack içindeki türü desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="5c85d-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="5c85d-158">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="5c85d-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="5c85d-159">Değeri `DateTime.MinValue` `January 1, 0001` , bir değerde kodlanmalıdır `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="5c85d-160">Bu nedenle, `DateTime.MinValue` JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5c85d-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="5c85d-161">`DateTime.MinValue`JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="5c85d-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="5c85d-162">Genellikle, `DateTime.MinValue` "eksik" veya değeri kodlamak için kullanılır `null` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="5c85d-163">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir bir `DateTime` değer ( `DateTime?` ) kullanın veya `bool` tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="5c85d-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="5c85d-164">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="5c85d-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="5c85d-165">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="5c85d-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="5c85d-166">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="5c85d-167">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5c85d-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="5c85d-168">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="5c85d-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="5c85d-169">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="5c85d-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="5c85d-170">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="5c85d-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="5c85d-171">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="5c85d-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="5c85d-172">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="5c85d-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="5c85d-173">Örneğin, gelen nesnenin bir sayı olan () bir özellik değeri varsa `{ foo: 42 }` , ancak .net sınıfındaki özelliği tür ise `string` , değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="5c85d-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="5c85d-174">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5c85d-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="5c85d-175">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="5c85d-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="5c85d-176">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5c85d-176">Related resources</span></span>

* [<span data-ttu-id="5c85d-177">Başlarken</span><span class="sxs-lookup"><span data-stu-id="5c85d-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="5c85d-178">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="5c85d-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="5c85d-179">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="5c85d-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="5c85d-180">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="5c85d-181">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="5c85d-181">What is MessagePack?</span></span>

<span data-ttu-id="5c85d-182">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="5c85d-183">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="5c85d-184">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="5c85d-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="5c85d-185">, MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="5c85d-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="5c85d-186">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5c85d-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="5c85d-187">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="5c85d-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="5c85d-188">`Startup.ConfigureServices`Yönteminde, `AddMessagePackProtocol` `AddSignalR` sunucuda MessagePack desteğini etkinleştirmek için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5c85d-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="5c85d-189">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-189">JSON is enabled by default.</span></span> <span data-ttu-id="5c85d-190">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="5c85d-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="5c85d-191">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="5c85d-192">Bu temsilcisinde, `FormatterResolvers` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="5c85d-193">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="5c85d-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="5c85d-194">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="5c85d-195">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="5c85d-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="5c85d-196">Örneğin, `MessagePackSecurity.Active` statik özelliği olarak ayarlanıyor `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="5c85d-197">Öğesinin ayarlanması için `MessagePackSecurity.Active` [MessagePack 'in bir 1.9. x sürümünün](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="5c85d-198">`MessagePack`Sürümün kullandığı 1.9. x yükseltmeleri yükleniyor SignalR .</span><span class="sxs-lookup"><span data-stu-id="5c85d-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="5c85d-199">`MessagePackSecurity.Active`, Olarak ayarlanmamışsa `MessagePackSecurity.UntrustedData` kötü amaçlı bir istemci hizmet reddine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="5c85d-200">`MessagePackSecurity.Active` `Program.Main` Aşağıdaki kodda gösterildiği gibi içinde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="5c85d-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="5c85d-201">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5c85d-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="5c85d-202">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="5c85d-203">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="5c85d-204">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="5c85d-205">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="5c85d-205">.NET client</span></span>

<span data-ttu-id="5c85d-206">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip `AddMessagePackProtocol` üzerine çağırın `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="5c85d-207">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="5c85d-208">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="5c85d-208">JavaScript client</span></span>

<span data-ttu-id="5c85d-209">JavaScript istemcisi için MessagePack desteği [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="5c85d-210">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="5c85d-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="5c85d-211">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5c85d-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="5c85d-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="5c85d-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="5c85d-213">Bir tarayıcıda, `msgpack5` Kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="5c85d-214">`<script>`Başvuru oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="5c85d-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="5c85d-215">Kitaplık *node_modules\msgpack5\dist\msgpack5.js*bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="5c85d-216">`<script>`Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="5c85d-217">*msgpack5.js*önce *signalr-protocol-msgpack.js* başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="5c85d-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="5c85d-218">*signalr-protocol-msgpack.js*önce de *signalr.js* gerekir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="5c85d-219">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Uygulamasına eklemek, `HubConnectionBuilder` istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="5c85d-220">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="5c85d-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="5c85d-221">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="5c85d-221">MessagePack quirks</span></span>

<span data-ttu-id="5c85d-222">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="5c85d-223">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="5c85d-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="5c85d-224">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="5c85d-225">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="5c85d-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="5c85d-226">JavaScript istemcisinden gönderirken, büyük/ `PascalCased` küçük harf C# sınıfıyla tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="5c85d-227">Örnek:</span><span class="sxs-lookup"><span data-stu-id="5c85d-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="5c85d-228">`camelCased`Adların kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="5c85d-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="5c85d-229">`Key`MessagePack özelliği için farklı bir ad belirtmek üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5c85d-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="5c85d-230">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="5c85d-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="5c85d-231">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="5c85d-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="5c85d-232">MessagePack protokolü, öğesinin değerini kodlamak için bir yol sağlamaz `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="5c85d-233">Sonuç olarak, bir tarih serisi kaldırılırken, MessagePack hub protokolü gelen tarihin UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="5c85d-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="5c85d-234">`DateTime`Yerel saat içinde değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="5c85d-235">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="5c85d-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="5c85d-236">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="5c85d-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="5c85d-237">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="5c85d-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="5c85d-238">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) Kitaplığı SignalR `timestamp96` MessagePack içindeki türü desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="5c85d-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="5c85d-239">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="5c85d-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="5c85d-240">Değeri `DateTime.MinValue` `January 1, 0001` , bir değerde kodlanmalıdır `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="5c85d-241">Bu nedenle, `DateTime.MinValue` JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5c85d-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="5c85d-242">`DateTime.MinValue`JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="5c85d-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="5c85d-243">Genellikle, `DateTime.MinValue` "eksik" veya değeri kodlamak için kullanılır `null` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="5c85d-244">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir bir `DateTime` değer ( `DateTime?` ) kullanın veya `bool` tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="5c85d-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="5c85d-245">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="5c85d-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="5c85d-246">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="5c85d-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="5c85d-247">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="5c85d-248">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5c85d-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="5c85d-249">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="5c85d-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="5c85d-250">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="5c85d-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="5c85d-251">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="5c85d-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="5c85d-252">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="5c85d-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="5c85d-253">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="5c85d-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="5c85d-254">Örneğin, gelen nesnenin bir sayı olan () bir özellik değeri varsa `{ foo: 42 }` , ancak .net sınıfındaki özelliği tür ise `string` , değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="5c85d-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="5c85d-255">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5c85d-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="5c85d-256">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="5c85d-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="5c85d-257">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5c85d-257">Related resources</span></span>

* [<span data-ttu-id="5c85d-258">Başlarken</span><span class="sxs-lookup"><span data-stu-id="5c85d-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="5c85d-259">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="5c85d-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="5c85d-260">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="5c85d-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5c85d-261">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="5c85d-262">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="5c85d-262">What is MessagePack?</span></span>

<span data-ttu-id="5c85d-263">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="5c85d-264">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="5c85d-265">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="5c85d-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="5c85d-266">, MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="5c85d-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="5c85d-267">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5c85d-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="5c85d-268">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="5c85d-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="5c85d-269">`Startup.ConfigureServices`Yönteminde, `AddMessagePackProtocol` `AddSignalR` sunucuda MessagePack desteğini etkinleştirmek için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5c85d-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="5c85d-270">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-270">JSON is enabled by default.</span></span> <span data-ttu-id="5c85d-271">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="5c85d-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="5c85d-272">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="5c85d-273">Bu temsilcisinde, `FormatterResolvers` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="5c85d-274">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="5c85d-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="5c85d-275">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="5c85d-276">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="5c85d-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="5c85d-277">Örneğin, `MessagePackSecurity.Active` statik özelliği olarak ayarlanıyor `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="5c85d-278">Öğesinin ayarlanması için `MessagePackSecurity.Active` [MessagePack 'in bir 1.9. x sürümünün](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="5c85d-279">`MessagePack`Sürümün kullandığı 1.9. x yükseltmeleri yükleniyor SignalR .</span><span class="sxs-lookup"><span data-stu-id="5c85d-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="5c85d-280">`MessagePackSecurity.Active`, Olarak ayarlanmamışsa `MessagePackSecurity.UntrustedData` kötü amaçlı bir istemci hizmet reddine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="5c85d-281">`MessagePackSecurity.Active` `Program.Main` Aşağıdaki kodda gösterildiği gibi içinde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="5c85d-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="5c85d-282">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5c85d-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="5c85d-283">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="5c85d-284">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="5c85d-285">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="5c85d-286">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="5c85d-286">.NET client</span></span>

<span data-ttu-id="5c85d-287">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip `AddMessagePackProtocol` üzerine çağırın `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="5c85d-288">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="5c85d-289">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="5c85d-289">JavaScript client</span></span>

<span data-ttu-id="5c85d-290">JavaScript istemcisi için MessagePack desteği [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="5c85d-291">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="5c85d-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="5c85d-292">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5c85d-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="5c85d-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="5c85d-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="5c85d-294">Bir tarayıcıda, `msgpack5` Kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="5c85d-295">`<script>`Başvuru oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="5c85d-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="5c85d-296">Kitaplık *node_modules\msgpack5\dist\msgpack5.js*bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="5c85d-297">`<script>`Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="5c85d-298">*msgpack5.js*önce *signalr-protocol-msgpack.js* başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="5c85d-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="5c85d-299">*signalr-protocol-msgpack.js*önce de *signalr.js* gerekir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="5c85d-300">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Uygulamasına eklemek, `HubConnectionBuilder` istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="5c85d-301">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="5c85d-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="5c85d-302">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="5c85d-302">MessagePack quirks</span></span>

<span data-ttu-id="5c85d-303">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="5c85d-304">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="5c85d-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="5c85d-305">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="5c85d-306">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="5c85d-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="5c85d-307">JavaScript istemcisinden gönderirken, büyük/ `PascalCased` küçük harf C# sınıfıyla tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="5c85d-308">Örnek:</span><span class="sxs-lookup"><span data-stu-id="5c85d-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="5c85d-309">`camelCased`Adların kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="5c85d-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="5c85d-310">`Key`MessagePack özelliği için farklı bir ad belirtmek üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5c85d-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="5c85d-311">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="5c85d-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="5c85d-312">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="5c85d-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="5c85d-313">MessagePack protokolü, öğesinin değerini kodlamak için bir yol sağlamaz `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="5c85d-314">Sonuç olarak, bir tarih serisi kaldırılırken, MessagePack hub protokolü gelen tarihin UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="5c85d-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="5c85d-315">`DateTime`Yerel saat içinde değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="5c85d-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="5c85d-316">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="5c85d-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="5c85d-317">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="5c85d-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="5c85d-318">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="5c85d-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="5c85d-319">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) Kitaplığı SignalR `timestamp96` MessagePack içindeki türü desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="5c85d-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="5c85d-320">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="5c85d-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="5c85d-321">Değeri, `DateTime.MinValue` `January 1, 0001` bir değerde kodlanmalıdır `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="5c85d-322">Bu nedenle, `DateTime.MinValue` JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5c85d-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="5c85d-323">`DateTime.MinValue`JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="5c85d-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="5c85d-324">Genellikle, `DateTime.MinValue` "eksik" veya değeri kodlamak için kullanılır `null` .</span><span class="sxs-lookup"><span data-stu-id="5c85d-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="5c85d-325">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir bir `DateTime` değer ( `DateTime?` ) kullanın veya `bool` tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="5c85d-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="5c85d-326">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="5c85d-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="5c85d-327">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="5c85d-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="5c85d-328">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="5c85d-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="5c85d-329">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="5c85d-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="5c85d-330">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="5c85d-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="5c85d-331">Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="5c85d-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="5c85d-332">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="5c85d-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="5c85d-333">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="5c85d-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="5c85d-334">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="5c85d-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="5c85d-335">Örneğin, gelen nesnenin bir sayı olan () bir özellik değeri varsa `{ foo: 42 }` , ancak .net sınıfındaki özelliği tür ise `string` , değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="5c85d-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="5c85d-336">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5c85d-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="5c85d-337">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="5c85d-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="5c85d-338">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5c85d-338">Related resources</span></span>

* [<span data-ttu-id="5c85d-339">Başlarken</span><span class="sxs-lookup"><span data-stu-id="5c85d-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="5c85d-340">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="5c85d-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="5c85d-341">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="5c85d-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
