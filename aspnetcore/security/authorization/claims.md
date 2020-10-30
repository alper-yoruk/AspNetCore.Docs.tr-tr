---
title: ASP.NET Core 'de talep tabanlı yetkilendirme
author: rick-anderson
description: ASP.NET Core uygulamasında yetkilendirme için talep denetimleri eklemeyi öğrenin.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/claims
ms.openlocfilehash: d6317da6bca69b4c46d74a2f76d81af4059d1cd8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060280"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>ASP.NET Core 'de talep tabanlı yetkilendirme

<a name="security-authorization-claims-based"></a>

Bir kimlik oluşturulduğunda, güvenilen bir taraf tarafından verilen bir veya daha fazla talep atanabilir. Talep, konunun ne yapabileceğini temsil eden bir ad değer çiftidir. Örneğin, bir yerel bir itici lisans yetkilisi tarafından verilen bir sürücü lisansına sahip olabilirsiniz. Sürücünüzün lisansının, bu tarihte Doğum tarihi vardır. Bu durumda, talep adı olacaktır `DateOfBirth` ; Örneğin, talep değeri Doğum tarihidir `8th June 1970` ve veren lisans yetkilisi olur. Talep tabanlı yetkilendirme, en basit, bir talebin değerini denetler ve bu değere göre bir kaynağa erişim sağlar. Örneğin, gece kulübünün erişim istiyorsanız yetkilendirme süreci şu olabilir:

Kapılı güvenlik müdürü, Doğum talepinizin tarihini ve erişim izni vermeden önce veren (itici lisans yetkilisi) tarafından güvenip güvenmeyeceğini değerlendirir.

Bir kimlik birden çok değer içeren birden fazla talep içerebilir ve aynı türde birden fazla talep içerebilir.

## <a name="adding-claims-checks"></a>Talep denetimleri ekleme

Talep tabanlı yetkilendirme denetimleri bildirime dayalı-geliştirici, bunları kendi kodlarında bir denetleyiciye veya denetleyici içindeki bir eyleme göre, geçerli kullanıcının sahip olması gereken talepleri belirterek ve isteğe bağlı olarak istenen kaynağa erişmek için talebin saklanması gereken değeri içerecek şekilde gömer. Talep gereksinimleri ilke tabanlıdır, geliştiricilerin talep gereksinimlerini ifade eden bir ilke oluşturması ve kaydetmesi gerekir.

En basit talep ilkesi türü bir talep olup olmadığını arar ve değeri denetlemez.

Önce ilkeyi oluşturmanız ve kaydetmeniz gerekir. Bu, normalde Startup.cs dosyanızda bir parçası olan yetkilendirme hizmeti yapılandırmasının bir parçası olarak gerçekleşir `ConfigureServices()` . *Startup.cs*

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

Bu durumda, `EmployeeOnly` ilke `EmployeeNumber` geçerli kimlik üzerinde bir talep olup olmadığını denetler.

Ardından ilkeyi, `Policy` `AuthorizeAttribute` ilke adını belirtmek için özniteliği üzerinde özelliğini kullanarak uygularsınız.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

`AuthorizeAttribute`Özniteliği bir denetleyicinin tamamına uygulanabilir, bu örnekte yalnızca ilkeyle eşleşen kimlikler denetleyicideki herhangi bir eyleme erişime izin verilir.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

Özniteliği tarafından korunan bir denetleyicsahipseniz `AuthorizeAttribute` , ancak belirli eylemlere anonim erişime izin vermek istiyorsanız, `AllowAnonymousAttribute` özniteliği uygularsınız.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

Çoğu talep bir değerle gelir. İlke oluştururken izin verilen değerlerin bir listesini belirtebilirsiniz. Aşağıdaki örnek yalnızca çalışan numarası 1, 2, 3, 4 veya 5 olan çalışanlar için başarılı olur.

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a>Genel talep denetimi ekleme

Talep değeri tek bir değer değilse veya bir dönüşüm gerekliyse, [Requireassertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion)kullanın. Daha fazla bilgi için bkz. bir [ilkeyi yerine getirmek için bir Func kullanma](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).

## <a name="multiple-policy-evaluation"></a>Birden çok Ilke değerlendirmesi

Bir denetleyiciye veya eyleme birden çok ilke uygularsanız, erişim verilmeden önce tüm ilkelerin geçmesi gerekir. Örneğin:

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

Yukarıdaki örnekte, ilkeyi karşılayan herhangi bir kimlik, `EmployeeOnly` `Payslip` denetleyicide ilke zorlandığından eyleme erişebilir. Ancak, eylemi çağırmak için `UpdateSalary` kimlik hem ilkeyi hem *de* ilkeyi yerine getirmelidir `EmployeeOnly` `HumanResources` .

Doğum talebinde bulunmak gibi daha karmaşık ilkeler isterseniz, bu tarihten itibaren bir yaşı hesaplamak, daha sonra yaşı 21 veya daha eski bir tarihte denetlemek için [özel ilke işleyicileri](xref:security/authorization/policies)yazmanız gerekir.
