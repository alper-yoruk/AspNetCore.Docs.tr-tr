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
# <a name="next-steps"></a><span data-ttu-id="02b05-103">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="02b05-103">Next steps</span></span>

<span data-ttu-id="02b05-104">Bu kılavuzda, ASP.NET Core örnek uygulaması için bir DevOps ardışık hattı oluşturdunuz.</span><span class="sxs-lookup"><span data-stu-id="02b05-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="02b05-105">Tebrikler!</span><span class="sxs-lookup"><span data-stu-id="02b05-105">Congratulations!</span></span> <span data-ttu-id="02b05-106">Core web uygulamalarını Azure App Service'de ASP.NET yayınlamayı ve değişikliklerin sürekli entegrasyonunu otomatikleştirmeyi öğrenmekten keyif aldığınızı umuyoruz.</span><span class="sxs-lookup"><span data-stu-id="02b05-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="02b05-107">Azure, web barındırma ve DevOps'lerin ötesinde, ASP.NET Core geliştiricileri için yararlı çok çeşitli Hizmet Olarak Platform (PaaS) hizmetlerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="02b05-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="02b05-108">Bu bölümde, en sık kullanılan hizmetlerden bazılarına kısa bir genel bakış yer almaktadır.</span><span class="sxs-lookup"><span data-stu-id="02b05-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="02b05-109">Depolama ve veritabanları</span><span class="sxs-lookup"><span data-stu-id="02b05-109">Storage and databases</span></span>

<span data-ttu-id="02b05-110">[Redis Önbellek,](/azure/redis-cache/) hizmet olarak kullanılabilen yüksek iş çıkışlı, düşük gecikmeli veri önbelleğe almadır.</span><span class="sxs-lookup"><span data-stu-id="02b05-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="02b05-111">Sayfa çıktısını önbelleğe alma, veritabanı isteklerini azaltma ve bir uygulamanın birden çok örneğinde ASP.NET Çekirdek oturum durumu sağlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="02b05-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="02b05-112">[Azure Depolama,](/azure/storage/) Azure'un büyük ölçüde ölçeklenebilir bulut depolama alanıdır.</span><span class="sxs-lookup"><span data-stu-id="02b05-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="02b05-113">Geliştiriciler güvenilir ileti sıraya için [Sıra Depolama'dan](/azure/storage/queues/storage-queues-introduction) yararlanabilir ve [Tablo Depolama,](/azure/storage/tables/table-storage-overview) büyük, yarı yapılandırılmış veri kümelerini kullanarak hızlı geliştirme için tasarlanmış bir NoSQL anahtar değer deposudur.</span><span class="sxs-lookup"><span data-stu-id="02b05-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="02b05-114">[Azure SQL Veritabanı,](/azure/sql-database/) Microsoft SQL Server Engine'i kullanarak bir hizmet olarak tanıdık ilişkisel veritabanı işlevselliği sağlar.</span><span class="sxs-lookup"><span data-stu-id="02b05-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="02b05-115">[Cosmos DB](/azure/cosmos-db/) küresel olarak dağıtılan, çok modelli NoSQL veritabanı hizmeti.</span><span class="sxs-lookup"><span data-stu-id="02b05-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="02b05-116">SQL API (eski adıyla DocumentDB), Cassandra ve MongoDB dahil olmak üzere birden çok API mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="02b05-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## <a name="identity"></a><span data-ttu-id="02b05-117">Kimlik</span><span class="sxs-lookup"><span data-stu-id="02b05-117">Identity</span></span>

<span data-ttu-id="02b05-118">[Azure Active Directory](/azure/active-directory/) ve [Azure Active Directory B2C](/azure/active-directory-b2c/) her ikisi de kimlik hizmetleridir.</span><span class="sxs-lookup"><span data-stu-id="02b05-118">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="02b05-119">Azure Active Directory kurumsal senaryolar için tasarlanmıştır ve Azure AD B2B (işletmeden işletmeye) işbirliğine olanak sağlarken, Azure Active Directory B2C'nin sosyal ağ oturum açma dahil olmak üzere işletmeden müşteriye senaryoları hedeflenir.</span><span class="sxs-lookup"><span data-stu-id="02b05-119">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="02b05-120">Mobil</span><span class="sxs-lookup"><span data-stu-id="02b05-120">Mobile</span></span>

<span data-ttu-id="02b05-121">[Bildirim Hub'ları,](/azure/notification-hubs/) çeşitli aygıttürlerinde çalışan uygulamalara hızlı bir şekilde milyonlarca ileti göndermek için çok platformlu, ölçeklenebilir bir anında bildirim motorudur.</span><span class="sxs-lookup"><span data-stu-id="02b05-121">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="02b05-122">Web altyapısı</span><span class="sxs-lookup"><span data-stu-id="02b05-122">Web infrastructure</span></span>

<span data-ttu-id="02b05-123">[Azure Konteyner Hizmeti,](/azure/aks/) barındırılan Kubernetes ortamınızı yöneterek konteyner düzenleme uzmanlığı olmadan konteyner uygulamalarının hızlı ve kolay dağıtılmasını ve yönetilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="02b05-123">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="02b05-124">[Azure Arama,](/azure/search/) özel ve heterojen içerik üzerinden kurumsal arama çözümü oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="02b05-124">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="02b05-125">[Service Fabric,](/azure/service-fabric/) ölçeklenebilir ve güvenilir mikro hizmetler ve konteynerlerin paketlenin, dağıtılmasını ve yönetilmesini kolaylaştıran dağıtılmış bir sistem platformudur.</span><span class="sxs-lookup"><span data-stu-id="02b05-125">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
