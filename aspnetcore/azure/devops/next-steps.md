---
title: ASP.NET Core ve Azure ile sonraki adımlar-DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps için ek öğrenme yolları.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/next-steps
ms.openlocfilehash: 92401d45d36dd3b93d175e08a8fa8697217ca7c7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766530"
---
# <a name="next-steps"></a><span data-ttu-id="51646-103">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="51646-103">Next steps</span></span>

<span data-ttu-id="51646-104">Bu kılavuzda, ASP.NET Core örnek bir uygulama için bir DevOps işlem hattı oluşturdunuz.</span><span class="sxs-lookup"><span data-stu-id="51646-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="51646-105">Tebrikler!</span><span class="sxs-lookup"><span data-stu-id="51646-105">Congratulations!</span></span> <span data-ttu-id="51646-106">ASP.NET Core Web uygulamalarını yayımlamaya Azure App Service ve değişikliklerin sürekli tümleştirmesini otomatikleştirmenizi umuyoruz.</span><span class="sxs-lookup"><span data-stu-id="51646-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="51646-107">Web barındırma ve DevOps 'un ötesinde Azure, ASP.NET Core geliştiricileri için kullanışlı bir hizmet olarak platform (PaaS) Hizmetleri dizisine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="51646-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="51646-108">Bu bölüm, en yaygın olarak kullanılan hizmetlerden bazılarına kısa bir genel bakış sunar.</span><span class="sxs-lookup"><span data-stu-id="51646-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="51646-109">Depolama ve veritabanları</span><span class="sxs-lookup"><span data-stu-id="51646-109">Storage and databases</span></span>

<span data-ttu-id="51646-110">[Redis Cache](/azure/redis-cache/) , yüksek aktarım hızı, düşük gecikmeli veri önbelleğe alma hizmeti olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51646-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="51646-111">Sayfa çıkışını önbelleğe almak, veritabanı isteklerini azaltmak ve bir uygulamanın birden çok örneği arasında ASP.NET Core oturum durumu sağlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51646-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="51646-112">Azure [depolama](/azure/storage/) , Azure 'un büyük oranda ölçeklenebilir bulut depolama alanı.</span><span class="sxs-lookup"><span data-stu-id="51646-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="51646-113">Geliştiriciler, güvenilir Message Queuing için [sıra depolamadan](/azure/storage/queues/storage-queues-introduction) yararlanabilir ve [Tablo Depolaması](/azure/storage/tables/table-storage-overview) , çok büyük ve yarı yapılandırılmış veri kümeleri kullanılarak hızlı geliştirme Için tasarlanan bir NoSQL anahtar-değer deposudur.</span><span class="sxs-lookup"><span data-stu-id="51646-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="51646-114">[Azure SQL veritabanı](/azure/sql-database/) , Microsoft SQL Server altyapısını kullanarak bir hizmet olarak tanıdık ilişkisel veritabanı işlevselliği sağlar.</span><span class="sxs-lookup"><span data-stu-id="51646-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="51646-115">[Cosmos DB](/azure/cosmos-db/) genel olarak dağıtılmış, çok modelli NoSQL veritabanı hizmeti.</span><span class="sxs-lookup"><span data-stu-id="51646-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="51646-116">SQL API (eski adıyla DocumentDB), Cassandra ve MongoDB gibi birden çok API mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="51646-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## Identity

<span data-ttu-id="51646-117">[Azure Active Directory](/azure/active-directory/) ve [Azure Active Directory B2C](/azure/active-directory-b2c/) her ikisi de kimlik hizmetlerdir.</span><span class="sxs-lookup"><span data-stu-id="51646-117">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="51646-118">Azure Active Directory, kurumsal senaryolar için tasarlanmıştır ve Azure AD B2B (işletmeler arası) işbirliğinin yanı sıra sosyal ağ oturum açma da dahil olmak üzere işletmeler arası senaryolar (Azure Active Directory B2C) sağlar.</span><span class="sxs-lookup"><span data-stu-id="51646-118">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="51646-119">Mobil</span><span class="sxs-lookup"><span data-stu-id="51646-119">Mobile</span></span>

<span data-ttu-id="51646-120">[Notification Hubs](/azure/notification-hubs/) , çeşitli türlerde cihazlarda çalışan uygulamalara Milyonlarca iletiyi hızlı bir şekilde göndermek için çok platformlu, ölçeklenebilir bir anında iletme bildirimi altyapısıdır.</span><span class="sxs-lookup"><span data-stu-id="51646-120">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="51646-121">Web altyapısı</span><span class="sxs-lookup"><span data-stu-id="51646-121">Web infrastructure</span></span>

<span data-ttu-id="51646-122">[Azure Container Service](/azure/aks/) barındırılan Kubernetes ortamınızı yöneterek kapsayıcı düzenleme uzmanlığına gerek kalmadan Kapsayıcılı uygulamaları dağıtmayı ve yönetmeyi hızlı ve kolay hale getirir.</span><span class="sxs-lookup"><span data-stu-id="51646-122">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="51646-123">[Azure Search](/azure/search/) , özel, heterojen içeriği üzerinden kurumsal arama çözümü oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="51646-123">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="51646-124">[Service Fabric](/azure/service-fabric/) , ölçeklenebilir ve güvenilir mikro hizmetleri ve kapsayıcıları paketlemeyi, dağıtmayı ve yönetmeyi kolaylaştıran bir dağıtılmış sistemler platformudur.</span><span class="sxs-lookup"><span data-stu-id="51646-124">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
