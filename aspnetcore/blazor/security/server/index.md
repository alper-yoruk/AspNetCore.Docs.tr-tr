---
title: Güvenli ASP.NET Core Blazor Server uygulamaları
author: guardrex
description: Uygulamaları ASP.NET Core uygulamalar olarak güvenli hale getirme hakkında bilgi edinin Blazor Server .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 5031273c3395be4365b3a6d239ebce7aaf9b66ac
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658644"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="43517-103">Güvenli ASP.NET Core Blazor Server uygulamaları</span><span class="sxs-lookup"><span data-stu-id="43517-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="43517-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="43517-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="43517-105">Blazor Server uygulamalar, güvenlik için ASP.NET Core uygulamalarla aynı şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="43517-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="43517-106">Daha fazla bilgi için, altındaki makalelere bakın <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="43517-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="43517-107">Bu genel bakışın altındaki konular özellikle için geçerlidir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="43517-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="43517-108">Blazor Server Proje şablonu</span><span class="sxs-lookup"><span data-stu-id="43517-108">Blazor Server project template</span></span>

<span data-ttu-id="43517-109">Proje Blazor Server oluşturulduğunda proje şablonu kimlik doğrulaması için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="43517-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="43517-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43517-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="43517-111"><xref:blazor/tooling>Kimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için Içindeki Visual Studio kılavuzunu izleyin Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="43517-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="43517-112">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda **Blazor Server uygulama** şablonunu seçtikten sonra, **kimlik doğrulaması** altında **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="43517-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="43517-113">Diğer ASP.NET Core projelerine yönelik aynı kimlik doğrulama mekanizması kümesini sunmak için bir iletişim kutusu açılır:</span><span class="sxs-lookup"><span data-stu-id="43517-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="43517-114">**Kimlik doğrulaması yok**</span><span class="sxs-lookup"><span data-stu-id="43517-114">**No Authentication**</span></span>
* <span data-ttu-id="43517-115">**Bireysel kullanıcı hesapları**: Kullanıcı hesapları depolanabilir:</span><span class="sxs-lookup"><span data-stu-id="43517-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="43517-116">ASP.NET Core sistemi kullanılarak uygulama içinde [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="43517-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="43517-117">[Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="43517-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="43517-118">**İş veya okul hesapları**</span><span class="sxs-lookup"><span data-stu-id="43517-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="43517-119">**Windows Kimlik Doğrulaması**</span><span class="sxs-lookup"><span data-stu-id="43517-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="43517-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43517-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="43517-121"><xref:blazor/tooling>Kimlik doğrulama mekanizmasıyla yeni bir proje oluşturmak için içindeki Visual Studio Code kılavuzunu izleyin Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="43517-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="43517-122">İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="43517-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="43517-123">Kimlik doğrulama mekanizması</span><span class="sxs-lookup"><span data-stu-id="43517-123">Authentication mechanism</span></span> | <span data-ttu-id="43517-124">Description</span><span class="sxs-lookup"><span data-stu-id="43517-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="43517-125">`None` varsayılanını</span><span class="sxs-lookup"><span data-stu-id="43517-125">`None` (default)</span></span>         | <span data-ttu-id="43517-126">Kimlik doğrulaması yok</span><span class="sxs-lookup"><span data-stu-id="43517-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="43517-127">Uygulamada depolanan kullanıcılar ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="43517-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="43517-128">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="43517-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="43517-129">Tek bir kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="43517-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="43517-130">Birden çok kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="43517-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="43517-131">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="43517-131">Windows Authentication</span></span> |

<span data-ttu-id="43517-132">Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :</span><span class="sxs-lookup"><span data-stu-id="43517-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="43517-133">Proje için bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="43517-133">Create a folder for the project.</span></span>
* <span data-ttu-id="43517-134">Projeyi adlandırın.</span><span class="sxs-lookup"><span data-stu-id="43517-134">Name the project.</span></span>

<span data-ttu-id="43517-135">Daha fazla bilgi için [`dotnet new`](/dotnet/core/tools/dotnet-new) .NET Core kılavuzundaki komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="43517-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="43517-136">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43517-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="43517-137">İçindeki Mac için Visual Studio kılavuzunu izleyin <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="43517-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="43517-138">**Yeni Blazor Server uygulamanızı yapılandırın** adımındaki **kimlik doğrulaması** açılan listesinden **bireysel kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="43517-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="43517-139">Uygulama, uygulamasında depolanan bireysel kullanıcılar için oluşturulur ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="43517-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="43517-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="43517-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="43517-141">Bir Blazor Server komut kabuğunda aşağıdaki komutu kullanarak kimlik doğrulama mekanizması ile yeni bir proje oluşturun:</span><span class="sxs-lookup"><span data-stu-id="43517-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="43517-142">İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="43517-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="43517-143">Kimlik doğrulama mekanizması</span><span class="sxs-lookup"><span data-stu-id="43517-143">Authentication mechanism</span></span> | <span data-ttu-id="43517-144">Description</span><span class="sxs-lookup"><span data-stu-id="43517-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="43517-145">`None` varsayılanını</span><span class="sxs-lookup"><span data-stu-id="43517-145">`None` (default)</span></span>         | <span data-ttu-id="43517-146">Kimlik doğrulaması yok</span><span class="sxs-lookup"><span data-stu-id="43517-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="43517-147">Uygulamada depolanan kullanıcılar ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="43517-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="43517-148">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="43517-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="43517-149">Tek bir kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="43517-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="43517-150">Birden çok kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="43517-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="43517-151">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="43517-151">Windows Authentication</span></span> |

<span data-ttu-id="43517-152">Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :</span><span class="sxs-lookup"><span data-stu-id="43517-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="43517-153">Proje için bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="43517-153">Create a folder for the project.</span></span>
* <span data-ttu-id="43517-154">Projeyi adlandırın.</span><span class="sxs-lookup"><span data-stu-id="43517-154">Name the project.</span></span>

<span data-ttu-id="43517-155">Daha fazla bilgi için:</span><span class="sxs-lookup"><span data-stu-id="43517-155">For more information:</span></span>

* <span data-ttu-id="43517-156">[`dotnet new`](/dotnet/core/tools/dotnet-new).NET Core kılavuzundaki komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="43517-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="43517-157">Blazor ServerBir komut kabuğu 'nda () şablonu için Yardım komutunu yürütün `blazorserver` :</span><span class="sxs-lookup"><span data-stu-id="43517-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="43517-158">İskele Identity</span><span class="sxs-lookup"><span data-stu-id="43517-158">Scaffold Identity</span></span>

<span data-ttu-id="43517-159">IdentityBir projeye yapı iskelesi Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="43517-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="43517-160">[Mevcut yetkilendirme olmadan](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="43517-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="43517-161">[Yetkilendirme ile](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="43517-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="azure-app-service-on-linux-with-no-locidentity-server"></a><span data-ttu-id="43517-162">Sunucu ile Linux üzerinde Azure App Service Identity</span><span class="sxs-lookup"><span data-stu-id="43517-162">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="43517-163">Sunucu ile Linux üzerinde Azure App Service dağıtım sırasında sertifikayı vereni açıkça belirtin Identity .</span><span class="sxs-lookup"><span data-stu-id="43517-163">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="43517-164">Daha fazla bilgi için bkz. <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="43517-164">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43517-165">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="43517-165">Additional resources</span></span>

* [<span data-ttu-id="43517-166">Hızlı başlangıç: Microsoft 'a ASP.NET Core Web uygulamasına oturum açma ekleme</span><span class="sxs-lookup"><span data-stu-id="43517-166">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="43517-167">Hızlı başlangıç: ASP.NET Core Web API 'sini Microsoft Identity platformu ile koruma</span><span class="sxs-lookup"><span data-stu-id="43517-167">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="43517-168"><xref:host-and-deploy/proxy-load-balancer>: Üzerine yönergeler içerir:</span><span class="sxs-lookup"><span data-stu-id="43517-168"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="43517-169">WAN sunucularında ve iç ağlarda HTTPS şema bilgilerini korumak için Iletilen üstbilgiler ara yazılımı kullanma.</span><span class="sxs-lookup"><span data-stu-id="43517-169">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="43517-170">El ile düzen yapılandırması, doğru istek yönlendirme için istek yolu değişiklikleri ve Linux ve IIS olmayan ters proxy 'ler için istek düzenini iletme dahil ek senaryolar ve kullanım örnekleri.</span><span class="sxs-lookup"><span data-stu-id="43517-170">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
