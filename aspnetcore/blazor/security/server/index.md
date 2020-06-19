---
title: Güvenli ASP.NET Core Blazor Server uygulamaları
author: guardrex
description: BlazorSunucu uygulamalarının ASP.NET Core uygulamalar olarak nasıl güvenli hale alınacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: a8604ca6ea60386bb3c54c950205ee695d37c689
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103792"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="49ae8-103">Güvenli ASP.NET Core Blazor Server uygulamaları</span><span class="sxs-lookup"><span data-stu-id="49ae8-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="49ae8-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="49ae8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a>Blazor<span data-ttu-id="49ae8-105">Sunucu projesi şablonu</span><span class="sxs-lookup"><span data-stu-id="49ae8-105"> Server project template</span></span>

<span data-ttu-id="49ae8-106">BlazorProje oluşturulduğunda sunucu projesi şablonu kimlik doğrulama için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="49ae8-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49ae8-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49ae8-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="49ae8-108"><xref:blazor/get-started> Blazor Kimlik doğrulama mekanizmasına sahip yeni bir sunucu projesi oluşturmak Için makalesindeki Visual Studio kılavuzunu izleyin.</span><span class="sxs-lookup"><span data-stu-id="49ae8-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="49ae8-109">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda \*\* Blazor sunucu uygulama\*\* şablonunu seçtikten sonra, **kimlik doğrulaması**altında **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="49ae8-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="49ae8-110">Diğer ASP.NET Core projelerine yönelik aynı kimlik doğrulama mekanizması kümesini sunmak için bir iletişim kutusu açılır:</span><span class="sxs-lookup"><span data-stu-id="49ae8-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="49ae8-111">**Kimlik Doğrulaması Yok**</span><span class="sxs-lookup"><span data-stu-id="49ae8-111">**No Authentication**</span></span>
* <span data-ttu-id="49ae8-112">**Bireysel kullanıcı hesapları**: Kullanıcı hesapları depolanabilir:</span><span class="sxs-lookup"><span data-stu-id="49ae8-112">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="49ae8-113">ASP.NET Core sistemi kullanılarak uygulama içinde [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="49ae8-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="49ae8-114">[Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="49ae8-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="49ae8-115">**İş veya okul hesapları**</span><span class="sxs-lookup"><span data-stu-id="49ae8-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="49ae8-116">**Windows Kimlik Doğrulaması**</span><span class="sxs-lookup"><span data-stu-id="49ae8-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="49ae8-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="49ae8-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="49ae8-118"><xref:blazor/get-started> Blazor Kimlik doğrulama mekanizmasına sahip yeni bir sunucu projesi oluşturmak için makalesindeki Visual Studio Code kılavuzunu izleyin:</span><span class="sxs-lookup"><span data-stu-id="49ae8-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="49ae8-119">İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="49ae8-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="49ae8-120">Kimlik doğrulama mekanizması</span><span class="sxs-lookup"><span data-stu-id="49ae8-120">Authentication mechanism</span></span> | <span data-ttu-id="49ae8-121">Açıklama</span><span class="sxs-lookup"><span data-stu-id="49ae8-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="49ae8-122">`None`varsayılanını</span><span class="sxs-lookup"><span data-stu-id="49ae8-122">`None` (default)</span></span>         | <span data-ttu-id="49ae8-123">Kimlik doğrulaması yok</span><span class="sxs-lookup"><span data-stu-id="49ae8-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="49ae8-124">ASP.NET Core ile uygulamada depolanan kullanıcılarIdentity</span><span class="sxs-lookup"><span data-stu-id="49ae8-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="49ae8-125">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="49ae8-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="49ae8-126">Tek bir kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="49ae8-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="49ae8-127">Birden çok kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="49ae8-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="49ae8-128">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="49ae8-128">Windows Authentication</span></span> |

<span data-ttu-id="49ae8-129">Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :</span><span class="sxs-lookup"><span data-stu-id="49ae8-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="49ae8-130">Proje için bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="49ae8-130">Create a folder for the project.</span></span>
* <span data-ttu-id="49ae8-131">Projeyi adlandırın.</span><span class="sxs-lookup"><span data-stu-id="49ae8-131">Name the project.</span></span>

<span data-ttu-id="49ae8-132">Daha fazla bilgi için .NET Core kılavuzundaki [DotNet New](/dotnet/core/tools/dotnet-new) komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="49ae8-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49ae8-133">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49ae8-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="49ae8-134">Makalesindeki Mac için Visual Studio kılavuzunu izleyin <xref:blazor/get-started> .</span><span class="sxs-lookup"><span data-stu-id="49ae8-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="49ae8-135">**Yeni Blazor sunucu uygulamanızı yapılandırın** adımında **kimlik doğrulaması** açılan listesinden **bağımsız kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="49ae8-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="49ae8-136">Uygulama, ASP.NET Core uygulamada depolanan bireysel kullanıcılar için oluşturulur Identity .</span><span class="sxs-lookup"><span data-stu-id="49ae8-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="49ae8-137">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="49ae8-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="49ae8-138"><xref:blazor/get-started> Blazor Kimlik doğrulama mekanizmasına sahip yeni bir sunucu projesi oluşturmak için makalesindeki .NET Core CLI kılavuzunu izleyin:</span><span class="sxs-lookup"><span data-stu-id="49ae8-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="49ae8-139">İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="49ae8-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="49ae8-140">Kimlik doğrulama mekanizması</span><span class="sxs-lookup"><span data-stu-id="49ae8-140">Authentication mechanism</span></span> | <span data-ttu-id="49ae8-141">Açıklama</span><span class="sxs-lookup"><span data-stu-id="49ae8-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="49ae8-142">`None`varsayılanını</span><span class="sxs-lookup"><span data-stu-id="49ae8-142">`None` (default)</span></span>         | <span data-ttu-id="49ae8-143">Kimlik doğrulaması yok</span><span class="sxs-lookup"><span data-stu-id="49ae8-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="49ae8-144">ASP.NET Core ile uygulamada depolanan kullanıcılarIdentity</span><span class="sxs-lookup"><span data-stu-id="49ae8-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="49ae8-145">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="49ae8-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="49ae8-146">Tek bir kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="49ae8-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="49ae8-147">Birden çok kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="49ae8-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="49ae8-148">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="49ae8-148">Windows Authentication</span></span> |

<span data-ttu-id="49ae8-149">Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :</span><span class="sxs-lookup"><span data-stu-id="49ae8-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="49ae8-150">Proje için bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="49ae8-150">Create a folder for the project.</span></span>
* <span data-ttu-id="49ae8-151">Projeyi adlandırın.</span><span class="sxs-lookup"><span data-stu-id="49ae8-151">Name the project.</span></span>

<span data-ttu-id="49ae8-152">Daha fazla bilgi için .NET Core kılavuzundaki [DotNet New](/dotnet/core/tools/dotnet-new) komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="49ae8-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="49ae8-153">Mevcut bir uygulamanın güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="49ae8-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="49ae8-154">Sunucu uygulamaları güvenlik için ASP.NET Core uygulamalarla aynı şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="49ae8-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="49ae8-155">Daha fazla bilgi için, altındaki makalelere bakın <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="49ae8-155">For more information, see the articles under <xref:security/index>.</span></span>

## <a name="scaffold-identity"></a><span data-ttu-id="49ae8-156">İskeleIdentity</span><span class="sxs-lookup"><span data-stu-id="49ae8-156">Scaffold Identity</span></span>

<span data-ttu-id="49ae8-157">IdentitySunucu projesine bir yapı iskelesi ekleyin Blazor :</span><span class="sxs-lookup"><span data-stu-id="49ae8-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="49ae8-158">[Mevcut yetkilendirme olmadan](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="49ae8-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="49ae8-159">[Yetkilendirme ile](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="49ae8-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>