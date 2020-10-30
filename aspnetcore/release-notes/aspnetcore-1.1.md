---
title: ASP.NET Core 1,1 ' deki yenilikler
author: rick-anderson
description: ASP.NET Core 1,1 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.date: 12/18/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: aspnetcore-1.1
ms.openlocfilehash: cc891280a6314dbc4c0838d5b4a8d2a20698eab6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059747"
---
# <a name="whats-new-in-aspnet-core-11"></a><span data-ttu-id="b4c73-103">ASP.NET Core 1,1 ' deki yenilikler</span><span class="sxs-lookup"><span data-stu-id="b4c73-103">What's new in ASP.NET Core 1.1</span></span>

<span data-ttu-id="b4c73-104">ASP.NET Core 1,1 aşağıdaki yeni özellikleri içerir:</span><span class="sxs-lookup"><span data-stu-id="b4c73-104">ASP.NET Core 1.1 includes the following new features:</span></span>

- [<span data-ttu-id="b4c73-105">URL yeniden yazma ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="b4c73-105">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
- [<span data-ttu-id="b4c73-106">Yanıt önbelleğe alma ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="b4c73-106">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
- [<span data-ttu-id="b4c73-107">Bileşenleri etiket yardımcıları olarak görüntüle</span><span class="sxs-lookup"><span data-stu-id="b4c73-107">View Components as Tag Helpers</span></span>](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper)
- [<span data-ttu-id="b4c73-108">MVC filtreleri olarak ara yazılım</span><span class="sxs-lookup"><span data-stu-id="b4c73-108">Middleware as MVC filters</span></span>](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline)
- [<span data-ttu-id="b4c73-109">:::no-loc(Cookie):::tabanlı TempData sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="b4c73-109">:::no-loc(Cookie):::-based TempData provider</span></span>](xref:fundamentals/app-state#tempdata)
- [<span data-ttu-id="b4c73-110">Azure App Service günlük sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="b4c73-110">Azure App Service logging provider</span></span>](xref:fundamentals/logging/index#azure-app-service-provider)
- [<span data-ttu-id="b4c73-111">Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="b4c73-111">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
- [<span data-ttu-id="b4c73-112">Azure ve Redsıs depolama veri koruma anahtarı depoları</span><span class="sxs-lookup"><span data-stu-id="b4c73-112">Azure and Redis Storage Data Protection Key Repositories</span></span>](xref:security/data-protection/implementation/key-storage-providers)
- <span data-ttu-id="b4c73-113">Windows için WebListener sunucusu</span><span class="sxs-lookup"><span data-stu-id="b4c73-113">WebListener Server for Windows</span></span>
- [<span data-ttu-id="b4c73-114">WebSockets desteği</span><span class="sxs-lookup"><span data-stu-id="b4c73-114">WebSockets support</span></span>](xref:fundamentals/websockets)

## <a name="choosing-between-versions-10-and-11-of-aspnet-core"></a><span data-ttu-id="b4c73-115">ASP.NET Core 1,0 ve 1,1 sürümleri arasında seçim yapma</span><span class="sxs-lookup"><span data-stu-id="b4c73-115">Choosing between versions 1.0 and 1.1 of ASP.NET Core</span></span>

<span data-ttu-id="b4c73-116">ASP.NET Core 1,1 ASP.NET Core 1,0 ' den daha fazla özelliğe sahip.</span><span class="sxs-lookup"><span data-stu-id="b4c73-116">ASP.NET Core 1.1 has more features than ASP.NET Core 1.0.</span></span> <span data-ttu-id="b4c73-117">Genel olarak, en son sürümü kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="b4c73-117">In general, we recommend you use the latest version.</span></span>

## <a name="additional-information"></a><span data-ttu-id="b4c73-118">Ek Bilgi</span><span class="sxs-lookup"><span data-stu-id="b4c73-118">Additional Information</span></span>

- [<span data-ttu-id="b4c73-119">ASP.NET Core 1.1.0 sürüm notları</span><span class="sxs-lookup"><span data-stu-id="b4c73-119">ASP.NET Core 1.1.0 Release Notes</span></span>](https://github.com/dotnet/aspnetcore/releases/tag/1.1.0)
- <span data-ttu-id="b4c73-120">ASP.NET Core geliştirme ekibinin ilerleme ve planlarıyla bağlantı kurmak için, [ASP.net Community](https://live.asp.net/)' ye ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b4c73-120">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
