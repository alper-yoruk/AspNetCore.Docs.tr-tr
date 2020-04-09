---
title: Sonraki adımlar - ASP.NET Core ve Azure ile DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps için ek öğrenme yolları.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: a775dc42551a43bcce72b5f9ca364ed00b1dc4e6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659476"
---
# <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, ASP.NET Core örnek uygulaması için bir DevOps ardışık hattı oluşturdunuz. Tebrikler! Core web uygulamalarını Azure App Service'de ASP.NET yayınlamayı ve değişikliklerin sürekli entegrasyonunu otomatikleştirmeyi öğrenmekten keyif aldığınızı umuyoruz.

Azure, web barındırma ve DevOps'lerin ötesinde, ASP.NET Core geliştiricileri için yararlı çok çeşitli Hizmet Olarak Platform (PaaS) hizmetlerine sahiptir. Bu bölümde, en sık kullanılan hizmetlerden bazılarına kısa bir genel bakış yer almaktadır.

## <a name="storage-and-databases"></a>Depolama ve veritabanları

[Redis Önbellek,](/azure/redis-cache/) hizmet olarak kullanılabilen yüksek iş çıkışlı, düşük gecikmeli veri önbelleğe almadır. Sayfa çıktısını önbelleğe alma, veritabanı isteklerini azaltma ve bir uygulamanın birden çok örneğinde ASP.NET Çekirdek oturum durumu sağlamak için kullanılabilir.

[Azure Depolama,](/azure/storage/) Azure'un büyük ölçüde ölçeklenebilir bulut depolama alanıdır. Geliştiriciler güvenilir ileti sıraya için [Sıra Depolama'dan](/azure/storage/queues/storage-queues-introduction) yararlanabilir ve [Tablo Depolama,](/azure/storage/tables/table-storage-overview) büyük, yarı yapılandırılmış veri kümelerini kullanarak hızlı geliştirme için tasarlanmış bir NoSQL anahtar değer deposudur.

[Azure SQL Veritabanı,](/azure/sql-database/) Microsoft SQL Server Engine'i kullanarak bir hizmet olarak tanıdık ilişkisel veritabanı işlevselliği sağlar.

[Cosmos DB](/azure/cosmos-db/) küresel olarak dağıtılan, çok modelli NoSQL veritabanı hizmeti. SQL API (eski adıyla DocumentDB), Cassandra ve MongoDB dahil olmak üzere birden çok API mevcuttur.

## <a name="identity"></a>Kimlik

[Azure Active Directory](/azure/active-directory/) ve [Azure Active Directory B2C](/azure/active-directory-b2c/) her ikisi de kimlik hizmetleridir. Azure Active Directory kurumsal senaryolar için tasarlanmıştır ve Azure AD B2B (işletmeden işletmeye) işbirliğine olanak sağlarken, Azure Active Directory B2C'nin sosyal ağ oturum açma dahil olmak üzere işletmeden müşteriye senaryoları hedeflenir.

## <a name="mobile"></a>Mobil

[Bildirim Hub'ları,](/azure/notification-hubs/) çeşitli aygıttürlerinde çalışan uygulamalara hızlı bir şekilde milyonlarca ileti göndermek için çok platformlu, ölçeklenebilir bir anında bildirim motorudur.

## <a name="web-infrastructure"></a>Web altyapısı

[Azure Konteyner Hizmeti,](/azure/aks/) barındırılan Kubernetes ortamınızı yöneterek konteyner düzenleme uzmanlığı olmadan konteyner uygulamalarının hızlı ve kolay dağıtılmasını ve yönetilmesini sağlar.

[Azure Arama,](/azure/search/) özel ve heterojen içerik üzerinden kurumsal arama çözümü oluşturmak için kullanılır.

[Service Fabric,](/azure/service-fabric/) ölçeklenebilir ve güvenilir mikro hizmetler ve konteynerlerin paketlenin, dağıtılmasını ve yönetilmesini kolaylaştıran dağıtılmış bir sistem platformudur.
