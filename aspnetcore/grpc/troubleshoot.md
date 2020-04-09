---
title: .NET Core'da gRPC'de sorun giderme
author: jamesnk
description: .NET Core'da gRPC kullanırken hataları giderin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664131"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="7dee5-103">.NET Core'da gRPC'de sorun giderme</span><span class="sxs-lookup"><span data-stu-id="7dee5-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="7dee5-104">Yazar: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="7dee5-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="7dee5-105">Bu belgede,.NET'te gRPC uygulamaları geliştirirken sık karşılaşılan sorunlar anlatılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="7dee5-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="7dee5-106">İstemci ve hizmet SSL/TLS yapılandırması arasındaki uyuşmazlık</span><span class="sxs-lookup"><span data-stu-id="7dee5-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="7dee5-107">gRPC şablonu ve örnekleri varsayılan olarak gRPC hizmetlerini güvence altına almak için [Aktarım Katmanı Güvenliği'ni (TLS)](https://tools.ietf.org/html/rfc5246) kullanır.</span><span class="sxs-lookup"><span data-stu-id="7dee5-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="7dee5-108">gRPC istemcilerinin güvenli gRPC hizmetlerini başarıyla aramak için güvenli bir bağlantı kullanmaları gerekir.</span><span class="sxs-lookup"><span data-stu-id="7dee5-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="7dee5-109">Core gRPC hizmetinin ASP.NET TLS kullandığını uygulama başlangıcında yazılan günlüklerde doğrulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7dee5-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="7dee5-110">Hizmet bir HTTPS bitiş noktasından dinliyor olacak:</span><span class="sxs-lookup"><span data-stu-id="7dee5-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="7dee5-111">.NET Core istemcisi, güvenli bir bağlantı yla arama yapmak için sunucu adresinde kullanmalıdır: `https`</span><span class="sxs-lookup"><span data-stu-id="7dee5-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="7dee5-112">Tüm gRPC istemci uygulamaları TLS'yi destekler.</span><span class="sxs-lookup"><span data-stu-id="7dee5-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="7dee5-113">diğer dillerdeki gRPC istemcileri genellikle kanalı `SslCredentials`niskontoile yapılandırılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7dee5-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="7dee5-114">`SslCredentials`istemcinin kullanacağı sertifikayı belirtir ve güvenli olmayan kimlik bilgileri yerine kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7dee5-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="7dee5-115">TLS kullanmak için farklı gRPC istemci uygulamalarını yapılandırma örnekleri için [bkz.](https://www.grpc.io/docs/guides/auth/)</span><span class="sxs-lookup"><span data-stu-id="7dee5-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="7dee5-116">Güvenilmeyen/geçersiz sertifikaya sahip bir gRPC hizmetini arama</span><span class="sxs-lookup"><span data-stu-id="7dee5-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="7dee5-117">.NET gRPC istemcisi, hizmetin güvenilir bir sertifikaya sahip olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7dee5-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="7dee5-118">Güvenilir bir sertifika olmadan bir gRPC hizmetini ararken aşağıdaki hata iletisi döndürülür:</span><span class="sxs-lookup"><span data-stu-id="7dee5-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="7dee5-119">Işlenmemiş özel durum.</span><span class="sxs-lookup"><span data-stu-id="7dee5-119">Unhandled exception.</span></span> <span data-ttu-id="7dee5-120">System.Net.Http.HttpRequestException: SSL bağlantısı kurulamadı, iç özel durumlara bakın.</span><span class="sxs-lookup"><span data-stu-id="7dee5-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="7dee5-121">---> System.Security.Authentication.AuthenticationException: Uzak sertifika doğrulama prosedürüne göre geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="7dee5-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="7dee5-122">Uygulamanızı yerel olarak test ediyorsanız ve ASP.NET Core HTTPS geliştirme sertifikasına güvenilmiyorsa bu hatayı görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7dee5-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="7dee5-123">Bu sorunu gidermek için yönergeleri gidermek için Windows [ve macOS'taki ASP.NET Çekirdek HTTPS geliştirme sertifikasına güven'e](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)bakın.</span><span class="sxs-lookup"><span data-stu-id="7dee5-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="7dee5-124">Başka bir makinede gRPC hizmetini arıyorsanız ve sertifikaya güvenemiyorsanız, gRPC istemcisi geçersiz sertifikayı yoksayacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="7dee5-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="7dee5-125">Aşağıdaki kod, güvenilir bir sertifika olmadan yapılan çağrılara izin vermek için [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) kullanır:</span><span class="sxs-lookup"><span data-stu-id="7dee5-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="7dee5-126">Güvenilmeyen sertifikalar yalnızca uygulama geliştirme sırasında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7dee5-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="7dee5-127">Üretim uygulamaları her zaman geçerli sertifikalar kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7dee5-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="7dee5-128">.NET Core istemcisi ile güvensiz gRPC hizmetlerini arayın</span><span class="sxs-lookup"><span data-stu-id="7dee5-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="7dee5-129">.NET Core istemcisi ile güvenli olmayan gRPC hizmetlerini aramak için ek yapılandırma gereklidir.</span><span class="sxs-lookup"><span data-stu-id="7dee5-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="7dee5-130">gRPC istemcisi `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` anahtarı `true` sunucu `http` adresinde ayarlamalı ve kullanmalıdır:</span><span class="sxs-lookup"><span data-stu-id="7dee5-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="7dee5-131">macOS'ta Core gRPC uygulaması ASP.NET başlatılamıyor</span><span class="sxs-lookup"><span data-stu-id="7dee5-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="7dee5-132">Kerkenez, macOS ve Windows 7 gibi eski Windows sürümlerinde TLS ile HTTP/2'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="7dee5-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="7dee5-133">ASP.NET Core gRPC şablonu ve örnekleri varsayılan olarak TLS kullanır.</span><span class="sxs-lookup"><span data-stu-id="7dee5-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="7dee5-134">gRPC sunucusunu başlatmaya çalıştığınızda aşağıdaki hata iletisini görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="7dee5-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="7dee5-135">IPv4 loopback arabirimine https://localhost:5001 bağlanamıyor: 'TLS üzerinden HTTP/2, EKSIK ALPN desteği nedeniyle macOS'ta desteklenmiyor.'.</span><span class="sxs-lookup"><span data-stu-id="7dee5-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="7dee5-136">Bu sorunu çözmek için Kestrel ve gRPC istemcisini *TLS'siz* HTTP/2 kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="7dee5-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="7dee5-137">Bunu yalnızca geliştirme sırasında yapmalısınız.</span><span class="sxs-lookup"><span data-stu-id="7dee5-137">You should only do this during development.</span></span> <span data-ttu-id="7dee5-138">TLS'nin kullanılmaması, gRPC iletilerinin şifreleme olmadan gönderilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="7dee5-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="7dee5-139">Kerkenez *Program.cs*TLS olmadan bir HTTP/2 bitiş noktası yapılandırmak gerekir:</span><span class="sxs-lookup"><span data-stu-id="7dee5-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="7dee5-140">BIR HTTP/2 bitiş noktası TLS olmadan yapılandırıldığında, bitiş noktasının [ListenOptions.Protocols'u](xref:fundamentals/servers/kestrel#listenoptionsprotocols) `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="7dee5-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="7dee5-141">`HttpProtocols.Http1AndHttp2`TLS HTTP/2'yi müzakere etmek için gerekli olduğundan kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="7dee5-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="7dee5-142">TLS olmadan, bitiş noktası varsayılan tüm bağlantıları HTTP/1.1 ve gRPC aramaları başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="7dee5-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="7dee5-143">gRPC istemcisi de TLS kullanmamak için yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7dee5-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="7dee5-144">Daha fazla bilgi için [bkz.](#call-insecure-grpc-services-with-net-core-client)</span><span class="sxs-lookup"><span data-stu-id="7dee5-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="7dee5-145">TLS'siz HTTP/2 sadece uygulama geliştirme sırasında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7dee5-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="7dee5-146">Üretim uygulamaları her zaman taşıma güvenliğini kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7dee5-146">Production apps should always use transport security.</span></span> <span data-ttu-id="7dee5-147">Daha fazla bilgi için, [ASP.NET Core için gRPC'deki Güvenlik hususlarına](xref:grpc/security#transport-security)bakın.</span><span class="sxs-lookup"><span data-stu-id="7dee5-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="7dee5-148">gRPC C# varlıkları .proto dosyalarından oluşturulan kod değildir</span><span class="sxs-lookup"><span data-stu-id="7dee5-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="7dee5-149">somut istemciler ve hizmet temel sınıflarının gRPC kod oluşturma protobuf dosyaları ve takım bir projeden başvurulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7dee5-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="7dee5-150">Şunları eklemeniz gerekir:</span><span class="sxs-lookup"><span data-stu-id="7dee5-150">You must include:</span></span>

* <span data-ttu-id="7dee5-151">*.madde* grubunda kullanmak `<Protobuf>` istediğiniz proto dosyaları.</span><span class="sxs-lookup"><span data-stu-id="7dee5-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="7dee5-152">[İçe aktarılan *.proto* dosyaları](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) proje tarafından başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7dee5-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="7dee5-153">gRPC takım paketi [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)paket referans .</span><span class="sxs-lookup"><span data-stu-id="7dee5-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="7dee5-154">gRPC C# varlıkları oluşturma hakkında daha <xref:grpc/basics>fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="7dee5-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="7dee5-155">Varsayılan olarak, `<Protobuf>` bir başvuru somut bir istemci ve bir hizmet taban sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7dee5-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="7dee5-156">Başvuru öğesinin `GrpcServices` özniteliği C# varlık oluşturmayı sınırlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7dee5-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="7dee5-157">Geçerli `GrpcServices` seçenekler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="7dee5-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="7dee5-158">`Both`(varsayılan zaman mevcut değil)</span><span class="sxs-lookup"><span data-stu-id="7dee5-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="7dee5-159">gRPC hizmetlerini barındıran ASP.NET Bir Core web uygulamasının yalnızca oluşturulan hizmet tabanı sınıfına ihtiyacı vardır:</span><span class="sxs-lookup"><span data-stu-id="7dee5-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="7dee5-160">gRPC aramaları yapan bir gRPC istemci uygulaması yalnızca oluşturulan somut istemciye ihtiyaç duyar:</span><span class="sxs-lookup"><span data-stu-id="7dee5-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="7dee5-161">.proto dosyalarından gRPC C# varlıkları oluşturamayan WPF projeleri</span><span class="sxs-lookup"><span data-stu-id="7dee5-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="7dee5-162">WPF projelerinde gRPC kod oluşturmanın doğru çalışmasını engelleyen bilinen bir [sorun](https://github.com/dotnet/wpf/issues/810) vardır.</span><span class="sxs-lookup"><span data-stu-id="7dee5-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="7dee5-163">Bir WPF projesinde başvuru `Grpc.Tools` ve *.proto* dosyalarına göre oluşturulan tüm gRPC türleri kullanıldığında derleme hataları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7dee5-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="7dee5-164">hata CS0246: Türü veya namespace adı 'MyGrpcServices' bulunamadı (bir kullanma yönergesi veya bir derleme başvuru eksik?)</span><span class="sxs-lookup"><span data-stu-id="7dee5-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="7dee5-165">Bu sorunu şu şekilde çözebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="7dee5-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="7dee5-166">Yeni bir .NET Core sınıf kitaplık projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7dee5-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="7dee5-167">Yeni projede, [ \* \*.proto\* dosyalarından C# kodu oluşumunu](xref:grpc/basics#generated-c-assets)etkinleştirmek için başvurular ekleyin:</span><span class="sxs-lookup"><span data-stu-id="7dee5-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="7dee5-168">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7dee5-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="7dee5-169">Öğe \* \*grubuna .proto\* dosyaları ekleyin. `<Protobuf>`</span><span class="sxs-lookup"><span data-stu-id="7dee5-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="7dee5-170">WPF uygulamasında, yeni projeye bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7dee5-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="7dee5-171">WPF uygulaması, yeni sınıf kitaplığı projesinden oluşturulan gRPC türlerini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="7dee5-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
