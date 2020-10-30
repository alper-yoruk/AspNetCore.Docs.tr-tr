---
title: ':::no-loc(Identity)::: ASP.NET Core model özelleştirmesi'
author: ajcvickers
description: 'Bu makalede, için temel alınan Entity Framework Core veri modelinin nasıl özelleştirileceği açıklanır :::no-loc(ASP.NET Core Identity)::: .'
ms.author: avickers
ms.date: 07/01/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 6e520c76a3377e889166ca8d08b75754ef34b6a1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052051"
---
# <a name="no-locidentity-model-customization-in-aspnet-core"></a><span data-ttu-id="5a7d4-103">:::no-loc(Identity)::: ASP.NET Core model özelleştirmesi</span><span class="sxs-lookup"><span data-stu-id="5a7d4-103">:::no-loc(Identity)::: model customization in ASP.NET Core</span></span>

<span data-ttu-id="5a7d4-104">[Arthur Vicranlar](https://github.com/ajcvickers) tarafından</span><span class="sxs-lookup"><span data-stu-id="5a7d4-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="5a7d4-105">:::no-loc(ASP.NET Core Identity)::: ASP.NET Core uygulamalarda Kullanıcı hesaplarını yönetmek ve depolamak için bir çerçeve sağlar.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-105">:::no-loc(ASP.NET Core Identity)::: provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="5a7d4-106">:::no-loc(Identity)::: , kimlik doğrulama mekanizması olarak **bireysel kullanıcı hesapları** seçildiğinde projenize eklenir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-106">:::no-loc(Identity)::: is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="5a7d4-107">Varsayılan olarak, :::no-loc(Identity)::: Entity Framework (EF) temel veri modelini kullanır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-107">By default, :::no-loc(Identity)::: makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="5a7d4-108">Bu makalede, modelin nasıl özelleştirileceği açıklanır :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-108">This article describes how to customize the :::no-loc(Identity)::: model.</span></span>

## <a name="no-locidentity-and-ef-core-migrations"></a><span data-ttu-id="5a7d4-109">:::no-loc(Identity)::: ve EF Core geçişleri</span><span class="sxs-lookup"><span data-stu-id="5a7d4-109">:::no-loc(Identity)::: and EF Core Migrations</span></span>

<span data-ttu-id="5a7d4-110">Modeli incelemeden önce, :::no-loc(Identity)::: bir veritabanı oluşturmak ve güncelleştirmek için [EF Core geçişlerle](/ef/core/managing-schemas/migrations/) nasıl çalıştığını anlamak yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-110">Before examining the model, it's useful to understand how :::no-loc(Identity)::: works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="5a7d4-111">En üst düzeyde, işlem şu şekilde yapılır:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="5a7d4-112">[Kodda bir veri modeli](/ef/core/modeling/)tanımlayın veya güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="5a7d4-113">Bu modeli veritabanına uygulanabilecek değişikliklere dönüştürmek için bir geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="5a7d4-114">Geçişin, amaclarınızı doğru şekilde temsil ettiğini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="5a7d4-115">Veritabanını modeliyle eşitlenmiş olacak şekilde güncelleştirmek için geçişi uygulayın.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="5a7d4-116">Modeli daha belirginleştirmek ve veritabanını eşitlenmiş halde tutmak için 1 ile 4 arasındaki adımları tekrarlayın.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="5a7d4-117">Geçişleri eklemek ve uygulamak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="5a7d4-118">Visual Studio kullanıyorsanız **Paket Yöneticisi konsolu** (PMC) penceresi.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="5a7d4-119">Daha fazla bilgi için bkz. [EF Core PMC araçları](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="5a7d4-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="5a7d4-120">Komut satırı kullanılıyorsa .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="5a7d4-121">Daha fazla bilgi için bkz. [.NET komut satırı araçları EF Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="5a7d4-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="5a7d4-122">Uygulama çalıştırıldığında hata sayfasındaki **geçişleri Uygula** düğmesine tıklanın.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="5a7d4-123">ASP.NET Core bir geliştirme zamanı hata sayfası işleyicisine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="5a7d4-124">İşleyici, uygulama çalıştırıldığında geçişleri uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="5a7d4-125">Üretim uygulamaları tipik olarak geçişlerden SQL betikleri oluşturur ve veritabanı değişiklikleri denetimli bir uygulama ve veritabanı dağıtımının bir parçası olarak dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="5a7d4-126">Kullanılarak yeni bir uygulama :::no-loc(Identity)::: oluşturulduğunda, yukarıdaki 1. ve 2. adım zaten tamamlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-126">When a new app using :::no-loc(Identity)::: is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="5a7d4-127">Diğer bir deyişle, ilk veri modeli zaten var ve ilk geçiş projeye eklendi.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="5a7d4-128">İlk geçişin hala veritabanına uygulanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="5a7d4-129">İlk geçiş aşağıdaki yaklaşımlardan biri aracılığıyla uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="5a7d4-130">`Update-Database`PMC 'de çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="5a7d4-131">`dotnet ef database update`Komut kabuğu 'nda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="5a7d4-132">Uygulama çalıştırıldığında hata sayfasında **geçişleri Uygula** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="5a7d4-133">Modelde değişiklikler yapıldığından önceki adımları yineleyin.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-no-locidentity-model"></a><span data-ttu-id="5a7d4-134">:::no-loc(Identity):::Model</span><span class="sxs-lookup"><span data-stu-id="5a7d4-134">The :::no-loc(Identity)::: model</span></span>

### <a name="entity-types"></a><span data-ttu-id="5a7d4-135">Varlık türleri</span><span class="sxs-lookup"><span data-stu-id="5a7d4-135">Entity types</span></span>

<span data-ttu-id="5a7d4-136">:::no-loc(Identity):::Model aşağıdaki varlık türlerinden oluşur.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-136">The :::no-loc(Identity)::: model consists of the following entity types.</span></span>

|<span data-ttu-id="5a7d4-137">Varlık türü</span><span class="sxs-lookup"><span data-stu-id="5a7d4-137">Entity type</span></span>|<span data-ttu-id="5a7d4-138">Açıklama</span><span class="sxs-lookup"><span data-stu-id="5a7d4-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="5a7d4-139">Kullanıcıyı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="5a7d4-140">Bir rolü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="5a7d4-141">Bir kullanıcının sahip olduğu talebi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="5a7d4-142">Bir kullanıcı için kimlik doğrulama belirtecini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="5a7d4-143">Kullanıcıyı bir oturum ile ilişkilendirir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="5a7d4-144">Bir rol içindeki tüm kullanıcılara verilen bir talebi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="5a7d4-145">Kullanıcıları ve rolleri ilişkilendiren bir JOIN varlığı.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="5a7d4-146">Varlık türü ilişkileri</span><span class="sxs-lookup"><span data-stu-id="5a7d4-146">Entity type relationships</span></span>

<span data-ttu-id="5a7d4-147">[Varlık türleri](#entity-types) , aşağıdaki yollarla birbirleriyle ilişkilidir:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="5a7d4-148">Her birinin `User` çok sayıda olabilir `UserClaims` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="5a7d4-149">Her birinin `User` çok sayıda olabilir `UserLogins` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="5a7d4-150">Her birinin `User` çok sayıda olabilir `UserTokens` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="5a7d4-151">Her birinin `Role` birden çok ilişkili olabilir `RoleClaims` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="5a7d4-152">Her biri `User` birden çok ilişkili olabilir `Roles` ve her biri `Role` birçok ile ilişkilendirilebilir `Users` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="5a7d4-153">Bu, veritabanında bir JOIN tablosu gerektiren çoktan çoğa bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="5a7d4-154">JOIN tablosu varlıkla temsil edilir `UserRole` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="5a7d4-155">Varsayılan model yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5a7d4-155">Default model configuration</span></span>

<span data-ttu-id="5a7d4-156">:::no-loc(Identity):::modeli yapılandırmak ve kullanmak için [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 'ten devraldığı birçok *bağlam sınıfını* tanımlar.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-156">:::no-loc(Identity)::: defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="5a7d4-157">Bu yapılandırma, bağlam sınıfının [Onmodeloluþturma](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) yönteminde [EF Core Code First floent API 'si](/ef/core/modeling/) kullanılarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="5a7d4-158">Varsayılan yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-158">The default configuration is:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a><span data-ttu-id="5a7d4-159">Model genel türleri</span><span class="sxs-lookup"><span data-stu-id="5a7d4-159">Model generic types</span></span>

<span data-ttu-id="5a7d4-160">:::no-loc(Identity)::: Yukarıda listelenen her varlık türü için varsayılan [ortak dil çalışma zamanı](/dotnet/standard/glossary#clr) (CLR) türlerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-160">:::no-loc(Identity)::: defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="5a7d4-161">Bu türlerin öneki *:::no-loc(Identity):::* :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-161">These types are all prefixed with *:::no-loc(Identity):::* :</span></span>

* `:::no-loc(Identity):::User`
* `:::no-loc(Identity):::Role`
* `:::no-loc(Identity):::UserClaim`
* `:::no-loc(Identity):::UserToken`
* `:::no-loc(Identity):::UserLogin`
* `:::no-loc(Identity):::RoleClaim`
* `:::no-loc(Identity):::UserRole`

<span data-ttu-id="5a7d4-162">Bu türleri doğrudan kullanmak yerine, türler uygulamanın kendi türleri için temel sınıflar olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="5a7d4-163">`DbContext`Tarafından tanımlanan sınıflar :::no-loc(Identity)::: geneldir, örneğin, modeldeki bir veya daha fazla varlık türü IÇIN farklı clr türleri kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-163">The `DbContext` classes defined by :::no-loc(Identity)::: are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="5a7d4-164">Bu genel türler Ayrıca `User` birincil anahtar (PK) veri türünün değiştirilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="5a7d4-165">:::no-loc(Identity):::Rol desteğiyle birlikte kullanıldığında bir <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext> sınıf kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-165">When using :::no-loc(Identity)::: with support for roles, an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext> class should be used.</span></span> <span data-ttu-id="5a7d4-166">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-166">For example:</span></span>

```csharp
// Uses all the built-in :::no-loc(Identity)::: types
// Uses `string` as the key type
public class :::no-loc(Identity):::DbContext
    : :::no-loc(Identity):::DbContext<:::no-loc(Identity):::User, :::no-loc(Identity):::Role, string>
{
}

// Uses the built-in :::no-loc(Identity)::: types except with a custom User type
// Uses `string` as the key type
public class :::no-loc(Identity):::DbContext<TUser>
    : :::no-loc(Identity):::DbContext<TUser, :::no-loc(Identity):::Role, string>
        where TUser : :::no-loc(Identity):::User
{
}

// Uses the built-in :::no-loc(Identity)::: types except with custom User and Role types
// The key type is defined by TKey
public class :::no-loc(Identity):::DbContext<TUser, TRole, TKey> : :::no-loc(Identity):::DbContext<
    TUser, TRole, TKey, :::no-loc(Identity):::UserClaim<TKey>, :::no-loc(Identity):::UserRole<TKey>,
    :::no-loc(Identity):::UserLogin<TKey>, :::no-loc(Identity):::RoleClaim<TKey>, :::no-loc(Identity):::UserToken<TKey>>
        where TUser : :::no-loc(Identity):::User<TKey>
        where TRole : :::no-loc(Identity):::Role<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in :::no-loc(Identity)::: types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class :::no-loc(Identity):::DbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : :::no-loc(Identity):::UserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : :::no-loc(Identity):::User<TKey>
         where TRole : :::no-loc(Identity):::Role<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : :::no-loc(Identity):::UserClaim<TKey>
         where TUserRole : :::no-loc(Identity):::UserRole<TKey>
         where TUserLogin : :::no-loc(Identity):::UserLogin<TKey>
         where TRoleClaim : :::no-loc(Identity):::RoleClaim<TKey>
         where TUserToken : :::no-loc(Identity):::UserToken<TKey>
```

<span data-ttu-id="5a7d4-167">Ayrıca :::no-loc(Identity)::: , bir <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::UserContext%601> sınıf kullanılması gereken roller (yalnızca talepler) olmadan da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-167">It's also possible to use :::no-loc(Identity)::: without roles (only claims), in which case an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::UserContext%601> class should be used:</span></span>

```csharp
// Uses the built-in non-role :::no-loc(Identity)::: types except with a custom User type
// Uses `string` as the key type
public class :::no-loc(Identity):::UserContext<TUser>
    : :::no-loc(Identity):::UserContext<TUser, string>
        where TUser : :::no-loc(Identity):::User
{
}

// Uses the built-in non-role :::no-loc(Identity)::: types except with a custom User type
// The key type is defined by TKey
public class :::no-loc(Identity):::UserContext<TUser, TKey> : :::no-loc(Identity):::UserContext<
    TUser, TKey, :::no-loc(Identity):::UserClaim<TKey>, :::no-loc(Identity):::UserLogin<TKey>,
    :::no-loc(Identity):::UserToken<TKey>>
        where TUser : :::no-loc(Identity):::User<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in :::no-loc(Identity)::: types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class :::no-loc(Identity):::UserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : :::no-loc(Identity):::User<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : :::no-loc(Identity):::UserClaim<TKey>
        where TUserLogin : :::no-loc(Identity):::UserLogin<TKey>
        where TUserToken : :::no-loc(Identity):::UserToken<TKey>
{
}
```

## <a name="customize-the-model"></a><span data-ttu-id="5a7d4-168">Modeli özelleştirme</span><span class="sxs-lookup"><span data-stu-id="5a7d4-168">Customize the model</span></span>

<span data-ttu-id="5a7d4-169">Model özelleştirmesi için başlangıç noktası uygun bağlam türünden türetilmelidir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="5a7d4-170">[Model genel türler](#model-generic-types) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="5a7d4-171">Bu bağlam türü geleneksel çağırılır `ApplicationDbContext` ve ASP.NET Core şablonları tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="5a7d4-172">Bağlam, modeli iki şekilde yapılandırmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="5a7d4-173">Genel tür parametreleri için varlık ve anahtar türleri sağlama.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="5a7d4-174">`OnModelCreating`Bu türlerin eşlemesini değiştirmek için geçersiz kılma.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="5a7d4-175">Geçersiz kıldığınızda `OnModelCreating` , `base.OnModelCreating` ilk olarak çağrılmalıdır; geçersiz kılma yapılandırması daha sonra çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="5a7d4-176">EF Core, yapılandırma için genellikle son bir WINS ilkesine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="5a7d4-177">Örneğin, bir `ToTable` varlık türünün yöntemi ilk olarak bir tablo adı ve daha sonra farklı bir tablo adıyla çağrılırsa ikinci çağrıda tablo adı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="5a7d4-178">Özel Kullanıcı verileri</span><span class="sxs-lookup"><span data-stu-id="5a7d4-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
 -->

<span data-ttu-id="5a7d4-179">[Özel Kullanıcı verileri](xref:security/authentication/add-user-data) , öğesinden devralınırken desteklenir `:::no-loc(Identity):::User` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `:::no-loc(Identity):::User`.</span></span> <span data-ttu-id="5a7d4-180">Bu tür için ad vermek önemlidir `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="5a7d4-181">`ApplicationUser`Türü bağlam için genel bir bağımsız değişken olarak kullanın:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : :::no-loc(Identity):::DbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

<span data-ttu-id="5a7d4-182">Sınıfında geçersiz kılınmasına gerek yoktur `OnModelCreating` `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="5a7d4-183">EF Core, `CustomTag` özelliği kuralına göre eşler.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="5a7d4-184">Ancak, veritabanının yeni bir sütun oluşturmak için güncelleştirilmesi gerekir `CustomTag` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="5a7d4-185">Sütunu oluşturmak için bir geçiş ekleyin ve sonra, [ :::no-loc(Identity)::: ve EF Core geçişleri](#identity-and-ef-core-migrations)içinde açıklandığı gibi veritabanını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-185">To create the column, add a migration, and then update the database as described in [:::no-loc(Identity)::: and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="5a7d4-186">*Sayfaları/paylaşılan/_LoginPartial. cshtml* 'yi güncelleştirin ve `:::no-loc(Identity):::User` ile değiştirin `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `:::no-loc(Identity):::User` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.:::no-loc(Identity):::
@using WebApp1.Areas.:::no-loc(Identity):::.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="5a7d4-187">*Alanı/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* veya `Startup.ConfigureServices` `:::no-loc(Identity):::User` ile değiştirin `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-187">Update *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*  or `Startup.ConfigureServices` and replace `:::no-loc(Identity):::User` with `ApplicationUser`.</span></span>

```csharp
services.Add:::no-loc(Identity):::<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="5a7d4-188">ASP.NET Core 2,1 veya sonraki sürümlerde :::no-loc(Identity)::: bir sınıf kitaplığı olarak sağlanır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-188">In ASP.NET Core 2.1 or later, :::no-loc(Identity)::: is provided as a :::no-loc(Razor)::: Class Library.</span></span> <span data-ttu-id="5a7d4-189">Daha fazla bilgi için bkz. <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="5a7d4-190">Sonuç olarak, yukarıdaki kod için bir çağrısı gerektirir <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="5a7d4-191">:::no-loc(Identity):::Projeye dosya eklemek için desteği kullanılmışsa :::no-loc(Identity)::: , çağrısını kaldırın `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-191">If the :::no-loc(Identity)::: scaffolder was used to add :::no-loc(Identity)::: files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="5a7d4-192">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-192">For more information, see:</span></span>

* [<span data-ttu-id="5a7d4-193">İskele :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="5a7d4-193">Scaffold :::no-loc(Identity):::</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="5a7d4-194">İçin özel kullanıcı verisi ekleme, indirme ve silme :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="5a7d4-194">Add, download, and delete custom user data to :::no-loc(Identity):::</span></span>](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a><span data-ttu-id="5a7d4-195">Birincil anahtar türünü değiştirme</span><span class="sxs-lookup"><span data-stu-id="5a7d4-195">Change the primary key type</span></span>

<span data-ttu-id="5a7d4-196">Veritabanı oluşturulduktan sonra PK sütununun veri türünün bir değişikliği birçok veritabanı sisteminde sorunlu olur.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="5a7d4-197">PK 'nin değiştirilmesi genellikle tabloyu bırakmayı ve yeniden oluşturmayı içerir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="5a7d4-198">Bu nedenle, veritabanı oluşturulduğunda ilk geçişte anahtar türleri belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="5a7d4-199">PK türünü değiştirmek için şu adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="5a7d4-200">Veritabanı PK değişikliğinden önce oluşturulduysa, `Drop-Database` dosyayı silmek için (PMC) veya `dotnet ef database drop` (.NET Core CLI) çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="5a7d4-201">Veritabanını silme işlemini onayladıktan sonra, `Remove-Migration` (PMC) veya `dotnet ef migrations remove` (.NET Core CLI) ile ilk geçişi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="5a7d4-202">`ApplicationDbContext`Türeten türetilmiş sınıfı güncelleştirin <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext%603> .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext%603>.</span></span> <span data-ttu-id="5a7d4-203">İçin yeni anahtar türünü belirtin `TKey` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="5a7d4-204">Örneğin, bir `Guid` anahtar türü kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-204">For example, to use a `Guid` key type:</span></span>

    ```csharp
    public class ApplicationDbContext
        : :::no-loc(Identity):::DbContext<:::no-loc(Identity):::User<Guid>, :::no-loc(Identity):::Role<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    <span data-ttu-id="5a7d4-205">Önceki kodda, genel sınıflar <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User%601> ve <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Role%601> Yeni anahtar türünü kullanmak için belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User%601> and <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Role%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="5a7d4-206">Önceki kodda, genel sınıflar <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::User%601> ve <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::Role%601> Yeni anahtar türünü kullanmak için belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::User%601> and <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::Role%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="5a7d4-207">`Startup.ConfigureServices` Genel kullanıcıyı kullanacak şekilde güncelleştirilmeleri gerekir:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefault:::no-loc(Identity):::<:::no-loc(Identity):::User<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.Add:::no-loc(Identity):::<:::no-loc(Identity):::User<Guid>, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.Add:::no-loc(Identity):::<:::no-loc(Identity):::User<Guid>, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. <span data-ttu-id="5a7d4-208">Özel bir `ApplicationUser` sınıf kullanılıyorsa, öğesinden devralacak olan sınıfı güncelleştirin `:::no-loc(Identity):::User` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `:::no-loc(Identity):::User`.</span></span> <span data-ttu-id="5a7d4-209">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/:::no-loc(Razor):::PagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="5a7d4-210">`ApplicationDbContext`Özel sınıfa başvurmak için Güncelleştir `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

    ```csharp
    public class ApplicationDbContext
        : :::no-loc(Identity):::DbContext<ApplicationUser, :::no-loc(Identity):::Role<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    <span data-ttu-id="5a7d4-211">Hizmeti eklerken özel veritabanı bağlam sınıfını Kaydet :::no-loc(Identity)::: `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-211">Register the custom database context class when adding the :::no-loc(Identity)::: service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.Add:::no-loc(Identity):::<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="5a7d4-212">Birincil anahtarın veri türü, [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) nesnesi analiz edilirken algılanır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="5a7d4-213">ASP.NET Core 2,1 veya sonraki sürümlerde :::no-loc(Identity)::: bir sınıf kitaplığı olarak sağlanır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-213">In ASP.NET Core 2.1 or later, :::no-loc(Identity)::: is provided as a :::no-loc(Razor)::: Class Library.</span></span> <span data-ttu-id="5a7d4-214">Daha fazla bilgi için bkz. <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="5a7d4-215">Sonuç olarak, yukarıdaki kod için bir çağrısı gerektirir <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="5a7d4-216">:::no-loc(Identity):::Projeye dosya eklemek için desteği kullanılmışsa :::no-loc(Identity)::: , çağrısını kaldırın `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-216">If the :::no-loc(Identity)::: scaffolder was used to add :::no-loc(Identity)::: files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="5a7d4-217">Birincil anahtarın veri türü, [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) nesnesi analiz edilirken algılanır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="5a7d4-218"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::EntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>Yöntemi, `TKey` birincil anahtarın veri türünü gösteren bir türü kabul eder.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-218">The <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::EntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="5a7d4-219">Özel bir `ApplicationRole` sınıf kullanılıyorsa, öğesinden devralacak olan sınıfı güncelleştirin `:::no-loc(Identity):::Role<TKey>` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `:::no-loc(Identity):::Role<TKey>`.</span></span> <span data-ttu-id="5a7d4-220">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/:::no-loc(Razor):::PagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="5a7d4-221">`ApplicationDbContext`Özel sınıfa başvurmak için güncelleştirin `ApplicationRole` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="5a7d4-222">Örneğin, aşağıdaki sınıf özel ve özel bir öğesine başvurur `ApplicationUser` `ApplicationRole` :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/:::no-loc(Razor):::PagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="5a7d4-223">Hizmeti eklerken özel veritabanı bağlam sınıfını Kaydet :::no-loc(Identity)::: `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-223">Register the custom database context class when adding the :::no-loc(Identity)::: service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/:::no-loc(Razor):::PagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="5a7d4-224">Birincil anahtarın veri türü, [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) nesnesi analiz edilirken algılanır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="5a7d4-225">ASP.NET Core 2,1 veya sonraki sürümlerde :::no-loc(Identity)::: bir sınıf kitaplığı olarak sağlanır :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-225">In ASP.NET Core 2.1 or later, :::no-loc(Identity)::: is provided as a :::no-loc(Razor)::: Class Library.</span></span> <span data-ttu-id="5a7d4-226">Daha fazla bilgi için bkz. <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="5a7d4-227">Sonuç olarak, yukarıdaki kod için bir çağrısı gerektirir <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="5a7d4-228">:::no-loc(Identity):::Projeye dosya eklemek için desteği kullanılmışsa :::no-loc(Identity)::: , çağrısını kaldırın `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-228">If the :::no-loc(Identity)::: scaffolder was used to add :::no-loc(Identity)::: files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/:::no-loc(Razor):::PagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="5a7d4-229">Hizmeti eklerken özel veritabanı bağlam sınıfını Kaydet :::no-loc(Identity)::: `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-229">Register the custom database context class when adding the :::no-loc(Identity)::: service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/:::no-loc(Razor):::PagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="5a7d4-230">Birincil anahtarın veri türü, [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) nesnesi analiz edilirken algılanır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="5a7d4-231">Hizmeti eklerken özel veritabanı bağlam sınıfını Kaydet :::no-loc(Identity)::: `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-231">Register the custom database context class when adding the :::no-loc(Identity)::: service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="5a7d4-232"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::EntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>Yöntemi, `TKey` birincil anahtarın veri türünü gösteren bir türü kabul eder.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-232">The <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::EntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="5a7d4-233">Gezinti özellikleri ekle</span><span class="sxs-lookup"><span data-stu-id="5a7d4-233">Add navigation properties</span></span>

<span data-ttu-id="5a7d4-234">İlişkiler için model yapılandırmasının değiştirilmesi, başka değişiklikler yapmaktan daha zor olabilir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="5a7d4-235">Yeni, ek ilişkiler oluşturmak yerine var olan ilişkilerin yerini almak için dikkatli olunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="5a7d4-236">Özellikle, değiştirilen ilişki var olan ilişki olarak aynı yabancı anahtar (FK) özelliğini belirtmelidir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="5a7d4-237">Örneğin, ve arasındaki ilişki `Users` `UserClaims` Varsayılan olarak, aşağıdaki şekilde belirtilir:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

<span data-ttu-id="5a7d4-238">Bu ilişki için FK özellik olarak belirtilir `UserClaim.UserId` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="5a7d4-239">`HasMany` ve, ' ın `WithOne` Gezinti özellikleri olmadan ilişki oluşturmak için bağımsız değişken olmadan çağırılır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="5a7d4-240">`ApplicationUser`Kullanıcıdan ilişkili olmasına izin veren bir gezinti özelliği ekleyin `UserClaims` :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public virtual ICollection<:::no-loc(Identity):::UserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="5a7d4-241">`TKey`İçin, `:::no-loc(Identity):::UserClaim<TKey>` Kullanıcı PK için belirtilen türdür.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-241">The `TKey` for `:::no-loc(Identity):::UserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="5a7d4-242">Bu durumda, `TKey` Varsayılan olarak `string` kullanılıyor.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="5a7d4-243">Varlık türü için PK türü **değildir** `UserClaim` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="5a7d4-244">Artık gezinti özelliği var olduğuna göre, bu, ' de yapılandırılması gerekir `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

```csharp
public class ApplicationDbContext : :::no-loc(Identity):::DbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

<span data-ttu-id="5a7d4-245">İlişkinin yalnızca ' de olduğu gibi, yalnızca çağrısında belirtilen bir gezinti özelliği ile yapılandırıldığından emin olun `HasMany` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="5a7d4-246">Gezinti özellikleri, veritabanında değil yalnızca EF modelinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="5a7d4-247">İlişki için FK değişmediğinden, bu tür bir model değişikliği veritabanının güncelleştirilmesini gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="5a7d4-248">Bu, değişiklik yapıldıktan sonra bir geçiş eklenerek denetlenebilir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="5a7d4-249">`Up`Ve `Down` yöntemleri boş.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="5a7d4-250">Tüm kullanıcı gezinti özelliklerini Ekle</span><span class="sxs-lookup"><span data-stu-id="5a7d4-250">Add all User navigation properties</span></span>

<span data-ttu-id="5a7d4-251">Aşağıdaki örnek, kılavuz olarak yukarıdaki bölümü kullanarak, Kullanıcı üzerindeki tüm ilişkiler için tek yönlü gezinti özelliklerini yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public virtual ICollection<:::no-loc(Identity):::UserClaim<string>> Claims { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserLogin<string>> Logins { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserToken<string>> Tokens { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : :::no-loc(Identity):::DbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="5a7d4-252">Kullanıcı ve rol gezinti özellikleri ekleme</span><span class="sxs-lookup"><span data-stu-id="5a7d4-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="5a7d4-253">Aşağıdaki örnek, kılavuz olarak yukarıdaki bölümü kullanarak, Kullanıcı ve roldeki tüm ilişkiler için gezinti özelliklerini yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public virtual ICollection<:::no-loc(Identity):::UserClaim<string>> Claims { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserLogin<string>> Logins { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : :::no-loc(Identity):::Role
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : :::no-loc(Identity):::UserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : :::no-loc(Identity):::DbContext<
        ApplicationUser, ApplicationRole, string,
        :::no-loc(Identity):::UserClaim<string>, ApplicationUserRole, :::no-loc(Identity):::UserLogin<string>,
        :::no-loc(Identity):::RoleClaim<string>, :::no-loc(Identity):::UserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

<span data-ttu-id="5a7d4-254">Notlar:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-254">Notes:</span></span>

* <span data-ttu-id="5a7d4-255">Bu örnek ayrıca `UserRole` , kullanıcılardan rollere kadar çoktan çoğa ilişkiye gitmek için gereken JOIN varlığını içerir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="5a7d4-256">Gezinti özelliklerinin türlerini, türlerin türler yerine kullanılmakta olduğunu yansıtacak şekilde değiştirmeyi unutmayın `Application{...}` `:::no-loc(Identity):::{...}` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-256">Remember to change the types of the navigation properties to reflect that `Application{...}` types are now being used instead of `:::no-loc(Identity):::{...}` types.</span></span>
* <span data-ttu-id="5a7d4-257">Öğesini `Application{...}` genel tanımda kullanmayı unutmayın `ApplicationContext` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-257">Remember to use the `Application{...}` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="5a7d4-258">Tüm gezinti özelliklerini Ekle</span><span class="sxs-lookup"><span data-stu-id="5a7d4-258">Add all navigation properties</span></span>

<span data-ttu-id="5a7d4-259">Aşağıdaki örnek, kılavuz olarak yukarıdaki bölümü kullanarak tüm varlık türlerindeki tüm ilişkiler için gezinti özelliklerini yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : :::no-loc(Identity):::Role
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : :::no-loc(Identity):::UserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : :::no-loc(Identity):::UserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : :::no-loc(Identity):::UserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : :::no-loc(Identity):::RoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : :::no-loc(Identity):::UserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : :::no-loc(Identity):::DbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a><span data-ttu-id="5a7d4-260">Bileşik anahtarlar kullanın</span><span class="sxs-lookup"><span data-stu-id="5a7d4-260">Use composite keys</span></span>

<span data-ttu-id="5a7d4-261">Önceki bölümlerde, modelde kullanılan anahtarın türünü değiştirme gösterilmiştir :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-261">The preceding sections demonstrated changing the type of key used in the :::no-loc(Identity)::: model.</span></span> <span data-ttu-id="5a7d4-262">:::no-loc(Identity):::Anahtar modelinin bileşik anahtarları kullanacak şekilde değiştirilmesi desteklenmez veya önerilmez.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-262">Changing the :::no-loc(Identity)::: key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="5a7d4-263">İle bileşik anahtar kullanılması :::no-loc(Identity)::: , :::no-loc(Identity)::: yönetici kodunun modelle nasıl etkileşime gireceğini içerir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-263">Using a composite key with :::no-loc(Identity)::: involves changing how the :::no-loc(Identity)::: manager code interacts with the model.</span></span> <span data-ttu-id="5a7d4-264">Bu özelleştirme, bu belgenin kapsamı dışındadır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="5a7d4-265">Tablo/sütun adlarını ve modelleri değiştirme</span><span class="sxs-lookup"><span data-stu-id="5a7d4-265">Change table/column names and facets</span></span>

<span data-ttu-id="5a7d4-266">Tablo ve sütun adlarını değiştirmek için çağrısı yapın `base.OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="5a7d4-267">Ardından, varsayılan ayarları geçersiz kılmak için yapılandırma ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="5a7d4-268">Örneğin, tüm tabloların adını değiştirmek için :::no-loc(Identity)::: :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-268">For example, to change the name of all the :::no-loc(Identity)::: tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<:::no-loc(Identity):::User>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<:::no-loc(Identity):::Role>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<:::no-loc(Identity):::RoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

<span data-ttu-id="5a7d4-269">Bu örnekler varsayılan türleri kullanır :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-269">These examples use the default :::no-loc(Identity)::: types.</span></span> <span data-ttu-id="5a7d4-270">Gibi bir uygulama türü kullanıyorsanız `ApplicationUser` , varsayılan tür yerine bu türü yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="5a7d4-271">Aşağıdaki örnek bazı sütun adlarını değiştirir:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-271">The following example changes some column names:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<:::no-loc(Identity):::User>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

<span data-ttu-id="5a7d4-272">Bazı veritabanı sütunları bazı tür bazı *modellerle* yapılandırılabilir (örneğin, `string` izin verilen en fazla uzunluk).</span><span class="sxs-lookup"><span data-stu-id="5a7d4-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="5a7d4-273">Aşağıdaki örnek, modeldeki çeşitli özellikler için en fazla sütun uzunluğunu ayarlar `string` :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<:::no-loc(Identity):::User>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a><span data-ttu-id="5a7d4-274">Farklı bir şemaya eşleme</span><span class="sxs-lookup"><span data-stu-id="5a7d4-274">Map to a different schema</span></span>

<span data-ttu-id="5a7d4-275">Şemalar, veritabanı sağlayıcıları genelinde farklı davranabilir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="5a7d4-276">SQL Server için varsayılan, *dbo* şemasında tüm tabloları oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="5a7d4-277">Tablolar farklı bir şemada oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="5a7d4-278">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="5a7d4-279">Geç yükleme</span><span class="sxs-lookup"><span data-stu-id="5a7d4-279">Lazy loading</span></span>

<span data-ttu-id="5a7d4-280">Bu bölümde, modeldeki yavaş yükleme proxy 'leri için destek :::no-loc(Identity)::: eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-280">In this section, support for lazy-loading proxies in the :::no-loc(Identity)::: model is added.</span></span> <span data-ttu-id="5a7d4-281">Yavaş yükleme, gezinti özelliklerinin önce yüklendiklerinden emin olmadan kullanılmasına izin verdiğinden yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="5a7d4-282">Varlık türleri, [EF Core belgelerinde](/ef/core/querying/related-data#lazy-loading)açıklandığı gibi çeşitli yollarla yavaş yükleme için uygun hale getirilebilir.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5a7d4-283">Basitlik için, aşağıdakileri gerektiren yavaş yükleme proxy 'leri kullanın:</span><span class="sxs-lookup"><span data-stu-id="5a7d4-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="5a7d4-284">[Microsoft. EntityFrameworkCore. proxy](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) paketi yüklemesi.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="5a7d4-285"><xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> [Adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)içinde bir çağrı.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="5a7d4-286">Gezinti özelliklerine sahip ortak varlık türleri `public virtual` .</span><span class="sxs-lookup"><span data-stu-id="5a7d4-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="5a7d4-287">Aşağıdaki örnekte çağırma gösterilmektedir `UseLazyLoadingProxies` `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5a7d4-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefault:::no-loc(Identity):::<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="5a7d4-288">Varlık türlerine gezinti özellikleri ekleme hakkında rehberlik için yukarıdaki örneklere bakın.</span><span class="sxs-lookup"><span data-stu-id="5a7d4-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5a7d4-289">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5a7d4-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
