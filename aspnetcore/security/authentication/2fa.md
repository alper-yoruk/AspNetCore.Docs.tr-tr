---
title: ASP.NET Core 'de SMS ile iki öğeli kimlik doğrulama
author: rick-anderson
description: ASP.NET Core bir uygulamayla iki öğeli kimlik doğrulamayı (2FA) ayarlamayı öğrenin.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/2fa
ms.openlocfilehash: 1ee9e656c2e631c9b5588149e0a75e07108baff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051271"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="b0d56-103">ASP.NET Core 'de SMS ile iki öğeli kimlik doğrulama</span><span class="sxs-lookup"><span data-stu-id="b0d56-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="b0d56-104">[Rick Anderson](https://twitter.com/RickAndMSFT) ve [Isviçre-Devs](https://github.com/Swiss-Devs) tarafından</span><span class="sxs-lookup"><span data-stu-id="b0d56-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="b0d56-105">Zamana bağlı bir kerelik parola algoritması (TOTP) kullanan iki öğeli kimlik doğrulama (2FA) Authenticator uygulaması, 2FA için önerilen sektördür.</span><span class="sxs-lookup"><span data-stu-id="b0d56-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="b0d56-106">2. TOTP kullanan 2FA, SMS 2FA için tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="b0d56-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="b0d56-107">Daha fazla bilgi için bkz. ASP.NET Core 2,0 ve üzeri için [ASP.NET Core ' de TOTP Authenticator uygulamaları IÇIN QR kodu oluşturmayı etkinleştirme](xref:security/authentication/identity-enable-qrcodes) .</span><span class="sxs-lookup"><span data-stu-id="b0d56-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="b0d56-108">Bu öğreticide, SMS kullanılarak iki öğeli kimlik doğrulamasının (2FA) nasıl ayarlanacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="b0d56-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="b0d56-109">Yönergeler [Twilio](https://www.twilio.com/) ve [aspsms](https://www.aspsms.com/asp.net/identity/core/testcredits/)için verilmiştir, ancak başka herhangi bir SMS sağlayıcısını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b0d56-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="b0d56-110">Bu Öğreticiyi başlatmadan önce [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm) işleminin tamamlanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="b0d56-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="b0d56-111">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span><span class="sxs-lookup"><span data-stu-id="b0d56-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="b0d56-112">[Nasıl indirileceği](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b0d56-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="b0d56-113">Yeni bir ASP.NET Core projesi oluştur</span><span class="sxs-lookup"><span data-stu-id="b0d56-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="b0d56-114">Bireysel kullanıcı hesaplarıyla adlı yeni bir ASP.NET Core Web uygulaması oluşturun `Web2FA` .</span><span class="sxs-lookup"><span data-stu-id="b0d56-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="b0d56-115"><xref:security/enforcing-ssl>Https 'yi ayarlamak ve istemek için içindeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="b0d56-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="b0d56-116">SMS hesabı oluşturma</span><span class="sxs-lookup"><span data-stu-id="b0d56-116">Create an SMS account</span></span>

<span data-ttu-id="b0d56-117">Örneğin, [Twilio](https://www.twilio.com/) veya [aspsms](https://www.aspsms.com/asp.net/identity/core/testcredits/)adresinden bir SMS hesabı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b0d56-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="b0d56-118">Kimlik doğrulama bilgilerini kaydedin (Twilio: Accountsıd ve authToken için, ASPSMS: userKey ve Password için).</span><span class="sxs-lookup"><span data-stu-id="b0d56-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="b0d56-119">SMS sağlayıcısı kimlik bilgileri alınıyor</span><span class="sxs-lookup"><span data-stu-id="b0d56-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="b0d56-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="b0d56-120">**Twilio:**</span></span>

<span data-ttu-id="b0d56-121">Twilio hesabınızın Pano sekmesinden **Hesap SID** 'Sini ve **kimlik doğrulama belirtecini** kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="b0d56-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token** .</span></span>

<span data-ttu-id="b0d56-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="b0d56-122">**ASPSMS:**</span></span>

<span data-ttu-id="b0d56-123">Hesap ayarlarınızda **userKey** ' e gidin ve **parolanızla** birlikte kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="b0d56-123">From your account settings, navigate to **Userkey** and copy it together with your **Password** .</span></span>

<span data-ttu-id="b0d56-124">Bu değerleri daha sonra anahtarlar içindeki gizli-Manager aracıyla birlikte depolayacağız `SMSAccountIdentification` `SMSAccountPassword` .</span><span class="sxs-lookup"><span data-stu-id="b0d56-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="b0d56-125">SenderId/oluşturana belirtme</span><span class="sxs-lookup"><span data-stu-id="b0d56-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="b0d56-126">**Twilio:** Sayılar sekmesinden Twilio **telefon numaranızı** kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="b0d56-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number** .</span></span>

<span data-ttu-id="b0d56-127">**Aspsms:** Kilit açma/kaldırma menüsünde, bir veya daha fazla kaynaktan yararlanın veya alfasayısal bir kaynağı (tüm ağlar tarafından desteklenmez) seçin.</span><span class="sxs-lookup"><span data-stu-id="b0d56-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="b0d56-128">Bu değeri daha sonra anahtar içinde gizli-Manager aracıyla depolayacağız `SMSAccountFrom` .</span><span class="sxs-lookup"><span data-stu-id="b0d56-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="b0d56-129">SMS hizmeti için kimlik bilgilerini belirtin</span><span class="sxs-lookup"><span data-stu-id="b0d56-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="b0d56-130">Kullanıcı hesabına ve anahtar ayarlarına erişmek için [Seçenekler modelini](xref:fundamentals/configuration/options) kullanacağız.</span><span class="sxs-lookup"><span data-stu-id="b0d56-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="b0d56-131">Güvenli SMS anahtarını getirmek için bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b0d56-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="b0d56-132">Bu örnekte, `SMSoptions` sınıfı *Services/smsoptions. cs* dosyasında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b0d56-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="b0d56-133">`SMSAccountIdentification`, Ve öğesini `SMSAccountPassword` `SMSAccountFrom` [gizli-Manager aracı](xref:security/app-secrets)ile ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b0d56-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="b0d56-134">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b0d56-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="b0d56-135">SMS sağlayıcısı için NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b0d56-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="b0d56-136">Paket Yöneticisi konsolundan (PMC) Çalıştır:</span><span class="sxs-lookup"><span data-stu-id="b0d56-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="b0d56-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="b0d56-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="b0d56-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="b0d56-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="b0d56-139">SMS 'yi etkinleştirmek için *Services/MessageServices. cs* dosyasına kod ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b0d56-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="b0d56-140">Twilio ya da ASPSMS bölümünü kullanın:</span><span class="sxs-lookup"><span data-stu-id="b0d56-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="b0d56-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="b0d56-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="b0d56-142">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="b0d56-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="b0d56-143">Başlangıç 'yi kullanacak şekilde yapılandırma `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="b0d56-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="b0d56-144">`SMSoptions` `ConfigureServices` *Startup.cs* içindeki yönteminde hizmet kapsayıcısına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b0d56-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs* :</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="b0d56-145">İki öğeli kimlik doğrulamayı etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="b0d56-145">Enable two-factor authentication</span></span>

<span data-ttu-id="b0d56-146">*Views/Manage/Index. cshtml* :::no-loc(Razor)::: Görünüm dosyasını açın ve açıklama karakterlerini kaldırın (Bu nedenle biçimlendirme yok).</span><span class="sxs-lookup"><span data-stu-id="b0d56-146">Open the *Views/Manage/Index.cshtml* :::no-loc(Razor)::: view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="b0d56-147">İki öğeli kimlik doğrulama ile oturum açma</span><span class="sxs-lookup"><span data-stu-id="b0d56-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="b0d56-148">Uygulamayı çalıştırma ve yeni bir Kullanıcı kaydetme</span><span class="sxs-lookup"><span data-stu-id="b0d56-148">Run the app and register a new user</span></span>

![Web uygulaması kayıt görünümü Microsoft Edge 'de açık](2fa/_static/login2fa1.png)

* <span data-ttu-id="b0d56-150">Kullanıcı adına dokunarak, `Index` Yönetim denetleyicisindeki eylem yöntemini etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="b0d56-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="b0d56-151">Ardından telefon numarası **Ekle** bağlantısına dokunun.</span><span class="sxs-lookup"><span data-stu-id="b0d56-151">Then tap the phone number **Add** link.</span></span>

![Yönetme görünümü-"Ekle" bağlantısına dokunun](2fa/_static/login2fa2.png)

* <span data-ttu-id="b0d56-153">Doğrulama kodunu alacak bir telefon numarası ekleyin ve **doğrulama kodu gönder** ' e dokunun.</span><span class="sxs-lookup"><span data-stu-id="b0d56-153">Add a phone number that will receive the verification code, and tap **Send verification code** .</span></span>

![Telefon numarası Ekle sayfası](2fa/_static/login2fa3.png)

* <span data-ttu-id="b0d56-155">Doğrulama kodunu içeren bir kısa mesaj alacaksınız.</span><span class="sxs-lookup"><span data-stu-id="b0d56-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="b0d56-156">Girin ve **Gönder** 'e dokunun</span><span class="sxs-lookup"><span data-stu-id="b0d56-156">Enter it and tap **Submit**</span></span>

![Telefon numarası sayfasını doğrula](2fa/_static/login2fa4.png)

<span data-ttu-id="b0d56-158">Kısa mesaj alamazsanız, bkz. Twilio günlük sayfası.</span><span class="sxs-lookup"><span data-stu-id="b0d56-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="b0d56-159">Yönet görünümü telefon numaranızı başarıyla eklendiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="b0d56-159">The Manage view shows your phone number was added successfully.</span></span>

![Görünümü Yönet-telefon numarası başarıyla eklendi](2fa/_static/login2fa5.png)

* <span data-ttu-id="b0d56-161">İki öğeli kimlik doğrulamayı etkinleştirmek için **Etkinleştir** ' e dokunun.</span><span class="sxs-lookup"><span data-stu-id="b0d56-161">Tap **Enable** to enable two-factor authentication.</span></span>

![Görünümü Yönet-iki öğeli kimlik doğrulamayı etkinleştir](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="b0d56-163">İki öğeli kimlik doğrulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="b0d56-163">Test two-factor authentication</span></span>

* <span data-ttu-id="b0d56-164">Oturumu kapatın.</span><span class="sxs-lookup"><span data-stu-id="b0d56-164">Log off.</span></span>

* <span data-ttu-id="b0d56-165">Oturum açın.</span><span class="sxs-lookup"><span data-stu-id="b0d56-165">Log in.</span></span>

* <span data-ttu-id="b0d56-166">Kullanıcı hesabı iki öğeli kimlik doğrulamayı etkinleştirdi, bu nedenle ikinci kimlik doğrulama faktörünü sağlamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="b0d56-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="b0d56-167">Bu öğreticide, telefon doğrulamasını etkinleştirdiniz.</span><span class="sxs-lookup"><span data-stu-id="b0d56-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="b0d56-168">Yerleşik şablonlar, e-postayı ikinci faktör olarak ayarlamanıza de olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="b0d56-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="b0d56-169">QR kodları gibi kimlik doğrulaması için ek ikinci faktörleri ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b0d56-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="b0d56-170">**Gönder** ' e dokunun.</span><span class="sxs-lookup"><span data-stu-id="b0d56-170">Tap **Submit** .</span></span>

![Doğrulama kodu görünümü gönder](2fa/_static/login2fa7.png)

* <span data-ttu-id="b0d56-172">SMS iletisinde aldığınız kodu girin.</span><span class="sxs-lookup"><span data-stu-id="b0d56-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="b0d56-173">**Bu tarayıcıya** göz atma onay kutusunu tıklatmak, aynı cihaz ve tarayıcıyı kullanırken oturum açmak için 2FA kullanmaya gerek duymasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="b0d56-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="b0d56-174">2FA 'yı etkinleştirmek ve **Bu tarayıcıyı unutmamak** , cihazınıza erişimi olmadığı sürece hesabınıza erişmeye çalışan kötü amaçlı kullanıcılardan güçlü 2FA koruması sağlar.</span><span class="sxs-lookup"><span data-stu-id="b0d56-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="b0d56-175">Bunu, düzenli olarak kullandığınız tüm özel cihazlarda yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b0d56-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="b0d56-176">**Bu tarayıcıyı aklınızda** bulundurarak, düzenli olarak kullanmayan CIHAZLARDAN 2FA 'nın ek güvenliğine sahip olursunuz ve kendi cihazlarınızda 2FA 'yı kullanmaya gerek kalmadan rahatlığını elde edersiniz.</span><span class="sxs-lookup"><span data-stu-id="b0d56-176">By setting  **Remember this browser** , you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![Görünümü doğrula](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="b0d56-178">Deneme yanılma saldırılarına karşı koruma için hesap kilitleme</span><span class="sxs-lookup"><span data-stu-id="b0d56-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="b0d56-179">Hesap kilitleme, 2FA ile önerilir.</span><span class="sxs-lookup"><span data-stu-id="b0d56-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="b0d56-180">Bir kullanıcı yerel bir hesap veya sosyal hesap aracılığıyla oturum açtığında, 2FA 'da başarısız olan her girişim depolanır.</span><span class="sxs-lookup"><span data-stu-id="b0d56-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="b0d56-181">En fazla başarısız erişim denemesine ulaşıldığında, Kullanıcı kilitlenir (varsayılan: 5 dakikalık kilitleme girişiminden sonra 5 dakika kilitleme).</span><span class="sxs-lookup"><span data-stu-id="b0d56-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="b0d56-182">Başarılı bir kimlik doğrulaması başarısız erişim denemesi sayısını sıfırlar ve saati sıfırlar.</span><span class="sxs-lookup"><span data-stu-id="b0d56-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="b0d56-183">Başarısız olan en fazla erişim denemesi sayısı ve kilitleme süresi [Maxfailedaccessattempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) ve [Defaultlockouttimespan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)ile ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="b0d56-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="b0d56-184">Aşağıdakiler 10 dakikalık erişim denemesinden sonra hesap kilitlemeyi 10 dakika sonra yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="b0d56-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="b0d56-185">[Passwordsignınasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) kümelerinin şu şekilde olduğunu onaylayın `lockoutOnFailure` `true` :</span><span class="sxs-lookup"><span data-stu-id="b0d56-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
