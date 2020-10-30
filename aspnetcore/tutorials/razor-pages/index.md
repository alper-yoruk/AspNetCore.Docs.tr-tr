---
title: 'Öğretici: ASP.NET Core bir :::no-loc(Razor)::: sayfa Web uygulaması oluşturma'
author: rick-anderson
description: :::no-loc(Razor):::Visual Studio, ASP.NET Core ve EF Core Windows üzerinde bir sayfa Web uygulaması oluşturun.
ms.author: riande
ms.date: 08/09/2019
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
uid: tutorials/razor-pages/index
ms.openlocfilehash: 0feb60c7e0189bf12d584c3916e0254b3be729c0
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058070"
---
# <a name="tutorial-create-a-no-locrazor-pages-web-app-with-aspnet-core"></a><span data-ttu-id="bf2b9-103">Öğretici: ASP.NET Core bir :::no-loc(Razor)::: sayfa Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="bf2b9-103">Tutorial: Create a :::no-loc(Razor)::: Pages web app with ASP.NET Core</span></span>

<span data-ttu-id="bf2b9-104">Bu öğretici dizisinde bir sayfa Web uygulaması oluşturma temelleri açıklanmaktadır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="bf2b9-104">This series of tutorials explains the basics of building a :::no-loc(Razor)::: Pages web app.</span></span> 

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="bf2b9-105">Bu seri aşağıdaki öğreticileri içerir:</span><span class="sxs-lookup"><span data-stu-id="bf2b9-105">This series includes the following tutorials:</span></span>

1. [<span data-ttu-id="bf2b9-106">:::no-loc(Razor):::Sayfalar Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="bf2b9-106">Create a :::no-loc(Razor)::: Pages web app</span></span>](xref:tutorials/razor-pages/razor-pages-start)
1. [<span data-ttu-id="bf2b9-107">:::no-loc(Razor):::Sayfalar uygulamasına model ekleme</span><span class="sxs-lookup"><span data-stu-id="bf2b9-107">Add a model to a :::no-loc(Razor)::: Pages app</span></span>](xref:tutorials/razor-pages/model)
1. [<span data-ttu-id="bf2b9-108">Yapı iskelesi (oluşturma) :::no-loc(Razor)::: sayfaları</span><span class="sxs-lookup"><span data-stu-id="bf2b9-108">Scaffold (generate) :::no-loc(Razor)::: pages</span></span>](xref:tutorials/razor-pages/page)
1. [<span data-ttu-id="bf2b9-109">Veritabanı ile çalışma</span><span class="sxs-lookup"><span data-stu-id="bf2b9-109">Work with a database</span></span>](xref:tutorials/razor-pages/sql)
1. [<span data-ttu-id="bf2b9-110">:::no-loc(Razor):::Sayfaları Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="bf2b9-110">Update :::no-loc(Razor)::: pages</span></span>](xref:tutorials/razor-pages/da1)
1. [<span data-ttu-id="bf2b9-111">Arama ekleme</span><span class="sxs-lookup"><span data-stu-id="bf2b9-111">Add search</span></span>](xref:tutorials/razor-pages/search)
1. [<span data-ttu-id="bf2b9-112">Yeni alan ekleme</span><span class="sxs-lookup"><span data-stu-id="bf2b9-112">Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
1. [<span data-ttu-id="bf2b9-113">Doğrulama ekleme</span><span class="sxs-lookup"><span data-stu-id="bf2b9-113">Add validation</span></span>](xref:tutorials/razor-pages/validation)

<span data-ttu-id="bf2b9-114">Son olarak, bir film veritabanını görüntüleyebilen ve yönetebileceğini bir uygulamanıza sahip olacaksınız.</span><span class="sxs-lookup"><span data-stu-id="bf2b9-114">At the end, you'll have an app that can display and manage a database of movies.</span></span>

![Örnek uygulamanın örnek sayfası](index/_static/sample-page.png)
