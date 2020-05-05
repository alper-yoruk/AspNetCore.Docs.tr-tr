---
title: ASP.NET Core 1,1 ' deki yenilikler
author: rick-anderson
description: ASP.NET Core 1,1 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.date: 12/18/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-1.1
ms.openlocfilehash: c3317c8ff905d1c7a9c9d9019d18cbebad92edaf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775849"
---
# <a name="whats-new-in-aspnet-core-11"></a><span data-ttu-id="092a0-103">ASP.NET Core 1,1 ' deki yenilikler</span><span class="sxs-lookup"><span data-stu-id="092a0-103">What's new in ASP.NET Core 1.1</span></span>

<span data-ttu-id="092a0-104">ASP.NET Core 1,1 aşağıdaki yeni özellikleri içerir:</span><span class="sxs-lookup"><span data-stu-id="092a0-104">ASP.NET Core 1.1 includes the following new features:</span></span>

- [<span data-ttu-id="092a0-105">URL yeniden yazma ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="092a0-105">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
- [<span data-ttu-id="092a0-106">Yanıt önbelleğe alma ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="092a0-106">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
- [<span data-ttu-id="092a0-107">Bileşenleri etiket yardımcıları olarak görüntüle</span><span class="sxs-lookup"><span data-stu-id="092a0-107">View Components as Tag Helpers</span></span>](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper)
- [<span data-ttu-id="092a0-108">MVC filtreleri olarak ara yazılım</span><span class="sxs-lookup"><span data-stu-id="092a0-108">Middleware as MVC filters</span></span>](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline)
- [<span data-ttu-id="092a0-109">Tanımlama bilgisi tabanlı TempData sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="092a0-109">Cookie-based TempData provider</span></span>](xref:fundamentals/app-state#tempdata)
- [<span data-ttu-id="092a0-110">Azure App Service günlük sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="092a0-110">Azure App Service logging provider</span></span>](xref:fundamentals/logging/index#azure-app-service-provider)
- [<span data-ttu-id="092a0-111">Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="092a0-111">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
- [<span data-ttu-id="092a0-112">Azure ve Redsıs depolama veri koruma anahtarı depoları</span><span class="sxs-lookup"><span data-stu-id="092a0-112">Azure and Redis Storage Data Protection Key Repositories</span></span>](xref:security/data-protection/implementation/key-storage-providers)
- <span data-ttu-id="092a0-113">Windows için WebListener sunucusu</span><span class="sxs-lookup"><span data-stu-id="092a0-113">WebListener Server for Windows</span></span>
- [<span data-ttu-id="092a0-114">WebSockets desteği</span><span class="sxs-lookup"><span data-stu-id="092a0-114">WebSockets support</span></span>](xref:fundamentals/websockets)

## <a name="choosing-between-versions-10-and-11-of-aspnet-core"></a><span data-ttu-id="092a0-115">ASP.NET Core 1,0 ve 1,1 sürümleri arasında seçim yapma</span><span class="sxs-lookup"><span data-stu-id="092a0-115">Choosing between versions 1.0 and 1.1 of ASP.NET Core</span></span>

<span data-ttu-id="092a0-116">ASP.NET Core 1,1 ASP.NET Core 1,0 ' den daha fazla özelliğe sahip.</span><span class="sxs-lookup"><span data-stu-id="092a0-116">ASP.NET Core 1.1 has more features than ASP.NET Core 1.0.</span></span> <span data-ttu-id="092a0-117">Genel olarak, en son sürümü kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="092a0-117">In general, we recommend you use the latest version.</span></span>

## <a name="additional-information"></a><span data-ttu-id="092a0-118">Ek Bilgi</span><span class="sxs-lookup"><span data-stu-id="092a0-118">Additional Information</span></span>

- [<span data-ttu-id="092a0-119">ASP.NET Core 1.1.0 sürüm notları</span><span class="sxs-lookup"><span data-stu-id="092a0-119">ASP.NET Core 1.1.0 Release Notes</span></span>](https://github.com/dotnet/aspnetcore/releases/tag/1.1.0)
- <span data-ttu-id="092a0-120">ASP.NET Core geliştirme ekibinin ilerleme ve planlarıyla bağlantı kurmak için, [ASP.net Community](https://live.asp.net/)' ye ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="092a0-120">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
