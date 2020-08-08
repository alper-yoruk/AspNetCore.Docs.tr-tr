---
title: ASP.NET Core 'de SMS ile iki öğeli kimlik doğrulama
author: rick-anderson
description: ASP.NET Core bir uygulamayla iki öğeli kimlik doğrulamayı (2FA) ayarlamayı öğrenin.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/2fa
ms.openlocfilehash: 28aef65234eaf162ba6e18a2594feb575c93b02f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019503"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>ASP.NET Core 'de SMS ile iki öğeli kimlik doğrulama

[Rick Anderson](https://twitter.com/RickAndMSFT) ve [Isviçre-Devs](https://github.com/Swiss-Devs) tarafından

>[!WARNING]
> Zamana bağlı bir kerelik parola algoritması (TOTP) kullanan iki öğeli kimlik doğrulama (2FA) Authenticator uygulaması, 2FA için önerilen sektördür. 2. TOTP kullanan 2FA, SMS 2FA için tercih edilir. Daha fazla bilgi için bkz. ASP.NET Core 2,0 ve üzeri için [ASP.NET Core ' de TOTP Authenticator uygulamaları IÇIN QR kodu oluşturmayı etkinleştirme](xref:security/authentication/identity-enable-qrcodes) .

Bu öğreticide, SMS kullanılarak iki öğeli kimlik doğrulamasının (2FA) nasıl ayarlanacağı gösterilmektedir. Yönergeler [Twilio](https://www.twilio.com/) ve [aspsms](https://www.aspsms.com/asp.net/identity/core/testcredits/)için verilmiştir, ancak başka herhangi bir SMS sağlayıcısını kullanabilirsiniz. Bu Öğreticiyi başlatmadan önce [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm) işleminin tamamlanmasını öneririz.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA). [Nasıl indirileceği](xref:index#how-to-download-a-sample).

## <a name="create-a-new-aspnet-core-project"></a>Yeni bir ASP.NET Core projesi oluştur

Bireysel kullanıcı hesaplarıyla adlı yeni bir ASP.NET Core Web uygulaması oluşturun `Web2FA` . <xref:security/enforcing-ssl>Https 'yi ayarlamak ve istemek için içindeki yönergeleri izleyin.

### <a name="create-an-sms-account"></a>SMS hesabı oluşturma

Örneğin, [Twilio](https://www.twilio.com/) veya [aspsms](https://www.aspsms.com/asp.net/identity/core/testcredits/)adresinden bir SMS hesabı oluşturun. Kimlik doğrulama bilgilerini kaydedin (Twilio: Accountsıd ve authToken için, ASPSMS: userKey ve Password için).

#### <a name="figuring-out-sms-provider-credentials"></a>SMS sağlayıcısı kimlik bilgileri alınıyor

**Twilio**

Twilio hesabınızın Pano sekmesinden **Hesap SID** 'Sini ve **kimlik doğrulama belirtecini**kopyalayın.

**ASPSMS:**

Hesap ayarlarınızda **userKey** ' e gidin ve **parolanızla**birlikte kopyalayın.

Bu değerleri daha sonra anahtarlar içindeki gizli-Manager aracıyla birlikte depolayacağız `SMSAccountIdentification` `SMSAccountPassword` .

#### <a name="specifying-senderid--originator"></a>SenderId/oluşturana belirtme

**Twilio:** Sayılar sekmesinden Twilio **telefon numaranızı**kopyalayın.

**Aspsms:** Kilit açma/kaldırma menüsünde, bir veya daha fazla kaynaktan yararlanın veya alfasayısal bir kaynağı (tüm ağlar tarafından desteklenmez) seçin.

Bu değeri daha sonra anahtar içinde gizli-Manager aracıyla depolayacağız `SMSAccountFrom` .

### <a name="provide-credentials-for-the-sms-service"></a>SMS hizmeti için kimlik bilgilerini belirtin

Kullanıcı hesabına ve anahtar ayarlarına erişmek için [Seçenekler modelini](xref:fundamentals/configuration/options) kullanacağız.

* Güvenli SMS anahtarını getirmek için bir sınıf oluşturun. Bu örnekte, `SMSoptions` sınıfı *Services/smsoptions. cs* dosyasında oluşturulur.

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

`SMSAccountIdentification`, Ve öğesini `SMSAccountPassword` `SMSAccountFrom` [gizli-Manager aracı](xref:security/app-secrets)ile ayarlayın. Örnek:

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* SMS sağlayıcısı için NuGet paketini ekleyin. Paket Yöneticisi konsolundan (PMC) Çalıştır:

**Twilio**

`Install-Package Twilio`

**ASPSMS:**

`Install-Package ASPSMS`

* SMS 'yi etkinleştirmek için *Services/MessageServices. cs* dosyasına kod ekleyin. Twilio ya da ASPSMS bölümünü kullanın:

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>Başlangıç 'yi kullanacak şekilde yapılandırma`SMSoptions`

`SMSoptions` `ConfigureServices` *Startup.cs*içindeki yönteminde hizmet kapsayıcısına ekleyin:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>İki öğeli kimlik doğrulamayı etkinleştirme

*Views/Manage/Index. cshtml* Razor Görünüm dosyasını açın ve açıklama karakterlerini kaldırın (Bu nedenle biçimlendirme yok).

## <a name="log-in-with-two-factor-authentication"></a>İki öğeli kimlik doğrulama ile oturum açma

* Uygulamayı çalıştırma ve yeni bir Kullanıcı kaydetme

![Web uygulaması kayıt görünümü Microsoft Edge 'de açık](2fa/_static/login2fa1.png)

* Kullanıcı adına dokunarak, `Index` Yönetim denetleyicisindeki eylem yöntemini etkinleştirir. Ardından telefon numarası **Ekle** bağlantısına dokunun.

![Yönetme görünümü-"Ekle" bağlantısına dokunun](2fa/_static/login2fa2.png)

* Doğrulama kodunu alacak bir telefon numarası ekleyin ve **doğrulama kodu gönder**' e dokunun.

![Telefon numarası Ekle sayfası](2fa/_static/login2fa3.png)

* Doğrulama kodunu içeren bir kısa mesaj alacaksınız. Girin ve **Gönder** 'e dokunun

![Telefon numarası sayfasını doğrula](2fa/_static/login2fa4.png)

Kısa mesaj alamazsanız, bkz. Twilio günlük sayfası.

* Yönet görünümü telefon numaranızı başarıyla eklendiğini gösterir.

![Görünümü Yönet-telefon numarası başarıyla eklendi](2fa/_static/login2fa5.png)

* İki öğeli kimlik doğrulamayı etkinleştirmek için **Etkinleştir** ' e dokunun.

![Görünümü Yönet-iki öğeli kimlik doğrulamayı etkinleştir](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>İki öğeli kimlik doğrulamayı test etme

* Oturumu kapatın.

* Oturum açın.

* Kullanıcı hesabı iki öğeli kimlik doğrulamayı etkinleştirdi, bu nedenle ikinci kimlik doğrulama faktörünü sağlamanız gerekir. Bu öğreticide, telefon doğrulamasını etkinleştirdiniz. Yerleşik şablonlar, e-postayı ikinci faktör olarak ayarlamanıza de olanak tanır. QR kodları gibi kimlik doğrulaması için ek ikinci faktörleri ayarlayabilirsiniz. **Gönder**' e dokunun.

![Doğrulama kodu görünümü gönder](2fa/_static/login2fa7.png)

* SMS iletisinde aldığınız kodu girin.

* **Bu tarayıcıya** göz atma onay kutusunu tıklatmak, aynı cihaz ve tarayıcıyı kullanırken oturum açmak için 2FA kullanmaya gerek duymasını sağlar. 2FA 'yı etkinleştirmek ve **Bu tarayıcıyı unutmamak** , cihazınıza erişimi olmadığı sürece hesabınıza erişmeye çalışan kötü amaçlı kullanıcılardan güçlü 2FA koruması sağlar. Bunu, düzenli olarak kullandığınız tüm özel cihazlarda yapabilirsiniz. **Bu tarayıcıyı aklınızda**bulundurarak, düzenli olarak kullanmayan CIHAZLARDAN 2FA 'nın ek güvenliğine sahip olursunuz ve kendi cihazlarınızda 2FA 'yı kullanmaya gerek kalmadan rahatlığını elde edersiniz.

![Görünümü doğrula](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>Deneme yanılma saldırılarına karşı koruma için hesap kilitleme

Hesap kilitleme, 2FA ile önerilir. Bir kullanıcı yerel bir hesap veya sosyal hesap aracılığıyla oturum açtığında, 2FA 'da başarısız olan her girişim depolanır. En fazla başarısız erişim denemesine ulaşıldığında, Kullanıcı kilitlenir (varsayılan: 5 dakikalık kilitleme girişiminden sonra 5 dakika kilitleme). Başarılı bir kimlik doğrulaması başarısız erişim denemesi sayısını sıfırlar ve saati sıfırlar. Başarısız olan en fazla erişim denemesi sayısı ve kilitleme süresi [Maxfailedaccessattempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) ve [Defaultlockouttimespan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)ile ayarlanabilir. Aşağıdakiler 10 dakikalık erişim denemesinden sonra hesap kilitlemeyi 10 dakika sonra yapılandırır:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

[Passwordsignınasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) kümelerinin şu şekilde olduğunu onaylayın `lockoutOnFailure` `true` :

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
