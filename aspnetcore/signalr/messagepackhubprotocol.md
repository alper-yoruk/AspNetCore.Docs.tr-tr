---
title: MessagePack Hub Protokolünü ASP.NET Çekirdek SignalR için kullanma
author: bradygaster
description: ASP.NET Core'a SignalRMessagePack Hub Protokolü ekleyin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277242"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="a7eec-103">MessagePack Hub Protokolünü ASP.NET Çekirdek SignalR için kullanma</span><span class="sxs-lookup"><span data-stu-id="a7eec-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a7eec-104">Bu makalede, okuyucu [nun Başlat'ta](xref:tutorials/signalr)kapsanan konulara aşina olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="a7eec-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="a7eec-105">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="a7eec-105">What is MessagePack?</span></span>

<span data-ttu-id="a7eec-106">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="a7eec-107">[JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği bir sorun olduğunda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="a7eec-108">Baytlar MessagePack araörneğinden geçirilmedikçe, ağ izlemelerine ve günlüklerine bakarken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="a7eec-109">MessagePack biçimi için yerleşik destek vardır ve istemci ve sunucunun kullanması için API'ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="a7eec-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="a7eec-110">MessagePack'i sunucuda yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a7eec-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="a7eec-111">Sunucuda MessagePack Hub Protokolü'nü etkinleştirmek için paketi uygulamanıza yükleyin. `Microsoft.AspNetCore.SignalR.Protocols.MessagePack`</span><span class="sxs-lookup"><span data-stu-id="a7eec-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="a7eec-112">`Startup.ConfigureServices` Yöntemde, sunucuda `AddSignalR` MessagePack desteğini etkinleştirmek için çağrıya ekleyin. `AddMessagePackProtocol`</span><span class="sxs-lookup"><span data-stu-id="a7eec-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="a7eec-113">JSON varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-113">JSON is enabled by default.</span></span> <span data-ttu-id="a7eec-114">MessagePack eklemek hem JSON hem de MessagePack istemcileri için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="a7eec-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="a7eec-115">MessagePack'in verilerinizi nasıl biçimlendireceğini özelleştirmek için seçenekleri `AddMessagePackProtocol` yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="a7eec-116">Bu temsilcide, `SerializerOptions` özellik MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="a7eec-117">Çözümleyicilerin nasıl çalıştığı hakkında daha fazla bilgi için [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)adresindeki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="a7eec-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="a7eec-118">Öznitelikler, nasıl işlenmeleri gerektiğini tanımlamak için serileştirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="a7eec-119">[CVE-2020-5234'ün](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) gözden geçirilmesini ve önerilen yamaları uygulamanızı önemle tavsiye ediyoruz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="a7eec-120">Örneğin, 'yi `.WithSecurity(MessagePackSecurity.UntrustedData)` `SerializerOptions`değiştirirken.</span><span class="sxs-lookup"><span data-stu-id="a7eec-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="a7eec-121">İleti Paketini istemcide yapılandır</span><span class="sxs-lookup"><span data-stu-id="a7eec-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="a7eec-122">JSON desteklenen istemciler için varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="a7eec-123">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="a7eec-124">MessagePack desteği eklemek, önceden yapılandırılmış protokollerin yerini alacaktır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="a7eec-125">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="a7eec-125">.NET client</span></span>

<span data-ttu-id="a7eec-126">MessagePack'i .NET İstemci'de etkinleştirmek için paketi yükleyin `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` ve çağırın. `AddMessagePackProtocol` `HubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="a7eec-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="a7eec-127">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="a7eec-128">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="a7eec-128">JavaScript client</span></span>

<span data-ttu-id="a7eec-129">JavaScript istemcisi için MessagePack [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) desteği npm paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="a7eec-130">Aşağıdaki komutu bir komut kabuğunda çalıştırarak paketi yükleyin:</span><span class="sxs-lookup"><span data-stu-id="a7eec-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="a7eec-131">npm paketini yükledikten sonra, modül doğrudan bir JavaScript modül yükleyici saracılığıyla kullanılabilir veya aşağıdaki dosyaya başvurarak tarayıcıya aktarılabilir:</span><span class="sxs-lookup"><span data-stu-id="a7eec-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="a7eec-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="a7eec-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="a7eec-133">Bir tarayıcıda, `msgpack5` kitaplık da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="a7eec-134">Başvuru `<script>` oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="a7eec-135">Kütüphane *node_modules\msgpack5\dist\msgpack5.js*adresinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="a7eec-136">Öğeyi `<script>` kullanırken, sipariş önemlidir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="a7eec-137">*Signalr-protocol-msgpack.js* *msgpack5.js*önce başvurulan ise, MessagePack ile bağlanmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="a7eec-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="a7eec-138">*signalr.js* de *sinyalci-protokol-msgpack.js*önce gereklidir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="a7eec-139">Bir `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` sunucuya bağlanırken MessagePack iletişim kuralını kullanmak için istemciyi `HubConnectionBuilder` yapılandıracaktır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="a7eec-140">Şu anda, JavaScript istemcisindeki MessagePack protokolü için yapılandırma seçeneği bulunmamaktadır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="a7eec-141">MessagePack tuhaflıklar</span><span class="sxs-lookup"><span data-stu-id="a7eec-141">MessagePack quirks</span></span>

<span data-ttu-id="a7eec-142">MessagePack Hub Protokolü'nü kullanırken dikkat edilmesi gereken birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="a7eec-143">MessagePack büyük/küçük harf duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="a7eec-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="a7eec-144">MessagePack protokolü büyük/küçük harf duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="a7eec-145">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="a7eec-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="a7eec-146">JavaScript istemcisinden gönderirken, kasacın C# sınıfıyla tam olarak eşleşmesi gerektiğinden, özellik adlarını kullanmanız `PascalCased` gerekir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="a7eec-147">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="a7eec-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="a7eec-148">Adkullanmak `camelCased` C# sınıfına düzgün bir şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="a7eec-149">MessagePack özelliği için farklı `Key` bir ad belirtmek için özniteliği kullanarak bu geçici olarak bu şekilde çalışabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="a7eec-150">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp#object-serialization)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="a7eec-151">DateTime.Kind serileştirme/deserializing zaman korunmuş değildir</span><span class="sxs-lookup"><span data-stu-id="a7eec-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="a7eec-152">MessagePack protokolü `Kind` bir `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="a7eec-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="a7eec-153">Sonuç olarak, bir tarih inserializing zaman, MessagePack Hub Protokolü UTC `DateTime.Kind` `DateTimeKind.Local` biçimine aksi takdirde zaman dokunmaz ve olduğu gibi geçmek dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="a7eec-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="a7eec-154">Değerlerle `DateTime` çalışıyorsanız, göndermeden önce UTC'ye dönüştürmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="a7eec-155">Bunları aldığınızda UTC'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="a7eec-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="a7eec-156">DateTime.MinValue JavaScript'te MessagePack tarafından desteklenmez</span><span class="sxs-lookup"><span data-stu-id="a7eec-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="a7eec-157">JavaScript istemcisi SignalR tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) kitaplığı MessagePack'teki `timestamp96` türü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="a7eec-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="a7eec-158">Bu tür çok büyük tarih değerlerini kodlamak için kullanılır (ya çok erken geçmişte ya da çok uzak gelecekte).</span><span class="sxs-lookup"><span data-stu-id="a7eec-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="a7eec-159">Değeri `DateTime.MinValue` `January 1, 0001`, bir `timestamp96` değer olarak kodlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="a7eec-160">Bu nedenle, `DateTime.MinValue` bir JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="a7eec-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="a7eec-161">JavaScript istemcisi tarafından alındığı zaman, `DateTime.MinValue` aşağıdaki hata atılır:</span><span class="sxs-lookup"><span data-stu-id="a7eec-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="a7eec-162">Genellikle, `DateTime.MinValue` bir "eksik" veya `null` değer kodlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="a7eec-163">Bu değeri MessagePack'te kodlamanız gerekiyorsa, geçersiz `DateTime` bir`DateTime?`değer () `bool` kullanın veya tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="a7eec-164">Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [aspnet/SignalR#2228'ye](https://github.com/aspnet/SignalR/issues/2228)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="a7eec-165">MessagePack desteği "ileri-zaman" derleme ortamında</span><span class="sxs-lookup"><span data-stu-id="a7eec-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="a7eec-166">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) kitaplığı, serileştirmeyi optimize etmek için kod oluşturma yı kullanır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="a7eec-167">Sonuç olarak, "zamanın ilerisi" derlemesi (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="a7eec-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="a7eec-168">MessagePack'i bu ortamlarda serializer/deserializer kodunu "ön üreterek" kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="a7eec-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="a7eec-169">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="a7eec-170">Serializer'leri önceden oluşturduktan sonra, aşağıdakilere geçen yapılandırma `AddMessagePackProtocol`temsilcisini kullanarak bunları kaydedebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="a7eec-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="a7eec-171">MessagePack'te tür denetimleri daha katıdır</span><span class="sxs-lookup"><span data-stu-id="a7eec-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="a7eec-172">JSON Hub Protokolü deserialization sırasında tür dönüşümleri gerçekleştirecektir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="a7eec-173">Örneğin, gelen nesnenin bir sayı (`{ foo: 42 }`) olan bir özellik değeri varsa, ancak `string`.NET sınıfındaki özellik türündeyse, değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="a7eec-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="a7eec-174">Ancak, MessagePack bu dönüşümü gerçekleştirmez ve sunucu tarafındaki günlüklerde (ve konsolda) görülebilecek bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="a7eec-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="a7eec-175">Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [SignalRaspnet/ #2937'a](https://github.com/aspnet/SignalR/issues/2937)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="a7eec-176">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a7eec-176">Related resources</span></span>

* [<span data-ttu-id="a7eec-177">Başlayın</span><span class="sxs-lookup"><span data-stu-id="a7eec-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a7eec-178">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="a7eec-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a7eec-179">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="a7eec-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="a7eec-180">Bu makalede, okuyucu [nun Başlat'ta](xref:tutorials/signalr)kapsanan konulara aşina olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="a7eec-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="a7eec-181">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="a7eec-181">What is MessagePack?</span></span>

<span data-ttu-id="a7eec-182">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="a7eec-183">[JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği bir sorun olduğunda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="a7eec-184">Baytlar MessagePack araörneğinden geçirilmedikçe, ağ izlemelerine ve günlüklerine bakarken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="a7eec-185">MessagePack biçimi için yerleşik destek vardır ve istemci ve sunucunun kullanması için API'ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="a7eec-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="a7eec-186">MessagePack'i sunucuda yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a7eec-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="a7eec-187">Sunucuda MessagePack Hub Protokolü'nü etkinleştirmek için paketi uygulamanıza yükleyin. `Microsoft.AspNetCore.SignalR.Protocols.MessagePack`</span><span class="sxs-lookup"><span data-stu-id="a7eec-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="a7eec-188">`Startup.ConfigureServices` Yöntemde, sunucuda `AddSignalR` MessagePack desteğini etkinleştirmek için çağrıya ekleyin. `AddMessagePackProtocol`</span><span class="sxs-lookup"><span data-stu-id="a7eec-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="a7eec-189">JSON varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-189">JSON is enabled by default.</span></span> <span data-ttu-id="a7eec-190">MessagePack eklemek hem JSON hem de MessagePack istemcileri için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="a7eec-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="a7eec-191">MessagePack'in verilerinizi nasıl biçimlendireceğini özelleştirmek için seçenekleri `AddMessagePackProtocol` yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="a7eec-192">Bu temsilcide, `FormatterResolvers` özellik MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="a7eec-193">Çözümleyicilerin nasıl çalıştığı hakkında daha fazla bilgi için [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)adresindeki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="a7eec-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="a7eec-194">Öznitelikler, nasıl işlenmeleri gerektiğini tanımlamak için serileştirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="a7eec-195">[CVE-2020-5234'ün](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) gözden geçirilmesini ve önerilen yamaları uygulamanızı önemle tavsiye ediyoruz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="a7eec-196">Örneğin, `MessagePackSecurity.Active` statik özelliği ' `MessagePackSecurity.UntrustedData`ye ayarlama</span><span class="sxs-lookup"><span data-stu-id="a7eec-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="a7eec-197">MessagePack'in `MessagePackSecurity.Active` [1.9.x sürümünü](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklemeyi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="a7eec-198">Yükleme `MessagePack` 1.9.x sürümü SignalR kullanır yükseltir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="a7eec-199">Ne `MessagePackSecurity.Active` zaman ayarlanmaz `MessagePackSecurity.UntrustedData`, kötü niyetli bir istemci hizmet reddi neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="a7eec-200">Aşağıdaki kodda gösterildiği gibi, `MessagePackSecurity.Active` `Program.Main`</span><span class="sxs-lookup"><span data-stu-id="a7eec-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="a7eec-201">İleti Paketini istemcide yapılandır</span><span class="sxs-lookup"><span data-stu-id="a7eec-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="a7eec-202">JSON desteklenen istemciler için varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="a7eec-203">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="a7eec-204">MessagePack desteği eklemek, önceden yapılandırılmış protokollerin yerini alacaktır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="a7eec-205">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="a7eec-205">.NET client</span></span>

<span data-ttu-id="a7eec-206">MessagePack'i .NET İstemci'de etkinleştirmek için paketi yükleyin `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` ve çağırın. `AddMessagePackProtocol` `HubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="a7eec-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="a7eec-207">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="a7eec-208">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="a7eec-208">JavaScript client</span></span>

<span data-ttu-id="a7eec-209">JavaScript istemcisi için MessagePack [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) desteği npm paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="a7eec-210">Aşağıdaki komutu bir komut kabuğunda çalıştırarak paketi yükleyin:</span><span class="sxs-lookup"><span data-stu-id="a7eec-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="a7eec-211">npm paketini yükledikten sonra, modül doğrudan bir JavaScript modül yükleyici saracılığıyla kullanılabilir veya aşağıdaki dosyaya başvurarak tarayıcıya aktarılabilir:</span><span class="sxs-lookup"><span data-stu-id="a7eec-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="a7eec-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="a7eec-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="a7eec-213">Bir tarayıcıda, `msgpack5` kitaplık da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="a7eec-214">Başvuru `<script>` oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="a7eec-215">Kütüphane *node_modules\msgpack5\dist\msgpack5.js*adresinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="a7eec-216">Öğeyi `<script>` kullanırken, sipariş önemlidir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="a7eec-217">*Signalr-protocol-msgpack.js* *msgpack5.js*önce başvurulan ise, MessagePack ile bağlanmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="a7eec-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="a7eec-218">*signalr.js* de *sinyalci-protokol-msgpack.js*önce gereklidir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="a7eec-219">Bir `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` sunucuya bağlanırken MessagePack iletişim kuralını kullanmak için istemciyi `HubConnectionBuilder` yapılandıracaktır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="a7eec-220">Şu anda, JavaScript istemcisindeki MessagePack protokolü için yapılandırma seçeneği bulunmamaktadır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="a7eec-221">MessagePack tuhaflıklar</span><span class="sxs-lookup"><span data-stu-id="a7eec-221">MessagePack quirks</span></span>

<span data-ttu-id="a7eec-222">MessagePack Hub Protokolü'nü kullanırken dikkat edilmesi gereken birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="a7eec-223">MessagePack büyük/küçük harf duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="a7eec-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="a7eec-224">MessagePack protokolü büyük/küçük harf duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="a7eec-225">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="a7eec-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="a7eec-226">JavaScript istemcisinden gönderirken, kasacın C# sınıfıyla tam olarak eşleşmesi gerektiğinden, özellik adlarını kullanmanız `PascalCased` gerekir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="a7eec-227">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="a7eec-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="a7eec-228">Adkullanmak `camelCased` C# sınıfına düzgün bir şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="a7eec-229">MessagePack özelliği için farklı `Key` bir ad belirtmek için özniteliği kullanarak bu geçici olarak bu şekilde çalışabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="a7eec-230">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp#object-serialization)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="a7eec-231">DateTime.Kind serileştirme/deserializing zaman korunmuş değildir</span><span class="sxs-lookup"><span data-stu-id="a7eec-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="a7eec-232">MessagePack protokolü `Kind` bir `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="a7eec-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="a7eec-233">Sonuç olarak, bir tarih deserializing, MessagePack Hub Protokolü gelen tarih UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="a7eec-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="a7eec-234">Yerel saatle değerlerle `DateTime` çalışıyorsanız, göndermeden önce UTC'ye dönüştürmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="a7eec-235">Bunları aldığınızda UTC'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="a7eec-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="a7eec-236">Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [SignalRaspnet/ #2632'e](https://github.com/aspnet/SignalR/issues/2632)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="a7eec-237">DateTime.MinValue JavaScript'te MessagePack tarafından desteklenmez</span><span class="sxs-lookup"><span data-stu-id="a7eec-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="a7eec-238">JavaScript istemcisi SignalR tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) kitaplığı MessagePack'teki `timestamp96` türü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="a7eec-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="a7eec-239">Bu tür çok büyük tarih değerlerini kodlamak için kullanılır (ya çok erken geçmişte ya da çok uzak gelecekte).</span><span class="sxs-lookup"><span data-stu-id="a7eec-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="a7eec-240">Değeri `DateTime.MinValue` `January 1, 0001`, bir `timestamp96` değer olarak kodlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="a7eec-241">Bu nedenle, `DateTime.MinValue` bir JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="a7eec-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="a7eec-242">JavaScript istemcisi tarafından alındığı zaman, `DateTime.MinValue` aşağıdaki hata atılır:</span><span class="sxs-lookup"><span data-stu-id="a7eec-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="a7eec-243">Genellikle, `DateTime.MinValue` bir "eksik" veya `null` değer kodlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="a7eec-244">Bu değeri MessagePack'te kodlamanız gerekiyorsa, geçersiz `DateTime` bir`DateTime?`değer () `bool` kullanın veya tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="a7eec-245">Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [aspnet/SignalR#2228'ye](https://github.com/aspnet/SignalR/issues/2228)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="a7eec-246">MessagePack desteği "ileri-zaman" derleme ortamında</span><span class="sxs-lookup"><span data-stu-id="a7eec-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="a7eec-247">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirmeyi optimize etmek için kod oluşturma yı kullanır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="a7eec-248">Sonuç olarak, "zamanın ilerisi" derlemesi (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="a7eec-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="a7eec-249">MessagePack'i bu ortamlarda serializer/deserializer kodunu "ön üreterek" kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="a7eec-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="a7eec-250">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="a7eec-251">Serializer'leri önceden oluşturduktan sonra, aşağıdakilere geçen yapılandırma `AddMessagePackProtocol`temsilcisini kullanarak bunları kaydedebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="a7eec-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="a7eec-252">MessagePack'te tür denetimleri daha katıdır</span><span class="sxs-lookup"><span data-stu-id="a7eec-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="a7eec-253">JSON Hub Protokolü deserialization sırasında tür dönüşümleri gerçekleştirecektir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="a7eec-254">Örneğin, gelen nesnenin bir sayı (`{ foo: 42 }`) olan bir özellik değeri varsa, ancak `string`.NET sınıfındaki özellik türündeyse, değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="a7eec-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="a7eec-255">Ancak, MessagePack bu dönüşümü gerçekleştirmez ve sunucu tarafındaki günlüklerde (ve konsolda) görülebilecek bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="a7eec-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="a7eec-256">Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [SignalRaspnet/ #2937'a](https://github.com/aspnet/SignalR/issues/2937)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="a7eec-257">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a7eec-257">Related resources</span></span>

* [<span data-ttu-id="a7eec-258">Başlayın</span><span class="sxs-lookup"><span data-stu-id="a7eec-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a7eec-259">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="a7eec-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a7eec-260">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="a7eec-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a7eec-261">Bu makalede, okuyucu [nun Başlat'ta](xref:tutorials/signalr)kapsanan konulara aşina olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="a7eec-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="a7eec-262">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="a7eec-262">What is MessagePack?</span></span>

<span data-ttu-id="a7eec-263">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="a7eec-264">[JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği bir sorun olduğunda yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="a7eec-265">Baytlar MessagePack araörneğinden geçirilmedikçe, ağ izlemelerine ve günlüklerine bakarken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="a7eec-266">MessagePack biçimi için yerleşik destek vardır ve istemci ve sunucunun kullanması için API'ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="a7eec-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="a7eec-267">MessagePack'i sunucuda yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a7eec-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="a7eec-268">Sunucuda MessagePack Hub Protokolü'nü etkinleştirmek için paketi uygulamanıza yükleyin. `Microsoft.AspNetCore.SignalR.Protocols.MessagePack`</span><span class="sxs-lookup"><span data-stu-id="a7eec-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="a7eec-269">`Startup.ConfigureServices` Yöntemde, sunucuda `AddSignalR` MessagePack desteğini etkinleştirmek için çağrıya ekleyin. `AddMessagePackProtocol`</span><span class="sxs-lookup"><span data-stu-id="a7eec-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="a7eec-270">JSON varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-270">JSON is enabled by default.</span></span> <span data-ttu-id="a7eec-271">MessagePack eklemek hem JSON hem de MessagePack istemcileri için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="a7eec-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="a7eec-272">MessagePack'in verilerinizi nasıl biçimlendireceğini özelleştirmek için seçenekleri `AddMessagePackProtocol` yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="a7eec-273">Bu temsilcide, `FormatterResolvers` özellik MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="a7eec-274">Çözümleyicilerin nasıl çalıştığı hakkında daha fazla bilgi için [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)adresindeki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="a7eec-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="a7eec-275">Öznitelikler, nasıl işlenmeleri gerektiğini tanımlamak için serileştirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="a7eec-276">[CVE-2020-5234'ün](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) gözden geçirilmesini ve önerilen yamaları uygulamanızı önemle tavsiye ediyoruz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="a7eec-277">Örneğin, `MessagePackSecurity.Active` statik özelliği ' `MessagePackSecurity.UntrustedData`ye ayarlama</span><span class="sxs-lookup"><span data-stu-id="a7eec-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="a7eec-278">MessagePack'in `MessagePackSecurity.Active` [1.9.x sürümünü](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklemeyi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="a7eec-279">Yükleme `MessagePack` 1.9.x sürümü SignalR kullanır yükseltir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="a7eec-280">Ne `MessagePackSecurity.Active` zaman ayarlanmaz `MessagePackSecurity.UntrustedData`, kötü niyetli bir istemci hizmet reddi neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="a7eec-281">Aşağıdaki kodda gösterildiği gibi, `MessagePackSecurity.Active` `Program.Main`</span><span class="sxs-lookup"><span data-stu-id="a7eec-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="a7eec-282">İleti Paketini istemcide yapılandır</span><span class="sxs-lookup"><span data-stu-id="a7eec-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="a7eec-283">JSON desteklenen istemciler için varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="a7eec-284">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="a7eec-285">MessagePack desteği eklemek, önceden yapılandırılmış protokollerin yerini alacaktır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="a7eec-286">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="a7eec-286">.NET client</span></span>

<span data-ttu-id="a7eec-287">MessagePack'i .NET İstemci'de etkinleştirmek için paketi yükleyin `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` ve çağırın. `AddMessagePackProtocol` `HubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="a7eec-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="a7eec-288">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="a7eec-289">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="a7eec-289">JavaScript client</span></span>

<span data-ttu-id="a7eec-290">JavaScript istemcisi için MessagePack [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) desteği npm paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="a7eec-291">Aşağıdaki komutu bir komut kabuğunda çalıştırarak paketi yükleyin:</span><span class="sxs-lookup"><span data-stu-id="a7eec-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="a7eec-292">npm paketini yükledikten sonra, modül doğrudan bir JavaScript modül yükleyici saracılığıyla kullanılabilir veya aşağıdaki dosyaya başvurarak tarayıcıya aktarılabilir:</span><span class="sxs-lookup"><span data-stu-id="a7eec-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="a7eec-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="a7eec-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="a7eec-294">Bir tarayıcıda, `msgpack5` kitaplık da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="a7eec-295">Başvuru `<script>` oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="a7eec-296">Kütüphane *node_modules\msgpack5\dist\msgpack5.js*adresinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="a7eec-297">Öğeyi `<script>` kullanırken, sipariş önemlidir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="a7eec-298">*Signalr-protocol-msgpack.js* *msgpack5.js*önce başvurulan ise, MessagePack ile bağlanmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="a7eec-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="a7eec-299">*signalr.js* de *sinyalci-protokol-msgpack.js*önce gereklidir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="a7eec-300">Bir `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` sunucuya bağlanırken MessagePack iletişim kuralını kullanmak için istemciyi `HubConnectionBuilder` yapılandıracaktır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="a7eec-301">Şu anda, JavaScript istemcisindeki MessagePack protokolü için yapılandırma seçeneği bulunmamaktadır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="a7eec-302">MessagePack tuhaflıklar</span><span class="sxs-lookup"><span data-stu-id="a7eec-302">MessagePack quirks</span></span>

<span data-ttu-id="a7eec-303">MessagePack Hub Protokolü'nü kullanırken dikkat edilmesi gereken birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="a7eec-304">MessagePack büyük/küçük harf duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="a7eec-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="a7eec-305">MessagePack protokolü büyük/küçük harf duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="a7eec-306">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="a7eec-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="a7eec-307">JavaScript istemcisinden gönderirken, kasacın C# sınıfıyla tam olarak eşleşmesi gerektiğinden, özellik adlarını kullanmanız `PascalCased` gerekir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="a7eec-308">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="a7eec-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="a7eec-309">Adkullanmak `camelCased` C# sınıfına düzgün bir şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="a7eec-310">MessagePack özelliği için farklı `Key` bir ad belirtmek için özniteliği kullanarak bu geçici olarak bu şekilde çalışabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="a7eec-311">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp#object-serialization)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="a7eec-312">DateTime.Kind serileştirme/deserializing zaman korunmuş değildir</span><span class="sxs-lookup"><span data-stu-id="a7eec-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="a7eec-313">MessagePack protokolü `Kind` bir `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="a7eec-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="a7eec-314">Sonuç olarak, bir tarih deserializing, MessagePack Hub Protokolü gelen tarih UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="a7eec-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="a7eec-315">Yerel saatle değerlerle `DateTime` çalışıyorsanız, göndermeden önce UTC'ye dönüştürmenizi öneririz.</span><span class="sxs-lookup"><span data-stu-id="a7eec-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="a7eec-316">Bunları aldığınızda UTC'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="a7eec-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="a7eec-317">Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [SignalRaspnet/ #2632'e](https://github.com/aspnet/SignalR/issues/2632)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="a7eec-318">DateTime.MinValue JavaScript'te MessagePack tarafından desteklenmez</span><span class="sxs-lookup"><span data-stu-id="a7eec-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="a7eec-319">JavaScript istemcisi SignalR tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) kitaplığı MessagePack'teki `timestamp96` türü desteklemez.</span><span class="sxs-lookup"><span data-stu-id="a7eec-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="a7eec-320">Bu tür çok büyük tarih değerlerini kodlamak için kullanılır (ya çok erken geçmişte ya da çok uzak gelecekte).</span><span class="sxs-lookup"><span data-stu-id="a7eec-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="a7eec-321">Değeri `DateTime.MinValue` bir `January 1, 0001` `timestamp96` değer de kodlanmış olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="a7eec-322">Bu nedenle, `DateTime.MinValue` bir JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="a7eec-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="a7eec-323">JavaScript istemcisi tarafından alındığı zaman, `DateTime.MinValue` aşağıdaki hata atılır:</span><span class="sxs-lookup"><span data-stu-id="a7eec-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="a7eec-324">Genellikle, `DateTime.MinValue` bir "eksik" veya `null` değer kodlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="a7eec-325">Bu değeri MessagePack'te kodlamanız gerekiyorsa, geçersiz `DateTime` bir`DateTime?`değer () `bool` kullanın veya tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="a7eec-326">Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [aspnet/SignalR#2228'ye](https://github.com/aspnet/SignalR/issues/2228)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="a7eec-327">MessagePack desteği "ileri-zaman" derleme ortamında</span><span class="sxs-lookup"><span data-stu-id="a7eec-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="a7eec-328">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirmeyi optimize etmek için kod oluşturma yı kullanır.</span><span class="sxs-lookup"><span data-stu-id="a7eec-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="a7eec-329">Sonuç olarak, "zamanın ilerisi" derlemesi (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="a7eec-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="a7eec-330">MessagePack'i bu ortamlarda serializer/deserializer kodunu "ön üreterek" kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="a7eec-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="a7eec-331">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="a7eec-332">Serializer'leri önceden oluşturduktan sonra, aşağıdakilere geçen yapılandırma `AddMessagePackProtocol`temsilcisini kullanarak bunları kaydedebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="a7eec-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="a7eec-333">MessagePack'te tür denetimleri daha katıdır</span><span class="sxs-lookup"><span data-stu-id="a7eec-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="a7eec-334">JSON Hub Protokolü deserialization sırasında tür dönüşümleri gerçekleştirecektir.</span><span class="sxs-lookup"><span data-stu-id="a7eec-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="a7eec-335">Örneğin, gelen nesnenin bir sayı (`{ foo: 42 }`) olan bir özellik değeri varsa, ancak `string`.NET sınıfındaki özellik türündeyse, değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="a7eec-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="a7eec-336">Ancak, MessagePack bu dönüşümü gerçekleştirmez ve sunucu tarafındaki günlüklerde (ve konsolda) görülebilecek bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="a7eec-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="a7eec-337">Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [SignalRaspnet/ #2937'a](https://github.com/aspnet/SignalR/issues/2937)bakın.</span><span class="sxs-lookup"><span data-stu-id="a7eec-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="a7eec-338">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a7eec-338">Related resources</span></span>

* [<span data-ttu-id="a7eec-339">Başlayın</span><span class="sxs-lookup"><span data-stu-id="a7eec-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a7eec-340">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="a7eec-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a7eec-341">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="a7eec-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
