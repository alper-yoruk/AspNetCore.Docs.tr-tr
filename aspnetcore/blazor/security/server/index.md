---
title: Güvenli ASP.NET Core Blazor Server uygulamaları
author: guardrex
description: Uygulamaları ASP.NET Core uygulamalar olarak güvenli hale getirme hakkında bilgi edinin Blazor Server .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: ab3baad30f78c5d5e2f969b3292d4886fcd0406d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402318"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="30984-103">Güvenli ASP.NET Core Blazor Server uygulamaları</span><span class="sxs-lookup"><span data-stu-id="30984-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="30984-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="30984-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor Server<span data-ttu-id="30984-105">uygulamalar, güvenlik için ASP.NET Core uygulamalarla aynı şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="30984-105"> apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="30984-106">Daha fazla bilgi için, altındaki makalelere bakın <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="30984-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="30984-107">Bu genel bakışın altındaki konular özellikle için geçerlidir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="30984-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="blazor-server-project-template"></a>Blazor Server<span data-ttu-id="30984-108">Proje şablonu</span><span class="sxs-lookup"><span data-stu-id="30984-108"> project template</span></span>

<span data-ttu-id="30984-109">Proje Blazor Server oluşturulduğunda proje şablonu kimlik doğrulaması için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="30984-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30984-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30984-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30984-111"><xref:blazor/get-started>Kimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için makalesindeki Visual Studio kılavuzunu izleyin Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="30984-111">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="30984-112">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda \*\* Blazor Server uygulama\*\* şablonunu seçtikten sonra, **kimlik doğrulaması**altında **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="30984-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="30984-113">Diğer ASP.NET Core projelerine yönelik aynı kimlik doğrulama mekanizması kümesini sunmak için bir iletişim kutusu açılır:</span><span class="sxs-lookup"><span data-stu-id="30984-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="30984-114">**Kimlik Doğrulaması Yok**</span><span class="sxs-lookup"><span data-stu-id="30984-114">**No Authentication**</span></span>
* <span data-ttu-id="30984-115">**Bireysel kullanıcı hesapları**: Kullanıcı hesapları depolanabilir:</span><span class="sxs-lookup"><span data-stu-id="30984-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="30984-116">ASP.NET Core sistemi kullanılarak uygulama içinde [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="30984-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="30984-117">[Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="30984-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="30984-118">**İş veya okul hesapları**</span><span class="sxs-lookup"><span data-stu-id="30984-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="30984-119">**Windows Kimlik Doğrulaması**</span><span class="sxs-lookup"><span data-stu-id="30984-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="30984-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="30984-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="30984-121"><xref:blazor/get-started>Kimlik doğrulama mekanizmasıyla yeni bir proje oluşturmak için makalesindeki Visual Studio Code kılavuzunu izleyin Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="30984-121">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="30984-122">İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="30984-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="30984-123">Kimlik doğrulama mekanizması</span><span class="sxs-lookup"><span data-stu-id="30984-123">Authentication mechanism</span></span> | <span data-ttu-id="30984-124">Açıklama</span><span class="sxs-lookup"><span data-stu-id="30984-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="30984-125">`None`varsayılanını</span><span class="sxs-lookup"><span data-stu-id="30984-125">`None` (default)</span></span>         | <span data-ttu-id="30984-126">Kimlik doğrulaması yok</span><span class="sxs-lookup"><span data-stu-id="30984-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="30984-127">ASP.NET Core ile uygulamada depolanan kullanıcılarIdentity</span><span class="sxs-lookup"><span data-stu-id="30984-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="30984-128">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="30984-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="30984-129">Tek bir kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="30984-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="30984-130">Birden çok kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="30984-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="30984-131">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="30984-131">Windows Authentication</span></span> |

<span data-ttu-id="30984-132">Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :</span><span class="sxs-lookup"><span data-stu-id="30984-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="30984-133">Proje için bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="30984-133">Create a folder for the project.</span></span>
* <span data-ttu-id="30984-134">Projeyi adlandırın.</span><span class="sxs-lookup"><span data-stu-id="30984-134">Name the project.</span></span>

<span data-ttu-id="30984-135">Daha fazla bilgi için [`dotnet new`](/dotnet/core/tools/dotnet-new) .NET Core kılavuzundaki komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="30984-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="30984-136">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30984-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="30984-137">Makalesindeki Mac için Visual Studio kılavuzunu izleyin <xref:blazor/get-started> .</span><span class="sxs-lookup"><span data-stu-id="30984-137">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="30984-138">**Yeni Blazor Server uygulamanızı yapılandırın** adımındaki **kimlik doğrulaması** açılan listesinden **bireysel kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="30984-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="30984-139">Uygulama, ASP.NET Core uygulamada depolanan bireysel kullanıcılar için oluşturulur Identity .</span><span class="sxs-lookup"><span data-stu-id="30984-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="30984-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="30984-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="30984-141"><xref:blazor/get-started>Kimlik doğrulama mekanizmasıyla yeni bir proje oluşturmak için makalesindeki .NET Core CLI kılavuzunu izleyin Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="30984-141">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="30984-142">İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="30984-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="30984-143">Kimlik doğrulama mekanizması</span><span class="sxs-lookup"><span data-stu-id="30984-143">Authentication mechanism</span></span> | <span data-ttu-id="30984-144">Açıklama</span><span class="sxs-lookup"><span data-stu-id="30984-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="30984-145">`None`varsayılanını</span><span class="sxs-lookup"><span data-stu-id="30984-145">`None` (default)</span></span>         | <span data-ttu-id="30984-146">Kimlik doğrulaması yok</span><span class="sxs-lookup"><span data-stu-id="30984-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="30984-147">ASP.NET Core ile uygulamada depolanan kullanıcılarIdentity</span><span class="sxs-lookup"><span data-stu-id="30984-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="30984-148">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="30984-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="30984-149">Tek bir kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="30984-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="30984-150">Birden çok kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="30984-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="30984-151">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="30984-151">Windows Authentication</span></span> |

<span data-ttu-id="30984-152">Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :</span><span class="sxs-lookup"><span data-stu-id="30984-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="30984-153">Proje için bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="30984-153">Create a folder for the project.</span></span>
* <span data-ttu-id="30984-154">Projeyi adlandırın.</span><span class="sxs-lookup"><span data-stu-id="30984-154">Name the project.</span></span>

<span data-ttu-id="30984-155">Daha fazla bilgi için [`dotnet new`](/dotnet/core/tools/dotnet-new) .NET Core kılavuzundaki komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="30984-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-identity"></a><span data-ttu-id="30984-156">İskeleIdentity</span><span class="sxs-lookup"><span data-stu-id="30984-156">Scaffold Identity</span></span>

<span data-ttu-id="30984-157">IdentityBir projeye yapı iskelesi Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="30984-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="30984-158">[Mevcut yetkilendirme olmadan](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="30984-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="30984-159">[Yetkilendirme ile](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="30984-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
