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
ms.openlocfilehash: 34876f31cbc51ba66a91ae32ea6a5213dc34a369
ms.sourcegitcommit: 9c031530d2e652fe422e786bd43392bc500d622f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2020
ms.locfileid: "90770161"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="98d7d-103">gRPC ile işlemler arası iletişim</span><span class="sxs-lookup"><span data-stu-id="98d7d-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="98d7d-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="98d7d-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="98d7d-105">istemci ve hizmet arasındaki gRPC çağrıları genellikle TCP Yuvaları üzerinden gönderilir.</span><span class="sxs-lookup"><span data-stu-id="98d7d-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="98d7d-106">TCP bir ağ üzerinden iletişim kurmak için tasarlandı.</span><span class="sxs-lookup"><span data-stu-id="98d7d-106">TCP was designed for communicating across a network.</span></span> <span data-ttu-id="98d7d-107">İstemci ve hizmet aynı makinede olduğunda, [Işlem arası iletişim (IPC)](https://wikipedia.org/wiki/Inter-process_communication) TCP 'den daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="98d7d-107">[Inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient than TCP when the client and service are on the same machine.</span></span> <span data-ttu-id="98d7d-108">Bu belgede, bkz..</span><span class="sxs-lookup"><span data-stu-id="98d7d-108">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="98d7d-109">Sunucu yapılandırması</span><span class="sxs-lookup"><span data-stu-id="98d7d-109">Server configuration</span></span>

<span data-ttu-id="98d7d-110">Özel aktarımlar [Kestrel](xref:fundamentals/servers/kestrel)tarafından desteklenir.</span><span class="sxs-lookup"><span data-stu-id="98d7d-110">Custom transports are supported by [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="98d7d-111">Kestrel, *program.cs*içinde yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="98d7d-111">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="98d7d-112">Önceki örnek:</span><span class="sxs-lookup"><span data-stu-id="98d7d-112">The preceding example:</span></span>

* <span data-ttu-id="98d7d-113">İçinde Kestrel 'in uç noktalarını yapılandırır `ConfigureKestrel` .</span><span class="sxs-lookup"><span data-stu-id="98d7d-113">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="98d7d-114"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Belirtilen yola sahip bir [UNIX etki alanı yuvasını (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) dinlemek için çağrılar.</span><span class="sxs-lookup"><span data-stu-id="98d7d-114">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="98d7d-115">Kestrel, UDS uç noktaları için yerleşik desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="98d7d-115">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="98d7d-116">UıDS, Linux, macOS ve [Windows 'un modern sürümlerinde](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)desteklenir.</span><span class="sxs-lookup"><span data-stu-id="98d7d-116">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="98d7d-117">İstemci yapılandırması</span><span class="sxs-lookup"><span data-stu-id="98d7d-117">Client configuration</span></span>

<span data-ttu-id="98d7d-118">`GrpcChannel` Özel aktarımlar üzerinde gRPC çağrıları yapmayı destekler.</span><span class="sxs-lookup"><span data-stu-id="98d7d-118">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="98d7d-119">Bir kanal oluşturulduğunda, özel bir içeren ile yapılandırılabilir `SocketsHttpHandler` `ConnectCallback` .</span><span class="sxs-lookup"><span data-stu-id="98d7d-119">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectCallback`.</span></span> <span data-ttu-id="98d7d-120">Geri arama, istemcinin özel aktarımlar üzerinden bağlantı yapmasına ve ardından bu aktarım üzerinden HTTP istekleri göndermesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="98d7d-120">The callback allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="98d7d-121">`SocketsHttpHandler.ConnectCallback` , .NET 5 sürüm adayı 2 ' de yeni bir API 'dir.</span><span class="sxs-lookup"><span data-stu-id="98d7d-121">`SocketsHttpHandler.ConnectCallback` is a new API in .NET 5 release candidate 2.</span></span>

<span data-ttu-id="98d7d-122">UNIX etki alanı yuvaları bağlantı fabrikası örneği:</span><span class="sxs-lookup"><span data-stu-id="98d7d-122">Unix domain sockets connection factory example:</span></span>

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

<span data-ttu-id="98d7d-123">Özel bağlantı fabrikası kullanarak bir kanal oluşturun:</span><span class="sxs-lookup"><span data-stu-id="98d7d-123">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectCallback = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="98d7d-124">Yukarıdaki kod kullanılarak oluşturulan kanallar, UNIX etki alanı Yuvaları üzerinden gRPC çağrıları gönderir.</span><span class="sxs-lookup"><span data-stu-id="98d7d-124">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span> <span data-ttu-id="98d7d-125">Diğer IPC teknolojileri için destek, Kestrel ve içindeki genişletilebilirlik kullanılarak uygulanabilir `SocketsHttpHandler` .</span><span class="sxs-lookup"><span data-stu-id="98d7d-125">Support for other IPC technologies can be implemented using the extensibility in Kestrel and `SocketsHttpHandler`.</span></span>
