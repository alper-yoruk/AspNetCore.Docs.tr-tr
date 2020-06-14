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
ms.openlocfilehash: 8d4488b3953a8c87033d3a092b656409a0c6a52d
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724373"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>ASP.NET Core hesap onaylama ve parola kurtarma

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)ve [ali Audette](https://twitter.com/joeaudette)

Bu öğreticide, e-posta onayı ve parola sıfırlama ile bir ASP.NET Core uygulamasının nasıl oluşturulacağı gösterilmektedir. Bu öğretici bir başlangıç konusu **değildir** . Şunu tanımanız gerekir:

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [Kimlik Doğrulaması](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

ASP.NET Core 1,1 sürümü için [Bu PDF dosyasına](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) bakın.

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a>Ön koşullar

[.NET Core 3,0 SDK veya üzeri](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a>Kimlik doğrulamasıyla bir Web uygulaması oluşturma ve test etme

Kimlik doğrulamasıyla bir Web uygulaması oluşturmak için aşağıdaki komutları çalıştırın.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

Uygulamayı çalıştırın, **Kaydet** bağlantısını seçin ve bir Kullanıcı kaydedin. Kaydolduktan sonra, `/Identity/Account/RegisterConfirmation` e-posta onayı benzetimi için bir bağlantı içeren to sayfasına yönlendirilirsiniz:

* Bağlantıyı seçin `Click here to confirm your account` .
* **Oturum açma** bağlantısını seçin ve aynı kimlik bilgileriyle oturum açın.
* `Hello YourEmail@provider.com!`Sizi sayfaya yönlendiren bağlantıyı seçin `/Identity/Account/Manage/PersonalData` .
* Sol taraftaki **kişisel veri** sekmesini seçin ve **Sil**' i seçin.

### <a name="configure-an-email-provider"></a>E-posta sağlayıcısı yapılandırma

Bu öğreticide, [SendGrid](https://sendgrid.com) e-posta göndermek için kullanılır. E-posta göndermek için bir SendGrid hesabına ve anahtarına ihtiyacınız vardır. Diğer e-posta sağlayıcılarını kullanabilirsiniz. E-posta göndermek için SendGrid veya başka bir e-posta hizmeti kullanmanızı öneririz. Güvenli hale getirmek ve düzgün şekilde ayarlamak zordur.

SendGrid hesabı, [gönderici ekleme](https://sendgrid.com/docs/ui/sending-email/senders/)gerektirebilir.

Güvenli e-posta anahtarını getirmek için bir sınıf oluşturun. Bu örnek için *Hizmetler/Authiletienderoptions. cs*oluşturun:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>SendGrid Kullanıcı gizli dizilerini yapılandırma

Ve öğesini `SendGridUser` `SendGridKey` [gizli-Manager aracı](xref:security/app-secrets)ile ayarlayın. Örnek:

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Windows 'da gizli dizi, anahtar/değer çiftlerini dizindeki bir *secrets.js* bir dosyada depolar `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` .

Dosyadaki *secrets.js* içeriği şifrelenmedi. Aşağıdaki biçimlendirme dosyadaki *secrets.js* gösterir. `SendGridKey`Değer kaldırılmıştır.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Daha fazla bilgi için bkz. [Options model](xref:fundamentals/configuration/options) ve [yapılandırma](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>SendGrid 'i yükler

Bu öğretici, [SendGrid](https://sendgrid.com/)aracılığıyla e-posta bildirimlerinin nasıl ekleneceğini gösterir, ancak SMTP ve diğer mekanizmaları kullanarak e-posta gönderebilirsiniz.

`SendGrid`NuGet paketini yükler:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Paket Yöneticisi konsolundan, aşağıdaki komutu girin:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Konsolundan aşağıdaki komutu girin:

```dotnetcli
dotnet add package SendGrid
```

---

Ücretsiz SendGrid hesabına kaydolmak için bkz. [SendGrid Ile çalışmaya başlayın](https://sendgrid.com/free/) .

### <a name="implement-iemailsender"></a>Iemailsender uygulama

Uygulamak için `IEmailSender` , aşağıdakine benzer bir kodla *Hizmetler/emailsender. cs* oluşturun:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Başlat 'ı e-postayı destekleyecek şekilde yapılandırma

Aşağıdaki kodu `ConfigureServices` *Startup.cs* dosyasındaki yöntemine ekleyin:

* `EmailSender`Geçici hizmet olarak ekleyin.
* `AuthMessageSenderOptions`Yapılandırma örneğini kaydedin.

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a>Kaydolun, e-postayı onaylayın ve parolayı sıfırlayın

Web uygulamasını çalıştırın ve hesap onaylama ve parola kurtarma akışını test edin.

* Uygulamayı çalıştırma ve yeni bir Kullanıcı kaydetme
* Hesap onay bağlantısı için e-postanızı kontrol edin. E-postayı alamazsanız [hata ayıklama e-postasına](#debug) bakın.
* E-postanızı onaylamak için bağlantıya tıklayın.
* E-postanız ve parolanızla oturum açın.
* Oturumunuzu kapatın.

### <a name="test-password-reset"></a>Sınama parolası sıfırlama

* Oturum açtıysanız **Oturumu Kapat**' ı seçin.
* **Oturum aç** bağlantısını seçin ve **parolanızı unuttum?** bağlantısını seçin.
* Hesabı kaydettirmek için kullandığınız e-postayı girin.
* Parolanızı sıfırlamaya yönelik bağlantı içeren bir e-posta gönderilir. E-postanızı kontrol edin ve parolanızı sıfırlamak için bağlantıya tıklayın. Parolanız başarıyla sıfırlandıktan sonra, e-posta ve yeni parolanızla oturum açabilirsiniz.

## <a name="change-email-and-activity-timeout"></a>E-posta ve etkinlik zaman aşımını değiştirme

Varsayılan eylemsizlik zaman aşımı 14 gündür. Aşağıdaki kod, etkinlik dışı zaman aşımını 5 güne ayarlar:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Tüm veri koruma belirteci kullanım alanlarını Değiştir

Aşağıdaki kod tüm veri koruma belirteçleri zaman aşımı süresini 3 saate değiştirir:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

Yerleşik Identity Kullanıcı belirteçleri (bkz. [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) bir [gün zaman aşımı](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>E-posta belirtecini değiştir kullanım ömrü

[ Identity Kullanıcı belirteçlerinin](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) varsayılan belirteç kullanım ömrü [bir gündür](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). Bu bölümde, eespan e-posta belirtecinin nasıl değiştirileceği gösterilmektedir.

Özel bir [veri korunabilir \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) ve ekleyin <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Özel sağlayıcıyı hizmet kapsayıcısına ekleyin:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a>E-posta onayını yeniden gönder

[Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/5410)bakın.

<a name="debug"></a>

### <a name="debug-email"></a>Hata ayıklama e-postası

E-posta ile çalışmayı alamazsanız:

* Doğrulamak için içinde bir kesme noktası ayarlayın `EmailSender.Execute` `SendGridClient.SendEmailAsync` .
* Benzer kod kullanarak [e-posta göndermek için bir konsol uygulaması](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) oluşturun `EmailSender.Execute` .
* [E-posta etkinlik](https://sendgrid.com/docs/User_Guide/email_activity.html) sayfasını gözden geçirin.
* İstenmeyen posta klasörünüzü kontrol edin.
* Farklı bir e-posta sağlayıcısında (Microsoft, Yahoo, Gmail vb.) başka bir e-posta diğer adı deneyin
* Farklı e-posta hesaplarına göndermeyi deneyin.

**En iyi güvenlik uygulaması** , test ve geliştirmede üretim sırlarını **kullanmamalıdır** . Uygulamayı Azure 'a yayımlarsanız, SendGrid gizli dizilerini Azure Web App Portal 'da uygulama ayarları olarak ayarlayın. Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.

## <a name="combine-social-and-local-login-accounts"></a>Sosyal ve yerel oturum açma hesaplarını birleştirme

Bu bölümü gerçekleştirmek için önce bir dış kimlik doğrulama sağlayıcısı etkinleştirmeniz gerekir. Bkz. [Facebook, Google ve dış sağlayıcı kimlik doğrulaması](xref:security/authentication/social/index).

E-posta bağlantısına tıklayarak Yerel ve sosyal hesapları birleştirebilirsiniz. Aşağıdaki dizide, " RickAndMSFT@gmail.com " ilk olarak yerel bir oturum açma olarak oluşturulur; ancak, önce hesabı önce sosyal oturum açma olarak oluşturabilir, ardından yerel bir oturum açma ekleyebilirsiniz.

![Web uygulaması: RickAndMSFT@gmail.com kullanıcının kimliği doğrulandı](accconfirm/_static/rick.png)

**Yönet** bağlantısına tıklayın. Bu hesapla ilişkili 0 harici (sosyal oturumlar) ' a göz önüne alın.

![Görünümü Yönet](accconfirm/_static/manage.png)

Başka bir oturum açma hizmeti bağlantısına tıklayın ve uygulama isteklerini kabul edin. Aşağıdaki görüntüde Facebook dış kimlik doğrulama sağlayıcısıdır:

![Dış oturum açma görünümlerinizi yönetin Facebook listeleme](accconfirm/_static/fb.png)

İki hesap birleştirildi. Her iki hesapla oturum açabiliyor olabilirsiniz. Kullanıcılarınızın sosyal oturum açma kimlik doğrulama hizmeti kapatılmış veya sosyal hesaplarına erişimi kaybettikleri olasılığına karşı yerel hesaplar eklemesini isteyebilirsiniz.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Bir sitede kullanıcılar varsa hesap onayını etkinleştir

Kullanıcılara bir sitede hesap onayını etkinleştirmek, mevcut tüm kullanıcıları kilitler. Mevcut kullanıcılar, hesapları onaylanmadığı için kilitlenir. Mevcut Kullanıcı kilitlemesini geçici olarak çözmek için aşağıdaki yaklaşımlardan birini kullanın:

* Tüm mevcut kullanıcıları onaylanmış olarak işaretlemek için veritabanını güncelleştirin.
* Mevcut kullanıcıları onaylayın. Örneğin, Batch-e-postaları onay bağlantılarıyla gönderin.

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a>Ön koşullar

[.NET Core 2,2 SDK veya üzeri](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a>Web uygulaması ve yapı iskelesi oluşturmaIdentity

Kimlik doğrulamasıyla bir Web uygulaması oluşturmak için aşağıdaki komutları çalıştırın.

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

## <a name="test-new-user-registration"></a>Yeni Kullanıcı kaydını sına

Uygulamayı çalıştırın, **Kaydet** bağlantısını seçin ve bir Kullanıcı kaydedin. Bu noktada, e-postadaki tek doğrulama [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) özniteliğine sahiptir. Kayıt gönderildikten sonra uygulamada oturum açarsınız. Daha sonra öğreticide, yeni kullanıcıların e-postaları doğrulanmadan oturum açması için kod güncellenir.

[!INCLUDE[](~/includes/view-identity-db.md)]

Tablonun `EmailConfirmed` alanının olduğunu aklınızda edin `False` .

Uygulama bir onay e-postası gönderdiğinde bir sonraki adımda bu e-postayı yeniden kullanmak isteyebilirsiniz. Satıra sağ tıklayın ve **Sil**' i seçin. E-posta diğer adını silmek aşağıdaki adımlarda daha kolay hale gelir.

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>E-posta onayı gerektir

Yeni bir Kullanıcı kaydının e-postasını onaylamak en iyi uygulamadır. E-posta onayı, başkalarının taklit etmeyeceğinden emin olmaya yardımcı olur (diğer bir deyişle, başka birinin e-postasına kaydolmamaları). Bir tartışma forumuna sahip olduğunuzu ve "" öğesini yli@example.com "" olarak kaydolmasını engellemek istediğinizi varsayalım nolivetto@contoso.com . E-posta onayı olmadan, " nolivetto@contoso.com " uygulamanızdan istenmeyen e-posta alabilir. Kullanıcının yanlışlıkla "" olarak kaydolduğunu ylo@example.com ve "yıllı" hatası olduğunu fark edelim. Uygulamanın doğru e-postası olmadığından parola kurtarma kullanamayacak. E-posta onayı, robotlardan sınırlı koruma sağlar. E-posta onayı çok sayıda e-posta hesabına sahip kötü amaçlı kullanıcılardan koruma sağlamaz.

Genellikle yeni kullanıcıların, onaylanan bir e-posta almadan önce Web sitenize veri almasını engellemek istersiniz.

`Startup.ConfigureServices`Onaylanan bir e-posta gerektirecek şekilde güncelleştir:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;`kayıtlı kullanıcıların e-postaları onaylanana kadar oturum açmasını önler.

### <a name="configure-email-provider"></a>E-posta sağlayıcısını Yapılandır

Bu öğreticide, [SendGrid](https://sendgrid.com) e-posta göndermek için kullanılır. E-posta göndermek için bir SendGrid hesabına ve anahtarına ihtiyacınız vardır. Diğer e-posta sağlayıcılarını kullanabilirsiniz. ASP.NET Core 2. x içerir `System.Net.Mail` . Bu, uygulamanızdan e-posta göndermenize olanak tanır. E-posta göndermek için SendGrid veya başka bir e-posta hizmeti kullanmanızı öneririz. Güvenli hale getirmek ve düzgün şekilde ayarlamak zordur.

Güvenli e-posta anahtarını getirmek için bir sınıf oluşturun. Bu örnek için *Hizmetler/Authiletienderoptions. cs*oluşturun:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>SendGrid Kullanıcı gizli dizilerini yapılandırma

Ve öğesini `SendGridUser` `SendGridKey` [gizli-Manager aracı](xref:security/app-secrets)ile ayarlayın. Örnek:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Windows 'da gizli dizi, anahtar/değer çiftlerini dizindeki bir *secrets.js* bir dosyada depolar `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` .

Dosyadaki *secrets.js* içeriği şifrelenmedi. Aşağıdaki biçimlendirme dosyadaki *secrets.js* gösterir. `SendGridKey`Değer kaldırılmıştır.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Daha fazla bilgi için bkz. [Options model](xref:fundamentals/configuration/options) ve [yapılandırma](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>SendGrid 'i yükler

Bu öğretici, [SendGrid](https://sendgrid.com/)aracılığıyla e-posta bildirimlerinin nasıl ekleneceğini gösterir, ancak SMTP ve diğer mekanizmaları kullanarak e-posta gönderebilirsiniz.

`SendGrid`NuGet paketini yükler:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Paket Yöneticisi konsolundan, aşağıdaki komutu girin:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Konsolundan aşağıdaki komutu girin:

```dotnetcli
dotnet add package SendGrid
```

---

Ücretsiz SendGrid hesabına kaydolmak için bkz. [SendGrid Ile çalışmaya başlayın](https://sendgrid.com/free/) .

### <a name="implement-iemailsender"></a>Iemailsender uygulama

Uygulamak için `IEmailSender` , aşağıdakine benzer bir kodla *Hizmetler/emailsender. cs* oluşturun:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Başlat 'ı e-postayı destekleyecek şekilde yapılandırma

Aşağıdaki kodu `ConfigureServices` *Startup.cs* dosyasındaki yöntemine ekleyin:

* `EmailSender`Geçici hizmet olarak ekleyin.
* `AuthMessageSenderOptions`Yapılandırma örneğini kaydedin.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>Hesap onaylama ve parola kurtarmayı etkinleştirme

Şablonda hesap onaylama ve parola kurtarma için kod bulunur. `OnPostAsync`Yöntemini */ Identity /Pages/Account/Register.cshtml.cs*içinde bulun.

Yeni kayıtlı kullanıcıların aşağıdaki satırı açıklama ekleyerek otomatik olarak oturum açmasını engelleyin:

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

Tüm Yöntem vurgulanmış olan değiştirilen çizgi ile gösterilir:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>Kaydolun, e-postayı onaylayın ve parolayı sıfırlayın

Web uygulamasını çalıştırın ve hesap onaylama ve parola kurtarma akışını test edin.

* Uygulamayı çalıştırma ve yeni bir Kullanıcı kaydetme
* Hesap onay bağlantısı için e-postanızı kontrol edin. E-postayı alamazsanız [hata ayıklama e-postasına](#debug) bakın.
* E-postanızı onaylamak için bağlantıya tıklayın.
* E-postanız ve parolanızla oturum açın.
* Oturumunuzu kapatın.

### <a name="view-the-manage-page"></a>Yönet sayfasını görüntüle

Kullanıcı adınızı tarayıcıda seçin: ![ tarayıcı penceresinde Kullanıcı adı](accconfirm/_static/un.png)

Yönet sayfası, **profil** sekmesi seçili olarak görüntülenir. **E** -postada, e-postanın onaylandığını belirten bir onay kutusu gösterilir.

### <a name="test-password-reset"></a>Sınama parolası sıfırlama

* Oturum açtıysanız **Oturumu Kapat**' ı seçin.
* **Oturum aç** bağlantısını seçin ve **parolanızı unuttum?** bağlantısını seçin.
* Hesabı kaydettirmek için kullandığınız e-postayı girin.
* Parolanızı sıfırlamaya yönelik bağlantı içeren bir e-posta gönderilir. E-postanızı kontrol edin ve parolanızı sıfırlamak için bağlantıya tıklayın. Parolanız başarıyla sıfırlandıktan sonra, e-posta ve yeni parolanızla oturum açabilirsiniz.

## <a name="change-email-and-activity-timeout"></a>E-posta ve etkinlik zaman aşımını değiştirme

Varsayılan eylemsizlik zaman aşımı 14 gündür. Aşağıdaki kod, etkinlik dışı zaman aşımını 5 güne ayarlar:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Tüm veri koruma belirteci kullanım alanlarını Değiştir

Aşağıdaki kod tüm veri koruma belirteçleri zaman aşımı süresini 3 saate değiştirir:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

Yerleşik Identity Kullanıcı belirteçleri (bkz. [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) bir [gün zaman aşımı](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>E-posta belirtecini değiştir kullanım ömrü

[ Identity Kullanıcı belirteçlerinin](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) varsayılan belirteç kullanım ömrü [bir gündür](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). Bu bölümde, eespan e-posta belirtecinin nasıl değiştirileceği gösterilmektedir.

Özel bir [veri korunabilir \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) ve ekleyin <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Özel sağlayıcıyı hizmet kapsayıcısına ekleyin:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>E-posta onayını yeniden gönder

[Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/5410)bakın.

<a name="debug"></a>

### <a name="debug-email"></a>Hata ayıklama e-postası

E-posta ile çalışmayı alamazsanız:

* Doğrulamak için içinde bir kesme noktası ayarlayın `EmailSender.Execute` `SendGridClient.SendEmailAsync` .
* Benzer kod kullanarak [e-posta göndermek için bir konsol uygulaması](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) oluşturun `EmailSender.Execute` .
* [E-posta etkinlik](https://sendgrid.com/docs/User_Guide/email_activity.html) sayfasını gözden geçirin.
* İstenmeyen posta klasörünüzü kontrol edin.
* Farklı bir e-posta sağlayıcısında (Microsoft, Yahoo, Gmail vb.) başka bir e-posta diğer adı deneyin
* Farklı e-posta hesaplarına göndermeyi deneyin.

**En iyi güvenlik uygulaması** , test ve geliştirmede üretim sırlarını **kullanmamalıdır** . Uygulamayı Azure 'a yayımlarsanız, SendGrid gizli dizilerini Azure Web App Portal 'da uygulama ayarları olarak ayarlayabilirsiniz. Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.

## <a name="combine-social-and-local-login-accounts"></a>Sosyal ve yerel oturum açma hesaplarını birleştirme

Bu bölümü gerçekleştirmek için önce bir dış kimlik doğrulama sağlayıcısı etkinleştirmeniz gerekir. Bkz. [Facebook, Google ve dış sağlayıcı kimlik doğrulaması](xref:security/authentication/social/index).

E-posta bağlantısına tıklayarak Yerel ve sosyal hesapları birleştirebilirsiniz. Aşağıdaki dizide, " RickAndMSFT@gmail.com " ilk olarak yerel bir oturum açma olarak oluşturulur; ancak, önce hesabı önce sosyal oturum açma olarak oluşturabilir, ardından yerel bir oturum açma ekleyebilirsiniz.

![Web uygulaması: RickAndMSFT@gmail.com kullanıcının kimliği doğrulandı](accconfirm/_static/rick.png)

**Yönet** bağlantısına tıklayın. Bu hesapla ilişkili 0 harici (sosyal oturumlar) ' a göz önüne alın.

![Görünümü Yönet](accconfirm/_static/manage.png)

Başka bir oturum açma hizmeti bağlantısına tıklayın ve uygulama isteklerini kabul edin. Aşağıdaki görüntüde Facebook dış kimlik doğrulama sağlayıcısıdır:

![Dış oturum açma görünümlerinizi yönetin Facebook listeleme](accconfirm/_static/fb.png)

İki hesap birleştirildi. Her iki hesapla oturum açabiliyor olabilirsiniz. Kullanıcılarınızın sosyal oturum açma kimlik doğrulama hizmeti kapatılmış veya sosyal hesaplarına erişimi kaybettikleri olasılığına karşı yerel hesaplar eklemesini isteyebilirsiniz.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Bir sitede kullanıcılar varsa hesap onayını etkinleştir

Kullanıcılara bir sitede hesap onayını etkinleştirmek, mevcut tüm kullanıcıları kilitler. Mevcut kullanıcılar, hesapları onaylanmadığı için kilitlenir. Mevcut Kullanıcı kilitlemesini geçici olarak çözmek için aşağıdaki yaklaşımlardan birini kullanın:

* Tüm mevcut kullanıcıları onaylanmış olarak işaretlemek için veritabanını güncelleştirin.
* Mevcut kullanıcıları onaylayın. Örneğin, Batch-e-postaları onay bağlantılarıyla gönderin.

::: moniker-end
