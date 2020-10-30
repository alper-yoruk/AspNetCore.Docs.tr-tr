---
title: ASP.NET Core ve Azure ile sonraki adımlar-DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps için ek öğrenme yolları.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/next-steps
ms.openlocfilehash: 35b0486611cc15f25b1c8b54584c264e4c1298c9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056601"
---
# <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, ASP.NET Core örnek bir uygulama için bir DevOps işlem hattı oluşturdunuz. Tebrikler! ASP.NET Core Web uygulamalarını yayımlamaya Azure App Service ve değişikliklerin sürekli tümleştirmesini otomatikleştirmenizi umuyoruz.

Web barındırma ve DevOps 'un ötesinde Azure, ASP.NET Core geliştiricileri için kullanışlı bir hizmet olarak platform (PaaS) Hizmetleri dizisine sahiptir. Bu bölüm, en yaygın olarak kullanılan hizmetlerden bazılarına kısa bir genel bakış sunar.

## <a name="storage-and-databases"></a>Depolama ve veritabanları

[Redis Cache](/azure/redis-cache/) , yüksek aktarım hızı, düşük gecikmeli veri önbelleğe alma hizmeti olarak kullanılabilir. Sayfa çıkışını önbelleğe almak, veritabanı isteklerini azaltmak ve bir uygulamanın birden çok örneği arasında ASP.NET Core oturum durumu sağlamak için kullanılabilir.

Azure [depolama](/azure/storage/) , Azure 'un büyük oranda ölçeklenebilir bulut depolama alanı. Geliştiriciler, güvenilir Message Queuing için [sıra depolamadan](/azure/storage/queues/storage-queues-introduction) yararlanabilir ve [Tablo Depolaması](/azure/storage/tables/table-storage-overview) , çok büyük ve yarı yapılandırılmış veri kümeleri kullanılarak hızlı geliştirme Için tasarlanan bir NoSQL anahtar-değer deposudur.

[Azure SQL veritabanı](/azure/sql-database/) , Microsoft SQL Server altyapısını kullanarak bir hizmet olarak tanıdık ilişkisel veritabanı işlevselliği sağlar.

[Cosmos DB](/azure/cosmos-db/) genel olarak dağıtılmış, çok modelli NoSQL veritabanı hizmeti. SQL API (eski adıyla DocumentDB), Cassandra ve MongoDB gibi birden çok API mevcuttur.

## Identity

[Azure Active Directory](/azure/active-directory/) ve [Azure Active Directory B2C](/azure/active-directory-b2c/) her ikisi de kimlik hizmetlerdir. Azure Active Directory, kurumsal senaryolar için tasarlanmıştır ve Azure AD B2B (işletmeler arası) işbirliğinin yanı sıra sosyal ağ oturum açma da dahil olmak üzere işletmeler arası senaryolar (Azure Active Directory B2C) sağlar.

## <a name="mobile"></a>Cep telefonu

[Notification Hubs](/azure/notification-hubs/) , çeşitli türlerde cihazlarda çalışan uygulamalara Milyonlarca iletiyi hızlı bir şekilde göndermek için çok platformlu, ölçeklenebilir bir anında iletme bildirimi altyapısıdır.

## <a name="web-infrastructure"></a>Web altyapısı

[Azure Container Service](/azure/aks/) barındırılan Kubernetes ortamınızı yöneterek kapsayıcı düzenleme uzmanlığına gerek kalmadan Kapsayıcılı uygulamaları dağıtmayı ve yönetmeyi hızlı ve kolay hale getirir.

[Azure Search](/azure/search/) , özel, heterojen içeriği üzerinden kurumsal arama çözümü oluşturmak için kullanılır.

[Service Fabric](/azure/service-fabric/) , ölçeklenebilir ve güvenilir mikro hizmetleri ve kapsayıcıları paketlemeyi, dağıtmayı ve yönetmeyi kolaylaştıran bir dağıtılmış sistemler platformudur.
