---
title: GRPC araçlarıyla test hizmetleri
author: jamesnk
description: GRPC araçlarıyla Hizmetleri test etme hakkında bilgi edinin. gRPC hizmetleriyle etkileşim kurmak için bir komut satırı aracını GRP. Grpcuı etkileşimli bir Web Kullanıcı arabirimi.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594433"
---
# <a name="test-services-with-grpc-tools"></a><span data-ttu-id="62fe7-105">GRPC araçlarıyla test hizmetleri</span><span class="sxs-lookup"><span data-stu-id="62fe7-105">Test services with gRPC tools</span></span>

<span data-ttu-id="62fe7-106">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="62fe7-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="62fe7-107">Araç, geliştiricilerin istemci uygulamaları oluşturmadan Hizmetleri test etmesine olanak tanıyan gRPC için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="62fe7-107">Tooling is available for gRPC that allows developers to test services without building client apps.</span></span> <span data-ttu-id="62fe7-108">[Grpkıvrık](https://github.com/fullstorydev/grpcurl) , GRPC hizmetleriyle etkileşim sağlayan bir komut satırı aracıdır.</span><span class="sxs-lookup"><span data-stu-id="62fe7-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span> <span data-ttu-id="62fe7-109">[Grpcuı](https://github.com/fullstorydev/grpcui) , GRPC için etkileşimli bir Web Kullanıcı arabirimi ekler.</span><span class="sxs-lookup"><span data-stu-id="62fe7-109">[gRPCui](https://github.com/fullstorydev/grpcui) adds an interactive web UI for gRPC.</span></span>

<span data-ttu-id="62fe7-110">Bu makalede nasıl yapılacağı açıklanır:</span><span class="sxs-lookup"><span data-stu-id="62fe7-110">This article discusses how to:</span></span>

* <span data-ttu-id="62fe7-111">Grpkıvrık ve Grpcuı indirip yükleyin.</span><span class="sxs-lookup"><span data-stu-id="62fe7-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="62fe7-112">GRPC yansımasını bir gRPC ASP.NET Core uygulamasıyla ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="62fe7-112">Setup gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="62fe7-113">İle gRPC hizmetlerini bulur ve test edin `grpcurl` .</span><span class="sxs-lookup"><span data-stu-id="62fe7-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="62fe7-114">Kullanarak bir tarayıcı aracılığıyla gRPC hizmetleriyle etkileşim kurun `grpcui` .</span><span class="sxs-lookup"><span data-stu-id="62fe7-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="62fe7-115">Grpkıvrımlı hakkında</span><span class="sxs-lookup"><span data-stu-id="62fe7-115">About gRPCurl</span></span>

<span data-ttu-id="62fe7-116">Grpkıvrık, gRPC topluluğu tarafından oluşturulan bir komut satırı aracıdır.</span><span class="sxs-lookup"><span data-stu-id="62fe7-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="62fe7-117">Özellikleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="62fe7-117">Its features include:</span></span>

* <span data-ttu-id="62fe7-118">Akış Hizmetleri dahil gRPC hizmetlerini çağırma.</span><span class="sxs-lookup"><span data-stu-id="62fe7-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="62fe7-119">[GRPC yansımasını](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)kullanan hizmet bulma.</span><span class="sxs-lookup"><span data-stu-id="62fe7-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="62fe7-120">GRPC hizmetlerini listeleme ve açıklama.</span><span class="sxs-lookup"><span data-stu-id="62fe7-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="62fe7-121">Güvenli (TLS) ve güvensiz (düz metin) sunucularla birlikte geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="62fe7-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="62fe7-122">İndirme ve yükleme hakkında daha fazla bilgi için `grpcurl` bkz. [Grpkıvrımlı GitHub giriş sayfası](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="62fe7-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

## <a name="setup-grpc-reflection"></a><span data-ttu-id="62fe7-123">GRPC yansımasını ayarlama</span><span class="sxs-lookup"><span data-stu-id="62fe7-123">Setup gRPC reflection</span></span>

<span data-ttu-id="62fe7-124">`grpcurl` , Hizmetleri çağırabilmesi için bunların Prototipsiz sözleşme anlaşması gerektiğini bilmelidir.</span><span class="sxs-lookup"><span data-stu-id="62fe7-124">`grpcurl` needs to know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="62fe7-125">Bunu yapmak için iki yol vardır:</span><span class="sxs-lookup"><span data-stu-id="62fe7-125">There are two ways to do this:</span></span>

* <span data-ttu-id="62fe7-126">Hizmet sözleşmelerini saptamak için gRPC yansımasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="62fe7-126">Use gRPC reflection to discover service contracts.</span></span>
* <span data-ttu-id="62fe7-127">Komut satırı bağımsız değişkenlerinde *. proto* dosyalarını belirtin.</span><span class="sxs-lookup"><span data-stu-id="62fe7-127">Specify *.proto* files in command-line arguments.</span></span>

<span data-ttu-id="62fe7-128">GRPC yansıtma ve hizmet bulma ile Grpkıvtı kullanmak daha kolay.</span><span class="sxs-lookup"><span data-stu-id="62fe7-128">It's easier to use gRPCurl with gRPC reflection and service discovery.</span></span> <span data-ttu-id="62fe7-129">gRPC ASP.NET Core GRPC [. AspNetCore. Server. Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) paketiyle GRPC yansıması için yerleşik desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="62fe7-129">gRPC ASP.NET Core has built-in support for gRPC reflection with the [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="62fe7-130">Bir uygulamadaki yansımayı yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="62fe7-130">To configure reflection in an app:</span></span>

* <span data-ttu-id="62fe7-131">`Grpc.AspNetCore.Server.Reflection`Paket başvurusu Ekle.</span><span class="sxs-lookup"><span data-stu-id="62fe7-131">Add `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="62fe7-132">Yansımayı *Startup.cs*'e Kaydet:</span><span class="sxs-lookup"><span data-stu-id="62fe7-132">Register reflection in *Startup.cs*:</span></span>
  * <span data-ttu-id="62fe7-133">`AddGrpcReflection` yansımayı etkinleştiren Hizmetleri kaydetmek için.</span><span class="sxs-lookup"><span data-stu-id="62fe7-133">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="62fe7-134">`MapGrpcReflectionService` yansıma hizmeti uç noktası eklemek için.</span><span class="sxs-lookup"><span data-stu-id="62fe7-134">`MapGrpcReflectionService` to add reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a><span data-ttu-id="62fe7-135">`grpcurl` komutunu kullanma</span><span class="sxs-lookup"><span data-stu-id="62fe7-135">Use `grpcurl`</span></span>

<span data-ttu-id="62fe7-136">`-help`Bağımsız değişkeni `grpcurl` komut satırı seçeneklerini açıklar:</span><span class="sxs-lookup"><span data-stu-id="62fe7-136">The `-help` argument explains `grpcurl` command-line options:</span></span>

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a><span data-ttu-id="62fe7-137">Hizmetleri bulma</span><span class="sxs-lookup"><span data-stu-id="62fe7-137">Discover services</span></span>

<span data-ttu-id="62fe7-138">`describe`Sunucu tarafından tanımlanan hizmetleri görüntülemek için fiili ' i kullanın:</span><span class="sxs-lookup"><span data-stu-id="62fe7-138">Use the `describe` verb to view the services defined by the server:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

<span data-ttu-id="62fe7-139">Önceki örnek:</span><span class="sxs-lookup"><span data-stu-id="62fe7-139">The preceding example:</span></span>

* <span data-ttu-id="62fe7-140">`describe`Sunucuda fiili çalıştırır `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="62fe7-140">Runs `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="62fe7-141">GRPC yansımasıyla döndürülen Hizmetleri ve yöntemleri yazdırır.</span><span class="sxs-lookup"><span data-stu-id="62fe7-141">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="62fe7-142">`Greeter` uygulama tarafından uygulanan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="62fe7-142">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="62fe7-143">`ServerReflection` hizmet paket tarafından eklendi `Grpc.AspNetCore.Server.Reflection` .</span><span class="sxs-lookup"><span data-stu-id="62fe7-143">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="62fe7-144">`describe`Ayrıntılarını görüntülemek için bir hizmet, yöntem veya ileti adıyla birleştirin:</span><span class="sxs-lookup"><span data-stu-id="62fe7-144">Combine `describe` with a service, method or message name to view its detail:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="62fe7-145">GRPC hizmetlerini çağırma</span><span class="sxs-lookup"><span data-stu-id="62fe7-145">Call gRPC services</span></span>

<span data-ttu-id="62fe7-146">Bir hizmet ve Yöntem adı belirterek, istek iletisini temsil eden bir JSON bağımsız değişkeniyle birlikte bir gRPC hizmeti çağırın.</span><span class="sxs-lookup"><span data-stu-id="62fe7-146">Call a gRPC service by specifying a service and method name, along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="62fe7-147">JSON, Protoarabelleğe dönüştürülüp hizmete gönderilir.</span><span class="sxs-lookup"><span data-stu-id="62fe7-147">The JSON is converted into Protobuf and sent to the service.</span></span>

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="62fe7-148">Önceki örnek:</span><span class="sxs-lookup"><span data-stu-id="62fe7-148">The preceding example:</span></span>

* <span data-ttu-id="62fe7-149">`-d` bağımsız değişken JSON ile bir istek iletisi belirtir.</span><span class="sxs-lookup"><span data-stu-id="62fe7-149">`-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="62fe7-150">Bu bağımsız değişken, sunucu adresi ve yöntem adından önce gelmelidir.</span><span class="sxs-lookup"><span data-stu-id="62fe7-150">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="62fe7-151">`SayHello`Hizmeti üzerinde yöntemini çağırır `greeter.Greeter` .</span><span class="sxs-lookup"><span data-stu-id="62fe7-151">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="62fe7-152">Yanıt iletisini JSON olarak yazdırır.</span><span class="sxs-lookup"><span data-stu-id="62fe7-152">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="62fe7-153">Grpcuı hakkında</span><span class="sxs-lookup"><span data-stu-id="62fe7-153">About gRPCui</span></span>

<span data-ttu-id="62fe7-154">Grpcuı, gRPC için etkileşimli bir Web Kullanıcı arabirimi.</span><span class="sxs-lookup"><span data-stu-id="62fe7-154">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="62fe7-155">Bu, Grpwrapper 'ın üzerinde oluşturulur ve, Postman gibi HTTP araçlarına benzer şekilde gRPC hizmetlerini keşfetmek ve test etmek için bir GUI sağlar.</span><span class="sxs-lookup"><span data-stu-id="62fe7-155">It builds on top of gRPCurl, and offers a GUI for discovering and testing gRPC services, similar to HTTP tools like Postman.</span></span>

<span data-ttu-id="62fe7-156">İndirme ve yükleme hakkında bilgi için `grpcui` bkz. [Grpcuı GitHub ana sayfası](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="62fe7-156">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="62fe7-157">`grpcui` kullanma</span><span class="sxs-lookup"><span data-stu-id="62fe7-157">Using `grpcui`</span></span>

<span data-ttu-id="62fe7-158">`grpcui`Bir bağımsız değişken olarak etkileşimde bulunmak için sunucu adresiyle çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="62fe7-158">Run `grpcui` with the server address to interact with as an argument.</span></span>

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="62fe7-159">Araç etkileşimli Web Kullanıcı arabirimi ile bir tarayıcı penceresi başlatır.</span><span class="sxs-lookup"><span data-stu-id="62fe7-159">The tool will launch a browser window with the interactive web UI.</span></span> <span data-ttu-id="62fe7-160">gRPC Hizmetleri, gRPC yansıması kullanılarak otomatik olarak bulunur.</span><span class="sxs-lookup"><span data-stu-id="62fe7-160">gRPC services are automatically discovered using gRPC reflection.</span></span>

![Grpcuı Web Kullanıcı arabirimi](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="62fe7-162">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="62fe7-162">Additional resources</span></span>

* [<span data-ttu-id="62fe7-163">Grpkıvrımlı GitHub giriş sayfası</span><span class="sxs-lookup"><span data-stu-id="62fe7-163">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="62fe7-164">Grpcuı GitHub giriş sayfası</span><span class="sxs-lookup"><span data-stu-id="62fe7-164">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [<span data-ttu-id="62fe7-165">GRPC. AspNetCore. Server. Reflection</span><span class="sxs-lookup"><span data-stu-id="62fe7-165">Grpc.AspNetCore.Server.Reflection</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
