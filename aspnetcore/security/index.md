---
title: ASP.NET Çekirdek Güvenliğine Genel Bakış
author: rick-anderson
description: ASP.NET Core'da kimlik doğrulama, yetkilendirme ve güvenlik temelleri hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/index
ms.openlocfilehash: 0f8e96fb7d5246e746b95f8907745f849de60e24
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656039"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="029ae-103">ASP.NET Çekirdek Güvenliğine Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="029ae-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="029ae-104">ASP.NET Core, geliştiricilerin uygulamalarının güvenliğini kolayca yapılandırmalarına ve yönetmelerine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="029ae-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="029ae-105">ASP.NET Core, kimlik doğrulama, yetkilendirme, veri koruma, HTTPS zorlama, uygulama sırları, istek karşıtı sahtecilik koruması ve CORS yönetimini yönetmek için özellikler içerir.</span><span class="sxs-lookup"><span data-stu-id="029ae-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="029ae-106">Bu güvenlik özellikleri, güçlü ama güvenli ASP.NET Core uygulamaları oluşturmanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="029ae-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="029ae-107">ASP.NET Temel güvenlik özellikleri</span><span class="sxs-lookup"><span data-stu-id="029ae-107">ASP.NET Core security features</span></span>

<span data-ttu-id="029ae-108">ASP.NET Core, yerleşik Kimlik sağlayıcıları da dahil olmak üzere uygulamalarınızı güvence altına almak için birçok araç ve kitaplık sağlar, ancak Facebook, Twitter veya LinkedIn gibi üçüncü taraf kimlik hizmetlerini kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="029ae-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in Identity providers but you can use 3rd party identity services such as Facebook, Twitter, or LinkedIn.</span></span> <span data-ttu-id="029ae-109">core ASP.NET ile, gizli bilgileri kodda ifşa etmek zorunda kalmadan depolamanın ve kullanmanın bir yolu olan uygulama sırlarını kolayca yönetebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="029ae-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="029ae-110">Kimlik doğrulama ve Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="029ae-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="029ae-111">Kimlik doğrulama, kullanıcının daha sonra bir işletim sistemi, veritabanı, uygulama veya kaynakta depolananlarla karşılaştırılan kimlik bilgileri sağladığı bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="029ae-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="029ae-112">Eşleşirlerse, kullanıcılar kimlik doğrulaması başarıyla gerçekleştirir ve yetkilendirme işlemi sırasında yetkilendirilen eylemleri gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="029ae-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="029ae-113">Yetkilendirme, kullanıcının ne yapmasına izin verilebilen işlemi ifade eder.</span><span class="sxs-lookup"><span data-stu-id="029ae-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="029ae-114">Kimlik doğrulaması düşünmenin başka bir yolu da, bunu sunucu, veritabanı, uygulama veya kaynak gibi bir alana girmenin bir yolu olarak değerlendirmektir, yetkilendirme ise kullanıcının o alandaki nesnelere (sunucu, veritabanı veya uygulama) hangi eylemleri gerçekleştirebileceğidir.</span><span class="sxs-lookup"><span data-stu-id="029ae-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="029ae-115">Yazılımdaki Yaygın Güvenlik Açıkları</span><span class="sxs-lookup"><span data-stu-id="029ae-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="029ae-116">ASP.NET Core ve EF, uygulamalarınızı güvenli hale almanıza ve güvenlik ihlallerini önlemenize yardımcı olan özellikler içerir.</span><span class="sxs-lookup"><span data-stu-id="029ae-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="029ae-117">Aşağıdaki bağlantı listesi sizi web uygulamalarında en yaygın güvenlik açıklarından kaçınmak için ayrıntılı teknikler olarak belgelendirmeye götürür:</span><span class="sxs-lookup"><span data-stu-id="029ae-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="029ae-118">Site arası komut dosyası saldırıları</span><span class="sxs-lookup"><span data-stu-id="029ae-118">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="029ae-119">SQL ekleme saldırıları</span><span class="sxs-lookup"><span data-stu-id="029ae-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="029ae-120">Site Arası İstek Sahteciliği (CSRF)</span><span class="sxs-lookup"><span data-stu-id="029ae-120">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="029ae-121">Yeniden yönlendirme saldırılarını aç</span><span class="sxs-lookup"><span data-stu-id="029ae-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="029ae-122">Farkında olmanız gereken daha fazla güvenlik açığı vardır.</span><span class="sxs-lookup"><span data-stu-id="029ae-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="029ae-123">Daha fazla bilgi için, içindekiler tablosunun **Güvenlik ve Kimlik** bölümündeki diğer makalelere bakın.</span><span class="sxs-lookup"><span data-stu-id="029ae-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
