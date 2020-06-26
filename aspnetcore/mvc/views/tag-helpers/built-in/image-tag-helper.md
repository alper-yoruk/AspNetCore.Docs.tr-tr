---
title: ASP.NET Core resim etiketi Yardımcısı
author: pkellner
description: Resim etiketi Yardımcısı ile çalışmayı gösterir.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: 4d8981b8bdf2e1341b5943745b901efd867d2e5e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399367"
---
# <a name="image-tag-helper-in-aspnet-core"></a><span data-ttu-id="cde76-103">ASP.NET Core resim etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="cde76-103">Image Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="cde76-104">By [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="cde76-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="cde76-105">Resim etiketi Yardımcısı, `<img>` statik görüntü dosyaları için önbellek-busting davranışı sağlamak üzere etiketini geliştirir.</span><span class="sxs-lookup"><span data-stu-id="cde76-105">The Image Tag Helper enhances the `<img>` tag to provide cache-busting behavior for static image files.</span></span>

<span data-ttu-id="cde76-106">Önbellek-busting dizesi, varlığın URL 'sine eklenen statik görüntü dosyasının karmasını temsil eden benzersiz bir değerdir.</span><span class="sxs-lookup"><span data-stu-id="cde76-106">A cache-busting string is a unique value representing the hash of the static image file appended to the asset's URL.</span></span> <span data-ttu-id="cde76-107">Benzersiz dize, istemcilerin (ve bazı proxy 'lerde), görüntüyü istemci önbelleğinden değil, ana bilgisayar Web sunucusundan yeniden yüklemesi için istemde bulunur.</span><span class="sxs-lookup"><span data-stu-id="cde76-107">The unique string prompts clients (and some proxies) to reload the image from the host web server and not from the client's cache.</span></span>

<span data-ttu-id="cde76-108">Görüntü kaynağı (), `src` ana bilgisayar Web sunucusunda bir statik dosya ise:</span><span class="sxs-lookup"><span data-stu-id="cde76-108">If the image source (`src`) is a static file on the host web server:</span></span>

* <span data-ttu-id="cde76-109">Benzersiz bir önbellek-busting dizesi, resim kaynağına bir sorgu parametresi olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="cde76-109">A unique cache-busting string is appended as a query parameter to the image source.</span></span>
* <span data-ttu-id="cde76-110">Ana bilgisayar Web sunucusundaki dosya değişirse, güncelleştirilmiş istek parametresini içeren benzersiz bir istek URL 'SI oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="cde76-110">If the file on the host web server changes, a unique request URL is generated that includes the updated request parameter.</span></span>

<span data-ttu-id="cde76-111">Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro> ..</span><span class="sxs-lookup"><span data-stu-id="cde76-111">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="image-tag-helper-attributes"></a><span data-ttu-id="cde76-112">Resim etiketi Yardımcısı öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="cde76-112">Image Tag Helper Attributes</span></span>

### <a name="src"></a><span data-ttu-id="cde76-113">src</span><span class="sxs-lookup"><span data-stu-id="cde76-113">src</span></span>

<span data-ttu-id="cde76-114">Resim etiketi yardımcısını etkinleştirmek için, `src` öğesinde özniteliği gereklidir `<img>` .</span><span class="sxs-lookup"><span data-stu-id="cde76-114">To activate the Image Tag Helper, the `src` attribute is required on the `<img>` element.</span></span>

<span data-ttu-id="cde76-115">Görüntü kaynağı ( `src` ), sunucudaki bir fiziksel statik dosyayı göstermelidir.</span><span class="sxs-lookup"><span data-stu-id="cde76-115">The image source (`src`) must point to a physical static file on the server.</span></span> <span data-ttu-id="cde76-116">, `src` Uzak bır URI ise, önbellek-busting sorgu dizesi parametresi oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="cde76-116">If the `src` is a remote URI, the cache-busting query string parameter isn't generated.</span></span>

### <a name="asp-append-version"></a><span data-ttu-id="cde76-117">ASP-Append-sürüm</span><span class="sxs-lookup"><span data-stu-id="cde76-117">asp-append-version</span></span>

<span data-ttu-id="cde76-118">`asp-append-version` `true` Bir özniteliğiyle birlikte belirtildiğinde `src` , resim etiketi Yardımcısı çağırılır.</span><span class="sxs-lookup"><span data-stu-id="cde76-118">When `asp-append-version` is specified with a `true` value along with a `src` attribute, the Image Tag Helper is invoked.</span></span>

<span data-ttu-id="cde76-119">Aşağıdaki örnek bir resim etiketi yardımcısını kullanır:</span><span class="sxs-lookup"><span data-stu-id="cde76-119">The following example uses an Image Tag Helper:</span></span>

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

<span data-ttu-id="cde76-120">Statik dosya */Wwwroot/images/* dizininde varsa, oluşturulan HTML şuna benzerdir (karma farklı olur):</span><span class="sxs-lookup"><span data-stu-id="cde76-120">If the static file exists in the directory */wwwroot/images/*, the generated HTML is similar to the following (the hash will be different):</span></span>

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

<span data-ttu-id="cde76-121">Parametreye atanan değer, `v` diskteki *asplogo.png* dosyanın karma değeridir.</span><span class="sxs-lookup"><span data-stu-id="cde76-121">The value assigned to the parameter `v` is the hash value of the *asplogo.png* file on disk.</span></span> <span data-ttu-id="cde76-122">Web sunucusu statik dosyaya okuma erişimi alamadığında, `v` `src` işlenen biçimlendirmede özniteliğe hiçbir parametre eklenmez.</span><span class="sxs-lookup"><span data-stu-id="cde76-122">If the web server is unable to obtain read access to the static file, no `v` parameter is added to the `src` attribute in the rendered markup.</span></span>

## <a name="hash-caching-behavior"></a><span data-ttu-id="cde76-123">Karma önbelleğe alma davranışı</span><span class="sxs-lookup"><span data-stu-id="cde76-123">Hash caching behavior</span></span>

<span data-ttu-id="cde76-124">Resim etiketi Yardımcısı, belirli bir dosyanın hesaplanmış karmasını depolamak için yerel Web sunucusundaki önbellek sağlayıcısını kullanır `Sha512` .</span><span class="sxs-lookup"><span data-stu-id="cde76-124">The Image Tag Helper uses the cache provider on the local web server to store the calculated `Sha512` hash of a given file.</span></span> <span data-ttu-id="cde76-125">Dosya birden çok kez isteniyorsa, karma yeniden hesaplanmadı.</span><span class="sxs-lookup"><span data-stu-id="cde76-125">If the file is requested multiple times, the hash isn't recalculated.</span></span> <span data-ttu-id="cde76-126">Önbellek, dosyanın karması hesaplanırken dosyaya iliştirilmiş bir dosya İzleyicisi tarafından geçersiz kılınır `Sha512` .</span><span class="sxs-lookup"><span data-stu-id="cde76-126">The cache is invalidated by a file watcher that's attached to the file when the file's `Sha512` hash is calculated.</span></span> <span data-ttu-id="cde76-127">Dosya diskte değiştiğinde yeni bir karma hesaplanır ve önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="cde76-127">When the file changes on disk, a new hash is calculated and cached.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cde76-128">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cde76-128">Additional resources</span></span>

* <xref:performance/caching/memory>
