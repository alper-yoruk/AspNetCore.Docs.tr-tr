---
title: ASP.NET Core MVC 'de görünüm tabanlı yetkilendirme
author: rick-anderson
description: Bu belgede, ASP.NET Core görünümü içinde yetkilendirme hizmetinin nasıl ekleneceği ve kullanılacağı gösterilir Razor .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/views
ms.openlocfilehash: b3d6e595aa08208f2bf9e95d7070cf9c24802b62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061333"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>ASP.NET Core MVC 'de görünüm tabanlı yetkilendirme

Geliştirici genellikle geçerli kullanıcı kimliğine göre Kullanıcı arabirimini göstermek, gizlemek veya değiştirmek ister. [Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla MVC görünümleri içindeki yetkilendirme hizmetine erişebilirsiniz. Yetkilendirme hizmetini bir görünüme eklemek için Razor `@inject` yönergesini kullanın:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Her görünümde yetkilendirme hizmeti isterseniz, `@inject` yönergeyi *görünümler* dizininin *_ViewImports. cshtml* dosyasına yerleştirin. Daha fazla bilgi için bkz. [görünümlere bağımlılık ekleme](xref:mvc/views/dependency-injection).

`AuthorizeAsync` [Kaynak tabanlı yetkilendirme](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)sırasında kontrol yaptığınız şekilde tam olarak çağırmak için eklenen yetkilendirme hizmetini kullanın:

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
> Tek yetkilendirme denetimi olarak uygulamanızın kullanıcı arabirimi öğelerinin görünürlüğünü değiştirmeye güvenmeyin. Bir kullanıcı arabirimi öğesini gizlemek, ilişkili denetleyici eylemine erişimi tamamen engellemeyebilir. Örneğin, önceki kod parçacığındaki düğmesini göz önünde bulundurun. Bir Kullanıcı `Edit` , göreli kaynak URL 'si */Document/Edit/1* olduğunu biliyorsa eylem yöntemini çağırabilir. Bu nedenle, `Edit` eylem yöntemi kendi yetkilendirme denetimini gerçekleştirmelidir.
