---
title: ASP.NET Çekirdek Kimlik Doğrulamaya Genel Bakış
author: mjrousos
description: ASP.NET Core'da kimlik doğrulama hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
uid: security/authentication/index
ms.openlocfilehash: 404904ecfa30d1fe7e47f0daaa423ddd6f1b06e8
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434336"
---
# <a name="overview-of-aspnet-core-authentication"></a>ASP.NET Çekirdek kimlik doğrulamaya genel bakış

Yazar: [Mike Rousos](https://github.com/mjrousos)

Kimlik doğrulama, kullanıcının kimliğini belirleme işlemidir. [Yetkilendirme,](xref:security/authorization/introduction) bir kullanıcının kaynağa erişimi olup olmadığını belirleme işlemidir. ASP.NET Core'da kimlik `IAuthenticationService`doğrulama, kimlik doğrulama [aracı](xref:fundamentals/middleware/index)tarafından kullanılan Kimlik doğrulama hizmeti, kimlik doğrulamayla ilgili eylemleri tamamlamak için kayıtlı kimlik doğrulama işleyicilerini kullanır. Kimlik doğrulamayla ilgili eylemlere örnek olarak şunlar verilebilir:

* Bir kullanıcının kimliğini doğrulama.
* Kimliği doğrulanmamış bir kullanıcı kısıtlı bir kaynağa erişmeye çalıştığında yanıt verir.

Kayıtlı kimlik doğrulama işleyicileri ve yapılandırma seçenekleri "düzenleri" olarak adlandırılır.

Kimlik doğrulama şemaları, kimlik doğrulama hizmetlerini `Startup.ConfigureServices`şu şekilde kaydederek belirtilir:

* Bir çağrıdan sonra şemaya özgü `services.AddAuthentication` bir uzatma `AddJwtBearer` `AddCookie`yöntemini arayarak (örneğin). Bu uzantı yöntemleri, uygun ayarlara sahip şemaları kaydetmek için [AuthenticationBuilder.AddScheme'i](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) kullanır.
* Daha az yaygın olarak, [Doğrudan AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) arayarak.

Örneğin, aşağıdaki kod, tanımlama bilgisi ve JWT taşıyıcı kimlik doğrulama düzenleri için kimlik doğrulama hizmetlerini ve işleyicilerini kaydeder:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

`AddAuthentication` Parametre, `JwtBearerDefaults.AuthenticationScheme` belirli bir düzen istenmediğinde varsayılan olarak kullanılacak şemanın adıdır.

Birden çok düzen kullanılıyorsa, yetkilendirme ilkeleri (veya yetkilendirme öznitelikleri), kullanıcının kimliğini doğrulamak için bağlı oldukları [kimlik doğrulama düzenini (veya şemaları) belirtebilir.](xref:security/authorization/limitingidentitybyscheme) Yukarıdaki örnekte, çerez kimlik doğrulama düzeni adını belirterek`CookieAuthenticationDefaults.AuthenticationScheme` kullanılabilir (varsayılan olarak, arama yaparken `AddCookie`farklı bir ad sağlanabilir).

Bazı durumlarda, arama `AddAuthentication` otomatik olarak diğer uzantı yöntemleri tarafından yapılır. Örneğin, ASP.NET [Çekirdek Kimlik](xref:security/authentication/identity) `AddAuthentication` kullanırken, dahili olarak adlandırılır.

Kimlik Doğrulama aracı, uygulamanın uzantısı yöntemini `Startup.Configure` <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> arayarak `IApplicationBuilder`eklenir. Arama, `UseAuthentication` daha önce kaydedilmiş kimlik doğrulama düzenlerini kullanan ara yazılımı kaydeder. Kullanıcıların `UseAuthentication` kimliğine bağlı olarak herhangi bir ara yazılımdan önce arayın. Uç nokta yönlendirmesini kullanırken, `UseAuthentication` çağrının gitmesi gerekir:

* Sonra `UseRouting`, böylece rota bilgileri kimlik doğrulama kararları için kullanılabilir.
* Önce `UseEndpoints`, böylece kullanıcıların bitiş noktalarına erişmeden önce kimlik doğrulaması yapılır.

## <a name="authentication-concepts"></a>Kimlik Doğrulama Kavramları

### <a name="authentication-scheme"></a>Kimlik doğrulama şeması

Kimlik doğrulama düzeni, aşağıdakilere karşılık gelen bir addır:

* Kimlik doğrulama işleyicisi.
* Işleyicinin belirli bir örneğini yapılandırma seçenekleri.

Şemalar, ilişkili işleyicinin kimlik doğrulamasına, meydan okumasına ve yasaklayıcı davranışlarına atıfta bulunan bir mekanizma olarak yararlıdır. Örneğin, bir yetkilendirme ilkesi, kullanıcının kimliğini doğrulamak için hangi kimlik doğrulama düzeninin (veya şemalar) kullanılması gerektiğini belirtmek için şema adlarını kullanabilir. Kimlik doğrulaması yapılandırırken, varsayılan kimlik doğrulama düzenini belirtmek yaygındır. Kaynak belirli bir düzen talep etmediği sürece varsayılan düzen kullanılır. Ayrıca şunları yapmak da mümkündür:

* Kimlik doğrulaması, meydan okuma ve eylemleri yasaklamak için kullanılacak farklı varsayılan düzenleri belirtin.
* İlke [şemaları](xref:security/authentication/policyschemes)kullanarak birden çok düzeni tek bir şema da birleştirin.

### <a name="authentication-handler"></a>Kimlik doğrulama işleyicisi

Kimlik doğrulama işleyicisi:

* Bir düzenin davranışını uygulayan bir türdür.
* Türetilmiştir <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>veya .
* Kullanıcıların kimliğini doğrulamak için birincil sorumluluğa sahiptir.

Kimlik doğrulama düzeninin yapılandırmasını ve gelen istek bağlamını temel alan kimlik doğrulama işleyicileri:

* Kimlik <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> doğrulaması başarılı olursa kullanıcının kimliğini temsil eden nesneler oluştur.
* Kimlik doğrulama başarısız olursa 'sonuç yok' veya 'başarısız' döndürün.
* Kullanıcılar kaynaklara erişmeye çalıştıklarında meydan okuma ve eylemleri yasaklama yöntemlerine sahip olabilir:
  * Erişim iznine sahipler (yasaklar).
  * Onlar doğrulanmamış olduğunda (meydan).

### <a name="authenticate"></a>Kimlik doğrulaması

Kimlik doğrulama düzeninin kimlik doğrulama eylemi, kullanıcının kimliğini istek bağlamına göre oluşturmakla yükümlüdür. Kimlik doğrulamanın başarılı olup olmadığını ve varsa bir kimlik doğrulama biletinde kullanıcının kimliğini <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> gösteren bir işaret verir. Bkz. <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>. Kimlik doğrulama örnekleri şunlardır:

* Tanımlama bilgilerinden kullanıcı kimliğini oluşturan bir çerez kimlik doğrulama düzeni.
* Kullanıcının kimliğini oluşturmak için bir JWT taşıyıcı belirteci deserializing ve doğrulayan bir JWT taşıyıcı düzeni.

### <a name="challenge"></a>Sınama

Kimlik doğrulaması, kimlik doğrulaması olmayan bir kullanıcı kimlik doğrulaması gerektiren bir bitiş noktası istediğinde Yetkilendirme tarafından çağrılır. Örneğin, anonim bir kullanıcı kısıtlı bir kaynak istediğinde veya oturum açma bağlantısını tıklattığında kimlik doğrulama sorunu düzenlenir. Yetkilendirme, belirtilen kimlik doğrulama düzenini(ler) veya hiçbiri belirtilmemişse varsayılanı kullanarak bir meydan okuma çağırır. Bkz. <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>. Kimlik doğrulama zorluğu örnekleri şunlardır:

* Kullanıcıyı oturum açma sayfasına yönlendiren bir çerez kimlik doğrulama düzeni.
* Bir JWT taşıyıcı düzeni bir `www-authenticate: bearer` üstbilgi ile 401 sonuç dönen.

Bir meydan okuma eylemi, kullanıcıya istenen kaynağa erişmek için hangi kimlik doğrulama mekanizmasını kullanacağını bildirmelidir.

### <a name="forbid"></a>Koru -sun

Kimlik doğrulama düzeninin yasaklama eylemi, kimlik doğrulaması yapılan bir kullanıcı erişmelerine izin verilen olmayan bir kaynağa erişmeye çalıştığında Yetkilendirme tarafından çağrılır. Bkz. <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>. Kimlik doğrulama yıkıntMa örnekleri şunlardır:
* Kullanıcıyı erişimin yasak olduğunu belirten bir sayfaya yönlendiren çerez kimlik doğrulama düzeni.
* Bir JWT taşıyıcı düzeni 403 sonuç döndürücü.
* Özel kimlik doğrulama düzeni, kullanıcının kaynağa erişim isteyebileceği bir sayfaya yönlendirilmesi.

Yasak bir eylem kullanıcıya şunu bildirebilir:

* Kimlik doğrulanmışlar.
* İstenen kaynağa erişmelerine izin verilmez.

Meydan okuma ve yasaklama arasındaki farklar için aşağıdaki bağlantılara bakın:

* [Bir operasyonel kaynak işleyicisi ile meydan okuma ve yasaklama.](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler)
* [Meydan okuma ve yasaklama arasındaki farklar.](xref:security/authorization/secure-data#challenge)

## <a name="authentication-providers-per-tenant"></a>Kiracı başına kimlik doğrulama sağlayıcıları

ASP.NET Core çerçevesi çok kiracılı kimlik doğrulaması için yerleşik bir çözüme sahip değildir.
Müşterilerin yerleşik özellikleri kullanarak bir tane yazması kesinlikle mümkün olsa da, müşterilerin bu amaçla [Orchard Core'a](https://www.orchardcore.net/) bakmalarını öneririz.

Orchard Core:

* ASP.NET Core ile oluşturulmuş açık kaynak kodlu modüler ve çok kiracılı bir uygulama çerçevesi.
* Bu uygulama çerçevesinin üzerine kurulmuş bir içerik yönetim sistemi (CMS).

Kiracı başına kimlik doğrulama sağlayıcıları örneği için [Orchard Core](https://github.com/OrchardCMS/OrchardCore) kaynağına bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
