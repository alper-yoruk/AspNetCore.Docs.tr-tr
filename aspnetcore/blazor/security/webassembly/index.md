---
title: Güvenli ASP.NET Core Blazor WebAssembly
author: guardrex
description: BlazorWebassemlby uygulamalarını tek sayfalı uygulamalar (maça 'lar) olarak güvenli hale getirme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/16/2020
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: 391a38e9af42b162fc74bac803e6aa3cf687e7d5
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626070"
---
# <a name="secure-aspnet-core-no-locblazor-webassembly"></a>Güvenli ASP.NET Core Blazor WebAssembly

Sağlayan [Javier Calvarro Nelson](https://github.com/javiercn)

Blazor WebAssembly uygulamalar, tek sayfalı uygulamalarla (maça 'Lar) aynı şekilde güvenli hale getirilir. Kullanıcıların maça üzerinde kimlik doğrulaması için birkaç yaklaşım vardır, ancak en yaygın ve kapsamlı yaklaşım, [OpenID Connect (OıDC)](https://openid.net/connect/)gibi [OAuth 2,0 protokolüne](https://oauth.net/)dayalı bir uygulama kullanmaktır.

## <a name="authentication-library"></a>Kimlik doğrulama kitaplığı

Blazor WebAssembly , kitaplık aracılığıyla OıDC kullanarak uygulamalara kimlik doğrulama ve yetkilendirme işlemini destekler [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) . Kitaplığı ASP.NET Core arka uçlara karşı sorunsuz kimlik doğrulama için bir dizi temel sağlar. Kitaplık, ASP.NET Core Identity [ Identity sunucu](https://identityserver.io/)üzerinde oluşturulan API yetkilendirme desteğiyle tümleştirilir. Kitaplık, Identity OpenID sağlayıcıları (OP) olarak adlandırılan OIDC 'yi destekleyen herhangi bir üçüncü taraf sağlayıcıya (IP) kimlik doğrulaması yapabilir.

İçindeki kimlik doğrulama desteği, Blazor WebAssembly `oidc-client.js` temeldeki kimlik doğrulama protokolü ayrıntılarını işlemek için kullanılan kitaplığın üzerine kurulmuştur.

Benzer site s kullanımı gibi, maça 'Lar için kimlik doğrulama seçenekleri mevcuttur cookie . Ancak, ' nin mühendislik tasarımı, Blazor WebAssembly uygulamalarda kimlik doğrulaması için en iyi seçenek olarak OAuth ve OıDC üzerinde kapatılmıştır Blazor WebAssembly . JSON Web belirteçlerini temel alan [belirteç tabanlı kimlik doğrulaması](xref:security/anti-request-forgery#token-based-authentication) [(jwts)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) , işlevsel ve güvenlik nedenleriyle [ cookie tabanlı kimlik doğrulaması](xref:security/anti-request-forgery#cookie-based-authentication) için seçilmiştir:

* Belirteç tabanlı bir protokol kullanmak, belirteçler tüm isteklerde gönderilmediğinden daha küçük bir saldırı yüzeyi alanı sunar.
* Belirteçleri açıkça gönderildiğinden, sunucu uç noktaları [siteler arası Istek sahteciliği (CSRF)](xref:security/anti-request-forgery) için koruma gerektirmez. Bu, Blazor WebAssembly MVC veya Pages uygulamalarıyla birlikte uygulamaları barındırmanıza olanak tanır Razor .
* Belirteçlerin şundan daha dar izinleri var cookie . Örneğin, belirteçler Kullanıcı hesabını yönetmek ya da bu işlevsellik açık bir şekilde uygulanmadığı takdirde kullanıcının parolasını değiştirmek için kullanılamaz.
* Belirteçler, varsayılan olarak bir saat, saldırı penceresini sınırlayan kısa bir yaşam süresine sahiptir. Belirteçler de herhangi bir zamanda iptal edilebilir.
* İstemci ve sunucu için kimlik doğrulama işlemiyle ilgili olarak kendi içinde bulunan JWTs teklifi garantisi. Örneğin, bir istemci, aldığı belirteçlerin meşru olduğunu ve belirli bir kimlik doğrulama işleminin bir parçası olarak yayıldığını tespit etmek ve doğrulamak anlamına gelir. Üçüncü bir taraf, kimlik doğrulama işleminin ortasında bir belirteci geçirmeyi denerse, istemci anahtarlamalı belirteci algılayabilir ve kullanmaktan kaçınabilir.
* OAuth ve OıDC içeren belirteçler, uygulamanın güvenli olduğundan emin olmak için doğru şekilde davranan kullanıcı aracısına güvenmeyin.
* OAuth ve OıDC gibi belirteç tabanlı protokoller aynı güvenlik özellikleriyle barındırılan ve tek başına uygulamaların kimliğini doğrulamaya ve yetkilendirmeye olanak tanır.

## <a name="authentication-process-with-oidc"></a>OıDC ile kimlik doğrulama işlemi

[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)Kitaplık, OıDC kullanarak kimlik doğrulaması ve yetkilendirme uygulamak için çeşitli temel olanaklar sunar. Büyük şartlar altında, kimlik doğrulaması aşağıdaki gibi çalışmaktadır:

* Anonim Kullanıcı oturum açma düğmesini seçtiğinde veya özniteliği uygulanmış bir sayfa istediğinde [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) , kullanıcı uygulamanın oturum açma sayfasına ( `/authentication/login` ) yönlendirilir.
* Oturum açma sayfasında, kimlik doğrulama kitaplığı yetkilendirme uç noktasına yeniden yönlendirme hazırlar. Yetkilendirme uç noktası, Blazor WebAssembly uygulamanın dışındadır ve ayrı bir kaynak üzerinde barındırılabilir. Uç nokta, kullanıcının kimliğinin yapılıp yapılmayacağını ve yanıt olarak bir veya daha fazla belirteç vermeyi belirlemekten sorumludur. Kimlik doğrulama kitaplığı, kimlik doğrulama yanıtını almak için bir oturum açma geri araması sağlar.
  * Kullanıcının kimliği doğrulanmadıysa, Kullanıcı, genellikle temel alınan kimlik doğrulama sistemine yönlendirilir ASP.NET Core Identity .
  * Kullanıcının kimliği zaten doğrulandıktan sonra, yetkilendirme uç noktası uygun belirteçleri oluşturur ve tarayıcıyı, oturum açma geri çağırma uç noktasına () yeniden yönlendirir `/authentication/login-callback` .
* Uygulama, Blazor WebAssembly oturum açma geri çağırma uç noktasını ( `/authentication/login-callback` ) yüklediğinde, kimlik doğrulama yanıtı işlenir.
  * Kimlik doğrulama işlemi başarıyla tamamlanırsa, kullanıcının kimliği doğrulanır ve isteğe bağlı olarak kullanıcının istediği özgün URL 'ye geri gönderilir.
  * Kimlik doğrulama işlemi herhangi bir nedenle başarısız olursa, Kullanıcı oturum açma başarısız sayfasına ( `/authentication/login-failed` ) gönderilir ve bir hata görüntülenir.

## <a name="authentication-component"></a>`Authentication` bileşeni

`Authentication`Bileşen ( `Pages/Authentication.razor` ) uzak kimlik doğrulama işlemlerini işler ve uygulamanın şunları kullanmasına izin verir:

* Kimlik doğrulama durumları için uygulama yollarını yapılandırın.
* Kimlik doğrulama durumları için Kullanıcı arabirimi içeriğini ayarlayın.
* Kimlik doğrulama durumunu yönetin.

Bir kullanıcıya kaydolma veya oturum açma gibi kimlik doğrulama eylemleri, Blazor Framework <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> bileşenine geçirilir ve bu da kimlik doğrulama işlemlerinde durum denetimi devam eder.

Daha fazla bilgi ve örnek için bkz <xref:blazor/security/webassembly/additional-scenarios> ..

## <a name="authorization"></a>Yetkilendirme

Blazor WebAssemblyUygulamalarda, tüm istemci tarafı kodlar kullanıcılar tarafından değiştirilemediği için, yetkilendirme denetimleri atlanabilir. Aynı, JavaScript SPA çerçeveleri veya herhangi bir işletim sistemi için yerel uygulamalar dahil olmak üzere tüm istemci tarafı uygulama teknolojileri için de geçerlidir.

**İstemci tarafı uygulamanız tarafından erişilen tüm API uç noktalarında sunucuda her zaman yetkilendirme denetimleri gerçekleştirin.**

## <a name="require-authorization-for-the-entire-app"></a>Tüm uygulama için yetkilendirme iste

Aşağıdaki yaklaşımlardan birini kullanarak, [ `[Authorize]` özniteliği](xref:blazor/security/index#authorize-attribute) ([API belgeleri](xref:System.Web.Mvc.AuthorizeAttribute)) Razor uygulamanın her bileşenine uygulayın:

* [`@attribute`](xref:mvc/views/razor#attribute)Dosyadaki yönergeyi kullanın `_Imports.razor` :

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* Özniteliği klasöründeki her bileşene ekleyin Razor `Pages` .

> [!NOTE]
> <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType>İle ilke olarak ayarlanması <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> desteklenmez. **not**

## <a name="refresh-tokens"></a>Belirteçleri Yenile

Yenileme belirteçleri uygulamalarda istemci tarafında güvenli olamaz Blazor WebAssembly . Bu nedenle, doğrudan kullanım için yenileme belirteçleri uygulamaya gönderilmez.

Yenileme belirteçleri, Blazor WebAssembly üçüncü taraf API 'lerine erişmek Için barındırılan bir çözümde sunucu tarafı uygulama tarafından korunabilir ve kullanılabilir. Daha fazla bilgi için bkz. <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.

## <a name="implementation-guidance"></a>Uygulama Kılavuzu

Bu *genel bakışın* altındaki makaleler, Blazor WebAssembly uygulamalarda kullanıcılara belirli sağlayıcılara göre kimlik doğrulaması hakkında bilgiler sağlar.

Tek başına Blazor WebAssembly uygulamalar:

* [OıDC sağlayıcıları ve WebAssembly kimlik doğrulama kitaplığı için genel kılavuz](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [Microsoft Hesapları](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

Barındırılan Blazor WebAssembly uygulamalar:

* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [Identity Server](xref:blazor/security/webassembly/hosted-with-identity-server)

Yapılandırma hakkında daha fazla bilgi için bkz <xref:blazor/security/webassembly/additional-scenarios> ..
