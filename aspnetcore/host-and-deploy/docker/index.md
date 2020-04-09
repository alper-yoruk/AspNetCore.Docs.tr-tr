---
title: Docker konteynerlerinde Ana bilgisayar ASP.NET Core
author: rick-anderson
description: ASP.NET Core uygulamalarını Docker konteynerlerinde barındırmayı öğrenmek için kaynaklara bağlantılar keşfedin.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
uid: host-and-deploy/docker/index
ms.openlocfilehash: cb5f774db5fab46a57f8ca4bbbca148f20f371ba
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664082"
---
# <a name="host-aspnet-core-in-docker-containers"></a>Docker konteynerlerinde Ana bilgisayar ASP.NET Core

Aşağıdaki makaleler Docker'da ASP.NET Core uygulamalarını barındırma hakkında bilgi edinmek için kullanılabilir:

[Kapsayıcılar ve Docker’a Giriş](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
Kapsayıcılaştırmanın, bir uygulamanın veya hizmetin, bağımlılıklarının ve yapılandırmanın kapsayıcı görüntüsü olarak birlikte paketlendiği yazılım geliştirmeye yönelik bir yaklaşım olduğunu görün. Görüntü sınanabilir ve daha sonra bir ana bilgisayara dağıtılabilir.

[Docker Nedir](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
Docker'ın, uygulamaların bulutta veya şirket içinde çalıştırılabilen taşınabilir, kendi kendine yetebilen kapsayıcılar olarak dağıtımını otomatikleştirmek için nasıl açık kaynaklı bir proje olduğunu keşfedin.

[Docker Terminolojisi](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
Docker teknolojisi için terimleri ve tanımları öğrenin.

[Docker kapsayıcıları, görüntüleri ve kayıt defterleri](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
Docker kapsayıcı görüntülerinin ortamlar arasında tutarlı dağıtım için görüntü kayıt defterinde nasıl depolandığını öğrenin.

<xref:host-and-deploy/docker/building-net-docker-images>ASP.NET Core uygulamasını nasıl oluşturup geliştireceklerini öğrenin. Microsoft tarafından tutulan Docker resimlerini keşfedin ve kullanım servis taleplerini inceleyin.

[Visual Studio kapsayıcı araçları](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
Visual Studio'nun ,WINDOWS için Docker'da .NET Framework veya .NET Core'u hedefleyen ASP.NET Core uygulamalarını oluşturmayı, hata ayıklamayı ve çalıştırmayı nasıl desteklediğini keşfedin. Hem Windows hem de Linux kapsayıcıları desteklenir.

[Azure Container Registry'de yayımlama](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
PowerShell'i kullanarak Azure'daki bir Docker ana bilgisayara ASP.NET Core uygulamasını dağıtmak için Visual Studio Container Tools uzantısını nasıl kullanacağız öğrenin.

[Proxy sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer)  
Proxy sunucuları ve yük dengeleyicileri arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. İstekleri bir proxy aracılığıyla aktarmak genellikle düzen ve istemci IP gibi özgün istek le ilgili bilgileri gizler. İstek le ilgili bazı bilgileri el ile uygulamaya iletmek gerekebilir.
