---
title: Yetkilendirme ile korunan kullanıcı verileriyle ASP.NET Core uygulama oluşturma
author: rick-anderson
description: Yetkilendirme ile korunan kullanıcı verileriyle Razor bir sayfalar uygulaması oluşturmayı öğrenin. HTTPS, kimlik doğrulaması, güvenlik, ASP.NET Core Identityiçerir.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: f52b08786dde54e7dcbd2e00f43badb58879cf79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775758"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>Yetkilendirme ile korunan kullanıcı verileriyle ASP.NET Core uygulama oluşturma

By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [ali Audette](https://twitter.com/joeaudette)

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core MVC sürümü için [Bu PDF 'ye](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) bakın. Bu öğreticinin ASP.NET Core 1,1 sürümü [Bu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) klasöründedir. 1,1 ASP.NET Core örnek [örnekleri](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[Bu PDF 'ye](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) bakın

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Bu öğreticide, yetkilendirme tarafından korunan kullanıcı verileriyle ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı gösterilmektedir. Kimliği doğrulanmış (kayıtlı) kullanıcıların oluşturduğu kişilerin listesini görüntüler. Üç güvenlik grubu vardır:

* **Kayıtlı kullanıcılar** tüm onaylanan verileri görüntüleyebilir ve kendi verilerini düzenleyebilir/silebilir.
* **Yöneticiler** , kişi verilerini onaylayabilir veya reddedebilir. Yalnızca onaylanan kişiler kullanıcılara görünür.
* **Yöneticiler** , verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.

Bu belgedeki görüntüler en son şablonlarla tam olarak eşleşmez.

Aşağıdaki görüntüde, User Rick (`rick@example.com`) oturum açtı. Rick, onaylanan kişileri görüntüleyebilir ve **Düzenle**/**Delete**/' e ait kişiler için**yeni bağlantılar oluştur** ' a bakın. Yalnızca Rick tarafından oluşturulan son kayıt, **Düzenle** ve **Sil** bağlantılarını görüntüler. Yönetici veya yönetici durumu "Onaylandı" olarak değiştirene kadar diğer kullanıcılar son kaydı görmez.

![Rick oturum açmış olduğunu gösteren ekran görüntüsü](secure-data/_static/rick.png)

Aşağıdaki görüntüde, `manager@contoso.com` ve yöneticisinin rolünde oturum açıldı:

![Oturum açmış manager@contoso.com olduğunu gösteren ekran görüntüsü](secure-data/_static/manager1.png)

Aşağıdaki görüntüde, bir kişinin Yöneticiler Ayrıntılar görünümü gösterilmektedir:

![Yöneticinin bir kişinin görünümü](secure-data/_static/manager.png)

**Onaylama** ve **reddetme** düğmeleri yalnızca Yöneticiler ve yöneticiler için görüntülenir.

Aşağıdaki görüntüde, `admin@contoso.com` ve yönetici rolünde oturum açıldı:

![Oturum açmış admin@contoso.com olduğunu gösteren ekran görüntüsü](secure-data/_static/admin.png)

Yöneticinin tüm ayrıcalıkları vardır. Herhangi bir kişiyi okuyabilir/düzenleyebilir/silebilir ve kişilerin durumunu değiştirebilir.

Uygulama, aşağıdaki `Contact` model için [Yapı iskelesi](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tarafından oluşturulmuştur:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Örnek, aşağıdaki yetkilendirme işleyicilerini içerir:

* `ContactIsOwnerAuthorizationHandler`: Bir kullanıcının yalnızca verilerini düzenleyebilmesini sağlar.
* `ContactManagerAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini sağlar.
* `ContactAdministratorsAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini ve kişileri düzenlemesini/silmesini sağlar.

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici gelişmiş bir deyişle. Şunu tanımanız gerekir:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Kimlik doğrulaması](xref:security/authentication/identity)
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

Kullanıcıların verilerini düzenleyebilmeleri, ancak diğer kullanıcıların verilerini düzenleyebilmeleri için ASP.NET [Identity](xref:security/authentication/identity) Kullanıcı kimliğini kullanın. `ContactStatus` Model ekleyin `OwnerID` `Contact`

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID``AspNetUser` [kimlik](xref:security/authentication/identity) veritabanındaki tablodaki kullanıcının kimliği. Bu `Status` alan, bir kişinin genel kullanıcılar tarafından görüntülenebilir olup olmadığını belirler.

Yeni bir geçiş oluşturun ve veritabanını güncelleştirin:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Kimliğe rol hizmetleri Ekle

Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a>Kimliği doğrulanmış kullanıcılar iste

Kullanıcıların kimliklerinin doğrulanmasını gerektirmek için varsayılan kimlik doğrulama ilkesini ayarlayın:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 Kimlik doğrulaması Razor sayfasında, denetleyicide veya eylem yöntemi düzeyinde `[AllowAnonymous]` özniteliğiyle devre dışı bırakabilirsiniz. Kullanıcıların kimliğinin doğrulanmasını gerektirmek için varsayılan kimlik doğrulama ilkesinin ayarlanması, yeni eklenen Razor Pages ve denetleyicilerin korunmasını sağlar. Varsayılan olarak kimlik doğrulamanın gerekli olması, yeni denetleyicilere bağlı olandan daha güvenlidir ve `[Authorize]` özniteliği dahil Razor Pages.

Anonim kullanıcıların, kaydolmadan önce site hakkında bilgi alması için dizin ve gizlilik sayfalarına [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) ekleyin.

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>Test hesabını yapılandırma

`SeedData` Sınıfı iki hesap oluşturur: yönetici ve yönetici. Bu hesaplara bir parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets) kullanın. Parolayı proje dizininden ayarlayın ( *program.cs*içeren dizin):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Güçlü bir parola belirtilmemişse, çağrıldığında bir özel durum oluşturulur `SeedData.Initialize` .

Sınama `Main` parolasını kullanacak şekilde güncelleştir:

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Test hesapları oluşturma ve kişileri güncelleştirme

Test hesapları `Initialize` oluşturmak için `SeedData` sınıfındaki yöntemi güncelleştirin:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

Yönetici kullanıcı KIMLIĞINI ve `ContactStatus` kişilerini ekleyin. Kişilerden birini "gönderildi" ve bir "reddedildi" yapın. Kullanıcı KIMLIĞINI ve durumunu tüm kişilere ekleyin. Yalnızca bir kişi gösterilir:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Sahip, yönetici ve yönetici yetkilendirme işleyicileri oluşturma

Yetkilendirme klasöründe `ContactIsOwnerAuthorizationHandler` bir sınıf oluşturun *Authorization* . Kullanıcının `ContactIsOwnerAuthorizationHandler` kaynağın sahibi olduğunu doğrular.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler` Çağıran [bağlamı. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)Kimliği doğrulanmış geçerli kullanıcı kişi sahibsiyse başarılı olur. Yetkilendirme işleyicileri genellikle:

* Gereksinimler `context.Succeed` karşılandığında döndürün.
* Gereksinimler `Task.CompletedTask` karşılanmazsa döndürün. `Task.CompletedTask`başarılı veya başarısız&mdash;değil, diğer yetkilendirme işleyicilerinin çalışmasına izin verir.

Açıkça başarısız olması gerekiyorsa, [bağlamı döndürün. Başarısız oldu](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Uygulama, iletişim sahiplerinin kendi verilerini düzenlemesine/silmesine/oluşturmasına izin verir. `ContactIsOwnerAuthorizationHandler`gereksinim parametresine geçirilen işlemi denetlemesi gerekmez.

### <a name="create-a-manager-authorization-handler"></a>Yönetici yetkilendirme işleyicisi oluşturma

Yetkilendirme klasöründe `ContactManagerAuthorizationHandler` bir sınıf oluşturun *Authorization* . Kaynak `ContactManagerAuthorizationHandler` üzerinde davranan kullanıcının bir yönetici olduğunu doğrular. İçerik değişikliklerini yalnızca Yöneticiler onaylayabilir veya reddedebilir (yeni veya değiştirilmiş).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Yönetici yetkilendirme işleyicisi oluşturma

Yetkilendirme klasöründe `ContactAdministratorsAuthorizationHandler` bir sınıf oluşturun *Authorization* . Kaynak `ContactAdministratorsAuthorizationHandler` üzerinde davranan kullanıcının bir yönetici olduğunu doğrular. Yönetici tüm işlemleri yapabilir.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Yetkilendirme işleyicilerini kaydetme

Entity Framework Core kullanan hizmetlerin, [Addkapsamlıdır](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)kullanılarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) için kayıtlı olması gerekir. , `ContactIsOwnerAuthorizationHandler` Entity Framework Core oluşturulan ASP.NET Core [kimliğini](xref:security/authentication/identity)kullanır. İşleyicileri hizmet koleksiyonuyla kaydedin, bu sayede `ContactsController` [bağımlılık ekleme](xref:fundamentals/dependency-injection)üzerinden kullanılabilir. Aşağıdaki kodu sonuna ekleyin `ConfigureServices`:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler`ve `ContactManagerAuthorizationHandler` tekton olarak eklenir. Bu, EF kullanmayan ve gereken tüm bilgiler `Context` `HandleRequirementAsync` yöntemin parametresinde olduğundan tektonlar vardır.

## <a name="support-authorization"></a>Destek yetkilendirme

Bu bölümde Razor Pages günceller ve bir işlem gereksinimleri sınıfı eklersiniz.

### <a name="review-the-contact-operations-requirements-class"></a>İlgili kişi işlemleri gereksinimleri sınıfını gözden geçirin

`ContactOperations` Sınıfını gözden geçirin. Bu sınıf, uygulamanın desteklediği gereksinimleri içerir:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Kişiler için bir temel sınıf oluşturun Razor Pages

Kişiler Razor Pages kullanılan hizmetleri içeren bir temel sınıf oluşturun. Temel sınıf, başlatma kodunu bir konuma koyar:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

Yukarıdaki kod:

* Yetkilendirme işleyicilerine `IAuthorizationService` erişim için hizmeti ekler.
* Kimlik `UserManager` hizmetini ekler.
* Öğesini ekleyin `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>CreateModel 'i Güncelleştir

Sayfa modeli oluşturma oluşturucusunu `DI_BasePageModel` temel sınıfı kullanacak şekilde güncelleştirin:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

`CreateModel.OnPostAsync` Yöntemi şu şekilde güncelleştirin:

* Kullanıcı KIMLIĞINI `Contact` modele ekleyin.
* Kullanıcının kişi oluşturma iznine sahip olduğunu doğrulamak için yetkilendirme işleyicisini çağırın.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Indexmodel 'i Güncelleştir

`OnGetAsync` Yöntemi yalnızca onaylanan kişilerin genel kullanıcılara gösterilmesi için güncelleştirin:

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

Yukarıdaki biçimlendirme birkaç `using` deyim ekliyor.

*Sayfalar/kişiler/Index. cshtml* 'deki **Düzenle** ve **Sil** bağlantılarını yalnızca uygun izinlere sahip kullanıcılar için işlendikleri şekilde güncelleştirin:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Verileri değiştirme izni olmayan kullanıcıların bağlantılarının gizlenmesi, uygulamanın güvenliğini sağlar. Bağlantıların gizlenmesi, yalnızca geçerli bağlantıları görüntüleyerek uygulamayı daha kolay hale getirir. Kullanıcılar, sahip olmadıkları veriler üzerinde düzenleme ve silme işlemlerini çağırmak için oluşturulan URL 'Leri hacme edebilir. Razor sayfası veya denetleyicinin, verilerin güvenliğini sağlamak için erişim denetimlerini zorlaması gerekir.

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

Bu uygulama, varsayılan ilkeyi [kimliği doğrulanmış kullanıcıları gerektirecek](#require-authenticated-users)şekilde ayarlar. Aşağıdaki kod anonim kullanıcılara izin verir. Anonim kullanıcılara, çekişme ile Fordeklarasyon arasındaki farkları gösterme izni verilir.

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

* `Contact` Tablodaki tüm kayıtları silin.
* Veritabanını temel alarak uygulamayı yeniden başlatın.

Tamamlanmış uygulamayı test etmenin kolay bir yolu da üç farklı tarayıcı (veya bilito/InPrivate oturumlar) kullanmaktır. Tek bir tarayıcıda yeni bir Kullanıcı kaydedin (örneğin, `test@example.com`). Her bir tarayıcıda farklı bir kullanıcıyla oturum açın. Aşağıdaki işlemleri doğrulayın:

* Kayıtlı kullanıcılar tüm onaylanan iletişim verilerini görüntüleyebilir.
* Kayıtlı kullanıcılar kendi verilerini düzenleyebilir/silebilir.
* Yöneticiler, kişi verilerini onaylayabilir/reddedebilir. `Details` Görünüm **Onayla** ve **Reddet** düğmelerini gösterir.
* Yöneticiler tüm verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.

| Kullanıcı                | Uygulama tarafından sağlanan | Seçenekler                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Hayır                | Kendi verilerini düzenleyin/silin.                |
| manager@contoso.com | Yes               | Kendi verilerini onaylama/reddetme ve düzenleme/silme. |
| admin@contoso.com   | Yes               | Tüm verileri onaylama/reddetme ve düzenleme/silme. |

Yöneticinin tarayıcısında bir kişi oluşturun. Yönetici iletişim kutusundan silme ve düzenleme için URL 'YI kopyalayın. Bu bağlantıları test kullanıcısının bu işlemleri gerçekleştiremediğinizi doğrulamak için test kullanıcısının tarayıcısına yapıştırın.

## <a name="create-the-starter-app"></a>Başlangıç uygulamasını oluşturma

* "ContactManager" adlı bir Razor Pages uygulaması oluşturma
  * **Ayrı kullanıcı hesaplarıyla**uygulamayı oluşturun.
  * Ad alanı örnekte kullanılan ad alanıyla eşleşecek şekilde "ContactManager" olarak adlandırın.
  * `-uld`SQLite yerine LocalDB belirtir

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *Modeller/Ilgili kişi ekle. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* `Contact` Modeli dolandırıcıdan katlayın.
* İlk geçiş oluşturun ve veritabanını güncelleştirin:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

`dotnet aspnet-codegenerator razorpage` Komutuyla bir hata yaşarsanız, [Bu GitHub sorununa](https://github.com/aspnet/Scaffolding/issues/984)bakın.

* *Pages/Shared/_Layout. cshtml* dosyasındaki **ContactManager** bağlayıcısını güncelleştirin:

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* Kişiyi oluşturarak, düzenleyerek ve silerek uygulamayı test edin

### <a name="seed-the-database"></a>Veritabanını çekirdek

*Veri* klasörüne [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) sınıfını ekleyin:

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

Şuradan `SeedData.Initialize` `Main`arayın:

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Uygulamanın veritabanını sunan test edin. Kişi VERITABANıNDA herhangi bir satır varsa, çekirdek Yöntem çalıştırılmaz.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

Bu öğreticide, yetkilendirme tarafından korunan kullanıcı verileriyle ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı gösterilmektedir. Kimliği doğrulanmış (kayıtlı) kullanıcıların oluşturduğu kişilerin listesini görüntüler. Üç güvenlik grubu vardır:

* **Kayıtlı kullanıcılar** tüm onaylanan verileri görüntüleyebilir ve kendi verilerini düzenleyebilir/silebilir.
* **Yöneticiler** , kişi verilerini onaylayabilir veya reddedebilir. Yalnızca onaylanan kişiler kullanıcılara görünür.
* **Yöneticiler** , verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.

Aşağıdaki görüntüde, User Rick (`rick@example.com`) oturum açtı. Rick, onaylanan kişileri görüntüleyebilir ve **Düzenle**/**Delete**/' e ait kişiler için**yeni bağlantılar oluştur** ' a bakın. Yalnızca Rick tarafından oluşturulan son kayıt, **Düzenle** ve **Sil** bağlantılarını görüntüler. Yönetici veya yönetici durumu "Onaylandı" olarak değiştirene kadar diğer kullanıcılar son kaydı görmez.

![Rick oturum açmış olduğunu gösteren ekran görüntüsü](secure-data/_static/rick.png)

Aşağıdaki görüntüde, `manager@contoso.com` ve yöneticisinin rolünde oturum açıldı:

![Oturum açmış manager@contoso.com olduğunu gösteren ekran görüntüsü](secure-data/_static/manager1.png)

Aşağıdaki görüntüde, bir kişinin Yöneticiler Ayrıntılar görünümü gösterilmektedir:

![Yöneticinin bir kişinin görünümü](secure-data/_static/manager.png)

**Onaylama** ve **reddetme** düğmeleri yalnızca Yöneticiler ve yöneticiler için görüntülenir.

Aşağıdaki görüntüde, `admin@contoso.com` ve yönetici rolünde oturum açıldı:

![Oturum açmış admin@contoso.com olduğunu gösteren ekran görüntüsü](secure-data/_static/admin.png)

Yöneticinin tüm ayrıcalıkları vardır. Herhangi bir kişiyi okuyabilir/düzenleyebilir/silebilir ve kişilerin durumunu değiştirebilir.

Uygulama, aşağıdaki `Contact` model için [Yapı iskelesi](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tarafından oluşturulmuştur:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Örnek, aşağıdaki yetkilendirme işleyicilerini içerir:

* `ContactIsOwnerAuthorizationHandler`: Bir kullanıcının yalnızca verilerini düzenleyebilmesini sağlar.
* `ContactManagerAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini sağlar.
* `ContactAdministratorsAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini ve kişileri düzenlemesini/silmesini sağlar.

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici gelişmiş bir deyişle. Şunu tanımanız gerekir:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Kimlik doğrulaması](xref:security/authentication/identity)
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

Kullanıcıların verilerini düzenleyebilmeleri, ancak diğer kullanıcıların verilerini düzenleyebilmeleri için ASP.NET [Identity](xref:security/authentication/identity) Kullanıcı kimliğini kullanın. `ContactStatus` Model ekleyin `OwnerID` `Contact`

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID``AspNetUser` [kimlik](xref:security/authentication/identity) veritabanındaki tablodaki kullanıcının kimliği. Bu `Status` alan, bir kişinin genel kullanıcılar tarafından görüntülenebilir olup olmadığını belirler.

Yeni bir geçiş oluşturun ve veritabanını güncelleştirin:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Kimliğe rol hizmetleri Ekle

Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a>Kimliği doğrulanmış kullanıcılar iste

Kullanıcıların kimliklerinin doğrulanmasını gerektirmek için varsayılan kimlik doğrulama ilkesini ayarlayın:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Kimlik doğrulaması Razor sayfasında, denetleyicide veya eylem yöntemi düzeyinde `[AllowAnonymous]` özniteliğiyle devre dışı bırakabilirsiniz. Kullanıcıların kimliğinin doğrulanmasını gerektirmek için varsayılan kimlik doğrulama ilkesinin ayarlanması, yeni eklenen Razor Pages ve denetleyicilerin korunmasını sağlar. Varsayılan olarak kimlik doğrulamanın gerekli olması, yeni denetleyicilere bağlı olandan daha güvenlidir ve `[Authorize]` özniteliği dahil Razor Pages.

Anonim kullanıcıların, kayıt yaptırmadan önce site hakkında bilgi alması için dizine, hakkında ve Iletişim sayfalarına [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) ekleyin.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>Test hesabını yapılandırma

`SeedData` Sınıfı iki hesap oluşturur: yönetici ve yönetici. Bu hesaplara bir parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets) kullanın. Parolayı proje dizininden ayarlayın ( *program.cs*içeren dizin):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Güçlü bir parola belirtilmemişse, çağrıldığında bir özel durum oluşturulur `SeedData.Initialize` .

Sınama `Main` parolasını kullanacak şekilde güncelleştir:

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Test hesapları oluşturma ve kişileri güncelleştirme

Test hesapları `Initialize` oluşturmak için `SeedData` sınıfındaki yöntemi güncelleştirin:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

Yönetici kullanıcı KIMLIĞINI ve `ContactStatus` kişilerini ekleyin. Kişilerden birini "gönderildi" ve bir "reddedildi" yapın. Kullanıcı KIMLIĞINI ve durumunu tüm kişilere ekleyin. Yalnızca bir kişi gösterilir:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Sahip, yönetici ve yönetici yetkilendirme işleyicileri oluşturma

Bir *Yetkilendirme* klasörü oluşturun ve içinde bir `ContactIsOwnerAuthorizationHandler` sınıf oluşturun. Kullanıcının `ContactIsOwnerAuthorizationHandler` kaynağın sahibi olduğunu doğrular.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler` Çağıran [bağlamı. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)Kimliği doğrulanmış geçerli kullanıcı kişi sahibsiyse başarılı olur. Yetkilendirme işleyicileri genellikle:

* Gereksinimler `context.Succeed` karşılandığında döndürün.
* Gereksinimler `Task.CompletedTask` karşılanmazsa döndürün. `Task.CompletedTask`başarılı veya başarısız&mdash;değil, diğer yetkilendirme işleyicilerinin çalışmasına izin verir.

Açıkça başarısız olması gerekiyorsa, [bağlamı döndürün. Başarısız oldu](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Uygulama, iletişim sahiplerinin kendi verilerini düzenlemesine/silmesine/oluşturmasına izin verir. `ContactIsOwnerAuthorizationHandler`gereksinim parametresine geçirilen işlemi denetlemesi gerekmez.

### <a name="create-a-manager-authorization-handler"></a>Yönetici yetkilendirme işleyicisi oluşturma

Yetkilendirme klasöründe `ContactManagerAuthorizationHandler` bir sınıf oluşturun *Authorization* . Kaynak `ContactManagerAuthorizationHandler` üzerinde davranan kullanıcının bir yönetici olduğunu doğrular. İçerik değişikliklerini yalnızca Yöneticiler onaylayabilir veya reddedebilir (yeni veya değiştirilmiş).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Yönetici yetkilendirme işleyicisi oluşturma

Yetkilendirme klasöründe `ContactAdministratorsAuthorizationHandler` bir sınıf oluşturun *Authorization* . Kaynak `ContactAdministratorsAuthorizationHandler` üzerinde davranan kullanıcının bir yönetici olduğunu doğrular. Yönetici tüm işlemleri yapabilir.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Yetkilendirme işleyicilerini kaydetme

Entity Framework Core kullanan hizmetlerin, [Addkapsamlıdır](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)kullanılarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) için kayıtlı olması gerekir. , `ContactIsOwnerAuthorizationHandler` Entity Framework Core oluşturulan ASP.NET Core [kimliğini](xref:security/authentication/identity)kullanır. İşleyicileri hizmet koleksiyonuyla kaydedin, bu sayede `ContactsController` [bağımlılık ekleme](xref:fundamentals/dependency-injection)üzerinden kullanılabilir. Aşağıdaki kodu sonuna ekleyin `ConfigureServices`:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler`ve `ContactManagerAuthorizationHandler` tekton olarak eklenir. Bu, EF kullanmayan ve gereken tüm bilgiler `Context` `HandleRequirementAsync` yöntemin parametresinde olduğundan tektonlar vardır.

## <a name="support-authorization"></a>Destek yetkilendirme

Bu bölümde Razor Pages günceller ve bir işlem gereksinimleri sınıfı eklersiniz.

### <a name="review-the-contact-operations-requirements-class"></a>İlgili kişi işlemleri gereksinimleri sınıfını gözden geçirin

`ContactOperations` Sınıfını gözden geçirin. Bu sınıf, uygulamanın desteklediği gereksinimleri içerir:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Kişiler için bir temel sınıf oluşturun Razor Pages

Kişiler Razor Pages kullanılan hizmetleri içeren bir temel sınıf oluşturun. Temel sınıf, başlatma kodunu bir konuma koyar:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

Yukarıdaki kod:

* Yetkilendirme işleyicilerine `IAuthorizationService` erişim için hizmeti ekler.
* Kimlik `UserManager` hizmetini ekler.
* Öğesini ekleyin `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>CreateModel 'i Güncelleştir

Sayfa modeli oluşturma oluşturucusunu `DI_BasePageModel` temel sınıfı kullanacak şekilde güncelleştirin:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

`CreateModel.OnPostAsync` Yöntemi şu şekilde güncelleştirin:

* Kullanıcı KIMLIĞINI `Contact` modele ekleyin.
* Kullanıcının kişi oluşturma iznine sahip olduğunu doğrulamak için yetkilendirme işleyicisini çağırın.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Indexmodel 'i Güncelleştir

`OnGetAsync` Yöntemi yalnızca onaylanan kişilerin genel kullanıcılara gösterilmesi için güncelleştirin:

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

Yukarıdaki biçimlendirme birkaç `using` deyim ekliyor.

*Sayfalar/kişiler/Index. cshtml* 'deki **Düzenle** ve **Sil** bağlantılarını yalnızca uygun izinlere sahip kullanıcılar için işlendikleri şekilde güncelleştirin:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Verileri değiştirme izni olmayan kullanıcıların bağlantılarının gizlenmesi, uygulamanın güvenliğini sağlar. Bağlantıların gizlenmesi, yalnızca geçerli bağlantıları görüntüleyerek uygulamayı daha kolay hale getirir. Kullanıcılar, sahip olmadıkları veriler üzerinde düzenleme ve silme işlemlerini çağırmak için oluşturulan URL 'Leri hacme edebilir. Razor sayfası veya denetleyicinin, verilerin güvenliğini sağlamak için erişim denetimlerini zorlaması gerekir.

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

Tamamlanmış uygulamayı test etmenin kolay bir yolu da üç farklı tarayıcı (veya bilito/InPrivate oturumlar) kullanmaktır. Tek bir tarayıcıda yeni bir Kullanıcı kaydedin (örneğin, `test@example.com`). Her bir tarayıcıda farklı bir kullanıcıyla oturum açın. Aşağıdaki işlemleri doğrulayın:

* Kayıtlı kullanıcılar tüm onaylanan iletişim verilerini görüntüleyebilir.
* Kayıtlı kullanıcılar kendi verilerini düzenleyebilir/silebilir.
* Yöneticiler, kişi verilerini onaylayabilir/reddedebilir. `Details` Görünüm **Onayla** ve **Reddet** düğmelerini gösterir.
* Yöneticiler tüm verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.

| Kullanıcı                | Uygulama tarafından sağlanan | Seçenekler                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Hayır                | Kendi verilerini düzenleyin/silin.                |
| manager@contoso.com | Yes               | Kendi verilerini onaylama/reddetme ve düzenleme/silme. |
| admin@contoso.com   | Yes               | Tüm verileri onaylama/reddetme ve düzenleme/silme. |

Yöneticinin tarayıcısında bir kişi oluşturun. Yönetici iletişim kutusundan silme ve düzenleme için URL 'YI kopyalayın. Bu bağlantıları test kullanıcısının bu işlemleri gerçekleştiremediğinizi doğrulamak için test kullanıcısının tarayıcısına yapıştırın.

## <a name="create-the-starter-app"></a>Başlangıç uygulamasını oluşturma

* "ContactManager" adlı bir Razor sayfalar uygulaması oluşturma
  * **Ayrı kullanıcı hesaplarıyla**uygulamayı oluşturun.
  * Ad alanı örnekte kullanılan ad alanıyla eşleşecek şekilde "ContactManager" olarak adlandırın.
  * `-uld`SQLite yerine LocalDB belirtir

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *Modeller/Ilgili kişi ekle. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* `Contact` Modeli dolandırıcıdan katlayın.
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

Şuradan `SeedData.Initialize` `Main`arayın:

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Uygulamanın veritabanını sunan test edin. Kişi VERITABANıNDA herhangi bir satır varsa, çekirdek Yöntem çalıştırılmaz.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Ek kaynaklar

* [Azure App Service’te .NET Core ve SQL Veritabanı web uygulaması oluşturma](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core yetkilendirme Laboratuvarı](https://github.com/blowdart/AspNetAuthorizationWorkshop). Bu laboratuvar, bu öğreticide sunulan güvenlik özellikleri hakkında daha fazla ayrıntıya gider.
* <xref:security/authorization/introduction>
* [Özel ilke tabanlı yetkilendirme](xref:security/authorization/policies)
