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
# <a name="troubleshoot-grpc-on-net-core"></a>.NET Core 'da gRPC sorunlarını giderme

, [James bAyKiNg](https://twitter.com/jamesnk)

Bu belgede, .NET üzerinde gRPC uygulamaları geliştirirken yaygın olarak karşılaşılan sorunlar ele alınmaktadır.

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>İstemci ve hizmet SSL/TLS yapılandırması arasında uyuşmazlık

GRPC şablonu ve örnekleri, varsayılan olarak gRPC hizmetlerini güvenli hale getirmek için [Aktarım Katmanı Güvenliği 'ni (TLS)](https://tools.ietf.org/html/rfc5246) kullanır. gRPC istemcilerinin güvenli gRPC hizmetlerini başarıyla çağırması için güvenli bir bağlantı kullanması gerekir.

ASP.NET Core gRPC hizmetinin uygulama başlatma bölümünde yazılan günlüklerde TLS kullandığını doğrulayabilirsiniz. Hizmet bir HTTPS uç noktasını dinleyerek:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

.NET Core istemcisinin, `https` güvenli bir bağlantıyla çağrı yapmak için sunucu adresinde kullanması gerekir:

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Tüm gRPC istemci uygulamaları TLS 'yi destekler. diğer dillerden gRPC istemcileri, genellikle kanalı ile yapılandırılmış olmasını gerektirir `SslCredentials` . `SslCredentials` İstemcinin kullanacağı sertifikayı belirtir ve güvenli olmayan kimlik bilgileri yerine kullanılması gerekir. Farklı gRPC istemci uygulamalarını TLS kullanmak üzere yapılandırma örnekleri için bkz. [GRPC kimlik doğrulaması](https://www.grpc.io/docs/guides/auth/).

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Güvenilir olmayan/geçersiz sertifikayla gRPC hizmetini çağırma

.NET gRPC istemcisi hizmetin güvenilen sertifikaya sahip olmasını gerektirir. Bir gRPC hizmeti güvenilir bir sertifika olmadan çağrılırken aşağıdaki hata iletisi döndürülür:

> İşlenmeyen özel durum. System .net. http. HttpRequestException: SSL bağlantısı kurulamadı, iç özel duruma bakın.
> ---> System. Security. Authentication. AuthenticationException: uzak sertifika, doğrulama yordamına göre geçersiz.

Uygulamanızı yerel olarak test ediyorsanız ve ASP.NET Core HTTPS geliştirme sertifikası güvenilir değilse bu hatayı görebilirsiniz. Bu sorunu gidermeye yönelik yönergeler için bkz. [Windows ve macOS 'ta ASP.NET Core https geliştirme sertifikasına güvenin](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

Bir gRPC hizmetini başka bir makineye arıyorsanız ve sertifikaya güvenmiyorsanız, gRPC istemcisi geçersiz sertifikayı yoksayacak şekilde yapılandırılabilir. Aşağıdaki kod, güvenilir bir sertifika olmadan çağrılara izin vermek için [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) kullanır:

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
> Güvenilmeyen sertifikalar yalnızca uygulama geliştirme sırasında kullanılmalıdır. Üretim uygulamaları her zaman geçerli sertifikaları kullanmalıdır.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>.NET Core istemcisiyle güvenli olmayan gRPC hizmetlerini çağırma

Bir uygulama .NET Core 3. x kullanıyorsa, .NET Core istemcisiyle güvenli olmayan gRPC hizmetlerini çağırmak için ek yapılandırma gerekir. GRPC istemcisinin, `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` sunucu adresinde anahtarı olarak ayarlanması `true` ve kullanması gerekir `http` :

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

.NET 5 uygulamalarına ek yapılandırma gerekmez, ancak güvenli olmayan gRPC hizmetlerini çağırmak için `Grpc.Net.Client` 2.32.0 veya üzeri sürümlerini kullanmaları gerekir.

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>MacOS 'ta ASP.NET Core gRPC uygulaması başlatılamıyor

Kestrel, macOS ve Windows 7 gibi eski Windows sürümlerindeki TLS ile HTTP/2 ' yi desteklemez. ASP.NET Core gRPC şablonu ve örnekleri varsayılan olarak TLS kullanır. GRPC sunucusunu başlatmayı denediğinizde aşağıdaki hata iletisini görürsünüz:

> https://localhost:5001IPv4 geri döngü arabirimine bağlanılamıyor: eksik ALPN desteği nedeniyle, macOS 'ta ' http/2 TLS üzerinden desteklenmez. '.

Bu sorunu geçici olarak çözmek için Kestrel ve gRPC istemcisini TLS *olmadan* http/2 kullanacak şekilde yapılandırın. Bunu yalnızca geliştirme sırasında yapmanız gerekir. TLS 'nin kullanılması, gRPC iletilerinin şifrelenmeden gönderilmesine neden olur.

Kestrel, *program.cs* içinde TLS olmadan bir http/2 uç noktası yapılandırmalıdır:

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

Bir HTTP/2 uç noktası TLS olmadan yapılandırıldığında, uç noktanın [Listenoptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) olarak ayarlanması gerekir `HttpProtocols.Http2` . `HttpProtocols.Http1AndHttp2` , HTTP/2 üzerinde anlaşmak için TLS gerektiğinden kullanılamıyor. TLS olmadan, uç nokta varsayılan HTTP/1.1 ve gRPC çağrıları için tüm bağlantılar başarısız olur.

GRPC istemcisinin, TLS kullanmak için de yapılandırılması gerekir. Daha fazla bilgi için bkz. [.NET Core istemcisiyle güvenli olmayan gRPC hizmetlerini çağırma](#call-insecure-grpc-services-with-net-core-client).

> [!WARNING]
> HTTP/2, TLS olmadan yalnızca uygulama geliştirme sırasında kullanılmalıdır. Üretim uygulamaları her zaman aktarım güvenliği kullanmalıdır. Daha fazla bilgi için bkz. [gRPC 'Deki güvenlik konuları ASP.NET Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>gRPC C# varlıkları. proto dosyalarından oluşturulan kod değildir

aynı somut istemci ve hizmet temel sınıflarının gRPC kod üretimi, bir projeden başvurulmak için prototip dosyaları ve araçları gerektirir. Şunları dahil etmeniz gerekir:

* öğe grubunda kullanmak istediğiniz *. proto* dosyaları `<Protobuf>` . [Içeri aktarılan *. proto* dosyalarına](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) proje tarafından başvurulmalıdır.
* GRPC araç paketi [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/)'a paket başvurusu.

GRPC C# varlıkları oluşturma hakkında daha fazla bilgi için bkz <xref:grpc/basics> ..

GRPC hizmetlerini barındıran bir ASP.NET Core Web uygulaması yalnızca oluşturulan hizmet taban sınıfına ihtiyaç duyuyor:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

GRPC çağrısı yapan bir gRPC istemci uygulaması yalnızca somut istemcinin oluşturulması gerekir:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a>WPF projeleri. proto dosyalarından gRPC C# varlıkları oluşturulamıyor

WPF projelerinde, gRPC kod oluşturmanın düzgün çalışmasını engelleyen [bilinen bir sorun](https://github.com/dotnet/wpf/issues/810) vardır. Bir WPF projesinde `Grpc.Tools` , ve *. proto* dosyalarına başvurarak oluşturulan tüm GRPC türleri, kullanıldığında derleme hataları oluşturur:

> hata CS0246: ' MyGrpcServices ' türü veya ad alanı adı bulunamadı (bir using yönergeniz veya derleme başvurunuz mu eksik?)

Bu soruna geçici çözüm olarak şunları yapabilirsiniz:

1. Yeni bir .NET Core sınıf kitaplığı projesi oluşturun.
2. Yeni projede, [ *\* . proto* dosyalarından C# kod üretimini](xref:grpc/basics#generated-c-assets)etkinleştirmek için başvurular ekleyin:
    * [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) paketine bir paket başvurusu ekleyin.
    * *\* . Proto* dosyalarını `<Protobuf>` öğe grubuna ekleyin.
3. WPF uygulamasında yeni projeye bir başvuru ekleyin.

WPF uygulaması, yeni sınıf kitaplığı projesinden gRPC tarafından oluşturulan türleri kullanabilir.

[!INCLUDE[](~/includes/gRPCazure.md)]
