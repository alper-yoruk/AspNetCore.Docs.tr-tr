---
title: Yetkilendirme ile korunan kullanıcı verileriyle ASP.NET Core uygulama oluşturma
author: rick-anderson
description: RazorYetkilendirme ile korunan kullanıcı verileriyle bir sayfalar uygulaması oluşturmayı öğrenin. HTTPS, kimlik doğrulaması, güvenlik, ASP.NET Core içerir Identity .
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: f50015af864a4a62abd5e2eab508aac915cb6370
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404723"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="6af9b-104">Yetkilendirme ile korunan kullanıcı verileriyle ASP.NET Core uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="6af9b-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="6af9b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [ali Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="6af9b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="6af9b-106">ASP.NET Core MVC sürümü için [Bu PDF 'ye](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) bakın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="6af9b-107">Bu öğreticinin ASP.NET Core 1,1 sürümü [Bu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="6af9b-108">1,1 ASP.NET Core örnek [örnekleri](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="6af9b-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="6af9b-109">[Bu PDF 'ye](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) bakın</span><span class="sxs-lookup"><span data-stu-id="6af9b-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6af9b-110">Bu öğreticide, yetkilendirme tarafından korunan kullanıcı verileriyle ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="6af9b-111">Kimliği doğrulanmış (kayıtlı) kullanıcıların oluşturduğu kişilerin listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6af9b-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="6af9b-112">Üç güvenlik grubu vardır:</span><span class="sxs-lookup"><span data-stu-id="6af9b-112">There are three security groups:</span></span>

* <span data-ttu-id="6af9b-113">**Kayıtlı kullanıcılar** tüm onaylanan verileri görüntüleyebilir ve kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="6af9b-114">**Yöneticiler** , kişi verilerini onaylayabilir veya reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="6af9b-115">Yalnızca onaylanan kişiler kullanıcılara görünür.</span><span class="sxs-lookup"><span data-stu-id="6af9b-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="6af9b-116">**Yöneticiler** , verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="6af9b-117">Bu belgedeki görüntüler en son şablonlarla tam olarak eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="6af9b-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="6af9b-118">Aşağıdaki görüntüde, User Rick ( `rick@example.com` ) oturum açtı.</span><span class="sxs-lookup"><span data-stu-id="6af9b-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="6af9b-119">Rick, onaylanan kişileri görüntüleyebilir ve **Düzenle** / **Delete**' e / ait kişiler için**yeni bağlantılar oluştur** ' a bakın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="6af9b-120">Yalnızca Rick tarafından oluşturulan son kayıt, **Düzenle** ve **Sil** bağlantılarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6af9b-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="6af9b-121">Yönetici veya yönetici durumu "Onaylandı" olarak değiştirene kadar diğer kullanıcılar son kaydı görmez.</span><span class="sxs-lookup"><span data-stu-id="6af9b-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick oturum açmış olduğunu gösteren ekran görüntüsü](secure-data/_static/rick.png)

<span data-ttu-id="6af9b-123">Aşağıdaki görüntüde, `manager@contoso.com` ve yöneticisinin rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="6af9b-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="6af9b-125">Aşağıdaki görüntüde, bir kişinin Yöneticiler Ayrıntılar görünümü gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="6af9b-125">The following image shows the managers details view of a contact:</span></span>

![Yöneticinin bir kişinin görünümü](secure-data/_static/manager.png)

<span data-ttu-id="6af9b-127">**Onaylama** ve **reddetme** düğmeleri yalnızca Yöneticiler ve yöneticiler için görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="6af9b-128">Aşağıdaki görüntüde, `admin@contoso.com` ve yönetici rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="6af9b-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="6af9b-130">Yöneticinin tüm ayrıcalıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="6af9b-130">The administrator has all privileges.</span></span> <span data-ttu-id="6af9b-131">Herhangi bir kişiyi okuyabilir/düzenleyebilir/silebilir ve kişilerin durumunu değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="6af9b-132">Uygulama, aşağıdaki model için [Yapı iskelesi](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tarafından oluşturulmuştur `Contact` :</span><span class="sxs-lookup"><span data-stu-id="6af9b-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="6af9b-133">Örnek, aşağıdaki yetkilendirme işleyicilerini içerir:</span><span class="sxs-lookup"><span data-stu-id="6af9b-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="6af9b-134">`ContactIsOwnerAuthorizationHandler`: Bir kullanıcının yalnızca verilerini düzenleyebilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6af9b-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="6af9b-135">`ContactManagerAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6af9b-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="6af9b-136">`ContactAdministratorsAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini ve kişileri düzenlemesini/silmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6af9b-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6af9b-137">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="6af9b-137">Prerequisites</span></span>

<span data-ttu-id="6af9b-138">Bu öğretici gelişmiş bir deyişle.</span><span class="sxs-lookup"><span data-stu-id="6af9b-138">This tutorial is advanced.</span></span> <span data-ttu-id="6af9b-139">Şunu tanımanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="6af9b-139">You should be familiar with:</span></span>

* [<span data-ttu-id="6af9b-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6af9b-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="6af9b-141">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="6af9b-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="6af9b-142">Hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="6af9b-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="6af9b-143">Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="6af9b-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="6af9b-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6af9b-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="6af9b-145">Başlatıcı ve tamamlanmış uygulama</span><span class="sxs-lookup"><span data-stu-id="6af9b-145">The starter and completed app</span></span>

<span data-ttu-id="6af9b-146">[Tamamlanmış](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) uygulamayı [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="6af9b-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="6af9b-147">Tamamlanmış uygulamayı [Test](#test-the-completed-app) edin, böylece güvenlik özellikleri hakkında bilgi sahibi olun.</span><span class="sxs-lookup"><span data-stu-id="6af9b-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="6af9b-148">Başlangıç uygulaması</span><span class="sxs-lookup"><span data-stu-id="6af9b-148">The starter app</span></span>

<span data-ttu-id="6af9b-149">[Başlangıç](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) uygulamasını [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="6af9b-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="6af9b-150">Uygulamayı çalıştırın, **ContactManager** bağlantısına dokunun ve bir kişi oluşturup silemdiğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="6af9b-151">Güvenli Kullanıcı verileri</span><span class="sxs-lookup"><span data-stu-id="6af9b-151">Secure user data</span></span>

<span data-ttu-id="6af9b-152">Aşağıdaki bölümlerde, güvenli Kullanıcı verileri uygulaması oluşturmak için tüm önemli adımlar verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="6af9b-153">Tamamlanmış projeye başvurmak faydalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="6af9b-154">Kişi verilerini kullanıcıya bağlama</span><span class="sxs-lookup"><span data-stu-id="6af9b-154">Tie the contact data to the user</span></span>

<span data-ttu-id="6af9b-155">[Identity](xref:security/authentication/identity)Kullanıcıların verilerini düzenleyebilmeleri, ancak diğer kullanıcıların verilerini düzenleyebilmeleri için ASP.NET Kullanıcı kimliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="6af9b-156">`OwnerID`Model ekleyin `ContactStatus` `Contact` :</span><span class="sxs-lookup"><span data-stu-id="6af9b-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="6af9b-157">`OwnerID`kullanıcının `AspNetUser` veritabanındaki TABLODAKI kimliği [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="6af9b-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="6af9b-158">Bu `Status` alan, bir kişinin genel kullanıcılar tarafından görüntülenebilir olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="6af9b-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="6af9b-159">Yeni bir geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="6af9b-160">Rol hizmetlerini EkleIdentity</span><span class="sxs-lookup"><span data-stu-id="6af9b-160">Add Role services to Identity</span></span>

<span data-ttu-id="6af9b-161">Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="6af9b-162">Kimliği doğrulanmış kullanıcılar iste</span><span class="sxs-lookup"><span data-stu-id="6af9b-162">Require authenticated users</span></span>

<span data-ttu-id="6af9b-163">Kullanıcıların kimliklerinin doğrulanmasını gerektirmek için varsayılan kimlik doğrulama ilkesini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="6af9b-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="6af9b-164">Bir Razor sayfada, denetleyicide veya eylem yöntemi düzeyinde kimlik doğrulamasından vazgeçebilirsiniz `[AllowAnonymous]` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="6af9b-165">Varsayılan kimlik doğrulama ilkesini kullanıcıların kimliğinin doğrulanmasını gerektirecek şekilde ayarlamak, yeni eklenen Razor sayfaları ve denetleyicileri korur.</span><span class="sxs-lookup"><span data-stu-id="6af9b-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="6af9b-166">Varsayılan olarak kimlik doğrulamanın gerekli olması, yeni denetleyicilere ve Razor sayfalarına bağlı olarak, özniteliğini dahil etmek için daha güvenlidir `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="6af9b-167">Anonim kullanıcıların, kaydolmadan önce site hakkında bilgi alması için dizin ve gizlilik sayfalarına [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="6af9b-168">Test hesabını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6af9b-168">Configure the test account</span></span>

<span data-ttu-id="6af9b-169">`SeedData`Sınıfı iki hesap oluşturur: yönetici ve yönetici.</span><span class="sxs-lookup"><span data-stu-id="6af9b-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="6af9b-170">Bu hesaplara bir parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets) kullanın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="6af9b-171">Parolayı proje dizininden ayarlayın ( *program.cs*içeren dizin):</span><span class="sxs-lookup"><span data-stu-id="6af9b-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="6af9b-172">Güçlü bir parola belirtilmemişse, çağrıldığında bir özel durum oluşturulur `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="6af9b-173">`Main`Sınama parolasını kullanacak şekilde güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="6af9b-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="6af9b-174">Test hesapları oluşturma ve kişileri güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="6af9b-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="6af9b-175">`Initialize` `SeedData` Test hesapları oluşturmak için sınıfındaki yöntemi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="6af9b-176">Yönetici kullanıcı KIMLIĞINI ve kişilerini ekleyin `ContactStatus` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="6af9b-177">Kişilerden birini "gönderildi" ve bir "reddedildi" yapın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="6af9b-178">Kullanıcı KIMLIĞINI ve durumunu tüm kişilere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="6af9b-179">Yalnızca bir kişi gösterilir:</span><span class="sxs-lookup"><span data-stu-id="6af9b-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="6af9b-180">Sahip, yönetici ve yönetici yetkilendirme işleyicileri oluşturma</span><span class="sxs-lookup"><span data-stu-id="6af9b-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="6af9b-181">`ContactIsOwnerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6af9b-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6af9b-182">`ContactIsOwnerAuthorizationHandler`Kullanıcının kaynağın sahibi olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="6af9b-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="6af9b-183">`ContactIsOwnerAuthorizationHandler`Çağıran [bağlamı. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)Kimliği doğrulanmış geçerli kullanıcı kişi sahibsiyse başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="6af9b-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="6af9b-184">Yetkilendirme işleyicileri genellikle:</span><span class="sxs-lookup"><span data-stu-id="6af9b-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="6af9b-185">`context.Succeed`Gereksinimler karşılandığında döndürün.</span><span class="sxs-lookup"><span data-stu-id="6af9b-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="6af9b-186">`Task.CompletedTask`Gereksinimler karşılanmazsa döndürün.</span><span class="sxs-lookup"><span data-stu-id="6af9b-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="6af9b-187">`Task.CompletedTask`başarılı veya başarısız değil, &mdash; diğer yetkilendirme işleyicilerinin çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="6af9b-188">Açıkça başarısız olması gerekiyorsa, [bağlamı döndürün. Başarısız oldu](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="6af9b-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="6af9b-189">Uygulama, iletişim sahiplerinin kendi verilerini düzenlemesine/silmesine/oluşturmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="6af9b-190">`ContactIsOwnerAuthorizationHandler`gereksinim parametresine geçirilen işlemi denetlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="6af9b-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="6af9b-191">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="6af9b-191">Create a manager authorization handler</span></span>

<span data-ttu-id="6af9b-192">`ContactManagerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6af9b-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6af9b-193">`ContactManagerAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="6af9b-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="6af9b-194">İçerik değişikliklerini yalnızca Yöneticiler onaylayabilir veya reddedebilir (yeni veya değiştirilmiş).</span><span class="sxs-lookup"><span data-stu-id="6af9b-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="6af9b-195">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="6af9b-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="6af9b-196">`ContactAdministratorsAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6af9b-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6af9b-197">`ContactAdministratorsAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="6af9b-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="6af9b-198">Yönetici tüm işlemleri yapabilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="6af9b-199">Yetkilendirme işleyicilerini kaydetme</span><span class="sxs-lookup"><span data-stu-id="6af9b-199">Register the authorization handlers</span></span>

<span data-ttu-id="6af9b-200">Entity Framework Core kullanan hizmetlerin, [Addkapsamlıdır](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)kullanılarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) için kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="6af9b-201">, `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core oluşturulan ASP.NET Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="6af9b-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="6af9b-202">İşleyicileri hizmet koleksiyonuyla kaydedin, bu sayede `ContactsController` [bağımlılık ekleme](xref:fundamentals/dependency-injection)üzerinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6af9b-203">Aşağıdaki kodu sonuna ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6af9b-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="6af9b-204">`ContactAdministratorsAuthorizationHandler`ve `ContactManagerAuthorizationHandler` tekton olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="6af9b-205">Bu, EF kullanmayan ve gereken tüm bilgiler yöntemin parametresinde olduğundan tektonlar vardır `Context` `HandleRequirementAsync` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="6af9b-206">Destek yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="6af9b-206">Support authorization</span></span>

<span data-ttu-id="6af9b-207">Bu bölümde, Razor sayfaları güncelleştirir ve bir işlem gereksinimleri sınıfı eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="6af9b-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="6af9b-208">İlgili kişi işlemleri gereksinimleri sınıfını gözden geçirin</span><span class="sxs-lookup"><span data-stu-id="6af9b-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="6af9b-209">Sınıfını gözden geçirin `ContactOperations` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="6af9b-210">Bu sınıf, uygulamanın desteklediği gereksinimleri içerir:</span><span class="sxs-lookup"><span data-stu-id="6af9b-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="6af9b-211">Kişiler sayfaları için temel sınıf oluşturma Razor</span><span class="sxs-lookup"><span data-stu-id="6af9b-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="6af9b-212">Kişiler sayfalarında kullanılan hizmetleri içeren bir temel sınıf oluşturun Razor .</span><span class="sxs-lookup"><span data-stu-id="6af9b-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="6af9b-213">Temel sınıf, başlatma kodunu bir konuma koyar:</span><span class="sxs-lookup"><span data-stu-id="6af9b-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="6af9b-214">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="6af9b-214">The preceding code:</span></span>

* <span data-ttu-id="6af9b-215">`IAuthorizationService`Yetkilendirme işleyicilerine erişim için hizmeti ekler.</span><span class="sxs-lookup"><span data-stu-id="6af9b-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="6af9b-216">Hizmeti ekler Identity `UserManager` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="6af9b-217">Öğesini ekleyin `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="6af9b-218">CreateModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6af9b-218">Update the CreateModel</span></span>

<span data-ttu-id="6af9b-219">Sayfa modeli oluşturma oluşturucusunu temel sınıfı kullanacak şekilde güncelleştirin `DI_BasePageModel` :</span><span class="sxs-lookup"><span data-stu-id="6af9b-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="6af9b-220">Yöntemi şu `CreateModel.OnPostAsync` şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="6af9b-221">Kullanıcı KIMLIĞINI `Contact` modele ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="6af9b-222">Kullanıcının kişi oluşturma iznine sahip olduğunu doğrulamak için yetkilendirme işleyicisini çağırın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="6af9b-223">Indexmodel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6af9b-223">Update the IndexModel</span></span>

<span data-ttu-id="6af9b-224">`OnGetAsync`Yöntemi yalnızca onaylanan kişilerin genel kullanıcılara gösterilmesi için güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="6af9b-225">EditModel güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="6af9b-225">Update the EditModel</span></span>

<span data-ttu-id="6af9b-226">Kullanıcının kişiye ait olduğunu doğrulamak için bir yetkilendirme işleyicisi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="6af9b-227">Kaynak yetkilendirmesi doğrulandığında, `[Authorize]` öznitelik yeterli değildir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="6af9b-228">Öznitelikler değerlendirildiğinde uygulamanın kaynağa erişimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="6af9b-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="6af9b-229">Kaynak tabanlı yetkilendirme sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6af9b-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="6af9b-230">Uygulamanın kaynağa erişimi olduğunda, sayfa modeline yükleyerek veya işleyicinin kendisi içine yükleyerek denetimlerin gerçekleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="6af9b-231">Kaynak anahtarını geçirerek kaynağa sıkça erişirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6af9b-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="6af9b-232">DeleteModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6af9b-232">Update the DeleteModel</span></span>

<span data-ttu-id="6af9b-233">Kullanıcının kişide silme iznine sahip olduğunu doğrulamak için, silme sayfası modelini yetkilendirme işleyicisini kullanacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="6af9b-234">Yetkilendirme hizmetini görünümlere ekleme</span><span class="sxs-lookup"><span data-stu-id="6af9b-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="6af9b-235">Şu anda kullanıcı ARABIRIMI, kullanıcının değiştiremeyeceğiniz kişiler için Düzenle ve Sil bağlantılarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="6af9b-236">Yetkilendirme hizmetini *Sayfalar/_ViewImports. cshtml* dosyasına ekleme, bu nedenle tüm görünümlerde kullanılabilir hale gelir:</span><span class="sxs-lookup"><span data-stu-id="6af9b-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="6af9b-237">Yukarıdaki biçimlendirme birkaç deyim ekliyor `using` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="6af9b-238">*Sayfalar/kişiler/Index. cshtml* 'deki **Düzenle** ve **Sil** bağlantılarını yalnızca uygun izinlere sahip kullanıcılar için işlendikleri şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="6af9b-239">Verileri değiştirme izni olmayan kullanıcıların bağlantılarının gizlenmesi, uygulamanın güvenliğini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6af9b-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="6af9b-240">Bağlantıların gizlenmesi, yalnızca geçerli bağlantıları görüntüleyerek uygulamayı daha kolay hale getirir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="6af9b-241">Kullanıcılar, sahip olmadıkları veriler üzerinde düzenleme ve silme işlemlerini çağırmak için oluşturulan URL 'Leri hacme edebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="6af9b-242">RazorSayfanın veya denetleyicinin, verilerin güvenliğini sağlamak için erişim denetimlerini zorlaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="6af9b-243">Güncelleştirme ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="6af9b-243">Update Details</span></span>

<span data-ttu-id="6af9b-244">Yöneticilerin kişileri onaylamasını veya reddedebilmesi için Ayrıntılar görünümünü güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="6af9b-245">Ayrıntılar sayfası modelini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="6af9b-246">Bir role Kullanıcı ekleme veya rolü kaldırma</span><span class="sxs-lookup"><span data-stu-id="6af9b-246">Add or remove a user to a role</span></span>

<span data-ttu-id="6af9b-247">Hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8502) bakın:</span><span class="sxs-lookup"><span data-stu-id="6af9b-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="6af9b-248">Bir kullanıcıdan ayrıcalıklar kaldırılıyor.</span><span class="sxs-lookup"><span data-stu-id="6af9b-248">Removing privileges from a user.</span></span> <span data-ttu-id="6af9b-249">Örneğin, bir sohbet uygulamasındaki kullanıcıyı kapatma.</span><span class="sxs-lookup"><span data-stu-id="6af9b-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="6af9b-250">Bir kullanıcıya ayrıcalık ekleme.</span><span class="sxs-lookup"><span data-stu-id="6af9b-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="6af9b-251">Challenge ve Fordeklarasyon arasındaki farklar</span><span class="sxs-lookup"><span data-stu-id="6af9b-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="6af9b-252">Bu uygulama, varsayılan ilkeyi [kimliği doğrulanmış kullanıcıları gerektirecek](#require-authenticated-users)şekilde ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6af9b-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="6af9b-253">Aşağıdaki kod anonim kullanıcılara izin verir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-253">The following code allows anonymous users.</span></span> <span data-ttu-id="6af9b-254">Anonim kullanıcılara, çekişme ile Fordeklarasyon arasındaki farkları gösterme izni verilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="6af9b-255">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="6af9b-255">In the preceding code:</span></span>

* <span data-ttu-id="6af9b-256">Kullanıcının kimliği **doğrulanmadığı** zaman, bir `ChallengeResult` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6af9b-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="6af9b-257">Bir `ChallengeResult` döndürüldüğünde, Kullanıcı oturum açma sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="6af9b-258">Kullanıcının kimliği doğrulandığında, ancak yetkilendirilmediğinde, bir `ForbidResult` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6af9b-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="6af9b-259">Bir `ForbidResult` döndürüldüğünde, Kullanıcı erişim reddedildi sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="6af9b-260">Tamamlanmış uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="6af9b-260">Test the completed app</span></span>

<span data-ttu-id="6af9b-261">Önceden oluşturulmuş kullanıcı hesapları için bir parola ayarlamadıysanız, parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets#secret-manager) kullanın:</span><span class="sxs-lookup"><span data-stu-id="6af9b-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="6af9b-262">Güçlü bir parola seçin: sekiz veya daha fazla karakter, en az bir büyük harf karakter, sayı ve simge kullanın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="6af9b-263">Örneğin, `Passw0rd!` güçlü parola gereksinimlerini karşılar.</span><span class="sxs-lookup"><span data-stu-id="6af9b-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="6af9b-264">Aşağıdaki komutu projenin klasöründen çalıştırın, burada `<PW>` parola olur:</span><span class="sxs-lookup"><span data-stu-id="6af9b-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="6af9b-265">Uygulamanın kişileri varsa:</span><span class="sxs-lookup"><span data-stu-id="6af9b-265">If the app has contacts:</span></span>

* <span data-ttu-id="6af9b-266">Tablodaki tüm kayıtları silin `Contact` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="6af9b-267">Veritabanını temel alarak uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="6af9b-268">Tamamlanmış uygulamayı test etmenin kolay bir yolu da üç farklı tarayıcı (veya bilito/InPrivate oturumlar) kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="6af9b-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="6af9b-269">Tek bir tarayıcıda yeni bir Kullanıcı kaydedin (örneğin, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="6af9b-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="6af9b-270">Her bir tarayıcıda farklı bir kullanıcıyla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="6af9b-271">Aşağıdaki işlemleri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="6af9b-271">Verify the following operations:</span></span>

* <span data-ttu-id="6af9b-272">Kayıtlı kullanıcılar tüm onaylanan iletişim verilerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="6af9b-273">Kayıtlı kullanıcılar kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="6af9b-274">Yöneticiler, kişi verilerini onaylayabilir/reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="6af9b-275">`Details`Görünüm **Onayla** ve **Reddet** düğmelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="6af9b-276">Yöneticiler tüm verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="6af9b-277">Kullanıcı</span><span class="sxs-lookup"><span data-stu-id="6af9b-277">User</span></span>                | <span data-ttu-id="6af9b-278">Uygulama tarafından sağlanan</span><span class="sxs-lookup"><span data-stu-id="6af9b-278">Seeded by the app</span></span> | <span data-ttu-id="6af9b-279">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="6af9b-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="6af9b-280">No</span><span class="sxs-lookup"><span data-stu-id="6af9b-280">No</span></span>                | <span data-ttu-id="6af9b-281">Kendi verilerini düzenleyin/silin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="6af9b-282">Yes</span><span class="sxs-lookup"><span data-stu-id="6af9b-282">Yes</span></span>               | <span data-ttu-id="6af9b-283">Kendi verilerini onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="6af9b-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="6af9b-284">Yes</span><span class="sxs-lookup"><span data-stu-id="6af9b-284">Yes</span></span>               | <span data-ttu-id="6af9b-285">Tüm verileri onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="6af9b-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="6af9b-286">Yöneticinin tarayıcısında bir kişi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6af9b-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="6af9b-287">Yönetici iletişim kutusundan silme ve düzenleme için URL 'YI kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="6af9b-288">Bu bağlantıları test kullanıcısının bu işlemleri gerçekleştiremediğinizi doğrulamak için test kullanıcısının tarayıcısına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="6af9b-289">Başlangıç uygulamasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="6af9b-289">Create the starter app</span></span>

* <span data-ttu-id="6af9b-290">Razor"ContactManager" adlı bir sayfalar uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="6af9b-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="6af9b-291">**Ayrı kullanıcı hesaplarıyla**uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6af9b-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="6af9b-292">Ad alanı örnekte kullanılan ad alanıyla eşleşecek şekilde "ContactManager" olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="6af9b-293">`-uld`SQLite yerine LocalDB belirtir</span><span class="sxs-lookup"><span data-stu-id="6af9b-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="6af9b-294">*Modeller/Ilgili kişi ekle. cs*:</span><span class="sxs-lookup"><span data-stu-id="6af9b-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="6af9b-295">Modeli dolandırıcıdan katlayın `Contact` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="6af9b-296">İlk geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="6af9b-297">Komutuyla bir hata yaşarsanız `dotnet aspnet-codegenerator razorpage` , [Bu GitHub sorununa](https://github.com/aspnet/Scaffolding/issues/984)bakın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="6af9b-298">*Pages/Shared/_Layout. cshtml* dosyasındaki **ContactManager** bağlayıcısını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="6af9b-299">Kişiyi oluşturarak, düzenleyerek ve silerek uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="6af9b-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="6af9b-300">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="6af9b-300">Seed the database</span></span>

<span data-ttu-id="6af9b-301">*Veri* klasörüne [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) sınıfını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="6af9b-302">`SeedData.Initialize`Şuradan arayın `Main` :</span><span class="sxs-lookup"><span data-stu-id="6af9b-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="6af9b-303">Uygulamanın veritabanını sunan test edin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-303">Test that the app seeded the database.</span></span> <span data-ttu-id="6af9b-304">Kişi VERITABANıNDA herhangi bir satır varsa, çekirdek Yöntem çalıştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="6af9b-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="6af9b-305">Bu öğreticide, yetkilendirme tarafından korunan kullanıcı verileriyle ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="6af9b-306">Kimliği doğrulanmış (kayıtlı) kullanıcıların oluşturduğu kişilerin listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6af9b-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="6af9b-307">Üç güvenlik grubu vardır:</span><span class="sxs-lookup"><span data-stu-id="6af9b-307">There are three security groups:</span></span>

* <span data-ttu-id="6af9b-308">**Kayıtlı kullanıcılar** tüm onaylanan verileri görüntüleyebilir ve kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="6af9b-309">**Yöneticiler** , kişi verilerini onaylayabilir veya reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="6af9b-310">Yalnızca onaylanan kişiler kullanıcılara görünür.</span><span class="sxs-lookup"><span data-stu-id="6af9b-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="6af9b-311">**Yöneticiler** , verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="6af9b-312">Aşağıdaki görüntüde, User Rick ( `rick@example.com` ) oturum açtı.</span><span class="sxs-lookup"><span data-stu-id="6af9b-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="6af9b-313">Rick, onaylanan kişileri görüntüleyebilir ve **Düzenle** / **Delete**' e / ait kişiler için**yeni bağlantılar oluştur** ' a bakın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="6af9b-314">Yalnızca Rick tarafından oluşturulan son kayıt, **Düzenle** ve **Sil** bağlantılarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6af9b-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="6af9b-315">Yönetici veya yönetici durumu "Onaylandı" olarak değiştirene kadar diğer kullanıcılar son kaydı görmez.</span><span class="sxs-lookup"><span data-stu-id="6af9b-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick oturum açmış olduğunu gösteren ekran görüntüsü](secure-data/_static/rick.png)

<span data-ttu-id="6af9b-317">Aşağıdaki görüntüde, `manager@contoso.com` ve yöneticisinin rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="6af9b-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="6af9b-319">Aşağıdaki görüntüde, bir kişinin Yöneticiler Ayrıntılar görünümü gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="6af9b-319">The following image shows the managers details view of a contact:</span></span>

![Yöneticinin bir kişinin görünümü](secure-data/_static/manager.png)

<span data-ttu-id="6af9b-321">**Onaylama** ve **reddetme** düğmeleri yalnızca Yöneticiler ve yöneticiler için görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="6af9b-322">Aşağıdaki görüntüde, `admin@contoso.com` ve yönetici rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="6af9b-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="6af9b-324">Yöneticinin tüm ayrıcalıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="6af9b-324">The administrator has all privileges.</span></span> <span data-ttu-id="6af9b-325">Herhangi bir kişiyi okuyabilir/düzenleyebilir/silebilir ve kişilerin durumunu değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="6af9b-326">Uygulama, aşağıdaki model için [Yapı iskelesi](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tarafından oluşturulmuştur `Contact` :</span><span class="sxs-lookup"><span data-stu-id="6af9b-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="6af9b-327">Örnek, aşağıdaki yetkilendirme işleyicilerini içerir:</span><span class="sxs-lookup"><span data-stu-id="6af9b-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="6af9b-328">`ContactIsOwnerAuthorizationHandler`: Bir kullanıcının yalnızca verilerini düzenleyebilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6af9b-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="6af9b-329">`ContactManagerAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6af9b-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="6af9b-330">`ContactAdministratorsAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini ve kişileri düzenlemesini/silmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6af9b-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6af9b-331">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="6af9b-331">Prerequisites</span></span>

<span data-ttu-id="6af9b-332">Bu öğretici gelişmiş bir deyişle.</span><span class="sxs-lookup"><span data-stu-id="6af9b-332">This tutorial is advanced.</span></span> <span data-ttu-id="6af9b-333">Şunu tanımanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="6af9b-333">You should be familiar with:</span></span>

* [<span data-ttu-id="6af9b-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6af9b-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="6af9b-335">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="6af9b-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="6af9b-336">Hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="6af9b-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="6af9b-337">Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="6af9b-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="6af9b-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6af9b-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="6af9b-339">Başlatıcı ve tamamlanmış uygulama</span><span class="sxs-lookup"><span data-stu-id="6af9b-339">The starter and completed app</span></span>

<span data-ttu-id="6af9b-340">[Tamamlanmış](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) uygulamayı [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="6af9b-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="6af9b-341">Tamamlanmış uygulamayı [Test](#test-the-completed-app) edin, böylece güvenlik özellikleri hakkında bilgi sahibi olun.</span><span class="sxs-lookup"><span data-stu-id="6af9b-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="6af9b-342">Başlangıç uygulaması</span><span class="sxs-lookup"><span data-stu-id="6af9b-342">The starter app</span></span>

<span data-ttu-id="6af9b-343">[Başlangıç](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) uygulamasını [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="6af9b-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="6af9b-344">Uygulamayı çalıştırın, **ContactManager** bağlantısına dokunun ve bir kişi oluşturup silemdiğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="6af9b-345">Güvenli Kullanıcı verileri</span><span class="sxs-lookup"><span data-stu-id="6af9b-345">Secure user data</span></span>

<span data-ttu-id="6af9b-346">Aşağıdaki bölümlerde, güvenli Kullanıcı verileri uygulaması oluşturmak için tüm önemli adımlar verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="6af9b-347">Tamamlanmış projeye başvurmak faydalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="6af9b-348">Kişi verilerini kullanıcıya bağlama</span><span class="sxs-lookup"><span data-stu-id="6af9b-348">Tie the contact data to the user</span></span>

<span data-ttu-id="6af9b-349">[Identity](xref:security/authentication/identity)Kullanıcıların verilerini düzenleyebilmeleri, ancak diğer kullanıcıların verilerini düzenleyebilmeleri için ASP.NET Kullanıcı kimliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="6af9b-350">`OwnerID`Model ekleyin `ContactStatus` `Contact` :</span><span class="sxs-lookup"><span data-stu-id="6af9b-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="6af9b-351">`OwnerID`kullanıcının `AspNetUser` veritabanındaki TABLODAKI kimliği [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="6af9b-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="6af9b-352">Bu `Status` alan, bir kişinin genel kullanıcılar tarafından görüntülenebilir olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="6af9b-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="6af9b-353">Yeni bir geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="6af9b-354">Rol hizmetlerini EkleIdentity</span><span class="sxs-lookup"><span data-stu-id="6af9b-354">Add Role services to Identity</span></span>

<span data-ttu-id="6af9b-355">Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="6af9b-356">Kimliği doğrulanmış kullanıcılar iste</span><span class="sxs-lookup"><span data-stu-id="6af9b-356">Require authenticated users</span></span>

<span data-ttu-id="6af9b-357">Kullanıcıların kimliklerinin doğrulanmasını gerektirmek için varsayılan kimlik doğrulama ilkesini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="6af9b-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="6af9b-358">Bir Razor sayfada, denetleyicide veya eylem yöntemi düzeyinde kimlik doğrulamasından vazgeçebilirsiniz `[AllowAnonymous]` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="6af9b-359">Varsayılan kimlik doğrulama ilkesini kullanıcıların kimliğinin doğrulanmasını gerektirecek şekilde ayarlamak, yeni eklenen Razor sayfaları ve denetleyicileri korur.</span><span class="sxs-lookup"><span data-stu-id="6af9b-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="6af9b-360">Varsayılan olarak kimlik doğrulamanın gerekli olması, yeni denetleyicilere ve Razor sayfalarına bağlı olarak, özniteliğini dahil etmek için daha güvenlidir `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="6af9b-361">Anonim kullanıcıların, kayıt yaptırmadan önce site hakkında bilgi alması için dizine, hakkında ve Iletişim sayfalarına [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="6af9b-362">Test hesabını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6af9b-362">Configure the test account</span></span>

<span data-ttu-id="6af9b-363">`SeedData`Sınıfı iki hesap oluşturur: yönetici ve yönetici.</span><span class="sxs-lookup"><span data-stu-id="6af9b-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="6af9b-364">Bu hesaplara bir parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets) kullanın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="6af9b-365">Parolayı proje dizininden ayarlayın ( *program.cs*içeren dizin):</span><span class="sxs-lookup"><span data-stu-id="6af9b-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="6af9b-366">Güçlü bir parola belirtilmemişse, çağrıldığında bir özel durum oluşturulur `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="6af9b-367">`Main`Sınama parolasını kullanacak şekilde güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="6af9b-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="6af9b-368">Test hesapları oluşturma ve kişileri güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="6af9b-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="6af9b-369">`Initialize` `SeedData` Test hesapları oluşturmak için sınıfındaki yöntemi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="6af9b-370">Yönetici kullanıcı KIMLIĞINI ve kişilerini ekleyin `ContactStatus` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="6af9b-371">Kişilerden birini "gönderildi" ve bir "reddedildi" yapın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="6af9b-372">Kullanıcı KIMLIĞINI ve durumunu tüm kişilere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="6af9b-373">Yalnızca bir kişi gösterilir:</span><span class="sxs-lookup"><span data-stu-id="6af9b-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="6af9b-374">Sahip, yönetici ve yönetici yetkilendirme işleyicileri oluşturma</span><span class="sxs-lookup"><span data-stu-id="6af9b-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="6af9b-375">Bir *Yetkilendirme* klasörü oluşturun ve içinde bir `ContactIsOwnerAuthorizationHandler` sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6af9b-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="6af9b-376">`ContactIsOwnerAuthorizationHandler`Kullanıcının kaynağın sahibi olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="6af9b-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="6af9b-377">`ContactIsOwnerAuthorizationHandler`Çağıran [bağlamı. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)Kimliği doğrulanmış geçerli kullanıcı kişi sahibsiyse başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="6af9b-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="6af9b-378">Yetkilendirme işleyicileri genellikle:</span><span class="sxs-lookup"><span data-stu-id="6af9b-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="6af9b-379">`context.Succeed`Gereksinimler karşılandığında döndürün.</span><span class="sxs-lookup"><span data-stu-id="6af9b-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="6af9b-380">`Task.CompletedTask`Gereksinimler karşılanmazsa döndürün.</span><span class="sxs-lookup"><span data-stu-id="6af9b-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="6af9b-381">`Task.CompletedTask`başarılı veya başarısız değil, &mdash; diğer yetkilendirme işleyicilerinin çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="6af9b-382">Açıkça başarısız olması gerekiyorsa, [bağlamı döndürün. Başarısız oldu](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="6af9b-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="6af9b-383">Uygulama, iletişim sahiplerinin kendi verilerini düzenlemesine/silmesine/oluşturmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="6af9b-384">`ContactIsOwnerAuthorizationHandler`gereksinim parametresine geçirilen işlemi denetlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="6af9b-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="6af9b-385">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="6af9b-385">Create a manager authorization handler</span></span>

<span data-ttu-id="6af9b-386">`ContactManagerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6af9b-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6af9b-387">`ContactManagerAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="6af9b-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="6af9b-388">İçerik değişikliklerini yalnızca Yöneticiler onaylayabilir veya reddedebilir (yeni veya değiştirilmiş).</span><span class="sxs-lookup"><span data-stu-id="6af9b-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="6af9b-389">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="6af9b-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="6af9b-390">`ContactAdministratorsAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6af9b-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6af9b-391">`ContactAdministratorsAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="6af9b-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="6af9b-392">Yönetici tüm işlemleri yapabilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="6af9b-393">Yetkilendirme işleyicilerini kaydetme</span><span class="sxs-lookup"><span data-stu-id="6af9b-393">Register the authorization handlers</span></span>

<span data-ttu-id="6af9b-394">Entity Framework Core kullanan hizmetlerin, [Addkapsamlıdır](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)kullanılarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) için kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="6af9b-395">, `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core oluşturulan ASP.NET Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="6af9b-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="6af9b-396">İşleyicileri hizmet koleksiyonuyla kaydedin, bu sayede `ContactsController` [bağımlılık ekleme](xref:fundamentals/dependency-injection)üzerinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6af9b-397">Aşağıdaki kodu sonuna ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6af9b-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="6af9b-398">`ContactAdministratorsAuthorizationHandler`ve `ContactManagerAuthorizationHandler` tekton olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="6af9b-399">Bu, EF kullanmayan ve gereken tüm bilgiler yöntemin parametresinde olduğundan tektonlar vardır `Context` `HandleRequirementAsync` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="6af9b-400">Destek yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="6af9b-400">Support authorization</span></span>

<span data-ttu-id="6af9b-401">Bu bölümde, Razor sayfaları güncelleştirir ve bir işlem gereksinimleri sınıfı eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="6af9b-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="6af9b-402">İlgili kişi işlemleri gereksinimleri sınıfını gözden geçirin</span><span class="sxs-lookup"><span data-stu-id="6af9b-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="6af9b-403">Sınıfını gözden geçirin `ContactOperations` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="6af9b-404">Bu sınıf, uygulamanın desteklediği gereksinimleri içerir:</span><span class="sxs-lookup"><span data-stu-id="6af9b-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="6af9b-405">Kişiler sayfaları için temel sınıf oluşturma Razor</span><span class="sxs-lookup"><span data-stu-id="6af9b-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="6af9b-406">Kişiler sayfalarında kullanılan hizmetleri içeren bir temel sınıf oluşturun Razor .</span><span class="sxs-lookup"><span data-stu-id="6af9b-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="6af9b-407">Temel sınıf, başlatma kodunu bir konuma koyar:</span><span class="sxs-lookup"><span data-stu-id="6af9b-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="6af9b-408">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="6af9b-408">The preceding code:</span></span>

* <span data-ttu-id="6af9b-409">`IAuthorizationService`Yetkilendirme işleyicilerine erişim için hizmeti ekler.</span><span class="sxs-lookup"><span data-stu-id="6af9b-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="6af9b-410">Hizmeti ekler Identity `UserManager` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="6af9b-411">Öğesini ekleyin `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="6af9b-412">CreateModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6af9b-412">Update the CreateModel</span></span>

<span data-ttu-id="6af9b-413">Sayfa modeli oluşturma oluşturucusunu temel sınıfı kullanacak şekilde güncelleştirin `DI_BasePageModel` :</span><span class="sxs-lookup"><span data-stu-id="6af9b-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="6af9b-414">Yöntemi şu `CreateModel.OnPostAsync` şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="6af9b-415">Kullanıcı KIMLIĞINI `Contact` modele ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="6af9b-416">Kullanıcının kişi oluşturma iznine sahip olduğunu doğrulamak için yetkilendirme işleyicisini çağırın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="6af9b-417">Indexmodel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6af9b-417">Update the IndexModel</span></span>

<span data-ttu-id="6af9b-418">`OnGetAsync`Yöntemi yalnızca onaylanan kişilerin genel kullanıcılara gösterilmesi için güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="6af9b-419">EditModel güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="6af9b-419">Update the EditModel</span></span>

<span data-ttu-id="6af9b-420">Kullanıcının kişiye ait olduğunu doğrulamak için bir yetkilendirme işleyicisi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="6af9b-421">Kaynak yetkilendirmesi doğrulandığında, `[Authorize]` öznitelik yeterli değildir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="6af9b-422">Öznitelikler değerlendirildiğinde uygulamanın kaynağa erişimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="6af9b-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="6af9b-423">Kaynak tabanlı yetkilendirme sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6af9b-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="6af9b-424">Uygulamanın kaynağa erişimi olduğunda, sayfa modeline yükleyerek veya işleyicinin kendisi içine yükleyerek denetimlerin gerçekleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="6af9b-425">Kaynak anahtarını geçirerek kaynağa sıkça erişirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6af9b-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="6af9b-426">DeleteModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="6af9b-426">Update the DeleteModel</span></span>

<span data-ttu-id="6af9b-427">Kullanıcının kişide silme iznine sahip olduğunu doğrulamak için, silme sayfası modelini yetkilendirme işleyicisini kullanacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="6af9b-428">Yetkilendirme hizmetini görünümlere ekleme</span><span class="sxs-lookup"><span data-stu-id="6af9b-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="6af9b-429">Şu anda kullanıcı ARABIRIMI, kullanıcının değiştiremeyeceğiniz kişiler için Düzenle ve Sil bağlantılarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="6af9b-430">Yetkilendirme hizmetini *Görünümler/_ViewImports. cshtml* dosyasında, tüm görünümlerde kullanılabilir olacak şekilde ekleme:</span><span class="sxs-lookup"><span data-stu-id="6af9b-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="6af9b-431">Yukarıdaki biçimlendirme birkaç deyim ekliyor `using` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="6af9b-432">*Sayfalar/kişiler/Index. cshtml* 'deki **Düzenle** ve **Sil** bağlantılarını yalnızca uygun izinlere sahip kullanıcılar için işlendikleri şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="6af9b-433">Verileri değiştirme izni olmayan kullanıcıların bağlantılarının gizlenmesi, uygulamanın güvenliğini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6af9b-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="6af9b-434">Bağlantıların gizlenmesi, yalnızca geçerli bağlantıları görüntüleyerek uygulamayı daha kolay hale getirir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="6af9b-435">Kullanıcılar, sahip olmadıkları veriler üzerinde düzenleme ve silme işlemlerini çağırmak için oluşturulan URL 'Leri hacme edebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="6af9b-436">RazorSayfanın veya denetleyicinin, verilerin güvenliğini sağlamak için erişim denetimlerini zorlaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="6af9b-437">Güncelleştirme ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="6af9b-437">Update Details</span></span>

<span data-ttu-id="6af9b-438">Yöneticilerin kişileri onaylamasını veya reddedebilmesi için Ayrıntılar görünümünü güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="6af9b-439">Ayrıntılar sayfası modelini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="6af9b-440">Bir role Kullanıcı ekleme veya rolü kaldırma</span><span class="sxs-lookup"><span data-stu-id="6af9b-440">Add or remove a user to a role</span></span>

<span data-ttu-id="6af9b-441">Hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8502) bakın:</span><span class="sxs-lookup"><span data-stu-id="6af9b-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="6af9b-442">Bir kullanıcıdan ayrıcalıklar kaldırılıyor.</span><span class="sxs-lookup"><span data-stu-id="6af9b-442">Removing privileges from a user.</span></span> <span data-ttu-id="6af9b-443">Örneğin, bir sohbet uygulamasındaki kullanıcıyı kapatma.</span><span class="sxs-lookup"><span data-stu-id="6af9b-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="6af9b-444">Bir kullanıcıya ayrıcalık ekleme.</span><span class="sxs-lookup"><span data-stu-id="6af9b-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="6af9b-445">Tamamlanmış uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="6af9b-445">Test the completed app</span></span>

<span data-ttu-id="6af9b-446">Önceden oluşturulmuş kullanıcı hesapları için bir parola ayarlamadıysanız, parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets#secret-manager) kullanın:</span><span class="sxs-lookup"><span data-stu-id="6af9b-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="6af9b-447">Güçlü bir parola seçin: sekiz veya daha fazla karakter, en az bir büyük harf karakter, sayı ve simge kullanın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="6af9b-448">Örneğin, `Passw0rd!` güçlü parola gereksinimlerini karşılar.</span><span class="sxs-lookup"><span data-stu-id="6af9b-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="6af9b-449">Aşağıdaki komutu projenin klasöründen çalıştırın, burada `<PW>` parola olur:</span><span class="sxs-lookup"><span data-stu-id="6af9b-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="6af9b-450">Veritabanını bırakma ve güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="6af9b-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="6af9b-451">Veritabanını temel alarak uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="6af9b-452">Tamamlanmış uygulamayı test etmenin kolay bir yolu da üç farklı tarayıcı (veya bilito/InPrivate oturumlar) kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="6af9b-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="6af9b-453">Tek bir tarayıcıda yeni bir Kullanıcı kaydedin (örneğin, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="6af9b-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="6af9b-454">Her bir tarayıcıda farklı bir kullanıcıyla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="6af9b-455">Aşağıdaki işlemleri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="6af9b-455">Verify the following operations:</span></span>

* <span data-ttu-id="6af9b-456">Kayıtlı kullanıcılar tüm onaylanan iletişim verilerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="6af9b-457">Kayıtlı kullanıcılar kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="6af9b-458">Yöneticiler, kişi verilerini onaylayabilir/reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="6af9b-459">`Details`Görünüm **Onayla** ve **Reddet** düğmelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="6af9b-460">Yöneticiler tüm verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="6af9b-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="6af9b-461">Kullanıcı</span><span class="sxs-lookup"><span data-stu-id="6af9b-461">User</span></span>                | <span data-ttu-id="6af9b-462">Uygulama tarafından sağlanan</span><span class="sxs-lookup"><span data-stu-id="6af9b-462">Seeded by the app</span></span> | <span data-ttu-id="6af9b-463">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="6af9b-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="6af9b-464">No</span><span class="sxs-lookup"><span data-stu-id="6af9b-464">No</span></span>                | <span data-ttu-id="6af9b-465">Kendi verilerini düzenleyin/silin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="6af9b-466">Yes</span><span class="sxs-lookup"><span data-stu-id="6af9b-466">Yes</span></span>               | <span data-ttu-id="6af9b-467">Kendi verilerini onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="6af9b-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="6af9b-468">Yes</span><span class="sxs-lookup"><span data-stu-id="6af9b-468">Yes</span></span>               | <span data-ttu-id="6af9b-469">Tüm verileri onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="6af9b-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="6af9b-470">Yöneticinin tarayıcısında bir kişi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6af9b-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="6af9b-471">Yönetici iletişim kutusundan silme ve düzenleme için URL 'YI kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="6af9b-472">Bu bağlantıları test kullanıcısının bu işlemleri gerçekleştiremediğinizi doğrulamak için test kullanıcısının tarayıcısına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="6af9b-473">Başlangıç uygulamasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="6af9b-473">Create the starter app</span></span>

* <span data-ttu-id="6af9b-474">Razor"ContactManager" adlı bir sayfalar uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="6af9b-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="6af9b-475">**Ayrı kullanıcı hesaplarıyla**uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6af9b-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="6af9b-476">Ad alanı örnekte kullanılan ad alanıyla eşleşecek şekilde "ContactManager" olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="6af9b-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="6af9b-477">`-uld`SQLite yerine LocalDB belirtir</span><span class="sxs-lookup"><span data-stu-id="6af9b-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="6af9b-478">*Modeller/Ilgili kişi ekle. cs*:</span><span class="sxs-lookup"><span data-stu-id="6af9b-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="6af9b-479">Modeli dolandırıcıdan katlayın `Contact` .</span><span class="sxs-lookup"><span data-stu-id="6af9b-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="6af9b-480">İlk geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="6af9b-481">*Pages/_Layout. cshtml* dosyasındaki **ContactManager** bağlayıcısını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="6af9b-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="6af9b-482">Kişiyi oluşturarak, düzenleyerek ve silerek uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="6af9b-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="6af9b-483">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="6af9b-483">Seed the database</span></span>

<span data-ttu-id="6af9b-484">*Veri* klasörüne [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) sınıfını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="6af9b-485">`SeedData.Initialize`Şuradan arayın `Main` :</span><span class="sxs-lookup"><span data-stu-id="6af9b-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="6af9b-486">Uygulamanın veritabanını sunan test edin.</span><span class="sxs-lookup"><span data-stu-id="6af9b-486">Test that the app seeded the database.</span></span> <span data-ttu-id="6af9b-487">Kişi VERITABANıNDA herhangi bir satır varsa, çekirdek Yöntem çalıştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="6af9b-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="6af9b-488">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6af9b-488">Additional resources</span></span>

* [<span data-ttu-id="6af9b-489">Azure App Service’te .NET Core ve SQL Veritabanı web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="6af9b-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="6af9b-490">[ASP.NET Core yetkilendirme Laboratuvarı](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="6af9b-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="6af9b-491">Bu laboratuvar, bu öğreticide sunulan güvenlik özellikleri hakkında daha fazla ayrıntıya gider.</span><span class="sxs-lookup"><span data-stu-id="6af9b-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="6af9b-492">Özel ilke tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="6af9b-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
