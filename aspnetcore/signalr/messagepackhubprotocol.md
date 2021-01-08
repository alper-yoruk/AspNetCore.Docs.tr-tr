---
title: ASP.NET Core için ' de MessagePack hub protokolünü kullanın SignalR
author: bradygaster
description: ASP.NET Core için MessagePack hub protokolünü ekleyin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: 7c3640e9cd2c5d392400a115813584861f789554
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024697"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="7e8bf-103">ASP.NET Core için ' de MessagePack hub protokolünü kullanın SignalR</span><span class="sxs-lookup"><span data-stu-id="7e8bf-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7e8bf-104">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="7e8bf-105">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="7e8bf-105">What is MessagePack?</span></span>

<span data-ttu-id="7e8bf-106">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="7e8bf-107">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="7e8bf-108">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="7e8bf-109">SignalR , MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="7e8bf-110">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7e8bf-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="7e8bf-111">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="7e8bf-112">`Startup.ConfigureServices`Yönteminde, `AddMessagePackProtocol` `AddSignalR` sunucuda MessagePack desteğini etkinleştirmek için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="7e8bf-113">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-113">JSON is enabled by default.</span></span> <span data-ttu-id="7e8bf-114">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="7e8bf-115">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="7e8bf-116">Bu temsilcisinde, `SerializerOptions` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="7e8bf-117">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="7e8bf-118">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="7e8bf-119">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="7e8bf-120">Örneğin, öğesini `.WithSecurity(MessagePackSecurity.UntrustedData)` değiştirirken çağırma `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="7e8bf-121">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7e8bf-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="7e8bf-122">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="7e8bf-123">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="7e8bf-124">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="7e8bf-125">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="7e8bf-125">.NET client</span></span>

<span data-ttu-id="7e8bf-126">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip `AddMessagePackProtocol` üzerine çağırın `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="7e8bf-127">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="7e8bf-128">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="7e8bf-128">JavaScript client</span></span>

<span data-ttu-id="7e8bf-129">JavaScript istemcisi için MessagePack desteği [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="7e8bf-130">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="7e8bf-131">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="7e8bf-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="7e8bf-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="7e8bf-133">Bir tarayıcıda, `msgpack5` Kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="7e8bf-134">`<script>`Başvuru oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="7e8bf-135">Kitaplık *node_modules\msgpack5\dist\msgpack5.js* bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="7e8bf-136">`<script>`Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="7e8bf-137">*msgpack5.js* önce *signalr-protocol-msgpack.js* başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="7e8bf-138">*signalr-protocol-msgpack.js* önce de *signalr.js* gerekir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="7e8bf-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Uygulamasına eklemek, `HubConnectionBuilder` istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="7e8bf-140">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

### <a name="java-client"></a><span data-ttu-id="7e8bf-141">Java istemcisi</span><span class="sxs-lookup"><span data-stu-id="7e8bf-141">Java client</span></span>

<span data-ttu-id="7e8bf-142">Bir Java ile MessagePack 'i etkinleştirmek için paketini yükledikten sonra `com.microsoft.signalr.messagepack` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-142">To enable MessagePack with Java, install the `com.microsoft.signalr.messagepack` package.</span></span> <span data-ttu-id="7e8bf-143">Gradle kullanırken, `dependencies` *Build. Gradle* dosyasının bölümüne aşağıdaki satırı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-143">When using Gradle, add the following line to the `dependencies` section of the *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

<span data-ttu-id="7e8bf-144">Maven kullanırken, `<dependencies>` *pom.xml* dosyanın öğesinin içine aşağıdaki satırları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-144">When using Maven, add the following lines inside the `<dependencies>` element of the *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

<span data-ttu-id="7e8bf-145">Üzerinde arama yapın `withHubProtocol(new MessagePackHubProtocol())` `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-145">Call `withHubProtocol(new MessagePackHubProtocol())` on `HubConnectionBuilder`.</span></span>

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a><span data-ttu-id="7e8bf-146">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="7e8bf-146">MessagePack quirks</span></span>

<span data-ttu-id="7e8bf-147">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-147">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="7e8bf-148">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="7e8bf-148">MessagePack is case-sensitive</span></span>

<span data-ttu-id="7e8bf-149">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-149">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="7e8bf-150">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-150">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="7e8bf-151">JavaScript istemcisinden gönderirken, büyük/ `PascalCased` küçük harf C# sınıfıyla tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-151">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="7e8bf-152">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-152">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="7e8bf-153">`camelCased`Adların kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-153">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="7e8bf-154">`Key`MessagePack özelliği için farklı bir ad belirtmek üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-154">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="7e8bf-155">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp#object-serialization)bakın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-155">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="7e8bf-156">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="7e8bf-156">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="7e8bf-157">MessagePack protokolü, öğesinin değerini kodlamak için bir yol sağlamaz `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-157">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="7e8bf-158">Sonuç olarak, bir tarihin serisi kaldırılırken MessagePack hub Protokolü UTC biçimine dönüştürülür, `DateTime.Kind` `DateTimeKind.Local` Aksi takdirde, zaman içinde dokunmaz ve olduğu gibi geçireceğiz.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-158">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="7e8bf-159">`DateTime`Değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-159">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="7e8bf-160">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-160">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="7e8bf-161">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="7e8bf-161">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="7e8bf-162">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) Kitaplığı SignalR `timestamp96` MessagePack içindeki türü desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-162">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="7e8bf-163">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="7e8bf-163">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="7e8bf-164">Değeri `DateTime.MinValue` `January 1, 0001` , bir değerde kodlanmalıdır `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-164">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="7e8bf-165">Bu nedenle, `DateTime.MinValue` JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-165">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="7e8bf-166">`DateTime.MinValue`JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-166">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="7e8bf-167">Genellikle, `DateTime.MinValue` "eksik" veya değeri kodlamak için kullanılır `null` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-167">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="7e8bf-168">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir bir `DateTime` değer ( `DateTime?` ) kullanın veya `bool` tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-168">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="7e8bf-169">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="7e8bf-169">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="7e8bf-170">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="7e8bf-170">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="7e8bf-171">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-171">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="7e8bf-172">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-172">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="7e8bf-173">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-173">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="7e8bf-174">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)bakın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-174">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="7e8bf-175">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="7e8bf-175">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="7e8bf-176">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="7e8bf-176">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="7e8bf-177">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-177">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="7e8bf-178">Örneğin, gelen nesnenin bir sayı olan () bir özellik değeri varsa `{ foo: 42 }` , ancak .net sınıfındaki özelliği tür ise `string` , değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-178">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="7e8bf-179">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-179">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="7e8bf-180">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="7e8bf-180">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

### <a name="chars-and-strings-in-java"></a><span data-ttu-id="7e8bf-181">Java 'daki karakterler ve dizeler</span><span class="sxs-lookup"><span data-stu-id="7e8bf-181">Chars and Strings in Java</span></span>

<span data-ttu-id="7e8bf-182">Java istemcisinde `char` nesneler tek karakterli nesneler olarak serileştirilir `String` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-182">In the java client, `char` objects will be serialized as one-character `String` objects.</span></span> <span data-ttu-id="7e8bf-183">Bu, C# ve JavaScript istemcisiyle aynı şekilde, nesneleri nesneler olarak serileştirilir `short` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-183">This is in contrast with the C# and JavaScript client, which serialize them as `short` objects.</span></span> <span data-ttu-id="7e8bf-184">MessagePack spec 'in kendisi nesneler için davranış tanımlamıyor `char` , bu nedenle, bunların serileştirilme biçimini belirlemek için kitaplık yazarına kadar.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-184">The MessagePack spec itself does not define behavior for `char` objects, so it is up to the library author to determine how to serialize them.</span></span> <span data-ttu-id="7e8bf-185">İstemcilerimiz arasındaki davranış farkı, uygulamalarımız için kullandığımız kitaplıkların bir sonucudur.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-185">The difference in behavior between our clients is a result of the libraries we used for our implementations.</span></span>

## <a name="related-resources"></a><span data-ttu-id="7e8bf-186">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7e8bf-186">Related resources</span></span>

* [<span data-ttu-id="7e8bf-187">Başlarken</span><span class="sxs-lookup"><span data-stu-id="7e8bf-187">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="7e8bf-188">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="7e8bf-188">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="7e8bf-189">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="7e8bf-189">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="7e8bf-190">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-190">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="7e8bf-191">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="7e8bf-191">What is MessagePack?</span></span>

<span data-ttu-id="7e8bf-192">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-192">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="7e8bf-193">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-193">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="7e8bf-194">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-194">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="7e8bf-195">SignalR , MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-195">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="7e8bf-196">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7e8bf-196">Configure MessagePack on the server</span></span>

<span data-ttu-id="7e8bf-197">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-197">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="7e8bf-198">`Startup.ConfigureServices`Yönteminde, `AddMessagePackProtocol` `AddSignalR` sunucuda MessagePack desteğini etkinleştirmek için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-198">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="7e8bf-199">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-199">JSON is enabled by default.</span></span> <span data-ttu-id="7e8bf-200">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-200">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="7e8bf-201">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-201">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="7e8bf-202">Bu temsilcisinde, `FormatterResolvers` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-202">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="7e8bf-203">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-203">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="7e8bf-204">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-204">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="7e8bf-205">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-205">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="7e8bf-206">Örneğin, `MessagePackSecurity.Active` statik özelliği olarak ayarlanıyor `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-206">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="7e8bf-207">Öğesinin ayarlanması için `MessagePackSecurity.Active` [MessagePack 'in bir 1.9. x sürümünün](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-207">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="7e8bf-208">`MessagePack`Sürümün kullandığı 1.9. x yükseltmeleri yükleniyor SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-208">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="7e8bf-209">`MessagePack` sürüm 2. x, büyük değişiklikler sunmuştur ve SignalR 3,1 ve önceki sürümleriyle uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-209">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="7e8bf-210">`MessagePackSecurity.Active`, Olarak ayarlanmadıysa `MessagePackSecurity.UntrustedData` kötü amaçlı bir istemci hizmet reddine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-210">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="7e8bf-211">`MessagePackSecurity.Active` `Program.Main` Aşağıdaki kodda gösterildiği gibi içinde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-211">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="7e8bf-212">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7e8bf-212">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="7e8bf-213">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-213">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="7e8bf-214">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-214">Clients can only support a single protocol.</span></span> <span data-ttu-id="7e8bf-215">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-215">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="7e8bf-216">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="7e8bf-216">.NET client</span></span>

<span data-ttu-id="7e8bf-217">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip `AddMessagePackProtocol` üzerine çağırın `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-217">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="7e8bf-218">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-218">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="7e8bf-219">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="7e8bf-219">JavaScript client</span></span>

<span data-ttu-id="7e8bf-220">JavaScript istemcisi için MessagePack desteği [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-220">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="7e8bf-221">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-221">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="7e8bf-222">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-222">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="7e8bf-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="7e8bf-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="7e8bf-224">Bir tarayıcıda, `msgpack5` Kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-224">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="7e8bf-225">`<script>`Başvuru oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-225">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="7e8bf-226">Kitaplık *node_modules\msgpack5\dist\msgpack5.js* bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-226">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="7e8bf-227">`<script>`Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-227">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="7e8bf-228">*msgpack5.js* önce *signalr-protocol-msgpack.js* başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-228">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="7e8bf-229">*signalr-protocol-msgpack.js* önce de *signalr.js* gerekir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-229">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="7e8bf-230">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Uygulamasına eklemek, `HubConnectionBuilder` istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-230">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="7e8bf-231">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-231">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="7e8bf-232">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="7e8bf-232">MessagePack quirks</span></span>

<span data-ttu-id="7e8bf-233">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-233">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="7e8bf-234">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="7e8bf-234">MessagePack is case-sensitive</span></span>

<span data-ttu-id="7e8bf-235">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-235">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="7e8bf-236">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-236">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="7e8bf-237">JavaScript istemcisinden gönderirken, büyük/ `PascalCased` küçük harf C# sınıfıyla tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-237">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="7e8bf-238">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-238">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="7e8bf-239">`camelCased`Adların kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-239">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="7e8bf-240">`Key`MessagePack özelliği için farklı bir ad belirtmek üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-240">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="7e8bf-241">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp#object-serialization)bakın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-241">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="7e8bf-242">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="7e8bf-242">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="7e8bf-243">MessagePack protokolü, öğesinin değerini kodlamak için bir yol sağlamaz `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-243">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="7e8bf-244">Sonuç olarak, bir tarih serisi kaldırılırken, MessagePack hub protokolü gelen tarihin UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-244">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="7e8bf-245">`DateTime`Yerel saat içinde değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-245">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="7e8bf-246">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-246">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="7e8bf-247">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="7e8bf-247">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="7e8bf-248">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="7e8bf-248">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="7e8bf-249">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) Kitaplığı SignalR `timestamp96` MessagePack içindeki türü desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-249">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="7e8bf-250">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="7e8bf-250">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="7e8bf-251">Değeri `DateTime.MinValue` `January 1, 0001` , bir değerde kodlanmalıdır `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-251">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="7e8bf-252">Bu nedenle, `DateTime.MinValue` JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-252">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="7e8bf-253">`DateTime.MinValue`JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-253">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="7e8bf-254">Genellikle, `DateTime.MinValue` "eksik" veya değeri kodlamak için kullanılır `null` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-254">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="7e8bf-255">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir bir `DateTime` değer ( `DateTime?` ) kullanın veya `bool` tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-255">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="7e8bf-256">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="7e8bf-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="7e8bf-257">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="7e8bf-257">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="7e8bf-258">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-258">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="7e8bf-259">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-259">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="7e8bf-260">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-260">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="7e8bf-261">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)bakın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-261">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="7e8bf-262">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="7e8bf-262">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="7e8bf-263">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="7e8bf-263">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="7e8bf-264">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-264">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="7e8bf-265">Örneğin, gelen nesnenin bir sayı olan () bir özellik değeri varsa `{ foo: 42 }` , ancak .net sınıfındaki özelliği tür ise `string` , değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-265">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="7e8bf-266">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-266">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="7e8bf-267">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="7e8bf-267">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="7e8bf-268">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7e8bf-268">Related resources</span></span>

* [<span data-ttu-id="7e8bf-269">Başlarken</span><span class="sxs-lookup"><span data-stu-id="7e8bf-269">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="7e8bf-270">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="7e8bf-270">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="7e8bf-271">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="7e8bf-271">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7e8bf-272">Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-272">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="7e8bf-273">MessagePack nedir?</span><span class="sxs-lookup"><span data-stu-id="7e8bf-273">What is MessagePack?</span></span>

<span data-ttu-id="7e8bf-274">[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-274">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="7e8bf-275">Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-275">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="7e8bf-276">Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-276">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="7e8bf-277">SignalR , MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-277">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="7e8bf-278">Sunucuda MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7e8bf-278">Configure MessagePack on the server</span></span>

<span data-ttu-id="7e8bf-279">Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-279">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="7e8bf-280">`Startup.ConfigureServices`Yönteminde, `AddMessagePackProtocol` `AddSignalR` sunucuda MessagePack desteğini etkinleştirmek için çağrıya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-280">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="7e8bf-281">JSON varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-281">JSON is enabled by default.</span></span> <span data-ttu-id="7e8bf-282">MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-282">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="7e8bf-283">MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-283">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="7e8bf-284">Bu temsilcisinde, `FormatterResolvers` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-284">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="7e8bf-285">Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-285">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="7e8bf-286">Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-286">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="7e8bf-287">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-287">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="7e8bf-288">Örneğin, `MessagePackSecurity.Active` statik özelliği olarak ayarlanıyor `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-288">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="7e8bf-289">Öğesinin ayarlanması için `MessagePackSecurity.Active` [MessagePack 'in bir 1.9. x sürümünün](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-289">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="7e8bf-290">`MessagePack`Sürümün kullandığı 1.9. x yükseltmeleri yükleniyor SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-290">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="7e8bf-291">`MessagePackSecurity.Active`, Olarak ayarlanmamışsa `MessagePackSecurity.UntrustedData` kötü amaçlı bir istemci hizmet reddine neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-291">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="7e8bf-292">`MessagePackSecurity.Active` `Program.Main` Aşağıdaki kodda gösterildiği gibi içinde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-292">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="7e8bf-293">İstemcide MessagePack 'i yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7e8bf-293">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="7e8bf-294">JSON, desteklenen istemciler için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-294">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="7e8bf-295">İstemciler yalnızca tek bir protokolü destekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-295">Clients can only support a single protocol.</span></span> <span data-ttu-id="7e8bf-296">MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-296">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="7e8bf-297">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="7e8bf-297">.NET client</span></span>

<span data-ttu-id="7e8bf-298">.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip `AddMessagePackProtocol` üzerine çağırın `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-298">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="7e8bf-299">Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-299">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="7e8bf-300">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="7e8bf-300">JavaScript client</span></span>

<span data-ttu-id="7e8bf-301">JavaScript istemcisi için MessagePack desteği [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-301">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="7e8bf-302">Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-302">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="7e8bf-303">NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-303">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="7e8bf-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="7e8bf-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="7e8bf-305">Bir tarayıcıda, `msgpack5` Kitaplığa da başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-305">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="7e8bf-306">`<script>`Başvuru oluşturmak için bir etiket kullanın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-306">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="7e8bf-307">Kitaplık *node_modules\msgpack5\dist\msgpack5.js* bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-307">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="7e8bf-308">`<script>`Öğesi kullanılırken, sıra önemlidir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-308">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="7e8bf-309">*msgpack5.js* önce *signalr-protocol-msgpack.js* başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-309">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="7e8bf-310">*signalr-protocol-msgpack.js* önce de *signalr.js* gerekir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-310">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="7e8bf-311">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Uygulamasına eklemek, `HubConnectionBuilder` istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-311">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="7e8bf-312">Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-312">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="7e8bf-313">MessagePack süslemeler</span><span class="sxs-lookup"><span data-stu-id="7e8bf-313">MessagePack quirks</span></span>

<span data-ttu-id="7e8bf-314">MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-314">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="7e8bf-315">MessagePack büyük/küçük harfe duyarlıdır</span><span class="sxs-lookup"><span data-stu-id="7e8bf-315">MessagePack is case-sensitive</span></span>

<span data-ttu-id="7e8bf-316">MessagePack Protokolü büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-316">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="7e8bf-317">Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-317">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="7e8bf-318">JavaScript istemcisinden gönderirken, büyük/ `PascalCased` küçük harf C# sınıfıyla tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-318">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="7e8bf-319">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-319">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="7e8bf-320">`camelCased`Adların kullanılması C# sınıfına düzgün şekilde bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-320">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="7e8bf-321">`Key`MessagePack özelliği için farklı bir ad belirtmek üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-321">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="7e8bf-322">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp#object-serialization)bakın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-322">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="7e8bf-323">Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz</span><span class="sxs-lookup"><span data-stu-id="7e8bf-323">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="7e8bf-324">MessagePack protokolü, öğesinin değerini kodlamak için bir yol sağlamaz `Kind` `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-324">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="7e8bf-325">Sonuç olarak, bir tarih serisi kaldırılırken, MessagePack hub protokolü gelen tarihin UTC biçiminde olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-325">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="7e8bf-326">`DateTime`Yerel saat içinde değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-326">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="7e8bf-327">Bunları aldığınızda UTC 'den yerel saate dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-327">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="7e8bf-328">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="7e8bf-328">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="7e8bf-329">DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor</span><span class="sxs-lookup"><span data-stu-id="7e8bf-329">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="7e8bf-330">JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) Kitaplığı SignalR `timestamp96` MessagePack içindeki türü desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-330">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="7e8bf-331">Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken).</span><span class="sxs-lookup"><span data-stu-id="7e8bf-331">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="7e8bf-332">Değeri, `DateTime.MinValue` `January 1, 0001` bir değerde kodlanmalıdır `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-332">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="7e8bf-333">Bu nedenle, `DateTime.MinValue` JavaScript istemcisine gönderme desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-333">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="7e8bf-334">`DateTime.MinValue`JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-334">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="7e8bf-335">Genellikle, `DateTime.MinValue` "eksik" veya değeri kodlamak için kullanılır `null` .</span><span class="sxs-lookup"><span data-stu-id="7e8bf-335">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="7e8bf-336">Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir bir `DateTime` değer ( `DateTime?` ) kullanın veya `bool` tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-336">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="7e8bf-337">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="7e8bf-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="7e8bf-338">"Zamanında" derleme ortamında MessagePack desteği</span><span class="sxs-lookup"><span data-stu-id="7e8bf-338">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="7e8bf-339">.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-339">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="7e8bf-340">Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-340">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="7e8bf-341">Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-341">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="7e8bf-342">Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)bakın.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-342">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="7e8bf-343">Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="7e8bf-343">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="7e8bf-344">Tür denetimleri MessagePack 'te daha sıkı</span><span class="sxs-lookup"><span data-stu-id="7e8bf-344">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="7e8bf-345">JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-345">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="7e8bf-346">Örneğin, gelen nesnenin bir sayı olan () bir özellik değeri varsa `{ foo: 42 }` , ancak .net sınıfındaki özelliği tür ise `string` , değer dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="7e8bf-346">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="7e8bf-347">Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7e8bf-347">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="7e8bf-348">Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="7e8bf-348">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="7e8bf-349">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7e8bf-349">Related resources</span></span>

* [<span data-ttu-id="7e8bf-350">Başlarken</span><span class="sxs-lookup"><span data-stu-id="7e8bf-350">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="7e8bf-351">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="7e8bf-351">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="7e8bf-352">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="7e8bf-352">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
