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
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>MessagePack Hub Protokolünü ASP.NET Çekirdek SignalR için kullanma

::: moniker range=">= aspnetcore-5.0"

Bu makalede, okuyucu [nun Başlat'ta](xref:tutorials/signalr)kapsanan konulara aşina olduğunu varsayar.

## <a name="what-is-messagepack"></a>MessagePack nedir?

[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir. [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği bir sorun olduğunda yararlıdır. Baytlar MessagePack araörneğinden geçirilmedikçe, ağ izlemelerine ve günlüklerine bakarken ikili iletiler okunamaz. SignalRMessagePack biçimi için yerleşik destek vardır ve istemci ve sunucunun kullanması için API'ler sağlar.

## <a name="configure-messagepack-on-the-server"></a>MessagePack'i sunucuda yapılandırma

Sunucuda MessagePack Hub Protokolü'nü etkinleştirmek için paketi uygulamanıza yükleyin. `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` `Startup.ConfigureServices` Yöntemde, sunucuda `AddSignalR` MessagePack desteğini etkinleştirmek için çağrıya ekleyin. `AddMessagePackProtocol`

> [!NOTE]
> JSON varsayılan olarak etkinleştirilir. MessagePack eklemek hem JSON hem de MessagePack istemcileri için destek sağlar.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack'in verilerinizi nasıl biçimlendireceğini özelleştirmek için seçenekleri `AddMessagePackProtocol` yapılandırmak için bir temsilci alır. Bu temsilcide, `SerializerOptions` özellik MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir. Çözümleyicilerin nasıl çalıştığı hakkında daha fazla bilgi için [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)adresindeki MessagePack kitaplığını ziyaret edin. Öznitelikler, nasıl işlenmeleri gerektiğini tanımlamak için serileştirmek istediğiniz nesnelerde kullanılabilir.

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
> [CVE-2020-5234'ün](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) gözden geçirilmesini ve önerilen yamaları uygulamanızı önemle tavsiye ediyoruz. Örneğin, 'yi `.WithSecurity(MessagePackSecurity.UntrustedData)` `SerializerOptions`değiştirirken.

## <a name="configure-messagepack-on-the-client"></a>İleti Paketini istemcide yapılandır

> [!NOTE]
> JSON desteklenen istemciler için varsayılan olarak etkinleştirilir. İstemciler yalnızca tek bir protokolü destekleyebilir. MessagePack desteği eklemek, önceden yapılandırılmış protokollerin yerini alacaktır.

### <a name="net-client"></a>.NET istemcisi

MessagePack'i .NET İstemci'de etkinleştirmek için paketi yükleyin `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` ve çağırın. `AddMessagePackProtocol` `HubConnectionBuilder`

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.

### <a name="javascript-client"></a>JavaScript istemcisi

JavaScript istemcisi için MessagePack [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) desteği npm paketi tarafından sağlanır. Aşağıdaki komutu bir komut kabuğunda çalıştırarak paketi yükleyin:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

npm paketini yükledikten sonra, modül doğrudan bir JavaScript modül yükleyici saracılığıyla kullanılabilir veya aşağıdaki dosyaya başvurarak tarayıcıya aktarılabilir:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

Bir tarayıcıda, `msgpack5` kitaplık da başvurulmalıdır. Başvuru `<script>` oluşturmak için bir etiket kullanın. Kütüphane *node_modules\msgpack5\dist\msgpack5.js*adresinde bulunabilir.

> [!NOTE]
> Öğeyi `<script>` kullanırken, sipariş önemlidir. *Signalr-protocol-msgpack.js* *msgpack5.js*önce başvurulan ise, MessagePack ile bağlanmaya çalışırken bir hata oluşur. *signalr.js* de *sinyalci-protokol-msgpack.js*önce gereklidir.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Bir `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` sunucuya bağlanırken MessagePack iletişim kuralını kullanmak için istemciyi `HubConnectionBuilder` yapılandıracaktır.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Şu anda, JavaScript istemcisindeki MessagePack protokolü için yapılandırma seçeneği bulunmamaktadır.

## <a name="messagepack-quirks"></a>MessagePack tuhaflıklar

MessagePack Hub Protokolü'nü kullanırken dikkat edilmesi gereken birkaç sorun vardır.

### <a name="messagepack-is-case-sensitive"></a>MessagePack büyük/küçük harf duyarlıdır

MessagePack protokolü büyük/küçük harf duyarlıdır. Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript istemcisinden gönderirken, kasacın C# sınıfıyla tam olarak eşleşmesi gerektiğinden, özellik adlarını kullanmanız `PascalCased` gerekir. Örneğin:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Adkullanmak `camelCased` C# sınıfına düzgün bir şekilde bağlanmaz. MessagePack özelliği için farklı `Key` bir ad belirtmek için özniteliği kullanarak bu geçici olarak bu şekilde çalışabilirsiniz. Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp#object-serialization)bakın.

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind serileştirme/deserializing zaman korunmuş değildir

MessagePack protokolü `Kind` bir `DateTime`. Sonuç olarak, bir tarih inserializing zaman, MessagePack Hub Protokolü UTC `DateTime.Kind` `DateTimeKind.Local` biçimine aksi takdirde zaman dokunmaz ve olduğu gibi geçmek dönüştürür. Değerlerle `DateTime` çalışıyorsanız, göndermeden önce UTC'ye dönüştürmenizi öneririz. Bunları aldığınızda UTC'den yerel saate dönüştürün.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue JavaScript'te MessagePack tarafından desteklenmez

JavaScript istemcisi SignalR tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) kitaplığı MessagePack'teki `timestamp96` türü desteklemez. Bu tür çok büyük tarih değerlerini kodlamak için kullanılır (ya çok erken geçmişte ya da çok uzak gelecekte). Değeri `DateTime.MinValue` `January 1, 0001`, bir `timestamp96` değer olarak kodlanmalıdır. Bu nedenle, `DateTime.MinValue` bir JavaScript istemcisine gönderme desteklenmez. JavaScript istemcisi tarafından alındığı zaman, `DateTime.MinValue` aşağıdaki hata atılır:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Genellikle, `DateTime.MinValue` bir "eksik" veya `null` değer kodlamak için kullanılır. Bu değeri MessagePack'te kodlamanız gerekiyorsa, geçersiz `DateTime` bir`DateTime?`değer () `bool` kullanın veya tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.

Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [aspnet/SignalR#2228'ye](https://github.com/aspnet/SignalR/issues/2228)bakın.

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>MessagePack desteği "ileri-zaman" derleme ortamında

.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) kitaplığı, serileştirmeyi optimize etmek için kod oluşturma yı kullanır. Sonuç olarak, "zamanın ilerisi" derlemesi (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez. MessagePack'i bu ortamlarda serializer/deserializer kodunu "ön üreterek" kullanmak mümkündür. Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)bakın. Serializer'leri önceden oluşturduktan sonra, aşağıdakilere geçen yapılandırma `AddMessagePackProtocol`temsilcisini kullanarak bunları kaydedebilirsiniz:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack'te tür denetimleri daha katıdır

JSON Hub Protokolü deserialization sırasında tür dönüşümleri gerçekleştirecektir. Örneğin, gelen nesnenin bir sayı (`{ foo: 42 }`) olan bir özellik değeri varsa, ancak `string`.NET sınıfındaki özellik türündeyse, değer dönüştürülür. Ancak, MessagePack bu dönüşümü gerçekleştirmez ve sunucu tarafındaki günlüklerde (ve konsolda) görülebilecek bir özel durum oluşturur:

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [SignalRaspnet/ #2937'a](https://github.com/aspnet/SignalR/issues/2937)bakın.

## <a name="related-resources"></a>İlgili kaynaklar

* [Başlayın](xref:tutorials/signalr)
* [.NET istemcisi](xref:signalr/dotnet-client)
* [JavaScript istemcisi](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Bu makalede, okuyucu [nun Başlat'ta](xref:tutorials/signalr)kapsanan konulara aşina olduğunu varsayar.

## <a name="what-is-messagepack"></a>MessagePack nedir?

[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir. [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği bir sorun olduğunda yararlıdır. Baytlar MessagePack araörneğinden geçirilmedikçe, ağ izlemelerine ve günlüklerine bakarken ikili iletiler okunamaz. SignalRMessagePack biçimi için yerleşik destek vardır ve istemci ve sunucunun kullanması için API'ler sağlar.

## <a name="configure-messagepack-on-the-server"></a>MessagePack'i sunucuda yapılandırma

Sunucuda MessagePack Hub Protokolü'nü etkinleştirmek için paketi uygulamanıza yükleyin. `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` `Startup.ConfigureServices` Yöntemde, sunucuda `AddSignalR` MessagePack desteğini etkinleştirmek için çağrıya ekleyin. `AddMessagePackProtocol`

> [!NOTE]
> JSON varsayılan olarak etkinleştirilir. MessagePack eklemek hem JSON hem de MessagePack istemcileri için destek sağlar.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack'in verilerinizi nasıl biçimlendireceğini özelleştirmek için seçenekleri `AddMessagePackProtocol` yapılandırmak için bir temsilci alır. Bu temsilcide, `FormatterResolvers` özellik MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir. Çözümleyicilerin nasıl çalıştığı hakkında daha fazla bilgi için [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)adresindeki MessagePack kitaplığını ziyaret edin. Öznitelikler, nasıl işlenmeleri gerektiğini tanımlamak için serileştirmek istediğiniz nesnelerde kullanılabilir.

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
> [CVE-2020-5234'ün](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) gözden geçirilmesini ve önerilen yamaları uygulamanızı önemle tavsiye ediyoruz. Örneğin, `MessagePackSecurity.Active` statik özelliği ' `MessagePackSecurity.UntrustedData`ye ayarlama MessagePack'in `MessagePackSecurity.Active` [1.9.x sürümünü](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklemeyi gerektirir. Yükleme `MessagePack` 1.9.x sürümü SignalR kullanır yükseltir. Ne `MessagePackSecurity.Active` zaman ayarlanmaz `MessagePackSecurity.UntrustedData`, kötü niyetli bir istemci hizmet reddi neden olabilir. Aşağıdaki kodda gösterildiği gibi, `MessagePackSecurity.Active` `Program.Main`

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>İleti Paketini istemcide yapılandır

> [!NOTE]
> JSON desteklenen istemciler için varsayılan olarak etkinleştirilir. İstemciler yalnızca tek bir protokolü destekleyebilir. MessagePack desteği eklemek, önceden yapılandırılmış protokollerin yerini alacaktır.

### <a name="net-client"></a>.NET istemcisi

MessagePack'i .NET İstemci'de etkinleştirmek için paketi yükleyin `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` ve çağırın. `AddMessagePackProtocol` `HubConnectionBuilder`

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.

### <a name="javascript-client"></a>JavaScript istemcisi

JavaScript istemcisi için MessagePack [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) desteği npm paketi tarafından sağlanır. Aşağıdaki komutu bir komut kabuğunda çalıştırarak paketi yükleyin:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

npm paketini yükledikten sonra, modül doğrudan bir JavaScript modül yükleyici saracılığıyla kullanılabilir veya aşağıdaki dosyaya başvurarak tarayıcıya aktarılabilir:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

Bir tarayıcıda, `msgpack5` kitaplık da başvurulmalıdır. Başvuru `<script>` oluşturmak için bir etiket kullanın. Kütüphane *node_modules\msgpack5\dist\msgpack5.js*adresinde bulunabilir.

> [!NOTE]
> Öğeyi `<script>` kullanırken, sipariş önemlidir. *Signalr-protocol-msgpack.js* *msgpack5.js*önce başvurulan ise, MessagePack ile bağlanmaya çalışırken bir hata oluşur. *signalr.js* de *sinyalci-protokol-msgpack.js*önce gereklidir.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Bir `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` sunucuya bağlanırken MessagePack iletişim kuralını kullanmak için istemciyi `HubConnectionBuilder` yapılandıracaktır.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Şu anda, JavaScript istemcisindeki MessagePack protokolü için yapılandırma seçeneği bulunmamaktadır.

## <a name="messagepack-quirks"></a>MessagePack tuhaflıklar

MessagePack Hub Protokolü'nü kullanırken dikkat edilmesi gereken birkaç sorun vardır.

### <a name="messagepack-is-case-sensitive"></a>MessagePack büyük/küçük harf duyarlıdır

MessagePack protokolü büyük/küçük harf duyarlıdır. Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript istemcisinden gönderirken, kasacın C# sınıfıyla tam olarak eşleşmesi gerektiğinden, özellik adlarını kullanmanız `PascalCased` gerekir. Örneğin:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Adkullanmak `camelCased` C# sınıfına düzgün bir şekilde bağlanmaz. MessagePack özelliği için farklı `Key` bir ad belirtmek için özniteliği kullanarak bu geçici olarak bu şekilde çalışabilirsiniz. Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp#object-serialization)bakın.

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind serileştirme/deserializing zaman korunmuş değildir

MessagePack protokolü `Kind` bir `DateTime`. Sonuç olarak, bir tarih deserializing, MessagePack Hub Protokolü gelen tarih UTC biçiminde olduğunu varsayar. Yerel saatle değerlerle `DateTime` çalışıyorsanız, göndermeden önce UTC'ye dönüştürmenizi öneririz. Bunları aldığınızda UTC'den yerel saate dönüştürün.

Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [SignalRaspnet/ #2632'e](https://github.com/aspnet/SignalR/issues/2632)bakın.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue JavaScript'te MessagePack tarafından desteklenmez

JavaScript istemcisi SignalR tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) kitaplığı MessagePack'teki `timestamp96` türü desteklemez. Bu tür çok büyük tarih değerlerini kodlamak için kullanılır (ya çok erken geçmişte ya da çok uzak gelecekte). Değeri `DateTime.MinValue` `January 1, 0001`, bir `timestamp96` değer olarak kodlanmalıdır. Bu nedenle, `DateTime.MinValue` bir JavaScript istemcisine gönderme desteklenmez. JavaScript istemcisi tarafından alındığı zaman, `DateTime.MinValue` aşağıdaki hata atılır:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Genellikle, `DateTime.MinValue` bir "eksik" veya `null` değer kodlamak için kullanılır. Bu değeri MessagePack'te kodlamanız gerekiyorsa, geçersiz `DateTime` bir`DateTime?`değer () `bool` kullanın veya tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.

Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [aspnet/SignalR#2228'ye](https://github.com/aspnet/SignalR/issues/2228)bakın.

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>MessagePack desteği "ileri-zaman" derleme ortamında

.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirmeyi optimize etmek için kod oluşturma yı kullanır. Sonuç olarak, "zamanın ilerisi" derlemesi (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez. MessagePack'i bu ortamlarda serializer/deserializer kodunu "ön üreterek" kullanmak mümkündür. Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)bakın. Serializer'leri önceden oluşturduktan sonra, aşağıdakilere geçen yapılandırma `AddMessagePackProtocol`temsilcisini kullanarak bunları kaydedebilirsiniz:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack'te tür denetimleri daha katıdır

JSON Hub Protokolü deserialization sırasında tür dönüşümleri gerçekleştirecektir. Örneğin, gelen nesnenin bir sayı (`{ foo: 42 }`) olan bir özellik değeri varsa, ancak `string`.NET sınıfındaki özellik türündeyse, değer dönüştürülür. Ancak, MessagePack bu dönüşümü gerçekleştirmez ve sunucu tarafındaki günlüklerde (ve konsolda) görülebilecek bir özel durum oluşturur:

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [SignalRaspnet/ #2937'a](https://github.com/aspnet/SignalR/issues/2937)bakın.

## <a name="related-resources"></a>İlgili kaynaklar

* [Başlayın](xref:tutorials/signalr)
* [.NET istemcisi](xref:signalr/dotnet-client)
* [JavaScript istemcisi](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makalede, okuyucu [nun Başlat'ta](xref:tutorials/signalr)kapsanan konulara aşina olduğunu varsayar.

## <a name="what-is-messagepack"></a>MessagePack nedir?

[MessagePack](https://msgpack.org/index.html) hızlı ve kompakt ikili serileştirme biçimidir. [JSON](https://www.json.org/)ile karşılaştırıldığında daha küçük iletiler oluşturduğundan performans ve bant genişliği bir sorun olduğunda yararlıdır. Baytlar MessagePack araörneğinden geçirilmedikçe, ağ izlemelerine ve günlüklerine bakarken ikili iletiler okunamaz. SignalRMessagePack biçimi için yerleşik destek vardır ve istemci ve sunucunun kullanması için API'ler sağlar.

## <a name="configure-messagepack-on-the-server"></a>MessagePack'i sunucuda yapılandırma

Sunucuda MessagePack Hub Protokolü'nü etkinleştirmek için paketi uygulamanıza yükleyin. `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` `Startup.ConfigureServices` Yöntemde, sunucuda `AddSignalR` MessagePack desteğini etkinleştirmek için çağrıya ekleyin. `AddMessagePackProtocol`

> [!NOTE]
> JSON varsayılan olarak etkinleştirilir. MessagePack eklemek hem JSON hem de MessagePack istemcileri için destek sağlar.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack'in verilerinizi nasıl biçimlendireceğini özelleştirmek için seçenekleri `AddMessagePackProtocol` yapılandırmak için bir temsilci alır. Bu temsilcide, `FormatterResolvers` özellik MessagePack serileştirme seçeneklerini yapılandırmak için kullanılabilir. Çözümleyicilerin nasıl çalıştığı hakkında daha fazla bilgi için [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)adresindeki MessagePack kitaplığını ziyaret edin. Öznitelikler, nasıl işlenmeleri gerektiğini tanımlamak için serileştirmek istediğiniz nesnelerde kullanılabilir.

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
> [CVE-2020-5234'ün](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) gözden geçirilmesini ve önerilen yamaları uygulamanızı önemle tavsiye ediyoruz. Örneğin, `MessagePackSecurity.Active` statik özelliği ' `MessagePackSecurity.UntrustedData`ye ayarlama MessagePack'in `MessagePackSecurity.Active` [1.9.x sürümünü](https://www.nuget.org/packages/MessagePack/1.9.3)el ile yüklemeyi gerektirir. Yükleme `MessagePack` 1.9.x sürümü SignalR kullanır yükseltir. Ne `MessagePackSecurity.Active` zaman ayarlanmaz `MessagePackSecurity.UntrustedData`, kötü niyetli bir istemci hizmet reddi neden olabilir. Aşağıdaki kodda gösterildiği gibi, `MessagePackSecurity.Active` `Program.Main`

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>İleti Paketini istemcide yapılandır

> [!NOTE]
> JSON desteklenen istemciler için varsayılan olarak etkinleştirilir. İstemciler yalnızca tek bir protokolü destekleyebilir. MessagePack desteği eklemek, önceden yapılandırılmış protokollerin yerini alacaktır.

### <a name="net-client"></a>.NET istemcisi

MessagePack'i .NET İstemci'de etkinleştirmek için paketi yükleyin `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` ve çağırın. `AddMessagePackProtocol` `HubConnectionBuilder`

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Bu `AddMessagePackProtocol` çağrı, sunucu gibi seçenekleri yapılandırmak için bir temsilci alır.

### <a name="javascript-client"></a>JavaScript istemcisi

JavaScript istemcisi için MessagePack [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) desteği npm paketi tarafından sağlanır. Aşağıdaki komutu bir komut kabuğunda çalıştırarak paketi yükleyin:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

npm paketini yükledikten sonra, modül doğrudan bir JavaScript modül yükleyici saracılığıyla kullanılabilir veya aşağıdaki dosyaya başvurarak tarayıcıya aktarılabilir:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

Bir tarayıcıda, `msgpack5` kitaplık da başvurulmalıdır. Başvuru `<script>` oluşturmak için bir etiket kullanın. Kütüphane *node_modules\msgpack5\dist\msgpack5.js*adresinde bulunabilir.

> [!NOTE]
> Öğeyi `<script>` kullanırken, sipariş önemlidir. *Signalr-protocol-msgpack.js* *msgpack5.js*önce başvurulan ise, MessagePack ile bağlanmaya çalışırken bir hata oluşur. *signalr.js* de *sinyalci-protokol-msgpack.js*önce gereklidir.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Bir `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` sunucuya bağlanırken MessagePack iletişim kuralını kullanmak için istemciyi `HubConnectionBuilder` yapılandıracaktır.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Şu anda, JavaScript istemcisindeki MessagePack protokolü için yapılandırma seçeneği bulunmamaktadır.

## <a name="messagepack-quirks"></a>MessagePack tuhaflıklar

MessagePack Hub Protokolü'nü kullanırken dikkat edilmesi gereken birkaç sorun vardır.

### <a name="messagepack-is-case-sensitive"></a>MessagePack büyük/küçük harf duyarlıdır

MessagePack protokolü büyük/küçük harf duyarlıdır. Örneğin, aşağıdaki C# sınıfını göz önünde bulundurun:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript istemcisinden gönderirken, kasacın C# sınıfıyla tam olarak eşleşmesi gerektiğinden, özellik adlarını kullanmanız `PascalCased` gerekir. Örneğin:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Adkullanmak `camelCased` C# sınıfına düzgün bir şekilde bağlanmaz. MessagePack özelliği için farklı `Key` bir ad belirtmek için özniteliği kullanarak bu geçici olarak bu şekilde çalışabilirsiniz. Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp#object-serialization)bakın.

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind serileştirme/deserializing zaman korunmuş değildir

MessagePack protokolü `Kind` bir `DateTime`. Sonuç olarak, bir tarih deserializing, MessagePack Hub Protokolü gelen tarih UTC biçiminde olduğunu varsayar. Yerel saatle değerlerle `DateTime` çalışıyorsanız, göndermeden önce UTC'ye dönüştürmenizi öneririz. Bunları aldığınızda UTC'den yerel saate dönüştürün.

Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [SignalRaspnet/ #2632'e](https://github.com/aspnet/SignalR/issues/2632)bakın.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue JavaScript'te MessagePack tarafından desteklenmez

JavaScript istemcisi SignalR tarafından kullanılan [msgpack5](https://github.com/mcollina/msgpack5) kitaplığı MessagePack'teki `timestamp96` türü desteklemez. Bu tür çok büyük tarih değerlerini kodlamak için kullanılır (ya çok erken geçmişte ya da çok uzak gelecekte). Değeri `DateTime.MinValue` bir `January 1, 0001` `timestamp96` değer de kodlanmış olması gerekir. Bu nedenle, `DateTime.MinValue` bir JavaScript istemcisine gönderme desteklenmez. JavaScript istemcisi tarafından alındığı zaman, `DateTime.MinValue` aşağıdaki hata atılır:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

Genellikle, `DateTime.MinValue` bir "eksik" veya `null` değer kodlamak için kullanılır. Bu değeri MessagePack'te kodlamanız gerekiyorsa, geçersiz `DateTime` bir`DateTime?`değer () `bool` kullanın veya tarihin mevcut olup olmadığını belirten ayrı bir değer kodlayın.

Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [aspnet/SignalR#2228'ye](https://github.com/aspnet/SignalR/issues/2228)bakın.

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>MessagePack desteği "ileri-zaman" derleme ortamında

.NET istemcisi ve sunucusu tarafından kullanılan [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) kitaplığı, serileştirmeyi optimize etmek için kod oluşturma yı kullanır. Sonuç olarak, "zamanın ilerisi" derlemesi (Xamarin iOS veya Unity gibi) kullanan ortamlarda varsayılan olarak desteklenmez. MessagePack'i bu ortamlarda serializer/deserializer kodunu "ön üreterek" kullanmak mümkündür. Daha fazla bilgi için [MessagePack-CSharp belgelerine](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)bakın. Serializer'leri önceden oluşturduktan sonra, aşağıdakilere geçen yapılandırma `AddMessagePackProtocol`temsilcisini kullanarak bunları kaydedebilirsiniz:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack'te tür denetimleri daha katıdır

JSON Hub Protokolü deserialization sırasında tür dönüşümleri gerçekleştirecektir. Örneğin, gelen nesnenin bir sayı (`{ foo: 42 }`) olan bir özellik değeri varsa, ancak `string`.NET sınıfındaki özellik türündeyse, değer dönüştürülür. Ancak, MessagePack bu dönüşümü gerçekleştirmez ve sunucu tarafındaki günlüklerde (ve konsolda) görülebilecek bir özel durum oluşturur:

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Bu sınırlama hakkında daha fazla bilgi için GitHub sorunu [SignalRaspnet/ #2937'a](https://github.com/aspnet/SignalR/issues/2937)bakın.

## <a name="related-resources"></a>İlgili kaynaklar

* [Başlayın](xref:tutorials/signalr)
* [.NET istemcisi](xref:signalr/dotnet-client)
* [JavaScript istemcisi](xref:signalr/javascript-client)

::: moniker-end
