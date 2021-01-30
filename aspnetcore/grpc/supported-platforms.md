---
title: .NET tarafından desteklenen platformlar için gRPC
author: jamesnk
description: .NET üzerinde gRPC için desteklenen platformlar hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 92ca38875c6618c8630a66af16548d32bc469a62
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057717"
---
# <a name="grpc-for-net-supported-platforms"></a>.NET tarafından desteklenen platformlar için gRPC

, [James bAyKiNg](https://twitter.com/jamesnk)

Bu makalede, .NET ile gRPC kullanmaya yönelik gereksinimler ve desteklenen platformlar açıklanmaktadır.

gRPC, daha gelişmiş özelliklerden bazıları için HTTP/2 kullanmak üzere tasarlanmıştır. GRPC kullanımını engelleyebilen her yerde HTTP/2 desteklenmez. Bu nedenle, istemciler ve sunucular arasında gRPC çağrıları göndermek için HTTP/1.1 ile uyumlu ikinci bir hat biçimi vardır:

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -HTTP/2 üzerinden gRPC, gRPC 'nin genellikle kullanıldığı bir şekilde yapılır.
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web, gRPC protokolünü HTTP/1.1 ile uyumlu olacak şekilde değiştirir. gRPC-Web daha fazla yerde kullanılabilir, özellikle de tarayıcı uygulamaları tarafından çağrılabilir. İki gelişmiş gRPC özelliği artık desteklenmiyor: istemci akışı ve çift yönlü akış.

.NET için gRPC, her iki kablo biçimini destekler. GRPC-Web ' i ayarlama hakkında daha fazla bilgi için bkz <xref:grpc/browser> ..

## <a name="device-requirements"></a>Cihaz gereksinimleri

.NET için gRPC, .NET Core 'un desteklediği tüm cihazları destekler.

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;
> * Browsers (Tarayıcılar)&Dagger;

&dagger;MacOS 'ta barındırılan ASP.NET Core uygulamalar HTTPS 'yi desteklemez. macOS üzerinde gRPC istemcileri, uzak hizmetleri çağırırken HTTPS kullanmaya devam edebilir.

&Dagger;Blazor WebAssembly uygulamalar, GRPC hizmetlerini gRPC-Web ile çağırabilir.

## <a name="aspnet-core-server-requirements"></a>ASP.NET Core sunucusu gereksinimleri

gRPC Hizmetleri, tüm yerleşik ASP.NET Core sunucularında barındırılabilir.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&dagger;

&dagger;IIS ve HTTP.sys .NET 5 ve Windows 10 derleme 20241 veya üstünü gerektirir.

Daha fazla bilgi için bkz. <xref:grpc/aspnetcore>.

## <a name="net-version-requirements"></a>.NET sürüm gereksinimleri

.NET için gRPC, .NET Core 3 ve .NET 5 veya üstünü destekler.

> [!div class="checklist"]
>
> * .NET 5 veya üzeri
> * .NET Core 3

.NET için gRPC .NET Framework ve Xamarin üzerinde çalışmayı desteklemez. [GRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/) , .NET Framework ve Xamarin 'i destekleyen üçüncü taraf bir kitaplıktır. gRPC C-Core, Microsoft tarafından desteklenmez.

## <a name="azure-services"></a>Azure hizmetleri

> [!div class="checklist"]
>
> * [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Azure App Service, gRPC 'yi HTTP/2 üzerinden barındırmayı desteklemez. gRPC-Web, uyumlu bir alternatiftir.

Azure App Service 'de HTTP/2 ile gRPC desteğinin geliştirilmesi için iş devam ediyor. Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/9020)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [gRPC C# Core-Library](https://grpc.io/docs/languages/csharp/quickstart/)
