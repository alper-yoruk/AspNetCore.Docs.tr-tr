---
title: .NET Core 'da gRPC sorunlarını giderme
author: jamesnk
description: .NET Core 'da gRPC kullanırken hata giderme.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/09/2020
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
uid: grpc/troubleshoot
ms.openlocfilehash: cbce85caf7ba792253ba62c6be084c8905acd00f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058720"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="7f63c-103">.NET Core 'da gRPC sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="7f63c-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="7f63c-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="7f63c-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="7f63c-105">Bu belgede, .NET üzerinde gRPC uygulamaları geliştirirken yaygın olarak karşılaşılan sorunlar ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="7f63c-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="7f63c-106">İstemci ve hizmet SSL/TLS yapılandırması arasında uyuşmazlık</span><span class="sxs-lookup"><span data-stu-id="7f63c-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="7f63c-107">GRPC şablonu ve örnekleri, varsayılan olarak gRPC hizmetlerini güvenli hale getirmek için [Aktarım Katmanı Güvenliği 'ni (TLS)](https://tools.ietf.org/html/rfc5246) kullanır.</span><span class="sxs-lookup"><span data-stu-id="7f63c-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="7f63c-108">gRPC istemcilerinin güvenli gRPC hizmetlerini başarıyla çağırması için güvenli bir bağlantı kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7f63c-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="7f63c-109">ASP.NET Core gRPC hizmetinin uygulama başlatma bölümünde yazılan günlüklerde TLS kullandığını doğrulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7f63c-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="7f63c-110">Hizmet bir HTTPS uç noktasını dinleyerek:</span><span class="sxs-lookup"><span data-stu-id="7f63c-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="7f63c-111">.NET Core istemcisinin, `https` güvenli bir bağlantıyla çağrı yapmak için sunucu adresinde kullanması gerekir:</span><span class="sxs-lookup"><span data-stu-id="7f63c-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="7f63c-112">Tüm gRPC istemci uygulamaları TLS 'yi destekler.</span><span class="sxs-lookup"><span data-stu-id="7f63c-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="7f63c-113">diğer dillerden gRPC istemcileri, genellikle kanalı ile yapılandırılmış olmasını gerektirir `SslCredentials` .</span><span class="sxs-lookup"><span data-stu-id="7f63c-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="7f63c-114">`SslCredentials` İstemcinin kullanacağı sertifikayı belirtir ve güvenli olmayan kimlik bilgileri yerine kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7f63c-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="7f63c-115">Farklı gRPC istemci uygulamalarını TLS kullanmak üzere yapılandırma örnekleri için bkz. [GRPC kimlik doğrulaması](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="7f63c-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="7f63c-116">Güvenilir olmayan/geçersiz sertifikayla gRPC hizmetini çağırma</span><span class="sxs-lookup"><span data-stu-id="7f63c-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="7f63c-117">.NET gRPC istemcisi hizmetin güvenilen sertifikaya sahip olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7f63c-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="7f63c-118">Bir gRPC hizmeti güvenilir bir sertifika olmadan çağrılırken aşağıdaki hata iletisi döndürülür:</span><span class="sxs-lookup"><span data-stu-id="7f63c-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="7f63c-119">İşlenmeyen özel durum.</span><span class="sxs-lookup"><span data-stu-id="7f63c-119">Unhandled exception.</span></span> <span data-ttu-id="7f63c-120">System .net. http. HttpRequestException: SSL bağlantısı kurulamadı, iç özel duruma bakın.</span><span class="sxs-lookup"><span data-stu-id="7f63c-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="7f63c-121">---> System. Security. Authentication. AuthenticationException: uzak sertifika, doğrulama yordamına göre geçersiz.</span><span class="sxs-lookup"><span data-stu-id="7f63c-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="7f63c-122">Uygulamanızı yerel olarak test ediyorsanız ve ASP.NET Core HTTPS geliştirme sertifikası güvenilir değilse bu hatayı görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7f63c-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="7f63c-123">Bu sorunu gidermeye yönelik yönergeler için bkz. [Windows ve macOS 'ta ASP.NET Core https geliştirme sertifikasına güvenin](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="7f63c-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="7f63c-124">Bir gRPC hizmetini başka bir makineye arıyorsanız ve sertifikaya güvenmiyorsanız, gRPC istemcisi geçersiz sertifikayı yoksayacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="7f63c-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="7f63c-125">Aşağıdaki kod, güvenilir bir sertifika olmadan çağrılara izin vermek için [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) kullanır:</span><span class="sxs-lookup"><span data-stu-id="7f63c-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="7f63c-126">Güvenilmeyen sertifikalar yalnızca uygulama geliştirme sırasında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f63c-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="7f63c-127">Üretim uygulamaları her zaman geçerli sertifikaları kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f63c-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="7f63c-128">.NET Core istemcisiyle güvenli olmayan gRPC hizmetlerini çağırma</span><span class="sxs-lookup"><span data-stu-id="7f63c-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="7f63c-129">Bir uygulama .NET Core 3. x kullanıyorsa, .NET Core istemcisiyle güvenli olmayan gRPC hizmetlerini çağırmak için ek yapılandırma gerekir.</span><span class="sxs-lookup"><span data-stu-id="7f63c-129">When an app is using .NET Core 3.x, additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="7f63c-130">GRPC istemcisinin, `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` sunucu adresinde anahtarı olarak ayarlanması `true` ve kullanması gerekir `http` :</span><span class="sxs-lookup"><span data-stu-id="7f63c-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="7f63c-131">.NET 5 uygulamalarına ek yapılandırma gerekmez, ancak güvenli olmayan gRPC hizmetlerini çağırmak için `Grpc.Net.Client` 2.32.0 veya üzeri sürümlerini kullanmaları gerekir.</span><span class="sxs-lookup"><span data-stu-id="7f63c-131">.NET 5 apps don't need additional configuration, but to call insecure gRPC services they must use `Grpc.Net.Client` version 2.32.0 or later.</span></span>

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="7f63c-132">MacOS 'ta ASP.NET Core gRPC uygulaması başlatılamıyor</span><span class="sxs-lookup"><span data-stu-id="7f63c-132">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="7f63c-133">Kestrel, macOS ve Windows 7 gibi eski Windows sürümlerindeki TLS ile HTTP/2 ' yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="7f63c-133">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="7f63c-134">ASP.NET Core gRPC şablonu ve örnekleri varsayılan olarak TLS kullanır.</span><span class="sxs-lookup"><span data-stu-id="7f63c-134">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="7f63c-135">GRPC sunucusunu başlatmayı denediğinizde aşağıdaki hata iletisini görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="7f63c-135">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="7f63c-136"> https://localhost:5001IPv4 geri döngü arabirimine bağlanılamıyor: eksik ALPN desteği nedeniyle, macOS 'ta ' http/2 TLS üzerinden desteklenmez. '.</span><span class="sxs-lookup"><span data-stu-id="7f63c-136">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="7f63c-137">Bu sorunu geçici olarak çözmek için Kestrel ve gRPC istemcisini TLS *olmadan* http/2 kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="7f63c-137">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="7f63c-138">Bunu yalnızca geliştirme sırasında yapmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="7f63c-138">You should only do this during development.</span></span> <span data-ttu-id="7f63c-139">TLS 'nin kullanılması, gRPC iletilerinin şifrelenmeden gönderilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="7f63c-139">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="7f63c-140">Kestrel, *program.cs* içinde TLS olmadan bir http/2 uç noktası yapılandırmalıdır:</span><span class="sxs-lookup"><span data-stu-id="7f63c-140">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="7f63c-141">Bir HTTP/2 uç noktası TLS olmadan yapılandırıldığında, uç noktanın [Listenoptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) olarak ayarlanması gerekir `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="7f63c-141">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="7f63c-142">`HttpProtocols.Http1AndHttp2` , HTTP/2 üzerinde anlaşmak için TLS gerektiğinden kullanılamıyor.</span><span class="sxs-lookup"><span data-stu-id="7f63c-142">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="7f63c-143">TLS olmadan, uç nokta varsayılan HTTP/1.1 ve gRPC çağrıları için tüm bağlantılar başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="7f63c-143">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="7f63c-144">GRPC istemcisinin, TLS kullanmak için de yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7f63c-144">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="7f63c-145">Daha fazla bilgi için bkz. [.NET Core istemcisiyle güvenli olmayan gRPC hizmetlerini çağırma](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="7f63c-145">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="7f63c-146">HTTP/2, TLS olmadan yalnızca uygulama geliştirme sırasında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f63c-146">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="7f63c-147">Üretim uygulamaları her zaman aktarım güvenliği kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f63c-147">Production apps should always use transport security.</span></span> <span data-ttu-id="7f63c-148">Daha fazla bilgi için bkz. [gRPC 'Deki güvenlik konuları ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="7f63c-148">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="7f63c-149">gRPC C# varlıkları. proto dosyalarından oluşturulan kod değildir</span><span class="sxs-lookup"><span data-stu-id="7f63c-149">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="7f63c-150">aynı somut istemci ve hizmet temel sınıflarının gRPC kod üretimi, bir projeden başvurulmak için prototip dosyaları ve araçları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7f63c-150">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="7f63c-151">Şunları dahil etmeniz gerekir:</span><span class="sxs-lookup"><span data-stu-id="7f63c-151">You must include:</span></span>

* <span data-ttu-id="7f63c-152">öğe grubunda kullanmak istediğiniz *. proto* dosyaları `<Protobuf>` .</span><span class="sxs-lookup"><span data-stu-id="7f63c-152">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="7f63c-153">[Içeri aktarılan *. proto* dosyalarına](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) proje tarafından başvurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7f63c-153">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="7f63c-154">GRPC araç paketi [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/)'a paket başvurusu.</span><span class="sxs-lookup"><span data-stu-id="7f63c-154">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="7f63c-155">GRPC C# varlıkları oluşturma hakkında daha fazla bilgi için bkz <xref:grpc/basics> ..</span><span class="sxs-lookup"><span data-stu-id="7f63c-155">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="7f63c-156">GRPC hizmetlerini barındıran bir ASP.NET Core Web uygulaması yalnızca oluşturulan hizmet taban sınıfına ihtiyaç duyuyor:</span><span class="sxs-lookup"><span data-stu-id="7f63c-156">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="7f63c-157">GRPC çağrısı yapan bir gRPC istemci uygulaması yalnızca somut istemcinin oluşturulması gerekir:</span><span class="sxs-lookup"><span data-stu-id="7f63c-157">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="7f63c-158">WPF projeleri. proto dosyalarından gRPC C# varlıkları oluşturulamıyor</span><span class="sxs-lookup"><span data-stu-id="7f63c-158">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="7f63c-159">WPF projelerinde, gRPC kod oluşturmanın düzgün çalışmasını engelleyen [bilinen bir sorun](https://github.com/dotnet/wpf/issues/810) vardır.</span><span class="sxs-lookup"><span data-stu-id="7f63c-159">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="7f63c-160">Bir WPF projesinde `Grpc.Tools` , ve *. proto* dosyalarına başvurarak oluşturulan tüm GRPC türleri, kullanıldığında derleme hataları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7f63c-160">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="7f63c-161">hata CS0246: ' MyGrpcServices ' türü veya ad alanı adı bulunamadı (bir using yönergeniz veya derleme başvurunuz mu eksik?)</span><span class="sxs-lookup"><span data-stu-id="7f63c-161">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="7f63c-162">Bu soruna geçici çözüm olarak şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="7f63c-162">You can workaround this issue by:</span></span>

1. <span data-ttu-id="7f63c-163">Yeni bir .NET Core sınıf kitaplığı projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7f63c-163">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="7f63c-164">Yeni projede, [ *\* . proto* dosyalarından C# kod üretimini](xref:grpc/basics#generated-c-assets)etkinleştirmek için başvurular ekleyin:</span><span class="sxs-lookup"><span data-stu-id="7f63c-164">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="7f63c-165">[GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7f63c-165">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="7f63c-166">*\* . Proto* dosyalarını `<Protobuf>` öğe grubuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7f63c-166">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="7f63c-167">WPF uygulamasında yeni projeye bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7f63c-167">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="7f63c-168">WPF uygulaması, yeni sınıf kitaplığı projesinden gRPC tarafından oluşturulan türleri kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="7f63c-168">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
