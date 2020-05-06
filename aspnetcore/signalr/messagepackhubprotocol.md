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
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a>ASP.NET Core için ' de SignalR MessagePack hub protokolünü kullanın

::: moniker range=">= aspnetcore-5.0"

Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.

## <a name="what-is-messagepack"></a>MessagePack nedir?

[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir. Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır. Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz. SignalR, MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.

## <a name="configure-messagepack-on-the-server"></a>Sunucuda MessagePack 'i yapılandırma

Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın. `Startup.ConfigureServices` Yönteminde, sunucuda MessagePack `AddMessagePackProtocol` desteğini etkinleştirmek `AddSignalR` için çağrıya ekleyin.

> [!NOTE]
> JSON varsayılan olarak etkindir. MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır. Bu temsilcisinde, `SerializerOptions` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir. Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin. Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.

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
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz. Örneğin, öğesini değiştirirken `.WithSecurity(MessagePackSecurity.UntrustedData)` çağırma `SerializerOptions`.

## <a name="configure-messagepack-on-the-client"></a>İstemcide MessagePack 'i yapılandırma

> [!NOTE]
> JSON, desteklenen istemciler için varsayılan olarak etkindir. İstemciler yalnızca tek bir protokolü destekleyebilir. MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.

### <a name="net-client"></a>.NET istemcisi

.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip üzerine `AddMessagePackProtocol` `HubConnectionBuilder`çağırın.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.

### <a name="javascript-client"></a>JavaScript istemcisi

JavaScript istemcisi için MessagePack desteği [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır. Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

Bir tarayıcıda, `msgpack5` kitaplığa da başvurulmalıdır. Başvuru oluşturmak `<script>` için bir etiket kullanın. Kitaplık, *node_modules \msgpack5\dist\msgpack5,JS*konumunda bulunabilir.

> [!NOTE]
> `<script>` Öğesi kullanılırken, sıra önemlidir. *SignalR-Protocol-msgpack. js* ' *msgpack5. js*' den önce başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur. *SignalR. js* , *SignalR-Protocol-msgpack. js*' den önce de gereklidir.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Uygulamasına `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` eklemek, Istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.

## <a name="messagepack-quirks"></a>MessagePack süslemeler

MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.

### <a name="messagepack-is-case-sensitive"></a>MessagePack büyük/küçük harfe duyarlıdır

MessagePack Protokolü büyük/küçük harfe duyarlıdır. Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript istemcisinden gönderirken, büyük/küçük harf C# sınıfıyla `PascalCased` tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir. Örneğin:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Adların `camelCased` kullanılması C# sınıfına düzgün şekilde bağlanmaz. MessagePack özelliği için farklı bir ad belirtmek `Key` üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz. Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz

MessagePack protokolü, öğesinin `Kind` değerini kodlamak için bir yol sağlamaz. `DateTime` Sonuç olarak, bir tarihin serisi kaldırılırken MessagePack hub Protokolü UTC biçimine `DateTime.Kind` dönüştürülür, `DateTimeKind.Local` Aksi takdirde, zaman içinde dokunmaz ve olduğu gibi geçireceğiz. `DateTime` Değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir. Bunları aldığınızda UTC 'den yerel saate dönüştürün.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor

JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) kitaplığı MessagePack içindeki `timestamp96` türü desteklemiyor. SignalR Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken). `DateTime.MinValue` Değeri `January 1, 0001`, bir `timestamp96` değerde kodlanmalıdır. Bu nedenle, JavaScript istemcisine `DateTime.MinValue` gönderme desteklenmez. `DateTime.MinValue` JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Genellikle, `DateTime.MinValue` "eksik" veya `null` değeri kodlamak için kullanılır. Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir `DateTime` bir değer (`DateTime?`) kullanın veya tarihin mevcut olup olmadığını belirten `bool` ayrı bir değer kodlayın.

Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"Zamanında" derleme ortamında MessagePack desteği

.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır. Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez. Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür. Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol`:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Tür denetimleri MessagePack 'te daha sıkı

JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek. Örneğin, gelen nesnenin bir sayı olan (`{ foo: 42 }`) bir özellik değeri varsa, ancak .net sınıfındaki özelliği tür `string`ise, değer dönüştürülür. Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>İlgili kaynaklar

* [Kullanmaya başlayın](xref:tutorials/signalr)
* [.NET istemcisi](xref:signalr/dotnet-client)
* [JavaScript istemcisi](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.

## <a name="what-is-messagepack"></a>MessagePack nedir?

[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir. Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır. Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz. SignalR, MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.

## <a name="configure-messagepack-on-the-server"></a>Sunucuda MessagePack 'i yapılandırma

Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın. `Startup.ConfigureServices` Yönteminde, sunucuda MessagePack `AddMessagePackProtocol` desteğini etkinleştirmek `AddSignalR` için çağrıya ekleyin.

> [!NOTE]
> JSON varsayılan olarak etkindir. MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır. Bu temsilcisinde, `FormatterResolvers` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir. Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin. Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.

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
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz. Örneğin, `MessagePackSecurity.Active` statik özelliği olarak `MessagePackSecurity.UntrustedData`ayarlanıyor. Öğesinin ayarlanması `MessagePackSecurity.Active` Için [MessagePack 'in bir 1.9. x sürümünün](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklenmesi gerekir. Sürümün `MessagePack` SignalR kullandığı 1.9. x yükseltmeleri yükleniyor. `MessagePackSecurity.Active` , Olarak `MessagePackSecurity.UntrustedData`ayarlanmamışsa kötü amaçlı bir istemci hizmet reddine neden olabilir. Aşağıdaki `MessagePackSecurity.Active` kodda `Program.Main`gösterildiği gibi içinde ayarlanır:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>İstemcide MessagePack 'i yapılandırma

> [!NOTE]
> JSON, desteklenen istemciler için varsayılan olarak etkindir. İstemciler yalnızca tek bir protokolü destekleyebilir. MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.

### <a name="net-client"></a>.NET istemcisi

.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip üzerine `AddMessagePackProtocol` `HubConnectionBuilder`çağırın.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.

### <a name="javascript-client"></a>JavaScript istemcisi

JavaScript istemcisi için MessagePack desteği [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır. Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

Bir tarayıcıda, `msgpack5` kitaplığa da başvurulmalıdır. Başvuru oluşturmak `<script>` için bir etiket kullanın. Kitaplık, *node_modules \msgpack5\dist\msgpack5,JS*konumunda bulunabilir.

> [!NOTE]
> `<script>` Öğesi kullanılırken, sıra önemlidir. *SignalR-Protocol-msgpack. js* ' *msgpack5. js*' den önce başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur. *SignalR. js* , *SignalR-Protocol-msgpack. js*' den önce de gereklidir.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Uygulamasına `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` eklemek, Istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.

## <a name="messagepack-quirks"></a>MessagePack süslemeler

MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.

### <a name="messagepack-is-case-sensitive"></a>MessagePack büyük/küçük harfe duyarlıdır

MessagePack Protokolü büyük/küçük harfe duyarlıdır. Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript istemcisinden gönderirken, büyük/küçük harf C# sınıfıyla `PascalCased` tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir. Örneğin:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Adların `camelCased` kullanılması C# sınıfına düzgün şekilde bağlanmaz. MessagePack özelliği için farklı bir ad belirtmek `Key` üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz. Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz

MessagePack protokolü, öğesinin `Kind` değerini kodlamak için bir yol sağlamaz. `DateTime` Sonuç olarak, bir tarih serisi kaldırılırken, MessagePack hub protokolü gelen tarihin UTC biçiminde olduğunu varsayar. Yerel saat içinde `DateTime` değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir. Bunları aldığınızda UTC 'den yerel saate dönüştürün.

Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor

JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) kitaplığı MessagePack içindeki `timestamp96` türü desteklemiyor. SignalR Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken). `DateTime.MinValue` Değeri `January 1, 0001`, bir `timestamp96` değerde kodlanmalıdır. Bu nedenle, JavaScript istemcisine `DateTime.MinValue` gönderme desteklenmez. `DateTime.MinValue` JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Genellikle, `DateTime.MinValue` "eksik" veya `null` değeri kodlamak için kullanılır. Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir `DateTime` bir değer (`DateTime?`) kullanın veya tarihin mevcut olup olmadığını belirten `bool` ayrı bir değer kodlayın.

Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"Zamanında" derleme ortamında MessagePack desteği

.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır. Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez. Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür. Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol`:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Tür denetimleri MessagePack 'te daha sıkı

JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek. Örneğin, gelen nesnenin bir sayı olan (`{ foo: 42 }`) bir özellik değeri varsa, ancak .net sınıfındaki özelliği tür `string`ise, değer dönüştürülür. Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>İlgili kaynaklar

* [Kullanmaya başlayın](xref:tutorials/signalr)
* [.NET istemcisi](xref:signalr/dotnet-client)
* [JavaScript istemcisi](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makalede, okuyucunun [Başlarken](xref:tutorials/signalr)bölümünde ele alınan konular hakkında bilgi sahibi olduğu varsayılır.

## <a name="what-is-messagepack"></a>MessagePack nedir?

[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir. Bu, [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği açısından yararlıdır. Baytlar bir MessagePack ayrıştırıcısı üzerinden geçirilmediği takdirde, ağ izlemeleri ve günlükleri aranırken ikili iletiler okunamaz. SignalR, MessagePack biçimi için yerleşik desteğe sahiptir ve istemci ve sunucunun kullanması için API 'Ler sağlar.

## <a name="configure-messagepack-on-the-server"></a>Sunucuda MessagePack 'i yapılandırma

Sunucuda MessagePack hub protokolünü etkinleştirmek için, `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketi uygulamanıza takın. `Startup.ConfigureServices` Yönteminde, sunucuda MessagePack `AddMessagePackProtocol` desteğini etkinleştirmek `AddSignalR` için çağrıya ekleyin.

> [!NOTE]
> JSON varsayılan olarak etkindir. MessagePack eklemek, hem JSON hem de MessagePack istemcileri için destek sunar.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack 'in verilerinizi nasıl biçimlendiğini özelleştirmek için, `AddMessagePackProtocol` seçenekleri yapılandırmak için bir temsilci alır. Bu temsilcisinde, `FormatterResolvers` özelliği MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir. Çözümleyiciler nasıl çalıştığı hakkında daha fazla bilgi için, [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)konumundaki MessagePack kitaplığını ziyaret edin. Öznitelikler, serileştirilmeleri nasıl ele alınacağını tanımlamak için seri hale getirmek istediğiniz nesnelerde kullanılabilir.

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
> [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) ' i gözden geçirmeyi ve önerilen düzeltme eklerini uygulamayı kesinlikle öneririz. Örneğin, `MessagePackSecurity.Active` statik özelliği olarak `MessagePackSecurity.UntrustedData`ayarlanıyor. Öğesinin ayarlanması `MessagePackSecurity.Active` Için [MessagePack 'in bir 1.9. x sürümünün](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklenmesi gerekir. Sürümün `MessagePack` SignalR kullandığı 1.9. x yükseltmeleri yükleniyor. `MessagePackSecurity.Active` , Olarak `MessagePackSecurity.UntrustedData`ayarlanmamışsa kötü amaçlı bir istemci hizmet reddine neden olabilir. Aşağıdaki `MessagePackSecurity.Active` kodda `Program.Main`gösterildiği gibi içinde ayarlanır:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>İstemcide MessagePack 'i yapılandırma

> [!NOTE]
> JSON, desteklenen istemciler için varsayılan olarak etkindir. İstemciler yalnızca tek bir protokolü destekleyebilir. MessagePack desteği eklemek, önceden yapılandırılmış tüm protokollerin yerini alır.

### <a name="net-client"></a>.NET istemcisi

.NET Istemcisinde MessagePack 'i etkinleştirmek için `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` paketini yükleyip üzerine `AddMessagePackProtocol` `HubConnectionBuilder`çağırın.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.

### <a name="javascript-client"></a>JavaScript istemcisi

JavaScript istemcisi için MessagePack desteği [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) NPM paketi tarafından sağlanmaktadır. Aşağıdaki komutu bir komut kabuğu 'nda yürüterek paketi yüklemelisiniz:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

NPM paketini yükledikten sonra modül, doğrudan bir JavaScript Modül Yükleyicisi aracılığıyla veya aşağıdaki dosyaya başvurarak tarayıcıya içeri aktarılabilecek şekilde kullanılabilir:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

Bir tarayıcıda, `msgpack5` kitaplığa da başvurulmalıdır. Başvuru oluşturmak `<script>` için bir etiket kullanın. Kitaplık, *node_modules \msgpack5\dist\msgpack5,JS*konumunda bulunabilir.

> [!NOTE]
> `<script>` Öğesi kullanılırken, sıra önemlidir. *SignalR-Protocol-msgpack. js* ' *msgpack5. js*' den önce başvuruluyorsa, MessagePack ile bağlantı kurmaya çalışırken bir hata oluşur. *SignalR. js* , *SignalR-Protocol-msgpack. js*' den önce de gereklidir.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Uygulamasına `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` eklemek, Istemcisini bir sunucuya bağlanırken MessagePack protokolünü kullanacak şekilde yapılandırır.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Şu anda JavaScript istemcisinde MessagePack protokolü için yapılandırma seçeneği yoktur.

## <a name="messagepack-quirks"></a>MessagePack süslemeler

MessagePack hub protokolünü kullanırken dikkat etmeniz birkaç sorun vardır.

### <a name="messagepack-is-case-sensitive"></a>MessagePack büyük/küçük harfe duyarlıdır

MessagePack Protokolü büyük/küçük harfe duyarlıdır. Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript istemcisinden gönderirken, büyük/küçük harf C# sınıfıyla `PascalCased` tam olarak eşleşmesi gerektiğinden özellik adlarını kullanmanız gerekir. Örneğin:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Adların `camelCased` kullanılması C# sınıfına düzgün şekilde bağlanmaz. MessagePack özelliği için farklı bir ad belirtmek `Key` üzere özniteliğini kullanarak bu soruna geçici bir çözüm bulabilirsiniz. Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>Seri hale getirme/seri durumdan çıkarma sırasında DateTime. Kind korunmaz

MessagePack protokolü, öğesinin `Kind` değerini kodlamak için bir yol sağlamaz. `DateTime` Sonuç olarak, bir tarih serisi kaldırılırken, MessagePack hub protokolü gelen tarihin UTC biçiminde olduğunu varsayar. Yerel saat içinde `DateTime` değerlerle çalışıyorsanız, göndermeden önce UTC 'ye dönüştürmeniz önerilir. Bunları aldığınızda UTC 'den yerel saate dönüştürün.

Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime. MinValue, JavaScript 'te MessagePack tarafından desteklenmiyor

JavaScript istemcisi tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) kitaplığı MessagePack içindeki `timestamp96` türü desteklemiyor. SignalR Bu tür, çok büyük tarih değerlerini kodlamak için kullanılır (daha önce geçmişte veya daha önce bir süre içinde çok erken). Değeri `DateTime.MinValue` `January 1, 0001` , bir `timestamp96` değerde kodlanmalıdır. Bu nedenle, JavaScript istemcisine `DateTime.MinValue` gönderme desteklenmez. `DateTime.MinValue` JavaScript istemcisi tarafından alındığında, aşağıdaki hata oluşur:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Genellikle, `DateTime.MinValue` "eksik" veya `null` değeri kodlamak için kullanılır. Bu değeri MessagePack 'te kodlamak gerekirse, null yapılabilir `DateTime` bir değer (`DateTime?`) kullanın veya tarihin mevcut olup olmadığını belirten `bool` ayrı bir değer kodlayın.

Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"Zamanında" derleme ortamında MessagePack desteği

.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirme işlemini iyileştirmek için kod oluşturmayı kullanır. Sonuç olarak, "güncel olmayan" derleme (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez. Seri hale getirici/seri hale getirici kodunu "önceden oluşturma" yoluyla bu ortamlarda MessagePack kullanmak mümkündür. Daha fazla bilgi için bkz. [MessagePack-CSharp belgeleri](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Serileştiriciler önceden oluşturulduktan sonra, geçirilen yapılandırma temsilcisini kullanarak bunları kaydedebilirsiniz `AddMessagePackProtocol`:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Tür denetimleri MessagePack 'te daha sıkı

JSON hub 'ı protokolü, seri durumdan çıkarma sırasında tür dönüştürmeleri gerçekleştirecek. Örneğin, gelen nesnenin bir sayı olan (`{ foo: 42 }`) bir özellik değeri varsa, ancak .net sınıfındaki özelliği tür `string`ise, değer dönüştürülür. Ancak, MessagePack bu dönüştürmeyi gerçekleştirmez ve sunucu tarafı günlüklerinde (ve konsolunda) görünebileceğini bir özel durum oluşturur:

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Bu sınırlama hakkında daha fazla bilgi için bkz. GitHub sorun [ASPNETSignalR/#2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>İlgili kaynaklar

* [Kullanmaya başlayın](xref:tutorials/signalr)
* [.NET istemcisi](xref:signalr/dotnet-client)
* [JavaScript istemcisi](xref:signalr/javascript-client)

::: moniker-end
