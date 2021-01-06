---
title: ASP.NET Core 'de Grpkıvle gRPC hizmetlerini test edin
author: jamesnk
description: GRPC araçlarıyla Hizmetleri test etme hakkında bilgi edinin. gRPC hizmetleriyle etkileşim kurmak için bir komut satırı aracını GRP. Grpcuı etkileşimli bir Web Kullanıcı arabirimi.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058798"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a><span data-ttu-id="672eb-105">ASP.NET Core 'de Grpkıvle gRPC hizmetlerini test edin</span><span class="sxs-lookup"><span data-stu-id="672eb-105">Test gRPC services with gRPCurl in ASP.NET Core</span></span>

<span data-ttu-id="672eb-106">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="672eb-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="672eb-107">Araç, geliştiricilerin istemci uygulamaları oluşturmadan Hizmetleri test etmesine olanak tanıyan gRPC için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="672eb-107">Tooling is available for gRPC that allows developers to test services without building client apps:</span></span>

* <span data-ttu-id="672eb-108">[Grpkıvrık](https://github.com/fullstorydev/grpcurl) , GRPC hizmetleriyle etkileşim sağlayan bir komut satırı aracıdır.</span><span class="sxs-lookup"><span data-stu-id="672eb-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span>
* <span data-ttu-id="672eb-109">[Grpcuı](https://github.com/fullstorydev/grpcui) , GRP-on üst kısmında derleme yapar ve GRPC Için Postman ve Swagger Kullanıcı arabirimi gibi araçlara benzer bir etkileşimli Web Kullanıcı arabirimi ekler.</span><span class="sxs-lookup"><span data-stu-id="672eb-109">[gRPCui](https://github.com/fullstorydev/grpcui) builds on top of gRPCurl and adds an interactive web UI for gRPC, similar to tools such as Postman and Swagger UI.</span></span>

<span data-ttu-id="672eb-110">Bu makalede nasıl yapılacağı açıklanır:</span><span class="sxs-lookup"><span data-stu-id="672eb-110">This article discusses how to:</span></span>

* <span data-ttu-id="672eb-111">Grpkıvrık ve Grpcuı indirip yükleyin.</span><span class="sxs-lookup"><span data-stu-id="672eb-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="672eb-112">GRPC yansımasını bir gRPC ASP.NET Core uygulamasıyla ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="672eb-112">Set up gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="672eb-113">İle gRPC hizmetlerini bulur ve test edin `grpcurl` .</span><span class="sxs-lookup"><span data-stu-id="672eb-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="672eb-114">Kullanarak bir tarayıcı aracılığıyla gRPC hizmetleriyle etkileşim kurun `grpcui` .</span><span class="sxs-lookup"><span data-stu-id="672eb-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="672eb-115">Grpkıvrımlı hakkında</span><span class="sxs-lookup"><span data-stu-id="672eb-115">About gRPCurl</span></span>

<span data-ttu-id="672eb-116">Grpkıvrık, gRPC topluluğu tarafından oluşturulan bir komut satırı aracıdır.</span><span class="sxs-lookup"><span data-stu-id="672eb-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="672eb-117">Özellikleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="672eb-117">Its features include:</span></span>

* <span data-ttu-id="672eb-118">Akış Hizmetleri dahil gRPC hizmetlerini çağırma.</span><span class="sxs-lookup"><span data-stu-id="672eb-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="672eb-119">[GRPC yansımasını](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)kullanan hizmet bulma.</span><span class="sxs-lookup"><span data-stu-id="672eb-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="672eb-120">GRPC hizmetlerini listeleme ve açıklama.</span><span class="sxs-lookup"><span data-stu-id="672eb-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="672eb-121">Güvenli (TLS) ve güvensiz (düz metin) sunucularla birlikte geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="672eb-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="672eb-122">İndirme ve yükleme hakkında daha fazla bilgi için `grpcurl` bkz. [Grpkıvrımlı GitHub giriş sayfası](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="672eb-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

![Grpkıvrımlı komut satırı](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a><span data-ttu-id="672eb-124">GRPC yansımasını ayarlama</span><span class="sxs-lookup"><span data-stu-id="672eb-124">Set up gRPC reflection</span></span>

<span data-ttu-id="672eb-125">`grpcurl` Hizmetleri çağırabilmeniz için önce hizmet sözleşmesi 'nin bir anlaşması bilmelidir.</span><span class="sxs-lookup"><span data-stu-id="672eb-125">`grpcurl` must know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="672eb-126">Bunu yapmak için iki yol vardır:</span><span class="sxs-lookup"><span data-stu-id="672eb-126">There are two ways to do this:</span></span>

* <span data-ttu-id="672eb-127">Sunucuda [GRPC yansımasını](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="672eb-127">Set up [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) on the server.</span></span> <span data-ttu-id="672eb-128">Grpkıvrımlı, hizmet sözleşmelerini otomatik olarak bulur.</span><span class="sxs-lookup"><span data-stu-id="672eb-128">gRPCurl automatically discovers service contracts.</span></span>
* <span data-ttu-id="672eb-129">`.proto`Grphizalamak için komut satırı bağımsız değişkenlerinde dosyaları belirtin.</span><span class="sxs-lookup"><span data-stu-id="672eb-129">Specify `.proto` files in command-line arguments to gRPCurl.</span></span>

<span data-ttu-id="672eb-130">GRPC yansımasıyla Grpkıvtı kullanmak daha kolay.</span><span class="sxs-lookup"><span data-stu-id="672eb-130">It's easier to use gRPCurl with gRPC reflection.</span></span> <span data-ttu-id="672eb-131">gRPC Reflection, uygulamanın Hizmetleri bulma için çağırabildiği uygulamaya yeni bir gRPC hizmeti ekler.</span><span class="sxs-lookup"><span data-stu-id="672eb-131">gRPC reflection adds a new gRPC service to the app that clients can call to discover services.</span></span>

<span data-ttu-id="672eb-132">gRPC ASP.NET Core, paket ile gRPC yansıtma için yerleşik desteğe sahiptir [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) .</span><span class="sxs-lookup"><span data-stu-id="672eb-132">gRPC ASP.NET Core has built-in support for gRPC reflection with the [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="672eb-133">Bir uygulamadaki yansımayı yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="672eb-133">To configure reflection in an app:</span></span>

* <span data-ttu-id="672eb-134">Bir `Grpc.AspNetCore.Server.Reflection` paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="672eb-134">Add a `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="672eb-135">Yansımayı Kaydet `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="672eb-135">Register reflection in `Startup.cs`:</span></span>
  * <span data-ttu-id="672eb-136">`AddGrpcReflection` yansımayı etkinleştiren Hizmetleri kaydetmek için.</span><span class="sxs-lookup"><span data-stu-id="672eb-136">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="672eb-137">`MapGrpcReflectionService` bir yansıma hizmeti uç noktası eklemek için.</span><span class="sxs-lookup"><span data-stu-id="672eb-137">`MapGrpcReflectionService` to add a reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

<span data-ttu-id="672eb-138">GRPC yansıması ayarlandığında:</span><span class="sxs-lookup"><span data-stu-id="672eb-138">When gRPC reflection is set up:</span></span>

* <span data-ttu-id="672eb-139">Sunucu uygulamasına bir gRPC yansıma hizmeti eklenir.</span><span class="sxs-lookup"><span data-stu-id="672eb-139">A gRPC reflection service is added to the server app.</span></span>
* <span data-ttu-id="672eb-140">GRPC yansımasını destekleyen istemci uygulamaları, sunucu tarafından barındırılan Hizmetleri bulmaya yönelik yansıma hizmetini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="672eb-140">Client apps that support gRPC reflection can call the reflection service to discover services hosted by the server.</span></span>
* <span data-ttu-id="672eb-141">gRPC hizmetleri yine de istemciden çağırılır.</span><span class="sxs-lookup"><span data-stu-id="672eb-141">gRPC services are still called from the client.</span></span> <span data-ttu-id="672eb-142">Yansıma yalnızca hizmet bulmayı ve sunucu tarafı güvenliği atlamalarını mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="672eb-142">Reflection only enables service discovery and doesn't bypass server-side security.</span></span> <span data-ttu-id="672eb-143">[Kimlik doğrulama ve yetkilendirme](xref:grpc/authn-and-authz) tarafından korunan uç noktalar, çağıranın, uç nokta için kimlik bilgilerini başarıyla çağrılacak şekilde geçmesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="672eb-143">Endpoints protected by [authentication and authorization](xref:grpc/authn-and-authz) require the caller to pass credentials for the endpoint to be called successfully.</span></span>

## <a name="use-grpcurl"></a><span data-ttu-id="672eb-144">`grpcurl` komutunu kullanma</span><span class="sxs-lookup"><span data-stu-id="672eb-144">Use `grpcurl`</span></span>

<span data-ttu-id="672eb-145">`-help`Bağımsız değişkeni `grpcurl` komut satırı seçeneklerini açıklar:</span><span class="sxs-lookup"><span data-stu-id="672eb-145">The `-help` argument explains `grpcurl` command-line options:</span></span>

```console
$ grpcurl -help
```

### <a name="discover-services"></a><span data-ttu-id="672eb-146">Hizmetleri bulma</span><span class="sxs-lookup"><span data-stu-id="672eb-146">Discover services</span></span>

<span data-ttu-id="672eb-147">`describe`Sunucu tarafından tanımlanan hizmetleri görüntülemek için fiili ' i kullanın:</span><span class="sxs-lookup"><span data-stu-id="672eb-147">Use the `describe` verb to view the services defined by the server:</span></span>

```console
$ grpcurl localhost:5001 describe
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

<span data-ttu-id="672eb-148">Önceki örnek:</span><span class="sxs-lookup"><span data-stu-id="672eb-148">The preceding example:</span></span>

* <span data-ttu-id="672eb-149">`describe`Sunucuda fiili çalıştırır `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="672eb-149">Runs the `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="672eb-150">GRPC yansımasıyla döndürülen Hizmetleri ve yöntemleri yazdırır.</span><span class="sxs-lookup"><span data-stu-id="672eb-150">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="672eb-151">`Greeter` uygulama tarafından uygulanan bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="672eb-151">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="672eb-152">`ServerReflection` hizmet paket tarafından eklendi `Grpc.AspNetCore.Server.Reflection` .</span><span class="sxs-lookup"><span data-stu-id="672eb-152">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="672eb-153">`describe`Ayrıntılarını görüntülemek için bir hizmet, yöntem veya ileti adıyla birleştirin:</span><span class="sxs-lookup"><span data-stu-id="672eb-153">Combine `describe` with a service, method, or message name to view its detail:</span></span>

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="672eb-154">GRPC hizmetlerini çağırma</span><span class="sxs-lookup"><span data-stu-id="672eb-154">Call gRPC services</span></span>

<span data-ttu-id="672eb-155">İstek iletisini temsil eden bir JSON bağımsız değişkeniyle birlikte bir hizmet ve Yöntem adı belirterek bir gRPC hizmeti çağırın.</span><span class="sxs-lookup"><span data-stu-id="672eb-155">Call a gRPC service by specifying a service and method name along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="672eb-156">JSON, Protoarabelleğe dönüştürülüp hizmete gönderilir.</span><span class="sxs-lookup"><span data-stu-id="672eb-156">The JSON is converted into Protobuf and sent to the service.</span></span>

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="672eb-157">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="672eb-157">In the preceding example:</span></span>

* <span data-ttu-id="672eb-158">`-d`Bağımsız DEĞIŞKENI JSON ile bir istek iletisi belirtir.</span><span class="sxs-lookup"><span data-stu-id="672eb-158">The `-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="672eb-159">Bu bağımsız değişken, sunucu adresi ve yöntem adından önce gelmelidir.</span><span class="sxs-lookup"><span data-stu-id="672eb-159">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="672eb-160">`SayHello`Hizmeti üzerinde yöntemini çağırır `greeter.Greeter` .</span><span class="sxs-lookup"><span data-stu-id="672eb-160">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="672eb-161">Yanıt iletisini JSON olarak yazdırır.</span><span class="sxs-lookup"><span data-stu-id="672eb-161">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="672eb-162">Grpcuı hakkında</span><span class="sxs-lookup"><span data-stu-id="672eb-162">About gRPCui</span></span>

<span data-ttu-id="672eb-163">Grpcuı, gRPC için etkileşimli bir Web Kullanıcı arabirimi.</span><span class="sxs-lookup"><span data-stu-id="672eb-163">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="672eb-164">Grpwrapper 'ın üzerine oluþturulur ve gRPC hizmetlerini keşfetmek ve test etmek için Postman veya Swagger Kullanıcı arabirimi gibi HTTP araçlarına benzer bir GUI sağlar.</span><span class="sxs-lookup"><span data-stu-id="672eb-164">It builds on top of gRPCurl and offers a GUI for discovering and testing gRPC services, similar to HTTP tools such as Postman or Swagger UI.</span></span>

<span data-ttu-id="672eb-165">İndirme ve yükleme hakkında bilgi için `grpcui` bkz. [Grpcuı GitHub ana sayfası](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="672eb-165">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="672eb-166">`grpcui` kullanma</span><span class="sxs-lookup"><span data-stu-id="672eb-166">Using `grpcui`</span></span>

<span data-ttu-id="672eb-167">`grpcui`Bir bağımsız değişken olarak etkileşimde bulunmak için sunucu adresiyle çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="672eb-167">Run `grpcui` with the server address to interact with as an argument:</span></span>

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="672eb-168">Araç etkileşimli Web Kullanıcı arabirimi ile bir tarayıcı penceresi başlatır.</span><span class="sxs-lookup"><span data-stu-id="672eb-168">The tool launches a browser window with the interactive web UI.</span></span> <span data-ttu-id="672eb-169">gRPC Hizmetleri, gRPC yansıması kullanılarak otomatik olarak bulunur.</span><span class="sxs-lookup"><span data-stu-id="672eb-169">gRPC services are automatically discovered using gRPC reflection.</span></span>

![Grpcuı Web Kullanıcı arabirimi](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="672eb-171">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="672eb-171">Additional resources</span></span>

* [<span data-ttu-id="672eb-172">Grpkıvrımlı GitHub giriş sayfası</span><span class="sxs-lookup"><span data-stu-id="672eb-172">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="672eb-173">Grpcuı GitHub giriş sayfası</span><span class="sxs-lookup"><span data-stu-id="672eb-173">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
