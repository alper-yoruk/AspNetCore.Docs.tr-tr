---
title: ASP.NET Core 'daki TOTP Authenticator uygulamaları için QR kodu oluşturmayı etkinleştirme
author: rick-anderson
description: ASP.NET Core iki öğeli kimlik doğrulamasıyla çalışan TOTP Authenticator uygulamaları için QR kod üretimini nasıl etkinleştireceğinizi öğrenin.
ms.author: riande
ms.date: 08/14/2018
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
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: b778e7238911ec9966edf7f0f7becd113b1e197a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060839"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="f9865-103">ASP.NET Core 'daki TOTP Authenticator uygulamaları için QR kodu oluşturmayı etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="f9865-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="f9865-104">QR kodları ASP.NET Core 2,0 veya üstünü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f9865-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f9865-105">ASP.NET Core, bireysel kimlik doğrulama için kimlik doğrulayıcı uygulamaları desteğiyle birlikte gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f9865-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="f9865-106">Zamana bağlı bir kerelik parola algoritması (TOTP) kullanan iki öğeli kimlik doğrulama (2FA) Authenticator uygulaması, 2FA için önerilen sektördür.</span><span class="sxs-lookup"><span data-stu-id="f9865-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="f9865-107">2. TOTP kullanan 2FA, SMS 2FA için tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="f9865-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="f9865-108">Bir Authenticator uygulaması, kullanıcıların kullanıcı adını ve parolasını onayladıktan sonra girmesi gereken 6 ' dan 8 basamaklı bir kod sağlar.</span><span class="sxs-lookup"><span data-stu-id="f9865-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="f9865-109">Genellikle bir akıllı telefona bir kimlik doğrulayıcı uygulaması yüklenir.</span><span class="sxs-lookup"><span data-stu-id="f9865-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="f9865-110">ASP.NET Core Web uygulaması şablonları, kimlik doğrulayıcılar destekler, ancak QRCode üretimi için destek sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="f9865-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="f9865-111">QRCode üreteçleri, 2FA kurulumunu kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="f9865-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="f9865-112">Bu belge, 2FA yapılandırma sayfasına [QR kod](https://wikipedia.org/wiki/QR_code) üretimi ekleme konusunda size kılavuzluk eder.</span><span class="sxs-lookup"><span data-stu-id="f9865-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="f9865-113">İki öğeli kimlik doğrulaması, [Google](xref:security/authentication/google-logins) veya [Facebook](xref:security/authentication/facebook-logins)gibi bir dış kimlik doğrulama sağlayıcısı kullanılarak gerçekleşmez.</span><span class="sxs-lookup"><span data-stu-id="f9865-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="f9865-114">Dış oturumlar, dış oturum açma sağlayıcısının sağladığı mekanizmaya karşı korunur.</span><span class="sxs-lookup"><span data-stu-id="f9865-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="f9865-115">Örneğin, [Microsoft](xref:security/authentication/microsoft-logins) kimlik doğrulama sağlayıcısı bir donanım anahtarı ya da başka bir 2FA yaklaşımı gerektirdiğini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="f9865-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="f9865-116">Varsayılan Şablonlar "yerel" 2FA uyguladığında, kullanıcıların yaygın olarak kullanılan bir senaryo olmayan iki 2FA yaklaşımının yerine getirmek gerekir.</span><span class="sxs-lookup"><span data-stu-id="f9865-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="f9865-117">2FA yapılandırma sayfasına QR kodları ekleme</span><span class="sxs-lookup"><span data-stu-id="f9865-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="f9865-118">Bu yönergeler depoyu *qrcode.js* kullanır https://davidshimjs.github.io/qrcodejs/ .</span><span class="sxs-lookup"><span data-stu-id="f9865-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="f9865-119">[qrcode.js JavaScript kitaplığını](https://davidshimjs.github.io/qrcodejs/) `wwwroot\lib` projenizdeki klasöre indirin.</span><span class="sxs-lookup"><span data-stu-id="f9865-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="f9865-120">*/Areas/ Identity /Pages/Account/Manage/enabledoğru* [ Identity](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="f9865-120">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml* .</span></span>
* <span data-ttu-id="f9865-121">*/Areas/ Identity /Pages/Account/Manage/enabledoğrulayıcısı Tor.exe* içinde, `Scripts` dosyanın sonundaki bölümü bulun:</span><span class="sxs-lookup"><span data-stu-id="f9865-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml* , locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="f9865-122">*Sayfalar/hesap/Yönet/EnableAuthenticator. cshtml* ( Razor Sayfalar) veya *Görünümler/Yönet/enableauthenticator. cshtml* (MVC) içinde, `Scripts` dosyanın sonundaki bölümü bulun:</span><span class="sxs-lookup"><span data-stu-id="f9865-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="f9865-123">`Scripts` `qrcodejs` Eklediğiniz kitaplığa bir başvuru ve QR kodu oluşturma çağrısı eklemek için bölümü güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="f9865-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="f9865-124">Aşağıdaki gibi görünmelidir:</span><span class="sxs-lookup"><span data-stu-id="f9865-124">It should look as follows:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* <span data-ttu-id="f9865-125">Bu yönergelere bağlanan paragrafı silin.</span><span class="sxs-lookup"><span data-stu-id="f9865-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="f9865-126">Uygulamanızı çalıştırın ve QR kodunu taramanızı ve kimlik doğrulayıcı ile ilgili kodu doğrulayabilmek için emin olun.</span><span class="sxs-lookup"><span data-stu-id="f9865-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="f9865-127">QR kodundaki site adını değiştirme</span><span class="sxs-lookup"><span data-stu-id="f9865-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f9865-128">QR kodundaki site adı, projenizi ilk kez oluştururken seçtiğiniz proje adından alınır.</span><span class="sxs-lookup"><span data-stu-id="f9865-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="f9865-129">`GenerateQrCodeUri(string email, string unformattedKey)` */Areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml.cs* içindeki yöntemi arayarak bunu değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f9865-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs* .</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="f9865-130">QR kodundaki site adı, projenizi ilk kez oluştururken seçtiğiniz proje adından alınır.</span><span class="sxs-lookup"><span data-stu-id="f9865-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="f9865-131">Bunu, `GenerateQrCodeUri(string email, string unformattedKey)` *Pages/Account/Manage/enableauthenticator. cshtml. cs* ( Razor Pages) dosyasında ya da *Controllers/managecontroller. cs* (MVC) dosyasındaki yöntemine bakarak değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f9865-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f9865-132">Şablondaki varsayılan kod aşağıdaki gibi görünür:</span><span class="sxs-lookup"><span data-stu-id="f9865-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="f9865-133">Çağrısındaki ikinci parametre `string.Format` , çözüm adından alınan sitenizin adıdır.</span><span class="sxs-lookup"><span data-stu-id="f9865-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="f9865-134">Herhangi bir değere değiştirilebilir, ancak her zaman URL kodlamalı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f9865-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="f9865-135">Farklı bir QR kod kitaplığı kullanma</span><span class="sxs-lookup"><span data-stu-id="f9865-135">Using a different QR Code library</span></span>

<span data-ttu-id="f9865-136">QR kod kitaplığı 'nı tercih ettiğiniz kitaplıkla değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f9865-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="f9865-137">HTML, `qrCode` kitaplığınızın sağladığı mekanizmaya BIR QR kodu yerleştirebileceğiniz bir öğesi içerir.</span><span class="sxs-lookup"><span data-stu-id="f9865-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="f9865-138">QR kodu için doğru şekilde biçimlendirilen URL şu şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="f9865-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="f9865-139">`AuthenticatorUri` Model özelliği.</span><span class="sxs-lookup"><span data-stu-id="f9865-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="f9865-140">`data-url``qrCodeData`öğesinde özelliği.</span><span class="sxs-lookup"><span data-stu-id="f9865-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="f9865-141">TOTP istemci ve sunucu saati eğriltme</span><span class="sxs-lookup"><span data-stu-id="f9865-141">TOTP client and server time skew</span></span>

<span data-ttu-id="f9865-142">TOTP (zamana dayalı One-Time parolası) kimlik doğrulaması, hem sunucu hem de Doğrulayıcı cihazına doğru bir zamana sahip olur.</span><span class="sxs-lookup"><span data-stu-id="f9865-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="f9865-143">Belirteçler yalnızca en az 30 saniye için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f9865-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="f9865-144">TOTP 2FA oturum açma işlemleri başarısız olursa, sunucu saatinin doğru olduğundan ve tercihen doğru bir NTP hizmeti ile eşitlendiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="f9865-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
