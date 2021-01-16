---
title: ASP.NET Core 'de ortam etiketi Yardımcısı
author: pkellner
description: Tüm özellikler dahil ASP.NET Core ortam etiketi Yardımcısı tanımlandı
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: d63364b0c052ba7f9e745e1ad829b8d1ca9122d2
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253130"
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="52885-103">ASP.NET Core 'de ortam etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="52885-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="52885-104">, [Peter Kellner](https://peterkellner.net) ve [Hisham bin ateya](https://twitter.com/hishambinateya) tarafından</span><span class="sxs-lookup"><span data-stu-id="52885-104">By [Peter Kellner](https://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="52885-105">Ortam etiketi Yardımcısı, Şirket içindeki içeriğini koşullu olarak geçerli [barındırma ortamına](xref:fundamentals/environments)göre oluşturur.</span><span class="sxs-lookup"><span data-stu-id="52885-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="52885-106">Ortam etiketi Yardımcısı 'nın tek özniteliği, `names` ortam adlarının virgülle ayrılmış listesidir.</span><span class="sxs-lookup"><span data-stu-id="52885-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="52885-107">Belirtilen ortam adlarından herhangi biri geçerli ortamla eşleşiyorsa, eklenen içerik işlenir.</span><span class="sxs-lookup"><span data-stu-id="52885-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="52885-108">Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro> ..</span><span class="sxs-lookup"><span data-stu-id="52885-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="52885-109">Ortam etiketi yardımcı öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="52885-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="52885-110">adlar</span><span class="sxs-lookup"><span data-stu-id="52885-110">names</span></span>

<span data-ttu-id="52885-111">`names` tek bir barındırma ortamı adını veya ekteki içeriğin işlenmesini tetikleyen barındırma ortamı adlarının virgülle ayrılmış bir listesini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="52885-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="52885-112">Ortam değerleri [ıwebhostenvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)tarafından döndürülen geçerli değerle karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="52885-112">Environment values are compared to the current value returned by [IWebHostEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="52885-113">Karşılaştırma büyük/küçük harf durumunu yoksayar.</span><span class="sxs-lookup"><span data-stu-id="52885-113">The comparison ignores case.</span></span>

<span data-ttu-id="52885-114">Aşağıdaki örnek bir ortam etiketi Yardımcısı kullanır.</span><span class="sxs-lookup"><span data-stu-id="52885-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="52885-115">İçerik, barındırma ortamı hazırlama veya üretim ise işlenir:</span><span class="sxs-lookup"><span data-stu-id="52885-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="52885-116">öznitelikleri dahil etme ve hariç tutma</span><span class="sxs-lookup"><span data-stu-id="52885-116">include and exclude attributes</span></span>

<span data-ttu-id="52885-117">`include`& `exclude` dahil edilen veya dışlanan barındırma ortamı adlarına göre, eklenen içeriği işleyen öznitelik denetimi.</span><span class="sxs-lookup"><span data-stu-id="52885-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="52885-118">include</span><span class="sxs-lookup"><span data-stu-id="52885-118">include</span></span>

<span data-ttu-id="52885-119">`include`Özelliği özniteliğe benzer bir davranış sergiler `names` .</span><span class="sxs-lookup"><span data-stu-id="52885-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="52885-120">Öznitelik değerinde listelenen bir ortam, `include` etiketin içeriğini işlemek için uygulamanın barındırma ortamıyla ([ıwebhostenvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) eşleşmelidir `<environment>` .</span><span class="sxs-lookup"><span data-stu-id="52885-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IWebHostEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="52885-121">dışlama</span><span class="sxs-lookup"><span data-stu-id="52885-121">exclude</span></span>

<span data-ttu-id="52885-122">`include`Özniteliğinin aksine,, `<environment>` barındırma ortamı öznitelik değerinde listelenen bir ortamla eşleşmediği zaman etiketin içeriği işlenir `exclude` .</span><span class="sxs-lookup"><span data-stu-id="52885-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>IWebHostEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="52885-123">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="52885-123">Additional resources</span></span>

* <xref:fundamentals/environments>
