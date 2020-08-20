---
title: ASP.NET Core ile Microsoft Identity platformu ve Azure Active Directory
author: rick-anderson
description: ASP.NET Core Web uygulamaları ve API 'Ler için Microsoft Identity platform Azure Active Directory kimlik doğrulamasıyla ilgili konuları bulun.
ms.author: riande
ms.date: 01/21/2020
ms.custom: mvc, seodec18
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
uid: security/authentication/azure-active-directory/index
ms.openlocfilehash: b807681e00b196449c74dbe4240e9d6996af51fc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633090"
---
# <a name="azure-active-directory-with-aspnet-core"></a><span data-ttu-id="cfe36-103">ASP.NET Core Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="cfe36-103">Azure Active Directory with ASP.NET Core</span></span>

<span data-ttu-id="cfe36-104">Bu öğreticiler ve örnekler, Microsoft Identity platform ve Azure Active Directory kullanarak ASP.NET Core kimlik doğrulamasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="cfe36-104">These tutorials and samples demonstrate authentication in ASP.NET Core using Microsoft identity platform and Azure Active Directory.</span></span> <span data-ttu-id="cfe36-105">Azure AD ile ASP.NET Core kullanarak ek öğreticiler ve örnekler için bkz. [Microsoft Identity platform](/azure/active-directory/develop/).</span><span class="sxs-lookup"><span data-stu-id="cfe36-105">For additional tutorials and samples using ASP.NET Core with Azure AD, see [Microsoft identity platform](/azure/active-directory/develop/).</span></span>

## <a name="application-scenarios"></a><span data-ttu-id="cfe36-106">Uygulama Senaryoları</span><span class="sxs-lookup"><span data-stu-id="cfe36-106">Application Scenarios</span></span>

* [<span data-ttu-id="cfe36-107">Hızlı başlangıç: Microsoft 'a ASP.NET Core Web uygulamasına oturum açma ekleme</span><span class="sxs-lookup"><span data-stu-id="cfe36-107">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="cfe36-108">Kullanıcılar için oturum açan web uygulaması</span><span class="sxs-lookup"><span data-stu-id="cfe36-108">Web app that signs in users</span></span>](/azure/active-directory/develop/scenario-web-app-sign-user-overview?tabs=aspnetcore)
* [<span data-ttu-id="cfe36-109">Web API’lerini çağıran web uygulaması</span><span class="sxs-lookup"><span data-stu-id="cfe36-109">Web app that calls web APIs</span></span>](/azure/active-directory/develop/scenario-web-app-call-api-overview)
* [<span data-ttu-id="cfe36-110">Korumalı web API’si</span><span class="sxs-lookup"><span data-stu-id="cfe36-110">Protected web API</span></span>](/azure/active-directory/develop/scenario-protected-web-api-overview)
* [<span data-ttu-id="cfe36-111">Diğer web API’lerini çağıran web uygulaması</span><span class="sxs-lookup"><span data-stu-id="cfe36-111">Web API that calls other web APIs</span></span>](/azure/active-directory/develop/scenario-web-api-call-api-overview)
* [<span data-ttu-id="cfe36-112">Azure AD B2C kullanıcılara oturum açan Web uygulaması</span><span class="sxs-lookup"><span data-stu-id="cfe36-112">Web app that signs in users with Azure AD B2C</span></span>](xref:security/authentication/azure-ad-b2c)

## <a name="samples"></a><span data-ttu-id="cfe36-113">Örnekler</span><span class="sxs-lookup"><span data-stu-id="cfe36-113">Samples</span></span>

* <span data-ttu-id="cfe36-114">[ASP.NET Core uygulamanızı Azure AD v2 'yi kullanarak kullanıcıların oturumunu açma ve Web API 'lerini çağırma konusunda etkinleştirin](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span><span class="sxs-lookup"><span data-stu-id="cfe36-114">[Enable your ASP.NET Core app to sign-in users and call web APIs using Azure AD V2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span></span> 
  * <span data-ttu-id="cfe36-115">[Bu ilişkili videoya](https://channel9.msdn.com/Events/Build/2018/THR5001) bakın</span><span class="sxs-lookup"><span data-stu-id="cfe36-115">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5001)</span></span>

* <span data-ttu-id="cfe36-116">[Azure AD v2 kullanarak BIR WPF uygulamasından ASP.NET Core 2,0 Web API 'Sini çağırma](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span><span class="sxs-lookup"><span data-stu-id="cfe36-116">[Calling an ASP.NET Core 2.0 Web API from a WPF application using Azure AD V2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span></span> 
  * <span data-ttu-id="cfe36-117">[Bu ilişkili videoya](https://channel9.msdn.com/Events/Build/2018/THR5000) bakın</span><span class="sxs-lookup"><span data-stu-id="cfe36-117">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5000)</span></span>

* [<span data-ttu-id="cfe36-118">Azure AD B2C bir ASP.NET Core Web API 'SI</span><span class="sxs-lookup"><span data-stu-id="cfe36-118">An ASP.NET Core web API with Azure AD B2C</span></span>](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/)
