---
title: ASP.NET Core için topluluk OSS kimlik doğrulama seçenekleri
author: rick-anderson
description: ASP.NET Core için açık kaynaklı kimlik doğrulama seçeneklerini bulun.
ms.author: riande
ms.date: 10/28/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/community
ms.openlocfilehash: 8f99294b6aa51eae350b3fa3f356b0b4807d0e58
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403137"
---
# <a name="community-oss-authentication-options-for-aspnet-core"></a><span data-ttu-id="1d171-103">ASP.NET Core için topluluk OSS kimlik doğrulama seçenekleri</span><span class="sxs-lookup"><span data-stu-id="1d171-103">Community OSS authentication options for ASP.NET Core</span></span>

<span data-ttu-id="1d171-104">Bu sayfa ASP.NET Core için topluluk tarafından sunulan, açık kaynaklı kimlik doğrulama seçeneklerini içerir.</span><span class="sxs-lookup"><span data-stu-id="1d171-104">This page contains community-provided, open source authentication options for ASP.NET Core.</span></span> <span data-ttu-id="1d171-105">Yeni sağlayıcılar kullanılabilir hale geldiğinde Bu sayfa düzenli olarak güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1d171-105">This page is periodically updated as new providers become available.</span></span>

## <a name="oss-authentication-providers"></a><span data-ttu-id="1d171-106">OSS kimlik doğrulama sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="1d171-106">OSS authentication providers</span></span>

<span data-ttu-id="1d171-107">Aşağıdaki liste alfabetik olarak sıralanır.</span><span class="sxs-lookup"><span data-stu-id="1d171-107">The list below is sorted alphabetically.</span></span>

| <span data-ttu-id="1d171-108">Adı</span><span class="sxs-lookup"><span data-stu-id="1d171-108">Name</span></span> | <span data-ttu-id="1d171-109">Açıklama</span><span class="sxs-lookup"><span data-stu-id="1d171-109">Description</span></span> |
| ---- | ----------- |
| [<span data-ttu-id="1d171-110">AspNet. Security. Openıdconnect. Server (ASOS)</span><span class="sxs-lookup"><span data-stu-id="1d171-110">AspNet.Security.OpenIdConnect.Server (ASOS)</span></span>](https://github.com/aspnet-contrib/AspNet.Security.OpenIdConnect.Server) | <span data-ttu-id="1d171-111">ASOS, ASP.NET Core ve OWıN/Katana için düşük düzey, protokol-ilk OpenID Connect sunucu çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="1d171-111">ASOS is a low-level, protocol-first OpenID Connect server framework for ASP.NET Core and OWIN/Katana.</span></span> |
| [<span data-ttu-id="1d171-112">Gluu sunucusu</span><span class="sxs-lookup"><span data-stu-id="1d171-112">Gluu Server</span></span>](https://gluu.org/) | <span data-ttu-id="1d171-113">Kimlik, erişim yönetimi (ıAM) ve çoklu oturum açma (SSO) için kurumsal özellikli, açık kaynaklı yazılım.</span><span class="sxs-lookup"><span data-stu-id="1d171-113">Enterprise ready, open source software for identity, access management (IAM), and single sign-on (SSO).</span></span> <span data-ttu-id="1d171-114">Daha fazla bilgi için bkz. [Gluu ürün belgeleri](https://gluu.org/docs/).</span><span class="sxs-lookup"><span data-stu-id="1d171-114">For more information, see the [Gluu Product Documentation](https://gluu.org/docs/).</span></span> |
| [<span data-ttu-id="1d171-115">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="1d171-115">IdentityServer</span></span>](https://identityserver.io/) | <span data-ttu-id="1d171-116">IdentityServer, ASP.NET Core için bir OpenID Connect ve OAuth 2,0 çerçevesidir, OpenID Foundation ve .NET Foundation yönetimi altında.</span><span class="sxs-lookup"><span data-stu-id="1d171-116">IdentityServer is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core, officially certified by the OpenID Foundation and under governance of the .NET Foundation.</span></span> <span data-ttu-id="1d171-117">Daha fazla bilgi için bkz. [ıdentityserver4 to Welcome (belgeler)](https://identityserver4.readthedocs.io/en/latest/).</span><span class="sxs-lookup"><span data-stu-id="1d171-117">For more information, see [Welcome to IdentityServer4 (Documentation)](https://identityserver4.readthedocs.io/en/latest/).</span></span> |
| [<span data-ttu-id="1d171-118">Openıddict</span><span class="sxs-lookup"><span data-stu-id="1d171-118">OpenIddict</span></span>](https://github.com/openiddict/openiddict-core) | <span data-ttu-id="1d171-119">Openıddict, ASP.NET Core için kullanımı kolay bir OpenID Connect sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="1d171-119">OpenIddict is an easy-to-use OpenID Connect server for ASP.NET Core.</span></span> |

<span data-ttu-id="1d171-120">Bir sağlayıcı eklemek için [Bu sayfayı düzenleyin](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Faspnet%2FDocs%2Fedit%2Fmaster%2Faspnetcore%2Fsecurity%2Fauthentication%2Fcommunity.md).</span><span class="sxs-lookup"><span data-stu-id="1d171-120">To add a provider, [edit this page](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Faspnet%2FDocs%2Fedit%2Fmaster%2Faspnetcore%2Fsecurity%2Fauthentication%2Fcommunity.md).</span></span>
