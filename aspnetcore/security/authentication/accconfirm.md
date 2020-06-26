---
title: ASP.NET Core hesap onaylama ve parola kurtarma
author: rick-anderson
description: E-posta onayı ve parola sıfırlama ile ASP.NET Core bir uygulama oluşturmayı öğrenin.
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: bf599487fdc3e574f72f1a3d35278cc9c2ce7513
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404658"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="149f0-103">ASP.NET Core hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="149f0-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="149f0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)ve [ali Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="149f0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="149f0-105">Bu öğreticide, e-posta onayı ve parola sıfırlama ile bir ASP.NET Core uygulamasının nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="149f0-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="149f0-106">Bu öğretici bir başlangıç konusu **değildir** .</span><span class="sxs-lookup"><span data-stu-id="149f0-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="149f0-107">Şunu tanımanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="149f0-107">You should be familiar with:</span></span>

* [<span data-ttu-id="149f0-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="149f0-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="149f0-109">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="149f0-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="149f0-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="149f0-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="149f0-111">ASP.NET Core 1,1 sürümü için [Bu PDF dosyasına](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) bakın.</span><span class="sxs-lookup"><span data-stu-id="149f0-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="149f0-112">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="149f0-112">Prerequisites</span></span>

[<span data-ttu-id="149f0-113">.NET Core 3,0 SDK veya üzeri</span><span class="sxs-lookup"><span data-stu-id="149f0-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="149f0-114">Kimlik doğrulamasıyla bir Web uygulaması oluşturma ve test etme</span><span class="sxs-lookup"><span data-stu-id="149f0-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="149f0-115">Kimlik doğrulamasıyla bir Web uygulaması oluşturmak için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="149f0-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="149f0-116">Uygulamayı çalıştırın, **Kaydet** bağlantısını seçin ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="149f0-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="149f0-117">Kaydolduktan sonra, `/Identity/Account/RegisterConfirmation` e-posta onayı benzetimi için bir bağlantı içeren to sayfasına yönlendirilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="149f0-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="149f0-118">Bağlantıyı seçin `Click here to confirm your account` .</span><span class="sxs-lookup"><span data-stu-id="149f0-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="149f0-119">**Oturum açma** bağlantısını seçin ve aynı kimlik bilgileriyle oturum açın.</span><span class="sxs-lookup"><span data-stu-id="149f0-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="149f0-120">`Hello YourEmail@provider.com!`Sizi sayfaya yönlendiren bağlantıyı seçin `/Identity/Account/Manage/PersonalData` .</span><span class="sxs-lookup"><span data-stu-id="149f0-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="149f0-121">Sol taraftaki **kişisel veri** sekmesini seçin ve **Sil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="149f0-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="149f0-122">E-posta sağlayıcısı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="149f0-122">Configure an email provider</span></span>

<span data-ttu-id="149f0-123">Bu öğreticide, [SendGrid](https://sendgrid.com) e-posta göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="149f0-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="149f0-124">E-posta göndermek için bir SendGrid hesabına ve anahtarına ihtiyacınız vardır.</span><span class="sxs-lookup"><span data-stu-id="149f0-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="149f0-125">Diğer e-posta sağlayıcılarını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-125">You can use other email providers.</span></span> <span data-ttu-id="149f0-126">E-posta göndermek için SendGrid veya başka bir e-posta hizmeti kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="149f0-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="149f0-127">Güvenli hale getirmek ve düzgün şekilde ayarlamak zordur.</span><span class="sxs-lookup"><span data-stu-id="149f0-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="149f0-128">SendGrid hesabı, [gönderici ekleme](https://sendgrid.com/docs/ui/sending-email/senders/)gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="149f0-128">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="149f0-129">Güvenli e-posta anahtarını getirmek için bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="149f0-129">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="149f0-130">Bu örnek için *Hizmetler/Authiletienderoptions. cs*oluşturun:</span><span class="sxs-lookup"><span data-stu-id="149f0-130">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="149f0-131">SendGrid Kullanıcı gizli dizilerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="149f0-131">Configure SendGrid user secrets</span></span>

<span data-ttu-id="149f0-132">Ve öğesini `SendGridUser` `SendGridKey` [gizli-Manager aracı](xref:security/app-secrets)ile ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="149f0-132">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="149f0-133">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="149f0-133">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="149f0-134">Windows 'da gizli dizi, anahtar/değer çiftlerini dizindeki bir *secrets.js* bir dosyada depolar `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` .</span><span class="sxs-lookup"><span data-stu-id="149f0-134">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="149f0-135">Dosyadaki *secrets.js* içeriği şifrelenmedi.</span><span class="sxs-lookup"><span data-stu-id="149f0-135">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="149f0-136">Aşağıdaki biçimlendirme dosyadaki *secrets.js* gösterir.</span><span class="sxs-lookup"><span data-stu-id="149f0-136">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="149f0-137">`SendGridKey`Değer kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="149f0-137">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="149f0-138">Daha fazla bilgi için bkz. [Options model](xref:fundamentals/configuration/options) ve [yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="149f0-138">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="149f0-139">SendGrid 'i yükler</span><span class="sxs-lookup"><span data-stu-id="149f0-139">Install SendGrid</span></span>

<span data-ttu-id="149f0-140">Bu öğretici, [SendGrid](https://sendgrid.com/)aracılığıyla e-posta bildirimlerinin nasıl ekleneceğini gösterir, ancak SMTP ve diğer mekanizmaları kullanarak e-posta gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-140">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="149f0-141">`SendGrid`NuGet paketini yükler:</span><span class="sxs-lookup"><span data-stu-id="149f0-141">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="149f0-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="149f0-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="149f0-143">Paket Yöneticisi konsolundan, aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="149f0-143">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="149f0-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="149f0-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="149f0-145">Konsolundan aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="149f0-145">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="149f0-146">Ücretsiz SendGrid hesabına kaydolmak için bkz. [SendGrid Ile çalışmaya başlayın](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="149f0-146">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="149f0-147">Iemailsender uygulama</span><span class="sxs-lookup"><span data-stu-id="149f0-147">Implement IEmailSender</span></span>

<span data-ttu-id="149f0-148">Uygulamak için `IEmailSender` , aşağıdakine benzer bir kodla *Hizmetler/emailsender. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="149f0-148">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="149f0-149">Başlat 'ı e-postayı destekleyecek şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="149f0-149">Configure startup to support email</span></span>

<span data-ttu-id="149f0-150">Aşağıdaki kodu `ConfigureServices` *Startup.cs* dosyasındaki yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="149f0-150">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="149f0-151">`EmailSender`Geçici hizmet olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="149f0-151">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="149f0-152">`AuthMessageSenderOptions`Yapılandırma örneğini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="149f0-152">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="149f0-153">Kaydolun, e-postayı onaylayın ve parolayı sıfırlayın</span><span class="sxs-lookup"><span data-stu-id="149f0-153">Register, confirm email, and reset password</span></span>

<span data-ttu-id="149f0-154">Web uygulamasını çalıştırın ve hesap onaylama ve parola kurtarma akışını test edin.</span><span class="sxs-lookup"><span data-stu-id="149f0-154">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="149f0-155">Uygulamayı çalıştırma ve yeni bir Kullanıcı kaydetme</span><span class="sxs-lookup"><span data-stu-id="149f0-155">Run the app and register a new user</span></span>
* <span data-ttu-id="149f0-156">Hesap onay bağlantısı için e-postanızı kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="149f0-156">Check your email for the account confirmation link.</span></span> <span data-ttu-id="149f0-157">E-postayı alamazsanız [hata ayıklama e-postasına](#debug) bakın.</span><span class="sxs-lookup"><span data-stu-id="149f0-157">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="149f0-158">E-postanızı onaylamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="149f0-158">Click the link to confirm your email.</span></span>
* <span data-ttu-id="149f0-159">E-postanız ve parolanızla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="149f0-159">Sign in with your email and password.</span></span>
* <span data-ttu-id="149f0-160">Oturumunuzu kapatın.</span><span class="sxs-lookup"><span data-stu-id="149f0-160">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="149f0-161">Sınama parolası sıfırlama</span><span class="sxs-lookup"><span data-stu-id="149f0-161">Test password reset</span></span>

* <span data-ttu-id="149f0-162">Oturum açtıysanız **Oturumu Kapat**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="149f0-162">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="149f0-163">**Oturum aç** bağlantısını seçin ve **parolanızı unuttum?** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="149f0-163">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="149f0-164">Hesabı kaydettirmek için kullandığınız e-postayı girin.</span><span class="sxs-lookup"><span data-stu-id="149f0-164">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="149f0-165">Parolanızı sıfırlamaya yönelik bağlantı içeren bir e-posta gönderilir.</span><span class="sxs-lookup"><span data-stu-id="149f0-165">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="149f0-166">E-postanızı kontrol edin ve parolanızı sıfırlamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="149f0-166">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="149f0-167">Parolanız başarıyla sıfırlandıktan sonra, e-posta ve yeni parolanızla oturum açabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-167">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="149f0-168">E-posta ve etkinlik zaman aşımını değiştirme</span><span class="sxs-lookup"><span data-stu-id="149f0-168">Change email and activity timeout</span></span>

<span data-ttu-id="149f0-169">Varsayılan eylemsizlik zaman aşımı 14 gündür.</span><span class="sxs-lookup"><span data-stu-id="149f0-169">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="149f0-170">Aşağıdaki kod, etkinlik dışı zaman aşımını 5 güne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="149f0-170">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="149f0-171">Tüm veri koruma belirteci kullanım alanlarını Değiştir</span><span class="sxs-lookup"><span data-stu-id="149f0-171">Change all data protection token lifespans</span></span>

<span data-ttu-id="149f0-172">Aşağıdaki kod tüm veri koruma belirteçleri zaman aşımı süresini 3 saate değiştirir:</span><span class="sxs-lookup"><span data-stu-id="149f0-172">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="149f0-173">Yerleşik Identity Kullanıcı belirteçleri (bkz. [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) bir [gün zaman aşımı](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="149f0-173">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="149f0-174">E-posta belirtecini değiştir kullanım ömrü</span><span class="sxs-lookup"><span data-stu-id="149f0-174">Change the email token lifespan</span></span>

<span data-ttu-id="149f0-175">[ Identity Kullanıcı belirteçlerinin](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) varsayılan belirteç kullanım ömrü [bir gündür](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="149f0-175">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="149f0-176">Bu bölümde, eespan e-posta belirtecinin nasıl değiştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="149f0-176">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="149f0-177">Özel bir [veri korunabilir \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) ve ekleyin <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="149f0-177">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="149f0-178">Özel sağlayıcıyı hizmet kapsayıcısına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="149f0-178">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="149f0-179">E-posta onayını yeniden gönder</span><span class="sxs-lookup"><span data-stu-id="149f0-179">Resend email confirmation</span></span>

<span data-ttu-id="149f0-180">[Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/5410)bakın.</span><span class="sxs-lookup"><span data-stu-id="149f0-180">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="149f0-181">Hata ayıklama e-postası</span><span class="sxs-lookup"><span data-stu-id="149f0-181">Debug email</span></span>

<span data-ttu-id="149f0-182">E-posta ile çalışmayı alamazsanız:</span><span class="sxs-lookup"><span data-stu-id="149f0-182">If you can't get email working:</span></span>

* <span data-ttu-id="149f0-183">Doğrulamak için içinde bir kesme noktası ayarlayın `EmailSender.Execute` `SendGridClient.SendEmailAsync` .</span><span class="sxs-lookup"><span data-stu-id="149f0-183">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="149f0-184">Benzer kod kullanarak [e-posta göndermek için bir konsol uygulaması](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) oluşturun `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="149f0-184">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="149f0-185">[E-posta etkinlik](https://sendgrid.com/docs/User_Guide/email_activity.html) sayfasını gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="149f0-185">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="149f0-186">İstenmeyen posta klasörünüzü kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="149f0-186">Check your spam folder.</span></span>
* <span data-ttu-id="149f0-187">Farklı bir e-posta sağlayıcısında (Microsoft, Yahoo, Gmail vb.) başka bir e-posta diğer adı deneyin</span><span class="sxs-lookup"><span data-stu-id="149f0-187">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="149f0-188">Farklı e-posta hesaplarına göndermeyi deneyin.</span><span class="sxs-lookup"><span data-stu-id="149f0-188">Try sending to different email accounts.</span></span>

<span data-ttu-id="149f0-189">**En iyi güvenlik uygulaması** , test ve geliştirmede üretim sırlarını **kullanmamalıdır** .</span><span class="sxs-lookup"><span data-stu-id="149f0-189">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="149f0-190">Uygulamayı Azure 'a yayımlarsanız, SendGrid gizli dizilerini Azure Web App Portal 'da uygulama ayarları olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="149f0-190">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="149f0-191">Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="149f0-191">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="149f0-192">Sosyal ve yerel oturum açma hesaplarını birleştirme</span><span class="sxs-lookup"><span data-stu-id="149f0-192">Combine social and local login accounts</span></span>

<span data-ttu-id="149f0-193">Bu bölümü gerçekleştirmek için önce bir dış kimlik doğrulama sağlayıcısı etkinleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="149f0-193">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="149f0-194">Bkz. [Facebook, Google ve dış sağlayıcı kimlik doğrulaması](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="149f0-194">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="149f0-195">E-posta bağlantısına tıklayarak Yerel ve sosyal hesapları birleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-195">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="149f0-196">Aşağıdaki dizide, " RickAndMSFT@gmail.com " ilk olarak yerel bir oturum açma olarak oluşturulur; ancak, önce hesabı önce sosyal oturum açma olarak oluşturabilir, ardından yerel bir oturum açma ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-196">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web uygulaması: RickAndMSFT@gmail.com kullanıcının kimliği doğrulandı](accconfirm/_static/rick.png)

<span data-ttu-id="149f0-198">**Yönet** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="149f0-198">Click on the **Manage** link.</span></span> <span data-ttu-id="149f0-199">Bu hesapla ilişkili 0 harici (sosyal oturumlar) ' a göz önüne alın.</span><span class="sxs-lookup"><span data-stu-id="149f0-199">Note the 0 external (social logins) associated with this account.</span></span>

![Görünümü Yönet](accconfirm/_static/manage.png)

<span data-ttu-id="149f0-201">Başka bir oturum açma hizmeti bağlantısına tıklayın ve uygulama isteklerini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="149f0-201">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="149f0-202">Aşağıdaki görüntüde Facebook dış kimlik doğrulama sağlayıcısıdır:</span><span class="sxs-lookup"><span data-stu-id="149f0-202">In the following image, Facebook is the external authentication provider:</span></span>

![Dış oturum açma görünümlerinizi yönetin Facebook listeleme](accconfirm/_static/fb.png)

<span data-ttu-id="149f0-204">İki hesap birleştirildi.</span><span class="sxs-lookup"><span data-stu-id="149f0-204">The two accounts have been combined.</span></span> <span data-ttu-id="149f0-205">Her iki hesapla oturum açabiliyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-205">You are able to sign in with either account.</span></span> <span data-ttu-id="149f0-206">Kullanıcılarınızın sosyal oturum açma kimlik doğrulama hizmeti kapatılmış veya sosyal hesaplarına erişimi kaybettikleri olasılığına karşı yerel hesaplar eklemesini isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-206">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="149f0-207">Bir sitede kullanıcılar varsa hesap onayını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="149f0-207">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="149f0-208">Kullanıcılara bir sitede hesap onayını etkinleştirmek, mevcut tüm kullanıcıları kilitler.</span><span class="sxs-lookup"><span data-stu-id="149f0-208">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="149f0-209">Mevcut kullanıcılar, hesapları onaylanmadığı için kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="149f0-209">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="149f0-210">Mevcut Kullanıcı kilitlemesini geçici olarak çözmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="149f0-210">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="149f0-211">Tüm mevcut kullanıcıları onaylanmış olarak işaretlemek için veritabanını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="149f0-211">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="149f0-212">Mevcut kullanıcıları onaylayın.</span><span class="sxs-lookup"><span data-stu-id="149f0-212">Confirm existing users.</span></span> <span data-ttu-id="149f0-213">Örneğin, Batch-e-postaları onay bağlantılarıyla gönderin.</span><span class="sxs-lookup"><span data-stu-id="149f0-213">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="149f0-214">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="149f0-214">Prerequisites</span></span>

[<span data-ttu-id="149f0-215">.NET Core 2,2 SDK veya üzeri</span><span class="sxs-lookup"><span data-stu-id="149f0-215">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="149f0-216">Web uygulaması ve yapı iskelesi oluşturmaIdentity</span><span class="sxs-lookup"><span data-stu-id="149f0-216">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="149f0-217">Kimlik doğrulamasıyla bir Web uygulaması oluşturmak için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="149f0-217">Run the following commands to create a web app with authentication.</span></span>

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

## <a name="test-new-user-registration"></a><span data-ttu-id="149f0-218">Yeni Kullanıcı kaydını sına</span><span class="sxs-lookup"><span data-stu-id="149f0-218">Test new user registration</span></span>

<span data-ttu-id="149f0-219">Uygulamayı çalıştırın, **Kaydet** bağlantısını seçin ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="149f0-219">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="149f0-220">Bu noktada, e-postadaki tek doğrulama [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) özniteliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="149f0-220">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="149f0-221">Kayıt gönderildikten sonra uygulamada oturum açarsınız.</span><span class="sxs-lookup"><span data-stu-id="149f0-221">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="149f0-222">Daha sonra öğreticide, yeni kullanıcıların e-postaları doğrulanmadan oturum açması için kod güncellenir.</span><span class="sxs-lookup"><span data-stu-id="149f0-222">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="149f0-223">Tablonun `EmailConfirmed` alanının olduğunu aklınızda edin `False` .</span><span class="sxs-lookup"><span data-stu-id="149f0-223">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="149f0-224">Uygulama bir onay e-postası gönderdiğinde bir sonraki adımda bu e-postayı yeniden kullanmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-224">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="149f0-225">Satıra sağ tıklayın ve **Sil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="149f0-225">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="149f0-226">E-posta diğer adını silmek aşağıdaki adımlarda daha kolay hale gelir.</span><span class="sxs-lookup"><span data-stu-id="149f0-226">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="149f0-227">E-posta onayı gerektir</span><span class="sxs-lookup"><span data-stu-id="149f0-227">Require email confirmation</span></span>

<span data-ttu-id="149f0-228">Yeni bir Kullanıcı kaydının e-postasını onaylamak en iyi uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="149f0-228">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="149f0-229">E-posta onayı, başkalarının taklit etmeyeceğinden emin olmaya yardımcı olur (diğer bir deyişle, başka birinin e-postasına kaydolmamaları).</span><span class="sxs-lookup"><span data-stu-id="149f0-229">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="149f0-230">Bir tartışma forumuna sahip olduğunuzu ve "" öğesini yli@example.com "" olarak kaydolmasını engellemek istediğinizi varsayalım nolivetto@contoso.com .</span><span class="sxs-lookup"><span data-stu-id="149f0-230">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="149f0-231">E-posta onayı olmadan, " nolivetto@contoso.com " uygulamanızdan istenmeyen e-posta alabilir.</span><span class="sxs-lookup"><span data-stu-id="149f0-231">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="149f0-232">Kullanıcının yanlışlıkla "" olarak kaydolduğunu ylo@example.com ve "yıllı" hatası olduğunu fark edelim.</span><span class="sxs-lookup"><span data-stu-id="149f0-232">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="149f0-233">Uygulamanın doğru e-postası olmadığından parola kurtarma kullanamayacak.</span><span class="sxs-lookup"><span data-stu-id="149f0-233">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="149f0-234">E-posta onayı, robotlardan sınırlı koruma sağlar.</span><span class="sxs-lookup"><span data-stu-id="149f0-234">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="149f0-235">E-posta onayı çok sayıda e-posta hesabına sahip kötü amaçlı kullanıcılardan koruma sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="149f0-235">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="149f0-236">Genellikle yeni kullanıcıların, onaylanan bir e-posta almadan önce Web sitenize veri almasını engellemek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-236">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="149f0-237">`Startup.ConfigureServices`Onaylanan bir e-posta gerektirecek şekilde güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="149f0-237">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="149f0-238">`config.SignIn.RequireConfirmedEmail = true;`kayıtlı kullanıcıların e-postaları onaylanana kadar oturum açmasını önler.</span><span class="sxs-lookup"><span data-stu-id="149f0-238">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="149f0-239">E-posta sağlayıcısını Yapılandır</span><span class="sxs-lookup"><span data-stu-id="149f0-239">Configure email provider</span></span>

<span data-ttu-id="149f0-240">Bu öğreticide, [SendGrid](https://sendgrid.com) e-posta göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="149f0-240">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="149f0-241">E-posta göndermek için bir SendGrid hesabına ve anahtarına ihtiyacınız vardır.</span><span class="sxs-lookup"><span data-stu-id="149f0-241">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="149f0-242">Diğer e-posta sağlayıcılarını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-242">You can use other email providers.</span></span> <span data-ttu-id="149f0-243">ASP.NET Core 2. x içerir `System.Net.Mail` . Bu, uygulamanızdan e-posta göndermenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="149f0-243">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="149f0-244">E-posta göndermek için SendGrid veya başka bir e-posta hizmeti kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="149f0-244">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="149f0-245">Güvenli hale getirmek ve düzgün şekilde ayarlamak zordur.</span><span class="sxs-lookup"><span data-stu-id="149f0-245">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="149f0-246">Güvenli e-posta anahtarını getirmek için bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="149f0-246">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="149f0-247">Bu örnek için *Hizmetler/Authiletienderoptions. cs*oluşturun:</span><span class="sxs-lookup"><span data-stu-id="149f0-247">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="149f0-248">SendGrid Kullanıcı gizli dizilerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="149f0-248">Configure SendGrid user secrets</span></span>

<span data-ttu-id="149f0-249">Ve öğesini `SendGridUser` `SendGridKey` [gizli-Manager aracı](xref:security/app-secrets)ile ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="149f0-249">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="149f0-250">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="149f0-250">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="149f0-251">Windows 'da gizli dizi, anahtar/değer çiftlerini dizindeki bir *secrets.js* bir dosyada depolar `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` .</span><span class="sxs-lookup"><span data-stu-id="149f0-251">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="149f0-252">Dosyadaki *secrets.js* içeriği şifrelenmedi.</span><span class="sxs-lookup"><span data-stu-id="149f0-252">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="149f0-253">Aşağıdaki biçimlendirme dosyadaki *secrets.js* gösterir.</span><span class="sxs-lookup"><span data-stu-id="149f0-253">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="149f0-254">`SendGridKey`Değer kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="149f0-254">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="149f0-255">Daha fazla bilgi için bkz. [Options model](xref:fundamentals/configuration/options) ve [yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="149f0-255">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="149f0-256">SendGrid 'i yükler</span><span class="sxs-lookup"><span data-stu-id="149f0-256">Install SendGrid</span></span>

<span data-ttu-id="149f0-257">Bu öğretici, [SendGrid](https://sendgrid.com/)aracılığıyla e-posta bildirimlerinin nasıl ekleneceğini gösterir, ancak SMTP ve diğer mekanizmaları kullanarak e-posta gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-257">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="149f0-258">`SendGrid`NuGet paketini yükler:</span><span class="sxs-lookup"><span data-stu-id="149f0-258">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="149f0-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="149f0-259">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="149f0-260">Paket Yöneticisi konsolundan, aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="149f0-260">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="149f0-261">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="149f0-261">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="149f0-262">Konsolundan aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="149f0-262">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="149f0-263">Ücretsiz SendGrid hesabına kaydolmak için bkz. [SendGrid Ile çalışmaya başlayın](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="149f0-263">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="149f0-264">Iemailsender uygulama</span><span class="sxs-lookup"><span data-stu-id="149f0-264">Implement IEmailSender</span></span>

<span data-ttu-id="149f0-265">Uygulamak için `IEmailSender` , aşağıdakine benzer bir kodla *Hizmetler/emailsender. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="149f0-265">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="149f0-266">Başlat 'ı e-postayı destekleyecek şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="149f0-266">Configure startup to support email</span></span>

<span data-ttu-id="149f0-267">Aşağıdaki kodu `ConfigureServices` *Startup.cs* dosyasındaki yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="149f0-267">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="149f0-268">`EmailSender`Geçici hizmet olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="149f0-268">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="149f0-269">`AuthMessageSenderOptions`Yapılandırma örneğini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="149f0-269">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="149f0-270">Hesap onaylama ve parola kurtarmayı etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="149f0-270">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="149f0-271">Şablonda hesap onaylama ve parola kurtarma için kod bulunur.</span><span class="sxs-lookup"><span data-stu-id="149f0-271">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="149f0-272">`OnPostAsync`Yöntemini */ Identity /Pages/Account/Register.cshtml.cs*içinde bulun.</span><span class="sxs-lookup"><span data-stu-id="149f0-272">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="149f0-273">Yeni kayıtlı kullanıcıların aşağıdaki satırı açıklama ekleyerek otomatik olarak oturum açmasını engelleyin:</span><span class="sxs-lookup"><span data-stu-id="149f0-273">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="149f0-274">Tüm Yöntem vurgulanmış olan değiştirilen çizgi ile gösterilir:</span><span class="sxs-lookup"><span data-stu-id="149f0-274">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="149f0-275">Kaydolun, e-postayı onaylayın ve parolayı sıfırlayın</span><span class="sxs-lookup"><span data-stu-id="149f0-275">Register, confirm email, and reset password</span></span>

<span data-ttu-id="149f0-276">Web uygulamasını çalıştırın ve hesap onaylama ve parola kurtarma akışını test edin.</span><span class="sxs-lookup"><span data-stu-id="149f0-276">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="149f0-277">Uygulamayı çalıştırma ve yeni bir Kullanıcı kaydetme</span><span class="sxs-lookup"><span data-stu-id="149f0-277">Run the app and register a new user</span></span>
* <span data-ttu-id="149f0-278">Hesap onay bağlantısı için e-postanızı kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="149f0-278">Check your email for the account confirmation link.</span></span> <span data-ttu-id="149f0-279">E-postayı alamazsanız [hata ayıklama e-postasına](#debug) bakın.</span><span class="sxs-lookup"><span data-stu-id="149f0-279">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="149f0-280">E-postanızı onaylamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="149f0-280">Click the link to confirm your email.</span></span>
* <span data-ttu-id="149f0-281">E-postanız ve parolanızla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="149f0-281">Sign in with your email and password.</span></span>
* <span data-ttu-id="149f0-282">Oturumunuzu kapatın.</span><span class="sxs-lookup"><span data-stu-id="149f0-282">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="149f0-283">Yönet sayfasını görüntüle</span><span class="sxs-lookup"><span data-stu-id="149f0-283">View the manage page</span></span>

<span data-ttu-id="149f0-284">Kullanıcı adınızı tarayıcıda seçin: ![ tarayıcı penceresinde Kullanıcı adı](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="149f0-284">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="149f0-285">Yönet sayfası, **profil** sekmesi seçili olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="149f0-285">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="149f0-286">**E** -postada, e-postanın onaylandığını belirten bir onay kutusu gösterilir.</span><span class="sxs-lookup"><span data-stu-id="149f0-286">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="149f0-287">Sınama parolası sıfırlama</span><span class="sxs-lookup"><span data-stu-id="149f0-287">Test password reset</span></span>

* <span data-ttu-id="149f0-288">Oturum açtıysanız **Oturumu Kapat**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="149f0-288">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="149f0-289">**Oturum aç** bağlantısını seçin ve **parolanızı unuttum?** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="149f0-289">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="149f0-290">Hesabı kaydettirmek için kullandığınız e-postayı girin.</span><span class="sxs-lookup"><span data-stu-id="149f0-290">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="149f0-291">Parolanızı sıfırlamaya yönelik bağlantı içeren bir e-posta gönderilir.</span><span class="sxs-lookup"><span data-stu-id="149f0-291">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="149f0-292">E-postanızı kontrol edin ve parolanızı sıfırlamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="149f0-292">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="149f0-293">Parolanız başarıyla sıfırlandıktan sonra, e-posta ve yeni parolanızla oturum açabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-293">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="149f0-294">E-posta ve etkinlik zaman aşımını değiştirme</span><span class="sxs-lookup"><span data-stu-id="149f0-294">Change email and activity timeout</span></span>

<span data-ttu-id="149f0-295">Varsayılan eylemsizlik zaman aşımı 14 gündür.</span><span class="sxs-lookup"><span data-stu-id="149f0-295">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="149f0-296">Aşağıdaki kod, etkinlik dışı zaman aşımını 5 güne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="149f0-296">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="149f0-297">Tüm veri koruma belirteci kullanım alanlarını Değiştir</span><span class="sxs-lookup"><span data-stu-id="149f0-297">Change all data protection token lifespans</span></span>

<span data-ttu-id="149f0-298">Aşağıdaki kod tüm veri koruma belirteçleri zaman aşımı süresini 3 saate değiştirir:</span><span class="sxs-lookup"><span data-stu-id="149f0-298">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="149f0-299">Yerleşik Identity Kullanıcı belirteçleri (bkz. [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) bir [gün zaman aşımı](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="149f0-299">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="149f0-300">E-posta belirtecini değiştir kullanım ömrü</span><span class="sxs-lookup"><span data-stu-id="149f0-300">Change the email token lifespan</span></span>

<span data-ttu-id="149f0-301">[ Identity Kullanıcı belirteçlerinin](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) varsayılan belirteç kullanım ömrü [bir gündür](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="149f0-301">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="149f0-302">Bu bölümde, eespan e-posta belirtecinin nasıl değiştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="149f0-302">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="149f0-303">Özel bir [veri korunabilir \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) ve ekleyin <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="149f0-303">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="149f0-304">Özel sağlayıcıyı hizmet kapsayıcısına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="149f0-304">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="149f0-305">E-posta onayını yeniden gönder</span><span class="sxs-lookup"><span data-stu-id="149f0-305">Resend email confirmation</span></span>

<span data-ttu-id="149f0-306">[Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/5410)bakın.</span><span class="sxs-lookup"><span data-stu-id="149f0-306">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="149f0-307">Hata ayıklama e-postası</span><span class="sxs-lookup"><span data-stu-id="149f0-307">Debug email</span></span>

<span data-ttu-id="149f0-308">E-posta ile çalışmayı alamazsanız:</span><span class="sxs-lookup"><span data-stu-id="149f0-308">If you can't get email working:</span></span>

* <span data-ttu-id="149f0-309">Doğrulamak için içinde bir kesme noktası ayarlayın `EmailSender.Execute` `SendGridClient.SendEmailAsync` .</span><span class="sxs-lookup"><span data-stu-id="149f0-309">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="149f0-310">Benzer kod kullanarak [e-posta göndermek için bir konsol uygulaması](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) oluşturun `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="149f0-310">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="149f0-311">[E-posta etkinlik](https://sendgrid.com/docs/User_Guide/email_activity.html) sayfasını gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="149f0-311">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="149f0-312">İstenmeyen posta klasörünüzü kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="149f0-312">Check your spam folder.</span></span>
* <span data-ttu-id="149f0-313">Farklı bir e-posta sağlayıcısında (Microsoft, Yahoo, Gmail vb.) başka bir e-posta diğer adı deneyin</span><span class="sxs-lookup"><span data-stu-id="149f0-313">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="149f0-314">Farklı e-posta hesaplarına göndermeyi deneyin.</span><span class="sxs-lookup"><span data-stu-id="149f0-314">Try sending to different email accounts.</span></span>

<span data-ttu-id="149f0-315">**En iyi güvenlik uygulaması** , test ve geliştirmede üretim sırlarını **kullanmamalıdır** .</span><span class="sxs-lookup"><span data-stu-id="149f0-315">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="149f0-316">Uygulamayı Azure 'a yayımlarsanız, SendGrid gizli dizilerini Azure Web App Portal 'da uygulama ayarları olarak ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-316">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="149f0-317">Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="149f0-317">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="149f0-318">Sosyal ve yerel oturum açma hesaplarını birleştirme</span><span class="sxs-lookup"><span data-stu-id="149f0-318">Combine social and local login accounts</span></span>

<span data-ttu-id="149f0-319">Bu bölümü gerçekleştirmek için önce bir dış kimlik doğrulama sağlayıcısı etkinleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="149f0-319">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="149f0-320">Bkz. [Facebook, Google ve dış sağlayıcı kimlik doğrulaması](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="149f0-320">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="149f0-321">E-posta bağlantısına tıklayarak Yerel ve sosyal hesapları birleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-321">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="149f0-322">Aşağıdaki dizide, " RickAndMSFT@gmail.com " ilk olarak yerel bir oturum açma olarak oluşturulur; ancak, önce hesabı önce sosyal oturum açma olarak oluşturabilir, ardından yerel bir oturum açma ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-322">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web uygulaması: RickAndMSFT@gmail.com kullanıcının kimliği doğrulandı](accconfirm/_static/rick.png)

<span data-ttu-id="149f0-324">**Yönet** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="149f0-324">Click on the **Manage** link.</span></span> <span data-ttu-id="149f0-325">Bu hesapla ilişkili 0 harici (sosyal oturumlar) ' a göz önüne alın.</span><span class="sxs-lookup"><span data-stu-id="149f0-325">Note the 0 external (social logins) associated with this account.</span></span>

![Görünümü Yönet](accconfirm/_static/manage.png)

<span data-ttu-id="149f0-327">Başka bir oturum açma hizmeti bağlantısına tıklayın ve uygulama isteklerini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="149f0-327">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="149f0-328">Aşağıdaki görüntüde Facebook dış kimlik doğrulama sağlayıcısıdır:</span><span class="sxs-lookup"><span data-stu-id="149f0-328">In the following image, Facebook is the external authentication provider:</span></span>

![Dış oturum açma görünümlerinizi yönetin Facebook listeleme](accconfirm/_static/fb.png)

<span data-ttu-id="149f0-330">İki hesap birleştirildi.</span><span class="sxs-lookup"><span data-stu-id="149f0-330">The two accounts have been combined.</span></span> <span data-ttu-id="149f0-331">Her iki hesapla oturum açabiliyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-331">You are able to sign in with either account.</span></span> <span data-ttu-id="149f0-332">Kullanıcılarınızın sosyal oturum açma kimlik doğrulama hizmeti kapatılmış veya sosyal hesaplarına erişimi kaybettikleri olasılığına karşı yerel hesaplar eklemesini isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="149f0-332">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="149f0-333">Bir sitede kullanıcılar varsa hesap onayını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="149f0-333">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="149f0-334">Kullanıcılara bir sitede hesap onayını etkinleştirmek, mevcut tüm kullanıcıları kilitler.</span><span class="sxs-lookup"><span data-stu-id="149f0-334">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="149f0-335">Mevcut kullanıcılar, hesapları onaylanmadığı için kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="149f0-335">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="149f0-336">Mevcut Kullanıcı kilitlemesini geçici olarak çözmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="149f0-336">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="149f0-337">Tüm mevcut kullanıcıları onaylanmış olarak işaretlemek için veritabanını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="149f0-337">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="149f0-338">Mevcut kullanıcıları onaylayın.</span><span class="sxs-lookup"><span data-stu-id="149f0-338">Confirm existing users.</span></span> <span data-ttu-id="149f0-339">Örneğin, Batch-e-postaları onay bağlantılarıyla gönderin.</span><span class="sxs-lookup"><span data-stu-id="149f0-339">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
