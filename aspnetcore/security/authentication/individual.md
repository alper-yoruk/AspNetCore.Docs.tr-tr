---
title: Bireysel kullanıcı hesaplarıyla oluşturulan ASP.NET Core projelerine dayalı makaleler
author: rick-anderson
description: Bireysel kullanıcı hesaplarıyla oluşturulan ASP.NET Core projelerine göre makaleleri bulun.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 6d3743fc31c29bed5075ac29381aea51a64a908c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406257"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="873df-103">Bireysel kullanıcı hesaplarıyla oluşturulan ASP.NET Core projelerine dayalı makaleler</span><span class="sxs-lookup"><span data-stu-id="873df-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="873df-104">ASP.NET Core Identity , Visual Studio 'daki proje şablonlarına "bireysel kullanıcı hesapları" seçeneği ile dahildir.</span><span class="sxs-lookup"><span data-stu-id="873df-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="873df-105">Kimlik doğrulama şablonları ile .NET Core CLI kullanılabilir `-au Individual` :</span><span class="sxs-lookup"><span data-stu-id="873df-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="873df-106">Web API kimlik doğrulaması için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/5833) bakın.</span><span class="sxs-lookup"><span data-stu-id="873df-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="873df-107">Kimlik Doğrulaması Yok</span><span class="sxs-lookup"><span data-stu-id="873df-107">No Authentication</span></span>

<span data-ttu-id="873df-108">Kimlik doğrulaması, .NET Core CLI `-au` seçeneğiyle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="873df-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="873df-109">Visual Studio 'da **kimlik doğrulaması Değiştir** iletişim kutusu yeni Web uygulamaları için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="873df-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="873df-110">Visual Studio 'da yeni Web uygulamaları için varsayılan değer **kimlik doğrulaması**değildir.</span><span class="sxs-lookup"><span data-stu-id="873df-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="873df-111">Kimlik doğrulaması olmadan oluşturulan projeler:</span><span class="sxs-lookup"><span data-stu-id="873df-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="873df-112">Oturum açmak ve oturumu kapatmak için Web sayfalarını ve Kullanıcı arabirimini bulundurmayın.</span><span class="sxs-lookup"><span data-stu-id="873df-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="873df-113">Kimlik doğrulama kodu içermiyor.</span><span class="sxs-lookup"><span data-stu-id="873df-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="873df-114">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="873df-114">Windows Authentication</span></span>

<span data-ttu-id="873df-115">.NET Core CLI yeni Web uygulamaları için Windows kimlik doğrulaması, `-au Windows` seçeneğiyle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="873df-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="873df-116">Visual Studio 'da **kimlik doğrulaması Değiştir** Iletişim kutusu **Windows kimlik doğrulama** seçeneklerini sağlar.</span><span class="sxs-lookup"><span data-stu-id="873df-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="873df-117">Windows kimlik doğrulaması seçilirse, uygulama [Windows kimlik doğrulama IIS modülünü](xref:host-and-deploy/iis/modules)kullanacak şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="873df-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="873df-118">Windows kimlik doğrulaması, Intranet web sitelerine yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="873df-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="873df-119">DotNet yeni WebApp kimlik doğrulama seçenekleri</span><span class="sxs-lookup"><span data-stu-id="873df-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="873df-120">Aşağıdaki tabloda yeni Web uygulamaları için kullanılabilen kimlik doğrulama seçenekleri gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="873df-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="873df-121">Seçenek</span><span class="sxs-lookup"><span data-stu-id="873df-121">Option</span></span> | <span data-ttu-id="873df-122">Kimlik doğrulama türü</span><span class="sxs-lookup"><span data-stu-id="873df-122">Type of authentication</span></span> | <span data-ttu-id="873df-123">Daha fazla bilgi için bağlantı</span><span class="sxs-lookup"><span data-stu-id="873df-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="873df-124">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="873df-124">None</span></span>            |  <span data-ttu-id="873df-125">Kimlik doğrulaması yok</span><span class="sxs-lookup"><span data-stu-id="873df-125">No authentication</span></span> | | 
| <span data-ttu-id="873df-126">Ye</span><span class="sxs-lookup"><span data-stu-id="873df-126">Individual</span></span>      |  <span data-ttu-id="873df-127">Tek kimlik doğrulama</span><span class="sxs-lookup"><span data-stu-id="873df-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="873df-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="873df-128">IndividualB2C</span></span>   |  <span data-ttu-id="873df-129">Azure AD B2C ile bulutta barındırılan bireysel kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="873df-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="873df-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="873df-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="873df-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="873df-131">SingleOrg</span></span>       |  <span data-ttu-id="873df-132">Tek bir kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="873df-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="873df-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="873df-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="873df-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="873df-134">MultiOrg</span></span>        |  <span data-ttu-id="873df-135">Birden çok kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="873df-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="873df-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="873df-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="873df-137">Windows</span><span class="sxs-lookup"><span data-stu-id="873df-137">Windows</span></span>         |  <span data-ttu-id="873df-138">Windows kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="873df-138">Windows authentication</span></span> | [<span data-ttu-id="873df-139">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="873df-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="873df-140">Visual Studio yeni WebApp kimlik doğrulama seçenekleri</span><span class="sxs-lookup"><span data-stu-id="873df-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="873df-141">Aşağıdaki tabloda, Visual Studio ile yeni bir Web uygulaması oluştururken kullanılabilen kimlik doğrulama seçenekleri gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="873df-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="873df-142">Seçenek</span><span class="sxs-lookup"><span data-stu-id="873df-142">Option</span></span> | <span data-ttu-id="873df-143">Kimlik doğrulama türü</span><span class="sxs-lookup"><span data-stu-id="873df-143">Type of authentication</span></span> | <span data-ttu-id="873df-144">Daha fazla bilgi için bağlantı</span><span class="sxs-lookup"><span data-stu-id="873df-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="873df-145">Hiçbiri</span><span class="sxs-lookup"><span data-stu-id="873df-145">None</span></span>            |  <span data-ttu-id="873df-146">Kimlik doğrulaması yok</span><span class="sxs-lookup"><span data-stu-id="873df-146">No authentication</span></span> | | 
| <span data-ttu-id="873df-147">Uygulama içi bireysel kullanıcı hesapları/mağaza Kullanıcı hesapları</span><span class="sxs-lookup"><span data-stu-id="873df-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="873df-148">Tek kimlik doğrulama</span><span class="sxs-lookup"><span data-stu-id="873df-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="873df-149">Bireysel kullanıcı hesapları/buluttaki mevcut bir Kullanıcı deposuna bağlanma</span><span class="sxs-lookup"><span data-stu-id="873df-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="873df-150">Azure AD B2C ile bulutta barındırılan bireysel kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="873df-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="873df-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="873df-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="873df-152">İş veya okul bulutu/tek kuruluş</span><span class="sxs-lookup"><span data-stu-id="873df-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="873df-153">Tek bir kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="873df-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="873df-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="873df-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="873df-155">İş veya okul bulutu/birden çok kuruluş</span><span class="sxs-lookup"><span data-stu-id="873df-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="873df-156">Birden çok kiracı için kuruluş kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="873df-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="873df-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="873df-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="873df-158">Windows</span><span class="sxs-lookup"><span data-stu-id="873df-158">Windows</span></span>         |  <span data-ttu-id="873df-159">Windows kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="873df-159">Windows authentication</span></span> | [<span data-ttu-id="873df-160">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="873df-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="873df-161">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="873df-161">Additional resources</span></span>

<span data-ttu-id="873df-162">Aşağıdaki makalelerde, bireysel kullanıcı hesapları kullanan ASP.NET Core şablonlarda oluşturulan kodun nasıl kullanılacağı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="873df-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="873df-163">ASP.NET Core hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="873df-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="873df-164">Yetkilendirme ile korunan kullanıcı verileriyle ASP.NET Core uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="873df-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
