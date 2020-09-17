---
title: .NET uygulamaları için Prototipsiz iletiler oluşturma
author: jamesnk
description: .NET uygulamaları için Prototipsiz iletiler oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/protobuf
ms.openlocfilehash: ea46e04bc4aa6269efbf8917d5f32194402a66ef
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722702"
---
# <a name="create-protobuf-messages-for-net-apps"></a>.NET uygulamaları için Prototipsiz iletiler oluşturma

[Can koç](https://twitter.com/jamesnk) ve [işareti yeniden adlandır](https://twitter.com/markrendle)

gRPC, arabirim tanım dili (IDL) olarak [Protoarabelleğe](https://developers.google.com/protocol-buffers) 'yi kullanır. Prototip IDL, gRPC Hizmetleri tarafından gönderilen ve alınan iletileri belirtmek için dilden bağımsız bir biçimdir. Prototip iletileri `.proto` dosyalarda tanımlanmıştır. Bu belgede, prototip kavramlarının .NET ile nasıl eşlendiği açıklanmaktadır.

## <a name="protobuf-messages"></a>Protobuf iletileri

İletiler, prototipte ana veri aktarımı nesnesidir. Bunlar kavramsal olarak .NET sınıflarıyla benzerdir.

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

Yukarıdaki ileti tanımı, üç alanı ad-değer çiftleri olarak belirtir. .NET türlerindeki özellikler gibi, her alanın bir adı ve türü vardır. Alan türü, [prototipsiz skaler bir değer türü](#scalar-value-types), ör. `int32` veya başka bir ileti olabilir.

Bir ada ek olarak, ileti tanımındaki her bir alanın benzersiz bir numarası vardır. Alan numaraları, ileti Protoarabelleğe serileştirildiğinde alanları tanımlamak için kullanılır. Küçük bir sayının serileştirilmesi, tüm alan adının serileştirilmesinin daha hızlıdır. Alan numaraları bir alanı tanımladığından, bunları değiştirirken dikkatli olmanız önemlidir. Prototipsiz iletileri değiştirme hakkında daha fazla bilgi için bkz <xref:grpc/versioning> ..

Bir uygulama yapılandırıldığında, prototipleme araçları dosyalardan .NET türleri oluşturur `.proto` . `Person`İleti bir .NET sınıfı üretir:

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

Prototipme iletileri hakkında daha fazla bilgi için bkz. [prototipme dili Kılavuzu](https://developers.google.com/protocol-buffers/docs/proto3#simple).

## <a name="scalar-value-types"></a>Skaler değer türleri

Prototip, yerel skaler değer türlerini destekler. Aşağıdaki tabloda bunların hepsi eşdeğer C# türü ile listelenmektedir:

| Prototip türü | C# türü      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

Skaler değerler her zaman varsayılan bir değere sahiptir ve olarak ayarlanamaz `null` . Bu kısıtlama `string` C# sınıfları içerir ve içerir `ByteString` . `string` Varsayılan değer boş bir dize değerine ve `ByteString` Varsayılan olarak boş bayt değerine sahip olur. Bunları `null` bir hata oluşturur olarak ayarlamaya çalışılıyor.

Null değer [alabilen sarmalayıcı türleri](#nullable-types) , null değerleri desteklemek için kullanılabilir.

### <a name="dates-and-times"></a>Tarihler ve saatler

Yerel skaler türler, ile eşdeğer tarih ve saat değerleri için sağlamaz. NET <xref:System.DateTimeOffset> , <xref:System.DateTime> ve <xref:System.TimeSpan> . Bu türler, prototipin *Iyi bilinen türler* uzantıları kullanılarak belirtilebilir. Bu uzantılar desteklenen platformlar genelinde karmaşık alan türleri için kod oluşturma ve çalışma zamanı desteği sağlar.

Aşağıdaki tabloda tarih ve saat türleri gösterilmektedir:

| .NET türü        | Prototip Iyi bilinen tür    |
| ---------------- | --------------------------- |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime`       | `google.protobuf.Timestamp` |
| `TimeSpan`       | `google.protobuf.Duration`  |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

C# sınıfındaki oluşturulan Özellikler .NET tarih ve saat türleri değildir. Özellikler, `Timestamp` `Duration` ad alanındaki ve sınıflarını kullanır `Google.Protobuf.WellKnownTypes` . Bu sınıflar,, ve ' a dönüştürmek için yöntemler sağlar `DateTimeOffset` `DateTime` `TimeSpan` .

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> `Timestamp`Tür UTC saatleriyle birlikte çalışmaktadır. `DateTimeOffset` değerler her zaman sıfır bir uzaklığa sahiptir ve `DateTime.Kind` özellik her zaman olur `DateTimeKind.Utc` .

### <a name="nullable-types"></a>Null atanabilir türler

C# için prototip kod oluşturma, gibi yerel türleri kullanır `int` `int32` . Bu nedenle değerler her zaman dahil edilir ve olamaz `null` .

`null`C# kodunda kullanılması gibi açık gerektiren değerler için `int?` , Prototipen Iyi bilinen türler, null yapılabilir C# türlerine derlenen sarmalayıcıları içerir. Bunları kullanmak için, `wrappers.proto` `.proto` Aşağıdaki kod gibi dosyanıza içeri aktarın:

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

`wrappers.proto` türler oluşturulan özelliklerde gösterilmez. Prototip, C# iletilerinde uygun .NET null yapılabilir türleriyle otomatik olarak eşlenir. Örneğin, bir `google.protobuf.Int32Value` alan bir özellik oluşturur `int?` . Ve gibi başvuru türü özellikleri, `string` `ByteString` `null` hata olmadan bunlara atanabilir.

Aşağıdaki tabloda, eşdeğer C# türüyle sarmalayıcı türlerinin tüm listesi gösterilmektedir:

| C# türü      | İyi bilinen tür sarmalayıcısı       |
| ------------ | ----------------------------- |
| `bool?`      | `google.protobuf.BoolValue`   |
| `double?`    | `google.protobuf.DoubleValue` |
| `float?`     | `google.protobuf.FloatValue`  |
| `int?`       | `google.protobuf.Int32Value`  |
| `long?`      | `google.protobuf.Int64Value`  |
| `uint?`      | `google.protobuf.UInt32Value` |
| `ulong?`     | `google.protobuf.UInt64Value` |
| `string`     | `google.protobuf.StringValue` |
| `ByteString` | `google.protobuf.BytesValue`  |

### <a name="bytes"></a>Bayt

İkili yükleri, skaler değer türü ile prototipte desteklenir `bytes` . C# içinde oluşturulan bir özellik `ByteString` , özellik türü olarak kullanır.

`ByteString.CopyFrom(byte[] data)`Bir bayt dizisinden yeni bir örnek oluşturmak için kullanın:

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

`ByteString` verilerine doğrudan veya kullanılarak erişilir `ByteString.Span` `ByteString.Memory` . Ya da `ByteString.ToByteArray()` bir örneği bir bayt dizisine geri dönüştürmek için çağırın:

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a>Ondalıklar

Prototip `decimal` , yalnızca ve .NET türünü yerel olarak desteklemez `double` `float` . Yaygın olarak bilinen türlere standart bir ondalık tür ekleme olasılığa ve bunu destekleyen diller ve çerçeveler için platform desteğiyle, prototip projesinde devam eden bir tartışma vardır. Henüz hiçbir şey uygulanmadı.

`decimal`.NET istemcileri ve sunucuları arasında güvenli seri hale getirme için kullanılan türü temsil eden bir ileti tanımı oluşturmak mümkündür. Ancak diğer platformlardaki geliştiricilerin, kullanılmakta olan biçimi anlaması ve onun için kendi işlemesini uygulaması gerekir.

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a>Protoarabellek için özel bir ondalık tür oluşturma

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

`nanos`Alan, öğesinden değerlerini temsil `0.999_999_999` eder `-0.999_999_999` . Örneğin, `decimal` değer `1.5m` olarak temsil edilir `{ units = 1, nanos = 500_000_000 }` . Bu, `nanos` Bu örnekteki alanın, `sfixed32` daha büyük değerler için daha verimli bir şekilde kodlama türünü kullanmadığına neden olur `int32` . `units`Alan negatifse `nanos` alanın de negatif olması gerekir.

> [!NOTE]
> Değerleri bayt dizeleri olarak kodlamak için birden çok farklı algoritma mevcuttur `decimal` , ancak bu iletinin bunlardan herhangi birinin anlaşılması kolaylaşır. Değerler, farklı platformlarda büyük endian veya küçük endian tarafından etkilenmez.

Bu tür ile BCL türü arasında dönüştürme `decimal` C# ' de şöyle uygulanabilir:

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a>Koleksiyonlar

### <a name="lists"></a>Listeler

Prototipteki listeler, `repeated` bir alandaki önek anahtar sözcüğü kullanılarak belirtilir. Aşağıdaki örnek, bir listenin nasıl oluşturulacağını gösterir:

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

Oluşturulan kodda, `repeated` alanlar genel tür tarafından temsil edilir `Google.Protobuf.Collections.RepeatedField<T>` .

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

`RepeatedField<T>` uygular <xref:System.Collections.Generic.IList%601> . Bu nedenle, LINQ sorgularını kullanabilir veya bir diziye veya bir listeye dönüştürebilirsiniz. `RepeatedField<T>` özelliklerin ortak bir ayarlayıcısı yok. Öğeler var olan koleksiyona eklenmelidir.

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a>Sözlükler

.NET <xref:System.Collections.Generic.IDictionary%602> türü prototipi kullanılarak temsil edilir `map<key_type, value_type>` .

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

Oluşturulan .NET kodunda, `map` alanlar genel tür tarafından temsil edilir `Google.Protobuf.Collections.MapField<TKey, TValue>` . `MapField<TKey, TValue>` uygular <xref:System.Collections.Generic.IDictionary%602> . `repeated`Özellikler gibi `map` Özellikler, ortak bir ayarlayıcı içermez. Öğeler var olan koleksiyona eklenmelidir.

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a>Yapılandırılmamış ve koşullu iletiler

Prototip, bir sözleşmenin ilk ileti biçimidir. Uygulama oluşturulduğunda, bir uygulamanın, alanları ve türleri de dahil olmak üzere iletiler içinde belirtilmesi gerekir `.proto` . Prototipleme sözleşmesi-ilk tasarımı ileti içeriğini zorlarken harika, ancak katı bir sözleşmenin gerekmediği senaryoları sınırlayabilir:

* Bilinmeyen yükleri olan mesajlar. Örneğin, herhangi bir ileti içerebilen bir alan içeren bir ileti.
* Koşullu iletiler. Örneğin, bir gRPC hizmetinden döndürülen bir ileti, başarılı bir sonuç veya hata sonucu olabilir.
* Dinamik değerler. Örneğin, JSON ile benzer şekilde yapılandırılmamış değer koleksiyonu içeren bir alan içeren bir ileti.

Prototip, bu senaryoları desteklemek için dil özellikleri ve türleri sunar.

### <a name="any"></a>Herhangi biri

`Any`Türü, kendi tanımına sahip olmayan iletileri katıştırılmış tür olarak kullanmanıza olanak tanır `.proto` . Türünü kullanmak için `Any` içeri aktarın `any.proto` .

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a>Oneof

`oneof` alanlar bir dil özelliğidir. Derleyici, `oneof` ileti sınıfını oluşturduğunda anahtar sözcüğünü işler. `oneof`Ya da döndüren bir yanıt iletisi belirtmek için kullanarak `Person` veya `Error` şöyle görünebilir:

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

Küme içindeki alanlar, `oneof` genel ileti bildiriminde benzersiz alan numaralarına sahip olmalıdır.

Kullanırken `oneof` , oluşturulan C# kodu, alanların hangisinin ayarlandığını belirten bir sabit listesi içerir. Hangi alanın ayarlandığını bulmak için sabit listesini test edebilirsiniz. Ayarlı olmayan alanlar `null` , özel durum oluşturmak yerine varsayılan değer döndürür.

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a>Değer

`Value`Tür, dinamik olarak yazılmış bir değeri temsil eder. Bu, `null` bir sayı, bir dize, Boole değeri, değerlerin bir sözlüğü ( `Struct` ) veya bir değerler listesi ( `ValueList` ) olabilir. `Value` , daha önce tartışılan özelliği kullanan, prototip Iyi bilinen bir türdür `oneof` . Türünü kullanmak için `Value` içeri aktarın `struct.proto` .

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

`Value`Doğrudan kullanımı ayrıntılı olabilir. Kullanmanın alternatif bir yolu `Value` , protoda ILETILERI JSON ile eşlemek için yerleşik destek sağlar. Prototip `JsonFormatter` ve `JsonWriter` türler herhangi bir prototiple ileti ile kullanılabilir. `Value` JSON 'dan ve bu sunucudan dönüştürülmek için özellikle idealdir.

Bu, önceki kodun JSON eşdeğeridir.

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a>Ek kaynaklar

* [Prototip dili Kılavuzu](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
