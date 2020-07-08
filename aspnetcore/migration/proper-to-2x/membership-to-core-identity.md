---
title: ASP.NET üyelik kimlik doğrulamasından ASP.NET Core 2,0 ' ye geçişIdentity
author: isaac2004
description: ASP.NET Core 2,0 ' ye üyelik kimlik doğrulaması kullanarak mevcut ASP.NET uygulamalarını geçirmeyi öğrenin Identity .
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: afad542a18a357a77f4542511a3d2c3108dbfb31
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059779"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="af0fd-103">ASP.NET üyelik kimlik doğrulamasından ASP.NET Core 2,0 ' ye geçişIdentity</span><span class="sxs-lookup"><span data-stu-id="af0fd-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="af0fd-104">İle [Isaac Levi](https://isaaclevin.com) tarafından</span><span class="sxs-lookup"><span data-stu-id="af0fd-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="af0fd-105">Bu makalede üyelik kimlik doğrulaması kullanılarak ASP.NET uygulamaları için veritabanı şemasının ASP.NET Core 2,0 ' ye geçirilmesi gösterilmektedir Identity .</span><span class="sxs-lookup"><span data-stu-id="af0fd-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="af0fd-106">Bu belge, ASP.NET üyelik tabanlı uygulamalar için veritabanı şemasını ASP.NET Core için kullanılan veritabanı şemasına geçirmek için gereken adımları sağlar Identity .</span><span class="sxs-lookup"><span data-stu-id="af0fd-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="af0fd-107">ASP.NET üyelik tabanlı kimlik doğrulamasından ASP.NET 'e geçiş hakkında daha fazla bilgi için Identity bkz. [SQL üyeliğinden mevcut bir uygulamayı ASP.net Identity 'ye geçirme ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="af0fd-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="af0fd-108">ASP.NET Core hakkında daha fazla bilgi için Identity bkz. [ Identity ASP.NET Core 'ye giriş](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="af0fd-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="af0fd-109">Üyelik şemasının incelenmesi</span><span class="sxs-lookup"><span data-stu-id="af0fd-109">Review of Membership schema</span></span>

<span data-ttu-id="af0fd-110">ASP.NET 2,0 ' den önce, geliştiriciler, uygulamaları için tüm kimlik doğrulama ve yetkilendirme sürecini oluşturmaya eklendi.</span><span class="sxs-lookup"><span data-stu-id="af0fd-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="af0fd-111">ASP.NET 2,0 ile üyelik tanıtılmıştı ve ASP.NET Apps içindeki güvenliği işlemek için ortak bir çözüm sağlar.</span><span class="sxs-lookup"><span data-stu-id="af0fd-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="af0fd-112">Geliştiriciler artık [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) komutuyla bir şemayı SQL Server veritabanına önyükleyebiliyor.</span><span class="sxs-lookup"><span data-stu-id="af0fd-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="af0fd-113">Bu komutu çalıştırdıktan sonra, veritabanında aşağıdaki tablolar oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="af0fd-113">After running this command, the following tables were created in the database.</span></span>

  ![Üyelik tabloları](identity/_static/membership-tables.png)

<span data-ttu-id="af0fd-115">Mevcut uygulamaları ASP.NET Core 2,0 ' ye geçirmek için Identity , bu tablolardaki verilerin yeni şema tarafından kullanılan tablolara geçirilmesi gerekir Identity .</span><span class="sxs-lookup"><span data-stu-id="af0fd-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="af0fd-116">ASP.NET Core Identity 2,0 şeması</span><span class="sxs-lookup"><span data-stu-id="af0fd-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="af0fd-117">ASP.NET Core 2,0, [Identity](/aspnet/identity/index) ASP.NET 4,5 ' de tanıtılan prensibi izler.</span><span class="sxs-lookup"><span data-stu-id="af0fd-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="af0fd-118">İlke paylaşılsa da, çerçeveler arasındaki uygulama, ASP.NET Core sürümleri arasında bile farklıdır (bkz. [kimlik doğrulamasını geçirme ve Identity ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="af0fd-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="af0fd-119">ASP.NET Core 2,0 şemasını görüntülemenin en hızlı yolu, Identity Yeni bir ASP.NET Core 2,0 uygulaması oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="af0fd-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="af0fd-120">Visual Studio 2017 'de şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="af0fd-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="af0fd-121">**Dosya**  >  **Yeni**  >  **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="af0fd-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="af0fd-122">*Coreıdentitysample*adlı yeni bir **ASP.NET Core Web uygulaması** projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="af0fd-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="af0fd-123">Açılan listede **ASP.NET Core 2,0** ' i seçin ve ardından **Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="af0fd-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="af0fd-124">Bu şablon bir [ Razor Sayfalar](xref:razor-pages/index) uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="af0fd-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="af0fd-125">**Tamam**' a tıklamadan önce **kimlik doğrulamasını Değiştir**' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="af0fd-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="af0fd-126">Şablonlar için **bireysel kullanıcı hesapları** seçin Identity .</span><span class="sxs-lookup"><span data-stu-id="af0fd-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="af0fd-127">Son olarak **Tamam**' a ve ardından **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="af0fd-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="af0fd-128">Visual Studio ASP.NET Core şablonunu kullanarak bir proje oluşturur Identity .</span><span class="sxs-lookup"><span data-stu-id="af0fd-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="af0fd-129">**Tools**  >  Paket Yöneticisi Konsolu (PMC) penceresini açmak için Araçlar**NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu** **Package Manager Console** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="af0fd-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="af0fd-130">PMC 'de Proje köküne gidin ve [Entity Framework (EF) Core](/ef/core) `Update-Database` komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="af0fd-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="af0fd-131">ASP.NET Core 2,0, Identity kimlik doğrulama verilerini depolayan veritabanıyla etkileşime geçmek için EF Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="af0fd-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="af0fd-132">Yeni oluşturulan uygulamanın çalışması için, bu verileri depolamak üzere bir veritabanı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="af0fd-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="af0fd-133">Yeni bir uygulama oluşturduktan sonra, bir veritabanı ortamında şemayı incelemenize en hızlı yol, [EF Core geçişlerini](/ef/core/managing-schemas/migrations/)kullanarak veritabanını oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="af0fd-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="af0fd-134">Bu işlem, yerel olarak veya başka bir yerde, bu şemayı taklit eden bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="af0fd-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="af0fd-135">Daha fazla bilgi için önceki belgeleri gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="af0fd-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="af0fd-136">EF Core komutları *üzerindeappsettings.js*belirtilen veritabanı için bağlantı dizesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="af0fd-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="af0fd-137">Aşağıdaki bağlantı dizesi, *ASP-NET-Core-Identity*adlı *localhost* üzerinde bir veritabanını hedefler.</span><span class="sxs-lookup"><span data-stu-id="af0fd-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="af0fd-138">Bu ayarda, EF Core bağlantı dizesini kullanacak şekilde yapılandırılır `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="af0fd-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="af0fd-139">SQL Server Nesne Gezgini **görüntüle**' yi seçin  >  **SQL Server Object Explorer**.</span><span class="sxs-lookup"><span data-stu-id="af0fd-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="af0fd-140">`ConnectionStrings:DefaultConnection` *appsettings.json*özelliğinde belirtilen veritabanı adına karşılık gelen düğümü genişletin.</span><span class="sxs-lookup"><span data-stu-id="af0fd-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="af0fd-141">`Update-Database`Komut, şema ile belirtilen veritabanını ve uygulama başlatma için gereken tüm verileri oluşturdu.</span><span class="sxs-lookup"><span data-stu-id="af0fd-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="af0fd-142">Aşağıdaki görüntüde, önceki adımlarla oluşturulan tablo yapısı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="af0fd-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    <span data-ttu-id="af0fd-143">![IdentityTakvimleri](identity/_static/identity-tables.png)</span><span class="sxs-lookup"><span data-stu-id="af0fd-143">![Identity Tables](identity/_static/identity-tables.png)</span></span>

## <a name="migrate-the-schema"></a><span data-ttu-id="af0fd-144">Şemayı geçirme</span><span class="sxs-lookup"><span data-stu-id="af0fd-144">Migrate the schema</span></span>

<span data-ttu-id="af0fd-145">Hem üyelik hem de ASP.NET Core için tablo yapılarında ve alanlarında hafif farklar vardır Identity .</span><span class="sxs-lookup"><span data-stu-id="af0fd-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="af0fd-146">Bu model, ASP.NET ve ASP.NET Core uygulamalarıyla kimlik doğrulama/yetkilendirme için önemli ölçüde değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="af0fd-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="af0fd-147">İle hala kullanılan temel nesneler Identity *Kullanıcılar* ve *rollerdir*.</span><span class="sxs-lookup"><span data-stu-id="af0fd-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="af0fd-148">*Kullanıcılar*, *Roller*ve Kullanıcı *rolleri*için eşleme tabloları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="af0fd-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="af0fd-149">Kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="af0fd-149">Users</span></span>

|Identity<br><span data-ttu-id="af0fd-150">( `dbo.AspNetUsers` ) sütunu</span><span class="sxs-lookup"><span data-stu-id="af0fd-150">(`dbo.AspNetUsers`) column</span></span>  |<span data-ttu-id="af0fd-151">Tür</span><span class="sxs-lookup"><span data-stu-id="af0fd-151">Type</span></span>     |<span data-ttu-id="af0fd-152">Üyelik</span><span class="sxs-lookup"><span data-stu-id="af0fd-152">Membership</span></span><br><span data-ttu-id="af0fd-153">( `dbo.aspnet_Users`  /  `dbo.aspnet_Membership` ) sütunu</span><span class="sxs-lookup"><span data-stu-id="af0fd-153">(`dbo.aspnet_Users` / `dbo.aspnet_Membership`) column</span></span>|<span data-ttu-id="af0fd-154">Tür</span><span class="sxs-lookup"><span data-stu-id="af0fd-154">Type</span></span>      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> <span data-ttu-id="af0fd-155">Alan eşlemelerinin hepsi, ASP.NET Core üyeliğinden bire bir ilişkiye benzemez Identity .</span><span class="sxs-lookup"><span data-stu-id="af0fd-155">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="af0fd-156">Yukarıdaki tablo, varsayılan üyelik kullanıcı şemasını alır ve ASP.NET Core şeması ile eşler Identity .</span><span class="sxs-lookup"><span data-stu-id="af0fd-156">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="af0fd-157">Üyelik için kullanılan diğer özel alanların el ile eşlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="af0fd-157">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="af0fd-158">Bu eşlemede, parola ölçütü ve parola salları iki arasında geçiş yapmadan, parola için bir eşleme yoktur.</span><span class="sxs-lookup"><span data-stu-id="af0fd-158">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="af0fd-159">**Parolayı null olarak bırakmanız ve kullanıcılardan parolalarını sıfırlamalarını istemek için önerilir.**</span><span class="sxs-lookup"><span data-stu-id="af0fd-159">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="af0fd-160">ASP.NET Core Identity , `LockoutEnd` Kullanıcı kilitli ise gelecekte bir tarih olarak ayarlanmalıdır. Bu, geçiş komut dosyasında gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="af0fd-160">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="af0fd-161">Roller</span><span class="sxs-lookup"><span data-stu-id="af0fd-161">Roles</span></span>

|Identity<br><span data-ttu-id="af0fd-162">( `dbo.AspNetRoles` ) sütunu</span><span class="sxs-lookup"><span data-stu-id="af0fd-162">(`dbo.AspNetRoles`) column</span></span>|<span data-ttu-id="af0fd-163">Tür</span><span class="sxs-lookup"><span data-stu-id="af0fd-163">Type</span></span>|<span data-ttu-id="af0fd-164">Üyelik</span><span class="sxs-lookup"><span data-stu-id="af0fd-164">Membership</span></span><br><span data-ttu-id="af0fd-165">( `dbo.aspnet_Roles` ) sütunu</span><span class="sxs-lookup"><span data-stu-id="af0fd-165">(`dbo.aspnet_Roles`) column</span></span>|<span data-ttu-id="af0fd-166">Tür</span><span class="sxs-lookup"><span data-stu-id="af0fd-166">Type</span></span>|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a><span data-ttu-id="af0fd-167">Kullanıcı Rolleri</span><span class="sxs-lookup"><span data-stu-id="af0fd-167">User Roles</span></span>

|Identity<br><span data-ttu-id="af0fd-168">( `dbo.AspNetUserRoles` ) sütunu</span><span class="sxs-lookup"><span data-stu-id="af0fd-168">(`dbo.AspNetUserRoles`) column</span></span>|<span data-ttu-id="af0fd-169">Tür</span><span class="sxs-lookup"><span data-stu-id="af0fd-169">Type</span></span>|<span data-ttu-id="af0fd-170">Üyelik</span><span class="sxs-lookup"><span data-stu-id="af0fd-170">Membership</span></span><br><span data-ttu-id="af0fd-171">( `dbo.aspnet_UsersInRoles` ) sütunu</span><span class="sxs-lookup"><span data-stu-id="af0fd-171">(`dbo.aspnet_UsersInRoles`) column</span></span>|<span data-ttu-id="af0fd-172">Tür</span><span class="sxs-lookup"><span data-stu-id="af0fd-172">Type</span></span>|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

<span data-ttu-id="af0fd-173">*Kullanıcılar* ve *Roller*için bir geçiş betiği oluştururken önceki eşleme tablolarına başvurun.</span><span class="sxs-lookup"><span data-stu-id="af0fd-173">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="af0fd-174">Aşağıdaki örnek, bir veritabanı sunucusunda iki veritabanınız olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="af0fd-174">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="af0fd-175">Bir veritabanı var olan ASP.NET üyelik şemasını ve verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="af0fd-175">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="af0fd-176">Diğer *Coreıdentitysample* veritabanı, daha önce açıklanan adımlar kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="af0fd-176">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="af0fd-177">Daha ayrıntılı bilgi için açıklamalar satır içi olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="af0fd-177">Comments are included inline for more details.</span></span>

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

<span data-ttu-id="af0fd-178">Önceki betiği tamamladıktan sonra, Identity daha önce oluşturulan ASP.NET Core uygulaması, üyelik kullanıcıları ile doldurulur.</span><span class="sxs-lookup"><span data-stu-id="af0fd-178">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="af0fd-179">Kullanıcıların oturum açmadan önce parolalarını değiştirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="af0fd-179">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="af0fd-180">Üyelik sisteminde kullanıcılara, e-posta adresiyle eşleşmeyen Kullanıcı adları varsa, bu, daha önce oluşturulan uygulamanın buna uyum sağlaması için değişiklikler yapmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="af0fd-180">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="af0fd-181">Varsayılan şablon `UserName` `Email` , ve için aynı olmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="af0fd-181">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="af0fd-182">Farklı oldukları durumlar için, oturum açma işleminin yerine kullanılacak şekilde değiştirilmesi gerekir `UserName` `Email` .</span><span class="sxs-lookup"><span data-stu-id="af0fd-182">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="af0fd-183">`PageModel` *Pages\Account\Login.cshtml.cs*adresinde bulunan oturum açma sayfasında, `[EmailAddress]` *e-posta* özelliğinden özniteliği kaldırın.</span><span class="sxs-lookup"><span data-stu-id="af0fd-183">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="af0fd-184">*Kullanıcı adı*olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="af0fd-184">Rename it to *UserName*.</span></span> <span data-ttu-id="af0fd-185">Bu, `EmailAddress` *Görünüm* ve *pagemodel*içinde bahsedilen her yerde bir değişiklik yapılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="af0fd-185">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="af0fd-186">Sonuç aşağıdakine benzer:</span><span class="sxs-lookup"><span data-stu-id="af0fd-186">The result looks like the following:</span></span>

 ![Sabit oturum açma](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="af0fd-188">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="af0fd-188">Next steps</span></span>

<span data-ttu-id="af0fd-189">Bu öğreticide, SQL üyeliğinden ASP.NET Core 2,0 ' ye kadar olan kullanıcıların bağlantı noktası oluşturmayı öğrendiniz Identity .</span><span class="sxs-lookup"><span data-stu-id="af0fd-189">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="af0fd-190">ASP.NET Core hakkında daha fazla bilgi için Identity bkz. [giriş Identity ](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="af0fd-190">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
