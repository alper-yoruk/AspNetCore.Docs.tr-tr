---
title: Yetkilendirme ile korunan kullanıcı verileriyle ASP.NET Core uygulama oluşturma
author: rick-anderson
description: Yetkilendirmeyle korunan kullanıcı verileriyle ASP.NET Core bir Web uygulaması oluşturmayı öğrenin. HTTPS, kimlik doğrulaması, güvenlik, içerir ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: 5f86e514ee6339888171d83ab3117e9b3fcf107e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627825"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a>Yetkilendirmeyle korunan kullanıcı verileriyle ASP.NET Core bir Web uygulaması oluşturma

By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [ali Audette](https://twitter.com/joeaudette)

::: moniker range="= aspnetcore-2.0"

[Bu PDF 'ye](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) bakın

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Bu öğreticide, yetkilendirme tarafından korunan kullanıcı verileriyle ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı gösterilmektedir. Kimliği doğrulanmış (kayıtlı) kullanıcıların oluşturduğu kişilerin listesini görüntüler. Üç güvenlik grubu vardır:

* **Kayıtlı kullanıcılar** tüm onaylanan verileri görüntüleyebilir ve kendi verilerini düzenleyebilir/silebilir.
* **Yöneticiler** , kişi verilerini onaylayabilir veya reddedebilir. Yalnızca onaylanan kişiler kullanıcılara görünür.
* **Yöneticiler** , verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.

Bu belgedeki görüntüler en son şablonlarla tam olarak eşleşmez.

Aşağıdaki görüntüde, User Rick ( `rick@example.com` ) oturum açtı. Rick, onaylanan kişileri görüntüleyebilir ve **Düzenle** / **Delete**' e / ait kişiler için**yeni bağlantılar oluştur** ' a bakın. Yalnızca Rick tarafından oluşturulan son kayıt, **Düzenle** ve **Sil** bağlantılarını görüntüler. Yönetici veya yönetici durumu "Onaylandı" olarak değiştirene kadar diğer kullanıcılar son kaydı görmez.

![Rick oturum açmış olduğunu gösteren ekran görüntüsü](secure-data/_static/rick.png)

Aşağıdaki görüntüde, `manager@contoso.com` ve yöneticisinin rolünde oturum açıldı:

![Oturum açmış olduğunu gösteren ekran görüntüsü manager@contoso.com](secure-data/_static/manager1.png)

Aşağıdaki görüntüde, bir kişinin Yöneticiler Ayrıntılar görünümü gösterilmektedir:

![Yöneticinin bir kişinin görünümü](secure-data/_static/manager.png)

**Onaylama** ve **reddetme** düğmeleri yalnızca Yöneticiler ve yöneticiler için görüntülenir.

Aşağıdaki görüntüde, `admin@contoso.com` ve yönetici rolünde oturum açıldı:

![Oturum açmış olduğunu gösteren ekran görüntüsü admin@contoso.com](secure-data/_static/admin.png)

Yöneticinin tüm ayrıcalıkları vardır. Herhangi bir kişiyi okuyabilir/düzenleyebilir/silebilir ve kişilerin durumunu değiştirebilir.

Uygulama, aşağıdaki model için [Yapı iskelesi](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tarafından oluşturulmuştur `Contact` :

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Örnek, aşağıdaki yetkilendirme işleyicilerini içerir:

* `ContactIsOwnerAuthorizationHandler`: Bir kullanıcının yalnızca verilerini düzenleyebilmesini sağlar.
* `ContactManagerAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini sağlar.
* `ContactAdministratorsAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini ve kişileri düzenlemesini/silmesini sağlar.

## <a name="prerequisites"></a>Ön koşullar

Bu öğretici gelişmiş bir deyişle. Şunu tanımanız gerekir:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Kimlik Doğrulaması](xref:security/authentication/identity)
* [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)
* [Yetkilendirme](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Başlatıcı ve tamamlanmış uygulama

[Tamamlanmış](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) uygulamayı [indirin](xref:index#how-to-download-a-sample) . Tamamlanmış uygulamayı [Test](#test-the-completed-app) edin, böylece güvenlik özellikleri hakkında bilgi sahibi olun.

### <a name="the-starter-app"></a>Başlangıç uygulaması

[Başlangıç](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) uygulamasını [indirin](xref:index#how-to-download-a-sample) .

Uygulamayı çalıştırın, **ContactManager** bağlantısına dokunun ve bir kişi oluşturup silemdiğinizi doğrulayın.

## <a name="secure-user-data"></a>Güvenli Kullanıcı verileri

Aşağıdaki bölümlerde, güvenli Kullanıcı verileri uygulaması oluşturmak için tüm önemli adımlar verilmiştir. Tamamlanmış projeye başvurmak faydalı olabilir.

### <a name="tie-the-contact-data-to-the-user"></a>Kişi verilerini kullanıcıya bağlama

[Identity](xref:security/authentication/identity)Kullanıcıların verilerini düzenleyebilmeleri, ancak diğer kullanıcıların verilerini düzenleyebilmeleri için ASP.NET Kullanıcı kimliğini kullanın. `OwnerID`Model ekleyin `ContactStatus` `Contact` :

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` kullanıcının `AspNetUser` veritabanındaki TABLODAKI kimliği [Identity](xref:security/authentication/identity) . Bu `Status` alan, bir kişinin genel kullanıcılar tarafından görüntülenebilir olup olmadığını belirler.

Yeni bir geçiş oluşturun ve veritabanını güncelleştirin:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Rol hizmetlerini Ekle Identity

Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a>Kimliği doğrulanmış kullanıcılar iste

Kullanıcıların kimliklerinin doğrulanmasını gerektirmek için geri dönüş kimlik doğrulama ilkesini ayarlayın:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

Önceki vurgulanmış kod, [geri dönüş kimlik doğrulama ilkesini](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)ayarlar. Geri dönüş kimlik doğrulama ilkesi ***all*** , Razor bir kimlik doğrulama özniteliğiyle sayfalar, denetleyiciler veya eylem yöntemleri dışında tüm kullanıcıların kimliklerinin doğrulanmasını gerektirir. Örneğin, Razor Sayfalar, denetleyiciler veya eylem yöntemleri, `[AllowAnonymous]` `[Authorize(PolicyName="MyPolicy")]` geri dönüş kimlik doğrulama ilkesi yerine uygulanan kimlik doğrulama özniteliğini kullanır.

Geri dönüş kimlik doğrulama ilkesi:

* Açıkça bir kimlik doğrulama ilkesi belirtmeyen tüm isteklere uygulanır. Endpoint Routing tarafından sunulan istekler için, bu, yetkilendirme özniteliği belirtmeyen tüm uç noktaları içerir. [Statik dosyalar](xref:fundamentals/static-files)gibi yetkilendirme ara yazılımı sonrasında diğer ara yazılım tarafından hizmet verilen istekler için, bu ilkeyi tüm isteklere uygular.

Geri dönüş kimlik doğrulama ilkesini kullanıcıların kimliğinin doğrulanmasını gerektirecek şekilde ayarlamak, yeni eklenen Razor sayfaları ve denetleyicileri korur. Varsayılan olarak kimlik doğrulamanın gerekli olması, yeni denetleyicilere ve Razor sayfalarına bağlı olarak, özniteliğini dahil etmek için daha güvenlidir `[Authorize]` .

<xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Sınıfı da içerir <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> . , `DefaultPolicy` `[Authorize]` İlke belirtilmediğinde özniteliğiyle kullanılan ilkedir. `[Authorize]` , aksine adlandırılmış bir ilke içermez `[Authorize(PolicyName="MyPolicy")]` .

İlkeler hakkında daha fazla bilgi için bkz <xref:security/authorization/policies> ..

MVC denetleyicilerinin ve Razor sayfaların tüm kullanıcıların kimliğinin doğrulanmasını gerektirmek için alternatif bir yol yetkilendirme filtresi ekliyor:

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

Yukarıdaki kod, bir yetkilendirme filtresi kullanır ve geri dönüş ilkesini ayarlama Endpoint Routing kullanır. Geri dönüş ilkesini ayarlamak, tüm kullanıcıların kimliğinin doğrulanmasını gerektirmek için tercih edilen yoldur.

[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) `Index` `Privacy` Anonim kullanıcıların, kaydolmadan önce site hakkında bilgi alması Için ve sayfalarına AllowAnonymous ekleyin:

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>Test hesabını yapılandırma

`SeedData`Sınıfı iki hesap oluşturur: yönetici ve yönetici. Bu hesaplara bir parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets) kullanın. Parolayı proje dizininden ayarlayın ( *program.cs*içeren dizin):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Güçlü bir parola belirtilmemişse, çağrıldığında bir özel durum oluşturulur `SeedData.Initialize` .

`Main`Sınama parolasını kullanacak şekilde güncelleştir:

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Test hesapları oluşturma ve kişileri güncelleştirme

`Initialize` `SeedData` Test hesapları oluşturmak için sınıfındaki yöntemi güncelleştirin:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

Yönetici kullanıcı KIMLIĞINI ve kişilerini ekleyin `ContactStatus` . Kişilerden birini "gönderildi" ve bir "reddedildi" yapın. Kullanıcı KIMLIĞINI ve durumunu tüm kişilere ekleyin. Yalnızca bir kişi gösterilir:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Sahip, yönetici ve yönetici yetkilendirme işleyicileri oluşturma

`ContactIsOwnerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun. `ContactIsOwnerAuthorizationHandler`Kullanıcının kaynağın sahibi olduğunu doğrular.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`Çağıran [bağlamı. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)Kimliği doğrulanmış geçerli kullanıcı kişi sahibsiyse başarılı olur. Yetkilendirme işleyicileri genellikle:

* `context.Succeed`Gereksinimler karşılandığında döndürün.
* `Task.CompletedTask`Gereksinimler karşılanmazsa döndürün. `Task.CompletedTask` başarılı veya başarısız değil, &mdash; diğer yetkilendirme işleyicilerinin çalışmasına izin verir.

Açıkça başarısız olması gerekiyorsa, [bağlamı döndürün. Başarısız oldu](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Uygulama, iletişim sahiplerinin kendi verilerini düzenlemesine/silmesine/oluşturmasına izin verir. `ContactIsOwnerAuthorizationHandler` gereksinim parametresine geçirilen işlemi denetlemesi gerekmez.

### <a name="create-a-manager-authorization-handler"></a>Yönetici yetkilendirme işleyicisi oluşturma

`ContactManagerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun. `ContactManagerAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular. İçerik değişikliklerini yalnızca Yöneticiler onaylayabilir veya reddedebilir (yeni veya değiştirilmiş).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Yönetici yetkilendirme işleyicisi oluşturma

`ContactAdministratorsAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun. `ContactAdministratorsAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular. Yönetici tüm işlemleri yapabilir.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Yetkilendirme işleyicilerini kaydetme

Entity Framework Core kullanan hizmetlerin, [Addkapsamlıdır](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)kullanılarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) için kayıtlı olması gerekir. , `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core oluşturulan ASP.NET Core kullanır. İşleyicileri hizmet koleksiyonuyla kaydedin, bu sayede `ContactsController` [bağımlılık ekleme](xref:fundamentals/dependency-injection)üzerinden kullanılabilir. Aşağıdaki kodu sonuna ekleyin `ConfigureServices` :

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` ve `ContactManagerAuthorizationHandler` tekton olarak eklenir. Bu, EF kullanmayan ve gereken tüm bilgiler yöntemin parametresinde olduğundan tektonlar vardır `Context` `HandleRequirementAsync` .

## <a name="support-authorization"></a>Destek yetkilendirme

Bu bölümde, Razor sayfaları güncelleştirir ve bir işlem gereksinimleri sınıfı eklersiniz.

### <a name="review-the-contact-operations-requirements-class"></a>İlgili kişi işlemleri gereksinimleri sınıfını gözden geçirin

Sınıfını gözden geçirin `ContactOperations` . Bu sınıf, uygulamanın desteklediği gereksinimleri içerir:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Kişiler sayfaları için temel sınıf oluşturma Razor

Kişiler sayfalarında kullanılan hizmetleri içeren bir temel sınıf oluşturun Razor . Temel sınıf, başlatma kodunu bir konuma koyar:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

Yukarıdaki kod:

* `IAuthorizationService`Yetkilendirme işleyicilerine erişim için hizmeti ekler.
* Hizmeti ekler Identity `UserManager` .
* Öğesini ekleyin `ApplicationDbContext` .

### <a name="update-the-createmodel"></a>CreateModel 'i Güncelleştir

Sayfa modeli oluşturma oluşturucusunu temel sınıfı kullanacak şekilde güncelleştirin `DI_BasePageModel` :

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Yöntemi şu `CreateModel.OnPostAsync` şekilde güncelleştirin:

* Kullanıcı KIMLIĞINI `Contact` modele ekleyin.
* Kullanıcının kişi oluşturma iznine sahip olduğunu doğrulamak için yetkilendirme işleyicisini çağırın.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Indexmodel 'i Güncelleştir

`OnGetAsync`Yöntemi yalnızca onaylanan kişilerin genel kullanıcılara gösterilmesi için güncelleştirin:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>EditModel güncelleştirme

Kullanıcının kişiye ait olduğunu doğrulamak için bir yetkilendirme işleyicisi ekleyin. Kaynak yetkilendirmesi doğrulandığında, `[Authorize]` öznitelik yeterli değildir. Öznitelikler değerlendirildiğinde uygulamanın kaynağa erişimi yoktur. Kaynak tabanlı yetkilendirme sağlanmalıdır. Uygulamanın kaynağa erişimi olduğunda, sayfa modeline yükleyerek veya işleyicinin kendisi içine yükleyerek denetimlerin gerçekleştirilmesi gerekir. Kaynak anahtarını geçirerek kaynağa sıkça erişirsiniz.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>DeleteModel 'i Güncelleştir

Kullanıcının kişide silme iznine sahip olduğunu doğrulamak için, silme sayfası modelini yetkilendirme işleyicisini kullanacak şekilde güncelleştirin.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Yetkilendirme hizmetini görünümlere ekleme

Şu anda kullanıcı ARABIRIMI, kullanıcının değiştiremeyeceğiniz kişiler için Düzenle ve Sil bağlantılarını gösterir.

Yetkilendirme hizmetini *Sayfalar/_ViewImports. cshtml* dosyasına ekleme, bu nedenle tüm görünümlerde kullanılabilir hale gelir:

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

Yukarıdaki biçimlendirme birkaç deyim ekliyor `using` .

*Sayfalar/kişiler/Index. cshtml* 'deki **Düzenle** ve **Sil** bağlantılarını yalnızca uygun izinlere sahip kullanıcılar için işlendikleri şekilde güncelleştirin:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Verileri değiştirme izni olmayan kullanıcıların bağlantılarının gizlenmesi, uygulamanın güvenliğini sağlar. Bağlantıların gizlenmesi, yalnızca geçerli bağlantıları görüntüleyerek uygulamayı daha kolay hale getirir. Kullanıcılar, sahip olmadıkları veriler üzerinde düzenleme ve silme işlemlerini çağırmak için oluşturulan URL 'Leri hacme edebilir. RazorSayfanın veya denetleyicinin, verilerin güvenliğini sağlamak için erişim denetimlerini zorlaması gerekir.

### <a name="update-details"></a>Güncelleştirme ayrıntıları

Yöneticilerin kişileri onaylamasını veya reddedebilmesi için Ayrıntılar görünümünü güncelleştirin:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

Ayrıntılar sayfası modelini güncelleştirin:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Bir role Kullanıcı ekleme veya rolü kaldırma

Hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8502) bakın:

* Bir kullanıcıdan ayrıcalıklar kaldırılıyor. Örneğin, bir sohbet uygulamasındaki kullanıcıyı kapatma.
* Bir kullanıcıya ayrıcalık ekleme.

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a>Challenge ve Fordeklarasyon arasındaki farklar

Bu uygulama, varsayılan ilkeyi [kimliği doğrulanmış kullanıcıları gerektirecek](#rau)şekilde ayarlar. Aşağıdaki kod anonim kullanıcılara izin verir. Anonim kullanıcılara, çekişme ile Fordeklarasyon arasındaki farkları gösterme izni verilir.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

Yukarıdaki kodda:

* Kullanıcının kimliği **doğrulanmadığı** zaman, bir `ChallengeResult` döndürülür. Bir `ChallengeResult` döndürüldüğünde, Kullanıcı oturum açma sayfasına yönlendirilir.
* Kullanıcının kimliği doğrulandığında, ancak yetkilendirilmediğinde, bir `ForbidResult` döndürülür. Bir `ForbidResult` döndürüldüğünde, Kullanıcı erişim reddedildi sayfasına yönlendirilir.

## <a name="test-the-completed-app"></a>Tamamlanmış uygulamayı test etme

Önceden oluşturulmuş kullanıcı hesapları için bir parola ayarlamadıysanız, parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets#secret-manager) kullanın:

* Güçlü bir parola seçin: sekiz veya daha fazla karakter, en az bir büyük harf karakter, sayı ve simge kullanın. Örneğin, `Passw0rd!` güçlü parola gereksinimlerini karşılar.
* Aşağıdaki komutu projenin klasöründen çalıştırın, burada `<PW>` parola olur:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

Uygulamanın kişileri varsa:

* Tablodaki tüm kayıtları silin `Contact` .
* Veritabanını temel alarak uygulamayı yeniden başlatın.

Tamamlanmış uygulamayı test etmenin kolay bir yolu da üç farklı tarayıcı (veya bilito/InPrivate oturumlar) kullanmaktır. Tek bir tarayıcıda yeni bir Kullanıcı kaydedin (örneğin, `test@example.com` ). Her bir tarayıcıda farklı bir kullanıcıyla oturum açın. Aşağıdaki işlemleri doğrulayın:

* Kayıtlı kullanıcılar tüm onaylanan iletişim verilerini görüntüleyebilir.
* Kayıtlı kullanıcılar kendi verilerini düzenleyebilir/silebilir.
* Yöneticiler, kişi verilerini onaylayabilir/reddedebilir. `Details`Görünüm **Onayla** ve **Reddet** düğmelerini gösterir.
* Yöneticiler tüm verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.

| Kullanıcı                | Uygulama tarafından sağlanan | Seçenekler                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | No                | Kendi verilerini düzenleyin/silin.                |
| manager@contoso.com | Yes               | Kendi verilerini onaylama/reddetme ve düzenleme/silme. |
| admin@contoso.com   | Yes               | Tüm verileri onaylama/reddetme ve düzenleme/silme. |

Yöneticinin tarayıcısında bir kişi oluşturun. Yönetici iletişim kutusundan silme ve düzenleme için URL 'YI kopyalayın. Bu bağlantıları test kullanıcısının bu işlemleri gerçekleştiremediğinizi doğrulamak için test kullanıcısının tarayıcısına yapıştırın.

## <a name="create-the-starter-app"></a>Başlangıç uygulamasını oluşturma

* Razor"ContactManager" adlı bir sayfalar uygulaması oluşturma
  * **Ayrı kullanıcı hesaplarıyla**uygulamayı oluşturun.
  * Ad alanı örnekte kullanılan ad alanıyla eşleşecek şekilde "ContactManager" olarak adlandırın.
  * `-uld` SQLite yerine LocalDB belirtir

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *Modeller/Ilgili kişi ekle. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Modeli dolandırıcıdan katlayın `Contact` .
* İlk geçiş oluşturun ve veritabanını güncelleştirin:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

Komutuyla bir hata yaşarsanız `dotnet aspnet-codegenerator razorpage` , [Bu GitHub sorununa](https://github.com/aspnet/Scaffolding/issues/984)bakın.

* *Pages/Shared/_Layout. cshtml* dosyasındaki **ContactManager** bağlayıcısını güncelleştirin:

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* Kişiyi oluşturarak, düzenleyerek ve silerek uygulamayı test edin

### <a name="seed-the-database"></a>Veritabanını çekirdek

*Veri* klasörüne [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) sınıfını ekleyin:

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

`SeedData.Initialize`Şuradan arayın `Main` :

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Uygulamanın veritabanını sunan test edin. Kişi VERITABANıNDA herhangi bir satır varsa, çekirdek Yöntem çalıştırılmaz.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

Bu öğreticide, yetkilendirme tarafından korunan kullanıcı verileriyle ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı gösterilmektedir. Kimliği doğrulanmış (kayıtlı) kullanıcıların oluşturduğu kişilerin listesini görüntüler. Üç güvenlik grubu vardır:

* **Kayıtlı kullanıcılar** tüm onaylanan verileri görüntüleyebilir ve kendi verilerini düzenleyebilir/silebilir.
* **Yöneticiler** , kişi verilerini onaylayabilir veya reddedebilir. Yalnızca onaylanan kişiler kullanıcılara görünür.
* **Yöneticiler** , verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.

Aşağıdaki görüntüde, User Rick ( `rick@example.com` ) oturum açtı. Rick, onaylanan kişileri görüntüleyebilir ve **Düzenle** / **Delete**' e / ait kişiler için**yeni bağlantılar oluştur** ' a bakın. Yalnızca Rick tarafından oluşturulan son kayıt, **Düzenle** ve **Sil** bağlantılarını görüntüler. Yönetici veya yönetici durumu "Onaylandı" olarak değiştirene kadar diğer kullanıcılar son kaydı görmez.

![Rick oturum açmış olduğunu gösteren ekran görüntüsü](secure-data/_static/rick.png)

Aşağıdaki görüntüde, `manager@contoso.com` ve yöneticisinin rolünde oturum açıldı:

![Oturum açmış olduğunu gösteren ekran görüntüsü manager@contoso.com](secure-data/_static/manager1.png)

Aşağıdaki görüntüde, bir kişinin Yöneticiler Ayrıntılar görünümü gösterilmektedir:

![Yöneticinin bir kişinin görünümü](secure-data/_static/manager.png)

**Onaylama** ve **reddetme** düğmeleri yalnızca Yöneticiler ve yöneticiler için görüntülenir.

Aşağıdaki görüntüde, `admin@contoso.com` ve yönetici rolünde oturum açıldı:

![Oturum açmış olduğunu gösteren ekran görüntüsü admin@contoso.com](secure-data/_static/admin.png)

Yöneticinin tüm ayrıcalıkları vardır. Herhangi bir kişiyi okuyabilir/düzenleyebilir/silebilir ve kişilerin durumunu değiştirebilir.

Uygulama, aşağıdaki model için [Yapı iskelesi](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tarafından oluşturulmuştur `Contact` :

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Örnek, aşağıdaki yetkilendirme işleyicilerini içerir:

* `ContactIsOwnerAuthorizationHandler`: Bir kullanıcının yalnızca verilerini düzenleyebilmesini sağlar.
* `ContactManagerAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini sağlar.
* `ContactAdministratorsAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini ve kişileri düzenlemesini/silmesini sağlar.

## <a name="prerequisites"></a>Ön koşullar

Bu öğretici gelişmiş bir deyişle. Şunu tanımanız gerekir:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Kimlik Doğrulaması](xref:security/authentication/identity)
* [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)
* [Yetkilendirme](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Başlatıcı ve tamamlanmış uygulama

[Tamamlanmış](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) uygulamayı [indirin](xref:index#how-to-download-a-sample) . Tamamlanmış uygulamayı [Test](#test-the-completed-app) edin, böylece güvenlik özellikleri hakkında bilgi sahibi olun.

### <a name="the-starter-app"></a>Başlangıç uygulaması

[Başlangıç](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) uygulamasını [indirin](xref:index#how-to-download-a-sample) .

Uygulamayı çalıştırın, **ContactManager** bağlantısına dokunun ve bir kişi oluşturup silemdiğinizi doğrulayın.

## <a name="secure-user-data"></a>Güvenli Kullanıcı verileri

Aşağıdaki bölümlerde, güvenli Kullanıcı verileri uygulaması oluşturmak için tüm önemli adımlar verilmiştir. Tamamlanmış projeye başvurmak faydalı olabilir.

### <a name="tie-the-contact-data-to-the-user"></a>Kişi verilerini kullanıcıya bağlama

[Identity](xref:security/authentication/identity)Kullanıcıların verilerini düzenleyebilmeleri, ancak diğer kullanıcıların verilerini düzenleyebilmeleri için ASP.NET Kullanıcı kimliğini kullanın. `OwnerID`Model ekleyin `ContactStatus` `Contact` :

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` kullanıcının `AspNetUser` veritabanındaki TABLODAKI kimliği [Identity](xref:security/authentication/identity) . Bu `Status` alan, bir kişinin genel kullanıcılar tarafından görüntülenebilir olup olmadığını belirler.

Yeni bir geçiş oluşturun ve veritabanını güncelleştirin:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Rol hizmetlerini Ekle Identity

Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a>Kimliği doğrulanmış kullanıcılar iste

Kullanıcıların kimliklerinin doğrulanmasını gerektirmek için varsayılan kimlik doğrulama ilkesini ayarlayın:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Bir Razor sayfada, denetleyicide veya eylem yöntemi düzeyinde kimlik doğrulamasından vazgeçebilirsiniz `[AllowAnonymous]` . Varsayılan kimlik doğrulama ilkesini kullanıcıların kimliğinin doğrulanmasını gerektirecek şekilde ayarlamak, yeni eklenen Razor sayfaları ve denetleyicileri korur. Varsayılan olarak kimlik doğrulamanın gerekli olması, yeni denetleyicilere ve Razor sayfalarına bağlı olarak, özniteliğini dahil etmek için daha güvenlidir `[Authorize]` .

Anonim kullanıcıların, kayıt yaptırmadan önce site hakkında bilgi alması için dizine, hakkında ve Iletişim sayfalarına [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) ekleyin.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>Test hesabını yapılandırma

`SeedData`Sınıfı iki hesap oluşturur: yönetici ve yönetici. Bu hesaplara bir parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets) kullanın. Parolayı proje dizininden ayarlayın ( *program.cs*içeren dizin):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Güçlü bir parola belirtilmemişse, çağrıldığında bir özel durum oluşturulur `SeedData.Initialize` .

`Main`Sınama parolasını kullanacak şekilde güncelleştir:

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Test hesapları oluşturma ve kişileri güncelleştirme

`Initialize` `SeedData` Test hesapları oluşturmak için sınıfındaki yöntemi güncelleştirin:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

Yönetici kullanıcı KIMLIĞINI ve kişilerini ekleyin `ContactStatus` . Kişilerden birini "gönderildi" ve bir "reddedildi" yapın. Kullanıcı KIMLIĞINI ve durumunu tüm kişilere ekleyin. Yalnızca bir kişi gösterilir:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Sahip, yönetici ve yönetici yetkilendirme işleyicileri oluşturma

Bir *Yetkilendirme* klasörü oluşturun ve içinde bir `ContactIsOwnerAuthorizationHandler` sınıf oluşturun. `ContactIsOwnerAuthorizationHandler`Kullanıcının kaynağın sahibi olduğunu doğrular.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`Çağıran [bağlamı. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)Kimliği doğrulanmış geçerli kullanıcı kişi sahibsiyse başarılı olur. Yetkilendirme işleyicileri genellikle:

* `context.Succeed`Gereksinimler karşılandığında döndürün.
* `Task.CompletedTask`Gereksinimler karşılanmazsa döndürün. `Task.CompletedTask` başarılı veya başarısız değil, &mdash; diğer yetkilendirme işleyicilerinin çalışmasına izin verir.

Açıkça başarısız olması gerekiyorsa, [bağlamı döndürün. Başarısız oldu](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Uygulama, iletişim sahiplerinin kendi verilerini düzenlemesine/silmesine/oluşturmasına izin verir. `ContactIsOwnerAuthorizationHandler` gereksinim parametresine geçirilen işlemi denetlemesi gerekmez.

### <a name="create-a-manager-authorization-handler"></a>Yönetici yetkilendirme işleyicisi oluşturma

`ContactManagerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun. `ContactManagerAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular. İçerik değişikliklerini yalnızca Yöneticiler onaylayabilir veya reddedebilir (yeni veya değiştirilmiş).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Yönetici yetkilendirme işleyicisi oluşturma

`ContactAdministratorsAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun. `ContactAdministratorsAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular. Yönetici tüm işlemleri yapabilir.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Yetkilendirme işleyicilerini kaydetme

Entity Framework Core kullanan hizmetlerin, [Addkapsamlıdır](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)kullanılarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) için kayıtlı olması gerekir. , `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core oluşturulan ASP.NET Core kullanır. İşleyicileri hizmet koleksiyonuyla kaydedin, bu sayede `ContactsController` [bağımlılık ekleme](xref:fundamentals/dependency-injection)üzerinden kullanılabilir. Aşağıdaki kodu sonuna ekleyin `ConfigureServices` :

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` ve `ContactManagerAuthorizationHandler` tekton olarak eklenir. Bu, EF kullanmayan ve gereken tüm bilgiler yöntemin parametresinde olduğundan tektonlar vardır `Context` `HandleRequirementAsync` .

## <a name="support-authorization"></a>Destek yetkilendirme

Bu bölümde, Razor sayfaları güncelleştirir ve bir işlem gereksinimleri sınıfı eklersiniz.

### <a name="review-the-contact-operations-requirements-class"></a>İlgili kişi işlemleri gereksinimleri sınıfını gözden geçirin

Sınıfını gözden geçirin `ContactOperations` . Bu sınıf, uygulamanın desteklediği gereksinimleri içerir:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Kişiler sayfaları için temel sınıf oluşturma Razor

Kişiler sayfalarında kullanılan hizmetleri içeren bir temel sınıf oluşturun Razor . Temel sınıf, başlatma kodunu bir konuma koyar:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

Yukarıdaki kod:

* `IAuthorizationService`Yetkilendirme işleyicilerine erişim için hizmeti ekler.
* Hizmeti ekler Identity `UserManager` .
* Öğesini ekleyin `ApplicationDbContext` .

### <a name="update-the-createmodel"></a>CreateModel 'i Güncelleştir

Sayfa modeli oluşturma oluşturucusunu temel sınıfı kullanacak şekilde güncelleştirin `DI_BasePageModel` :

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Yöntemi şu `CreateModel.OnPostAsync` şekilde güncelleştirin:

* Kullanıcı KIMLIĞINI `Contact` modele ekleyin.
* Kullanıcının kişi oluşturma iznine sahip olduğunu doğrulamak için yetkilendirme işleyicisini çağırın.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Indexmodel 'i Güncelleştir

`OnGetAsync`Yöntemi yalnızca onaylanan kişilerin genel kullanıcılara gösterilmesi için güncelleştirin:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>EditModel güncelleştirme

Kullanıcının kişiye ait olduğunu doğrulamak için bir yetkilendirme işleyicisi ekleyin. Kaynak yetkilendirmesi doğrulandığında, `[Authorize]` öznitelik yeterli değildir. Öznitelikler değerlendirildiğinde uygulamanın kaynağa erişimi yoktur. Kaynak tabanlı yetkilendirme sağlanmalıdır. Uygulamanın kaynağa erişimi olduğunda, sayfa modeline yükleyerek veya işleyicinin kendisi içine yükleyerek denetimlerin gerçekleştirilmesi gerekir. Kaynak anahtarını geçirerek kaynağa sıkça erişirsiniz.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>DeleteModel 'i Güncelleştir

Kullanıcının kişide silme iznine sahip olduğunu doğrulamak için, silme sayfası modelini yetkilendirme işleyicisini kullanacak şekilde güncelleştirin.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Yetkilendirme hizmetini görünümlere ekleme

Şu anda kullanıcı ARABIRIMI, kullanıcının değiştiremeyeceğiniz kişiler için Düzenle ve Sil bağlantılarını gösterir.

Yetkilendirme hizmetini *Görünümler/_ViewImports. cshtml* dosyasında, tüm görünümlerde kullanılabilir olacak şekilde ekleme:

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

Yukarıdaki biçimlendirme birkaç deyim ekliyor `using` .

*Sayfalar/kişiler/Index. cshtml* 'deki **Düzenle** ve **Sil** bağlantılarını yalnızca uygun izinlere sahip kullanıcılar için işlendikleri şekilde güncelleştirin:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Verileri değiştirme izni olmayan kullanıcıların bağlantılarının gizlenmesi, uygulamanın güvenliğini sağlar. Bağlantıların gizlenmesi, yalnızca geçerli bağlantıları görüntüleyerek uygulamayı daha kolay hale getirir. Kullanıcılar, sahip olmadıkları veriler üzerinde düzenleme ve silme işlemlerini çağırmak için oluşturulan URL 'Leri hacme edebilir. RazorSayfanın veya denetleyicinin, verilerin güvenliğini sağlamak için erişim denetimlerini zorlaması gerekir.

### <a name="update-details"></a>Güncelleştirme ayrıntıları

Yöneticilerin kişileri onaylamasını veya reddedebilmesi için Ayrıntılar görünümünü güncelleştirin:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Ayrıntılar sayfası modelini güncelleştirin:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Bir role Kullanıcı ekleme veya rolü kaldırma

Hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8502) bakın:

* Bir kullanıcıdan ayrıcalıklar kaldırılıyor. Örneğin, bir sohbet uygulamasındaki kullanıcıyı kapatma.
* Bir kullanıcıya ayrıcalık ekleme.

## <a name="test-the-completed-app"></a>Tamamlanmış uygulamayı test etme

Önceden oluşturulmuş kullanıcı hesapları için bir parola ayarlamadıysanız, parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets#secret-manager) kullanın:

* Güçlü bir parola seçin: sekiz veya daha fazla karakter, en az bir büyük harf karakter, sayı ve simge kullanın. Örneğin, `Passw0rd!` güçlü parola gereksinimlerini karşılar.
* Aşağıdaki komutu projenin klasöründen çalıştırın, burada `<PW>` parola olur:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* Veritabanını bırakma ve güncelleştirme

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* Veritabanını temel alarak uygulamayı yeniden başlatın.

Tamamlanmış uygulamayı test etmenin kolay bir yolu da üç farklı tarayıcı (veya bilito/InPrivate oturumlar) kullanmaktır. Tek bir tarayıcıda yeni bir Kullanıcı kaydedin (örneğin, `test@example.com` ). Her bir tarayıcıda farklı bir kullanıcıyla oturum açın. Aşağıdaki işlemleri doğrulayın:

* Kayıtlı kullanıcılar tüm onaylanan iletişim verilerini görüntüleyebilir.
* Kayıtlı kullanıcılar kendi verilerini düzenleyebilir/silebilir.
* Yöneticiler, kişi verilerini onaylayabilir/reddedebilir. `Details`Görünüm **Onayla** ve **Reddet** düğmelerini gösterir.
* Yöneticiler tüm verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.

| Kullanıcı                | Uygulama tarafından sağlanan | Seçenekler                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | No                | Kendi verilerini düzenleyin/silin.                |
| manager@contoso.com | Yes               | Kendi verilerini onaylama/reddetme ve düzenleme/silme. |
| admin@contoso.com   | Yes               | Tüm verileri onaylama/reddetme ve düzenleme/silme. |

Yöneticinin tarayıcısında bir kişi oluşturun. Yönetici iletişim kutusundan silme ve düzenleme için URL 'YI kopyalayın. Bu bağlantıları test kullanıcısının bu işlemleri gerçekleştiremediğinizi doğrulamak için test kullanıcısının tarayıcısına yapıştırın.

## <a name="create-the-starter-app"></a>Başlangıç uygulamasını oluşturma

* Razor"ContactManager" adlı bir sayfalar uygulaması oluşturma
  * **Ayrı kullanıcı hesaplarıyla**uygulamayı oluşturun.
  * Ad alanı örnekte kullanılan ad alanıyla eşleşecek şekilde "ContactManager" olarak adlandırın.
  * `-uld` SQLite yerine LocalDB belirtir

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *Modeller/Ilgili kişi ekle. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Modeli dolandırıcıdan katlayın `Contact` .
* İlk geçiş oluşturun ve veritabanını güncelleştirin:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* *Pages/_Layout. cshtml* dosyasındaki **ContactManager** bağlayıcısını güncelleştirin:

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* Kişiyi oluşturarak, düzenleyerek ve silerek uygulamayı test edin

### <a name="seed-the-database"></a>Veritabanını çekirdek

*Veri* klasörüne [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) sınıfını ekleyin.

`SeedData.Initialize`Şuradan arayın `Main` :

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Uygulamanın veritabanını sunan test edin. Kişi VERITABANıNDA herhangi bir satır varsa, çekirdek Yöntem çalıştırılmaz.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Ek kaynaklar

* [Azure App Service’te .NET Core ve SQL Veritabanı web uygulaması oluşturma](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core yetkilendirme Laboratuvarı](https://github.com/blowdart/AspNetAuthorizationWorkshop). Bu laboratuvar, bu öğreticide sunulan güvenlik özellikleri hakkında daha fazla ayrıntıya gider.
* <xref:security/authorization/introduction>
* [Özel ilke tabanlı yetkilendirme](xref:security/authorization/policies)
