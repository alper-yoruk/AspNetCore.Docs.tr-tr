---
title: RazorASP.NET Core için sayfa yetkilendirme kuralları
author: rick-anderson
description: Kullanıcılara yetki veren ve anonim kullanıcıların sayfalara veya sayfa klasörlerine erişmesine izin veren kurallara sahip sayfalara erişimi denetleme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 5e0c76ea6c6b4af8e24693b1bfe8ac28bf2ce3a9
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022283"
---
# <a name="no-locrazor-pages-authorization-conventions-in-aspnet-core"></a>RazorASP.NET Core için sayfa yetkilendirme kuralları

::: moniker range=">= aspnetcore-3.0"

Sayfalar uygulamanızda erişimi denetlemeye yönelik bir yol Razor , başlangıçta yetkilendirme kurallarını kullanmaktır. Bu kurallar, kullanıcıları yetkilendirmeniz ve anonim kullanıcıların ayrı sayfalara veya sayfa klasörlerine erişmesine izin verir. Bu konu başlığı altında açıklanan kurallar, erişimi denetlemek için otomatik olarak [Yetkilendirme filtreleri](xref:mvc/controllers/filters#authorization-filters) uygular.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek uygulama [ cookie ASP.NET Core Identity olmadan kimlik doğrulaması ](xref:security/authentication/cookie)kullanır. Bu konu başlığında gösterilen kavramlar ve örnekler, ASP.NET Core kullanan uygulamalar için eşit oranda geçerlidir Identity . ASP.NET Core kullanmak için Identity içindeki yönergeleri izleyin <xref:security/authentication/identity> .

## <a name="require-authorization-to-access-a-page"></a>Bir sayfaya erişmek için yetkilendirme gerektir

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*>Belirtilen yoldaki sayfaya bir eklemek için kuralı kullanın <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> :

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

Belirtilen yol, Razor uzantısı olmayan ve yalnızca eğik çizgi içeren sayfa kök göreli yolu olan görünüm altyapısı yoludur.

Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek Için bir [authorizepage aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)kullanın:

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Filtre özniteliğiyle bir sayfa modeli sınıfına uygulanabilir `[Authorize]` . Daha fazla bilgi için bkz. [Yetkilendirme filtre özniteliği](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Bir sayfa klasörüne erişmek için yetkilendirme gerektir

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Belirtilen yoldaki bir klasördeki tüm sayfalara bir eklemek için kuralını kullanın:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

Belirtilen yol, Razor sayfa kök göreli yolu olan görünüm altyapısı yoludur.

Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek için, bir [authorizefolder aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)kullanın:

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Alan sayfasına erişmek için yetkilendirme gerektir

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Belirtilen yoldaki alan sayfasına eklemek için kuralını kullanın:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Sayfa adı, belirtilen alanın sayfalar kök dizinine göre uzantısı olmayan dosyanın yoludur. Örneğin, dosya *alanı/ Identity /Pages/Manage/accounts.exe* için sayfa adı */Manage/accounts*olur.

Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek için, bir [Authorizeareapage aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)kullanın:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Bir alan klasörüne erişmek için yetkilendirme gerektir

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Belirtilen yoldaki bir klasördeki tüm alanlara bir eklemek için kuralını kullanın:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Klasör yolu, belirtilen alanın sayfalar kök dizinine göre klasörün yoludur. Örneğin, *Areas/ Identity /Pages/Manage/* altındaki dosyalar için klasör yolu/ *Yönet*' dir.

Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek için, bir [Authorizeareafolder aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)kullanın:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Bir sayfaya anonim erişime izin ver

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*>Belirtilen yoldaki bir sayfaya bir eklemek için kuralı kullanın <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> :

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

Belirtilen yol, Razor uzantısı olmayan ve yalnızca eğik çizgi içeren sayfa kök göreli yolu olan görünüm altyapısı yoludur.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Bir sayfa klasörüne anonim erişime izin ver

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Belirtilen yoldaki bir klasördeki tüm sayfalara bir eklemek için kuralını kullanın:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

Belirtilen yol, Razor sayfa kök göreli yolu olan görünüm altyapısı yoludur.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Yetkili ve anonim erişimi birleştirme hakkında

Bir sayfa klasörünün yetkilendirme gerektirdiğini ve sonra bu klasörün içindeki bir sayfanın adsız erişime izin verdiğini belirtmek için geçerlidir:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Ancak, tersi de geçerlidir. Anonim erişim için bir sayfa klasörü bildiremezsiniz ve ardından bu klasör içinde yetkilendirme gerektiren bir sayfa belirtemezsiniz:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Özel sayfada yetkilendirme gerektirme başarısız olur. Ve sayfaya her ikisi de <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> uygulandığında,, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> öncelik alır ve erişimi denetler.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Sayfalar uygulamanızda erişimi denetlemeye yönelik bir yol Razor , başlangıçta yetkilendirme kurallarını kullanmaktır. Bu kurallar, kullanıcıları yetkilendirmeniz ve anonim kullanıcıların ayrı sayfalara veya sayfa klasörlerine erişmesine izin verir. Bu konu başlığı altında açıklanan kurallar, erişimi denetlemek için otomatik olarak [Yetkilendirme filtreleri](xref:mvc/controllers/filters#authorization-filters) uygular.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek uygulama [ cookie ASP.NET Core Identity olmadan kimlik doğrulaması ](xref:security/authentication/cookie)kullanır. Bu konu başlığında gösterilen kavramlar ve örnekler, ASP.NET Core kullanan uygulamalar için eşit oranda geçerlidir Identity . ASP.NET Core kullanmak için Identity içindeki yönergeleri izleyin <xref:security/authentication/identity> .

## <a name="require-authorization-to-access-a-page"></a>Bir sayfaya erişmek için yetkilendirme gerektir

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Belirtilen yoldaki sayfaya bir eklemek için aracılığıyla kuralını kullanın <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> :

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Belirtilen yol, Razor uzantısı olmayan ve yalnızca eğik çizgi içeren sayfa kök göreli yolu olan görünüm altyapısı yoludur.

Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek Için bir [authorizepage aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)kullanın:

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>Filtre özniteliğiyle bir sayfa modeli sınıfına uygulanabilir `[Authorize]` . Daha fazla bilgi için bkz. [Yetkilendirme filtre özniteliği](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Bir sayfa klasörüne erişmek için yetkilendirme gerektir

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Belirtilen yoldaki bir klasördeki tüm sayfalara eklemek için aracılığıyla kuralını kullanın:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Belirtilen yol, Razor sayfa kök göreli yolu olan görünüm altyapısı yoludur.

Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek için, bir [authorizefolder aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)kullanın:

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Alan sayfasına erişmek için yetkilendirme gerektir

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Belirtilen yoldaki alan sayfasına eklemek için aracılığıyla kuralını kullanın <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> :

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Sayfa adı, belirtilen alanın sayfalar kök dizinine göre uzantısı olmayan dosyanın yoludur. Örneğin, dosya *alanı/ Identity /Pages/Manage/accounts.exe* için sayfa adı */Manage/accounts*olur.

Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek için, bir [Authorizeareapage aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)kullanın:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Bir alan klasörüne erişmek için yetkilendirme gerektir

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Belirtilen yoldaki bir klasördeki tüm alanlara bir eklemek için aracılığıyla kuralını kullanın:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Klasör yolu, belirtilen alanın sayfalar kök dizinine göre klasörün yoludur. Örneğin, *Areas/ Identity /Pages/Manage/* altındaki dosyalar için klasör yolu/ *Yönet*' dir.

Bir [Yetkilendirme İlkesi](xref:security/authorization/policies)belirtmek için, bir [Authorizeareafolder aşırı yüklemesi](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)kullanın:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Bir sayfaya anonim erişime izin ver

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Belirtilen yoldaki bir sayfaya bir eklemek için aracılığıyla kuralını kullanın <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> :

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Belirtilen yol, Razor uzantısı olmayan ve yalnızca eğik çizgi içeren sayfa kök göreli yolu olan görünüm altyapısı yoludur.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Bir sayfa klasörüne anonim erişime izin ver

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Belirtilen yoldaki bir klasördeki tüm sayfalara eklemek için aracılığıyla kuralını kullanın:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Belirtilen yol, Razor sayfa kök göreli yolu olan görünüm altyapısı yoludur.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Yetkili ve anonim erişimi birleştirme hakkında

Yetkilendirme gerektiren bir sayfa klasörünün ve bu klasörün içindeki bir sayfanın adsız erişime izin verdiğini belirtmek için geçerlidir:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Ancak, tersi de geçerlidir. Anonim erişim için bir sayfa klasörü bildiremezsiniz ve ardından bu klasör içinde yetkilendirme gerektiren bir sayfa belirtemezsiniz:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Özel sayfada yetkilendirme gerektirme başarısız olur. Ve sayfaya her ikisi de <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> uygulandığında,, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> öncelik alır ve erişimi denetler.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
