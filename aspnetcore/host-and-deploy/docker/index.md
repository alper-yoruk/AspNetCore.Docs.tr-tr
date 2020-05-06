---
title: Docker kapsayıcılarında konak ASP.NET Core
author: rick-anderson
description: Docker kapsayıcılarında ASP.NET Core uygulamaları nasıl barındırdığınızı öğrenmek için kaynakların bağlantılarını bulun.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/docker/index
ms.openlocfilehash: 94996f279929f6e3837efb613c99927edbd9143e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774593"
---
# <a name="host-aspnet-core-in-docker-containers"></a>Docker kapsayıcılarında konak ASP.NET Core

Docker 'da ASP.NET Core uygulamalar barındırma hakkında bilgi edinmek için aşağıdaki makaleler sunulmaktadır:

[Kapsayıcılar ve Docker’a Giriş](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
Kapsayıcının bir uygulama veya hizmetin, bağımlılıklarının ve yapılandırmasının bir kapsayıcı görüntüsü olarak birlikte paketlendiği yazılım geliştirme konusunda nasıl yaklaşım olduğunu öğrenin. Görüntü test edilebilir ve bir konağa dağıtılabilir.

[Docker nedir?](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
Docker 'ın, bulutta veya şirket içinde çalışabilen, taşınabilir, kendi kendine yeterli kapsayıcı olarak uygulama dağıtımını otomatik hale getirmeye yönelik açık kaynaklı bir proje olduğunu öğrenin.

[Docker terminolojisi](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
Docker teknolojisine yönelik hüküm ve tanımlar hakkında bilgi edinin.

[Docker kapsayıcıları, görüntüleri ve kayıt defterleri](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
Docker kapsayıcı görüntülerinin, ortamlar genelinde tutarlı dağıtım için bir görüntü kayıt defterinde nasıl depolandığını öğrenin.

<xref:host-and-deploy/docker/building-net-docker-images>ASP.NET Core bir uygulamayı nasıl oluşturacağınızı ve kullanabileceğinizi öğrenin. Microsoft tarafından tutulan Docker görüntülerini araştırın ve kullanım örneklerini inceleyin.

[Visual Studio kapsayıcı araçları](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
Visual Studio 'Nun Docker for Windows .NET Framework veya .NET Core 'u hedefleyen ASP.NET Core uygulamaları oluşturmayı, hata ayıklamayı ve çalıştırmayı nasıl desteklediğini öğrenin. Hem Windows hem de Linux kapsayıcıları desteklenir.

[Azure Container Registry'de yayımlama](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
Azure 'da PowerShell kullanarak bir ASP.NET Core uygulamasını Docker konağına dağıtmak için Visual Studio kapsayıcı araçları uzantısı 'nın nasıl kullanılacağını öğrenin.

[Proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer)  
Proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. İsteklerin bir ara sunucu üzerinden geçirilmesi, genellikle orijinal istekle ilgili olarak düzen ve istemci IP 'si gibi bilgileri gizler. İstekle ilgili bazı bilgilerin uygulamaya el ile iletilmesi gerekebilir.

[Docker ve küçük kapsayıcılar kullanılarak GC](xref:performance/memory#sc) Küçük kapsayıcılarla GC seçimini açıklar.