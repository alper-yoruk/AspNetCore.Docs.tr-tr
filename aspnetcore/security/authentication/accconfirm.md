---
title: ASP.NET Core hesap onaylama ve parola kurtarma
author: rick-anderson
description: E-posta onayı ve parola sıfırlama ile ASP.NET Core bir uygulama oluşturmayı öğrenin.
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: b7856a3004cfc76acfb485ff8f1fadf87f5aa904
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777117"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="6b91d-103">ASP.NET Core hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="6b91d-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="6b91d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)ve [ali Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="6b91d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="6b91d-105">Bu öğreticide, e-posta onayı ve parola sıfırlama ile bir ASP.NET Core uygulamasının nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="6b91d-106">Bu öğretici bir başlangıç konusu **değildir** .</span><span class="sxs-lookup"><span data-stu-id="6b91d-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="6b91d-107">Şunu tanımanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="6b91d-107">You should be familiar with:</span></span>

* [<span data-ttu-id="6b91d-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6b91d-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="6b91d-109">Kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="6b91d-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="6b91d-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6b91d-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="6b91d-111">ASP.NET Core 1,1 sürümü için [Bu PDF dosyasına](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) bakın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="6b91d-112">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="6b91d-112">Prerequisites</span></span>

[<span data-ttu-id="6b91d-113">.NET Core 3,0 SDK veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6b91d-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="6b91d-114">Kimlik doğrulamasıyla bir Web uygulaması oluşturma ve test etme</span><span class="sxs-lookup"><span data-stu-id="6b91d-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="6b91d-115">Kimlik doğrulamasıyla bir Web uygulaması oluşturmak için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="6b91d-116">Uygulamayı çalıştırın, **Kaydet** bağlantısını seçin ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="6b91d-117">Kaydolduktan sonra, e-posta onayı benzetimi `/Identity/Account/RegisterConfirmation` için bir bağlantı içeren to sayfasına yönlendirilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="6b91d-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="6b91d-118">`Click here to confirm your account` Bağlantıyı seçin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="6b91d-119">**Oturum açma** bağlantısını seçin ve aynı kimlik bilgileriyle oturum açın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="6b91d-120">Sizi `/Identity/Account/Manage/PersonalData` sayfaya `Hello YourEmail@provider.com!` yönlendiren bağlantıyı seçin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="6b91d-121">Sol taraftaki **kişisel veri** sekmesini seçin ve **Sil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="6b91d-122">E-posta sağlayıcısı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6b91d-122">Configure an email provider</span></span>

<span data-ttu-id="6b91d-123">Bu öğreticide, [SendGrid](https://sendgrid.com) e-posta göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b91d-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="6b91d-124">E-posta göndermek için bir SendGrid hesabına ve anahtarına ihtiyacınız vardır.</span><span class="sxs-lookup"><span data-stu-id="6b91d-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="6b91d-125">Diğer e-posta sağlayıcılarını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-125">You can use other email providers.</span></span> <span data-ttu-id="6b91d-126">E-posta göndermek için SendGrid veya başka bir e-posta hizmeti kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="6b91d-127">Güvenli hale getirmek ve düzgün şekilde ayarlamak zordur.</span><span class="sxs-lookup"><span data-stu-id="6b91d-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="6b91d-128">Güvenli e-posta anahtarını getirmek için bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6b91d-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="6b91d-129">Bu örnek için *Hizmetler/Authiletienderoptions. cs*oluşturun:</span><span class="sxs-lookup"><span data-stu-id="6b91d-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="6b91d-130">SendGrid Kullanıcı gizli dizilerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6b91d-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="6b91d-131">`SendGridUser` Ve `SendGridKey` öğesini [gizli-Manager aracı](xref:security/app-secrets)ile ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="6b91d-132">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="6b91d-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="6b91d-133">Windows 'da, gizli dizi Anahtarlar/değer çiftlerini `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` dizindeki bir *gizlilikler. JSON* dosyasında depolar.</span><span class="sxs-lookup"><span data-stu-id="6b91d-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="6b91d-134">*Gizli dizileri. JSON* dosyasının içeriği şifrelenmez.</span><span class="sxs-lookup"><span data-stu-id="6b91d-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="6b91d-135">Aşağıdaki biçimlendirme, *gizlilikler. JSON* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="6b91d-136">`SendGridKey` Değer kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="6b91d-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="6b91d-137">Daha fazla bilgi için bkz. [Options model](xref:fundamentals/configuration/options) ve [yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6b91d-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="6b91d-138">SendGrid 'i yükler</span><span class="sxs-lookup"><span data-stu-id="6b91d-138">Install SendGrid</span></span>

<span data-ttu-id="6b91d-139">Bu öğretici, [SendGrid](https://sendgrid.com/)aracılığıyla e-posta bildirimlerinin nasıl ekleneceğini gösterir, ancak SMTP ve diğer mekanizmaları kullanarak e-posta gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="6b91d-140">`SendGrid` NuGet paketini yükler:</span><span class="sxs-lookup"><span data-stu-id="6b91d-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b91d-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b91d-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b91d-142">Paket Yöneticisi konsolundan, aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="6b91d-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="6b91d-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6b91d-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6b91d-144">Konsolundan aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="6b91d-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="6b91d-145">Ücretsiz SendGrid hesabına kaydolmak için bkz. [SendGrid Ile çalışmaya başlayın](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="6b91d-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="6b91d-146">Iemailsender uygulama</span><span class="sxs-lookup"><span data-stu-id="6b91d-146">Implement IEmailSender</span></span>

<span data-ttu-id="6b91d-147">Uygulamak `IEmailSender`için, aşağıdakine benzer bir kodla *Hizmetler/emailsender. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="6b91d-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="6b91d-148">Başlat 'ı e-postayı destekleyecek şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6b91d-148">Configure startup to support email</span></span>

<span data-ttu-id="6b91d-149">Aşağıdaki kodu `ConfigureServices` *Startup.cs* dosyasındaki yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6b91d-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="6b91d-150">Geçici `EmailSender` hizmet olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="6b91d-151">`AuthMessageSenderOptions` Yapılandırma örneğini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="6b91d-152">Kaydolun, e-postayı onaylayın ve parolayı sıfırlayın</span><span class="sxs-lookup"><span data-stu-id="6b91d-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="6b91d-153">Web uygulamasını çalıştırın ve hesap onaylama ve parola kurtarma akışını test edin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="6b91d-154">Uygulamayı çalıştırma ve yeni bir Kullanıcı kaydetme</span><span class="sxs-lookup"><span data-stu-id="6b91d-154">Run the app and register a new user</span></span>
* <span data-ttu-id="6b91d-155">Hesap onay bağlantısı için e-postanızı kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="6b91d-156">E-postayı alamazsanız [hata ayıklama e-postasına](#debug) bakın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="6b91d-157">E-postanızı onaylamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="6b91d-158">E-postanız ve parolanızla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="6b91d-159">Oturumunuzu kapatın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="6b91d-160">Sınama parolası sıfırlama</span><span class="sxs-lookup"><span data-stu-id="6b91d-160">Test password reset</span></span>

* <span data-ttu-id="6b91d-161">Oturum açtıysanız **Oturumu Kapat**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="6b91d-162">**Oturum aç** bağlantısını seçin ve **parolanızı unuttum?** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="6b91d-163">Hesabı kaydettirmek için kullandığınız e-postayı girin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="6b91d-164">Parolanızı sıfırlamaya yönelik bağlantı içeren bir e-posta gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="6b91d-165">E-postanızı kontrol edin ve parolanızı sıfırlamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="6b91d-166">Parolanız başarıyla sıfırlandıktan sonra, e-posta ve yeni parolanızla oturum açabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="6b91d-167">E-posta ve etkinlik zaman aşımını değiştirme</span><span class="sxs-lookup"><span data-stu-id="6b91d-167">Change email and activity timeout</span></span>

<span data-ttu-id="6b91d-168">Varsayılan eylemsizlik zaman aşımı 14 gündür.</span><span class="sxs-lookup"><span data-stu-id="6b91d-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="6b91d-169">Aşağıdaki kod, etkinlik dışı zaman aşımını 5 güne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="6b91d-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="6b91d-170">Tüm veri koruma belirteci kullanım alanlarını Değiştir</span><span class="sxs-lookup"><span data-stu-id="6b91d-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="6b91d-171">Aşağıdaki kod tüm veri koruma belirteçleri zaman aşımı süresini 3 saate değiştirir:</span><span class="sxs-lookup"><span data-stu-id="6b91d-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="6b91d-172">Yerleşik kimlik Kullanıcı belirteçleri (bkz [. Aspnetcore/src/Identity/Extensions. Core/src/TokenOptions. cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) bir [gün zaman aşımı](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="6b91d-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="6b91d-173">E-posta belirtecini değiştir kullanım ömrü</span><span class="sxs-lookup"><span data-stu-id="6b91d-173">Change the email token lifespan</span></span>

<span data-ttu-id="6b91d-174">[Kimlik Kullanıcı belirteçlerinin](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) varsayılan belirteç kullanım ömrü [bir gündür](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="6b91d-174">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="6b91d-175">Bu bölümde, eespan e-posta belirtecinin nasıl değiştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="6b91d-176">Özel bir [veri korunabilir\<>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) ve <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6b91d-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="6b91d-177">Özel sağlayıcıyı hizmet kapsayıcısına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6b91d-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="6b91d-178">E-posta onayını yeniden gönder</span><span class="sxs-lookup"><span data-stu-id="6b91d-178">Resend email confirmation</span></span>

<span data-ttu-id="6b91d-179">[Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/5410)bakın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-179">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="6b91d-180">Hata ayıklama e-postası</span><span class="sxs-lookup"><span data-stu-id="6b91d-180">Debug email</span></span>

<span data-ttu-id="6b91d-181">E-posta ile çalışmayı alamazsanız:</span><span class="sxs-lookup"><span data-stu-id="6b91d-181">If you can't get email working:</span></span>

* <span data-ttu-id="6b91d-182">Doğrulamak `EmailSender.Execute` `SendGridClient.SendEmailAsync` için içinde bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="6b91d-183">Benzer kod kullanarak [e-posta göndermek için bir konsol uygulaması](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) oluşturun `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="6b91d-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="6b91d-184">[E-posta etkinlik](https://sendgrid.com/docs/User_Guide/email_activity.html) sayfasını gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="6b91d-185">İstenmeyen posta klasörünüzü kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-185">Check your spam folder.</span></span>
* <span data-ttu-id="6b91d-186">Farklı bir e-posta sağlayıcısında (Microsoft, Yahoo, Gmail vb.) başka bir e-posta diğer adı deneyin</span><span class="sxs-lookup"><span data-stu-id="6b91d-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="6b91d-187">Farklı e-posta hesaplarına göndermeyi deneyin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="6b91d-188">**En iyi güvenlik uygulaması** , test ve geliştirmede üretim sırlarını **kullanmamalıdır** .</span><span class="sxs-lookup"><span data-stu-id="6b91d-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="6b91d-189">Uygulamayı Azure 'a yayımlarsanız, SendGrid gizli dizilerini Azure Web App Portal 'da uygulama ayarları olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="6b91d-190">Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6b91d-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="6b91d-191">Sosyal ve yerel oturum açma hesaplarını birleştirme</span><span class="sxs-lookup"><span data-stu-id="6b91d-191">Combine social and local login accounts</span></span>

<span data-ttu-id="6b91d-192">Bu bölümü gerçekleştirmek için önce bir dış kimlik doğrulama sağlayıcısı etkinleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="6b91d-193">Bkz. [Facebook, Google ve dış sağlayıcı kimlik doğrulaması](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="6b91d-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="6b91d-194">E-posta bağlantısına tıklayarak Yerel ve sosyal hesapları birleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="6b91d-195">Aşağıdaki dizide, "RickAndMSFT@gmail.com" ilk olarak yerel bir oturum açma olarak oluşturulur; Bununla birlikte, önce hesabı önce sosyal oturum açma olarak oluşturabilir, ardından yerel bir oturum açma ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web uygulaması: RickAndMSFT@gmail.com kullanıcının kimliği doğrulandı](accconfirm/_static/rick.png)

<span data-ttu-id="6b91d-197">**Yönet** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-197">Click on the **Manage** link.</span></span> <span data-ttu-id="6b91d-198">Bu hesapla ilişkili 0 harici (sosyal oturumlar) ' a göz önüne alın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-198">Note the 0 external (social logins) associated with this account.</span></span>

![Görünümü Yönet](accconfirm/_static/manage.png)

<span data-ttu-id="6b91d-200">Başka bir oturum açma hizmeti bağlantısına tıklayın ve uygulama isteklerini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="6b91d-201">Aşağıdaki görüntüde Facebook dış kimlik doğrulama sağlayıcısıdır:</span><span class="sxs-lookup"><span data-stu-id="6b91d-201">In the following image, Facebook is the external authentication provider:</span></span>

![Dış oturum açma görünümlerinizi yönetin Facebook listeleme](accconfirm/_static/fb.png)

<span data-ttu-id="6b91d-203">İki hesap birleştirildi.</span><span class="sxs-lookup"><span data-stu-id="6b91d-203">The two accounts have been combined.</span></span> <span data-ttu-id="6b91d-204">Her iki hesapla oturum açabiliyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-204">You are able to sign in with either account.</span></span> <span data-ttu-id="6b91d-205">Kullanıcılarınızın sosyal oturum açma kimlik doğrulama hizmeti kapatılmış veya sosyal hesaplarına erişimi kaybettikleri olasılığına karşı yerel hesaplar eklemesini isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="6b91d-206">Bir sitede kullanıcılar varsa hesap onayını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="6b91d-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="6b91d-207">Kullanıcılara bir sitede hesap onayını etkinleştirmek, mevcut tüm kullanıcıları kilitler.</span><span class="sxs-lookup"><span data-stu-id="6b91d-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="6b91d-208">Mevcut kullanıcılar, hesapları onaylanmadığı için kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="6b91d-209">Mevcut Kullanıcı kilitlemesini geçici olarak çözmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="6b91d-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="6b91d-210">Tüm mevcut kullanıcıları onaylanmış olarak işaretlemek için veritabanını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="6b91d-211">Mevcut kullanıcıları onaylayın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-211">Confirm existing users.</span></span> <span data-ttu-id="6b91d-212">Örneğin, Batch-e-postaları onay bağlantılarıyla gönderin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="6b91d-213">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="6b91d-213">Prerequisites</span></span>

[<span data-ttu-id="6b91d-214">.NET Core 2,2 SDK veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6b91d-214">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="6b91d-215">Web uygulaması ve yapı iskelesi kimliği oluşturma</span><span class="sxs-lookup"><span data-stu-id="6b91d-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="6b91d-216">Kimlik doğrulamasıyla bir Web uygulaması oluşturmak için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-216">Run the following commands to create a web app with authentication.</span></span>

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

## <a name="test-new-user-registration"></a><span data-ttu-id="6b91d-217">Yeni Kullanıcı kaydını sına</span><span class="sxs-lookup"><span data-stu-id="6b91d-217">Test new user registration</span></span>

<span data-ttu-id="6b91d-218">Uygulamayı çalıştırın, **Kaydet** bağlantısını seçin ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="6b91d-219">Bu noktada, e-postadaki tek doğrulama [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) özniteliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-219">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="6b91d-220">Kayıt gönderildikten sonra uygulamada oturum açarsınız.</span><span class="sxs-lookup"><span data-stu-id="6b91d-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="6b91d-221">Daha sonra öğreticide, yeni kullanıcıların e-postaları doğrulanmadan oturum açması için kod güncellenir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="6b91d-222">Tablonun `EmailConfirmed` alanının olduğunu `False`aklınızda edin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="6b91d-223">Uygulama bir onay e-postası gönderdiğinde bir sonraki adımda bu e-postayı yeniden kullanmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="6b91d-224">Satıra sağ tıklayın ve **Sil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="6b91d-225">E-posta diğer adını silmek aşağıdaki adımlarda daha kolay hale gelir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="6b91d-226">E-posta onayı gerektir</span><span class="sxs-lookup"><span data-stu-id="6b91d-226">Require email confirmation</span></span>

<span data-ttu-id="6b91d-227">Yeni bir Kullanıcı kaydının e-postasını onaylamak en iyi uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="6b91d-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="6b91d-228">E-posta onayı, başkalarının taklit etmeyeceğinden emin olmaya yardımcı olur (diğer bir deyişle, başka birinin e-postasına kaydolmamaları).</span><span class="sxs-lookup"><span data-stu-id="6b91d-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="6b91d-229">Bir tartışma forumuna sahip olduğunuzu ve "" öğesini "yli@example.comnolivetto@contoso.com" olarak kaydolmasını engellemek istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="6b91d-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="6b91d-230">E-posta onayı olmadannolivetto@contoso.com, "" uygulamanızdan istenmeyen e-posta alabilir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="6b91d-231">Kullanıcının yanlışlıkla "ylo@example.com" olarak kaydolduğunu ve "yıllı" hatası olduğunu fark edelim.</span><span class="sxs-lookup"><span data-stu-id="6b91d-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="6b91d-232">Uygulamanın doğru e-postası olmadığından parola kurtarma kullanamayacak.</span><span class="sxs-lookup"><span data-stu-id="6b91d-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="6b91d-233">E-posta onayı, robotlardan sınırlı koruma sağlar.</span><span class="sxs-lookup"><span data-stu-id="6b91d-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="6b91d-234">E-posta onayı çok sayıda e-posta hesabına sahip kötü amaçlı kullanıcılardan koruma sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="6b91d-235">Genellikle yeni kullanıcıların, onaylanan bir e-posta almadan önce Web sitenize veri almasını engellemek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="6b91d-236">Onaylanan `Startup.ConfigureServices` bir e-posta gerektirecek şekilde güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="6b91d-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="6b91d-237">`config.SignIn.RequireConfirmedEmail = true;`kayıtlı kullanıcıların e-postaları onaylanana kadar oturum açmasını önler.</span><span class="sxs-lookup"><span data-stu-id="6b91d-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="6b91d-238">E-posta sağlayıcısını Yapılandır</span><span class="sxs-lookup"><span data-stu-id="6b91d-238">Configure email provider</span></span>

<span data-ttu-id="6b91d-239">Bu öğreticide, [SendGrid](https://sendgrid.com) e-posta göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6b91d-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="6b91d-240">E-posta göndermek için bir SendGrid hesabına ve anahtarına ihtiyacınız vardır.</span><span class="sxs-lookup"><span data-stu-id="6b91d-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="6b91d-241">Diğer e-posta sağlayıcılarını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-241">You can use other email providers.</span></span> <span data-ttu-id="6b91d-242">ASP.NET Core 2. x içerir `System.Net.Mail`. Bu, uygulamanızdan e-posta göndermenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="6b91d-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="6b91d-243">E-posta göndermek için SendGrid veya başka bir e-posta hizmeti kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="6b91d-244">Güvenli hale getirmek ve düzgün şekilde ayarlamak zordur.</span><span class="sxs-lookup"><span data-stu-id="6b91d-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="6b91d-245">Güvenli e-posta anahtarını getirmek için bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6b91d-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="6b91d-246">Bu örnek için *Hizmetler/Authiletienderoptions. cs*oluşturun:</span><span class="sxs-lookup"><span data-stu-id="6b91d-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="6b91d-247">SendGrid Kullanıcı gizli dizilerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6b91d-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="6b91d-248">`SendGridUser` Ve `SendGridKey` öğesini [gizli-Manager aracı](xref:security/app-secrets)ile ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="6b91d-249">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="6b91d-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="6b91d-250">Windows 'da, gizli dizi Anahtarlar/değer çiftlerini `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` dizindeki bir *gizlilikler. JSON* dosyasında depolar.</span><span class="sxs-lookup"><span data-stu-id="6b91d-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="6b91d-251">*Gizli dizileri. JSON* dosyasının içeriği şifrelenmez.</span><span class="sxs-lookup"><span data-stu-id="6b91d-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="6b91d-252">Aşağıdaki biçimlendirme, *gizlilikler. JSON* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="6b91d-253">`SendGridKey` Değer kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="6b91d-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="6b91d-254">Daha fazla bilgi için bkz. [Options model](xref:fundamentals/configuration/options) ve [yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6b91d-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="6b91d-255">SendGrid 'i yükler</span><span class="sxs-lookup"><span data-stu-id="6b91d-255">Install SendGrid</span></span>

<span data-ttu-id="6b91d-256">Bu öğretici, [SendGrid](https://sendgrid.com/)aracılığıyla e-posta bildirimlerinin nasıl ekleneceğini gösterir, ancak SMTP ve diğer mekanizmaları kullanarak e-posta gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="6b91d-257">`SendGrid` NuGet paketini yükler:</span><span class="sxs-lookup"><span data-stu-id="6b91d-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b91d-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b91d-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b91d-259">Paket Yöneticisi konsolundan, aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="6b91d-259">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="6b91d-260">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6b91d-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6b91d-261">Konsolundan aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="6b91d-261">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="6b91d-262">Ücretsiz SendGrid hesabına kaydolmak için bkz. [SendGrid Ile çalışmaya başlayın](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="6b91d-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="6b91d-263">Iemailsender uygulama</span><span class="sxs-lookup"><span data-stu-id="6b91d-263">Implement IEmailSender</span></span>

<span data-ttu-id="6b91d-264">Uygulamak `IEmailSender`için, aşağıdakine benzer bir kodla *Hizmetler/emailsender. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="6b91d-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="6b91d-265">Başlat 'ı e-postayı destekleyecek şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6b91d-265">Configure startup to support email</span></span>

<span data-ttu-id="6b91d-266">Aşağıdaki kodu `ConfigureServices` *Startup.cs* dosyasındaki yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6b91d-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="6b91d-267">Geçici `EmailSender` hizmet olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="6b91d-268">`AuthMessageSenderOptions` Yapılandırma örneğini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="6b91d-269">Hesap onaylama ve parola kurtarmayı etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="6b91d-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="6b91d-270">Şablonda hesap onaylama ve parola kurtarma için kod bulunur.</span><span class="sxs-lookup"><span data-stu-id="6b91d-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="6b91d-271">`OnPostAsync` Yöntemini */Identity/Pages/Account/Register.cshtml.cs*içinde bulun.</span><span class="sxs-lookup"><span data-stu-id="6b91d-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="6b91d-272">Yeni kayıtlı kullanıcıların aşağıdaki satırı açıklama ekleyerek otomatik olarak oturum açmasını engelleyin:</span><span class="sxs-lookup"><span data-stu-id="6b91d-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="6b91d-273">Tüm Yöntem vurgulanmış olan değiştirilen çizgi ile gösterilir:</span><span class="sxs-lookup"><span data-stu-id="6b91d-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="6b91d-274">Kaydolun, e-postayı onaylayın ve parolayı sıfırlayın</span><span class="sxs-lookup"><span data-stu-id="6b91d-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="6b91d-275">Web uygulamasını çalıştırın ve hesap onaylama ve parola kurtarma akışını test edin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="6b91d-276">Uygulamayı çalıştırma ve yeni bir Kullanıcı kaydetme</span><span class="sxs-lookup"><span data-stu-id="6b91d-276">Run the app and register a new user</span></span>
* <span data-ttu-id="6b91d-277">Hesap onay bağlantısı için e-postanızı kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="6b91d-278">E-postayı alamazsanız [hata ayıklama e-postasına](#debug) bakın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="6b91d-279">E-postanızı onaylamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="6b91d-280">E-postanız ve parolanızla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="6b91d-281">Oturumunuzu kapatın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="6b91d-282">Yönet sayfasını görüntüle</span><span class="sxs-lookup"><span data-stu-id="6b91d-282">View the manage page</span></span>

<span data-ttu-id="6b91d-283">Kullanıcı adınızı tarayıcıda seçin: ![tarayıcı penceresinde Kullanıcı adı](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="6b91d-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="6b91d-284">Yönet sayfası, **profil** sekmesi seçili olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="6b91d-285">**E** -postada, e-postanın onaylandığını belirten bir onay kutusu gösterilir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="6b91d-286">Sınama parolası sıfırlama</span><span class="sxs-lookup"><span data-stu-id="6b91d-286">Test password reset</span></span>

* <span data-ttu-id="6b91d-287">Oturum açtıysanız **Oturumu Kapat**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="6b91d-288">**Oturum aç** bağlantısını seçin ve **parolanızı unuttum?** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="6b91d-289">Hesabı kaydettirmek için kullandığınız e-postayı girin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="6b91d-290">Parolanızı sıfırlamaya yönelik bağlantı içeren bir e-posta gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="6b91d-291">E-postanızı kontrol edin ve parolanızı sıfırlamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="6b91d-292">Parolanız başarıyla sıfırlandıktan sonra, e-posta ve yeni parolanızla oturum açabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="6b91d-293">E-posta ve etkinlik zaman aşımını değiştirme</span><span class="sxs-lookup"><span data-stu-id="6b91d-293">Change email and activity timeout</span></span>

<span data-ttu-id="6b91d-294">Varsayılan eylemsizlik zaman aşımı 14 gündür.</span><span class="sxs-lookup"><span data-stu-id="6b91d-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="6b91d-295">Aşağıdaki kod, etkinlik dışı zaman aşımını 5 güne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="6b91d-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="6b91d-296">Tüm veri koruma belirteci kullanım alanlarını Değiştir</span><span class="sxs-lookup"><span data-stu-id="6b91d-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="6b91d-297">Aşağıdaki kod tüm veri koruma belirteçleri zaman aşımı süresini 3 saate değiştirir:</span><span class="sxs-lookup"><span data-stu-id="6b91d-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="6b91d-298">Yerleşik Identity Kullanıcı belirteçleri (bkz. [aspnetcore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) bir [gün zaman aşımı](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="6b91d-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="6b91d-299">E-posta belirtecini değiştir kullanım ömrü</span><span class="sxs-lookup"><span data-stu-id="6b91d-299">Change the email token lifespan</span></span>

<span data-ttu-id="6b91d-300">Kullanıcı belirteçlerinin varsayılan belirteç kullanım ömrü [bir gündür](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). [ Identity ](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)</span><span class="sxs-lookup"><span data-stu-id="6b91d-300">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="6b91d-301">Bu bölümde, eespan e-posta belirtecinin nasıl değiştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="6b91d-302">Özel bir [veri korunabilir\<>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) ve <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6b91d-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="6b91d-303">Özel sağlayıcıyı hizmet kapsayıcısına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6b91d-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="6b91d-304">E-posta onayını yeniden gönder</span><span class="sxs-lookup"><span data-stu-id="6b91d-304">Resend email confirmation</span></span>

<span data-ttu-id="6b91d-305">[Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/5410)bakın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-305">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="6b91d-306">Hata ayıklama e-postası</span><span class="sxs-lookup"><span data-stu-id="6b91d-306">Debug email</span></span>

<span data-ttu-id="6b91d-307">E-posta ile çalışmayı alamazsanız:</span><span class="sxs-lookup"><span data-stu-id="6b91d-307">If you can't get email working:</span></span>

* <span data-ttu-id="6b91d-308">Doğrulamak `EmailSender.Execute` `SendGridClient.SendEmailAsync` için içinde bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="6b91d-309">Benzer kod kullanarak [e-posta göndermek için bir konsol uygulaması](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) oluşturun `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="6b91d-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="6b91d-310">[E-posta etkinlik](https://sendgrid.com/docs/User_Guide/email_activity.html) sayfasını gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="6b91d-311">İstenmeyen posta klasörünüzü kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-311">Check your spam folder.</span></span>
* <span data-ttu-id="6b91d-312">Farklı bir e-posta sağlayıcısında (Microsoft, Yahoo, Gmail vb.) başka bir e-posta diğer adı deneyin</span><span class="sxs-lookup"><span data-stu-id="6b91d-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="6b91d-313">Farklı e-posta hesaplarına göndermeyi deneyin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="6b91d-314">**En iyi güvenlik uygulaması** , test ve geliştirmede üretim sırlarını **kullanmamalıdır** .</span><span class="sxs-lookup"><span data-stu-id="6b91d-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="6b91d-315">Uygulamayı Azure 'a yayımlarsanız, SendGrid gizli dizilerini Azure Web App Portal 'da uygulama ayarları olarak ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="6b91d-316">Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6b91d-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="6b91d-317">Sosyal ve yerel oturum açma hesaplarını birleştirme</span><span class="sxs-lookup"><span data-stu-id="6b91d-317">Combine social and local login accounts</span></span>

<span data-ttu-id="6b91d-318">Bu bölümü gerçekleştirmek için önce bir dış kimlik doğrulama sağlayıcısı etkinleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="6b91d-319">Bkz. [Facebook, Google ve dış sağlayıcı kimlik doğrulaması](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="6b91d-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="6b91d-320">E-posta bağlantısına tıklayarak Yerel ve sosyal hesapları birleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="6b91d-321">Aşağıdaki dizide, "RickAndMSFT@gmail.com" ilk olarak yerel bir oturum açma olarak oluşturulur; Bununla birlikte, önce hesabı önce sosyal oturum açma olarak oluşturabilir, ardından yerel bir oturum açma ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web uygulaması: RickAndMSFT@gmail.com kullanıcının kimliği doğrulandı](accconfirm/_static/rick.png)

<span data-ttu-id="6b91d-323">**Yönet** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-323">Click on the **Manage** link.</span></span> <span data-ttu-id="6b91d-324">Bu hesapla ilişkili 0 harici (sosyal oturumlar) ' a göz önüne alın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-324">Note the 0 external (social logins) associated with this account.</span></span>

![Görünümü Yönet](accconfirm/_static/manage.png)

<span data-ttu-id="6b91d-326">Başka bir oturum açma hizmeti bağlantısına tıklayın ve uygulama isteklerini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="6b91d-327">Aşağıdaki görüntüde Facebook dış kimlik doğrulama sağlayıcısıdır:</span><span class="sxs-lookup"><span data-stu-id="6b91d-327">In the following image, Facebook is the external authentication provider:</span></span>

![Dış oturum açma görünümlerinizi yönetin Facebook listeleme](accconfirm/_static/fb.png)

<span data-ttu-id="6b91d-329">İki hesap birleştirildi.</span><span class="sxs-lookup"><span data-stu-id="6b91d-329">The two accounts have been combined.</span></span> <span data-ttu-id="6b91d-330">Her iki hesapla oturum açabiliyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-330">You are able to sign in with either account.</span></span> <span data-ttu-id="6b91d-331">Kullanıcılarınızın sosyal oturum açma kimlik doğrulama hizmeti kapatılmış veya sosyal hesaplarına erişimi kaybettikleri olasılığına karşı yerel hesaplar eklemesini isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6b91d-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="6b91d-332">Bir sitede kullanıcılar varsa hesap onayını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="6b91d-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="6b91d-333">Kullanıcılara bir sitede hesap onayını etkinleştirmek, mevcut tüm kullanıcıları kilitler.</span><span class="sxs-lookup"><span data-stu-id="6b91d-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="6b91d-334">Mevcut kullanıcılar, hesapları onaylanmadığı için kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="6b91d-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="6b91d-335">Mevcut Kullanıcı kilitlemesini geçici olarak çözmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="6b91d-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="6b91d-336">Tüm mevcut kullanıcıları onaylanmış olarak işaretlemek için veritabanını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="6b91d-337">Mevcut kullanıcıları onaylayın.</span><span class="sxs-lookup"><span data-stu-id="6b91d-337">Confirm existing users.</span></span> <span data-ttu-id="6b91d-338">Örneğin, Batch-e-postaları onay bağlantılarıyla gönderin.</span><span class="sxs-lookup"><span data-stu-id="6b91d-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
