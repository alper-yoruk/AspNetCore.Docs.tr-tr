---
title: Razor stránky autorizační konvence v ASP.NET Core autor: Rick-Anderson Popis: Naučte se řídit přístup ke stránkám pomocí konvencí, které autorizují uživatele a umožňují anonymním uživatelům přístup k stránkám nebo složkám stránek.
monikerRange: ' >= aspnetcore-2,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 08/12/2019 No-Loc: [Blazor, "identity", "Pojďme šifrovat", Razor, Signaler] UID: zabezpečení/autorizace/Razor-Page-Authorization
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>RazorAutorizační konvence stránek v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Jedním ze způsobů, jak řídit přístup Razor v aplikaci Pages, je použít při spuštění konvence autorizace. Tyto konvence umožňují uživatelům ověřovat a povolit anonymním uživatelům přístup k jednotlivým stránkám nebo složkám stránek. Konvence popsané v tomto tématu automaticky použijí [filtry pro autorizaci](xref:mvc/controllers/filters#authorization-filters) k řízení přístupu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace používá [ověřování souborem cookie bez IdentityASP.NET Core ](xref:security/authentication/cookie). Koncepty a příklady uvedené v tomto tématu platí stejně jako aplikace, které používají ASP.NET Core Identity. Pokud chcete použít IdentityASP.NET Core, postupujte podle pokynů <xref:security/authentication/identity>v části.

## <a name="require-authorization-to-access-a-page"></a>Vyžadovat autorizaci pro přístup ke stránce

K přidání <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> na zadané cestě použijte konvenci pomocí nástroje:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Zadaná cesta je cesta k modulu zobrazení, která je relativní cestou Razor ke kořenu stránek bez přípony a obsahuje pouze lomítka.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Lze použít na třídu modelu stránky s atributem `[Authorize]` Filter. Další informace najdete v tématu [autorizace atributu Filter](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Vyžadovat autorizaci pro přístup ke složce stránek

Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> konvenci pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> nástroje a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do všech stránek ve složce v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Zadaná cesta je cesta k modulu zobrazení, což je relativní cesta Razor ke kořeni stránek.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Vyžadovat autorizaci pro přístup na stránku oblasti

Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> konvenci pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> nástroje a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku oblasti v zadané cestě:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Název stránky je cesta k souboru bez přípony vzhledem k kořenovému adresáři stránek zadané oblasti. Například název stránky pro *oblasti souboruIdentity//Pages/Manage/accounts.cshtml* je */Manage/Accounts*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Vyžadovat autorizaci pro přístup ke složce oblastí

Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> konvenci pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> nástroje a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do všech oblastí ve složce v zadané cestě:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Cesta ke složce je cesta ke složce vzhledem k kořenovému adresáři stránek zadané oblasti. Například cesta ke složce pro soubory v *oblasti oblasti/Identity/Pages/Manage/* je */Manage*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Povolení anonymního přístupu na stránku

K přidání <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> na stránku <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> na zadané cestě použijte konvenci pomocí nástroje:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Zadaná cesta je cesta k modulu zobrazení, která je relativní cestou Razor ke kořenu stránek bez přípony a obsahuje pouze lomítka.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Povolení anonymního přístupu ke složce stránek

Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> konvenci pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> nástroje a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do všech stránek ve složce v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Zadaná cesta je cesta k modulu zobrazení, což je relativní cesta Razor ke kořeni stránek.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Poznámka o kombinování autorizovaných a anonymních přístupů

Je platný, pokud chcete určit, že složka stránek vyžaduje autorizaci, a pak určit, že stránka v této složce umožňuje anonymní přístup:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Zpětný chod ale není platný. Nemůžete deklarovat složku stránek pro anonymní přístup a pak zadat stránku v této složce, která vyžaduje autorizaci:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Požadavek na autorizaci na soukromé stránce se nezdařil. Pokud jsou <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> aplikovány na stránku, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> má přednost a řídí přístup.

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Jedním ze způsobů, jak řídit přístup Razor v aplikaci Pages, je použít při spuštění konvence autorizace. Tyto konvence umožňují uživatelům ověřovat a povolit anonymním uživatelům přístup k jednotlivým stránkám nebo složkám stránek. Konvence popsané v tomto tématu automaticky použijí [filtry pro autorizaci](xref:mvc/controllers/filters#authorization-filters) k řízení přístupu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace používá [ověřování souborem cookie bez IdentityASP.NET Core ](xref:security/authentication/cookie). Koncepty a příklady uvedené v tomto tématu platí stejně jako aplikace, které používají ASP.NET Core Identity. Pokud chcete použít IdentityASP.NET Core, postupujte podle pokynů <xref:security/authentication/identity>v části.

## <a name="require-authorization-to-access-a-page"></a>Vyžadovat autorizaci pro přístup ke stránce

K přidání <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> na zadané cestě použijte konvenci pomocí nástroje:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

Zadaná cesta je cesta k modulu zobrazení, která je relativní cestou Razor ke kořenu stránek bez přípony a obsahuje pouze lomítka.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Lze použít na třídu modelu stránky s atributem `[Authorize]` Filter. Další informace najdete v tématu [autorizace atributu Filter](xref:razor-pages/filter#authorize-filter-attribute).

## <a name="require-authorization-to-access-a-folder-of-pages"></a>Vyžadovat autorizaci pro přístup ke složce stránek

Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> konvenci pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> nástroje a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do všech stránek ve složce v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

Zadaná cesta je cesta k modulu zobrazení, což je relativní cesta Razor ke kořeni stránek.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>Vyžadovat autorizaci pro přístup na stránku oblasti

Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> konvenci pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> nástroje a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> na stránku oblasti v zadané cestě:

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

Název stránky je cesta k souboru bez přípony vzhledem k kořenovému adresáři stránek zadané oblasti. Například název stránky pro *oblasti souboruIdentity//Pages/Manage/accounts.cshtml* je */Manage/Accounts*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>Vyžadovat autorizaci pro přístup ke složce oblastí

Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> konvenci pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> nástroje a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> do všech oblastí ve složce v zadané cestě:

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

Cesta ke složce je cesta ke složce vzhledem k kořenovému adresáři stránek zadané oblasti. Například cesta ke složce pro soubory v *oblasti oblasti/Identity/Pages/Manage/* je */Manage*.

Chcete-li zadat [zásady autorizace](xref:security/authorization/policies), použijte [přetížení AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>Povolení anonymního přístupu na stránku

K přidání <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> na stránku <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> na zadané cestě použijte konvenci pomocí nástroje:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

Zadaná cesta je cesta k modulu zobrazení, která je relativní cestou Razor ke kořenu stránek bez přípony a obsahuje pouze lomítka.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>Povolení anonymního přístupu ke složce stránek

Použijte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> konvenci pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> nástroje a přidejte <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> do všech stránek ve složce v zadané cestě:

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

Zadaná cesta je cesta k modulu zobrazení, což je relativní cesta Razor ke kořeni stránek.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>Poznámka o kombinování autorizovaných a anonymních přístupů

Je platný, pokud chcete určit složku stránek, které vyžadují autorizaci, a než určit, že stránka v této složce povoluje anonymní přístup:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

Zpětný chod ale není platný. Nemůžete deklarovat složku stránek pro anonymní přístup a pak zadat stránku v této složce, která vyžaduje autorizaci:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

Požadavek na autorizaci na soukromé stránce se nezdařil. Pokud jsou <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> aplikovány na stránku, <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> má přednost a řídí přístup.

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
