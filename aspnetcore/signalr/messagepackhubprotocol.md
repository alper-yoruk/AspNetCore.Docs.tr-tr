---
title: "ASP.NET Core için ' de MessagePack hub protokolünü kullanın :::no-loc(SignalR):::"
author: bradygaster
description: 'ASP.NET Core için MessagePack hub protokolünü ekleyin :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: e7d19a42e48048d2be4b87d6b0ac1ba6b2596ff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058175"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="eb972-103">ASP.NET Core için ' de MessagePack hub protokolünü kullanın :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="eb972-103">Use MessagePack Hub Protocol in :::no-loc(SignalR)::: for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="eb972-104">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="eb972-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="eb972-105">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="eb972-105">What is MessagePack?</span></span>

<span data-ttu-id="eb972-106">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="eb972-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="eb972-107">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="eb972-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="eb972-108">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="eb972-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="eb972-109">:::no-loc(SignalR)::: , MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="eb972-109">:::no-loc(SignalR)::: has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="eb972-110">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="eb972-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="eb972-111">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="eb972-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="eb972-112">`Startup.ConfigureServices`Yönteminde, `AddMessagePackProtocol` `Add:::no-loc(SignalR):::` sunucuda MessagePack desteğini etkinleştirmek için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="eb972-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="eb972-113">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="eb972-113">JSON is enabled by default.</span></span> <span data-ttu-id="eb972-114">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="eb972-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="eb972-115">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="eb972-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="eb972-116">Bu temsilcisinde, `SerializerOptions` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="eb972-117">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="eb972-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="eb972-118">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="eb972-119">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="eb972-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="eb972-120">Örneğin, öğesini `.WithSecurity(MessagePackSecurity.UntrustedData)` değiştirirken çağırma `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="eb972-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="eb972-121">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="eb972-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="eb972-122">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="eb972-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="eb972-123">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="eb972-124">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="eb972-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="eb972-125">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="eb972-125">.NET client</span></span>

<span data-ttu-id="eb972-126">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` paketini yükleyip `AddMessagePackProtocol` üzerine çağırın `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="eb972-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="eb972-127">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="eb972-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="eb972-128">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="eb972-128">JavaScript client</span></span>

<span data-ttu-id="eb972-129">JavaScript istemcisi için MessagePack desteği [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="eb972-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="eb972-130">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="eb972-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="eb972-131">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="eb972-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="eb972-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="eb972-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="eb972-133">Bir tarayıcıda, `msgpack5` Kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="eb972-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="eb972-134">`<script>`Başvuru oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="eb972-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="eb972-135">Kitaplık *node_modules\msgpack5\dist\msgpack5.js* bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="eb972-136">`<script>`Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="eb972-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="eb972-137">*msgpack5.js* önce *signalr-protocol-msgpack.js* başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="eb972-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="eb972-138">*signalr-protocol-msgpack.js* önce de *signalr.js* gerekir.</span><span class="sxs-lookup"><span data-stu-id="eb972-138">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="eb972-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Uygulamasına eklemek, `HubConnectionBuilder` istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="eb972-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="eb972-140">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="eb972-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="eb972-141">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="eb972-141">MessagePack quirks</span></span>

<span data-ttu-id="eb972-142">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="eb972-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="eb972-143">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="eb972-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="eb972-144">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="eb972-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="eb972-145">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="eb972-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="eb972-146">JavaScript istemcisinden gönderirken, büyük/ `PascalCased` küçük harf C# sınıfıyla tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="eb972-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="eb972-147">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eb972-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="eb972-148">`camelCased`Adların kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="eb972-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="eb972-149">`Key`MessagePack özelliği için farklı bir ad belirtmek üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="eb972-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="eb972-150">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp#object-serialization)bakın.</span><span class="sxs-lookup"><span data-stu-id="eb972-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="eb972-151">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="eb972-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="eb972-152">MessagePack protokolü, öğesinin değerini kodlamak için bir yol sağlamaz `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="eb972-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="eb972-153">Sonuç olarak, bir tarihin serisi kaldırılırken MessagePack hub Protokolü UTC biçimine dönüştürülür, `DateTime.Kind` `DateTimeKind.Local` Aksi takdirde, zaman içinde dokunmaz ve olduğu gibi geçireceğiz.</span><span class="sxs-lookup"><span data-stu-id="eb972-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="eb972-154">`DateTime`Değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="eb972-155">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="eb972-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="eb972-156">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="eb972-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="eb972-157">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) Kitaplığı :::no-loc(SignalR)::: `timestamp96` MessagePack içindeki türü desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="eb972-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="eb972-158">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="eb972-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="eb972-159">Değeri `DateTime.MinValue` `January 1, 0001` , bir değerde kodlanmalıdır `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="eb972-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="eb972-160">Bu nedenle, `DateTime.MinValue` JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="eb972-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="eb972-161">`DateTime.MinValue`JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="eb972-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="eb972-162">Genellikle, `DateTime.MinValue` "eksik" veya değeri kodlamak için kullanılır `null` .</span><span class="sxs-lookup"><span data-stu-id="eb972-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="eb972-163">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir bir `DateTime` değer ( `DateTime?` ) kullanın veya `bool` tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="eb972-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="eb972-164">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="eb972-164">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="eb972-165">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="eb972-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="eb972-166">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="eb972-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="eb972-167">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="eb972-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="eb972-168">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="eb972-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="eb972-169">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)bakın.</span><span class="sxs-lookup"><span data-stu-id="eb972-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="eb972-170">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="eb972-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="eb972-171">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="eb972-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="eb972-172">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="eb972-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="eb972-173">Örneğin, gelen nesnenin bir sayı olan () bir özellik değeri varsa `{ foo: 42 }` , ancak .net sınıfındaki özelliği tür ise `string` , değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="eb972-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="eb972-174">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="eb972-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="eb972-175">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="eb972-175">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="eb972-176">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="eb972-176">Related resources</span></span>

* [<span data-ttu-id="eb972-177">Başlarken</span><span class="sxs-lookup"><span data-stu-id="eb972-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="eb972-178">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="eb972-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="eb972-179">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="eb972-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="eb972-180">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="eb972-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="eb972-181">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="eb972-181">What is MessagePack?</span></span>

<span data-ttu-id="eb972-182">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="eb972-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="eb972-183">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="eb972-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="eb972-184">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="eb972-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="eb972-185">:::no-loc(SignalR)::: , MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="eb972-185">:::no-loc(SignalR)::: has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="eb972-186">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="eb972-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="eb972-187">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="eb972-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="eb972-188">`Startup.ConfigureServices`Yönteminde, `AddMessagePackProtocol` `Add:::no-loc(SignalR):::` sunucuda MessagePack desteğini etkinleştirmek için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="eb972-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="eb972-189">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="eb972-189">JSON is enabled by default.</span></span> <span data-ttu-id="eb972-190">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="eb972-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="eb972-191">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="eb972-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="eb972-192">Bu temsilcisinde, `FormatterResolvers` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="eb972-193">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="eb972-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="eb972-194">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="eb972-195">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="eb972-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="eb972-196">Örneğin, `MessagePackSecurity.Active` statik özelliği olarak ayarlanıyor `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="eb972-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="eb972-197">Öğesinin ayarlanması için `MessagePackSecurity.Active` [MessagePack 'in bir 1.9. x sürümünün](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="eb972-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="eb972-198">`MessagePack`Sürümün kullandığı 1.9. x yükseltmeleri yükleniyor :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="eb972-198">Installing `MessagePack` 1.9.x upgrades the version :::no-loc(SignalR)::: uses.</span></span> <span data-ttu-id="eb972-199">`MessagePack` sürüm 2. x, büyük değişiklikler sunmuştur ve :::no-loc(SignalR)::: 3,1 ve önceki sürümleriyle uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="eb972-199">`MessagePack` version 2.x introduced breaking changes and is incompatible with :::no-loc(SignalR)::: versions 3.1 and earlier.</span></span> <span data-ttu-id="eb972-200">`MessagePackSecurity.Active`, Olarak ayarlanmadıysa `MessagePackSecurity.UntrustedData` kötü amaçlı bir istemci hizmet reddine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-200">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="eb972-201">`MessagePackSecurity.Active` `Program.Main` Aşağıdaki kodda gösterildiği gibi içinde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="eb972-201">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="eb972-202">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="eb972-202">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="eb972-203">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="eb972-203">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="eb972-204">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-204">Clients can only support a single protocol.</span></span> <span data-ttu-id="eb972-205">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="eb972-205">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="eb972-206">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="eb972-206">.NET client</span></span>

<span data-ttu-id="eb972-207">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` paketini yükleyip `AddMessagePackProtocol` üzerine çağırın `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="eb972-207">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="eb972-208">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="eb972-208">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="eb972-209">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="eb972-209">JavaScript client</span></span>

<span data-ttu-id="eb972-210">JavaScript istemcisi için MessagePack desteği [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="eb972-210">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="eb972-211">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="eb972-211">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="eb972-212">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="eb972-212">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="eb972-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="eb972-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="eb972-214">Bir tarayıcıda, `msgpack5` Kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="eb972-214">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="eb972-215">`<script>`Başvuru oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="eb972-215">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="eb972-216">Kitaplık *node_modules\msgpack5\dist\msgpack5.js* bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-216">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="eb972-217">`<script>`Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="eb972-217">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="eb972-218">*msgpack5.js* önce *signalr-protocol-msgpack.js* başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="eb972-218">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="eb972-219">*signalr-protocol-msgpack.js* önce de *signalr.js* gerekir.</span><span class="sxs-lookup"><span data-stu-id="eb972-219">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="eb972-220">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Uygulamasına eklemek, `HubConnectionBuilder` istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="eb972-220">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="eb972-221">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="eb972-221">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="eb972-222">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="eb972-222">MessagePack quirks</span></span>

<span data-ttu-id="eb972-223">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="eb972-223">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="eb972-224">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="eb972-224">MessagePack is case-sensitive</span></span>

<span data-ttu-id="eb972-225">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="eb972-225">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="eb972-226">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="eb972-226">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="eb972-227">JavaScript istemcisinden gönderirken, büyük/ `PascalCased` küçük harf C# sınıfıyla tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="eb972-227">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="eb972-228">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eb972-228">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="eb972-229">`camelCased`Adların kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="eb972-229">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="eb972-230">`Key`MessagePack özelliği için farklı bir ad belirtmek üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="eb972-230">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="eb972-231">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp#object-serialization)bakın.</span><span class="sxs-lookup"><span data-stu-id="eb972-231">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="eb972-232">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="eb972-232">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="eb972-233">MessagePack protokolü, öğesinin değerini kodlamak için bir yol sağlamaz `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="eb972-233">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="eb972-234">Sonuç olarak, bir tarih serisi kaldırılırken, MessagePack hub protokolü gelen tarihin UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="eb972-234">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="eb972-235">`DateTime`Yerel saat içinde değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-235">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="eb972-236">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="eb972-236">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="eb972-237">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ :::no-loc(SignalR)::: #2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="eb972-237">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="eb972-238">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="eb972-238">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="eb972-239">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) Kitaplığı :::no-loc(SignalR)::: `timestamp96` MessagePack içindeki türü desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="eb972-239">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="eb972-240">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="eb972-240">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="eb972-241">Değeri `DateTime.MinValue` `January 1, 0001` , bir değerde kodlanmalıdır `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="eb972-241">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="eb972-242">Bu nedenle, `DateTime.MinValue` JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="eb972-242">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="eb972-243">`DateTime.MinValue`JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="eb972-243">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="eb972-244">Genellikle, `DateTime.MinValue` "eksik" veya değeri kodlamak için kullanılır `null` .</span><span class="sxs-lookup"><span data-stu-id="eb972-244">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="eb972-245">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir bir `DateTime` değer ( `DateTime?` ) kullanın veya `bool` tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="eb972-245">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="eb972-246">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="eb972-246">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="eb972-247">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="eb972-247">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="eb972-248">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="eb972-248">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="eb972-249">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="eb972-249">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="eb972-250">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="eb972-250">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="eb972-251">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)bakın.</span><span class="sxs-lookup"><span data-stu-id="eb972-251">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="eb972-252">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="eb972-252">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="eb972-253">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="eb972-253">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="eb972-254">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="eb972-254">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="eb972-255">Örneğin, gelen nesnenin bir sayı olan () bir özellik değeri varsa `{ foo: 42 }` , ancak .net sınıfındaki özelliği tür ise `string` , değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="eb972-255">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="eb972-256">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="eb972-256">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="eb972-257">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="eb972-257">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="eb972-258">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="eb972-258">Related resources</span></span>

* [<span data-ttu-id="eb972-259">Başlarken</span><span class="sxs-lookup"><span data-stu-id="eb972-259">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="eb972-260">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="eb972-260">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="eb972-261">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="eb972-261">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eb972-262">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="eb972-262">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="eb972-263">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="eb972-263">What is MessagePack?</span></span>

<span data-ttu-id="eb972-264">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="eb972-264">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="eb972-265">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="eb972-265">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="eb972-266">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="eb972-266">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="eb972-267">:::no-loc(SignalR)::: , MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="eb972-267">:::no-loc(SignalR)::: has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="eb972-268">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="eb972-268">Configure MessagePack on the server</span></span>

<span data-ttu-id="eb972-269">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="eb972-269">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="eb972-270">`Startup.ConfigureServices`Yönteminde, `AddMessagePackProtocol` `Add:::no-loc(SignalR):::` sunucuda MessagePack desteğini etkinleştirmek için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="eb972-270">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `Add:::no-loc(SignalR):::` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="eb972-271">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="eb972-271">JSON is enabled by default.</span></span> <span data-ttu-id="eb972-272">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="eb972-272">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol();
```

<span data-ttu-id="eb972-273">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="eb972-273">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="eb972-274">Bu temsilcisinde, `FormatterResolvers` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-274">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="eb972-275">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="eb972-275">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="eb972-276">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-276">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="eb972-277">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="eb972-277">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="eb972-278">Örneğin, `MessagePackSecurity.Active` statik özelliği olarak ayarlanıyor `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="eb972-278">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="eb972-279">Öğesinin ayarlanması için `MessagePackSecurity.Active` [MessagePack 'in bir 1.9. x sürümünün](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="eb972-279">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="eb972-280">`MessagePack`Sürümün kullandığı 1.9. x yükseltmeleri yükleniyor :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="eb972-280">Installing `MessagePack` 1.9.x upgrades the version :::no-loc(SignalR)::: uses.</span></span> <span data-ttu-id="eb972-281">`MessagePackSecurity.Active`, Olarak ayarlanmamışsa `MessagePackSecurity.UntrustedData` kötü amaçlı bir istemci hizmet reddine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-281">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="eb972-282">`MessagePackSecurity.Active` `Program.Main` Aşağıdaki kodda gösterildiği gibi içinde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="eb972-282">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="eb972-283">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="eb972-283">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="eb972-284">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="eb972-284">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="eb972-285">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-285">Clients can only support a single protocol.</span></span> <span data-ttu-id="eb972-286">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="eb972-286">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="eb972-287">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="eb972-287">.NET client</span></span>

<span data-ttu-id="eb972-288">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` paketini yükleyip `AddMessagePackProtocol` üzerine çağırın `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="eb972-288">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.:::no-loc(SignalR):::.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(SignalR):::.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="eb972-289">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="eb972-289">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="eb972-290">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="eb972-290">JavaScript client</span></span>

<span data-ttu-id="eb972-291">JavaScript istemcisi için MessagePack desteği [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="eb972-291">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="eb972-292">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="eb972-292">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="eb972-293">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="eb972-293">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="eb972-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="eb972-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="eb972-295">Bir tarayıcıda, `msgpack5` Kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="eb972-295">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="eb972-296">`<script>`Başvuru oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="eb972-296">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="eb972-297">Kitaplık *node_modules\msgpack5\dist\msgpack5.js* bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-297">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="eb972-298">`<script>`Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="eb972-298">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="eb972-299">*msgpack5.js* önce *signalr-protocol-msgpack.js* başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="eb972-299">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="eb972-300">*signalr-protocol-msgpack.js* önce de *signalr.js* gerekir.</span><span class="sxs-lookup"><span data-stu-id="eb972-300">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="eb972-301">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Uygulamasına eklemek, `HubConnectionBuilder` istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="eb972-301">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="eb972-302">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="eb972-302">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="eb972-303">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="eb972-303">MessagePack quirks</span></span>

<span data-ttu-id="eb972-304">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="eb972-304">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="eb972-305">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="eb972-305">MessagePack is case-sensitive</span></span>

<span data-ttu-id="eb972-306">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="eb972-306">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="eb972-307">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="eb972-307">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="eb972-308">JavaScript istemcisinden gönderirken, büyük/ `PascalCased` küçük harf C# sınıfıyla tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="eb972-308">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="eb972-309">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eb972-309">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="eb972-310">`camelCased`Adların kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="eb972-310">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="eb972-311">`Key`MessagePack özelliği için farklı bir ad belirtmek üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="eb972-311">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="eb972-312">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp#object-serialization)bakın.</span><span class="sxs-lookup"><span data-stu-id="eb972-312">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="eb972-313">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="eb972-313">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="eb972-314">MessagePack protokolü, öğesinin değerini kodlamak için bir yol sağlamaz `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="eb972-314">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="eb972-315">Sonuç olarak, bir tarih serisi kaldırılırken, MessagePack hub protokolü gelen tarihin UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="eb972-315">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="eb972-316">`DateTime`Yerel saat içinde değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="eb972-316">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="eb972-317">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="eb972-317">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="eb972-318">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ :::no-loc(SignalR)::: #2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="eb972-318">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2632](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="eb972-319">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="eb972-319">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="eb972-320">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) Kitaplığı :::no-loc(SignalR)::: `timestamp96` MessagePack içindeki türü desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="eb972-320">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the :::no-loc(SignalR)::: JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="eb972-321">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="eb972-321">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="eb972-322">Değeri, `DateTime.MinValue` `January 1, 0001` bir değerde kodlanmalıdır `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="eb972-322">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="eb972-323">Bu nedenle, `DateTime.MinValue` JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="eb972-323">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="eb972-324">`DateTime.MinValue`JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="eb972-324">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="eb972-325">Genellikle, `DateTime.MinValue` "eksik" veya değeri kodlamak için kullanılır `null` .</span><span class="sxs-lookup"><span data-stu-id="eb972-325">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="eb972-326">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir bir `DateTime` değer ( `DateTime?` ) kullanın veya `bool` tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="eb972-326">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="eb972-327">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ :::no-loc(SignalR)::: #2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="eb972-327">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2228](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="eb972-328">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="eb972-328">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="eb972-329">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="eb972-329">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="eb972-330">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="eb972-330">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="eb972-331">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="eb972-331">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="eb972-332">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)bakın.</span><span class="sxs-lookup"><span data-stu-id="eb972-332">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="eb972-333">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="eb972-333">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="eb972-334">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="eb972-334">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="eb972-335">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="eb972-335">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="eb972-336">Örneğin, gelen nesnenin bir sayı olan () bir özellik değeri varsa `{ foo: 42 }` , ancak .net sınıfındaki özelliği tür ise `string` , değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="eb972-336">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="eb972-337">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="eb972-337">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="eb972-338">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ :::no-loc(SignalR)::: #2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="eb972-338">For more information on this limitation, see GitHub issue [aspnet/:::no-loc(SignalR):::#2937](https://github.com/aspnet/:::no-loc(SignalR):::/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="eb972-339">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="eb972-339">Related resources</span></span>

* [<span data-ttu-id="eb972-340">Başlarken</span><span class="sxs-lookup"><span data-stu-id="eb972-340">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="eb972-341">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="eb972-341">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="eb972-342">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="eb972-342">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
