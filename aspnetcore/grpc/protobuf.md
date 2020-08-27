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
ms.openlocfilehash: b8149b79c1e7b204e52cc8595d1193b623bb0008
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945767"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="fb11f-103">.NET uygulamaları için Prototipsiz iletiler oluşturma</span><span class="sxs-lookup"><span data-stu-id="fb11f-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="fb11f-104">[Can koç](https://twitter.com/jamesnk) ve [işareti yeniden adlandır](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="fb11f-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="fb11f-105">gRPC, arabirim tanım dili (IDL) olarak [Protoarabelleğe](https://developers.google.com/protocol-buffers) 'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="fb11f-106">Prototip IDL, gRPC Hizmetleri tarafından gönderilen ve alınan iletileri belirtmek için dilden bağımsız bir biçimdir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="fb11f-107">Prototip iletileri *. proto* dosyalarında tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-107">Protobuf messages are defined in *.proto* files.</span></span> <span data-ttu-id="fb11f-108">Bu belgede, prototip kavramlarının .NET ile nasıl eşlendiği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="fb11f-109">Protobuf iletileri</span><span class="sxs-lookup"><span data-stu-id="fb11f-109">Protobuf messages</span></span>

<span data-ttu-id="fb11f-110">İletiler, prototipte ana veri aktarımı nesnesidir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="fb11f-111">Bunlar kavramsal olarak .NET sınıflarıyla benzerdir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="fb11f-112">Yukarıdaki ileti tanımı, üç alanı ad-değer çiftleri olarak belirtir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="fb11f-113">.NET türlerindeki özellikler gibi, her alanın bir adı ve türü vardır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="fb11f-114">Alan türü, [prototipsiz skaler bir değer türü](#scalar-value-types), ör. `int32` veya başka bir ileti olabilir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="fb11f-115">Bir ada ek olarak, ileti tanımındaki her bir alanın benzersiz bir numarası vardır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="fb11f-116">Alan numaraları, ileti Protoarabelleğe serileştirildiğinde alanları tanımlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="fb11f-117">Küçük bir sayının serileştirilmesi, tüm alan adının serileştirilmesinin daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="fb11f-118">Alan numaraları bir alanı tanımladığından, bunları değiştirirken dikkatli olmanız önemlidir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="fb11f-119">Prototipsiz iletileri değiştirme hakkında daha fazla bilgi için bkz <xref:grpc/versioning> ..</span><span class="sxs-lookup"><span data-stu-id="fb11f-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="fb11f-120">Bir uygulama oluşturulduğunda, prototipleme araçları *. proto* dosyalarından .net türleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fb11f-120">When an app is built the Protobuf tooling generates .NET types from *.proto* files.</span></span> <span data-ttu-id="fb11f-121">`Person`İleti bir .NET sınıfı üretir:</span><span class="sxs-lookup"><span data-stu-id="fb11f-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="fb11f-122">Prototipme iletileri hakkında daha fazla bilgi için bkz. [prototipme dili Kılavuzu](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span><span class="sxs-lookup"><span data-stu-id="fb11f-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="fb11f-123">Skaler değer türleri</span><span class="sxs-lookup"><span data-stu-id="fb11f-123">Scalar Value Types</span></span>

<span data-ttu-id="fb11f-124">Prototip, yerel skaler değer türlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="fb11f-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="fb11f-125">Aşağıdaki tabloda bunların hepsi eşdeğer C# türü ile listelenmektedir:</span><span class="sxs-lookup"><span data-stu-id="fb11f-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="fb11f-126">Prototip türü</span><span class="sxs-lookup"><span data-stu-id="fb11f-126">Protobuf type</span></span> | <span data-ttu-id="fb11f-127">C# türü</span><span class="sxs-lookup"><span data-stu-id="fb11f-127">C# type</span></span>      |
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

### <a name="dates-and-times"></a><span data-ttu-id="fb11f-128">Tarihler ve saatler</span><span class="sxs-lookup"><span data-stu-id="fb11f-128">Dates and times</span></span>

<span data-ttu-id="fb11f-129">Yerel skaler türler, ile eşdeğer tarih ve saat değerleri için sağlamaz. NET <xref:System.DateTimeOffset> , <xref:System.DateTime> ve <xref:System.TimeSpan> .</span><span class="sxs-lookup"><span data-stu-id="fb11f-129">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="fb11f-130">Bu türler, Prototiplerde "tanınmış türler" uzantıları kullanılarak belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-130">These types can be specified by using some of Protobuf's "Well Known Types" extensions.</span></span> <span data-ttu-id="fb11f-131">Bu uzantılar desteklenen platformlar genelinde karmaşık alan türleri için kod oluşturma ve çalışma zamanı desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="fb11f-131">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="fb11f-132">Aşağıdaki tabloda tarih ve saat türleri gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="fb11f-132">The following table shows the date and time types:</span></span>

| <span data-ttu-id="fb11f-133">.NET türü</span><span class="sxs-lookup"><span data-stu-id="fb11f-133">.NET type</span></span> | <span data-ttu-id="fb11f-134">Prototip iyi bilinen tür</span><span class="sxs-lookup"><span data-stu-id="fb11f-134">Protobuf well-known type</span></span> |
| ------- | ------------------------ |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime` | `google.protobuf.Timestamp` |
| `TimeSpan` | `google.protobuf.Duration` |

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

<span data-ttu-id="fb11f-135">C# sınıfındaki oluşturulan Özellikler .NET tarih ve saat türleri değildir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-135">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="fb11f-136">Özellikler, `Timestamp` `Duration` ad alanındaki ve sınıflarını kullanır `Google.Protobuf.WellKnownTypes` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-136">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="fb11f-137">Bu sınıflar,, ve ' a dönüştürmek için yöntemler sağlar `DateTimeOffset` `DateTime` `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-137">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

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
> <span data-ttu-id="fb11f-138">`Timestamp`Tür UTC saatleriyle birlikte çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-138">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="fb11f-139">`DateTimeOffset` değerler her zaman sıfır bir uzaklığa sahiptir ve `DateTime.Kind` özellik her zaman olur `DateTimeKind.Utc` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-139">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="fb11f-140">Null atanabilir türler</span><span class="sxs-lookup"><span data-stu-id="fb11f-140">Nullable types</span></span>

<span data-ttu-id="fb11f-141">C# için prototip kod oluşturma, gibi yerel türleri kullanır `int` `int32` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-141">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="fb11f-142">Bu nedenle değerler her zaman dahil edilir ve olamaz `null` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-142">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="fb11f-143">C# kodunda kullanılması gibi açık gerektiren değerler için `null` `int?` , prototipli "Iyi bilinen türler", null yapılabilir C# türlerine derlenen sarmalayıcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-143">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's "Well Known Types" include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="fb11f-144">Bunları kullanmak için, `wrappers.proto` `.proto` Aşağıdaki kod gibi dosyanıza içeri aktarın:</span><span class="sxs-lookup"><span data-stu-id="fb11f-144">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="fb11f-145">Prototip, `int?` üretilen ileti özelliği için .net Nullable türler (örneğin,) kullanır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-145">Protobuf uses .NET nullable types, for example, `int?`, for the generated message property.</span></span>

<span data-ttu-id="fb11f-146">Aşağıdaki tabloda, eşdeğer C# türüyle sarmalayıcı türlerinin tüm listesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="fb11f-146">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="fb11f-147">C# türü</span><span class="sxs-lookup"><span data-stu-id="fb11f-147">C# type</span></span>   | <span data-ttu-id="fb11f-148">İyi bilinen tür sarmalayıcısı</span><span class="sxs-lookup"><span data-stu-id="fb11f-148">Well Known Type wrapper</span></span>       |
| --------- | ----------------------------- |
| `bool?`   | `google.protobuf.BoolValue`   |
| `double?` | `google.protobuf.DoubleValue` |
| `float?`  | `google.protobuf.FloatValue`  |
| `int?`    | `google.protobuf.Int32Value`  |
| `long?`   | `google.protobuf.Int64Value`  |
| `uint?`   | `google.protobuf.UInt32Value` |
| `ulong?`  | `google.protobuf.UInt64Value` |

### <a name="decimals"></a><span data-ttu-id="fb11f-149">Ondalıklar</span><span class="sxs-lookup"><span data-stu-id="fb11f-149">Decimals</span></span>

<span data-ttu-id="fb11f-150">Prototip `decimal` , yalnızca ve .NET türünü yerel olarak desteklemez `double` `float` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-150">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="fb11f-151">Yaygın olarak bilinen türlere standart bir ondalık tür ekleme olasılığa ve bunu destekleyen diller ve çerçeveler için platform desteğiyle, prototip projesinde devam eden bir tartışma vardır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-151">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the well-known types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="fb11f-152">Henüz hiçbir şey uygulanmadı.</span><span class="sxs-lookup"><span data-stu-id="fb11f-152">Nothing has been implemented yet.</span></span>

<span data-ttu-id="fb11f-153">`decimal`.NET istemcileri ve sunucuları arasında güvenli seri hale getirme için kullanılan türü temsil eden bir ileti tanımı oluşturmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="fb11f-153">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="fb11f-154">Ancak diğer platformlardaki geliştiricilerin, kullanılmakta olan biçimi anlaması ve onun için kendi işlemesini uygulaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-154">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="fb11f-155">Protoarabellek için özel bir ondalık tür oluşturma</span><span class="sxs-lookup"><span data-stu-id="fb11f-155">Creating a custom decimal type for Protobuf</span></span>

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

<span data-ttu-id="fb11f-156">`nanos`Alan, öğesinden değerlerini temsil `0.999_999_999` eder `-0.999_999_999` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-156">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="fb11f-157">Örneğin, `decimal` değer `1.5m` olarak temsil edilir `{ units = 1, nanos = 500_000_000 }` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-157">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="fb11f-158">Bu, `nanos` Bu örnekteki alanın, `sfixed32` daha büyük değerler için daha verimli bir şekilde kodlama türünü kullanmadığına neden olur `int32` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-158">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="fb11f-159">`units`Alan negatifse `nanos` alanın de negatif olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-159">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="fb11f-160">Değerleri bayt dizeleri olarak kodlamak için birden çok farklı algoritma mevcuttur `decimal` , ancak bu iletinin bunlardan herhangi birinin anlaşılması kolaylaşır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-160">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="fb11f-161">Değerler, farklı platformlarda büyük endian veya küçük endian tarafından etkilenmez.</span><span class="sxs-lookup"><span data-stu-id="fb11f-161">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="fb11f-162">Bu tür ile BCL türü arasında dönüştürme `decimal` C# ' de şöyle uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="fb11f-162">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

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

## <a name="collections"></a><span data-ttu-id="fb11f-163">Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="fb11f-163">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="fb11f-164">Listeler</span><span class="sxs-lookup"><span data-stu-id="fb11f-164">Lists</span></span>

<span data-ttu-id="fb11f-165">Prototipteki listeler, `repeated` bir alandaki önek anahtar sözcüğü kullanılarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-165">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="fb11f-166">Aşağıdaki örnek, bir listenin nasıl oluşturulacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="fb11f-166">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="fb11f-167">Oluşturulan kodda, `repeated` alanlar genel tür tarafından temsil edilir `Google.Protobuf.Collections.RepeatedField<T>` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-167">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="fb11f-168">`RepeatedField<T>` uygular <xref:System.Collections.Generic.IList%601> .</span><span class="sxs-lookup"><span data-stu-id="fb11f-168">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="fb11f-169">Bu nedenle, LINQ sorgularını kullanabilir veya bir diziye veya bir listeye dönüştürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fb11f-169">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="fb11f-170">`RepeatedField<T>` özelliklerin ortak bir ayarlayıcısı yok.</span><span class="sxs-lookup"><span data-stu-id="fb11f-170">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="fb11f-171">Öğeler var olan koleksiyona eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-171">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="fb11f-172">Sözlükler</span><span class="sxs-lookup"><span data-stu-id="fb11f-172">Dictionaries</span></span>

<span data-ttu-id="fb11f-173">.NET <xref:System.Collections.Generic.IDictionary%602> türü prototipi kullanılarak temsil edilir `map<key_type, value_type>` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-173">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="fb11f-174">Oluşturulan .NET kodunda, `map` alanlar genel tür tarafından temsil edilir `Google.Protobuf.Collections.MapField<TKey, TValue>` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-174">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="fb11f-175">`MapField<TKey, TValue>` uygular <xref:System.Collections.Generic.IDictionary%602> .</span><span class="sxs-lookup"><span data-stu-id="fb11f-175">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="fb11f-176">`repeated`Özellikler gibi `map` Özellikler, ortak bir ayarlayıcı içermez.</span><span class="sxs-lookup"><span data-stu-id="fb11f-176">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="fb11f-177">Öğeler var olan koleksiyona eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-177">Items should be added to the existing collection.</span></span>

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

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="fb11f-178">Yapılandırılmamış ve koşullu iletiler</span><span class="sxs-lookup"><span data-stu-id="fb11f-178">Unstructured and conditional messages</span></span>

<span data-ttu-id="fb11f-179">Prototip, bir sözleşmenin ilk ileti biçimidir ve uygulama oluşturulduğunda *. proto* dosyalarında bir uygulamalar iletisi belirtilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-179">Protobuf is a contract-first messaging format, and an apps messages need to be specified in *.proto* files when the app is built.</span></span> <span data-ttu-id="fb11f-180">Gelişmiş senaryolar için Protoarabellek, koşullu ve bilinmeyen iletileri desteklemek için dil özellikleri ve iyi bilinen türler sunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-180">For advanced scenarios, Protobuf offers language features and well known types to support conditional and unknown messages.</span></span>

### <a name="any"></a><span data-ttu-id="fb11f-181">Herhangi bir</span><span class="sxs-lookup"><span data-stu-id="fb11f-181">Any</span></span>

<span data-ttu-id="fb11f-182">`Any`Türü, *. proto* tanımına sahip olmayan iletileri katıştırılmış tür olarak kullanmanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-182">The `Any` type lets you use messages as embedded types without having their *.proto* definition.</span></span> <span data-ttu-id="fb11f-183">Türünü kullanmak için `Any` içeri aktarın `any.proto` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-183">To use the `Any` type, import `any.proto`.</span></span>

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

### <a name="oneof"></a><span data-ttu-id="fb11f-184">Oneof</span><span class="sxs-lookup"><span data-stu-id="fb11f-184">Oneof</span></span>

<span data-ttu-id="fb11f-185">`oneof` alanlar bir dil özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-185">`oneof` fields are a language feature.</span></span> <span data-ttu-id="fb11f-186">Derleyici, `oneof` ileti sınıfını oluşturduğunda anahtar sözcüğünü işler.</span><span class="sxs-lookup"><span data-stu-id="fb11f-186">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="fb11f-187">`oneof`Ya da döndüren bir yanıt iletisi belirtmek için kullanarak `Person` veya `Error` şöyle görünebilir:</span><span class="sxs-lookup"><span data-stu-id="fb11f-187">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

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

<span data-ttu-id="fb11f-188">Küme içindeki alanlar, `oneof` genel ileti bildiriminde benzersiz alan numaralarına sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fb11f-188">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="fb11f-189">Kullanırken `oneof` , oluşturulan C# kodu, alanların hangisinin ayarlandığını belirten bir sabit listesi içerir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-189">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="fb11f-190">Hangi alanın ayarlandığını bulmak için sabit listesini test edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fb11f-190">You can test the enum to find which field is set.</span></span> <span data-ttu-id="fb11f-191">Ayarlı olmayan alanlar `null` , özel durum oluşturmak yerine varsayılan değer döndürür.</span><span class="sxs-lookup"><span data-stu-id="fb11f-191">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

```csharp
var response = client.GetPersonAsync(new RequestMessage());

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

### <a name="value"></a><span data-ttu-id="fb11f-192">Değer</span><span class="sxs-lookup"><span data-stu-id="fb11f-192">Value</span></span>

<span data-ttu-id="fb11f-193">`Value`Tür, dinamik olarak yazılmış bir değeri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="fb11f-193">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="fb11f-194">Bu, `null` bir sayı, bir dize, Boole değeri, değerlerin bir sözlüğü ( `Struct` ) veya bir değerler listesi ( `ValueList` ) olabilir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-194">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="fb11f-195">`Value` , daha önce tartışılan özelliği kullanan iyi bilinen bir türdür `oneof` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-195">`Value` is a well known type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="fb11f-196">Türünü kullanmak için `Value` içeri aktarın `struct.proto` .</span><span class="sxs-lookup"><span data-stu-id="fb11f-196">To use the `Value` type, import `struct.proto`.</span></span>

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

<span data-ttu-id="fb11f-197">`Value`Doğrudan kullanımı ayrıntılı olabilir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-197">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="fb11f-198">Kullanmanın alternatif bir yolu `Value` , protoda ILETILERI JSON ile eşlemek için yerleşik destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="fb11f-198">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="fb11f-199">Prototip `JsonFormatter` ve `JsonWriter` türler herhangi bir prototiple ileti ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-199">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="fb11f-200">`Value` JSON 'dan ve bu sunucudan dönüştürülmek için özellikle idealdir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-200">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="fb11f-201">Bu, önceki kodun JSON eşdeğeridir.</span><span class="sxs-lookup"><span data-stu-id="fb11f-201">This is the JSON equivalent of the previous code:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="fb11f-202">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fb11f-202">Additional resources</span></span>

* [<span data-ttu-id="fb11f-203">Prototip dili Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="fb11f-203">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
