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
# <a name="troubleshoot-grpc-on-net-core"></a>.NET Core'da gRPC'de sorun giderme

Yazar: [James Newton-King](https://twitter.com/jamesnk)

Bu belgede,.NET'te gRPC uygulamaları geliştirirken sık karşılaşılan sorunlar anlatılmaktadır.

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>İstemci ve hizmet SSL/TLS yapılandırması arasındaki uyuşmazlık

gRPC şablonu ve örnekleri varsayılan olarak gRPC hizmetlerini güvence altına almak için [Aktarım Katmanı Güvenliği'ni (TLS)](https://tools.ietf.org/html/rfc5246) kullanır. gRPC istemcilerinin güvenli gRPC hizmetlerini başarıyla aramak için güvenli bir bağlantı kullanmaları gerekir.

Core gRPC hizmetinin ASP.NET TLS kullandığını uygulama başlangıcında yazılan günlüklerde doğrulayabilirsiniz. Hizmet bir HTTPS bitiş noktasından dinliyor olacak:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

.NET Core istemcisi, güvenli bir bağlantı yla arama yapmak için sunucu adresinde kullanmalıdır: `https`

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Tüm gRPC istemci uygulamaları TLS'yi destekler. diğer dillerdeki gRPC istemcileri genellikle kanalı `SslCredentials`niskontoile yapılandırılmasını gerektirir. `SslCredentials`istemcinin kullanacağı sertifikayı belirtir ve güvenli olmayan kimlik bilgileri yerine kullanılması gerekir. TLS kullanmak için farklı gRPC istemci uygulamalarını yapılandırma örnekleri için [bkz.](https://www.grpc.io/docs/guides/auth/)

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Güvenilmeyen/geçersiz sertifikaya sahip bir gRPC hizmetini arama

.NET gRPC istemcisi, hizmetin güvenilir bir sertifikaya sahip olmasını gerektirir. Güvenilir bir sertifika olmadan bir gRPC hizmetini ararken aşağıdaki hata iletisi döndürülür:

> Işlenmemiş özel durum. System.Net.Http.HttpRequestException: SSL bağlantısı kurulamadı, iç özel durumlara bakın.
> ---> System.Security.Authentication.AuthenticationException: Uzak sertifika doğrulama prosedürüne göre geçersizdir.

Uygulamanızı yerel olarak test ediyorsanız ve ASP.NET Core HTTPS geliştirme sertifikasına güvenilmiyorsa bu hatayı görebilirsiniz. Bu sorunu gidermek için yönergeleri gidermek için Windows [ve macOS'taki ASP.NET Çekirdek HTTPS geliştirme sertifikasına güven'e](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)bakın.

Başka bir makinede gRPC hizmetini arıyorsanız ve sertifikaya güvenemiyorsanız, gRPC istemcisi geçersiz sertifikayı yoksayacak şekilde yapılandırılabilir. Aşağıdaki kod, güvenilir bir sertifika olmadan yapılan çağrılara izin vermek için [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) kullanır:

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
> Güvenilmeyen sertifikalar yalnızca uygulama geliştirme sırasında kullanılmalıdır. Üretim uygulamaları her zaman geçerli sertifikalar kullanmalıdır.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>.NET Core istemcisi ile güvensiz gRPC hizmetlerini arayın

.NET Core istemcisi ile güvenli olmayan gRPC hizmetlerini aramak için ek yapılandırma gereklidir. gRPC istemcisi `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` anahtarı `true` sunucu `http` adresinde ayarlamalı ve kullanmalıdır:

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>macOS'ta Core gRPC uygulaması ASP.NET başlatılamıyor

Kerkenez, macOS ve Windows 7 gibi eski Windows sürümlerinde TLS ile HTTP/2'yi desteklemez. ASP.NET Core gRPC şablonu ve örnekleri varsayılan olarak TLS kullanır. gRPC sunucusunu başlatmaya çalıştığınızda aşağıdaki hata iletisini görürsünüz:

> IPv4 loopback arabirimine https://localhost:5001 bağlanamıyor: 'TLS üzerinden HTTP/2, EKSIK ALPN desteği nedeniyle macOS'ta desteklenmiyor.'.

Bu sorunu çözmek için Kestrel ve gRPC istemcisini *TLS'siz* HTTP/2 kullanacak şekilde yapılandırın. Bunu yalnızca geliştirme sırasında yapmalısınız. TLS'nin kullanılmaması, gRPC iletilerinin şifreleme olmadan gönderilmesine neden olur.

Kerkenez *Program.cs*TLS olmadan bir HTTP/2 bitiş noktası yapılandırmak gerekir:

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

BIR HTTP/2 bitiş noktası TLS olmadan yapılandırıldığında, bitiş noktasının [ListenOptions.Protocols'u](xref:fundamentals/servers/kestrel#listenoptionsprotocols) `HttpProtocols.Http2`. `HttpProtocols.Http1AndHttp2`TLS HTTP/2'yi müzakere etmek için gerekli olduğundan kullanılamaz. TLS olmadan, bitiş noktası varsayılan tüm bağlantıları HTTP/1.1 ve gRPC aramaları başarısız olur.

gRPC istemcisi de TLS kullanmamak için yapılandırılmalıdır. Daha fazla bilgi için [bkz.](#call-insecure-grpc-services-with-net-core-client)

> [!WARNING]
> TLS'siz HTTP/2 sadece uygulama geliştirme sırasında kullanılmalıdır. Üretim uygulamaları her zaman taşıma güvenliğini kullanmalıdır. Daha fazla bilgi için, [ASP.NET Core için gRPC'deki Güvenlik hususlarına](xref:grpc/security#transport-security)bakın.

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>gRPC C# varlıkları .proto dosyalarından oluşturulan kod değildir

somut istemciler ve hizmet temel sınıflarının gRPC kod oluşturma protobuf dosyaları ve takım bir projeden başvurulması gerekir. Şunları eklemeniz gerekir:

* *.madde* grubunda kullanmak `<Protobuf>` istediğiniz proto dosyaları. [İçe aktarılan *.proto* dosyaları](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) proje tarafından başvurulmalıdır.
* gRPC takım paketi [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)paket referans .

gRPC C# varlıkları oluşturma hakkında daha <xref:grpc/basics>fazla bilgi için bkz.

Varsayılan olarak, `<Protobuf>` bir başvuru somut bir istemci ve bir hizmet taban sınıfı oluşturur. Başvuru öğesinin `GrpcServices` özniteliği C# varlık oluşturmayı sınırlamak için kullanılabilir. Geçerli `GrpcServices` seçenekler şunlardır:

* `Both`(varsayılan zaman mevcut değil)
* `Server`
* `Client`
* `None`

gRPC hizmetlerini barındıran ASP.NET Bir Core web uygulamasının yalnızca oluşturulan hizmet tabanı sınıfına ihtiyacı vardır:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

gRPC aramaları yapan bir gRPC istemci uygulaması yalnızca oluşturulan somut istemciye ihtiyaç duyar:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a>.proto dosyalarından gRPC C# varlıkları oluşturamayan WPF projeleri

WPF projelerinde gRPC kod oluşturmanın doğru çalışmasını engelleyen bilinen bir [sorun](https://github.com/dotnet/wpf/issues/810) vardır. Bir WPF projesinde başvuru `Grpc.Tools` ve *.proto* dosyalarına göre oluşturulan tüm gRPC türleri kullanıldığında derleme hataları oluşturur:

> hata CS0246: Türü veya namespace adı 'MyGrpcServices' bulunamadı (bir kullanma yönergesi veya bir derleme başvuru eksik?)

Bu sorunu şu şekilde çözebilirsiniz:

1. Yeni bir .NET Core sınıf kitaplık projesi oluşturun.
2. Yeni projede, [ * \*.proto* dosyalarından C# kodu oluşumunu](xref:grpc/basics#generated-c-assets)etkinleştirmek için başvurular ekleyin:
    * [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) paketine bir paket başvurusu ekleyin.
    * Öğe * \*grubuna .proto* dosyaları ekleyin. `<Protobuf>`
3. WPF uygulamasında, yeni projeye bir başvuru ekleyin.

WPF uygulaması, yeni sınıf kitaplığı projesinden oluşturulan gRPC türlerini kullanabilir.

[!INCLUDE[](~/includes/gRPCazure.md)]
