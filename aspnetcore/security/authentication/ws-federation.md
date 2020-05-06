---
title: ASP.NET Core 'de WS-Federation ile kullanıcıların kimliğini doğrulama
author: chlowell
description: Bu öğreticide, ASP.NET Core uygulamasında WS-Federation kullanımı gösterilmektedir.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/ws-federation
ms.openlocfilehash: fede3887ad7dacd40cf3bb5d1b785392a9bc1480
ms.sourcegitcommit: 4a9321db7ca4e69074fa08a678dcc91e16215b1e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82850467"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="b0212-103">ASP.NET Core 'de WS-Federation ile kullanıcıların kimliğini doğrulama</span><span class="sxs-lookup"><span data-stu-id="b0212-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="b0212-104">Bu öğreticide, kullanıcıların Active Directory Federasyon Hizmetleri (AD FS) (ADFS) veya [Azure Active Directory](/azure/active-directory/) (AAD) gıbı bir WS-Federasyon kimlik doğrulama sağlayıcısıyla oturum açmasını sağlama gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="b0212-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="b0212-105">[Facebook, Google ve dış sağlayıcı kimlik doğrulamasında](xref:security/authentication/social/index)açıklanan ASP.NET Core örnek uygulamasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="b0212-105">It uses the ASP.NET Core sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="b0212-106">ASP.NET Core uygulamalar için, [Microsoft. AspNetCore. Authentication. WSFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)tarafından WS-Federation desteği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="b0212-106">For ASP.NET Core apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="b0212-107">Bu bileşen [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) 'tan ve bu bileşenin mekanizması 'nın birçoğunu paylaşır.</span><span class="sxs-lookup"><span data-stu-id="b0212-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="b0212-108">Ancak, bileşenler birkaç önemli şekilde farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="b0212-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="b0212-109">Varsayılan olarak, yeni ara yazılım:</span><span class="sxs-lookup"><span data-stu-id="b0212-109">By default, the new middleware:</span></span>

* <span data-ttu-id="b0212-110">, İstenmeyen oturum açma işlemlerine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="b0212-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="b0212-111">WS-Federation protokolünün bu özelliği XSRF saldırılarına karşı savunmasızdır.</span><span class="sxs-lookup"><span data-stu-id="b0212-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="b0212-112">Ancak, `AllowUnsolicitedLogins` seçeneğiyle etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b0212-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="b0212-113">Oturum açma iletileri için her form gönderisini denetlemez.</span><span class="sxs-lookup"><span data-stu-id="b0212-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="b0212-114">Yalnızca öğesine yapılan istekler `CallbackPath` , oturum açma işlemleri için denetlenir. `CallbackPath` varsayılan olarak `/signin-wsfed` , ancak [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) sınıfının devralınmış [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği aracılığıyla değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b0212-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="b0212-115">Bu yol, [Skipunrecognizedrequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) seçeneği etkinleştirilerek diğer kimlik doğrulama sağlayıcılarıyla paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="b0212-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="b0212-116">Uygulamayı Active Directory kaydetme</span><span class="sxs-lookup"><span data-stu-id="b0212-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="b0212-117">Active Directory Federasyon Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="b0212-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="b0212-118">ADFS yönetim konsolundan sunucunun **bağlı olan taraf güveni Ekleme Sihirbazı 'nı** açın:</span><span class="sxs-lookup"><span data-stu-id="b0212-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Bağlı olan taraf güveni Ekleme Sihirbazı: hoş geldiniz](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="b0212-120">Verileri el ile girmeyi seçin:</span><span class="sxs-lookup"><span data-stu-id="b0212-120">Choose to enter data manually:</span></span>

![Bağlı olan taraf güveni Ekleme Sihirbazı: veri kaynağını seçin](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="b0212-122">Bağlı olan taraf için bir görünen ad girin.</span><span class="sxs-lookup"><span data-stu-id="b0212-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="b0212-123">Ad ASP.NET Core uygulaması için önemli değildir.</span><span class="sxs-lookup"><span data-stu-id="b0212-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="b0212-124">[Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) , belirteç şifreleme desteği yok, bu nedenle bir belirteç şifreleme sertifikası yapılandırmayın:</span><span class="sxs-lookup"><span data-stu-id="b0212-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Bağlı olan taraf güveni Ekleme Sihirbazı: sertifikayı yapılandırma](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="b0212-126">Uygulamanın URL 'sini kullanarak WS-Federation pasif Protokolü desteğini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="b0212-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="b0212-127">Uygulamanın bağlantı noktasının doğru olduğundan emin olun:</span><span class="sxs-lookup"><span data-stu-id="b0212-127">Verify the port is correct for the app:</span></span>

![Bağlı olan taraf güveni Ekleme Sihirbazı: URL 'YI yapılandırma](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="b0212-129">Bu bir HTTPS URL 'SI olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b0212-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="b0212-130">IIS Express, geliştirme sırasında uygulamayı barındırırken otomatik olarak imzalanan bir sertifika sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="b0212-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="b0212-131">Kestrel el ile sertifika yapılandırması gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b0212-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="b0212-132">Daha fazla bilgi için [Kestrel belgelerine](xref:fundamentals/servers/kestrel) bakın.</span><span class="sxs-lookup"><span data-stu-id="b0212-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="b0212-133">Sihirbazın geri kalanında **İleri** ' ye tıklayın ve sonda da **kapatın** .</span><span class="sxs-lookup"><span data-stu-id="b0212-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="b0212-134">ASP.NET Core Identity BIR **ad kimliği** talebi gerektiriyor.</span><span class="sxs-lookup"><span data-stu-id="b0212-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="b0212-135">**Talep kurallarını Düzenle** iletişim kutusundan bir tane ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b0212-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Talep kurallarını Düzenle](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="b0212-137">**Dönüşüm talebi kuralı Ekle sihirbazında**, varsayılan **LDAP özniteliklerini talep şablonu olarak gönder** ' i seçili bırakın ve **İleri**' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b0212-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="b0212-138">**SAM-Account-Name** LDAP ÖZNITELIĞINI **ad kimliği** giden talebine eşleyen bir kural ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b0212-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Dönüşüm talep kuralı ekleme Sihirbazı: talep kuralını yapılandırma](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="b0212-140"> >  **Talep kurallarını Düzenle** penceresinde**Tamam** **' a**tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b0212-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="b0212-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="b0212-141">Azure Active Directory</span></span>

* <span data-ttu-id="b0212-142">AAD kiracının uygulama kayıtları dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="b0212-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="b0212-143">**Yeni uygulama kaydı**' na tıklayın:</span><span class="sxs-lookup"><span data-stu-id="b0212-143">Click **New application registration**:</span></span>

![Azure Active Directory: Uygulama kayıtları](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="b0212-145">Uygulama kaydı için bir ad girin.</span><span class="sxs-lookup"><span data-stu-id="b0212-145">Enter a name for the app registration.</span></span> <span data-ttu-id="b0212-146">Bu, ASP.NET Core uygulaması için önemli değildir.</span><span class="sxs-lookup"><span data-stu-id="b0212-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="b0212-147">Uygulamanın **oturum açma URL 'si**olarak dinlediği URL 'yi girin:</span><span class="sxs-lookup"><span data-stu-id="b0212-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: uygulama kaydı oluşturma](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="b0212-149">**Uç noktalar** ' a tıklayın ve **Federasyon meta veri belgesi** URL 'sine göz önünde</span><span class="sxs-lookup"><span data-stu-id="b0212-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="b0212-150">Bu, WS-Federation ara yazılımı ' `MetadataAddress`dır:</span><span class="sxs-lookup"><span data-stu-id="b0212-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: uç noktalar](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="b0212-152">Yeni uygulama kaydına gidin.</span><span class="sxs-lookup"><span data-stu-id="b0212-152">Navigate to the new app registration.</span></span> <span data-ttu-id="b0212-153">**Ayarlar** > **Özellikler** ' e tıklayın ve **uygulama kimliği URI**'sini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="b0212-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="b0212-154">Bu, WS-Federation ara yazılımı ' `Wtrealm`dır:</span><span class="sxs-lookup"><span data-stu-id="b0212-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: uygulama kaydı özellikleri](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="b0212-156">ASP.NET Core olmadan WS-Federation kullanınIdentity</span><span class="sxs-lookup"><span data-stu-id="b0212-156">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="b0212-157">WS-Federation ara yazılımı, olmadan Identitykullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b0212-157">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="b0212-158">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b0212-158">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="b0212-159">WS-Federation ' i ASP.NET Core için dış oturum açma sağlayıcısı olarak ekleyinIdentity</span><span class="sxs-lookup"><span data-stu-id="b0212-159">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="b0212-160">Projeye [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) bağımlılığı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b0212-160">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="b0212-161">WS-Federation Ekle `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0212-161">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="b0212-162">WS-Federation ile oturum açma</span><span class="sxs-lookup"><span data-stu-id="b0212-162">Log in with WS-Federation</span></span>

<span data-ttu-id="b0212-163">Uygulamaya gidin ve NAV üstbilgisindeki **oturum aç** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b0212-163">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="b0212-164">WsFederation: ![oturum açma sayfasında oturum açma seçeneği vardır](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="b0212-164">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="b0212-165">Sağlayıcı olarak ADFS ile, düğme bir ADFS oturum açma sayfasına yönlendirir: ![ADFS oturum açma sayfası](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="b0212-165">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="b0212-166">Sağlayıcı olarak Azure Active Directory, düğme bir AAD oturum açma sayfasına yeniden yönlendirir: ![AAD oturum açma sayfası](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="b0212-166">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="b0212-167">Yeni bir kullanıcı için başarılı bir oturum açma işlemi, uygulamanın kullanıcı kayıt sayfasına yeniden yönlendiriliyor: ![kayıt sayfası](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="b0212-167">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>
