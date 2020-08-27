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
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="6f33c-103">GRPC ile işlemler arası iletişim</span><span class="sxs-lookup"><span data-stu-id="6f33c-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="6f33c-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="6f33c-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="6f33c-105">istemci ve hizmet arasındaki gRPC çağrıları genellikle TCP Yuvaları üzerinden gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6f33c-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="6f33c-106">TCP bir ağ üzerinden iletişim kurmak için idealdir, ancak istemci ve hizmet aynı makinede olduğunda, [işlemler arası iletişim (IPC)](https://wikipedia.org/wiki/Inter-process_communication) daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="6f33c-106">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span> <span data-ttu-id="6f33c-107">Bu belgede, bkz..</span><span class="sxs-lookup"><span data-stu-id="6f33c-107">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="6f33c-108">Sunucu yapılandırması</span><span class="sxs-lookup"><span data-stu-id="6f33c-108">Server configuration</span></span>

<span data-ttu-id="6f33c-109">Özel aktarımlar Kestrel tarafından desteklenir.</span><span class="sxs-lookup"><span data-stu-id="6f33c-109">Custom transports are supported by Kestrel.</span></span> <span data-ttu-id="6f33c-110">Kestrel, *program.cs*içinde yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="6f33c-110">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="6f33c-111">Önceki örnek:</span><span class="sxs-lookup"><span data-stu-id="6f33c-111">The preceding example:</span></span>

* <span data-ttu-id="6f33c-112">İçinde Kestrel 'in uç noktalarını yapılandırır `ConfigureKestrel` .</span><span class="sxs-lookup"><span data-stu-id="6f33c-112">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="6f33c-113"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Belirtilen yola sahip bir [UNIX etki alanı yuvasını (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) dinlemek için çağrılar.</span><span class="sxs-lookup"><span data-stu-id="6f33c-113">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="6f33c-114">Kestrel, UDS uç noktaları için yerleşik desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="6f33c-114">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="6f33c-115">UıDS, Linux, macOS ve [Windows 'un modern sürümlerinde](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)desteklenir.</span><span class="sxs-lookup"><span data-stu-id="6f33c-115">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="6f33c-116">İstemci yapılandırması</span><span class="sxs-lookup"><span data-stu-id="6f33c-116">Client configuration</span></span>

<span data-ttu-id="6f33c-117">`GrpcChannel` Özel aktarımlar üzerinde gRPC çağrıları yapmayı destekler.</span><span class="sxs-lookup"><span data-stu-id="6f33c-117">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="6f33c-118">Bir kanal oluşturulduğunda, özel bir içeren ile yapılandırılabilir `SocketsHttpHandler` `ConnectionFactory` .</span><span class="sxs-lookup"><span data-stu-id="6f33c-118">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectionFactory`.</span></span> <span data-ttu-id="6f33c-119">Fabrika, istemcinin özel aktarımlar üzerinden bağlantı yapmasına ve ardından bu aktarım üzerinden HTTP istekleri göndermesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6f33c-119">The factory allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6f33c-120">`ConnectionFactory` , .NET 5 sürüm adayı 1 ' de yeni bir API 'dir.</span><span class="sxs-lookup"><span data-stu-id="6f33c-120">`ConnectionFactory` is a new API in .NET 5 release candidate 1.</span></span>

<span data-ttu-id="6f33c-121">UNIX etki alanı yuvaları bağlantı fabrikası örneği:</span><span class="sxs-lookup"><span data-stu-id="6f33c-121">Unix domain sockets connection factory example:</span></span>

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

<span data-ttu-id="6f33c-122">Özel bağlantı fabrikası kullanarak bir kanal oluşturun:</span><span class="sxs-lookup"><span data-stu-id="6f33c-122">Using the custom connection factory to create a channel:</span></span>

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

<span data-ttu-id="6f33c-123">Yukarıdaki kod kullanılarak oluşturulan kanallar, UNIX etki alanı Yuvaları üzerinden gRPC çağrıları gönderir.</span><span class="sxs-lookup"><span data-stu-id="6f33c-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span>
