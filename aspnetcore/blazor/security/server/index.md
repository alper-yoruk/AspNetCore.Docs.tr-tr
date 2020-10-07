---
title: Güvenli ASP.NET Core Blazor Server uygulamaları
author: guardrex
description: Uygulamaları ASP.NET Core uygulamalar olarak güvenli hale getirme hakkında bilgi edinin Blazor Server .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
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
uid: blazor/security/server/index
ms.openlocfilehash: d6d0f6f859dbaef98c6c8a9c53fe9858705cdc0a
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805511"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="e5b13-103">Güvenli ASP.NET Core Blazor Server uygulamaları</span><span class="sxs-lookup"><span data-stu-id="e5b13-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="e5b13-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="e5b13-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e5b13-105">Blazor Server uygulamalar, güvenlik için ASP.NET Core uygulamalarla aynı şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e5b13-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="e5b13-106">Daha fazla bilgi için, altındaki makalelere bakın <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="e5b13-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="e5b13-107">Bu genel bakışın altındaki konular özellikle için geçerlidir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="e5b13-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="e5b13-108">Blazor Server Proje şablonu</span><span class="sxs-lookup"><span data-stu-id="e5b13-108">Blazor Server project template</span></span>

<span data-ttu-id="e5b13-109">Proje Blazor Server oluşturulduğunda proje şablonu kimlik doğrulaması için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="e5b13-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e5b13-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5b13-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5b13-111"><xref:blazor/tooling>Kimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için Içindeki Visual Studio kılavuzunu izleyin Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="e5b13-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="e5b13-112">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda \*\* Blazor Server uygulama\*\* şablonunu seçtikten sonra, **kimlik doğrulaması**altında **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="e5b13-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="e5b13-113">Diğer ASP.NET Core projelerine yönelik aynı kimlik doğrulama mekanizması kümesini sunmak için bir iletişim kutusu açılır:</span><span class="sxs-lookup"><span data-stu-id="e5b13-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="e5b13-114">**Kimlik doğrulaması yok**</span><span class="sxs-lookup"><span data-stu-id="e5b13-114">**No Authentication**</span></span>
* <span data-ttu-id="e5b13-115">**Bireysel kullanıcı hesapları**: Kullanıcı hesapları depolanabilir:</span><span class="sxs-lookup"><span data-stu-id="e5b13-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="e5b13-116">ASP.NET Core sistemi kullanılarak uygulama içinde [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="e5b13-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="e5b13-117">[Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="e5b13-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="e5b13-118">**İş veya okul hesapları**</span><span class="sxs-lookup"><span data-stu-id="e5b13-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="e5b13-119">**Windows Kimlik Doğrulaması**</span><span class="sxs-lookup"><span data-stu-id="e5b13-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e5b13-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5b13-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e5b13-121"><xref:blazor/tooling>Kimlik doğrulama mekanizmasıyla yeni bir proje oluşturmak için içindeki Visual Studio Code kılavuzunu izleyin Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="e5b13-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="e5b13-122">İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e5b13-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="e5b13-123">Kimlik doğrulama mekanizması</span><span class="sxs-lookup"><span data-stu-id="e5b13-123">Authentication mechanism</span></span> | <span data-ttu-id="e5b13-124">Açıklama</span><span class="sxs-lookup"><span data-stu-id="e5b13-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="e5b13-125">`None` varsayılanını</span><span class="sxs-lookup"><span data-stu-id="e5b13-125">`None` (default)</span></span>         | <span data-ttu-id="e5b13-126">Kimlik doğrulaması yok</span><span class="sxs-lookup"><span data-stu-id="e5b13-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="e5b13-127">Uygulamada depolanan kullanıcılar ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="e5b13-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="e5b13-128">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="e5b13-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="e5b13-129">Tek bir kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="e5b13-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="e5b13-130">Birden çok kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="e5b13-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="e5b13-131">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="e5b13-131">Windows Authentication</span></span> |

<span data-ttu-id="e5b13-132">Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :</span><span class="sxs-lookup"><span data-stu-id="e5b13-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="e5b13-133">Proje için bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e5b13-133">Create a folder for the project.</span></span>
* <span data-ttu-id="e5b13-134">Projeyi adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e5b13-134">Name the project.</span></span>

<span data-ttu-id="e5b13-135">Daha fazla bilgi için [`dotnet new`](/dotnet/core/tools/dotnet-new) .NET Core kılavuzundaki komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="e5b13-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e5b13-136">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5b13-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e5b13-137">İçindeki Mac için Visual Studio kılavuzunu izleyin <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="e5b13-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="e5b13-138">**Yeni Blazor Server uygulamanızı yapılandırın** adımındaki **kimlik doğrulaması** açılan listesinden **bireysel kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="e5b13-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="e5b13-139">Uygulama, uygulamasında depolanan bireysel kullanıcılar için oluşturulur ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="e5b13-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e5b13-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e5b13-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="e5b13-141">Bir Blazor Server komut kabuğunda aşağıdaki komutu kullanarak kimlik doğrulama mekanizması ile yeni bir proje oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e5b13-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="e5b13-142">İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e5b13-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="e5b13-143">Kimlik doğrulama mekanizması</span><span class="sxs-lookup"><span data-stu-id="e5b13-143">Authentication mechanism</span></span> | <span data-ttu-id="e5b13-144">Açıklama</span><span class="sxs-lookup"><span data-stu-id="e5b13-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="e5b13-145">`None` varsayılanını</span><span class="sxs-lookup"><span data-stu-id="e5b13-145">`None` (default)</span></span>         | <span data-ttu-id="e5b13-146">Kimlik doğrulaması yok</span><span class="sxs-lookup"><span data-stu-id="e5b13-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="e5b13-147">Uygulamada depolanan kullanıcılar ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="e5b13-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="e5b13-148">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="e5b13-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="e5b13-149">Tek bir kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="e5b13-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="e5b13-150">Birden çok kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="e5b13-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="e5b13-151">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="e5b13-151">Windows Authentication</span></span> |

<span data-ttu-id="e5b13-152">Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :</span><span class="sxs-lookup"><span data-stu-id="e5b13-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="e5b13-153">Proje için bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e5b13-153">Create a folder for the project.</span></span>
* <span data-ttu-id="e5b13-154">Projeyi adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e5b13-154">Name the project.</span></span>

<span data-ttu-id="e5b13-155">Daha fazla bilgi için:</span><span class="sxs-lookup"><span data-stu-id="e5b13-155">For more information:</span></span>

* <span data-ttu-id="e5b13-156">[`dotnet new`](/dotnet/core/tools/dotnet-new).NET Core kılavuzundaki komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="e5b13-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="e5b13-157">Blazor ServerBir komut kabuğu 'nda () şablonu için Yardım komutunu yürütün `blazorserver` :</span><span class="sxs-lookup"><span data-stu-id="e5b13-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="e5b13-158">İskele Identity</span><span class="sxs-lookup"><span data-stu-id="e5b13-158">Scaffold Identity</span></span>

<span data-ttu-id="e5b13-159">IdentityBir projeye yapı iskelesi Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="e5b13-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="e5b13-160">[Mevcut yetkilendirme olmadan](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="e5b13-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="e5b13-161">[Yetkilendirme ile](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="e5b13-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e5b13-162">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e5b13-162">Additional resources</span></span>

* [<span data-ttu-id="e5b13-163">Hızlı başlangıç: Microsoft 'a ASP.NET Core Web uygulamasına oturum açma ekleme</span><span class="sxs-lookup"><span data-stu-id="e5b13-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="e5b13-164">Hızlı başlangıç: ASP.NET Core Web API 'sini Microsoft Identity platformu ile koruma</span><span class="sxs-lookup"><span data-stu-id="e5b13-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
