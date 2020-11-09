---
title: ASP.NET Core hesap onaylama ve parola kurtarma
author: rick-anderson
description: E-posta onayı ve parola sıfırlama ile ASP.NET Core bir uygulama oluşturmayı öğrenin.
ms.author: riande
ms.date: 03/11/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/accconfirm
ms.openlocfilehash: 91148c67d5dc0bf97e2f926f50dcff5dd0708f4b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052324"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="b9d84-103">ASP.NET Core hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="b9d84-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="b9d84-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)ve [ali Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="b9d84-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="b9d84-105">Bu öğreticide, e-posta onayı ve parola sıfırlama ile bir ASP.NET Core uygulamasının nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="b9d84-106">Bu öğretici bir başlangıç konusu **değildir** .</span><span class="sxs-lookup"><span data-stu-id="b9d84-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="b9d84-107">Şunu tanımanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="b9d84-107">You should be familiar with:</span></span>

* [<span data-ttu-id="b9d84-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9d84-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="b9d84-109">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="b9d84-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="b9d84-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b9d84-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="b9d84-111">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="b9d84-111">Prerequisites</span></span>

[<span data-ttu-id="b9d84-112">.NET Core 3,0 SDK veya üzeri</span><span class="sxs-lookup"><span data-stu-id="b9d84-112">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="b9d84-113">Kimlik doğrulamasıyla bir Web uygulaması oluşturma ve test etme</span><span class="sxs-lookup"><span data-stu-id="b9d84-113">Create and test a web app with authentication</span></span>

<span data-ttu-id="b9d84-114">Kimlik doğrulamasıyla bir Web uygulaması oluşturmak için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-114">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="b9d84-115">Uygulamayı çalıştırın, **Kaydet** bağlantısını seçin ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-115">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="b9d84-116">Kaydolduktan sonra, `/Identity/Account/RegisterConfirmation` e-posta onayı benzetimi için bir bağlantı içeren to sayfasına yönlendirilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="b9d84-116">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="b9d84-117">`Click here to confirm your account` bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-117">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="b9d84-118">**Oturum açma** bağlantısını seçin ve aynı kimlik bilgileriyle oturum açın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-118">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="b9d84-119">`Hello YourEmail@provider.com!`Sizi sayfaya yönlendiren bağlantıyı seçin `/Identity/Account/Manage/PersonalData` .</span><span class="sxs-lookup"><span data-stu-id="b9d84-119">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="b9d84-120">Sol taraftaki **kişisel veri** sekmesini seçin ve **Sil** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-120">Select the **Personal data** tab on the left, and then select **Delete** .</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="b9d84-121">E-posta sağlayıcısı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b9d84-121">Configure an email provider</span></span>

<span data-ttu-id="b9d84-122">Bu öğreticide, [SendGrid](https://sendgrid.com) e-posta göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b9d84-122">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="b9d84-123">E-posta göndermek için bir SendGrid hesabına ve anahtarına ihtiyacınız vardır.</span><span class="sxs-lookup"><span data-stu-id="b9d84-123">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="b9d84-124">Diğer e-posta sağlayıcılarını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-124">You can use other email providers.</span></span> <span data-ttu-id="b9d84-125">E-posta göndermek için SendGrid veya başka bir e-posta hizmeti kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-125">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="b9d84-126">Güvenli hale getirmek ve düzgün şekilde ayarlamak zordur.</span><span class="sxs-lookup"><span data-stu-id="b9d84-126">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="b9d84-127">SendGrid hesabı, [gönderici ekleme](https://sendgrid.com/docs/ui/sending-email/senders/)gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-127">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="b9d84-128">Güvenli e-posta anahtarını getirmek için bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b9d84-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="b9d84-129">Bu örnek için *Hizmetler/Authiletienderoptions. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="b9d84-129">For this sample, create *Services/AuthMessageSenderOptions.cs* :</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="b9d84-130">SendGrid Kullanıcı gizli dizilerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b9d84-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="b9d84-131">Ve öğesini `SendGridUser` `SendGridKey` [gizli-Manager aracı](xref:security/app-secrets)ile ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="b9d84-132">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9d84-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="b9d84-133">Windows 'da gizli dizi, anahtar/değer çiftlerini dizindeki bir *secrets.js* bir dosyada depolar `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` .</span><span class="sxs-lookup"><span data-stu-id="b9d84-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="b9d84-134">Dosyadaki *secrets.js* içeriği şifrelenmedi.</span><span class="sxs-lookup"><span data-stu-id="b9d84-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="b9d84-135">Aşağıdaki biçimlendirme dosyadaki *secrets.js* gösterir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="b9d84-136">`SendGridKey`Değer kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="b9d84-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="b9d84-137">Daha fazla bilgi için bkz. [Options model](xref:fundamentals/configuration/options) ve [yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b9d84-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="b9d84-138">SendGrid 'i yükler</span><span class="sxs-lookup"><span data-stu-id="b9d84-138">Install SendGrid</span></span>

<span data-ttu-id="b9d84-139">Bu öğretici, [SendGrid](https://sendgrid.com/)aracılığıyla e-posta bildirimlerinin nasıl ekleneceğini gösterir, ancak SMTP ve diğer mekanizmaları kullanarak e-posta gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="b9d84-140">`SendGrid`NuGet paketini yükler:</span><span class="sxs-lookup"><span data-stu-id="b9d84-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9d84-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9d84-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b9d84-142">Paket Yöneticisi konsolundan, aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="b9d84-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="b9d84-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b9d84-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b9d84-144">Konsolundan aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="b9d84-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="b9d84-145">Ücretsiz SendGrid hesabına kaydolmak için bkz. [SendGrid Ile çalışmaya başlayın](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="b9d84-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="b9d84-146">Iemailsender uygulama</span><span class="sxs-lookup"><span data-stu-id="b9d84-146">Implement IEmailSender</span></span>

<span data-ttu-id="b9d84-147">Uygulamak için `IEmailSender` , aşağıdakine benzer bir kodla *Hizmetler/emailsender. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="b9d84-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="b9d84-148">Başlat 'ı e-postayı destekleyecek şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b9d84-148">Configure startup to support email</span></span>

<span data-ttu-id="b9d84-149">Aşağıdaki kodu `ConfigureServices` *Startup.cs* dosyasındaki yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b9d84-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="b9d84-150">`EmailSender`Geçici hizmet olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="b9d84-151">`AuthMessageSenderOptions`Yapılandırma örneğini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a><span data-ttu-id="b9d84-152">Yapı iskelesi RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="b9d84-152">Scaffold RegisterConfirmation</span></span>

<span data-ttu-id="b9d84-153">[Yapı iskelesi Identity ](xref:security/authentication/scaffold-identity) ve yapı iskelesi için yönergeleri izleyin `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="b9d84-153">Follow the instructions for [Scaffold Identity](xref:security/authentication/scaffold-identity) and scaffold `RegisterConfirmation`.</span></span>

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="b9d84-154">Kaydolun, e-postayı onaylayın ve parolayı sıfırlayın</span><span class="sxs-lookup"><span data-stu-id="b9d84-154">Register, confirm email, and reset password</span></span>

<span data-ttu-id="b9d84-155">Web uygulamasını çalıştırın ve hesap onaylama ve parola kurtarma akışını test edin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-155">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="b9d84-156">Uygulamayı çalıştırma ve yeni bir Kullanıcı kaydetme</span><span class="sxs-lookup"><span data-stu-id="b9d84-156">Run the app and register a new user</span></span>
* <span data-ttu-id="b9d84-157">Hesap onay bağlantısı için e-postanızı kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-157">Check your email for the account confirmation link.</span></span> <span data-ttu-id="b9d84-158">E-postayı alamazsanız [hata ayıklama e-postasına](#debug) bakın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-158">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="b9d84-159">E-postanızı onaylamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-159">Click the link to confirm your email.</span></span>
* <span data-ttu-id="b9d84-160">E-postanız ve parolanızla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-160">Sign in with your email and password.</span></span>
* <span data-ttu-id="b9d84-161">Oturumunuzu kapatın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-161">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="b9d84-162">Sınama parolası sıfırlama</span><span class="sxs-lookup"><span data-stu-id="b9d84-162">Test password reset</span></span>

* <span data-ttu-id="b9d84-163">Oturum açtıysanız **Oturumu Kapat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-163">If you're signed in, select **Logout** .</span></span>
* <span data-ttu-id="b9d84-164">**Oturum aç** bağlantısını seçin ve **parolanızı unuttum?** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-164">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="b9d84-165">Hesabı kaydettirmek için kullandığınız e-postayı girin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-165">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="b9d84-166">Parolanızı sıfırlamaya yönelik bağlantı içeren bir e-posta gönderilir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-166">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="b9d84-167">E-postanızı kontrol edin ve parolanızı sıfırlamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-167">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="b9d84-168">Parolanız başarıyla sıfırlandıktan sonra, e-posta ve yeni parolanızla oturum açabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-168">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

<a name="resend"></a>

## <a name="resend-email-confirmation"></a><span data-ttu-id="b9d84-169">E-posta onayını yeniden gönder</span><span class="sxs-lookup"><span data-stu-id="b9d84-169">Resend email confirmation</span></span>

<span data-ttu-id="b9d84-170">ASP.NET Core 5,0 ve üzeri sürümlerde, **oturum açma** sayfasında **e-postayı yeniden gönder onay** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-170">In ASP.NET Core 5.0 and later, select the **Resend email confirmation** link on the **Login** page.</span></span>

### <a name="change-email-and-activity-timeout"></a><span data-ttu-id="b9d84-171">E-posta ve etkinlik zaman aşımını değiştirme</span><span class="sxs-lookup"><span data-stu-id="b9d84-171">Change email and activity timeout</span></span>

<span data-ttu-id="b9d84-172">Varsayılan eylemsizlik zaman aşımı 14 gündür.</span><span class="sxs-lookup"><span data-stu-id="b9d84-172">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="b9d84-173">Aşağıdaki kod, etkinlik dışı zaman aşımını 5 güne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="b9d84-173">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="b9d84-174">Tüm veri koruma belirteci kullanım alanlarını Değiştir</span><span class="sxs-lookup"><span data-stu-id="b9d84-174">Change all data protection token lifespans</span></span>

<span data-ttu-id="b9d84-175">Aşağıdaki kod tüm veri koruma belirteçleri zaman aşımı süresini 3 saate değiştirir:</span><span class="sxs-lookup"><span data-stu-id="b9d84-175">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="b9d84-176">Yerleşik Identity Kullanıcı belirteçleri (bkz. [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) bir [gün zaman aşımı](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="b9d84-176">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="b9d84-177">E-posta belirtecini değiştir kullanım ömrü</span><span class="sxs-lookup"><span data-stu-id="b9d84-177">Change the email token lifespan</span></span>

<span data-ttu-id="b9d84-178">[ Identity Kullanıcı belirteçlerinin](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) varsayılan belirteç kullanım ömrü [bir gündür](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="b9d84-178">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="b9d84-179">Bu bölümde, eespan e-posta belirtecinin nasıl değiştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-179">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="b9d84-180">Özel bir [veri korunabilir \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) ve ekleyin <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="b9d84-180">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="b9d84-181">Özel sağlayıcıyı hizmet kapsayıcısına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b9d84-181">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="b9d84-182">Hata ayıklama e-postası</span><span class="sxs-lookup"><span data-stu-id="b9d84-182">Debug email</span></span>

<span data-ttu-id="b9d84-183">E-posta ile çalışmayı alamazsanız:</span><span class="sxs-lookup"><span data-stu-id="b9d84-183">If you can't get email working:</span></span>

* <span data-ttu-id="b9d84-184">Doğrulamak için içinde bir kesme noktası ayarlayın `EmailSender.Execute` `SendGridClient.SendEmailAsync` .</span><span class="sxs-lookup"><span data-stu-id="b9d84-184">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="b9d84-185">Benzer kod kullanarak [e-posta göndermek için bir konsol uygulaması](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) oluşturun `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="b9d84-185">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="b9d84-186">[E-posta etkinlik](https://sendgrid.com/docs/User_Guide/email_activity.html) sayfasını gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-186">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="b9d84-187">İstenmeyen posta klasörünüzü kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-187">Check your spam folder.</span></span>
* <span data-ttu-id="b9d84-188">Farklı bir e-posta sağlayıcısında (Microsoft, Yahoo, Gmail vb.) başka bir e-posta diğer adı deneyin</span><span class="sxs-lookup"><span data-stu-id="b9d84-188">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="b9d84-189">Farklı e-posta hesaplarına göndermeyi deneyin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-189">Try sending to different email accounts.</span></span>

<span data-ttu-id="b9d84-190">**En iyi güvenlik uygulaması** , test ve geliştirmede üretim sırlarını **kullanmamalıdır** .</span><span class="sxs-lookup"><span data-stu-id="b9d84-190">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="b9d84-191">Uygulamayı Azure 'a yayımlarsanız, SendGrid gizli dizilerini Azure Web App Portal 'da uygulama ayarları olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-191">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="b9d84-192">Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b9d84-192">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="b9d84-193">Sosyal ve yerel oturum açma hesaplarını birleştirme</span><span class="sxs-lookup"><span data-stu-id="b9d84-193">Combine social and local login accounts</span></span>

<span data-ttu-id="b9d84-194">Bu bölümü gerçekleştirmek için önce bir dış kimlik doğrulama sağlayıcısı etkinleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-194">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="b9d84-195">Bkz. [Facebook, Google ve dış sağlayıcı kimlik doğrulaması](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="b9d84-195">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="b9d84-196">E-posta bağlantısına tıklayarak Yerel ve sosyal hesapları birleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-196">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="b9d84-197">Aşağıdaki dizide, " RickAndMSFT@gmail.com " ilk olarak yerel bir oturum açma olarak oluşturulur; ancak, önce hesabı önce sosyal oturum açma olarak oluşturabilir, ardından yerel bir oturum açma ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-197">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web uygulaması: RickAndMSFT@gmail.com kullanıcının kimliği doğrulandı](accconfirm/_static/rick.png)

<span data-ttu-id="b9d84-199">**Yönet** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-199">Click on the **Manage** link.</span></span> <span data-ttu-id="b9d84-200">Bu hesapla ilişkili 0 harici (sosyal oturumlar) ' a göz önüne alın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-200">Note the 0 external (social logins) associated with this account.</span></span>

![Görünümü Yönet](accconfirm/_static/manage.png)

<span data-ttu-id="b9d84-202">Başka bir oturum açma hizmeti bağlantısına tıklayın ve uygulama isteklerini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-202">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="b9d84-203">Aşağıdaki görüntüde Facebook dış kimlik doğrulama sağlayıcısıdır:</span><span class="sxs-lookup"><span data-stu-id="b9d84-203">In the following image, Facebook is the external authentication provider:</span></span>

![Dış oturum açma görünümlerinizi yönetin Facebook listeleme](accconfirm/_static/fb.png)

<span data-ttu-id="b9d84-205">İki hesap birleştirildi.</span><span class="sxs-lookup"><span data-stu-id="b9d84-205">The two accounts have been combined.</span></span> <span data-ttu-id="b9d84-206">Her iki hesapla oturum açabiliyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-206">You are able to sign in with either account.</span></span> <span data-ttu-id="b9d84-207">Kullanıcılarınızın sosyal oturum açma kimlik doğrulama hizmeti kapatılmış veya sosyal hesaplarına erişimi kaybettikleri olasılığına karşı yerel hesaplar eklemesini isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-207">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="b9d84-208">Bir sitede kullanıcılar varsa hesap onayını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="b9d84-208">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="b9d84-209">Kullanıcılara bir sitede hesap onayını etkinleştirmek, mevcut tüm kullanıcıları kilitler.</span><span class="sxs-lookup"><span data-stu-id="b9d84-209">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="b9d84-210">Mevcut kullanıcılar, hesapları onaylanmadığı için kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-210">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="b9d84-211">Mevcut Kullanıcı kilitlemesini geçici olarak çözmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="b9d84-211">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="b9d84-212">Tüm mevcut kullanıcıları onaylanmış olarak işaretlemek için veritabanını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-212">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="b9d84-213">Mevcut kullanıcıları onaylayın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-213">Confirm existing users.</span></span> <span data-ttu-id="b9d84-214">Örneğin, Batch-e-postaları onay bağlantılarıyla gönderin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-214">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="b9d84-215">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="b9d84-215">Prerequisites</span></span>

[<span data-ttu-id="b9d84-216">.NET Core 2,2 SDK veya üzeri</span><span class="sxs-lookup"><span data-stu-id="b9d84-216">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-no-locidentity"></a><span data-ttu-id="b9d84-217">Web uygulaması ve yapı iskelesi oluşturma Identity</span><span class="sxs-lookup"><span data-stu-id="b9d84-217">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="b9d84-218">Kimlik doğrulamasıyla bir Web uygulaması oluşturmak için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-218">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run
```

> [!NOTE]
> <span data-ttu-id="b9d84-219">' <xref:Microsoft.AspNetCore.Identity.PasswordOptions> De yapılandırılmışsa `Startup.ConfigureServices` , [ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Yapı iskelesi sayfalarında özelliği için öznitelik yapılandırması gerekli olabilir Identity .</span><span class="sxs-lookup"><span data-stu-id="b9d84-219">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="b9d84-220">Bir `InputModel` `Password` özellik, `Areas/Identity/Pages/Account/Register.cshtml.cs` Yapı iskelesi sonrasında dosyasında bulunur Identity .</span><span class="sxs-lookup"><span data-stu-id="b9d84-220">An `InputModel` `Password` property is found in the `Areas/Identity/Pages/Account/Register.cshtml.cs` file after scaffolding Identity.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="b9d84-221">Yeni Kullanıcı kaydını sına</span><span class="sxs-lookup"><span data-stu-id="b9d84-221">Test new user registration</span></span>

<span data-ttu-id="b9d84-222">Uygulamayı çalıştırın, **Kaydet** bağlantısını seçin ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-222">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="b9d84-223">Bu noktada, e-postadaki tek doğrulama [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) özniteliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-223">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="b9d84-224">Kayıt gönderildikten sonra uygulamada oturum açarsınız.</span><span class="sxs-lookup"><span data-stu-id="b9d84-224">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="b9d84-225">Daha sonra öğreticide, yeni kullanıcıların e-postaları doğrulanmadan oturum açması için kod güncellenir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-225">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="b9d84-226">Tablonun `EmailConfirmed` alanının olduğunu aklınızda edin `False` .</span><span class="sxs-lookup"><span data-stu-id="b9d84-226">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="b9d84-227">Uygulama bir onay e-postası gönderdiğinde bir sonraki adımda bu e-postayı yeniden kullanmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-227">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="b9d84-228">Satıra sağ tıklayın ve **Sil** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-228">Right-click on the row and select **Delete** .</span></span> <span data-ttu-id="b9d84-229">E-posta diğer adını silmek aşağıdaki adımlarda daha kolay hale gelir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-229">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="b9d84-230">E-posta onayı gerektir</span><span class="sxs-lookup"><span data-stu-id="b9d84-230">Require email confirmation</span></span>

<span data-ttu-id="b9d84-231">Yeni bir Kullanıcı kaydının e-postasını onaylamak en iyi uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="b9d84-231">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="b9d84-232">E-posta onayı, başkalarının taklit etmeyeceğinden emin olmaya yardımcı olur (diğer bir deyişle, başka birinin e-postasına kaydolmamaları).</span><span class="sxs-lookup"><span data-stu-id="b9d84-232">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="b9d84-233">Bir tartışma forumuna sahip olduğunuzu ve "" öğesini yli@example.com "" olarak kaydolmasını engellemek istediğinizi varsayalım nolivetto@contoso.com .</span><span class="sxs-lookup"><span data-stu-id="b9d84-233">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="b9d84-234">E-posta onayı olmadan, " nolivetto@contoso.com " uygulamanızdan istenmeyen e-posta alabilir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-234">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="b9d84-235">Kullanıcının yanlışlıkla "" olarak kaydolduğunu ylo@example.com ve "yıllı" hatası olduğunu fark edelim.</span><span class="sxs-lookup"><span data-stu-id="b9d84-235">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="b9d84-236">Uygulamanın doğru e-postası olmadığından parola kurtarma kullanamayacak.</span><span class="sxs-lookup"><span data-stu-id="b9d84-236">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="b9d84-237">E-posta onayı, robotlardan sınırlı koruma sağlar.</span><span class="sxs-lookup"><span data-stu-id="b9d84-237">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="b9d84-238">E-posta onayı çok sayıda e-posta hesabına sahip kötü amaçlı kullanıcılardan koruma sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-238">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="b9d84-239">Genellikle yeni kullanıcıların, onaylanan bir e-posta almadan önce Web sitenize veri almasını engellemek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-239">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="b9d84-240">`Startup.ConfigureServices`Onaylanan bir e-posta gerektirecek şekilde güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="b9d84-240">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="b9d84-241">`config.SignIn.RequireConfirmedEmail = true;` kayıtlı kullanıcıların e-postaları onaylanana kadar oturum açmasını önler.</span><span class="sxs-lookup"><span data-stu-id="b9d84-241">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="b9d84-242">E-posta sağlayıcısını Yapılandır</span><span class="sxs-lookup"><span data-stu-id="b9d84-242">Configure email provider</span></span>

<span data-ttu-id="b9d84-243">Bu öğreticide, [SendGrid](https://sendgrid.com) e-posta göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b9d84-243">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="b9d84-244">E-posta göndermek için bir SendGrid hesabına ve anahtarına ihtiyacınız vardır.</span><span class="sxs-lookup"><span data-stu-id="b9d84-244">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="b9d84-245">Diğer e-posta sağlayıcılarını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-245">You can use other email providers.</span></span> <span data-ttu-id="b9d84-246">ASP.NET Core 2. x içerir `System.Net.Mail` . Bu, uygulamanızdan e-posta göndermenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="b9d84-246">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="b9d84-247">E-posta göndermek için SendGrid veya başka bir e-posta hizmeti kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-247">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="b9d84-248">Güvenli hale getirmek ve düzgün şekilde ayarlamak zordur.</span><span class="sxs-lookup"><span data-stu-id="b9d84-248">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="b9d84-249">Güvenli e-posta anahtarını getirmek için bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b9d84-249">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="b9d84-250">Bu örnek için *Hizmetler/Authiletienderoptions. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="b9d84-250">For this sample, create *Services/AuthMessageSenderOptions.cs* :</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="b9d84-251">SendGrid Kullanıcı gizli dizilerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b9d84-251">Configure SendGrid user secrets</span></span>

<span data-ttu-id="b9d84-252">Ve öğesini `SendGridUser` `SendGridKey` [gizli-Manager aracı](xref:security/app-secrets)ile ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-252">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="b9d84-253">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9d84-253">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="b9d84-254">Windows 'da gizli dizi, anahtar/değer çiftlerini dizindeki bir *secrets.js* bir dosyada depolar `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` .</span><span class="sxs-lookup"><span data-stu-id="b9d84-254">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="b9d84-255">Dosyadaki *secrets.js* içeriği şifrelenmedi.</span><span class="sxs-lookup"><span data-stu-id="b9d84-255">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="b9d84-256">Aşağıdaki biçimlendirme dosyadaki *secrets.js* gösterir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-256">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="b9d84-257">`SendGridKey`Değer kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="b9d84-257">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="b9d84-258">Daha fazla bilgi için bkz. [Options model](xref:fundamentals/configuration/options) ve [yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b9d84-258">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="b9d84-259">SendGrid 'i yükler</span><span class="sxs-lookup"><span data-stu-id="b9d84-259">Install SendGrid</span></span>

<span data-ttu-id="b9d84-260">Bu öğretici, [SendGrid](https://sendgrid.com/)aracılığıyla e-posta bildirimlerinin nasıl ekleneceğini gösterir, ancak SMTP ve diğer mekanizmaları kullanarak e-posta gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-260">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="b9d84-261">`SendGrid`NuGet paketini yükler:</span><span class="sxs-lookup"><span data-stu-id="b9d84-261">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9d84-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9d84-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b9d84-263">Paket Yöneticisi konsolundan, aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="b9d84-263">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="b9d84-264">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b9d84-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b9d84-265">Konsolundan aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="b9d84-265">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="b9d84-266">Ücretsiz SendGrid hesabına kaydolmak için bkz. [SendGrid Ile çalışmaya başlayın](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="b9d84-266">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="b9d84-267">Iemailsender uygulama</span><span class="sxs-lookup"><span data-stu-id="b9d84-267">Implement IEmailSender</span></span>

<span data-ttu-id="b9d84-268">Uygulamak için `IEmailSender` , aşağıdakine benzer bir kodla *Hizmetler/emailsender. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="b9d84-268">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="b9d84-269">Başlat 'ı e-postayı destekleyecek şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b9d84-269">Configure startup to support email</span></span>

<span data-ttu-id="b9d84-270">Aşağıdaki kodu `ConfigureServices` *Startup.cs* dosyasındaki yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b9d84-270">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="b9d84-271">`EmailSender`Geçici hizmet olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-271">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="b9d84-272">`AuthMessageSenderOptions`Yapılandırma örneğini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-272">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="b9d84-273">Hesap onaylama ve parola kurtarmayı etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="b9d84-273">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="b9d84-274">Şablonda hesap onaylama ve parola kurtarma için kod bulunur.</span><span class="sxs-lookup"><span data-stu-id="b9d84-274">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="b9d84-275">`OnPostAsync`Yöntemini */ Identity /Pages/Account/Register.cshtml.cs* içinde bulun.</span><span class="sxs-lookup"><span data-stu-id="b9d84-275">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs* .</span></span>

<span data-ttu-id="b9d84-276">Yeni kayıtlı kullanıcıların aşağıdaki satırı açıklama ekleyerek otomatik olarak oturum açmasını engelleyin:</span><span class="sxs-lookup"><span data-stu-id="b9d84-276">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="b9d84-277">Tüm Yöntem vurgulanmış olan değiştirilen çizgi ile gösterilir:</span><span class="sxs-lookup"><span data-stu-id="b9d84-277">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="b9d84-278">Kaydolun, e-postayı onaylayın ve parolayı sıfırlayın</span><span class="sxs-lookup"><span data-stu-id="b9d84-278">Register, confirm email, and reset password</span></span>

<span data-ttu-id="b9d84-279">Web uygulamasını çalıştırın ve hesap onaylama ve parola kurtarma akışını test edin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-279">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="b9d84-280">Uygulamayı çalıştırma ve yeni bir Kullanıcı kaydetme</span><span class="sxs-lookup"><span data-stu-id="b9d84-280">Run the app and register a new user</span></span>
* <span data-ttu-id="b9d84-281">Hesap onay bağlantısı için e-postanızı kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-281">Check your email for the account confirmation link.</span></span> <span data-ttu-id="b9d84-282">E-postayı alamazsanız [hata ayıklama e-postasına](#debug) bakın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-282">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="b9d84-283">E-postanızı onaylamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-283">Click the link to confirm your email.</span></span>
* <span data-ttu-id="b9d84-284">E-postanız ve parolanızla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-284">Sign in with your email and password.</span></span>
* <span data-ttu-id="b9d84-285">Oturumunuzu kapatın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-285">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="b9d84-286">Yönet sayfasını görüntüle</span><span class="sxs-lookup"><span data-stu-id="b9d84-286">View the manage page</span></span>

<span data-ttu-id="b9d84-287">Kullanıcı adınızı tarayıcıda seçin: ![ tarayıcı penceresinde Kullanıcı adı](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="b9d84-287">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="b9d84-288">Yönet sayfası, **profil** sekmesi seçili olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-288">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="b9d84-289">**E** -postada, e-postanın onaylandığını belirten bir onay kutusu gösterilir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-289">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="b9d84-290">Sınama parolası sıfırlama</span><span class="sxs-lookup"><span data-stu-id="b9d84-290">Test password reset</span></span>

* <span data-ttu-id="b9d84-291">Oturum açtıysanız **Oturumu Kapat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-291">If you're signed in, select **Logout** .</span></span>
* <span data-ttu-id="b9d84-292">**Oturum aç** bağlantısını seçin ve **parolanızı unuttum?** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-292">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="b9d84-293">Hesabı kaydettirmek için kullandığınız e-postayı girin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-293">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="b9d84-294">Parolanızı sıfırlamaya yönelik bağlantı içeren bir e-posta gönderilir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-294">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="b9d84-295">E-postanızı kontrol edin ve parolanızı sıfırlamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-295">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="b9d84-296">Parolanız başarıyla sıfırlandıktan sonra, e-posta ve yeni parolanızla oturum açabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-296">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="b9d84-297">E-posta ve etkinlik zaman aşımını değiştirme</span><span class="sxs-lookup"><span data-stu-id="b9d84-297">Change email and activity timeout</span></span>

<span data-ttu-id="b9d84-298">Varsayılan eylemsizlik zaman aşımı 14 gündür.</span><span class="sxs-lookup"><span data-stu-id="b9d84-298">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="b9d84-299">Aşağıdaki kod, etkinlik dışı zaman aşımını 5 güne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="b9d84-299">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="b9d84-300">Tüm veri koruma belirteci kullanım alanlarını Değiştir</span><span class="sxs-lookup"><span data-stu-id="b9d84-300">Change all data protection token lifespans</span></span>

<span data-ttu-id="b9d84-301">Aşağıdaki kod tüm veri koruma belirteçleri zaman aşımı süresini 3 saate değiştirir:</span><span class="sxs-lookup"><span data-stu-id="b9d84-301">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="b9d84-302">Yerleşik Identity Kullanıcı belirteçleri (bkz. [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) bir [gün zaman aşımı](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="b9d84-302">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="b9d84-303">E-posta belirtecini değiştir kullanım ömrü</span><span class="sxs-lookup"><span data-stu-id="b9d84-303">Change the email token lifespan</span></span>

<span data-ttu-id="b9d84-304">[ Identity Kullanıcı belirteçlerinin](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) varsayılan belirteç kullanım ömrü [bir gündür](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="b9d84-304">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="b9d84-305">Bu bölümde, eespan e-posta belirtecinin nasıl değiştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-305">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="b9d84-306">Özel bir [veri korunabilir \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) ve ekleyin <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="b9d84-306">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="b9d84-307">Özel sağlayıcıyı hizmet kapsayıcısına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b9d84-307">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="b9d84-308">E-posta onayını yeniden gönder</span><span class="sxs-lookup"><span data-stu-id="b9d84-308">Resend email confirmation</span></span>

<span data-ttu-id="b9d84-309">[Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/5410)bakın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-309">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="b9d84-310">Hata ayıklama e-postası</span><span class="sxs-lookup"><span data-stu-id="b9d84-310">Debug email</span></span>

<span data-ttu-id="b9d84-311">E-posta ile çalışmayı alamazsanız:</span><span class="sxs-lookup"><span data-stu-id="b9d84-311">If you can't get email working:</span></span>

* <span data-ttu-id="b9d84-312">Doğrulamak için içinde bir kesme noktası ayarlayın `EmailSender.Execute` `SendGridClient.SendEmailAsync` .</span><span class="sxs-lookup"><span data-stu-id="b9d84-312">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="b9d84-313">Benzer kod kullanarak [e-posta göndermek için bir konsol uygulaması](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) oluşturun `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="b9d84-313">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="b9d84-314">[E-posta etkinlik](https://sendgrid.com/docs/User_Guide/email_activity.html) sayfasını gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-314">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="b9d84-315">İstenmeyen posta klasörünüzü kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-315">Check your spam folder.</span></span>
* <span data-ttu-id="b9d84-316">Farklı bir e-posta sağlayıcısında (Microsoft, Yahoo, Gmail vb.) başka bir e-posta diğer adı deneyin</span><span class="sxs-lookup"><span data-stu-id="b9d84-316">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="b9d84-317">Farklı e-posta hesaplarına göndermeyi deneyin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-317">Try sending to different email accounts.</span></span>

<span data-ttu-id="b9d84-318">**En iyi güvenlik uygulaması** , test ve geliştirmede üretim sırlarını **kullanmamalıdır** .</span><span class="sxs-lookup"><span data-stu-id="b9d84-318">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="b9d84-319">Uygulamayı Azure 'a yayımlarsanız, SendGrid gizli dizilerini Azure Web App Portal 'da uygulama ayarları olarak ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-319">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="b9d84-320">Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b9d84-320">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="b9d84-321">Sosyal ve yerel oturum açma hesaplarını birleştirme</span><span class="sxs-lookup"><span data-stu-id="b9d84-321">Combine social and local login accounts</span></span>

<span data-ttu-id="b9d84-322">Bu bölümü gerçekleştirmek için önce bir dış kimlik doğrulama sağlayıcısı etkinleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-322">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="b9d84-323">Bkz. [Facebook, Google ve dış sağlayıcı kimlik doğrulaması](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="b9d84-323">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="b9d84-324">E-posta bağlantısına tıklayarak Yerel ve sosyal hesapları birleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-324">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="b9d84-325">Aşağıdaki dizide, " RickAndMSFT@gmail.com " ilk olarak yerel bir oturum açma olarak oluşturulur; ancak, önce hesabı önce sosyal oturum açma olarak oluşturabilir, ardından yerel bir oturum açma ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-325">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web uygulaması: RickAndMSFT@gmail.com kullanıcının kimliği doğrulandı](accconfirm/_static/rick.png)

<span data-ttu-id="b9d84-327">**Yönet** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-327">Click on the **Manage** link.</span></span> <span data-ttu-id="b9d84-328">Bu hesapla ilişkili 0 harici (sosyal oturumlar) ' a göz önüne alın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-328">Note the 0 external (social logins) associated with this account.</span></span>

![Görünümü Yönet](accconfirm/_static/manage.png)

<span data-ttu-id="b9d84-330">Başka bir oturum açma hizmeti bağlantısına tıklayın ve uygulama isteklerini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-330">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="b9d84-331">Aşağıdaki görüntüde Facebook dış kimlik doğrulama sağlayıcısıdır:</span><span class="sxs-lookup"><span data-stu-id="b9d84-331">In the following image, Facebook is the external authentication provider:</span></span>

![Dış oturum açma görünümlerinizi yönetin Facebook listeleme](accconfirm/_static/fb.png)

<span data-ttu-id="b9d84-333">İki hesap birleştirildi.</span><span class="sxs-lookup"><span data-stu-id="b9d84-333">The two accounts have been combined.</span></span> <span data-ttu-id="b9d84-334">Her iki hesapla oturum açabiliyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-334">You are able to sign in with either account.</span></span> <span data-ttu-id="b9d84-335">Kullanıcılarınızın sosyal oturum açma kimlik doğrulama hizmeti kapatılmış veya sosyal hesaplarına erişimi kaybettikleri olasılığına karşı yerel hesaplar eklemesini isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9d84-335">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="b9d84-336">Bir sitede kullanıcılar varsa hesap onayını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="b9d84-336">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="b9d84-337">Kullanıcılara bir sitede hesap onayını etkinleştirmek, mevcut tüm kullanıcıları kilitler.</span><span class="sxs-lookup"><span data-stu-id="b9d84-337">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="b9d84-338">Mevcut kullanıcılar, hesapları onaylanmadığı için kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="b9d84-338">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="b9d84-339">Mevcut Kullanıcı kilitlemesini geçici olarak çözmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="b9d84-339">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="b9d84-340">Tüm mevcut kullanıcıları onaylanmış olarak işaretlemek için veritabanını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-340">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="b9d84-341">Mevcut kullanıcıları onaylayın.</span><span class="sxs-lookup"><span data-stu-id="b9d84-341">Confirm existing users.</span></span> <span data-ttu-id="b9d84-342">Örneğin, Batch-e-postaları onay bağlantılarıyla gönderin.</span><span class="sxs-lookup"><span data-stu-id="b9d84-342">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
