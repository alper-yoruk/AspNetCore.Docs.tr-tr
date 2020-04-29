---
title: Güvenli ASP.NET Core Blazor Server uygulamaları
author: guardrex
description: Sunucu uygulamalarının ASP.NET Core uygulamalar Blazor olarak nasıl güvenli hale alınacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 0021911b731e57bc6eabf857c27a13462e7400ae
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206370"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="eb26a-103">Güvenli ASP.NET Core Blazor Server uygulamaları</span><span class="sxs-lookup"><span data-stu-id="eb26a-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="eb26a-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="eb26a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="eb26a-105">Blazor sunucusu proje şablonu</span><span class="sxs-lookup"><span data-stu-id="eb26a-105">Blazor Server project template</span></span>

<span data-ttu-id="eb26a-106">Blazor sunucusu proje şablonu, proje oluşturulduğunda kimlik doğrulama için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="eb26a-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb26a-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb26a-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb26a-108">Kimlik doğrulama mekanizması ile yeni bir Blazor <xref:blazor/get-started> Server projesi oluşturmak Için makalesindeki Visual Studio kılavuzunu izleyin.</span><span class="sxs-lookup"><span data-stu-id="eb26a-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="eb26a-109">**Yeni ASP.NET Core Web uygulaması oluştur** Iletişim kutusunda **Blazor Server uygulama** şablonunu seçtikten sonra, **kimlik doğrulaması**altında **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="eb26a-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="eb26a-110">Diğer ASP.NET Core projelerine yönelik aynı kimlik doğrulama mekanizması kümesini sunmak için bir iletişim kutusu açılır:</span><span class="sxs-lookup"><span data-stu-id="eb26a-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="eb26a-111">**Kimlik Doğrulaması Yok**</span><span class="sxs-lookup"><span data-stu-id="eb26a-111">**No Authentication**</span></span>
* <span data-ttu-id="eb26a-112">**Bireysel kullanıcı hesapları** &ndash; Kullanıcı hesapları depolanabilir:</span><span class="sxs-lookup"><span data-stu-id="eb26a-112">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="eb26a-113">ASP.NET Core [kimlik](xref:security/authentication/identity) sistemini kullanarak uygulama içinde.</span><span class="sxs-lookup"><span data-stu-id="eb26a-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="eb26a-114">[Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="eb26a-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="eb26a-115">**İş veya okul hesapları**</span><span class="sxs-lookup"><span data-stu-id="eb26a-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="eb26a-116">**Windows Kimlik Doğrulaması**</span><span class="sxs-lookup"><span data-stu-id="eb26a-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eb26a-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb26a-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eb26a-118">Kimlik doğrulama mekanizması ile yeni bir <xref:blazor/get-started> Blazor Server projesi oluşturmak için makalesindeki Visual Studio Code kılavuzunu izleyin:</span><span class="sxs-lookup"><span data-stu-id="eb26a-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="eb26a-119">İzin verilen kimlik doğrulama`{AUTHENTICATION}`değerleri () aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="eb26a-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="eb26a-120">Kimlik doğrulama mekanizması</span><span class="sxs-lookup"><span data-stu-id="eb26a-120">Authentication mechanism</span></span> | <span data-ttu-id="eb26a-121">Açıklama</span><span class="sxs-lookup"><span data-stu-id="eb26a-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="eb26a-122">`None`varsayılanını</span><span class="sxs-lookup"><span data-stu-id="eb26a-122">`None` (default)</span></span>         | <span data-ttu-id="eb26a-123">Kimlik doğrulaması yok</span><span class="sxs-lookup"><span data-stu-id="eb26a-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="eb26a-124">Uygulamada ASP.NET Core kimlikle depolanan kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="eb26a-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="eb26a-125">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="eb26a-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="eb26a-126">Tek bir kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="eb26a-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="eb26a-127">Birden çok kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="eb26a-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="eb26a-128">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="eb26a-128">Windows Authentication</span></span> |

<span data-ttu-id="eb26a-129">Bu `-o|--output` seçeneği kullanarak, komut `{APP NAME}` yer tutucusu için belirtilen değeri kullanır:</span><span class="sxs-lookup"><span data-stu-id="eb26a-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="eb26a-130">Proje için bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="eb26a-130">Create a folder for the project.</span></span>
* <span data-ttu-id="eb26a-131">Projeyi adlandırın.</span><span class="sxs-lookup"><span data-stu-id="eb26a-131">Name the project.</span></span>

<span data-ttu-id="eb26a-132">Daha fazla bilgi için .NET Core kılavuzundaki [DotNet New](/dotnet/core/tools/dotnet-new) komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="eb26a-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eb26a-133">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb26a-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="eb26a-134"><xref:blazor/get-started> Makalesindeki Mac için Visual Studio kılavuzunu izleyin.</span><span class="sxs-lookup"><span data-stu-id="eb26a-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="eb26a-135">**Yeni Blazor Server uygulamanızı yapılandırın** adımında **kimlik doğrulaması** açılan listesinden **bağımsız kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="eb26a-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="eb26a-136">Uygulama, ASP.NET Core kimlik ile uygulamada depolanan bireysel kullanıcılar için oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="eb26a-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="eb26a-137">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="eb26a-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="eb26a-138">Kimlik doğrulama mekanizması ile yeni bir <xref:blazor/get-started> Blazor Server projesi oluşturmak için makalesindeki .NET Core CLI kılavuzunu izleyin:</span><span class="sxs-lookup"><span data-stu-id="eb26a-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="eb26a-139">İzin verilen kimlik doğrulama`{AUTHENTICATION}`değerleri () aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="eb26a-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="eb26a-140">Kimlik doğrulama mekanizması</span><span class="sxs-lookup"><span data-stu-id="eb26a-140">Authentication mechanism</span></span> | <span data-ttu-id="eb26a-141">Açıklama</span><span class="sxs-lookup"><span data-stu-id="eb26a-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="eb26a-142">`None`varsayılanını</span><span class="sxs-lookup"><span data-stu-id="eb26a-142">`None` (default)</span></span>         | <span data-ttu-id="eb26a-143">Kimlik doğrulaması yok</span><span class="sxs-lookup"><span data-stu-id="eb26a-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="eb26a-144">Uygulamada ASP.NET Core kimlikle depolanan kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="eb26a-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="eb26a-145">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="eb26a-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="eb26a-146">Tek bir kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="eb26a-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="eb26a-147">Birden çok kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="eb26a-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="eb26a-148">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="eb26a-148">Windows Authentication</span></span> |

<span data-ttu-id="eb26a-149">Bu `-o|--output` seçeneği kullanarak, komut `{APP NAME}` yer tutucusu için belirtilen değeri kullanır:</span><span class="sxs-lookup"><span data-stu-id="eb26a-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="eb26a-150">Proje için bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="eb26a-150">Create a folder for the project.</span></span>
* <span data-ttu-id="eb26a-151">Projeyi adlandırın.</span><span class="sxs-lookup"><span data-stu-id="eb26a-151">Name the project.</span></span>

<span data-ttu-id="eb26a-152">Daha fazla bilgi için .NET Core kılavuzundaki [DotNet New](/dotnet/core/tools/dotnet-new) komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="eb26a-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---
