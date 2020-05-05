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
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>ASP.NET Core MVC 'de görünüm tabanlı yetkilendirme

Geliştirici genellikle geçerli kullanıcı kimliğine göre Kullanıcı arabirimini göstermek, gizlemek veya değiştirmek ister. [Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla MVC görünümleri içindeki yetkilendirme hizmetine erişebilirsiniz. Yetkilendirme hizmetini bir Razor görünüme eklemek için `@inject` yönergesini kullanın:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Her görünümde yetkilendirme hizmeti isterseniz, `@inject` yönergeyi *Görünümler* dizininin *_ViewImports. cshtml* dosyasına yerleştirin. Daha fazla bilgi için bkz. [görünümlere bağımlılık ekleme](xref:mvc/views/dependency-injection).

[Kaynak tabanlı yetkilendirme](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)sırasında kontrol yaptığınız şekilde `AuthorizeAsync` tam olarak çağırmak için eklenen yetkilendirme hizmetini kullanın:

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

Bazı durumlarda, kaynak görünüm modeliniz olur. `AuthorizeAsync` [Kaynak tabanlı yetkilendirme](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)sırasında kontrol ettiğiniz şekilde tam olarak çağır:

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

Yukarıdaki kodda, model, ilke değerlendirmesinin dikkate alınması gereken bir kaynak olarak geçirilir.

> [!WARNING]
> Tek yetkilendirme denetimi olarak uygulamanızın kullanıcı arabirimi öğelerinin görünürlüğünü değiştirmeye güvenmeyin. Bir kullanıcı arabirimi öğesini gizlemek, ilişkili denetleyici eylemine erişimi tamamen engellemeyebilir. Örneğin, önceki kod parçacığındaki düğmesini göz önünde bulundurun. Bir Kullanıcı, göreli kaynak `Edit` URL 'si */Document/Edit/1*olduğunu biliyorsa eylem yöntemini çağırabilir. Bu nedenle, `Edit` eylem yöntemi kendi yetkilendirme denetimini gerçekleştirmelidir.
