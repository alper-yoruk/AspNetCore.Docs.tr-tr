---
title: ASP.NET Core Azure Active Directory B2C ile bulut kimlik doğrulaması
author: camsoper
description: ASP.NET Core ile Azure Active Directory B2C kimlik doğrulamasını ayarlamayı öğrenin.
ms.author: casoper
ms.custom: devx-track-csharp, mvc
ms.date: 01/21/2019
no-loc:
- appsettings.json
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
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: f917bec8f2d929e62bf43494159a63458f135c5f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061398"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a>ASP.NET Core Azure Active Directory B2C ile bulut kimlik doğrulaması

[Cam Soper](https://twitter.com/camsoper) göre

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C), Web ve mobil uygulamalar için bir bulut kimlik yönetimi çözümüdür. Hizmet, bulutta ve şirket içinde barındırılan uygulamalar için kimlik doğrulaması sağlar. Kimlik doğrulama türleri tek tek hesapları, sosyal ağ hesaplarını ve Federasyon kurumsal hesaplarını içerir. Ayrıca, Azure AD B2C, çok faktörlü kimlik doğrulaması için en az yapılandırma sağlar.

> [!TIP]
> Azure Active Directory (Azure AD) ve Azure AD B2C ayrı ürün tekliflerdir. Bir Azure AD kiracısı bir kuruluşu temsil ettiğinde Azure AD B2C kiracı, bağlı olan taraf uygulamalarıyla kullanılacak bir kimlik koleksiyonunu temsil eder. Daha fazla bilgi için bkz. [Azure AD B2C: sık sorulan sorular (SSS)](/azure/active-directory-b2c/active-directory-b2c-faqs).

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Azure Active Directory B2C kiracı oluşturma
> * Uygulamayı Azure AD B2C kaydetme
> * Kimlik doğrulaması için Azure AD B2C kiracısını kullanacak şekilde yapılandırılmış bir ASP.NET Core Web uygulaması oluşturmak için Visual Studio 'Yu kullanma
> * Azure AD B2C kiracının davranışını denetleyen ilkeleri yapılandırın

## <a name="prerequisites"></a>Önkoşullar

Bu izlenecek yol için aşağıdakiler gereklidir:

* [Microsoft Azure aboneliği](https://azure.microsoft.com/free/dotnet/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C kiracı oluşturma

[Belgelerde açıklandığı şekilde](/azure/active-directory-b2c/active-directory-b2c-get-started)bir Azure Active Directory B2C kiracı oluşturun. İstendiğinde, kiracının bir Azure aboneliğiyle ilişkilendirilmesi Bu öğretici için isteğe bağlıdır.

## <a name="register-the-app-in-azure-ad-b2c"></a>Uygulamayı Azure AD B2C kaydetme

Yeni oluşturulan Azure AD B2C kiracısında, **Web uygulaması kaydetme** bölümünün altındaki [belgelerde bulunan adımları](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) kullanarak uygulamanızı kaydedin. **Web uygulaması istemci parolası oluşturma** bölümünde durun. Bu öğretici için bir istemci gizli anahtarı gerekli değildir. 

Aşağıdaki değerleri kullanın:

| Ayar                       | Değer                     | Notlar                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Ad**                      | *&lt;uygulama adı&gt;*        | Uygulamanızı tüketicilere açıklayan uygulama için bir **ad** girin.                                                                                                                                 |
| **Web uygulamasını / web API'sini dahil etme** | Evet                       |                                                                                                                                                                                                    |
| **Örtük akışa izin verme**       | Evet                       |                                                                                                                                                                                                    |
| **Yanıt URL'si**                 | `https://localhost:44300/signin-oidc` | Yanıt URL'leri, Azure AD B2C'nin, uygulamanız tarafından istenen belirteçleri döndürdüğü uç noktalardır. Visual Studio, kullanılacak yanıt URL 'sini sağlar. Şu an için `https://localhost:44300/signin-oidc` formunu doldurun için yazın. |
| **Uygulama Kimliği URI'si**                | Boş bırakın               | Bu öğretici için gerekli değildir.                                                                                                                                                                    |
| **Yerel istemci ekle**     | Hayır                        |                                                                                                                                                                                                    |

> [!WARNING]
> Localhost olmayan bir yanıt URL 'SI ayarlıyorsanız, [yanıt URL 'si listesinde izin verilen kısıtlamalara](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)dikkat edin. 

Uygulama kaydedildikten sonra, Kiracıdaki uygulamaların listesi görüntülenir. Yeni kaydedilen uygulamayı seçin. Pano 'ya kopyalamak için **uygulama kimliği** alanının sağ tarafındaki **Kopyala** simgesini seçin.

Şu anda Azure AD B2C kiracısında başka hiçbir şey yapılandırılamaz, ancak tarayıcı penceresini açık bırakın. ASP.NET Core uygulama oluşturulduktan sonra daha fazla yapılandırma vardır.

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Visual Studio 'da ASP.NET Core uygulaması oluşturma

Visual Studio Web uygulaması şablonu, kimlik doğrulaması için Azure AD B2C kiracısını kullanacak şekilde yapılandırılabilir.

Visual Studio 'da:

1. Yeni bir ASP.NET Core Web uygulaması oluşturun. 
2. Şablonlar listesinden **Web uygulaması** ' nı seçin.
3. **Kimlik doğrulamasını Değiştir** düğmesini seçin.
    
    ![Kimlik doğrulama düğmesini Değiştir](./azure-ad-b2c/_static/changeauth.png)

4. **Kimlik doğrulamasını Değiştir** iletişim kutusunda, **bireysel kullanıcı hesapları** ' nı seçin ve ardından açılan listede **buluttaki mevcut bir Kullanıcı deposuna Bağlan** ' ı seçin. 
    
    ![Kimlik doğrulamasını Değiştir Iletişim kutusu](./azure-ad-b2c/_static/changeauthdialog.png)

5. Formu aşağıdaki değerlerle doldurun:
    
    | Ayar                       | Değer                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Etki alanı adı**               | *&lt;B2C kiracınızın etki alanı adı&gt;*          |
    | **Uygulama Kimliği**            | *&lt;Uygulama KIMLIĞINI Panodan yapıştırma&gt;* |
    | **Geri çağırma yolu**             | *&lt;Varsayılan değeri kullan&gt;*                       |
    | **Kaydolma veya oturum açma ilkesi** | `B2C_1_SiUpIn`                                        |
    | **Parola ilkesini Sıfırla**     | `B2C_1_SSPR`                                          |
    | **Profil ilkesini Düzenle**       | *&lt;boş bırakın&gt;*                                 |
    
    Yanıt URI 'sini panoya kopyalamak için **Yanıt URI 'sinin** yanındaki **Kopyala** bağlantısını seçin. **Kimlik doğrulaması Değiştir** iletişim kutusunu kapatmak için **Tamam ' ı** seçin. Web uygulamasını oluşturmak için **Tamam ' ı** seçin.

## <a name="finish-the-b2c-app-registration"></a>B2C uygulama kaydını tamamlama

B2C uygulama özellikleri hala açık olan tarayıcı penceresine geri dönün. Daha önce belirtilen geçici **yanıt URL** 'Sini Visual Studio 'dan kopyalanmış değerle değiştirin. Pencerenin üst kısmındaki **Kaydet** ' i seçin.

> [!TIP]
> Yanıt URL 'sini kopyalamadıysanız, Web projesi özelliklerindeki hata ayıklama sekmesinden HTTPS adresini kullanın ve ' den **Callbackpath** değerini ekleyin *appsettings.json* .

## <a name="configure-policies"></a>İlkeleri yapılandırma

[Kaydolma veya oturum açma ilkesi oluşturmak](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)için Azure AD B2C belgelerindeki adımları kullanın ve ardından [bir parola sıfırlama ilkesi oluşturun](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions). **Identity Sağlayıcılar** , **kaydolma öznitelikleri** ve **uygulama talepleri** için belgelerde verilen örnek değerleri kullanın. Belgelerde açıklandığı şekilde ilkeleri test etmek için **Şimdi Çalıştır** düğmesini kullanmak isteğe bağlıdır.

> [!WARNING]
> İlke adlarının, bu ilkeler Visual Studio 'daki **kimlik doğrulaması Değiştir** iletişim kutusunda kullanıldığından emin olun. İlke adları ' de doğrulanabilir *appsettings.json* .

## <a name="configure-the-underlying-openidconnectoptionsjwtbearerno-loccookie-options"></a>Temel alınan Openıdconnectoptions/Jwttaşıyıcı/ Cookie seçeneklerini yapılandırın

Temel alınan seçenekleri doğrudan yapılandırmak için, içinde uygun düzen sabitini kullanın `Startup.ConfigureServices` :

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Visual Studio 'da **F5** ' e basarak uygulamayı derleyin ve çalıştırın. Web uygulaması başlatıldıktan sonra, (istenirse) kullanımını kabul etmek için **kabul et** ' i seçin cookie ve ardından **oturum aç** ' ı seçin.

![Uygulamada oturum açın](./azure-ad-b2c/_static/signin.png)

Tarayıcı Azure AD B2C kiracıya yeniden yönlendirir. Mevcut bir hesapla oturum açın (ilkeleri test etmeyi oluşturulduysa) veya yeni bir hesap oluşturmak için **Şimdi kaydolun** ' ı seçin. Unutulan bir parolayı sıfırlamak için **parolanızı mı unuttunuz?** bağlantısı kullanılır.

![Azure AD B2C oturumu açma](./azure-ad-b2c/_static/b2csts.png)

Başarıyla oturum açtıktan sonra tarayıcı web uygulamasına yeniden yönlendirir.

![Başarılı](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure Active Directory B2C kiracı oluşturma
> * Uygulamayı Azure AD B2C kaydetme
> * Kimlik doğrulaması için Azure AD B2C kiracısını kullanacak şekilde yapılandırılmış bir ASP.NET Core Web uygulaması oluşturmak için Visual Studio 'Yu kullanma
> * Azure AD B2C kiracının davranışını denetleyen ilkeleri yapılandırın

ASP.NET Core uygulaması kimlik doğrulaması için Azure AD B2C kullanacak şekilde yapılandırıldığına göre, uygulamanızın güvenliğini sağlamak için [Yetkilendir özniteliği](xref:security/authorization/simple) kullanılabilir. Şunları öğrenerek uygulamanızı geliştirmeye devam edin:

* [Azure AD B2C Kullanıcı arabirimini özelleştirin](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Parola karmaşıklığı gereksinimlerini yapılandırın](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Multi-Factor Authentication 'ı etkinleştirin](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)ve diğerleri gibi ek kimlik sağlayıcılarını yapılandırın.
* Azure AD B2C kiracısından grup üyeliği gibi ek kullanıcı bilgilerini almak için [Azure AD Graph API kullanın](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) .
* [Azure AD B2C kullanarak ASP.NET Core ile oluşturulmuş bir Web API 'sinin güvenliğini sağlama](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).
* [Öğretici: Azure Active Directory B2C kullanarak bir ASP.NET Web API 'sine erişim Izni verme](/azure/active-directory-b2c/tutorial-web-api-dotnet).
