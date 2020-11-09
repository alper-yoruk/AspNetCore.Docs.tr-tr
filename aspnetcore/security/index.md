---
title: ASP.NET Core güvenliğine genel bakış
author: rick-anderson
description: ASP.NET Core 'da kimlik doğrulama, yetkilendirme ve güvenlik temelleri hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/index
ms.openlocfilehash: 3c86c66bebe8a5ce1c195ebf931193e7e2a73fef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051635"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="ac30a-103">ASP.NET Core güvenliğine genel bakış</span><span class="sxs-lookup"><span data-stu-id="ac30a-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="ac30a-104">ASP.NET Core, geliştiricilerin uygulamaları için güvenliği kolayca yapılandırıp yönetmesine olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="ac30a-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="ac30a-105">ASP.NET Core, kimlik doğrulaması, yetkilendirme, veri koruma, HTTPS zorlaması, uygulama parolaları, XSRF/CSRF önleme ve CORS yönetimi yönetimi için özellikler içerir.</span><span class="sxs-lookup"><span data-stu-id="ac30a-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, XSRF/CSRF prevention, and CORS management.</span></span> <span data-ttu-id="ac30a-106">Bu güvenlik özellikleri, güçlü ancak güvenli ASP.NET Core uygulamalar oluşturmanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="ac30a-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="ac30a-107">ASP.NET Core güvenlik özellikleri</span><span class="sxs-lookup"><span data-stu-id="ac30a-107">ASP.NET Core security features</span></span>

<span data-ttu-id="ac30a-108">ASP.NET Core, uygulamalarınızı güvenli hale getirmek için yerleşik kimlik sağlayıcıları da dahil olmak üzere birçok araç ve kitaplık sağlar, ancak Facebook, Twitter ve LinkedIn gibi üçüncü taraf kimlik hizmetlerini de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ac30a-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in identity providers, but you can use third-party identity services such as Facebook, Twitter, and LinkedIn.</span></span> <span data-ttu-id="ac30a-109">ASP.NET Core ile, uygulama gizli dizilerini kolayca yönetebilirsiniz. Bu, özel bilgileri kodda kullanıma sunmak zorunda kalmadan depolamanın ve kullanmanın bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="ac30a-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="ac30a-110">Kimlik doğrulama ve yetkilendirme karşılaştırması</span><span class="sxs-lookup"><span data-stu-id="ac30a-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="ac30a-111">Kimlik doğrulaması, bir kullanıcının bir işletim sistemi, veritabanı, uygulama veya kaynakta depolananlara kıyasla kimlik bilgileri sağladığı bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="ac30a-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="ac30a-112">Eşleşiyorsa, kullanıcılar başarıyla kimlik doğrular ve yetkilendirme işlemi sırasında, için yetkilendirildiği eylemleri gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="ac30a-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="ac30a-113">Yetkilendirme, bir kullanıcının ne yapmasına izin verileceğini belirleyen işlemi ifade eder.</span><span class="sxs-lookup"><span data-stu-id="ac30a-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="ac30a-114">Kimlik doğrulamayı düşünmenin bir diğer yolu da, bir sunucu, veritabanı, uygulama veya kaynak gibi bir boşluk girmek için bir yöntem olarak göz önünde bulundurmaktır. yetkilendirme sırasında kullanıcının bu alanın içindeki hangi nesneleri gerçekleştirebileceği (sunucu, veritabanı veya uygulama) eylemleri.</span><span class="sxs-lookup"><span data-stu-id="ac30a-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="ac30a-115">Yazılımda yaygın olarak karşılaşılan güvenlik açıkları</span><span class="sxs-lookup"><span data-stu-id="ac30a-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="ac30a-116">ASP.NET Core ve EF, uygulamalarınızın güvenliğini sağlamanıza ve güvenlik ihlallerinin engellenmesine yardımcı olan özellikler içerir.</span><span class="sxs-lookup"><span data-stu-id="ac30a-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="ac30a-117">Aşağıdaki bağlantı listesi, Web Apps 'teki en yaygın güvenlik açıklarını önlemenize yönelik teknik bilgi için sizi belgeler halinde yönlendirir:</span><span class="sxs-lookup"><span data-stu-id="ac30a-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="ac30a-118">Siteler arası komut dosyası (XSS) saldırıları</span><span class="sxs-lookup"><span data-stu-id="ac30a-118">Cross-Site Scripting (XSS) attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="ac30a-119">SQL ekleme saldırıları</span><span class="sxs-lookup"><span data-stu-id="ac30a-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="ac30a-120">Çapraz site Isteği forgery (XSRF/CSRF) saldırıları</span><span class="sxs-lookup"><span data-stu-id="ac30a-120">Cross-Site Request Forgery (XSRF/CSRF) attacks</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="ac30a-121">Yeniden yönlendirme saldırılarını aç</span><span class="sxs-lookup"><span data-stu-id="ac30a-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="ac30a-122">Bilmeniz gereken daha fazla güvenlik açığı vardır.</span><span class="sxs-lookup"><span data-stu-id="ac30a-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="ac30a-123">Daha fazla bilgi için içindekiler tablosunun **güvenlik ve Identity** bölümündeki diğer makalelere bakın.</span><span class="sxs-lookup"><span data-stu-id="ac30a-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
