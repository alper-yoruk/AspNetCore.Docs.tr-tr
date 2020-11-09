---
title: ASP.NET Core MVC 'de görünüm tabanlı yetkilendirme
author: rick-anderson
description: 'Bu belgede, ASP.NET Core görünümü içinde yetkilendirme hizmetinin nasıl ekleneceği ve kullanılacağı gösterilir Razor .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/views
ms.openlocfilehash: b3d6e595aa08208f2bf9e95d7070cf9c24802b62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061333"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="c0f72-103">ASP.NET Core MVC 'de görünüm tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="c0f72-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="c0f72-104">Geliştirici genellikle geçerli kullanıcı kimliğine göre Kullanıcı arabirimini göstermek, gizlemek veya değiştirmek ister.</span><span class="sxs-lookup"><span data-stu-id="c0f72-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="c0f72-105">[Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla MVC görünümleri içindeki yetkilendirme hizmetine erişebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c0f72-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c0f72-106">Yetkilendirme hizmetini bir görünüme eklemek için Razor `@inject` yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="c0f72-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="c0f72-107">Her görünümde yetkilendirme hizmeti isterseniz, `@inject` yönergeyi *görünümler* dizininin *_ViewImports. cshtml* dosyasına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="c0f72-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="c0f72-108">Daha fazla bilgi için bkz. [görünümlere bağımlılık ekleme](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c0f72-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="c0f72-109">`AuthorizeAsync` [Kaynak tabanlı yetkilendirme](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)sırasında kontrol yaptığınız şekilde tam olarak çağırmak için eklenen yetkilendirme hizmetini kullanın:</span><span class="sxs-lookup"><span data-stu-id="c0f72-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="c0f72-110">Bazı durumlarda, kaynak görünüm modeliniz olur.</span><span class="sxs-lookup"><span data-stu-id="c0f72-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="c0f72-111">`AuthorizeAsync` [Kaynak tabanlı yetkilendirme](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)sırasında kontrol ettiğiniz şekilde tam olarak çağır:</span><span class="sxs-lookup"><span data-stu-id="c0f72-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="c0f72-112">Yukarıdaki kodda, model, ilke değerlendirmesinin dikkate alınması gereken bir kaynak olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="c0f72-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="c0f72-113">Tek yetkilendirme denetimi olarak uygulamanızın kullanıcı arabirimi öğelerinin görünürlüğünü değiştirmeye güvenmeyin.</span><span class="sxs-lookup"><span data-stu-id="c0f72-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="c0f72-114">Bir kullanıcı arabirimi öğesini gizlemek, ilişkili denetleyici eylemine erişimi tamamen engellemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="c0f72-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="c0f72-115">Örneğin, önceki kod parçacığındaki düğmesini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="c0f72-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="c0f72-116">Bir Kullanıcı `Edit` , göreli kaynak URL 'si */Document/Edit/1* olduğunu biliyorsa eylem yöntemini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="c0f72-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1* .</span></span> <span data-ttu-id="c0f72-117">Bu nedenle, `Edit` eylem yöntemi kendi yetkilendirme denetimini gerçekleştirmelidir.</span><span class="sxs-lookup"><span data-stu-id="c0f72-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
