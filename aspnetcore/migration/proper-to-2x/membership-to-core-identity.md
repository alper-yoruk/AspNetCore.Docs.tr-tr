---
title: ASP.NET üyelik kimlik doğrulamasından ASP.NET Core 2,0 ' ye geçişIdentity
author: isaac2004
description: ASP.NET Core 2,0 Identity' ye üyelik kimlik doğrulaması kullanarak mevcut ASP.NET uygulamalarını geçirmeyi öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: b5205ef69943f3744bba8381701008369dd0843c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774515"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="05bb0-103">ASP.NET üyelik kimlik doğrulamasından ASP.NET Core 2,0 kimliği 'ne geçiş</span><span class="sxs-lookup"><span data-stu-id="05bb0-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="05bb0-104">İle [Isaac Levi](https://isaaclevin.com) tarafından</span><span class="sxs-lookup"><span data-stu-id="05bb0-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="05bb0-105">Bu makalede üyelik kimlik doğrulamasını kullanarak ASP.NET uygulamaları için veritabanı şemasının ASP.NET Core 2,0 kimliğine geçirilmesi gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="05bb0-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="05bb0-106">Bu belge, ASP.NET üyelik tabanlı uygulamalar için veritabanı şemasını ASP.NET Core kimliği için kullanılan veritabanı şemasına geçirmek için gereken adımları sağlar.</span><span class="sxs-lookup"><span data-stu-id="05bb0-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="05bb0-107">ASP.NET üyelik tabanlı kimlik doğrulamasından ASP.NET Identity geçirme hakkında daha fazla bilgi için bkz. [SQL üyeliğinden mevcut bir uygulamayı ASP.NET Identity geçirme](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="05bb0-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="05bb0-108">ASP.NET Core kimliği hakkında daha fazla bilgi için bkz. [ASP.NET Core kimliğe giriş](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="05bb0-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="05bb0-109">Üyelik şemasının incelenmesi</span><span class="sxs-lookup"><span data-stu-id="05bb0-109">Review of Membership schema</span></span>

<span data-ttu-id="05bb0-110">ASP.NET 2,0 ' den önce, geliştiriciler, uygulamaları için tüm kimlik doğrulama ve yetkilendirme sürecini oluşturmaya eklendi.</span><span class="sxs-lookup"><span data-stu-id="05bb0-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="05bb0-111">ASP.NET 2,0 ile üyelik tanıtılmıştı ve ASP.NET Apps içindeki güvenliği işlemek için ortak bir çözüm sağlar.</span><span class="sxs-lookup"><span data-stu-id="05bb0-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="05bb0-112">Geliştiriciler, bir şemayı, [aspnet_regsql. exe](https://msdn.microsoft.com/library/ms229862.aspx) komutuyla bir SQL Server veritabanına önyükleyebiliyor.</span><span class="sxs-lookup"><span data-stu-id="05bb0-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="05bb0-113">Bu komutu çalıştırdıktan sonra, veritabanında aşağıdaki tablolar oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="05bb0-113">After running this command, the following tables were created in the database.</span></span>

  ![Üyelik tabloları](identity/_static/membership-tables.png)

<span data-ttu-id="05bb0-115">Mevcut uygulamaları ASP.NET Core 2,0 kimliğine geçirmek için, bu tablolardaki verilerin yeni kimlik şeması tarafından kullanılan tablolara geçirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="05bb0-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="05bb0-116">ASP.NET Core Identity 2,0 şeması</span><span class="sxs-lookup"><span data-stu-id="05bb0-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="05bb0-117">ASP.NET Core 2,0, ASP.NET 4,5 ' de tanıtılan [kimlik](/aspnet/identity/index) ilkesini izler.</span><span class="sxs-lookup"><span data-stu-id="05bb0-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="05bb0-118">İlke paylaşılsa da, çerçeveler arasındaki uygulama, ASP.NET Core sürümleri arasında bile farklıdır (bkz. [kimlik doğrulama ve kimliği ASP.NET Core 2,0 ' e geçirme](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="05bb0-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="05bb0-119">ASP.NET Core 2,0 kimliği için şemayı görüntülemenin en hızlı yolu, yeni bir ASP.NET Core 2,0 uygulaması oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="05bb0-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="05bb0-120">Visual Studio 2017 'de şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="05bb0-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="05bb0-121">**Dosya** > **New**yeni > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="05bb0-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="05bb0-122">*Coreıdentitysample*adlı yeni bir **ASP.NET Core Web uygulaması** projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="05bb0-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="05bb0-123">Açılan listede **ASP.NET Core 2,0** ' i seçin ve ardından **Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="05bb0-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="05bb0-124">Bu şablon bir [Razor Pages](xref:razor-pages/index) uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="05bb0-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="05bb0-125">**Tamam**' a tıklamadan önce **kimlik doğrulamasını Değiştir**' e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="05bb0-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="05bb0-126">Kimlik şablonları için **bireysel kullanıcı hesapları** seçin.</span><span class="sxs-lookup"><span data-stu-id="05bb0-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="05bb0-127">Son olarak **Tamam**' a ve ardından **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="05bb0-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="05bb0-128">Visual Studio ASP.NET Core Identity şablonunu kullanarak bir proje oluşturur.</span><span class="sxs-lookup"><span data-stu-id="05bb0-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="05bb0-129">**Paket Yöneticisi konsolu** (PMC) penceresini açmak için **Araçlar** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="05bb0-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="05bb0-130">PMC 'de Proje köküne gidin ve [Entity Framework (EF) Core](/ef/core) `Update-Database` komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="05bb0-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="05bb0-131">ASP.NET Core 2,0 kimliği, kimlik doğrulama verilerini depolayan veritabanıyla etkileşime geçmek için EF Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="05bb0-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="05bb0-132">Yeni oluşturulan uygulamanın çalışması için, bu verileri depolamak üzere bir veritabanı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="05bb0-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="05bb0-133">Yeni bir uygulama oluşturduktan sonra, bir veritabanı ortamında şemayı incelemenize en hızlı yol, [EF Core geçişlerini](/ef/core/managing-schemas/migrations/)kullanarak veritabanını oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="05bb0-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="05bb0-134">Bu işlem, yerel olarak veya başka bir yerde, bu şemayı taklit eden bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="05bb0-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="05bb0-135">Daha fazla bilgi için önceki belgeleri gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="05bb0-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="05bb0-136">EF Core komutları *appSettings. JSON*içinde belirtilen veritabanı için bağlantı dizesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="05bb0-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="05bb0-137">Aşağıdaki bağlantı dizesi, *ASP-NET-Core-Identity*adlı *localhost* üzerinde bir veritabanını hedefler.</span><span class="sxs-lookup"><span data-stu-id="05bb0-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="05bb0-138">Bu ayarda, EF Core `DefaultConnection` bağlantı dizesini kullanacak şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="05bb0-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="05bb0-139">SQL Server Nesne Gezgini **görüntüle** > **SQL Server Object Explorer**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="05bb0-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="05bb0-140">`ConnectionStrings:DefaultConnection` *AppSettings. JSON*özelliğinde belirtilen veritabanı adına karşılık gelen düğümü genişletin.</span><span class="sxs-lookup"><span data-stu-id="05bb0-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="05bb0-141">`Update-Database` Komut, şema ile belirtilen veritabanını ve uygulama başlatma için gereken tüm verileri oluşturdu.</span><span class="sxs-lookup"><span data-stu-id="05bb0-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="05bb0-142">Aşağıdaki görüntüde, önceki adımlarla oluşturulan tablo yapısı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="05bb0-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![Kimlik tabloları](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="05bb0-144">Şemayı geçirme</span><span class="sxs-lookup"><span data-stu-id="05bb0-144">Migrate the schema</span></span>

<span data-ttu-id="05bb0-145">Hem üyelik hem de ASP.NET Core kimlik için tablo yapılarında ve alanlarında hafif farklar vardır.</span><span class="sxs-lookup"><span data-stu-id="05bb0-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="05bb0-146">Bu model, ASP.NET ve ASP.NET Core uygulamalarıyla kimlik doğrulama/yetkilendirme için önemli ölçüde değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="05bb0-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="05bb0-147">Kimlik ile hala kullanılan önemli nesneler, *Kullanıcılar* ve *rollerdir*.</span><span class="sxs-lookup"><span data-stu-id="05bb0-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="05bb0-148">*Kullanıcılar*, *Roller*ve Kullanıcı *rolleri*için eşleme tabloları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="05bb0-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="05bb0-149">Kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="05bb0-149">Users</span></span>

|<span data-ttu-id="05bb0-150">*Identity<br>(dbo. AspNetUsers)*</span><span class="sxs-lookup"><span data-stu-id="05bb0-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="05bb0-151">*Üyelik<br>(dbo. aspnet_Users/dbo. aspnet_Membership)*</span><span class="sxs-lookup"><span data-stu-id="05bb0-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="05bb0-152">**Alan adı**</span><span class="sxs-lookup"><span data-stu-id="05bb0-152">**Field Name**</span></span>                 |<span data-ttu-id="05bb0-153">**Tür**</span><span class="sxs-lookup"><span data-stu-id="05bb0-153">**Type**</span></span>|<span data-ttu-id="05bb0-154">**Alan adı**</span><span class="sxs-lookup"><span data-stu-id="05bb0-154">**Field Name**</span></span>                                    |<span data-ttu-id="05bb0-155">**Tür**</span><span class="sxs-lookup"><span data-stu-id="05bb0-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="05bb0-156">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="05bb0-157">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="05bb0-158">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="05bb0-159">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="05bb0-160">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="05bb0-161">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="05bb0-162">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="05bb0-163">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="05bb0-164">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="05bb0-165">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="05bb0-166">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="05bb0-167">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="05bb0-168">bit</span><span class="sxs-lookup"><span data-stu-id="05bb0-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="05bb0-169">bit</span><span class="sxs-lookup"><span data-stu-id="05bb0-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="05bb0-170">Alan eşlemelerinin hepsi, ASP.NET Core kimlik üyeliğinden bire bir ilişkiye benzemez.</span><span class="sxs-lookup"><span data-stu-id="05bb0-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="05bb0-171">Yukarıdaki tablo, varsayılan üyelik kullanıcı şemasını alır ve ASP.NET Core Identity şeması ile eşler.</span><span class="sxs-lookup"><span data-stu-id="05bb0-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="05bb0-172">Üyelik için kullanılan diğer özel alanların el ile eşlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="05bb0-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="05bb0-173">Bu eşlemede, parola ölçütü ve parola salları iki arasında geçiş yapmadan, parola için bir eşleme yoktur.</span><span class="sxs-lookup"><span data-stu-id="05bb0-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="05bb0-174">**Parolayı null olarak bırakmanız ve kullanıcılardan parolalarını sıfırlamalarını istemek için önerilir.**</span><span class="sxs-lookup"><span data-stu-id="05bb0-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="05bb0-175">Kullanıcı kilitlenmişse, `LockoutEnd` ASP.NET Core kimlik ' de gelecekte bir tarih olarak ayarlanmalıdır. Bu, geçiş komut dosyasında gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="05bb0-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="05bb0-176">Roller</span><span class="sxs-lookup"><span data-stu-id="05bb0-176">Roles</span></span>

|<span data-ttu-id="05bb0-177">*Identity<br>(dbo. AspNetRoles)*</span><span class="sxs-lookup"><span data-stu-id="05bb0-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="05bb0-178">*Üyelik<br>(dbo. aspnet_Roles)*</span><span class="sxs-lookup"><span data-stu-id="05bb0-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="05bb0-179">**Alan adı**</span><span class="sxs-lookup"><span data-stu-id="05bb0-179">**Field Name**</span></span>                 |<span data-ttu-id="05bb0-180">**Tür**</span><span class="sxs-lookup"><span data-stu-id="05bb0-180">**Type**</span></span>|<span data-ttu-id="05bb0-181">**Alan adı**</span><span class="sxs-lookup"><span data-stu-id="05bb0-181">**Field Name**</span></span>   |<span data-ttu-id="05bb0-182">**Tür**</span><span class="sxs-lookup"><span data-stu-id="05bb0-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="05bb0-183">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-183">string</span></span>  |`RoleId`         | <span data-ttu-id="05bb0-184">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="05bb0-185">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-185">string</span></span>  |`RoleName`       | <span data-ttu-id="05bb0-186">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="05bb0-187">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="05bb0-188">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="05bb0-189">Kullanıcı Rolleri</span><span class="sxs-lookup"><span data-stu-id="05bb0-189">User Roles</span></span>

|<span data-ttu-id="05bb0-190">*Identity<br>(dbo. AspNetUserRoles)*</span><span class="sxs-lookup"><span data-stu-id="05bb0-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="05bb0-191">*Üyelik<br>(dbo. aspnet_UsersInRoles)*</span><span class="sxs-lookup"><span data-stu-id="05bb0-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="05bb0-192">**Alan adı**</span><span class="sxs-lookup"><span data-stu-id="05bb0-192">**Field Name**</span></span>           |<span data-ttu-id="05bb0-193">**Tür**</span><span class="sxs-lookup"><span data-stu-id="05bb0-193">**Type**</span></span>  |<span data-ttu-id="05bb0-194">**Alan adı**</span><span class="sxs-lookup"><span data-stu-id="05bb0-194">**Field Name**</span></span>|<span data-ttu-id="05bb0-195">**Tür**</span><span class="sxs-lookup"><span data-stu-id="05bb0-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="05bb0-196">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-196">string</span></span>    |`RoleId`      |<span data-ttu-id="05bb0-197">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="05bb0-198">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-198">string</span></span>    |`UserId`      |<span data-ttu-id="05bb0-199">string</span><span class="sxs-lookup"><span data-stu-id="05bb0-199">string</span></span>                     |

<span data-ttu-id="05bb0-200">*Kullanıcılar* ve *Roller*için bir geçiş betiği oluştururken önceki eşleme tablolarına başvurun.</span><span class="sxs-lookup"><span data-stu-id="05bb0-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="05bb0-201">Aşağıdaki örnek, bir veritabanı sunucusunda iki veritabanınız olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="05bb0-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="05bb0-202">Bir veritabanı var olan ASP.NET üyelik şemasını ve verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="05bb0-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="05bb0-203">Diğer *Coreıdentitysample* veritabanı, daha önce açıklanan adımlar kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="05bb0-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="05bb0-204">Daha ayrıntılı bilgi için açıklamalar satır içi olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="05bb0-204">Comments are included inline for more details.</span></span>

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

<span data-ttu-id="05bb0-205">Önceki betiği tamamladıktan sonra, daha önce oluşturulan ASP.NET Core Identity uygulaması, üyelik kullanıcıları ile doldurulur.</span><span class="sxs-lookup"><span data-stu-id="05bb0-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="05bb0-206">Kullanıcıların oturum açmadan önce parolalarını değiştirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="05bb0-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="05bb0-207">Üyelik sisteminde kullanıcılara, e-posta adresiyle eşleşmeyen Kullanıcı adları varsa, bu, daha önce oluşturulan uygulamanın buna uyum sağlaması için değişiklikler yapmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="05bb0-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="05bb0-208">Varsayılan şablon, ve `UserName` `Email` için aynı olmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="05bb0-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="05bb0-209">Farklı oldukları durumlar için, oturum açma işleminin yerine kullanılacak `UserName` şekilde değiştirilmesi gerekir. `Email`</span><span class="sxs-lookup"><span data-stu-id="05bb0-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="05bb0-210">`PageModel` *Pages\Account\Login.cshtml.cs*adresinde bulunan oturum açma sayfasında, `[EmailAddress]` *e-posta* özelliğinden özniteliği kaldırın.</span><span class="sxs-lookup"><span data-stu-id="05bb0-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="05bb0-211">*Kullanıcı adı*olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="05bb0-211">Rename it to *UserName*.</span></span> <span data-ttu-id="05bb0-212">Bu, `EmailAddress` *Görünüm* ve *pagemodel*içinde bahsedilen her yerde bir değişiklik yapılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="05bb0-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="05bb0-213">Sonuç aşağıdakine benzer:</span><span class="sxs-lookup"><span data-stu-id="05bb0-213">The result looks like the following:</span></span>

 ![Sabit oturum açma](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="05bb0-215">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="05bb0-215">Next steps</span></span>

<span data-ttu-id="05bb0-216">Bu öğreticide, SQL üyeliğinden ASP.NET Core 2,0 Identity' ye kadar olan kullanıcıların bağlantı noktası oluşturmayı öğrendiniz.</span><span class="sxs-lookup"><span data-stu-id="05bb0-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="05bb0-217">ASP.NET Core Identityhakkında daha fazla bilgi için bkz. [giriş Identity ](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="05bb0-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
