---
title: 'Razor ASP.NET Core için sayfa yetkilendirme kuralları'
author: rick-anderson
description: Kullanıcılara yetki veren ve anonim kullanıcıların sayfalara veya sayfa klasörlerine erişmesine izin veren kurallara sahip sayfalara erişimi denetleme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
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
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 69e1d639aeb55ae64cc54b1cda402ed6bcbb04ab
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060189"
---
# <a name="no-locrazor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="eb8df-103">Razor ASP.NET Core için sayfa yetkilendirme kuralları</span><span class="sxs-lookup"><span data-stu-id="eb8df-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb8df-104">Sayfalar uygulamanızda erişimi denetlemeye yönelik bir yol Razor , başlangıçta yetkilendirme kurallarını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="eb8df-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="eb8df-105">Bu kurallar, kullanıcıları yetkilendirmeniz ve anonim kullanıcıların ayrı sayfalara veya sayfa klasörlerine erişmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="eb8df-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="eb8df-106">Bu konu başlığı altında açıklanan kurallar, erişimi denetlemek için otomatik olarak [Yetkilendirme filtreleri](xref:mvc/controllers/filters#authorization-filters) uygular.</span><span class="sxs-lookup"><span data-stu-id="eb8df-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="eb8df-107">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eb8df-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="eb8df-108">Örnek uygulama, [ cookie kimlik doğrulaması olmadan ASP.NET Core Identity ](xref:security/authentication/cookie)kullanır.</span><span class="sxs-lookup"><span data-stu-id="eb8df-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="eb8df-109">Bu konuda gösterilen kavramlar ve örnekler, kullanan uygulamalar için eşit oranda geçerlidir ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="eb8df-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="eb8df-110">Kullanmak için ASP.NET Core Identity içindeki yönergeleri izleyin <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="eb8df-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="eb8df-111">Bir sayfaya erişmek için yetkilendirme gerektir</span><span class="sxs-lookup"><span data-stu-id="eb8df-111">Require authorization to access a page</span></span>

<span data-ttu-id="eb8df-112"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*>Belirtilen yoldaki sayfaya bir eklemek için kuralı kullanın <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> :</span><span class="sxs-lookup"><span data-stu-id="eb8df-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="eb8df-113">Belirtilen yol, Razor uzantısı olmayan ve yalnızca eğik çizgi içeren sayfa kök göreli yolu olan görünüm altyapısı yoludur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="eb8df-114">Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek Için bir [authorizepage aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="eb8df-115"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Filtre özniteliğiyle bir sayfa modeli sınıfına uygulanabilir `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="eb8df-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="eb8df-116">Daha fazla bilgi için bkz. [Yetkilendirme filtre özniteliği](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="eb8df-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="eb8df-117">Bir sayfa klasörüne erişmek için yetkilendirme gerektir</span><span class="sxs-lookup"><span data-stu-id="eb8df-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="eb8df-118"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Belirtilen yoldaki bir klasördeki tüm sayfalara bir eklemek için kuralını kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

<span data-ttu-id="eb8df-119">Belirtilen yol, Razor sayfa kök göreli yolu olan görünüm altyapısı yoludur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="eb8df-120">Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek için, bir [authorizefolder aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="eb8df-121">Alan sayfasına erişmek için yetkilendirme gerektir</span><span class="sxs-lookup"><span data-stu-id="eb8df-121">Require authorization to access an area page</span></span>

<span data-ttu-id="eb8df-122"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Belirtilen yoldaki alan sayfasına eklemek için kuralını kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="eb8df-123">Sayfa adı, belirtilen alanın sayfalar kök dizinine göre uzantısı olmayan dosyanın yoludur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="eb8df-124">Örneğin, dosya *alanı/ Identity /Pages/Manage/accounts.exe* için sayfa adı */Manage/accounts* olur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts* .</span></span>

<span data-ttu-id="eb8df-125">Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek için, bir [Authorizeareapage aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="eb8df-126">Bir alan klasörüne erişmek için yetkilendirme gerektir</span><span class="sxs-lookup"><span data-stu-id="eb8df-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="eb8df-127"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Belirtilen yoldaki bir klasördeki tüm alanlara bir eklemek için kuralını kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="eb8df-128">Klasör yolu, belirtilen alanın sayfalar kök dizinine göre klasörün yoludur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="eb8df-129">Örneğin, *Areas/ Identity /Pages/Manage/* altındaki dosyalar için klasör yolu/ *Yönet* ' dir.</span><span class="sxs-lookup"><span data-stu-id="eb8df-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage* .</span></span>

<span data-ttu-id="eb8df-130">Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek için, bir [Authorizeareafolder aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="eb8df-131">Bir sayfaya anonim erişime izin ver</span><span class="sxs-lookup"><span data-stu-id="eb8df-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="eb8df-132"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*>Belirtilen yoldaki bir sayfaya bir eklemek için kuralı kullanın <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> :</span><span class="sxs-lookup"><span data-stu-id="eb8df-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="eb8df-133">Belirtilen yol, Razor uzantısı olmayan ve yalnızca eğik çizgi içeren sayfa kök göreli yolu olan görünüm altyapısı yoludur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="eb8df-134">Bir sayfa klasörüne anonim erişime izin ver</span><span class="sxs-lookup"><span data-stu-id="eb8df-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="eb8df-135"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Belirtilen yoldaki bir klasördeki tüm sayfalara bir eklemek için kuralını kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="eb8df-136">Belirtilen yol, Razor sayfa kök göreli yolu olan görünüm altyapısı yoludur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="eb8df-137">Yetkili ve anonim erişimi birleştirme hakkında</span><span class="sxs-lookup"><span data-stu-id="eb8df-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="eb8df-138">Bir sayfa klasörünün yetkilendirme gerektirdiğini ve sonra bu klasörün içindeki bir sayfanın adsız erişime izin verdiğini belirtmek için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="eb8df-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="eb8df-139">Ancak, tersi de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="eb8df-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="eb8df-140">Anonim erişim için bir sayfa klasörü bildiremezsiniz ve ardından bu klasör içinde yetkilendirme gerektiren bir sayfa belirtemezsiniz:</span><span class="sxs-lookup"><span data-stu-id="eb8df-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="eb8df-141">Özel sayfada yetkilendirme gerektirme başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="eb8df-142">Ve sayfaya her ikisi de <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> uygulandığında,, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> öncelik alır ve erişimi denetler.</span><span class="sxs-lookup"><span data-stu-id="eb8df-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb8df-143">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="eb8df-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eb8df-144">Sayfalar uygulamanızda erişimi denetlemeye yönelik bir yol Razor , başlangıçta yetkilendirme kurallarını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="eb8df-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="eb8df-145">Bu kurallar, kullanıcıları yetkilendirmeniz ve anonim kullanıcıların ayrı sayfalara veya sayfa klasörlerine erişmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="eb8df-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="eb8df-146">Bu konu başlığı altında açıklanan kurallar, erişimi denetlemek için otomatik olarak [Yetkilendirme filtreleri](xref:mvc/controllers/filters#authorization-filters) uygular.</span><span class="sxs-lookup"><span data-stu-id="eb8df-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="eb8df-147">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eb8df-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="eb8df-148">Örnek uygulama, [ cookie kimlik doğrulaması olmadan ASP.NET Core Identity ](xref:security/authentication/cookie)kullanır.</span><span class="sxs-lookup"><span data-stu-id="eb8df-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="eb8df-149">Bu konuda gösterilen kavramlar ve örnekler, kullanan uygulamalar için eşit oranda geçerlidir ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="eb8df-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="eb8df-150">Kullanmak için ASP.NET Core Identity içindeki yönergeleri izleyin <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="eb8df-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="eb8df-151">Bir sayfaya erişmek için yetkilendirme gerektir</span><span class="sxs-lookup"><span data-stu-id="eb8df-151">Require authorization to access a page</span></span>

<span data-ttu-id="eb8df-152"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Belirtilen yoldaki sayfaya bir eklemek için aracılığıyla kuralını kullanın <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> :</span><span class="sxs-lookup"><span data-stu-id="eb8df-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="eb8df-153">Belirtilen yol, Razor uzantısı olmayan ve yalnızca eğik çizgi içeren sayfa kök göreli yolu olan görünüm altyapısı yoludur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="eb8df-154">Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek Için bir [authorizepage aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="eb8df-155"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Filtre özniteliğiyle bir sayfa modeli sınıfına uygulanabilir `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="eb8df-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="eb8df-156">Daha fazla bilgi için bkz. [Yetkilendirme filtre özniteliği](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="eb8df-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="eb8df-157">Bir sayfa klasörüne erişmek için yetkilendirme gerektir</span><span class="sxs-lookup"><span data-stu-id="eb8df-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="eb8df-158"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Belirtilen yoldaki bir klasördeki tüm sayfalara eklemek için aracılığıyla kuralını kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="eb8df-159">Belirtilen yol, Razor sayfa kök göreli yolu olan görünüm altyapısı yoludur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="eb8df-160">Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek için, bir [authorizefolder aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="eb8df-161">Alan sayfasına erişmek için yetkilendirme gerektir</span><span class="sxs-lookup"><span data-stu-id="eb8df-161">Require authorization to access an area page</span></span>

<span data-ttu-id="eb8df-162"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Belirtilen yoldaki alan sayfasına eklemek için aracılığıyla kuralını kullanın <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> :</span><span class="sxs-lookup"><span data-stu-id="eb8df-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="eb8df-163">Sayfa adı, belirtilen alanın sayfalar kök dizinine göre uzantısı olmayan dosyanın yoludur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="eb8df-164">Örneğin, dosya *alanı/ Identity /Pages/Manage/accounts.exe* için sayfa adı */Manage/accounts* olur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts* .</span></span>

<span data-ttu-id="eb8df-165">Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek için, bir [Authorizeareapage aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="eb8df-166">Bir alan klasörüne erişmek için yetkilendirme gerektir</span><span class="sxs-lookup"><span data-stu-id="eb8df-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="eb8df-167"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Belirtilen yoldaki bir klasördeki tüm alanlara bir eklemek için aracılığıyla kuralını kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="eb8df-168">Klasör yolu, belirtilen alanın sayfalar kök dizinine göre klasörün yoludur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="eb8df-169">Örneğin, *Areas/ Identity /Pages/Manage/* altındaki dosyalar için klasör yolu/ *Yönet* ' dir.</span><span class="sxs-lookup"><span data-stu-id="eb8df-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage* .</span></span>

<span data-ttu-id="eb8df-170">Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek için, bir [Authorizeareafolder aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="eb8df-171">Bir sayfaya anonim erişime izin ver</span><span class="sxs-lookup"><span data-stu-id="eb8df-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="eb8df-172"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Belirtilen yoldaki bir sayfaya bir eklemek için aracılığıyla kuralını kullanın <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> :</span><span class="sxs-lookup"><span data-stu-id="eb8df-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="eb8df-173">Belirtilen yol, Razor uzantısı olmayan ve yalnızca eğik çizgi içeren sayfa kök göreli yolu olan görünüm altyapısı yoludur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="eb8df-174">Bir sayfa klasörüne anonim erişime izin ver</span><span class="sxs-lookup"><span data-stu-id="eb8df-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="eb8df-175"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Belirtilen yoldaki bir klasördeki tüm sayfalara eklemek için aracılığıyla kuralını kullanın:</span><span class="sxs-lookup"><span data-stu-id="eb8df-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="eb8df-176">Belirtilen yol, Razor sayfa kök göreli yolu olan görünüm altyapısı yoludur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="eb8df-177">Yetkili ve anonim erişimi birleştirme hakkında</span><span class="sxs-lookup"><span data-stu-id="eb8df-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="eb8df-178">Yetkilendirme gerektiren bir sayfa klasörünün ve bu klasörün içindeki bir sayfanın adsız erişime izin verdiğini belirtmek için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="eb8df-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="eb8df-179">Ancak, tersi de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="eb8df-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="eb8df-180">Anonim erişim için bir sayfa klasörü bildiremezsiniz ve ardından bu klasör içinde yetkilendirme gerektiren bir sayfa belirtemezsiniz:</span><span class="sxs-lookup"><span data-stu-id="eb8df-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="eb8df-181">Özel sayfada yetkilendirme gerektirme başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="eb8df-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="eb8df-182">Ve sayfaya her ikisi de <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> uygulandığında,, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> öncelik alır ve erişimi denetler.</span><span class="sxs-lookup"><span data-stu-id="eb8df-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb8df-183">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="eb8df-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
