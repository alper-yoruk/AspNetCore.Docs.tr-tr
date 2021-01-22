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
ms.openlocfilehash: ebd3c0dc9baa63b30f142773d7a3d621ce4082d9
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689311"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="cf98c-104">Yetkilendirmeyle korunan kullanıcı verileriyle ASP.NET Core bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="cf98c-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="cf98c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [ali Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="cf98c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="cf98c-106">[Bu PDF 'ye](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) bakın</span><span class="sxs-lookup"><span data-stu-id="cf98c-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cf98c-107">Bu öğreticide, yetkilendirme tarafından korunan kullanıcı verileriyle ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="cf98c-108">Kimliği doğrulanmış (kayıtlı) kullanıcıların oluşturduğu kişilerin listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="cf98c-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="cf98c-109">Üç güvenlik grubu vardır:</span><span class="sxs-lookup"><span data-stu-id="cf98c-109">There are three security groups:</span></span>

* <span data-ttu-id="cf98c-110">**Kayıtlı kullanıcılar** tüm onaylanan verileri görüntüleyebilir ve kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="cf98c-111">**Yöneticiler** , kişi verilerini onaylayabilir veya reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="cf98c-112">Yalnızca onaylanan kişiler kullanıcılara görünür.</span><span class="sxs-lookup"><span data-stu-id="cf98c-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="cf98c-113">**Yöneticiler** , verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="cf98c-114">Bu belgedeki görüntüler en son şablonlarla tam olarak eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="cf98c-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="cf98c-115">Aşağıdaki görüntüde, User Rick ( `rick@example.com` ) oturum açtı.</span><span class="sxs-lookup"><span data-stu-id="cf98c-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="cf98c-116">Rick, onaylanan kişileri görüntüleyebilir ve **Düzenle** / **Delete**' e / ait kişiler için **yeni bağlantılar oluştur** ' a bakın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="cf98c-117">Yalnızca Rick tarafından oluşturulan son kayıt, **Düzenle** ve **Sil** bağlantılarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="cf98c-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="cf98c-118">Yönetici veya yönetici durumu "Onaylandı" olarak değiştirene kadar diğer kullanıcılar son kaydı görmez.</span><span class="sxs-lookup"><span data-stu-id="cf98c-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick oturum açmış olduğunu gösteren ekran görüntüsü](secure-data/_static/rick.png)

<span data-ttu-id="cf98c-120">Aşağıdaki görüntüde, `manager@contoso.com` ve yöneticisinin rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="cf98c-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="cf98c-122">Aşağıdaki görüntüde, bir kişinin Yöneticiler Ayrıntılar görünümü gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="cf98c-122">The following image shows the managers details view of a contact:</span></span>

![Yöneticinin bir kişinin görünümü](secure-data/_static/manager.png)

<span data-ttu-id="cf98c-124">**Onaylama** ve **reddetme** düğmeleri yalnızca Yöneticiler ve yöneticiler için görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="cf98c-125">Aşağıdaki görüntüde, `admin@contoso.com` ve yönetici rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="cf98c-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="cf98c-127">Yöneticinin tüm ayrıcalıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="cf98c-127">The administrator has all privileges.</span></span> <span data-ttu-id="cf98c-128">Herhangi bir kişiyi okuyabilir/düzenleyebilir/silebilir ve kişilerin durumunu değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="cf98c-129">Uygulama, aşağıdaki model için [Yapı iskelesi](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tarafından oluşturulmuştur `Contact` :</span><span class="sxs-lookup"><span data-stu-id="cf98c-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="cf98c-130">Örnek, aşağıdaki yetkilendirme işleyicilerini içerir:</span><span class="sxs-lookup"><span data-stu-id="cf98c-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="cf98c-131">`ContactIsOwnerAuthorizationHandler`: Bir kullanıcının yalnızca verilerini düzenleyebilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf98c-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="cf98c-132">`ContactManagerAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf98c-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="cf98c-133">`ContactAdministratorsAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini ve kişileri düzenlemesini/silmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf98c-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cf98c-134">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="cf98c-134">Prerequisites</span></span>

<span data-ttu-id="cf98c-135">Bu öğretici gelişmiş bir deyişle.</span><span class="sxs-lookup"><span data-stu-id="cf98c-135">This tutorial is advanced.</span></span> <span data-ttu-id="cf98c-136">Şunu tanımanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="cf98c-136">You should be familiar with:</span></span>

* [<span data-ttu-id="cf98c-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf98c-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="cf98c-138">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="cf98c-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="cf98c-139">Hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="cf98c-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="cf98c-140">Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="cf98c-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="cf98c-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="cf98c-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="cf98c-142">Başlatıcı ve tamamlanmış uygulama</span><span class="sxs-lookup"><span data-stu-id="cf98c-142">The starter and completed app</span></span>

<span data-ttu-id="cf98c-143">[Tamamlanmış](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) uygulamayı [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="cf98c-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="cf98c-144">Tamamlanmış uygulamayı [Test](#test-the-completed-app) edin, böylece güvenlik özellikleri hakkında bilgi sahibi olun.</span><span class="sxs-lookup"><span data-stu-id="cf98c-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="cf98c-145">Başlangıç uygulaması</span><span class="sxs-lookup"><span data-stu-id="cf98c-145">The starter app</span></span>

<span data-ttu-id="cf98c-146">[Başlangıç](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) uygulamasını [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="cf98c-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="cf98c-147">Uygulamayı çalıştırın, **ContactManager** bağlantısına dokunun ve bir kişi oluşturup silemdiğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="cf98c-148">Başlangıç uygulamasını oluşturmak için bkz. [Başlangıç uygulamasını oluşturma](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="cf98c-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="cf98c-149">Güvenli Kullanıcı verileri</span><span class="sxs-lookup"><span data-stu-id="cf98c-149">Secure user data</span></span>

<span data-ttu-id="cf98c-150">Aşağıdaki bölümlerde, güvenli Kullanıcı verileri uygulaması oluşturmak için tüm önemli adımlar verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="cf98c-151">Tamamlanmış projeye başvurmak faydalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="cf98c-152">Kişi verilerini kullanıcıya bağlama</span><span class="sxs-lookup"><span data-stu-id="cf98c-152">Tie the contact data to the user</span></span>

<span data-ttu-id="cf98c-153">[Identity](xref:security/authentication/identity)Kullanıcıların verilerini düzenleyebilmeleri, ancak diğer kullanıcıların verilerini düzenleyebilmeleri için ASP.NET Kullanıcı kimliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="cf98c-154">`OwnerID`Model ekleyin `ContactStatus` `Contact` :</span><span class="sxs-lookup"><span data-stu-id="cf98c-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="cf98c-155">`OwnerID` kullanıcının `AspNetUser` veritabanındaki TABLODAKI kimliği [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="cf98c-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="cf98c-156">Bu `Status` alan, bir kişinin genel kullanıcılar tarafından görüntülenebilir olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="cf98c-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="cf98c-157">Yeni bir geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="cf98c-158">Rol hizmetlerini Ekle Identity</span><span class="sxs-lookup"><span data-stu-id="cf98c-158">Add Role services to Identity</span></span>

<span data-ttu-id="cf98c-159">Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="cf98c-160">Kimliği doğrulanmış kullanıcılar iste</span><span class="sxs-lookup"><span data-stu-id="cf98c-160">Require authenticated users</span></span>

<span data-ttu-id="cf98c-161">Kullanıcıların kimliklerinin doğrulanmasını gerektirmek için geri dönüş kimlik doğrulama ilkesini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="cf98c-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="cf98c-162">Önceki vurgulanmış kod, [geri dönüş kimlik doğrulama ilkesini](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="cf98c-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="cf98c-163">Geri dönüş kimlik doğrulama ilkesi,\*\* Razor bir kimlik doğrulama özniteliğine sahip sayfalar, denetleyiciler veya eylem yöntemleri dışında \* tüm _ kullanıcıların kimliğinin doğrulanmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="cf98c-164">Örneğin, Razor Sayfalar, denetleyiciler veya eylem yöntemleri, `[AllowAnonymous]` `[Authorize(PolicyName="MyPolicy")]` geri dönüş kimlik doğrulama ilkesi yerine uygulanan kimlik doğrulama özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="cf98c-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="cf98c-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> geçerli <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> kullanıcının kimliğinin doğrulanmasını zorlayan geçerli örneğe ekler.</span><span class="sxs-lookup"><span data-stu-id="cf98c-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

<span data-ttu-id="cf98c-166">Geri dönüş kimlik doğrulama ilkesi:</span><span class="sxs-lookup"><span data-stu-id="cf98c-166">The fallback authentication policy:</span></span>

<span data-ttu-id="cf98c-167">_ Açıkça bir kimlik doğrulama ilkesi belirtmeyen tüm isteklere uygulanır.</span><span class="sxs-lookup"><span data-stu-id="cf98c-167">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="cf98c-168">Endpoint Routing tarafından sunulan istekler için, bu, yetkilendirme özniteliği belirtmeyen tüm uç noktaları içerir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-168">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="cf98c-169">[Statik dosyalar](xref:fundamentals/static-files)gibi yetkilendirme ara yazılımı sonrasında diğer ara yazılım tarafından hizmet verilen istekler için, bu ilkeyi tüm isteklere uygular.</span><span class="sxs-lookup"><span data-stu-id="cf98c-169">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="cf98c-170">Geri dönüş kimlik doğrulama ilkesini kullanıcıların kimliğinin doğrulanmasını gerektirecek şekilde ayarlamak, yeni eklenen Razor sayfaları ve denetleyicileri korur.</span><span class="sxs-lookup"><span data-stu-id="cf98c-170">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="cf98c-171">Varsayılan olarak kimlik doğrulamanın gerekli olması, yeni denetleyicilere ve Razor sayfalarına bağlı olarak, özniteliğini dahil etmek için daha güvenlidir `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-171">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="cf98c-172"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Sınıfı da içerir <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="cf98c-172">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="cf98c-173">, `DefaultPolicy` `[Authorize]` İlke belirtilmediğinde özniteliğiyle kullanılan ilkedir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-173">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="cf98c-174">`[Authorize]` , aksine adlandırılmış bir ilke içermez `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-174">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="cf98c-175">İlkeler hakkında daha fazla bilgi için bkz <xref:security/authorization/policies> ..</span><span class="sxs-lookup"><span data-stu-id="cf98c-175">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="cf98c-176">MVC denetleyicilerinin ve Razor sayfaların tüm kullanıcıların kimliğinin doğrulanmasını gerektirmek için alternatif bir yol yetkilendirme filtresi ekliyor:</span><span class="sxs-lookup"><span data-stu-id="cf98c-176">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="cf98c-177">Yukarıdaki kod, bir yetkilendirme filtresi kullanır ve geri dönüş ilkesini ayarlama Endpoint Routing kullanır.</span><span class="sxs-lookup"><span data-stu-id="cf98c-177">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="cf98c-178">Geri dönüş ilkesini ayarlamak, tüm kullanıcıların kimliğinin doğrulanmasını gerektirmek için tercih edilen yoldur.</span><span class="sxs-lookup"><span data-stu-id="cf98c-178">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="cf98c-179">[](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) `Index` `Privacy` Anonim kullanıcıların, kaydolmadan önce site hakkında bilgi alması Için ve sayfalarına AllowAnonymous ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-179">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="cf98c-180">Test hesabını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="cf98c-180">Configure the test account</span></span>

<span data-ttu-id="cf98c-181">`SeedData`Sınıfı iki hesap oluşturur: yönetici ve yönetici.</span><span class="sxs-lookup"><span data-stu-id="cf98c-181">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="cf98c-182">Bu hesaplara bir parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets) kullanın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-182">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="cf98c-183">Parolayı proje dizininden ayarlayın ( *program.cs* içeren dizin):</span><span class="sxs-lookup"><span data-stu-id="cf98c-183">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="cf98c-184">Güçlü bir parola belirtilmemişse, çağrıldığında bir özel durum oluşturulur `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-184">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="cf98c-185">`Main`Sınama parolasını kullanacak şekilde güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="cf98c-185">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="cf98c-186">Test hesapları oluşturma ve kişileri güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="cf98c-186">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="cf98c-187">`Initialize` `SeedData` Test hesapları oluşturmak için sınıfındaki yöntemi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-187">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="cf98c-188">Yönetici kullanıcı KIMLIĞINI ve kişilerini ekleyin `ContactStatus` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-188">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="cf98c-189">Kişilerden birini "gönderildi" ve bir "reddedildi" yapın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-189">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="cf98c-190">Kullanıcı KIMLIĞINI ve durumunu tüm kişilere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-190">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="cf98c-191">Yalnızca bir kişi gösterilir:</span><span class="sxs-lookup"><span data-stu-id="cf98c-191">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="cf98c-192">Sahip, yönetici ve yönetici yetkilendirme işleyicileri oluşturma</span><span class="sxs-lookup"><span data-stu-id="cf98c-192">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="cf98c-193">`ContactIsOwnerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cf98c-193">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cf98c-194">`ContactIsOwnerAuthorizationHandler`Kullanıcının kaynağın sahibi olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="cf98c-194">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="cf98c-195">`ContactIsOwnerAuthorizationHandler`Çağıran [bağlamı. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)Kimliği doğrulanmış geçerli kullanıcı kişi sahibsiyse başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="cf98c-195">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="cf98c-196">Yetkilendirme işleyicileri genellikle:</span><span class="sxs-lookup"><span data-stu-id="cf98c-196">Authorization handlers generally:</span></span>

* <span data-ttu-id="cf98c-197">`context.Succeed`Gereksinimler karşılandığında döndürün.</span><span class="sxs-lookup"><span data-stu-id="cf98c-197">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="cf98c-198">`Task.CompletedTask`Gereksinimler karşılanmazsa döndürün.</span><span class="sxs-lookup"><span data-stu-id="cf98c-198">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="cf98c-199">`Task.CompletedTask` başarılı veya başarısız değil, &mdash; diğer yetkilendirme işleyicilerinin çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-199">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="cf98c-200">Açıkça başarısız olması gerekiyorsa, [bağlamı döndürün. Başarısız oldu](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="cf98c-200">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="cf98c-201">Uygulama, iletişim sahiplerinin kendi verilerini düzenlemesine/silmesine/oluşturmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-201">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="cf98c-202">`ContactIsOwnerAuthorizationHandler` gereksinim parametresine geçirilen işlemi denetlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="cf98c-202">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="cf98c-203">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="cf98c-203">Create a manager authorization handler</span></span>

<span data-ttu-id="cf98c-204">`ContactManagerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cf98c-204">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cf98c-205">`ContactManagerAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="cf98c-205">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="cf98c-206">İçerik değişikliklerini yalnızca Yöneticiler onaylayabilir veya reddedebilir (yeni veya değiştirilmiş).</span><span class="sxs-lookup"><span data-stu-id="cf98c-206">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="cf98c-207">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="cf98c-207">Create an administrator authorization handler</span></span>

<span data-ttu-id="cf98c-208">`ContactAdministratorsAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cf98c-208">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cf98c-209">`ContactAdministratorsAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="cf98c-209">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="cf98c-210">Yönetici tüm işlemleri yapabilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-210">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="cf98c-211">Yetkilendirme işleyicilerini kaydetme</span><span class="sxs-lookup"><span data-stu-id="cf98c-211">Register the authorization handlers</span></span>

<span data-ttu-id="cf98c-212">Entity Framework Core kullanan hizmetlerin, [Addkapsamlıdır](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)kullanılarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) için kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-212">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="cf98c-213">, `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core oluşturulan ASP.NET Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="cf98c-213">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="cf98c-214">İşleyicileri hizmet koleksiyonuyla kaydedin, bu sayede `ContactsController` [bağımlılık ekleme](xref:fundamentals/dependency-injection)üzerinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-214">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cf98c-215">Aşağıdaki kodu sonuna ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cf98c-215">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="cf98c-216">`ContactAdministratorsAuthorizationHandler` ve `ContactManagerAuthorizationHandler` tekton olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-216">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="cf98c-217">Bu, EF kullanmayan ve gereken tüm bilgiler yöntemin parametresinde olduğundan tektonlar vardır `Context` `HandleRequirementAsync` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-217">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="cf98c-218">Destek yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="cf98c-218">Support authorization</span></span>

<span data-ttu-id="cf98c-219">Bu bölümde, Razor sayfaları güncelleştirir ve bir işlem gereksinimleri sınıfı eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="cf98c-219">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="cf98c-220">İlgili kişi işlemleri gereksinimleri sınıfını gözden geçirin</span><span class="sxs-lookup"><span data-stu-id="cf98c-220">Review the contact operations requirements class</span></span>

<span data-ttu-id="cf98c-221">Sınıfını gözden geçirin `ContactOperations` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-221">Review the `ContactOperations` class.</span></span> <span data-ttu-id="cf98c-222">Bu sınıf, uygulamanın desteklediği gereksinimleri içerir:</span><span class="sxs-lookup"><span data-stu-id="cf98c-222">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="cf98c-223">Kişiler sayfaları için temel sınıf oluşturma Razor</span><span class="sxs-lookup"><span data-stu-id="cf98c-223">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="cf98c-224">Kişiler sayfalarında kullanılan hizmetleri içeren bir temel sınıf oluşturun Razor .</span><span class="sxs-lookup"><span data-stu-id="cf98c-224">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="cf98c-225">Temel sınıf, başlatma kodunu bir konuma koyar:</span><span class="sxs-lookup"><span data-stu-id="cf98c-225">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="cf98c-226">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="cf98c-226">The preceding code:</span></span>

* <span data-ttu-id="cf98c-227">`IAuthorizationService`Yetkilendirme işleyicilerine erişim için hizmeti ekler.</span><span class="sxs-lookup"><span data-stu-id="cf98c-227">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="cf98c-228">Hizmeti ekler Identity `UserManager` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-228">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="cf98c-229">Öğesini ekleyin `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-229">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="cf98c-230">CreateModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="cf98c-230">Update the CreateModel</span></span>

<span data-ttu-id="cf98c-231">Sayfa modeli oluşturma oluşturucusunu temel sınıfı kullanacak şekilde güncelleştirin `DI_BasePageModel` :</span><span class="sxs-lookup"><span data-stu-id="cf98c-231">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="cf98c-232">Yöntemi şu `CreateModel.OnPostAsync` şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-232">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="cf98c-233">Kullanıcı KIMLIĞINI `Contact` modele ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-233">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="cf98c-234">Kullanıcının kişi oluşturma iznine sahip olduğunu doğrulamak için yetkilendirme işleyicisini çağırın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-234">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="cf98c-235">Indexmodel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="cf98c-235">Update the IndexModel</span></span>

<span data-ttu-id="cf98c-236">`OnGetAsync`Yöntemi yalnızca onaylanan kişilerin genel kullanıcılara gösterilmesi için güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-236">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="cf98c-237">EditModel güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="cf98c-237">Update the EditModel</span></span>

<span data-ttu-id="cf98c-238">Kullanıcının kişiye ait olduğunu doğrulamak için bir yetkilendirme işleyicisi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-238">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="cf98c-239">Kaynak yetkilendirmesi doğrulandığında, `[Authorize]` öznitelik yeterli değildir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-239">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="cf98c-240">Öznitelikler değerlendirildiğinde uygulamanın kaynağa erişimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="cf98c-240">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="cf98c-241">Kaynak tabanlı yetkilendirme sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="cf98c-241">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="cf98c-242">Uygulamanın kaynağa erişimi olduğunda, sayfa modeline yükleyerek veya işleyicinin kendisi içine yükleyerek denetimlerin gerçekleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-242">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="cf98c-243">Kaynak anahtarını geçirerek kaynağa sıkça erişirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cf98c-243">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="cf98c-244">DeleteModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="cf98c-244">Update the DeleteModel</span></span>

<span data-ttu-id="cf98c-245">Kullanıcının kişide silme iznine sahip olduğunu doğrulamak için, silme sayfası modelini yetkilendirme işleyicisini kullanacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-245">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="cf98c-246">Yetkilendirme hizmetini görünümlere ekleme</span><span class="sxs-lookup"><span data-stu-id="cf98c-246">Inject the authorization service into the views</span></span>

<span data-ttu-id="cf98c-247">Şu anda kullanıcı ARABIRIMI, kullanıcının değiştiremeyeceğiniz kişiler için Düzenle ve Sil bağlantılarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-247">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="cf98c-248">Yetkilendirme hizmetini *Sayfalar/_ViewImports. cshtml* dosyasına ekleme, bu nedenle tüm görünümlerde kullanılabilir hale gelir:</span><span class="sxs-lookup"><span data-stu-id="cf98c-248">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="cf98c-249">Yukarıdaki biçimlendirme birkaç deyim ekliyor `using` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-249">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="cf98c-250">*Sayfalar/kişiler/Index. cshtml* 'deki **Düzenle** ve **Sil** bağlantılarını yalnızca uygun izinlere sahip kullanıcılar için işlendikleri şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-250">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="cf98c-251">Verileri değiştirme izni olmayan kullanıcıların bağlantılarının gizlenmesi, uygulamanın güvenliğini sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf98c-251">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="cf98c-252">Bağlantıların gizlenmesi, yalnızca geçerli bağlantıları görüntüleyerek uygulamayı daha kolay hale getirir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-252">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="cf98c-253">Kullanıcılar, sahip olmadıkları veriler üzerinde düzenleme ve silme işlemlerini çağırmak için oluşturulan URL 'Leri hacme edebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-253">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="cf98c-254">RazorSayfanın veya denetleyicinin, verilerin güvenliğini sağlamak için erişim denetimlerini zorlaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-254">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="cf98c-255">Güncelleştirme ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="cf98c-255">Update Details</span></span>

<span data-ttu-id="cf98c-256">Yöneticilerin kişileri onaylamasını veya reddedebilmesi için Ayrıntılar görünümünü güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-256">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="cf98c-257">Ayrıntılar sayfası modelini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-257">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="cf98c-258">Bir role Kullanıcı ekleme veya rolü kaldırma</span><span class="sxs-lookup"><span data-stu-id="cf98c-258">Add or remove a user to a role</span></span>

<span data-ttu-id="cf98c-259">Hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8502) bakın:</span><span class="sxs-lookup"><span data-stu-id="cf98c-259">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="cf98c-260">Bir kullanıcıdan ayrıcalıklar kaldırılıyor.</span><span class="sxs-lookup"><span data-stu-id="cf98c-260">Removing privileges from a user.</span></span> <span data-ttu-id="cf98c-261">Örneğin, bir sohbet uygulamasındaki kullanıcıyı kapatma.</span><span class="sxs-lookup"><span data-stu-id="cf98c-261">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="cf98c-262">Bir kullanıcıya ayrıcalık ekleme.</span><span class="sxs-lookup"><span data-stu-id="cf98c-262">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="cf98c-263">Challenge ve Fordeklarasyon arasındaki farklar</span><span class="sxs-lookup"><span data-stu-id="cf98c-263">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="cf98c-264">Bu uygulama, varsayılan ilkeyi [kimliği doğrulanmış kullanıcıları gerektirecek](#rau)şekilde ayarlar.</span><span class="sxs-lookup"><span data-stu-id="cf98c-264">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="cf98c-265">Aşağıdaki kod anonim kullanıcılara izin verir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-265">The following code allows anonymous users.</span></span> <span data-ttu-id="cf98c-266">Anonim kullanıcılara, çekişme ile Fordeklarasyon arasındaki farkları gösterme izni verilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-266">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="cf98c-267">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="cf98c-267">In the preceding code:</span></span>

* <span data-ttu-id="cf98c-268">Kullanıcının kimliği **doğrulanmadığı** zaman, bir `ChallengeResult` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="cf98c-268">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="cf98c-269">Bir `ChallengeResult` döndürüldüğünde, Kullanıcı oturum açma sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-269">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="cf98c-270">Kullanıcının kimliği doğrulandığında, ancak yetkilendirilmediğinde, bir `ForbidResult` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="cf98c-270">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="cf98c-271">Bir `ForbidResult` döndürüldüğünde, Kullanıcı erişim reddedildi sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-271">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="cf98c-272">Tamamlanmış uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="cf98c-272">Test the completed app</span></span>

<span data-ttu-id="cf98c-273">Önceden oluşturulmuş kullanıcı hesapları için bir parola ayarlamadıysanız, parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets#secret-manager) kullanın:</span><span class="sxs-lookup"><span data-stu-id="cf98c-273">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="cf98c-274">Güçlü bir parola seçin: sekiz veya daha fazla karakter, en az bir büyük harf karakter, sayı ve simge kullanın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-274">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="cf98c-275">Örneğin, `Passw0rd!` güçlü parola gereksinimlerini karşılar.</span><span class="sxs-lookup"><span data-stu-id="cf98c-275">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="cf98c-276">Aşağıdaki komutu projenin klasöründen çalıştırın, burada `<PW>` parola olur:</span><span class="sxs-lookup"><span data-stu-id="cf98c-276">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="cf98c-277">Uygulamanın kişileri varsa:</span><span class="sxs-lookup"><span data-stu-id="cf98c-277">If the app has contacts:</span></span>

* <span data-ttu-id="cf98c-278">Tablodaki tüm kayıtları silin `Contact` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-278">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="cf98c-279">Veritabanını temel alarak uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-279">Restart the app to seed the database.</span></span>

<span data-ttu-id="cf98c-280">Tamamlanmış uygulamayı test etmenin kolay bir yolu da üç farklı tarayıcı (veya bilito/InPrivate oturumlar) kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="cf98c-280">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="cf98c-281">Tek bir tarayıcıda yeni bir Kullanıcı kaydedin (örneğin, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="cf98c-281">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="cf98c-282">Her bir tarayıcıda farklı bir kullanıcıyla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-282">Sign in to each browser with a different user.</span></span> <span data-ttu-id="cf98c-283">Aşağıdaki işlemleri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="cf98c-283">Verify the following operations:</span></span>

* <span data-ttu-id="cf98c-284">Kayıtlı kullanıcılar tüm onaylanan iletişim verilerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-284">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="cf98c-285">Kayıtlı kullanıcılar kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-285">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="cf98c-286">Yöneticiler, kişi verilerini onaylayabilir/reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-286">Managers can approve/reject contact data.</span></span> <span data-ttu-id="cf98c-287">`Details`Görünüm **Onayla** ve **Reddet** düğmelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-287">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="cf98c-288">Yöneticiler tüm verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-288">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="cf98c-289">Kullanıcı</span><span class="sxs-lookup"><span data-stu-id="cf98c-289">User</span></span>                | <span data-ttu-id="cf98c-290">Uygulama tarafından sağlanan</span><span class="sxs-lookup"><span data-stu-id="cf98c-290">Seeded by the app</span></span> | <span data-ttu-id="cf98c-291">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="cf98c-291">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="cf98c-292">No</span><span class="sxs-lookup"><span data-stu-id="cf98c-292">No</span></span>                | <span data-ttu-id="cf98c-293">Kendi verilerini düzenleyin/silin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-293">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="cf98c-294">Yes</span><span class="sxs-lookup"><span data-stu-id="cf98c-294">Yes</span></span>               | <span data-ttu-id="cf98c-295">Kendi verilerini onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="cf98c-295">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="cf98c-296">Yes</span><span class="sxs-lookup"><span data-stu-id="cf98c-296">Yes</span></span>               | <span data-ttu-id="cf98c-297">Tüm verileri onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="cf98c-297">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="cf98c-298">Yöneticinin tarayıcısında bir kişi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cf98c-298">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="cf98c-299">Yönetici iletişim kutusundan silme ve düzenleme için URL 'YI kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-299">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="cf98c-300">Bu bağlantıları test kullanıcısının bu işlemleri gerçekleştiremediğinizi doğrulamak için test kullanıcısının tarayıcısına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-300">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="cf98c-301">Başlangıç uygulamasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="cf98c-301">Create the starter app</span></span>

* <span data-ttu-id="cf98c-302">Razor"ContactManager" adlı bir sayfalar uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="cf98c-302">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="cf98c-303">**Ayrı kullanıcı hesaplarıyla** uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cf98c-303">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="cf98c-304">Ad alanı örnekte kullanılan ad alanıyla eşleşecek şekilde "ContactManager" olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-304">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="cf98c-305">`-uld` SQLite yerine LocalDB belirtir</span><span class="sxs-lookup"><span data-stu-id="cf98c-305">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="cf98c-306">*Modeller/Ilgili kişi ekle. cs*:</span><span class="sxs-lookup"><span data-stu-id="cf98c-306">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="cf98c-307">Modeli dolandırıcıdan katlayın `Contact` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-307">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="cf98c-308">İlk geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-308">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="cf98c-309">Komutuyla bir hata yaşarsanız `dotnet aspnet-codegenerator razorpage` , [Bu GitHub sorununa](https://github.com/aspnet/Scaffolding/issues/984)bakın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-309">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="cf98c-310">*Pages/Shared/_Layout. cshtml* dosyasındaki **ContactManager** bağlayıcısını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-310">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="cf98c-311">Kişiyi oluşturarak, düzenleyerek ve silerek uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="cf98c-311">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="cf98c-312">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="cf98c-312">Seed the database</span></span>

<span data-ttu-id="cf98c-313">*Veri* klasörüne [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) sınıfını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-313">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="cf98c-314">`SeedData.Initialize`Şuradan arayın `Main` :</span><span class="sxs-lookup"><span data-stu-id="cf98c-314">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="cf98c-315">Uygulamanın veritabanını sunan test edin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-315">Test that the app seeded the database.</span></span> <span data-ttu-id="cf98c-316">Kişi VERITABANıNDA herhangi bir satır varsa, çekirdek Yöntem çalıştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="cf98c-316">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="cf98c-317">Bu öğreticide, yetkilendirme tarafından korunan kullanıcı verileriyle ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-317">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="cf98c-318">Kimliği doğrulanmış (kayıtlı) kullanıcıların oluşturduğu kişilerin listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="cf98c-318">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="cf98c-319">Üç güvenlik grubu vardır:</span><span class="sxs-lookup"><span data-stu-id="cf98c-319">There are three security groups:</span></span>

* <span data-ttu-id="cf98c-320">**Kayıtlı kullanıcılar** tüm onaylanan verileri görüntüleyebilir ve kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-320">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="cf98c-321">**Yöneticiler** , kişi verilerini onaylayabilir veya reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-321">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="cf98c-322">Yalnızca onaylanan kişiler kullanıcılara görünür.</span><span class="sxs-lookup"><span data-stu-id="cf98c-322">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="cf98c-323">**Yöneticiler** , verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-323">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="cf98c-324">Aşağıdaki görüntüde, User Rick ( `rick@example.com` ) oturum açtı.</span><span class="sxs-lookup"><span data-stu-id="cf98c-324">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="cf98c-325">Rick, onaylanan kişileri görüntüleyebilir ve **Düzenle** / **Delete**' e / ait kişiler için **yeni bağlantılar oluştur** ' a bakın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-325">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="cf98c-326">Yalnızca Rick tarafından oluşturulan son kayıt, **Düzenle** ve **Sil** bağlantılarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="cf98c-326">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="cf98c-327">Yönetici veya yönetici durumu "Onaylandı" olarak değiştirene kadar diğer kullanıcılar son kaydı görmez.</span><span class="sxs-lookup"><span data-stu-id="cf98c-327">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick oturum açmış olduğunu gösteren ekran görüntüsü](secure-data/_static/rick.png)

<span data-ttu-id="cf98c-329">Aşağıdaki görüntüde, `manager@contoso.com` ve yöneticisinin rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="cf98c-329">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="cf98c-331">Aşağıdaki görüntüde, bir kişinin Yöneticiler Ayrıntılar görünümü gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="cf98c-331">The following image shows the managers details view of a contact:</span></span>

![Yöneticinin bir kişinin görünümü](secure-data/_static/manager.png)

<span data-ttu-id="cf98c-333">**Onaylama** ve **reddetme** düğmeleri yalnızca Yöneticiler ve yöneticiler için görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-333">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="cf98c-334">Aşağıdaki görüntüde, `admin@contoso.com` ve yönetici rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="cf98c-334">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="cf98c-336">Yöneticinin tüm ayrıcalıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="cf98c-336">The administrator has all privileges.</span></span> <span data-ttu-id="cf98c-337">Herhangi bir kişiyi okuyabilir/düzenleyebilir/silebilir ve kişilerin durumunu değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-337">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="cf98c-338">Uygulama, aşağıdaki model için [Yapı iskelesi](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tarafından oluşturulmuştur `Contact` :</span><span class="sxs-lookup"><span data-stu-id="cf98c-338">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="cf98c-339">Örnek, aşağıdaki yetkilendirme işleyicilerini içerir:</span><span class="sxs-lookup"><span data-stu-id="cf98c-339">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="cf98c-340">`ContactIsOwnerAuthorizationHandler`: Bir kullanıcının yalnızca verilerini düzenleyebilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf98c-340">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="cf98c-341">`ContactManagerAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf98c-341">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="cf98c-342">`ContactAdministratorsAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini ve kişileri düzenlemesini/silmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf98c-342">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cf98c-343">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="cf98c-343">Prerequisites</span></span>

<span data-ttu-id="cf98c-344">Bu öğretici gelişmiş bir deyişle.</span><span class="sxs-lookup"><span data-stu-id="cf98c-344">This tutorial is advanced.</span></span> <span data-ttu-id="cf98c-345">Şunu tanımanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="cf98c-345">You should be familiar with:</span></span>

* [<span data-ttu-id="cf98c-346">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf98c-346">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="cf98c-347">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="cf98c-347">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="cf98c-348">Hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="cf98c-348">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="cf98c-349">Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="cf98c-349">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="cf98c-350">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="cf98c-350">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="cf98c-351">Başlatıcı ve tamamlanmış uygulama</span><span class="sxs-lookup"><span data-stu-id="cf98c-351">The starter and completed app</span></span>

<span data-ttu-id="cf98c-352">[Tamamlanmış](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) uygulamayı [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="cf98c-352">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="cf98c-353">Tamamlanmış uygulamayı [Test](#test-the-completed-app) edin, böylece güvenlik özellikleri hakkında bilgi sahibi olun.</span><span class="sxs-lookup"><span data-stu-id="cf98c-353">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="cf98c-354">Başlangıç uygulaması</span><span class="sxs-lookup"><span data-stu-id="cf98c-354">The starter app</span></span>

<span data-ttu-id="cf98c-355">[Başlangıç](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) uygulamasını [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="cf98c-355">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="cf98c-356">Uygulamayı çalıştırın, **ContactManager** bağlantısına dokunun ve bir kişi oluşturup silemdiğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-356">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="cf98c-357">Güvenli Kullanıcı verileri</span><span class="sxs-lookup"><span data-stu-id="cf98c-357">Secure user data</span></span>

<span data-ttu-id="cf98c-358">Aşağıdaki bölümlerde, güvenli Kullanıcı verileri uygulaması oluşturmak için tüm önemli adımlar verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-358">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="cf98c-359">Tamamlanmış projeye başvurmak faydalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-359">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="cf98c-360">Kişi verilerini kullanıcıya bağlama</span><span class="sxs-lookup"><span data-stu-id="cf98c-360">Tie the contact data to the user</span></span>

<span data-ttu-id="cf98c-361">[Identity](xref:security/authentication/identity)Kullanıcıların verilerini düzenleyebilmeleri, ancak diğer kullanıcıların verilerini düzenleyebilmeleri için ASP.NET Kullanıcı kimliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-361">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="cf98c-362">`OwnerID`Model ekleyin `ContactStatus` `Contact` :</span><span class="sxs-lookup"><span data-stu-id="cf98c-362">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="cf98c-363">`OwnerID` kullanıcının `AspNetUser` veritabanındaki TABLODAKI kimliği [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="cf98c-363">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="cf98c-364">Bu `Status` alan, bir kişinin genel kullanıcılar tarafından görüntülenebilir olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="cf98c-364">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="cf98c-365">Yeni bir geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-365">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="cf98c-366">Rol hizmetlerini Ekle Identity</span><span class="sxs-lookup"><span data-stu-id="cf98c-366">Add Role services to Identity</span></span>

<span data-ttu-id="cf98c-367">Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-367">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="cf98c-368">Kimliği doğrulanmış kullanıcılar iste</span><span class="sxs-lookup"><span data-stu-id="cf98c-368">Require authenticated users</span></span>

<span data-ttu-id="cf98c-369">Kullanıcıların kimliklerinin doğrulanmasını gerektirmek için varsayılan kimlik doğrulama ilkesini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="cf98c-369">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="cf98c-370">Bir Razor sayfada, denetleyicide veya eylem yöntemi düzeyinde kimlik doğrulamasından vazgeçebilirsiniz `[AllowAnonymous]` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-370">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="cf98c-371">Varsayılan kimlik doğrulama ilkesini kullanıcıların kimliğinin doğrulanmasını gerektirecek şekilde ayarlamak, yeni eklenen Razor sayfaları ve denetleyicileri korur.</span><span class="sxs-lookup"><span data-stu-id="cf98c-371">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="cf98c-372">Varsayılan olarak kimlik doğrulamanın gerekli olması, yeni denetleyicilere ve Razor sayfalarına bağlı olarak, özniteliğini dahil etmek için daha güvenlidir `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-372">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="cf98c-373">Anonim kullanıcıların, kayıt yaptırmadan önce site hakkında bilgi alması için dizine, hakkında ve Iletişim sayfalarına [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-373">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="cf98c-374">Test hesabını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="cf98c-374">Configure the test account</span></span>

<span data-ttu-id="cf98c-375">`SeedData`Sınıfı iki hesap oluşturur: yönetici ve yönetici.</span><span class="sxs-lookup"><span data-stu-id="cf98c-375">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="cf98c-376">Bu hesaplara bir parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets) kullanın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-376">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="cf98c-377">Parolayı proje dizininden ayarlayın ( *program.cs* içeren dizin):</span><span class="sxs-lookup"><span data-stu-id="cf98c-377">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="cf98c-378">Güçlü bir parola belirtilmemişse, çağrıldığında bir özel durum oluşturulur `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-378">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="cf98c-379">`Main`Sınama parolasını kullanacak şekilde güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="cf98c-379">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="cf98c-380">Test hesapları oluşturma ve kişileri güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="cf98c-380">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="cf98c-381">`Initialize` `SeedData` Test hesapları oluşturmak için sınıfındaki yöntemi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-381">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="cf98c-382">Yönetici kullanıcı KIMLIĞINI ve kişilerini ekleyin `ContactStatus` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-382">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="cf98c-383">Kişilerden birini "gönderildi" ve bir "reddedildi" yapın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-383">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="cf98c-384">Kullanıcı KIMLIĞINI ve durumunu tüm kişilere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-384">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="cf98c-385">Yalnızca bir kişi gösterilir:</span><span class="sxs-lookup"><span data-stu-id="cf98c-385">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="cf98c-386">Sahip, yönetici ve yönetici yetkilendirme işleyicileri oluşturma</span><span class="sxs-lookup"><span data-stu-id="cf98c-386">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="cf98c-387">Bir *Yetkilendirme* klasörü oluşturun ve içinde bir `ContactIsOwnerAuthorizationHandler` sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cf98c-387">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="cf98c-388">`ContactIsOwnerAuthorizationHandler`Kullanıcının kaynağın sahibi olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="cf98c-388">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="cf98c-389">`ContactIsOwnerAuthorizationHandler`Çağıran [bağlamı. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)Kimliği doğrulanmış geçerli kullanıcı kişi sahibsiyse başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="cf98c-389">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="cf98c-390">Yetkilendirme işleyicileri genellikle:</span><span class="sxs-lookup"><span data-stu-id="cf98c-390">Authorization handlers generally:</span></span>

* <span data-ttu-id="cf98c-391">`context.Succeed`Gereksinimler karşılandığında döndürün.</span><span class="sxs-lookup"><span data-stu-id="cf98c-391">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="cf98c-392">`Task.CompletedTask`Gereksinimler karşılanmazsa döndürün.</span><span class="sxs-lookup"><span data-stu-id="cf98c-392">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="cf98c-393">`Task.CompletedTask` başarılı veya başarısız değil, &mdash; diğer yetkilendirme işleyicilerinin çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-393">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="cf98c-394">Açıkça başarısız olması gerekiyorsa, [bağlamı döndürün. Başarısız oldu](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="cf98c-394">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="cf98c-395">Uygulama, iletişim sahiplerinin kendi verilerini düzenlemesine/silmesine/oluşturmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-395">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="cf98c-396">`ContactIsOwnerAuthorizationHandler` gereksinim parametresine geçirilen işlemi denetlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="cf98c-396">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="cf98c-397">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="cf98c-397">Create a manager authorization handler</span></span>

<span data-ttu-id="cf98c-398">`ContactManagerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cf98c-398">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cf98c-399">`ContactManagerAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="cf98c-399">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="cf98c-400">İçerik değişikliklerini yalnızca Yöneticiler onaylayabilir veya reddedebilir (yeni veya değiştirilmiş).</span><span class="sxs-lookup"><span data-stu-id="cf98c-400">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="cf98c-401">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="cf98c-401">Create an administrator authorization handler</span></span>

<span data-ttu-id="cf98c-402">`ContactAdministratorsAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cf98c-402">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cf98c-403">`ContactAdministratorsAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="cf98c-403">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="cf98c-404">Yönetici tüm işlemleri yapabilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-404">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="cf98c-405">Yetkilendirme işleyicilerini kaydetme</span><span class="sxs-lookup"><span data-stu-id="cf98c-405">Register the authorization handlers</span></span>

<span data-ttu-id="cf98c-406">Entity Framework Core kullanan hizmetlerin, [Addkapsamlıdır](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)kullanılarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) için kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-406">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="cf98c-407">, `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core oluşturulan ASP.NET Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="cf98c-407">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="cf98c-408">İşleyicileri hizmet koleksiyonuyla kaydedin, bu sayede `ContactsController` [bağımlılık ekleme](xref:fundamentals/dependency-injection)üzerinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-408">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cf98c-409">Aşağıdaki kodu sonuna ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cf98c-409">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="cf98c-410">`ContactAdministratorsAuthorizationHandler` ve `ContactManagerAuthorizationHandler` tekton olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-410">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="cf98c-411">Bu, EF kullanmayan ve gereken tüm bilgiler yöntemin parametresinde olduğundan tektonlar vardır `Context` `HandleRequirementAsync` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-411">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="cf98c-412">Destek yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="cf98c-412">Support authorization</span></span>

<span data-ttu-id="cf98c-413">Bu bölümde, Razor sayfaları güncelleştirir ve bir işlem gereksinimleri sınıfı eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="cf98c-413">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="cf98c-414">İlgili kişi işlemleri gereksinimleri sınıfını gözden geçirin</span><span class="sxs-lookup"><span data-stu-id="cf98c-414">Review the contact operations requirements class</span></span>

<span data-ttu-id="cf98c-415">Sınıfını gözden geçirin `ContactOperations` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-415">Review the `ContactOperations` class.</span></span> <span data-ttu-id="cf98c-416">Bu sınıf, uygulamanın desteklediği gereksinimleri içerir:</span><span class="sxs-lookup"><span data-stu-id="cf98c-416">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="cf98c-417">Kişiler sayfaları için temel sınıf oluşturma Razor</span><span class="sxs-lookup"><span data-stu-id="cf98c-417">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="cf98c-418">Kişiler sayfalarında kullanılan hizmetleri içeren bir temel sınıf oluşturun Razor .</span><span class="sxs-lookup"><span data-stu-id="cf98c-418">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="cf98c-419">Temel sınıf, başlatma kodunu bir konuma koyar:</span><span class="sxs-lookup"><span data-stu-id="cf98c-419">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="cf98c-420">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="cf98c-420">The preceding code:</span></span>

* <span data-ttu-id="cf98c-421">`IAuthorizationService`Yetkilendirme işleyicilerine erişim için hizmeti ekler.</span><span class="sxs-lookup"><span data-stu-id="cf98c-421">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="cf98c-422">Hizmeti ekler Identity `UserManager` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-422">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="cf98c-423">Öğesini ekleyin `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-423">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="cf98c-424">CreateModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="cf98c-424">Update the CreateModel</span></span>

<span data-ttu-id="cf98c-425">Sayfa modeli oluşturma oluşturucusunu temel sınıfı kullanacak şekilde güncelleştirin `DI_BasePageModel` :</span><span class="sxs-lookup"><span data-stu-id="cf98c-425">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="cf98c-426">Yöntemi şu `CreateModel.OnPostAsync` şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-426">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="cf98c-427">Kullanıcı KIMLIĞINI `Contact` modele ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-427">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="cf98c-428">Kullanıcının kişi oluşturma iznine sahip olduğunu doğrulamak için yetkilendirme işleyicisini çağırın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-428">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="cf98c-429">Indexmodel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="cf98c-429">Update the IndexModel</span></span>

<span data-ttu-id="cf98c-430">`OnGetAsync`Yöntemi yalnızca onaylanan kişilerin genel kullanıcılara gösterilmesi için güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-430">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="cf98c-431">EditModel güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="cf98c-431">Update the EditModel</span></span>

<span data-ttu-id="cf98c-432">Kullanıcının kişiye ait olduğunu doğrulamak için bir yetkilendirme işleyicisi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-432">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="cf98c-433">Kaynak yetkilendirmesi doğrulandığında, `[Authorize]` öznitelik yeterli değildir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-433">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="cf98c-434">Öznitelikler değerlendirildiğinde uygulamanın kaynağa erişimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="cf98c-434">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="cf98c-435">Kaynak tabanlı yetkilendirme sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="cf98c-435">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="cf98c-436">Uygulamanın kaynağa erişimi olduğunda, sayfa modeline yükleyerek veya işleyicinin kendisi içine yükleyerek denetimlerin gerçekleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-436">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="cf98c-437">Kaynak anahtarını geçirerek kaynağa sıkça erişirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cf98c-437">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="cf98c-438">DeleteModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="cf98c-438">Update the DeleteModel</span></span>

<span data-ttu-id="cf98c-439">Kullanıcının kişide silme iznine sahip olduğunu doğrulamak için, silme sayfası modelini yetkilendirme işleyicisini kullanacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-439">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="cf98c-440">Yetkilendirme hizmetini görünümlere ekleme</span><span class="sxs-lookup"><span data-stu-id="cf98c-440">Inject the authorization service into the views</span></span>

<span data-ttu-id="cf98c-441">Şu anda kullanıcı ARABIRIMI, kullanıcının değiştiremeyeceğiniz kişiler için Düzenle ve Sil bağlantılarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-441">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="cf98c-442">Yetkilendirme hizmetini *Görünümler/_ViewImports. cshtml* dosyasında, tüm görünümlerde kullanılabilir olacak şekilde ekleme:</span><span class="sxs-lookup"><span data-stu-id="cf98c-442">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="cf98c-443">Yukarıdaki biçimlendirme birkaç deyim ekliyor `using` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-443">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="cf98c-444">*Sayfalar/kişiler/Index. cshtml* 'deki **Düzenle** ve **Sil** bağlantılarını yalnızca uygun izinlere sahip kullanıcılar için işlendikleri şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-444">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="cf98c-445">Verileri değiştirme izni olmayan kullanıcıların bağlantılarının gizlenmesi, uygulamanın güvenliğini sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf98c-445">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="cf98c-446">Bağlantıların gizlenmesi, yalnızca geçerli bağlantıları görüntüleyerek uygulamayı daha kolay hale getirir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-446">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="cf98c-447">Kullanıcılar, sahip olmadıkları veriler üzerinde düzenleme ve silme işlemlerini çağırmak için oluşturulan URL 'Leri hacme edebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-447">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="cf98c-448">RazorSayfanın veya denetleyicinin, verilerin güvenliğini sağlamak için erişim denetimlerini zorlaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-448">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="cf98c-449">Güncelleştirme ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="cf98c-449">Update Details</span></span>

<span data-ttu-id="cf98c-450">Yöneticilerin kişileri onaylamasını veya reddedebilmesi için Ayrıntılar görünümünü güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-450">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="cf98c-451">Ayrıntılar sayfası modelini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-451">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="cf98c-452">Bir role Kullanıcı ekleme veya rolü kaldırma</span><span class="sxs-lookup"><span data-stu-id="cf98c-452">Add or remove a user to a role</span></span>

<span data-ttu-id="cf98c-453">Hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8502) bakın:</span><span class="sxs-lookup"><span data-stu-id="cf98c-453">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="cf98c-454">Bir kullanıcıdan ayrıcalıklar kaldırılıyor.</span><span class="sxs-lookup"><span data-stu-id="cf98c-454">Removing privileges from a user.</span></span> <span data-ttu-id="cf98c-455">Örneğin, bir sohbet uygulamasındaki kullanıcıyı kapatma.</span><span class="sxs-lookup"><span data-stu-id="cf98c-455">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="cf98c-456">Bir kullanıcıya ayrıcalık ekleme.</span><span class="sxs-lookup"><span data-stu-id="cf98c-456">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="cf98c-457">Tamamlanmış uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="cf98c-457">Test the completed app</span></span>

<span data-ttu-id="cf98c-458">Önceden oluşturulmuş kullanıcı hesapları için bir parola ayarlamadıysanız, parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets#secret-manager) kullanın:</span><span class="sxs-lookup"><span data-stu-id="cf98c-458">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="cf98c-459">Güçlü bir parola seçin: sekiz veya daha fazla karakter, en az bir büyük harf karakter, sayı ve simge kullanın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-459">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="cf98c-460">Örneğin, `Passw0rd!` güçlü parola gereksinimlerini karşılar.</span><span class="sxs-lookup"><span data-stu-id="cf98c-460">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="cf98c-461">Aşağıdaki komutu projenin klasöründen çalıştırın, burada `<PW>` parola olur:</span><span class="sxs-lookup"><span data-stu-id="cf98c-461">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="cf98c-462">Veritabanını bırakma ve güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="cf98c-462">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="cf98c-463">Veritabanını temel alarak uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-463">Restart the app to seed the database.</span></span>

<span data-ttu-id="cf98c-464">Tamamlanmış uygulamayı test etmenin kolay bir yolu da üç farklı tarayıcı (veya bilito/InPrivate oturumlar) kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="cf98c-464">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="cf98c-465">Tek bir tarayıcıda yeni bir Kullanıcı kaydedin (örneğin, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="cf98c-465">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="cf98c-466">Her bir tarayıcıda farklı bir kullanıcıyla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-466">Sign in to each browser with a different user.</span></span> <span data-ttu-id="cf98c-467">Aşağıdaki işlemleri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="cf98c-467">Verify the following operations:</span></span>

* <span data-ttu-id="cf98c-468">Kayıtlı kullanıcılar tüm onaylanan iletişim verilerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-468">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="cf98c-469">Kayıtlı kullanıcılar kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-469">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="cf98c-470">Yöneticiler, kişi verilerini onaylayabilir/reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-470">Managers can approve/reject contact data.</span></span> <span data-ttu-id="cf98c-471">`Details`Görünüm **Onayla** ve **Reddet** düğmelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-471">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="cf98c-472">Yöneticiler tüm verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="cf98c-472">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="cf98c-473">Kullanıcı</span><span class="sxs-lookup"><span data-stu-id="cf98c-473">User</span></span>                | <span data-ttu-id="cf98c-474">Uygulama tarafından sağlanan</span><span class="sxs-lookup"><span data-stu-id="cf98c-474">Seeded by the app</span></span> | <span data-ttu-id="cf98c-475">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="cf98c-475">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="cf98c-476">No</span><span class="sxs-lookup"><span data-stu-id="cf98c-476">No</span></span>                | <span data-ttu-id="cf98c-477">Kendi verilerini düzenleyin/silin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-477">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="cf98c-478">Yes</span><span class="sxs-lookup"><span data-stu-id="cf98c-478">Yes</span></span>               | <span data-ttu-id="cf98c-479">Kendi verilerini onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="cf98c-479">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="cf98c-480">Yes</span><span class="sxs-lookup"><span data-stu-id="cf98c-480">Yes</span></span>               | <span data-ttu-id="cf98c-481">Tüm verileri onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="cf98c-481">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="cf98c-482">Yöneticinin tarayıcısında bir kişi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cf98c-482">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="cf98c-483">Yönetici iletişim kutusundan silme ve düzenleme için URL 'YI kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-483">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="cf98c-484">Bu bağlantıları test kullanıcısının bu işlemleri gerçekleştiremediğinizi doğrulamak için test kullanıcısının tarayıcısına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-484">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="cf98c-485">Başlangıç uygulamasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="cf98c-485">Create the starter app</span></span>

* <span data-ttu-id="cf98c-486">Razor"ContactManager" adlı bir sayfalar uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="cf98c-486">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="cf98c-487">**Ayrı kullanıcı hesaplarıyla** uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cf98c-487">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="cf98c-488">Ad alanı örnekte kullanılan ad alanıyla eşleşecek şekilde "ContactManager" olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="cf98c-488">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="cf98c-489">`-uld` SQLite yerine LocalDB belirtir</span><span class="sxs-lookup"><span data-stu-id="cf98c-489">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="cf98c-490">*Modeller/Ilgili kişi ekle. cs*:</span><span class="sxs-lookup"><span data-stu-id="cf98c-490">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="cf98c-491">Modeli dolandırıcıdan katlayın `Contact` .</span><span class="sxs-lookup"><span data-stu-id="cf98c-491">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="cf98c-492">İlk geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-492">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="cf98c-493">*Pages/_Layout. cshtml* dosyasındaki **ContactManager** bağlayıcısını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cf98c-493">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="cf98c-494">Kişiyi oluşturarak, düzenleyerek ve silerek uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="cf98c-494">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="cf98c-495">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="cf98c-495">Seed the database</span></span>

<span data-ttu-id="cf98c-496">*Veri* klasörüne [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) sınıfını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-496">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="cf98c-497">`SeedData.Initialize`Şuradan arayın `Main` :</span><span class="sxs-lookup"><span data-stu-id="cf98c-497">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="cf98c-498">Uygulamanın veritabanını sunan test edin.</span><span class="sxs-lookup"><span data-stu-id="cf98c-498">Test that the app seeded the database.</span></span> <span data-ttu-id="cf98c-499">Kişi VERITABANıNDA herhangi bir satır varsa, çekirdek Yöntem çalıştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="cf98c-499">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="cf98c-500">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cf98c-500">Additional resources</span></span>

* [<span data-ttu-id="cf98c-501">Azure App Service’te .NET Core ve SQL Veritabanı web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="cf98c-501">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="cf98c-502">[ASP.NET Core yetkilendirme Laboratuvarı](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="cf98c-502">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="cf98c-503">Bu laboratuvar, bu öğreticide sunulan güvenlik özellikleri hakkında daha fazla ayrıntıya gider.</span><span class="sxs-lookup"><span data-stu-id="cf98c-503">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="cf98c-504">Özel ilke tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="cf98c-504">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
