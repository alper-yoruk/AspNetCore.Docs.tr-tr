---
title: gRPC ile işlemler arası iletişim
author: jamesnk
description: GRPC 'yi işlem içi iletişim için nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
ms.openlocfilehash: 7278ebd001aea4ba52c1134b3bac696c01950a27
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90723005"
---
# <a name="inter-process-communication-with-grpc"></a>gRPC ile işlemler arası iletişim

, [James bAyKiNg](https://twitter.com/jamesnk)

istemci ve hizmet arasındaki gRPC çağrıları genellikle TCP Yuvaları üzerinden gönderilir. TCP bir ağ üzerinden iletişim kurmak için tasarlandı. İstemci ve hizmet aynı makinede olduğunda, [Işlem arası iletişim (IPC)](https://wikipedia.org/wiki/Inter-process_communication) TCP 'den daha etkilidir. Bu belgede, bkz..

## <a name="server-configuration"></a>Sunucu yapılandırması

Özel aktarımlar [Kestrel](xref:fundamentals/servers/kestrel)tarafından desteklenir. Kestrel, *program.cs*içinde yapılandırılır:

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
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Belirtilen yola sahip bir [UNIX etki alanı yuvasını (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) dinlemek için çağrılar.

Kestrel, UDS uç noktaları için yerleşik desteğe sahiptir. UıDS, Linux, macOS ve [Windows 'un modern sürümlerinde](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)desteklenir.

## <a name="client-configuration"></a>İstemci yapılandırması

`GrpcChannel` Özel aktarımlar üzerinde gRPC çağrıları yapmayı destekler. Bir kanal oluşturulduğunda, özel bir içeren ile yapılandırılabilir `SocketsHttpHandler` `ConnectCallback` . Geri arama, istemcinin özel aktarımlar üzerinden bağlantı yapmasına ve ardından bu aktarım üzerinden HTTP istekleri göndermesini sağlar.

> [!IMPORTANT]
> `SocketsHttpHandler.ConnectCallback` , .NET 5 sürüm adayı 2 ' de yeni bir API 'dir.

UNIX etki alanı yuvaları bağlantı fabrikası örneği:

```csharp
public class UnixDomainSocketConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
    {
        _endPoint = endPoint;
    }

    public async ValueTask<Stream> ConnectAsync(SocketsHttpConnectionContext _,
        CancellationToken cancellationToken = default)
    {
        var socket = new Socket(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified);

        try
        {
            await socket.ConnectAsync(_endPoint, cancellationToken).ConfigureAwait(false);
            return new NetworkStream(socket, true);
        }
        catch
        {
            socket.Dispose();
            throw;
        }
    }
}
```

Özel bağlantı fabrikası kullanarak bir kanal oluşturun:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

Yukarıdaki kod kullanılarak oluşturulan kanallar, UNIX etki alanı Yuvaları üzerinden gRPC çağrıları gönderir. Diğer IPC teknolojileri için destek, Kestrel ve içindeki genişletilebilirlik kullanılarak uygulanabilir `SocketsHttpHandler` .
