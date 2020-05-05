---
title: ASP.NET Core MVC 'de görünüm tabanlı yetkilendirme
author: rick-anderson
description: Bu belgede, ASP.NET Core Razor görünümü içinde yetkilendirme hizmetinin nasıl ekleneceği ve kullanılacağı gösterilir.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/views
ms.openlocfilehash: 1a3b4a92d270844fa99fc9fb0283226e94edb22d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775628"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="19ade-103">ASP.NET Core MVC 'de görünüm tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="19ade-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="19ade-104">Geliştirici genellikle geçerli kullanıcı kimliğine göre Kullanıcı arabirimini göstermek, gizlemek veya değiştirmek ister.</span><span class="sxs-lookup"><span data-stu-id="19ade-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="19ade-105">[Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla MVC görünümleri içindeki yetkilendirme hizmetine erişebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="19ade-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="19ade-106">Yetkilendirme hizmetini bir Razor görünüme eklemek için `@inject` yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="19ade-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="19ade-107">Her görünümde yetkilendirme hizmeti isterseniz, `@inject` yönergeyi *Görünümler* dizininin *_ViewImports. cshtml* dosyasına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="19ade-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="19ade-108">Daha fazla bilgi için bkz. [görünümlere bağımlılık ekleme](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="19ade-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="19ade-109">[Kaynak tabanlı yetkilendirme](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)sırasında kontrol yaptığınız şekilde `AuthorizeAsync` tam olarak çağırmak için eklenen yetkilendirme hizmetini kullanın:</span><span class="sxs-lookup"><span data-stu-id="19ade-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="19ade-110">Bazı durumlarda, kaynak görünüm modeliniz olur.</span><span class="sxs-lookup"><span data-stu-id="19ade-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="19ade-111">`AuthorizeAsync` [Kaynak tabanlı yetkilendirme](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)sırasında kontrol ettiğiniz şekilde tam olarak çağır:</span><span class="sxs-lookup"><span data-stu-id="19ade-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="19ade-112">Yukarıdaki kodda, model, ilke değerlendirmesinin dikkate alınması gereken bir kaynak olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="19ade-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="19ade-113">Tek yetkilendirme denetimi olarak uygulamanızın kullanıcı arabirimi öğelerinin görünürlüğünü değiştirmeye güvenmeyin.</span><span class="sxs-lookup"><span data-stu-id="19ade-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="19ade-114">Bir kullanıcı arabirimi öğesini gizlemek, ilişkili denetleyici eylemine erişimi tamamen engellemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="19ade-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="19ade-115">Örneğin, önceki kod parçacığındaki düğmesini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="19ade-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="19ade-116">Bir Kullanıcı, göreli kaynak `Edit` URL 'si */Document/Edit/1*olduğunu biliyorsa eylem yöntemini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="19ade-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1*.</span></span> <span data-ttu-id="19ade-117">Bu nedenle, `Edit` eylem yöntemi kendi yetkilendirme denetimini gerçekleştirmelidir.</span><span class="sxs-lookup"><span data-stu-id="19ade-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
