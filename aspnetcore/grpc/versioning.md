---
title: gRPC hizmetlerinin sürümünü oluşturma
author: jamesnk
description: GRPC hizmetlerini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
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
uid: grpc/versioning
ms.openlocfilehash: 38204b16d041f21221862c566b90a6a9571d26a1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058707"
---
# <a name="versioning-grpc-services"></a><span data-ttu-id="36cf0-103">gRPC hizmetlerinin sürümünü oluşturma</span><span class="sxs-lookup"><span data-stu-id="36cf0-103">Versioning gRPC services</span></span>

<span data-ttu-id="36cf0-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="36cf0-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="36cf0-105">Bir uygulamaya eklenen yeni özellikler, bazı durumlarda bazen beklenmedik ve kırılmaya karşı istemciler için sunulan gRPC hizmetlerini gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-105">New features added to an app can require gRPC services provided to clients to change, sometimes in unexpected and breaking ways.</span></span> <span data-ttu-id="36cf0-106">GRPC Hizmetleri değiştiğinde:</span><span class="sxs-lookup"><span data-stu-id="36cf0-106">When gRPC services change:</span></span>

* <span data-ttu-id="36cf0-107">Değişikliklerin istemcileri nasıl etkilediğini göz önünde bulundurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-107">Consideration should be given on how changes impact clients.</span></span>
* <span data-ttu-id="36cf0-108">Değişiklikleri desteklemek için sürüm oluşturma stratejisi uygulanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-108">A versioning strategy to support changes should be implemented.</span></span>

## <a name="backwards-compatibility"></a><span data-ttu-id="36cf0-109">Geriye dönük uyumluluk</span><span class="sxs-lookup"><span data-stu-id="36cf0-109">Backwards compatibility</span></span>

<span data-ttu-id="36cf0-110">GRPC protokolü, zaman içinde değişen Hizmetleri destekleyecek şekilde tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-110">The gRPC protocol is designed to support services that change over time.</span></span> <span data-ttu-id="36cf0-111">Genel olarak, gRPC Hizmetleri ve yöntemlerine yapılan ekler kırılmamış değildir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-111">Generally, additions to gRPC services and methods are non-breaking.</span></span> <span data-ttu-id="36cf0-112">Bölünmez değişiklikler mevcut istemcilerin değişiklik yapmadan çalışmaya devam etmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-112">Non-breaking changes allow existing clients to continue working without changes.</span></span> <span data-ttu-id="36cf0-113">GRPC hizmetlerini değiştirme veya silme, değişiklikler ortadan kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-113">Changing or deleting gRPC services are breaking changes.</span></span> <span data-ttu-id="36cf0-114">GRPC hizmetlerinde son değişiklikler olduğunda, bu hizmeti kullanan istemcilerin güncellenmesi ve yeniden dağıtılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-114">When gRPC services have breaking changes, clients using that service have to be updated and redeployed.</span></span>

<span data-ttu-id="36cf0-115">Bir hizmette önemli olmayan değişiklikler yapmak çok sayıda avantaj sunar:</span><span class="sxs-lookup"><span data-stu-id="36cf0-115">Making non-breaking changes to a service has a number of benefits:</span></span>

* <span data-ttu-id="36cf0-116">Mevcut istemciler çalışmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="36cf0-116">Existing clients continue to run.</span></span>
* <span data-ttu-id="36cf0-117">, Bir veya daha fazla değişiklik için istemcileri bildirmeye ve bunları güncelleştirmeye dahil olan çalışmayı önler.</span><span class="sxs-lookup"><span data-stu-id="36cf0-117">Avoids work involved with notifying clients of breaking changes, and updating them.</span></span>
* <span data-ttu-id="36cf0-118">Hizmetin yalnızca bir sürümünün belgelenilmesi ve saklanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-118">Only one version of the service needs to be documented and maintained.</span></span>

### <a name="non-breaking-changes"></a><span data-ttu-id="36cf0-119">Kırılamayan değişiklikler</span><span class="sxs-lookup"><span data-stu-id="36cf0-119">Non-breaking changes</span></span>

<span data-ttu-id="36cf0-120">Bu değişiklikler, gRPC protokol düzeyinde ve .NET ikili düzeyinde kırılmamış değildir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-120">These changes are non-breaking at a gRPC protocol level and .NET binary level.</span></span>

* <span data-ttu-id="36cf0-121">**Yeni bir hizmet ekleniyor**</span><span class="sxs-lookup"><span data-stu-id="36cf0-121">**Adding a new service**</span></span>
* <span data-ttu-id="36cf0-122">**Bir hizmete yeni bir yöntem ekleme**</span><span class="sxs-lookup"><span data-stu-id="36cf0-122">**Adding a new method to a service**</span></span>
* <span data-ttu-id="36cf0-123">**İstek iletisine alan ekleme** -bir istek iletisine eklenen alanlar, ayarlanmayan sunucu üzerindeki [varsayılan değerle](https://developers.google.com/protocol-buffers/docs/proto3#default) seri durumdan silinir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-123">**Adding a field to a request message** - Fields added to a request message are deserialized with the [default value](https://developers.google.com/protocol-buffers/docs/proto3#default) on the server when not set.</span></span> <span data-ttu-id="36cf0-124">Yeni alan eski istemciler tarafından ayarlanmamışsa, bir olmayan değişiklik olması için hizmetin başarılı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-124">To be a non-breaking change, the service must succeed when the new field isn't set by older clients.</span></span>
* <span data-ttu-id="36cf0-125">**Yanıt iletisine bir alan ekleme** -yanıt iletisine eklenen alanlar, istemcideki [Bilinmeyen alanlar](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) koleksiyonuna seri durumdan çıkarılacak.</span><span class="sxs-lookup"><span data-stu-id="36cf0-125">**Adding a field to a response message** - Fields added to a response message are deserialized into the message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) collection on the client.</span></span>
* <span data-ttu-id="36cf0-126">**Sabit listesine değer eklemek** sayısal bir değer olarak serileştirilir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-126">**Adding a value to an enum** - Enums are serialized as a numeric value.</span></span> <span data-ttu-id="36cf0-127">Yeni Enum değerleri, bir sabit listesi adı olmadan, istemcide seri hale getirilmesi değeri olarak seri durumdan silinir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-127">New enum values are deserialized on the client to the enum value without an enum name.</span></span> <span data-ttu-id="36cf0-128">Yeni bir sabit listesi değeri alınırken, daha eski istemcilerin düzgün çalışması gerekir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-128">To be a non-breaking change, older clients must run correctly when receiving the new enum value.</span></span>

### <a name="binary-breaking-changes"></a><span data-ttu-id="36cf0-129">İkili son değişiklikler</span><span class="sxs-lookup"><span data-stu-id="36cf0-129">Binary breaking changes</span></span>

<span data-ttu-id="36cf0-130">Aşağıdaki değişiklikler bir gRPC protokol düzeyinde kırılmamış, ancak en son *. proto* sözleşmesine veya istemci .net derlemesine yükseltiyorsa istemcinin güncelleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-130">The following changes are non-breaking at a gRPC protocol level, but the client needs to be updated if it upgrades to the latest *.proto* contract or client .NET assembly.</span></span> <span data-ttu-id="36cf0-131">Bir gRPC kitaplığını NuGet 'e yayımlamayı planlıyorsanız ikili uyumluluk önemlidir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-131">Binary compatibility is important if you plan to publish a gRPC library to NuGet.</span></span>

* <span data-ttu-id="36cf0-132">Kaldırılan bir alandan **alan değerlerinin kaldırılması** , iletinin [Bilinmeyen alanlarına](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)seri durumdan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-132">**Removing a field** - Values from a removed field are deserialized to a message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns).</span></span> <span data-ttu-id="36cf0-133">Bu bir gRPC protokol bölünmesi değişikliği değildir, ancak en son sözleşmeye yükseltiyorsa istemcinin güncelleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-133">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="36cf0-134">Kaldırılmış bir alan numarasının gelecekte yanlışlıkla yeniden kullanılması önemlidir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-134">It's important that a removed field number isn't accidentally reused in the future.</span></span> <span data-ttu-id="36cf0-135">Bunun gerçekleşmediğinden emin olmak için, Protoarabelleğe ait [ayrılmış](https://developers.google.com/protocol-buffers/docs/proto3#reserved) anahtar sözcüğünü kullanarak, silinen alan numaralarını ve iletideki adları belirtin.</span><span class="sxs-lookup"><span data-stu-id="36cf0-135">To ensure this doesn't happen, specify deleted field numbers and names on the message using Protobuf's [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved) keyword.</span></span>
* <span data-ttu-id="36cf0-136">**Iletiyi yeniden adlandırma** -ileti adları genellikle ağ üzerinde gönderilmemektedir, bu nedenle bu bir GRPC protokol bölünmesi değişikliği değildir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-136">**Renaming a message** - Message names aren't typically sent on the network, so this isn't a gRPC protocol breaking change.</span></span> <span data-ttu-id="36cf0-137">En son sözleşmeye yükseltiyorsa istemcinin güncelleştirilmeleri gerekir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-137">The client will need to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="36cf0-138">İleti **adlarının ağ** üzerinde gönderildiği bir durum, ileti adının ileti türünü tanımlamak için [kullanıldığı bir durumdur](https://developers.google.com/protocol-buffers/docs/proto3#any) .</span><span class="sxs-lookup"><span data-stu-id="36cf0-138">One situation where message names **are** sent on the network is with [Any](https://developers.google.com/protocol-buffers/docs/proto3#any) fields, when the message name is used to identify the message type.</span></span>
* <span data-ttu-id="36cf0-139">**Csharp_namespace** değiştirme değiştirmek `csharp_namespace` , oluşturulan .net türlerinin ad alanını değiştirecek.</span><span class="sxs-lookup"><span data-stu-id="36cf0-139">**Changing csharp_namespace** - Changing `csharp_namespace` will change the namespace of generated .NET types.</span></span> <span data-ttu-id="36cf0-140">Bu bir gRPC protokol bölünmesi değişikliği değildir, ancak en son sözleşmeye yükseltiyorsa istemcinin güncelleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-140">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span>

### <a name="protocol-breaking-changes"></a><span data-ttu-id="36cf0-141">Protokol bölünmesi değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="36cf0-141">Protocol breaking changes</span></span>

<span data-ttu-id="36cf0-142">Aşağıdaki öğeler protokol ve ikili parçalara ayırma değişiklerdir:</span><span class="sxs-lookup"><span data-stu-id="36cf0-142">The following items are protocol and binary breaking changes:</span></span>

* <span data-ttu-id="36cf0-143">**Alanı yeniden adlandırma** -prototip içeriğiyle, alan adları yalnızca oluşturulan kodda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-143">**Renaming a field** - With Protobuf content, the field names are only used in generated code.</span></span> <span data-ttu-id="36cf0-144">Alan numarası, ağdaki alanları tanımlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-144">The field number is used to identify fields on the network.</span></span> <span data-ttu-id="36cf0-145">Bir alanı yeniden adlandırmak, Protoarabelleğe yönelik protokol bölünmesi değişikliği değildir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-145">Renaming a field isn't a protocol breaking change for Protobuf.</span></span> <span data-ttu-id="36cf0-146">Ancak, bir sunucu JSON içeriğini kullanıyorsa, bir alanı yeniden adlandırmak, Son değişiklik olur.</span><span class="sxs-lookup"><span data-stu-id="36cf0-146">However, if a server is using JSON content then renaming a field is a breaking change.</span></span>
* <span data-ttu-id="36cf0-147">**Alan veri türünü değiştirme** -bir alanın veri türünü [uyumsuz bir türe](https://developers.google.com/protocol-buffers/docs/proto3#updating) değiştirme, iletinin serisi kaldırılırken hatalara neden olur.</span><span class="sxs-lookup"><span data-stu-id="36cf0-147">**Changing a field data type** - Changing a field's data type to an [incompatible type](https://developers.google.com/protocol-buffers/docs/proto3#updating) will cause errors when deserializing the message.</span></span> <span data-ttu-id="36cf0-148">Yeni veri türü uyumlu olsa bile, en son sözleşmeye yükseltiyorsa istemcinin yeni türü destekleyecek şekilde güncelleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-148">Even if the new data type is compatible, it's likely the client needs to be updated to support the new type if it upgrades to the latest contract.</span></span>
* <span data-ttu-id="36cf0-149">**Alan numarasını değiştirme** -prototip yükleri ile, alan numarası ağdaki alanları tanımlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-149">**Changing a field number** - With Protobuf payloads, the field number is used to identify fields on the network.</span></span>
* <span data-ttu-id="36cf0-150">**Bir paketi, hizmet veya yöntemi yeniden adlandırma** -GRPC, URL 'yi oluşturmak için paket adını, hizmet adını ve yöntem adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-150">**Renaming a package, service or method** - gRPC uses the package name, service name, and method name to build the URL.</span></span> <span data-ttu-id="36cf0-151">İstemci sunucudan *uygulanmayan* bir durum alır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-151">The client gets an *UNIMPLEMENTED* status from the server.</span></span>
* <span data-ttu-id="36cf0-152">**Bir hizmet veya yöntemi kaldırma** -istemci, kaldırılan yöntemi çağırırken sunucudan *uygulanmayan* bir durum alır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-152">**Removing a service or method** - The client gets an *UNIMPLEMENTED* status from the server when calling the removed method.</span></span>

### <a name="behavior-breaking-changes"></a><span data-ttu-id="36cf0-153">Davranış son değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="36cf0-153">Behavior breaking changes</span></span>

<span data-ttu-id="36cf0-154">Kırılmamış olmayan değişiklikler yaparken, eski istemcilerin yeni hizmet davranışıyla çalışmaya devam edip etmediğini de göz önünde bulundurmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-154">When making non-breaking changes, you must also consider whether older clients can continue working with the new service behavior.</span></span> <span data-ttu-id="36cf0-155">Örneğin, bir istek iletisine yeni bir alan ekleme:</span><span class="sxs-lookup"><span data-stu-id="36cf0-155">For example, adding a new field to a request message:</span></span>

* <span data-ttu-id="36cf0-156">Bir protokol bölünmesi değişikliği değildir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-156">Isn't a protocol breaking change.</span></span>
* <span data-ttu-id="36cf0-157">Yeni alan ayarlanmamışsa sunucuya bir hata durumu döndürülüyor, eski istemciler için bir değişiklik yapmaz.</span><span class="sxs-lookup"><span data-stu-id="36cf0-157">Returning an error status on the server if the new field isn't set makes it a breaking change for old clients.</span></span>

<span data-ttu-id="36cf0-158">Davranış uyumluluğu, uygulamaya özgü kodunuz tarafından belirlenir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-158">Behavior compatibility is determined by your app-specific code.</span></span>

## <a name="version-number-services"></a><span data-ttu-id="36cf0-159">Sürüm numarası Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="36cf0-159">Version number services</span></span>

<span data-ttu-id="36cf0-160">Hizmetler, eski istemcilerle geriye dönük olarak uyumlu kalacak şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-160">Services should strive to remain backwards compatible with old clients.</span></span> <span data-ttu-id="36cf0-161">Uygulamanızda yapılan değişiklikler, son değişiklikleri gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-161">Eventually changes to your app may require breaking changes.</span></span> <span data-ttu-id="36cf0-162">Eski istemcileri bozun ve hizmetinize birlikte güncelleştirilmesini zorluyor, iyi bir kullanıcı deneyimi değildir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-162">Breaking old clients and forcing them to be updated along with your service isn't a good user experience.</span></span> <span data-ttu-id="36cf0-163">Geriye dönük uyumluluğu sağlamanın bir yolu, büyük değişiklikler yapılırken bir hizmetin birden çok sürümünü yayımlamaktır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-163">A way to maintain backwards compatibility while making breaking changes is to publish multiple versions of a service.</span></span>

<span data-ttu-id="36cf0-164">gRPC, .NET ad alanı gibi işlev gören isteğe bağlı bir [paket](https://developers.google.com/protocol-buffers/docs/proto3#packages) tanımlayıcısını destekler.</span><span class="sxs-lookup"><span data-stu-id="36cf0-164">gRPC supports an optional [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) specifier, which functions much like a .NET namespace.</span></span> <span data-ttu-id="36cf0-165">Aslında, `package` `option csharp_namespace` *. proto* dosyasında ayarlanmamışsa, oluşturulan .net türleri için .net ad alanı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-165">In fact, the `package` will be used as the .NET namespace for generated .NET types if `option csharp_namespace` is not set in the *.proto* file.</span></span> <span data-ttu-id="36cf0-166">Paket, hizmetiniz ve iletileri için bir sürüm numarası belirtmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="36cf0-166">The package can be used to specify a version number for your service and its messages:</span></span>

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

<span data-ttu-id="36cf0-167">Hizmet adresini tanımlamak için paket adı hizmet adıyla birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-167">The package name is combined with the service name to identify a service address.</span></span> <span data-ttu-id="36cf0-168">Hizmet adresi, bir hizmetin birden çok sürümünün yan yana barındırılmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="36cf0-168">A service address allows multiple versions of a service to be hosted side-by-side:</span></span>

* `greet.v1.Greeter`
* `greet.v2.Greeter`

<span data-ttu-id="36cf0-169">Sürümlenmiş hizmetin uygulamaları *Startup.cs*'ye kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="36cf0-169">Implementations of the versioned service are registered in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

<span data-ttu-id="36cf0-170">Paket adında bir sürüm numarası dahil olmak üzere, bir *v2* sürümünü, son değişiklikler ile yayımlama olanağı sağlar. bu sayede *v1* sürümünü çağıran eski istemcileri desteklemeye devam edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="36cf0-170">Including a version number in the package name gives you the opportunity to publish a *v2* version of your service with breaking changes, while continuing to support older clients who call the *v1* version.</span></span> <span data-ttu-id="36cf0-171">İstemciler, *v2* hizmetini kullanmak üzere güncelleştirildikten sonra eski sürümü kaldırmayı seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="36cf0-171">Once clients have updated to use the *v2* service, you can choose to remove the old version.</span></span> <span data-ttu-id="36cf0-172">Bir hizmetin birden çok sürümünü yayımlamayı planlarken:</span><span class="sxs-lookup"><span data-stu-id="36cf0-172">When planning to publish multiple versions of a service:</span></span>

* <span data-ttu-id="36cf0-173">Makul olursa değişikliklerden kaçının.</span><span class="sxs-lookup"><span data-stu-id="36cf0-173">Avoid breaking changes if reasonable.</span></span>
* <span data-ttu-id="36cf0-174">Son değişiklik yapmadığınız takdirde sürüm numarasını güncelleştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="36cf0-174">Don't update the version number unless making breaking changes.</span></span>
* <span data-ttu-id="36cf0-175">Büyük değişiklikler yaptığınızda sürüm numarasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="36cf0-175">Do update the version number when you make breaking changes.</span></span>

<span data-ttu-id="36cf0-176">Bir hizmetin birden çok sürümünün yayımlanması onu çoğaltır.</span><span class="sxs-lookup"><span data-stu-id="36cf0-176">Publishing multiple versions of a service duplicates it.</span></span> <span data-ttu-id="36cf0-177">Yinelemeyi azaltmak için, iş mantığını hizmet uygulamalarından eski ve yeni uygulamalar tarafından yeniden kullanılabilen merkezi bir konuma taşımayı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="36cf0-177">To reduce duplication, consider moving business logic from the service implementations to a centralized location that can be reused by the old and new implementations:</span></span>

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

<span data-ttu-id="36cf0-178">Farklı paket adlarıyla oluşturulan hizmetler ve mesajlar **farklı .net türlerdir**.</span><span class="sxs-lookup"><span data-stu-id="36cf0-178">Services and messages generated with different package names are **different .NET types**.</span></span> <span data-ttu-id="36cf0-179">İş mantığını merkezi bir konuma taşımak, iletilerin ortak türlere eşlenmelerini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="36cf0-179">Moving business logic to a centralized location requires mapping messages to common types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="36cf0-180">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="36cf0-180">Additional resources</span></span>

* <xref:grpc/protobuf>
