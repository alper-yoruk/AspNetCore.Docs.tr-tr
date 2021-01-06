---
title: Yetkilendirme ile korunan kullanıcı verileriyle ASP.NET Core uygulama oluşturma
author: rick-anderson
description: Yetkilendirmeyle korunan kullanıcı verileriyle ASP.NET Core bir Web uygulaması oluşturmayı öğrenin. HTTPS, kimlik doğrulaması, güvenlik, içerir ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: dc70cfe7cb0c0f044f5f1e7ee68a293b3ea7507f
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854658"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="18e15-104">Yetkilendirmeyle korunan kullanıcı verileriyle ASP.NET Core bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="18e15-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="18e15-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [ali Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="18e15-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="18e15-106">[Bu PDF 'ye](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) bakın</span><span class="sxs-lookup"><span data-stu-id="18e15-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18e15-107">Bu öğreticide, yetkilendirme tarafından korunan kullanıcı verileriyle ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="18e15-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="18e15-108">Kimliği doğrulanmış (kayıtlı) kullanıcıların oluşturduğu kişilerin listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="18e15-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="18e15-109">Üç güvenlik grubu vardır:</span><span class="sxs-lookup"><span data-stu-id="18e15-109">There are three security groups:</span></span>

* <span data-ttu-id="18e15-110">**Kayıtlı kullanıcılar** tüm onaylanan verileri görüntüleyebilir ve kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="18e15-111">**Yöneticiler** , kişi verilerini onaylayabilir veya reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="18e15-112">Yalnızca onaylanan kişiler kullanıcılara görünür.</span><span class="sxs-lookup"><span data-stu-id="18e15-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="18e15-113">**Yöneticiler** , verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="18e15-114">Bu belgedeki görüntüler en son şablonlarla tam olarak eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="18e15-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="18e15-115">Aşağıdaki görüntüde, User Rick ( `rick@example.com` ) oturum açtı.</span><span class="sxs-lookup"><span data-stu-id="18e15-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="18e15-116">Rick, onaylanan kişileri görüntüleyebilir ve **Düzenle** / **Delete**' e / ait kişiler için **yeni bağlantılar oluştur** ' a bakın.</span><span class="sxs-lookup"><span data-stu-id="18e15-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="18e15-117">Yalnızca Rick tarafından oluşturulan son kayıt, **Düzenle** ve **Sil** bağlantılarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="18e15-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="18e15-118">Yönetici veya yönetici durumu "Onaylandı" olarak değiştirene kadar diğer kullanıcılar son kaydı görmez.</span><span class="sxs-lookup"><span data-stu-id="18e15-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick oturum açmış olduğunu gösteren ekran görüntüsü](secure-data/_static/rick.png)

<span data-ttu-id="18e15-120">Aşağıdaki görüntüde, `manager@contoso.com` ve yöneticisinin rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="18e15-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="18e15-122">Aşağıdaki görüntüde, bir kişinin Yöneticiler Ayrıntılar görünümü gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="18e15-122">The following image shows the managers details view of a contact:</span></span>

![Yöneticinin bir kişinin görünümü](secure-data/_static/manager.png)

<span data-ttu-id="18e15-124">**Onaylama** ve **reddetme** düğmeleri yalnızca Yöneticiler ve yöneticiler için görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="18e15-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="18e15-125">Aşağıdaki görüntüde, `admin@contoso.com` ve yönetici rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="18e15-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="18e15-127">Yöneticinin tüm ayrıcalıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="18e15-127">The administrator has all privileges.</span></span> <span data-ttu-id="18e15-128">Herhangi bir kişiyi okuyabilir/düzenleyebilir/silebilir ve kişilerin durumunu değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="18e15-129">Uygulama, aşağıdaki model için [Yapı iskelesi](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tarafından oluşturulmuştur `Contact` :</span><span class="sxs-lookup"><span data-stu-id="18e15-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="18e15-130">Örnek, aşağıdaki yetkilendirme işleyicilerini içerir:</span><span class="sxs-lookup"><span data-stu-id="18e15-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="18e15-131">`ContactIsOwnerAuthorizationHandler`: Bir kullanıcının yalnızca verilerini düzenleyebilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="18e15-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="18e15-132">`ContactManagerAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="18e15-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="18e15-133">`ContactAdministratorsAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini ve kişileri düzenlemesini/silmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="18e15-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="18e15-134">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="18e15-134">Prerequisites</span></span>

<span data-ttu-id="18e15-135">Bu öğretici gelişmiş bir deyişle.</span><span class="sxs-lookup"><span data-stu-id="18e15-135">This tutorial is advanced.</span></span> <span data-ttu-id="18e15-136">Şunu tanımanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="18e15-136">You should be familiar with:</span></span>

* [<span data-ttu-id="18e15-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18e15-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="18e15-138">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="18e15-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="18e15-139">Hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="18e15-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="18e15-140">Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="18e15-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="18e15-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="18e15-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="18e15-142">Başlatıcı ve tamamlanmış uygulama</span><span class="sxs-lookup"><span data-stu-id="18e15-142">The starter and completed app</span></span>

<span data-ttu-id="18e15-143">[Tamamlanmış](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) uygulamayı [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="18e15-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="18e15-144">Tamamlanmış uygulamayı [Test](#test-the-completed-app) edin, böylece güvenlik özellikleri hakkında bilgi sahibi olun.</span><span class="sxs-lookup"><span data-stu-id="18e15-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="18e15-145">Başlangıç uygulaması</span><span class="sxs-lookup"><span data-stu-id="18e15-145">The starter app</span></span>

<span data-ttu-id="18e15-146">[Başlangıç](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) uygulamasını [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="18e15-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="18e15-147">Uygulamayı çalıştırın, **ContactManager** bağlantısına dokunun ve bir kişi oluşturup silemdiğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="18e15-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="18e15-148">Başlangıç uygulamasını oluşturmak için bkz. [Başlangıç uygulamasını oluşturma](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="18e15-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="18e15-149">Güvenli Kullanıcı verileri</span><span class="sxs-lookup"><span data-stu-id="18e15-149">Secure user data</span></span>

<span data-ttu-id="18e15-150">Aşağıdaki bölümlerde, güvenli Kullanıcı verileri uygulaması oluşturmak için tüm önemli adımlar verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="18e15-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="18e15-151">Tamamlanmış projeye başvurmak faydalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="18e15-152">Kişi verilerini kullanıcıya bağlama</span><span class="sxs-lookup"><span data-stu-id="18e15-152">Tie the contact data to the user</span></span>

<span data-ttu-id="18e15-153">[Identity](xref:security/authentication/identity)Kullanıcıların verilerini düzenleyebilmeleri, ancak diğer kullanıcıların verilerini düzenleyebilmeleri için ASP.NET Kullanıcı kimliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="18e15-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="18e15-154">`OwnerID`Model ekleyin `ContactStatus` `Contact` :</span><span class="sxs-lookup"><span data-stu-id="18e15-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="18e15-155">`OwnerID` kullanıcının `AspNetUser` veritabanındaki TABLODAKI kimliği [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="18e15-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="18e15-156">Bu `Status` alan, bir kişinin genel kullanıcılar tarafından görüntülenebilir olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="18e15-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="18e15-157">Yeni bir geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="18e15-158">Rol hizmetlerini Ekle Identity</span><span class="sxs-lookup"><span data-stu-id="18e15-158">Add Role services to Identity</span></span>

<span data-ttu-id="18e15-159">Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="18e15-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="18e15-160">Kimliği doğrulanmış kullanıcılar iste</span><span class="sxs-lookup"><span data-stu-id="18e15-160">Require authenticated users</span></span>

<span data-ttu-id="18e15-161">Kullanıcıların kimliklerinin doğrulanmasını gerektirmek için geri dönüş kimlik doğrulama ilkesini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="18e15-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="18e15-162">Önceki vurgulanmış kod, [geri dönüş kimlik doğrulama ilkesini](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="18e15-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="18e15-163">Geri dönüş kimlik doğrulama ilkesi,\*\* Razor bir kimlik doğrulama özniteliğine sahip sayfalar, denetleyiciler veya eylem yöntemleri dışında \* tüm _ kullanıcıların kimliğinin doğrulanmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="18e15-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="18e15-164">Örneğin, Razor Sayfalar, denetleyiciler veya eylem yöntemleri, `[AllowAnonymous]` `[Authorize(PolicyName="MyPolicy")]` geri dönüş kimlik doğrulama ilkesi yerine uygulanan kimlik doğrulama özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="18e15-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="18e15-165">Geri dönüş kimlik doğrulama ilkesi:</span><span class="sxs-lookup"><span data-stu-id="18e15-165">The fallback authentication policy:</span></span>

<span data-ttu-id="18e15-166">_ Açıkça bir kimlik doğrulama ilkesi belirtmeyen tüm isteklere uygulanır.</span><span class="sxs-lookup"><span data-stu-id="18e15-166">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="18e15-167">Endpoint Routing tarafından sunulan istekler için, bu, yetkilendirme özniteliği belirtmeyen tüm uç noktaları içerir.</span><span class="sxs-lookup"><span data-stu-id="18e15-167">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="18e15-168">[Statik dosyalar](xref:fundamentals/static-files)gibi yetkilendirme ara yazılımı sonrasında diğer ara yazılım tarafından hizmet verilen istekler için, bu ilkeyi tüm isteklere uygular.</span><span class="sxs-lookup"><span data-stu-id="18e15-168">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="18e15-169">Geri dönüş kimlik doğrulama ilkesini kullanıcıların kimliğinin doğrulanmasını gerektirecek şekilde ayarlamak, yeni eklenen Razor sayfaları ve denetleyicileri korur.</span><span class="sxs-lookup"><span data-stu-id="18e15-169">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="18e15-170">Varsayılan olarak kimlik doğrulamanın gerekli olması, yeni denetleyicilere ve Razor sayfalarına bağlı olarak, özniteliğini dahil etmek için daha güvenlidir `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="18e15-170">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="18e15-171"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Sınıfı da içerir <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="18e15-171">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="18e15-172">, `DefaultPolicy` `[Authorize]` İlke belirtilmediğinde özniteliğiyle kullanılan ilkedir.</span><span class="sxs-lookup"><span data-stu-id="18e15-172">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="18e15-173">`[Authorize]` , aksine adlandırılmış bir ilke içermez `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="18e15-173">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="18e15-174">İlkeler hakkında daha fazla bilgi için bkz <xref:security/authorization/policies> ..</span><span class="sxs-lookup"><span data-stu-id="18e15-174">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="18e15-175">MVC denetleyicilerinin ve Razor sayfaların tüm kullanıcıların kimliğinin doğrulanmasını gerektirmek için alternatif bir yol yetkilendirme filtresi ekliyor:</span><span class="sxs-lookup"><span data-stu-id="18e15-175">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="18e15-176">Yukarıdaki kod, bir yetkilendirme filtresi kullanır ve geri dönüş ilkesini ayarlama Endpoint Routing kullanır.</span><span class="sxs-lookup"><span data-stu-id="18e15-176">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="18e15-177">Geri dönüş ilkesini ayarlamak, tüm kullanıcıların kimliğinin doğrulanmasını gerektirmek için tercih edilen yoldur.</span><span class="sxs-lookup"><span data-stu-id="18e15-177">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="18e15-178">[](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) `Index` `Privacy` Anonim kullanıcıların, kaydolmadan önce site hakkında bilgi alması Için ve sayfalarına AllowAnonymous ekleyin:</span><span class="sxs-lookup"><span data-stu-id="18e15-178">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="18e15-179">Test hesabını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="18e15-179">Configure the test account</span></span>

<span data-ttu-id="18e15-180">`SeedData`Sınıfı iki hesap oluşturur: yönetici ve yönetici.</span><span class="sxs-lookup"><span data-stu-id="18e15-180">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="18e15-181">Bu hesaplara bir parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets) kullanın.</span><span class="sxs-lookup"><span data-stu-id="18e15-181">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="18e15-182">Parolayı proje dizininden ayarlayın ( *program.cs* içeren dizin):</span><span class="sxs-lookup"><span data-stu-id="18e15-182">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="18e15-183">Güçlü bir parola belirtilmemişse, çağrıldığında bir özel durum oluşturulur `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="18e15-183">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="18e15-184">`Main`Sınama parolasını kullanacak şekilde güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="18e15-184">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="18e15-185">Test hesapları oluşturma ve kişileri güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="18e15-185">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="18e15-186">`Initialize` `SeedData` Test hesapları oluşturmak için sınıfındaki yöntemi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-186">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="18e15-187">Yönetici kullanıcı KIMLIĞINI ve kişilerini ekleyin `ContactStatus` .</span><span class="sxs-lookup"><span data-stu-id="18e15-187">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="18e15-188">Kişilerden birini "gönderildi" ve bir "reddedildi" yapın.</span><span class="sxs-lookup"><span data-stu-id="18e15-188">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="18e15-189">Kullanıcı KIMLIĞINI ve durumunu tüm kişilere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="18e15-189">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="18e15-190">Yalnızca bir kişi gösterilir:</span><span class="sxs-lookup"><span data-stu-id="18e15-190">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="18e15-191">Sahip, yönetici ve yönetici yetkilendirme işleyicileri oluşturma</span><span class="sxs-lookup"><span data-stu-id="18e15-191">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="18e15-192">`ContactIsOwnerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="18e15-192">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="18e15-193">`ContactIsOwnerAuthorizationHandler`Kullanıcının kaynağın sahibi olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="18e15-193">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="18e15-194">`ContactIsOwnerAuthorizationHandler`Çağıran [bağlamı. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)Kimliği doğrulanmış geçerli kullanıcı kişi sahibsiyse başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="18e15-194">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="18e15-195">Yetkilendirme işleyicileri genellikle:</span><span class="sxs-lookup"><span data-stu-id="18e15-195">Authorization handlers generally:</span></span>

* <span data-ttu-id="18e15-196">`context.Succeed`Gereksinimler karşılandığında döndürün.</span><span class="sxs-lookup"><span data-stu-id="18e15-196">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="18e15-197">`Task.CompletedTask`Gereksinimler karşılanmazsa döndürün.</span><span class="sxs-lookup"><span data-stu-id="18e15-197">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="18e15-198">`Task.CompletedTask` başarılı veya başarısız değil, &mdash; diğer yetkilendirme işleyicilerinin çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="18e15-198">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="18e15-199">Açıkça başarısız olması gerekiyorsa, [bağlamı döndürün. Başarısız oldu](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="18e15-199">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="18e15-200">Uygulama, iletişim sahiplerinin kendi verilerini düzenlemesine/silmesine/oluşturmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="18e15-200">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="18e15-201">`ContactIsOwnerAuthorizationHandler` gereksinim parametresine geçirilen işlemi denetlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="18e15-201">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="18e15-202">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="18e15-202">Create a manager authorization handler</span></span>

<span data-ttu-id="18e15-203">`ContactManagerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="18e15-203">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="18e15-204">`ContactManagerAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="18e15-204">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="18e15-205">İçerik değişikliklerini yalnızca Yöneticiler onaylayabilir veya reddedebilir (yeni veya değiştirilmiş).</span><span class="sxs-lookup"><span data-stu-id="18e15-205">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="18e15-206">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="18e15-206">Create an administrator authorization handler</span></span>

<span data-ttu-id="18e15-207">`ContactAdministratorsAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="18e15-207">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="18e15-208">`ContactAdministratorsAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="18e15-208">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="18e15-209">Yönetici tüm işlemleri yapabilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-209">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="18e15-210">Yetkilendirme işleyicilerini kaydetme</span><span class="sxs-lookup"><span data-stu-id="18e15-210">Register the authorization handlers</span></span>

<span data-ttu-id="18e15-211">Entity Framework Core kullanan hizmetlerin, [Addkapsamlıdır](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)kullanılarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) için kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="18e15-211">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="18e15-212">, `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core oluşturulan ASP.NET Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="18e15-212">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="18e15-213">İşleyicileri hizmet koleksiyonuyla kaydedin, bu sayede `ContactsController` [bağımlılık ekleme](xref:fundamentals/dependency-injection)üzerinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-213">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="18e15-214">Aşağıdaki kodu sonuna ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="18e15-214">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="18e15-215">`ContactAdministratorsAuthorizationHandler` ve `ContactManagerAuthorizationHandler` tekton olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="18e15-215">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="18e15-216">Bu, EF kullanmayan ve gereken tüm bilgiler yöntemin parametresinde olduğundan tektonlar vardır `Context` `HandleRequirementAsync` .</span><span class="sxs-lookup"><span data-stu-id="18e15-216">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="18e15-217">Destek yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="18e15-217">Support authorization</span></span>

<span data-ttu-id="18e15-218">Bu bölümde, Razor sayfaları güncelleştirir ve bir işlem gereksinimleri sınıfı eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="18e15-218">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="18e15-219">İlgili kişi işlemleri gereksinimleri sınıfını gözden geçirin</span><span class="sxs-lookup"><span data-stu-id="18e15-219">Review the contact operations requirements class</span></span>

<span data-ttu-id="18e15-220">Sınıfını gözden geçirin `ContactOperations` .</span><span class="sxs-lookup"><span data-stu-id="18e15-220">Review the `ContactOperations` class.</span></span> <span data-ttu-id="18e15-221">Bu sınıf, uygulamanın desteklediği gereksinimleri içerir:</span><span class="sxs-lookup"><span data-stu-id="18e15-221">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="18e15-222">Kişiler sayfaları için temel sınıf oluşturma Razor</span><span class="sxs-lookup"><span data-stu-id="18e15-222">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="18e15-223">Kişiler sayfalarında kullanılan hizmetleri içeren bir temel sınıf oluşturun Razor .</span><span class="sxs-lookup"><span data-stu-id="18e15-223">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="18e15-224">Temel sınıf, başlatma kodunu bir konuma koyar:</span><span class="sxs-lookup"><span data-stu-id="18e15-224">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="18e15-225">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="18e15-225">The preceding code:</span></span>

* <span data-ttu-id="18e15-226">`IAuthorizationService`Yetkilendirme işleyicilerine erişim için hizmeti ekler.</span><span class="sxs-lookup"><span data-stu-id="18e15-226">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="18e15-227">Hizmeti ekler Identity `UserManager` .</span><span class="sxs-lookup"><span data-stu-id="18e15-227">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="18e15-228">Öğesini ekleyin `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="18e15-228">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="18e15-229">CreateModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="18e15-229">Update the CreateModel</span></span>

<span data-ttu-id="18e15-230">Sayfa modeli oluşturma oluşturucusunu temel sınıfı kullanacak şekilde güncelleştirin `DI_BasePageModel` :</span><span class="sxs-lookup"><span data-stu-id="18e15-230">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="18e15-231">Yöntemi şu `CreateModel.OnPostAsync` şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-231">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="18e15-232">Kullanıcı KIMLIĞINI `Contact` modele ekleyin.</span><span class="sxs-lookup"><span data-stu-id="18e15-232">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="18e15-233">Kullanıcının kişi oluşturma iznine sahip olduğunu doğrulamak için yetkilendirme işleyicisini çağırın.</span><span class="sxs-lookup"><span data-stu-id="18e15-233">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="18e15-234">Indexmodel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="18e15-234">Update the IndexModel</span></span>

<span data-ttu-id="18e15-235">`OnGetAsync`Yöntemi yalnızca onaylanan kişilerin genel kullanıcılara gösterilmesi için güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-235">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="18e15-236">EditModel güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="18e15-236">Update the EditModel</span></span>

<span data-ttu-id="18e15-237">Kullanıcının kişiye ait olduğunu doğrulamak için bir yetkilendirme işleyicisi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="18e15-237">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="18e15-238">Kaynak yetkilendirmesi doğrulandığında, `[Authorize]` öznitelik yeterli değildir.</span><span class="sxs-lookup"><span data-stu-id="18e15-238">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="18e15-239">Öznitelikler değerlendirildiğinde uygulamanın kaynağa erişimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="18e15-239">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="18e15-240">Kaynak tabanlı yetkilendirme sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="18e15-240">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="18e15-241">Uygulamanın kaynağa erişimi olduğunda, sayfa modeline yükleyerek veya işleyicinin kendisi içine yükleyerek denetimlerin gerçekleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="18e15-241">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="18e15-242">Kaynak anahtarını geçirerek kaynağa sıkça erişirsiniz.</span><span class="sxs-lookup"><span data-stu-id="18e15-242">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="18e15-243">DeleteModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="18e15-243">Update the DeleteModel</span></span>

<span data-ttu-id="18e15-244">Kullanıcının kişide silme iznine sahip olduğunu doğrulamak için, silme sayfası modelini yetkilendirme işleyicisini kullanacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="18e15-244">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="18e15-245">Yetkilendirme hizmetini görünümlere ekleme</span><span class="sxs-lookup"><span data-stu-id="18e15-245">Inject the authorization service into the views</span></span>

<span data-ttu-id="18e15-246">Şu anda kullanıcı ARABIRIMI, kullanıcının değiştiremeyeceğiniz kişiler için Düzenle ve Sil bağlantılarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="18e15-246">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="18e15-247">Yetkilendirme hizmetini *Sayfalar/_ViewImports. cshtml* dosyasına ekleme, bu nedenle tüm görünümlerde kullanılabilir hale gelir:</span><span class="sxs-lookup"><span data-stu-id="18e15-247">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="18e15-248">Yukarıdaki biçimlendirme birkaç deyim ekliyor `using` .</span><span class="sxs-lookup"><span data-stu-id="18e15-248">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="18e15-249">*Sayfalar/kişiler/Index. cshtml* 'deki **Düzenle** ve **Sil** bağlantılarını yalnızca uygun izinlere sahip kullanıcılar için işlendikleri şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-249">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="18e15-250">Verileri değiştirme izni olmayan kullanıcıların bağlantılarının gizlenmesi, uygulamanın güvenliğini sağlar.</span><span class="sxs-lookup"><span data-stu-id="18e15-250">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="18e15-251">Bağlantıların gizlenmesi, yalnızca geçerli bağlantıları görüntüleyerek uygulamayı daha kolay hale getirir.</span><span class="sxs-lookup"><span data-stu-id="18e15-251">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="18e15-252">Kullanıcılar, sahip olmadıkları veriler üzerinde düzenleme ve silme işlemlerini çağırmak için oluşturulan URL 'Leri hacme edebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-252">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="18e15-253">RazorSayfanın veya denetleyicinin, verilerin güvenliğini sağlamak için erişim denetimlerini zorlaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="18e15-253">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="18e15-254">Güncelleştirme ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="18e15-254">Update Details</span></span>

<span data-ttu-id="18e15-255">Yöneticilerin kişileri onaylamasını veya reddedebilmesi için Ayrıntılar görünümünü güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-255">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="18e15-256">Ayrıntılar sayfası modelini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-256">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="18e15-257">Bir role Kullanıcı ekleme veya rolü kaldırma</span><span class="sxs-lookup"><span data-stu-id="18e15-257">Add or remove a user to a role</span></span>

<span data-ttu-id="18e15-258">Hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8502) bakın:</span><span class="sxs-lookup"><span data-stu-id="18e15-258">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="18e15-259">Bir kullanıcıdan ayrıcalıklar kaldırılıyor.</span><span class="sxs-lookup"><span data-stu-id="18e15-259">Removing privileges from a user.</span></span> <span data-ttu-id="18e15-260">Örneğin, bir sohbet uygulamasındaki kullanıcıyı kapatma.</span><span class="sxs-lookup"><span data-stu-id="18e15-260">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="18e15-261">Bir kullanıcıya ayrıcalık ekleme.</span><span class="sxs-lookup"><span data-stu-id="18e15-261">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="18e15-262">Challenge ve Fordeklarasyon arasındaki farklar</span><span class="sxs-lookup"><span data-stu-id="18e15-262">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="18e15-263">Bu uygulama, varsayılan ilkeyi [kimliği doğrulanmış kullanıcıları gerektirecek](#rau)şekilde ayarlar.</span><span class="sxs-lookup"><span data-stu-id="18e15-263">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="18e15-264">Aşağıdaki kod anonim kullanıcılara izin verir.</span><span class="sxs-lookup"><span data-stu-id="18e15-264">The following code allows anonymous users.</span></span> <span data-ttu-id="18e15-265">Anonim kullanıcılara, çekişme ile Fordeklarasyon arasındaki farkları gösterme izni verilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-265">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="18e15-266">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="18e15-266">In the preceding code:</span></span>

* <span data-ttu-id="18e15-267">Kullanıcının kimliği **doğrulanmadığı** zaman, bir `ChallengeResult` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="18e15-267">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="18e15-268">Bir `ChallengeResult` döndürüldüğünde, Kullanıcı oturum açma sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-268">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="18e15-269">Kullanıcının kimliği doğrulandığında, ancak yetkilendirilmediğinde, bir `ForbidResult` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="18e15-269">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="18e15-270">Bir `ForbidResult` döndürüldüğünde, Kullanıcı erişim reddedildi sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-270">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="18e15-271">Tamamlanmış uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="18e15-271">Test the completed app</span></span>

<span data-ttu-id="18e15-272">Önceden oluşturulmuş kullanıcı hesapları için bir parola ayarlamadıysanız, parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets#secret-manager) kullanın:</span><span class="sxs-lookup"><span data-stu-id="18e15-272">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="18e15-273">Güçlü bir parola seçin: sekiz veya daha fazla karakter, en az bir büyük harf karakter, sayı ve simge kullanın.</span><span class="sxs-lookup"><span data-stu-id="18e15-273">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="18e15-274">Örneğin, `Passw0rd!` güçlü parola gereksinimlerini karşılar.</span><span class="sxs-lookup"><span data-stu-id="18e15-274">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="18e15-275">Aşağıdaki komutu projenin klasöründen çalıştırın, burada `<PW>` parola olur:</span><span class="sxs-lookup"><span data-stu-id="18e15-275">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="18e15-276">Uygulamanın kişileri varsa:</span><span class="sxs-lookup"><span data-stu-id="18e15-276">If the app has contacts:</span></span>

* <span data-ttu-id="18e15-277">Tablodaki tüm kayıtları silin `Contact` .</span><span class="sxs-lookup"><span data-stu-id="18e15-277">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="18e15-278">Veritabanını temel alarak uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="18e15-278">Restart the app to seed the database.</span></span>

<span data-ttu-id="18e15-279">Tamamlanmış uygulamayı test etmenin kolay bir yolu da üç farklı tarayıcı (veya bilito/InPrivate oturumlar) kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="18e15-279">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="18e15-280">Tek bir tarayıcıda yeni bir Kullanıcı kaydedin (örneğin, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="18e15-280">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="18e15-281">Her bir tarayıcıda farklı bir kullanıcıyla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="18e15-281">Sign in to each browser with a different user.</span></span> <span data-ttu-id="18e15-282">Aşağıdaki işlemleri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="18e15-282">Verify the following operations:</span></span>

* <span data-ttu-id="18e15-283">Kayıtlı kullanıcılar tüm onaylanan iletişim verilerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-283">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="18e15-284">Kayıtlı kullanıcılar kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-284">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="18e15-285">Yöneticiler, kişi verilerini onaylayabilir/reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-285">Managers can approve/reject contact data.</span></span> <span data-ttu-id="18e15-286">`Details`Görünüm **Onayla** ve **Reddet** düğmelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="18e15-286">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="18e15-287">Yöneticiler tüm verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-287">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="18e15-288">Kullanıcı</span><span class="sxs-lookup"><span data-stu-id="18e15-288">User</span></span>                | <span data-ttu-id="18e15-289">Uygulama tarafından sağlanan</span><span class="sxs-lookup"><span data-stu-id="18e15-289">Seeded by the app</span></span> | <span data-ttu-id="18e15-290">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="18e15-290">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="18e15-291">Hayır</span><span class="sxs-lookup"><span data-stu-id="18e15-291">No</span></span>                | <span data-ttu-id="18e15-292">Kendi verilerini düzenleyin/silin.</span><span class="sxs-lookup"><span data-stu-id="18e15-292">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="18e15-293">Yes</span><span class="sxs-lookup"><span data-stu-id="18e15-293">Yes</span></span>               | <span data-ttu-id="18e15-294">Kendi verilerini onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="18e15-294">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="18e15-295">Yes</span><span class="sxs-lookup"><span data-stu-id="18e15-295">Yes</span></span>               | <span data-ttu-id="18e15-296">Tüm verileri onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="18e15-296">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="18e15-297">Yöneticinin tarayıcısında bir kişi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="18e15-297">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="18e15-298">Yönetici iletişim kutusundan silme ve düzenleme için URL 'YI kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="18e15-298">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="18e15-299">Bu bağlantıları test kullanıcısının bu işlemleri gerçekleştiremediğinizi doğrulamak için test kullanıcısının tarayıcısına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="18e15-299">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="18e15-300">Başlangıç uygulamasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="18e15-300">Create the starter app</span></span>

* <span data-ttu-id="18e15-301">Razor"ContactManager" adlı bir sayfalar uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="18e15-301">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="18e15-302">**Ayrı kullanıcı hesaplarıyla** uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="18e15-302">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="18e15-303">Ad alanı örnekte kullanılan ad alanıyla eşleşecek şekilde "ContactManager" olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="18e15-303">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="18e15-304">`-uld` SQLite yerine LocalDB belirtir</span><span class="sxs-lookup"><span data-stu-id="18e15-304">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="18e15-305">*Modeller/Ilgili kişi ekle. cs*:</span><span class="sxs-lookup"><span data-stu-id="18e15-305">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="18e15-306">Modeli dolandırıcıdan katlayın `Contact` .</span><span class="sxs-lookup"><span data-stu-id="18e15-306">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="18e15-307">İlk geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-307">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="18e15-308">Komutuyla bir hata yaşarsanız `dotnet aspnet-codegenerator razorpage` , [Bu GitHub sorununa](https://github.com/aspnet/Scaffolding/issues/984)bakın.</span><span class="sxs-lookup"><span data-stu-id="18e15-308">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="18e15-309">*Pages/Shared/_Layout. cshtml* dosyasındaki **ContactManager** bağlayıcısını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-309">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="18e15-310">Kişiyi oluşturarak, düzenleyerek ve silerek uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="18e15-310">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="18e15-311">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="18e15-311">Seed the database</span></span>

<span data-ttu-id="18e15-312">*Veri* klasörüne [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) sınıfını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="18e15-312">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="18e15-313">`SeedData.Initialize`Şuradan arayın `Main` :</span><span class="sxs-lookup"><span data-stu-id="18e15-313">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="18e15-314">Uygulamanın veritabanını sunan test edin.</span><span class="sxs-lookup"><span data-stu-id="18e15-314">Test that the app seeded the database.</span></span> <span data-ttu-id="18e15-315">Kişi VERITABANıNDA herhangi bir satır varsa, çekirdek Yöntem çalıştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="18e15-315">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="18e15-316">Bu öğreticide, yetkilendirme tarafından korunan kullanıcı verileriyle ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="18e15-316">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="18e15-317">Kimliği doğrulanmış (kayıtlı) kullanıcıların oluşturduğu kişilerin listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="18e15-317">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="18e15-318">Üç güvenlik grubu vardır:</span><span class="sxs-lookup"><span data-stu-id="18e15-318">There are three security groups:</span></span>

* <span data-ttu-id="18e15-319">**Kayıtlı kullanıcılar** tüm onaylanan verileri görüntüleyebilir ve kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-319">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="18e15-320">**Yöneticiler** , kişi verilerini onaylayabilir veya reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-320">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="18e15-321">Yalnızca onaylanan kişiler kullanıcılara görünür.</span><span class="sxs-lookup"><span data-stu-id="18e15-321">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="18e15-322">**Yöneticiler** , verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-322">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="18e15-323">Aşağıdaki görüntüde, User Rick ( `rick@example.com` ) oturum açtı.</span><span class="sxs-lookup"><span data-stu-id="18e15-323">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="18e15-324">Rick, onaylanan kişileri görüntüleyebilir ve **Düzenle** / **Delete**' e / ait kişiler için **yeni bağlantılar oluştur** ' a bakın.</span><span class="sxs-lookup"><span data-stu-id="18e15-324">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="18e15-325">Yalnızca Rick tarafından oluşturulan son kayıt, **Düzenle** ve **Sil** bağlantılarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="18e15-325">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="18e15-326">Yönetici veya yönetici durumu "Onaylandı" olarak değiştirene kadar diğer kullanıcılar son kaydı görmez.</span><span class="sxs-lookup"><span data-stu-id="18e15-326">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick oturum açmış olduğunu gösteren ekran görüntüsü](secure-data/_static/rick.png)

<span data-ttu-id="18e15-328">Aşağıdaki görüntüde, `manager@contoso.com` ve yöneticisinin rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="18e15-328">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="18e15-330">Aşağıdaki görüntüde, bir kişinin Yöneticiler Ayrıntılar görünümü gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="18e15-330">The following image shows the managers details view of a contact:</span></span>

![Yöneticinin bir kişinin görünümü](secure-data/_static/manager.png)

<span data-ttu-id="18e15-332">**Onaylama** ve **reddetme** düğmeleri yalnızca Yöneticiler ve yöneticiler için görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="18e15-332">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="18e15-333">Aşağıdaki görüntüde, `admin@contoso.com` ve yönetici rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="18e15-333">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="18e15-335">Yöneticinin tüm ayrıcalıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="18e15-335">The administrator has all privileges.</span></span> <span data-ttu-id="18e15-336">Herhangi bir kişiyi okuyabilir/düzenleyebilir/silebilir ve kişilerin durumunu değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-336">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="18e15-337">Uygulama, aşağıdaki model için [Yapı iskelesi](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tarafından oluşturulmuştur `Contact` :</span><span class="sxs-lookup"><span data-stu-id="18e15-337">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="18e15-338">Örnek, aşağıdaki yetkilendirme işleyicilerini içerir:</span><span class="sxs-lookup"><span data-stu-id="18e15-338">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="18e15-339">`ContactIsOwnerAuthorizationHandler`: Bir kullanıcının yalnızca verilerini düzenleyebilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="18e15-339">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="18e15-340">`ContactManagerAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="18e15-340">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="18e15-341">`ContactAdministratorsAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini ve kişileri düzenlemesini/silmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="18e15-341">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="18e15-342">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="18e15-342">Prerequisites</span></span>

<span data-ttu-id="18e15-343">Bu öğretici gelişmiş bir deyişle.</span><span class="sxs-lookup"><span data-stu-id="18e15-343">This tutorial is advanced.</span></span> <span data-ttu-id="18e15-344">Şunu tanımanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="18e15-344">You should be familiar with:</span></span>

* [<span data-ttu-id="18e15-345">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18e15-345">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="18e15-346">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="18e15-346">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="18e15-347">Hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="18e15-347">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="18e15-348">Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="18e15-348">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="18e15-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="18e15-349">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="18e15-350">Başlatıcı ve tamamlanmış uygulama</span><span class="sxs-lookup"><span data-stu-id="18e15-350">The starter and completed app</span></span>

<span data-ttu-id="18e15-351">[Tamamlanmış](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) uygulamayı [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="18e15-351">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="18e15-352">Tamamlanmış uygulamayı [Test](#test-the-completed-app) edin, böylece güvenlik özellikleri hakkında bilgi sahibi olun.</span><span class="sxs-lookup"><span data-stu-id="18e15-352">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="18e15-353">Başlangıç uygulaması</span><span class="sxs-lookup"><span data-stu-id="18e15-353">The starter app</span></span>

<span data-ttu-id="18e15-354">[Başlangıç](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) uygulamasını [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="18e15-354">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="18e15-355">Uygulamayı çalıştırın, **ContactManager** bağlantısına dokunun ve bir kişi oluşturup silemdiğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="18e15-355">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="18e15-356">Güvenli Kullanıcı verileri</span><span class="sxs-lookup"><span data-stu-id="18e15-356">Secure user data</span></span>

<span data-ttu-id="18e15-357">Aşağıdaki bölümlerde, güvenli Kullanıcı verileri uygulaması oluşturmak için tüm önemli adımlar verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="18e15-357">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="18e15-358">Tamamlanmış projeye başvurmak faydalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-358">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="18e15-359">Kişi verilerini kullanıcıya bağlama</span><span class="sxs-lookup"><span data-stu-id="18e15-359">Tie the contact data to the user</span></span>

<span data-ttu-id="18e15-360">[Identity](xref:security/authentication/identity)Kullanıcıların verilerini düzenleyebilmeleri, ancak diğer kullanıcıların verilerini düzenleyebilmeleri için ASP.NET Kullanıcı kimliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="18e15-360">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="18e15-361">`OwnerID`Model ekleyin `ContactStatus` `Contact` :</span><span class="sxs-lookup"><span data-stu-id="18e15-361">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="18e15-362">`OwnerID` kullanıcının `AspNetUser` veritabanındaki TABLODAKI kimliği [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="18e15-362">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="18e15-363">Bu `Status` alan, bir kişinin genel kullanıcılar tarafından görüntülenebilir olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="18e15-363">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="18e15-364">Yeni bir geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-364">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="18e15-365">Rol hizmetlerini Ekle Identity</span><span class="sxs-lookup"><span data-stu-id="18e15-365">Add Role services to Identity</span></span>

<span data-ttu-id="18e15-366">Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="18e15-366">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="18e15-367">Kimliği doğrulanmış kullanıcılar iste</span><span class="sxs-lookup"><span data-stu-id="18e15-367">Require authenticated users</span></span>

<span data-ttu-id="18e15-368">Kullanıcıların kimliklerinin doğrulanmasını gerektirmek için varsayılan kimlik doğrulama ilkesini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="18e15-368">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="18e15-369">Bir Razor sayfada, denetleyicide veya eylem yöntemi düzeyinde kimlik doğrulamasından vazgeçebilirsiniz `[AllowAnonymous]` .</span><span class="sxs-lookup"><span data-stu-id="18e15-369">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="18e15-370">Varsayılan kimlik doğrulama ilkesini kullanıcıların kimliğinin doğrulanmasını gerektirecek şekilde ayarlamak, yeni eklenen Razor sayfaları ve denetleyicileri korur.</span><span class="sxs-lookup"><span data-stu-id="18e15-370">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="18e15-371">Varsayılan olarak kimlik doğrulamanın gerekli olması, yeni denetleyicilere ve Razor sayfalarına bağlı olarak, özniteliğini dahil etmek için daha güvenlidir `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="18e15-371">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="18e15-372">Anonim kullanıcıların, kayıt yaptırmadan önce site hakkında bilgi alması için dizine, hakkında ve Iletişim sayfalarına [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="18e15-372">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="18e15-373">Test hesabını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="18e15-373">Configure the test account</span></span>

<span data-ttu-id="18e15-374">`SeedData`Sınıfı iki hesap oluşturur: yönetici ve yönetici.</span><span class="sxs-lookup"><span data-stu-id="18e15-374">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="18e15-375">Bu hesaplara bir parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets) kullanın.</span><span class="sxs-lookup"><span data-stu-id="18e15-375">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="18e15-376">Parolayı proje dizininden ayarlayın ( *program.cs* içeren dizin):</span><span class="sxs-lookup"><span data-stu-id="18e15-376">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="18e15-377">Güçlü bir parola belirtilmemişse, çağrıldığında bir özel durum oluşturulur `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="18e15-377">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="18e15-378">`Main`Sınama parolasını kullanacak şekilde güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="18e15-378">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="18e15-379">Test hesapları oluşturma ve kişileri güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="18e15-379">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="18e15-380">`Initialize` `SeedData` Test hesapları oluşturmak için sınıfındaki yöntemi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-380">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="18e15-381">Yönetici kullanıcı KIMLIĞINI ve kişilerini ekleyin `ContactStatus` .</span><span class="sxs-lookup"><span data-stu-id="18e15-381">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="18e15-382">Kişilerden birini "gönderildi" ve bir "reddedildi" yapın.</span><span class="sxs-lookup"><span data-stu-id="18e15-382">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="18e15-383">Kullanıcı KIMLIĞINI ve durumunu tüm kişilere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="18e15-383">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="18e15-384">Yalnızca bir kişi gösterilir:</span><span class="sxs-lookup"><span data-stu-id="18e15-384">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="18e15-385">Sahip, yönetici ve yönetici yetkilendirme işleyicileri oluşturma</span><span class="sxs-lookup"><span data-stu-id="18e15-385">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="18e15-386">Bir *Yetkilendirme* klasörü oluşturun ve içinde bir `ContactIsOwnerAuthorizationHandler` sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="18e15-386">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="18e15-387">`ContactIsOwnerAuthorizationHandler`Kullanıcının kaynağın sahibi olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="18e15-387">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="18e15-388">`ContactIsOwnerAuthorizationHandler`Çağıran [bağlamı. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)Kimliği doğrulanmış geçerli kullanıcı kişi sahibsiyse başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="18e15-388">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="18e15-389">Yetkilendirme işleyicileri genellikle:</span><span class="sxs-lookup"><span data-stu-id="18e15-389">Authorization handlers generally:</span></span>

* <span data-ttu-id="18e15-390">`context.Succeed`Gereksinimler karşılandığında döndürün.</span><span class="sxs-lookup"><span data-stu-id="18e15-390">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="18e15-391">`Task.CompletedTask`Gereksinimler karşılanmazsa döndürün.</span><span class="sxs-lookup"><span data-stu-id="18e15-391">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="18e15-392">`Task.CompletedTask` başarılı veya başarısız değil, &mdash; diğer yetkilendirme işleyicilerinin çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="18e15-392">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="18e15-393">Açıkça başarısız olması gerekiyorsa, [bağlamı döndürün. Başarısız oldu](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="18e15-393">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="18e15-394">Uygulama, iletişim sahiplerinin kendi verilerini düzenlemesine/silmesine/oluşturmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="18e15-394">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="18e15-395">`ContactIsOwnerAuthorizationHandler` gereksinim parametresine geçirilen işlemi denetlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="18e15-395">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="18e15-396">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="18e15-396">Create a manager authorization handler</span></span>

<span data-ttu-id="18e15-397">`ContactManagerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="18e15-397">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="18e15-398">`ContactManagerAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="18e15-398">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="18e15-399">İçerik değişikliklerini yalnızca Yöneticiler onaylayabilir veya reddedebilir (yeni veya değiştirilmiş).</span><span class="sxs-lookup"><span data-stu-id="18e15-399">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="18e15-400">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="18e15-400">Create an administrator authorization handler</span></span>

<span data-ttu-id="18e15-401">`ContactAdministratorsAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="18e15-401">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="18e15-402">`ContactAdministratorsAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="18e15-402">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="18e15-403">Yönetici tüm işlemleri yapabilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-403">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="18e15-404">Yetkilendirme işleyicilerini kaydetme</span><span class="sxs-lookup"><span data-stu-id="18e15-404">Register the authorization handlers</span></span>

<span data-ttu-id="18e15-405">Entity Framework Core kullanan hizmetlerin, [Addkapsamlıdır](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)kullanılarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) için kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="18e15-405">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="18e15-406">, `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core oluşturulan ASP.NET Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="18e15-406">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="18e15-407">İşleyicileri hizmet koleksiyonuyla kaydedin, bu sayede `ContactsController` [bağımlılık ekleme](xref:fundamentals/dependency-injection)üzerinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-407">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="18e15-408">Aşağıdaki kodu sonuna ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="18e15-408">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="18e15-409">`ContactAdministratorsAuthorizationHandler` ve `ContactManagerAuthorizationHandler` tekton olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="18e15-409">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="18e15-410">Bu, EF kullanmayan ve gereken tüm bilgiler yöntemin parametresinde olduğundan tektonlar vardır `Context` `HandleRequirementAsync` .</span><span class="sxs-lookup"><span data-stu-id="18e15-410">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="18e15-411">Destek yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="18e15-411">Support authorization</span></span>

<span data-ttu-id="18e15-412">Bu bölümde, Razor sayfaları güncelleştirir ve bir işlem gereksinimleri sınıfı eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="18e15-412">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="18e15-413">İlgili kişi işlemleri gereksinimleri sınıfını gözden geçirin</span><span class="sxs-lookup"><span data-stu-id="18e15-413">Review the contact operations requirements class</span></span>

<span data-ttu-id="18e15-414">Sınıfını gözden geçirin `ContactOperations` .</span><span class="sxs-lookup"><span data-stu-id="18e15-414">Review the `ContactOperations` class.</span></span> <span data-ttu-id="18e15-415">Bu sınıf, uygulamanın desteklediği gereksinimleri içerir:</span><span class="sxs-lookup"><span data-stu-id="18e15-415">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="18e15-416">Kişiler sayfaları için temel sınıf oluşturma Razor</span><span class="sxs-lookup"><span data-stu-id="18e15-416">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="18e15-417">Kişiler sayfalarında kullanılan hizmetleri içeren bir temel sınıf oluşturun Razor .</span><span class="sxs-lookup"><span data-stu-id="18e15-417">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="18e15-418">Temel sınıf, başlatma kodunu bir konuma koyar:</span><span class="sxs-lookup"><span data-stu-id="18e15-418">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="18e15-419">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="18e15-419">The preceding code:</span></span>

* <span data-ttu-id="18e15-420">`IAuthorizationService`Yetkilendirme işleyicilerine erişim için hizmeti ekler.</span><span class="sxs-lookup"><span data-stu-id="18e15-420">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="18e15-421">Hizmeti ekler Identity `UserManager` .</span><span class="sxs-lookup"><span data-stu-id="18e15-421">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="18e15-422">Öğesini ekleyin `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="18e15-422">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="18e15-423">CreateModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="18e15-423">Update the CreateModel</span></span>

<span data-ttu-id="18e15-424">Sayfa modeli oluşturma oluşturucusunu temel sınıfı kullanacak şekilde güncelleştirin `DI_BasePageModel` :</span><span class="sxs-lookup"><span data-stu-id="18e15-424">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="18e15-425">Yöntemi şu `CreateModel.OnPostAsync` şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-425">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="18e15-426">Kullanıcı KIMLIĞINI `Contact` modele ekleyin.</span><span class="sxs-lookup"><span data-stu-id="18e15-426">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="18e15-427">Kullanıcının kişi oluşturma iznine sahip olduğunu doğrulamak için yetkilendirme işleyicisini çağırın.</span><span class="sxs-lookup"><span data-stu-id="18e15-427">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="18e15-428">Indexmodel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="18e15-428">Update the IndexModel</span></span>

<span data-ttu-id="18e15-429">`OnGetAsync`Yöntemi yalnızca onaylanan kişilerin genel kullanıcılara gösterilmesi için güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-429">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="18e15-430">EditModel güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="18e15-430">Update the EditModel</span></span>

<span data-ttu-id="18e15-431">Kullanıcının kişiye ait olduğunu doğrulamak için bir yetkilendirme işleyicisi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="18e15-431">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="18e15-432">Kaynak yetkilendirmesi doğrulandığında, `[Authorize]` öznitelik yeterli değildir.</span><span class="sxs-lookup"><span data-stu-id="18e15-432">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="18e15-433">Öznitelikler değerlendirildiğinde uygulamanın kaynağa erişimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="18e15-433">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="18e15-434">Kaynak tabanlı yetkilendirme sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="18e15-434">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="18e15-435">Uygulamanın kaynağa erişimi olduğunda, sayfa modeline yükleyerek veya işleyicinin kendisi içine yükleyerek denetimlerin gerçekleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="18e15-435">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="18e15-436">Kaynak anahtarını geçirerek kaynağa sıkça erişirsiniz.</span><span class="sxs-lookup"><span data-stu-id="18e15-436">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="18e15-437">DeleteModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="18e15-437">Update the DeleteModel</span></span>

<span data-ttu-id="18e15-438">Kullanıcının kişide silme iznine sahip olduğunu doğrulamak için, silme sayfası modelini yetkilendirme işleyicisini kullanacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="18e15-438">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="18e15-439">Yetkilendirme hizmetini görünümlere ekleme</span><span class="sxs-lookup"><span data-stu-id="18e15-439">Inject the authorization service into the views</span></span>

<span data-ttu-id="18e15-440">Şu anda kullanıcı ARABIRIMI, kullanıcının değiştiremeyeceğiniz kişiler için Düzenle ve Sil bağlantılarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="18e15-440">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="18e15-441">Yetkilendirme hizmetini *Görünümler/_ViewImports. cshtml* dosyasında, tüm görünümlerde kullanılabilir olacak şekilde ekleme:</span><span class="sxs-lookup"><span data-stu-id="18e15-441">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="18e15-442">Yukarıdaki biçimlendirme birkaç deyim ekliyor `using` .</span><span class="sxs-lookup"><span data-stu-id="18e15-442">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="18e15-443">*Sayfalar/kişiler/Index. cshtml* 'deki **Düzenle** ve **Sil** bağlantılarını yalnızca uygun izinlere sahip kullanıcılar için işlendikleri şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-443">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="18e15-444">Verileri değiştirme izni olmayan kullanıcıların bağlantılarının gizlenmesi, uygulamanın güvenliğini sağlar.</span><span class="sxs-lookup"><span data-stu-id="18e15-444">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="18e15-445">Bağlantıların gizlenmesi, yalnızca geçerli bağlantıları görüntüleyerek uygulamayı daha kolay hale getirir.</span><span class="sxs-lookup"><span data-stu-id="18e15-445">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="18e15-446">Kullanıcılar, sahip olmadıkları veriler üzerinde düzenleme ve silme işlemlerini çağırmak için oluşturulan URL 'Leri hacme edebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-446">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="18e15-447">RazorSayfanın veya denetleyicinin, verilerin güvenliğini sağlamak için erişim denetimlerini zorlaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="18e15-447">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="18e15-448">Güncelleştirme ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="18e15-448">Update Details</span></span>

<span data-ttu-id="18e15-449">Yöneticilerin kişileri onaylamasını veya reddedebilmesi için Ayrıntılar görünümünü güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-449">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="18e15-450">Ayrıntılar sayfası modelini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-450">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="18e15-451">Bir role Kullanıcı ekleme veya rolü kaldırma</span><span class="sxs-lookup"><span data-stu-id="18e15-451">Add or remove a user to a role</span></span>

<span data-ttu-id="18e15-452">Hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8502) bakın:</span><span class="sxs-lookup"><span data-stu-id="18e15-452">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="18e15-453">Bir kullanıcıdan ayrıcalıklar kaldırılıyor.</span><span class="sxs-lookup"><span data-stu-id="18e15-453">Removing privileges from a user.</span></span> <span data-ttu-id="18e15-454">Örneğin, bir sohbet uygulamasındaki kullanıcıyı kapatma.</span><span class="sxs-lookup"><span data-stu-id="18e15-454">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="18e15-455">Bir kullanıcıya ayrıcalık ekleme.</span><span class="sxs-lookup"><span data-stu-id="18e15-455">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="18e15-456">Tamamlanmış uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="18e15-456">Test the completed app</span></span>

<span data-ttu-id="18e15-457">Önceden oluşturulmuş kullanıcı hesapları için bir parola ayarlamadıysanız, parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets#secret-manager) kullanın:</span><span class="sxs-lookup"><span data-stu-id="18e15-457">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="18e15-458">Güçlü bir parola seçin: sekiz veya daha fazla karakter, en az bir büyük harf karakter, sayı ve simge kullanın.</span><span class="sxs-lookup"><span data-stu-id="18e15-458">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="18e15-459">Örneğin, `Passw0rd!` güçlü parola gereksinimlerini karşılar.</span><span class="sxs-lookup"><span data-stu-id="18e15-459">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="18e15-460">Aşağıdaki komutu projenin klasöründen çalıştırın, burada `<PW>` parola olur:</span><span class="sxs-lookup"><span data-stu-id="18e15-460">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="18e15-461">Veritabanını bırakma ve güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="18e15-461">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="18e15-462">Veritabanını temel alarak uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="18e15-462">Restart the app to seed the database.</span></span>

<span data-ttu-id="18e15-463">Tamamlanmış uygulamayı test etmenin kolay bir yolu da üç farklı tarayıcı (veya bilito/InPrivate oturumlar) kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="18e15-463">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="18e15-464">Tek bir tarayıcıda yeni bir Kullanıcı kaydedin (örneğin, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="18e15-464">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="18e15-465">Her bir tarayıcıda farklı bir kullanıcıyla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="18e15-465">Sign in to each browser with a different user.</span></span> <span data-ttu-id="18e15-466">Aşağıdaki işlemleri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="18e15-466">Verify the following operations:</span></span>

* <span data-ttu-id="18e15-467">Kayıtlı kullanıcılar tüm onaylanan iletişim verilerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-467">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="18e15-468">Kayıtlı kullanıcılar kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-468">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="18e15-469">Yöneticiler, kişi verilerini onaylayabilir/reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-469">Managers can approve/reject contact data.</span></span> <span data-ttu-id="18e15-470">`Details`Görünüm **Onayla** ve **Reddet** düğmelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="18e15-470">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="18e15-471">Yöneticiler tüm verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="18e15-471">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="18e15-472">Kullanıcı</span><span class="sxs-lookup"><span data-stu-id="18e15-472">User</span></span>                | <span data-ttu-id="18e15-473">Uygulama tarafından sağlanan</span><span class="sxs-lookup"><span data-stu-id="18e15-473">Seeded by the app</span></span> | <span data-ttu-id="18e15-474">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="18e15-474">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="18e15-475">Hayır</span><span class="sxs-lookup"><span data-stu-id="18e15-475">No</span></span>                | <span data-ttu-id="18e15-476">Kendi verilerini düzenleyin/silin.</span><span class="sxs-lookup"><span data-stu-id="18e15-476">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="18e15-477">Yes</span><span class="sxs-lookup"><span data-stu-id="18e15-477">Yes</span></span>               | <span data-ttu-id="18e15-478">Kendi verilerini onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="18e15-478">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="18e15-479">Yes</span><span class="sxs-lookup"><span data-stu-id="18e15-479">Yes</span></span>               | <span data-ttu-id="18e15-480">Tüm verileri onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="18e15-480">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="18e15-481">Yöneticinin tarayıcısında bir kişi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="18e15-481">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="18e15-482">Yönetici iletişim kutusundan silme ve düzenleme için URL 'YI kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="18e15-482">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="18e15-483">Bu bağlantıları test kullanıcısının bu işlemleri gerçekleştiremediğinizi doğrulamak için test kullanıcısının tarayıcısına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="18e15-483">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="18e15-484">Başlangıç uygulamasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="18e15-484">Create the starter app</span></span>

* <span data-ttu-id="18e15-485">Razor"ContactManager" adlı bir sayfalar uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="18e15-485">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="18e15-486">**Ayrı kullanıcı hesaplarıyla** uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="18e15-486">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="18e15-487">Ad alanı örnekte kullanılan ad alanıyla eşleşecek şekilde "ContactManager" olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="18e15-487">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="18e15-488">`-uld` SQLite yerine LocalDB belirtir</span><span class="sxs-lookup"><span data-stu-id="18e15-488">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="18e15-489">*Modeller/Ilgili kişi ekle. cs*:</span><span class="sxs-lookup"><span data-stu-id="18e15-489">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="18e15-490">Modeli dolandırıcıdan katlayın `Contact` .</span><span class="sxs-lookup"><span data-stu-id="18e15-490">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="18e15-491">İlk geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-491">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="18e15-492">*Pages/_Layout. cshtml* dosyasındaki **ContactManager** bağlayıcısını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="18e15-492">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="18e15-493">Kişiyi oluşturarak, düzenleyerek ve silerek uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="18e15-493">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="18e15-494">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="18e15-494">Seed the database</span></span>

<span data-ttu-id="18e15-495">*Veri* klasörüne [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) sınıfını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="18e15-495">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="18e15-496">`SeedData.Initialize`Şuradan arayın `Main` :</span><span class="sxs-lookup"><span data-stu-id="18e15-496">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="18e15-497">Uygulamanın veritabanını sunan test edin.</span><span class="sxs-lookup"><span data-stu-id="18e15-497">Test that the app seeded the database.</span></span> <span data-ttu-id="18e15-498">Kişi VERITABANıNDA herhangi bir satır varsa, çekirdek Yöntem çalıştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="18e15-498">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="18e15-499">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="18e15-499">Additional resources</span></span>

* [<span data-ttu-id="18e15-500">Azure App Service’te .NET Core ve SQL Veritabanı web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="18e15-500">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="18e15-501">[ASP.NET Core yetkilendirme Laboratuvarı](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="18e15-501">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="18e15-502">Bu laboratuvar, bu öğreticide sunulan güvenlik özellikleri hakkında daha fazla ayrıntıya gider.</span><span class="sxs-lookup"><span data-stu-id="18e15-502">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="18e15-503">Özel ilke tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="18e15-503">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
