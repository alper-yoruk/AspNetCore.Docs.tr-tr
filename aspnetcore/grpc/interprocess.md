---
title: GRPC ile işlemler arası iletişim
author: jamesnk
description: GRPC 'yi işlem içi iletişim için nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 08/24/2020
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
uid: grpc/interprocess
ms.openlocfilehash: cfe8c98bb94817035437b2feb127a07bf5cad24b
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945764"
---
# <a name="inter-process-communication-with-grpc"></a>GRPC ile işlemler arası iletişim

, [James bAyKiNg](https://twitter.com/jamesnk)

istemci ve hizmet arasındaki gRPC çağrıları genellikle TCP Yuvaları üzerinden gönderilir. TCP bir ağ üzerinden iletişim kurmak için idealdir, ancak istemci ve hizmet aynı makinede olduğunda, [işlemler arası iletişim (IPC)](https://wikipedia.org/wiki/Inter-process_communication) daha etkilidir. Bu belgede, bkz..

## <a name="server-configuration"></a>Sunucu yapılandırması

Özel aktarımlar Kestrel tarafından desteklenir. Kestrel, *program.cs*içinde yapılandırılır:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

Önceki örnek:

* İçinde Kestrel 'in uç noktalarını yapılandırır `ConfigureKestrel` .
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Belirtilen yola sahip bir [UNIX etki alanı yuvasını (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) dinlemek için çağrılar.

Kestrel, UDS uç noktaları için yerleşik desteğe sahiptir. UıDS, Linux, macOS ve [Windows 'un modern sürümlerinde](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)desteklenir.

## <a name="client-configuration"></a>İstemci yapılandırması

`GrpcChannel` Özel aktarımlar üzerinde gRPC çağrıları yapmayı destekler. Bir kanal oluşturulduğunda, özel bir içeren ile yapılandırılabilir `SocketsHttpHandler` `ConnectionFactory` . Fabrika, istemcinin özel aktarımlar üzerinden bağlantı yapmasına ve ardından bu aktarım üzerinden HTTP istekleri göndermesini sağlar.

> [!IMPORTANT]
> `ConnectionFactory` , .NET 5 sürüm adayı 1 ' de yeni bir API 'dir.

UNIX etki alanı yuvaları bağlantı fabrikası örneği:

```csharp
public class UnixDomainSocketConnectionFactory : SocketsConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
        : base(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified)
    {
        _endPoint = endPoint;
    }

    public override ValueTask<Connection> ConnectAsync(EndPoint? endPoint,
        IConnectionProperties? options = null, CancellationToken cancellationToken = default)
    {
        return base.ConnectAsync(_endPoint, options, cancellationToken);
    }
}
```

Özel bağlantı fabrikası kullanarak bir kanal oluşturun:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint)
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

Yukarıdaki kod kullanılarak oluşturulan kanallar, UNIX etki alanı Yuvaları üzerinden gRPC çağrıları gönderir.
