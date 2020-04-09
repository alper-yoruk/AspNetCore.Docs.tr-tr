---
title: LibMan ile ASP.NET Core'da istemci tarafı kitaplık edinimi
author: scottaddie
description: Kitaplık Yöneticisi 'ni (LibMan) kullanarak istemci tarafındaki kitaplık varlıklarını ASP.NET Core projesine nasıl yükleyebilirsiniz öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: 87987446b7f2c625da90951510e697e06569ba36
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664670"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a><span data-ttu-id="b05a4-103">LibMan ile ASP.NET Core'da istemci tarafı kitaplık edinimi</span><span class="sxs-lookup"><span data-stu-id="b05a4-103">Client-side library acquisition in ASP.NET Core with LibMan</span></span>

<span data-ttu-id="b05a4-104">Yazar: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="b05a4-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="b05a4-105">Kitaplık Yöneticisi (LibMan) hafif, istemci tarafı kitaplık edinme aracıdır.</span><span class="sxs-lookup"><span data-stu-id="b05a4-105">Library Manager (LibMan) is a lightweight, client-side library acquisition tool.</span></span> <span data-ttu-id="b05a4-106">LibMan, popüler kitaplıkları ve çerçeveleri dosya sisteminden veya [içerik dağıtım ağından (CDN)](https://wikipedia.org/wiki/Content_delivery_network)indirir.</span><span class="sxs-lookup"><span data-stu-id="b05a4-106">LibMan downloads popular libraries and frameworks from the file system or from a [content delivery network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span></span> <span data-ttu-id="b05a4-107">Desteklenen [CDNs CDNJS](https://cdnjs.com/)dahil , [jsDelivr](https://www.jsdelivr.com/), ve [unpkg](https://unpkg.com/#/).</span><span class="sxs-lookup"><span data-stu-id="b05a4-107">The supported CDNs include [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/), and [unpkg](https://unpkg.com/#/).</span></span> <span data-ttu-id="b05a4-108">Seçili kitaplık dosyaları getirilir ve ASP.NET Çekirdek projesi içinde uygun konuma yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b05a4-108">The selected library files are fetched and placed in the appropriate location within the ASP.NET Core project.</span></span>

## <a name="libman-use-cases"></a><span data-ttu-id="b05a4-109">LibMan kullanım örnekleri</span><span class="sxs-lookup"><span data-stu-id="b05a4-109">LibMan use cases</span></span>

<span data-ttu-id="b05a4-110">LibMan aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="b05a4-110">LibMan offers the following benefits:</span></span>

* <span data-ttu-id="b05a4-111">Yalnızca gereksinim duyduğunuz kitaplık dosyaları indirilir.</span><span class="sxs-lookup"><span data-stu-id="b05a4-111">Only the library files you need are downloaded.</span></span>
* <span data-ttu-id="b05a4-112">[Not.js,](https://nodejs.org) [npm](https://www.npmjs.com)ve [WebPack](https://webpack.js.org)gibi ek araç, kitaplıktaki dosyaların bir alt kümesini elde etmek için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="b05a4-112">Additional tooling, such as [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), and [WebPack](https://webpack.js.org), isn't necessary to acquire a subset of files in a library.</span></span>
* <span data-ttu-id="b05a4-113">Dosyalar, görevler oluşturma veya el ile dosya kopyalamaya başvurmadan belirli bir konuma yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b05a4-113">Files can be placed in a specific location without resorting to build tasks or manual file copying.</span></span>

<span data-ttu-id="b05a4-114">LibMan'ın avantajları hakkında daha fazla bilgi için [Visual Studio 2017: LibMan segmentinde Modern ön uç web geliştirmesini](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s)izleyin.</span><span class="sxs-lookup"><span data-stu-id="b05a4-114">For more information about LibMan's benefits, watch [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span></span>

<span data-ttu-id="b05a4-115">LibMan bir paket yönetim sistemi değil.</span><span class="sxs-lookup"><span data-stu-id="b05a4-115">LibMan isn't a package management system.</span></span> <span data-ttu-id="b05a4-116">Zaten npm veya [iplik](https://yarnpkg.com)gibi bir paket yöneticisi kullanıyorsanız, bunu yapmaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="b05a4-116">If you're already using a package manager, such as npm or [yarn](https://yarnpkg.com), continue doing so.</span></span> <span data-ttu-id="b05a4-117">LibMan bu aletleri değiştirmek için geliştirilmemiş.</span><span class="sxs-lookup"><span data-stu-id="b05a4-117">LibMan wasn't developed to replace those tools.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b05a4-118">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b05a4-118">Additional resources</span></span>

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="b05a4-119">LibMan GitHub deposu</span><span class="sxs-lookup"><span data-stu-id="b05a4-119">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
