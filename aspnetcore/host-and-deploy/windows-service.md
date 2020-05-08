---
title: Windows hizmetinde konak ASP.NET Core
author: rick-anderson
description: ASP.NET Core uygulamasının bir Windows hizmetinde nasıl barındıralınacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/windows-service
ms.openlocfilehash: 4ad9086c60e58f89bdde4962d7487036df251cc1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776350"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="c535d-103">Windows hizmetinde konak ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c535d-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c535d-104">Bir ASP.NET Core uygulaması, IIS kullanmadan Windows [hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak Windows üzerinde barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="c535d-105">Windows hizmeti olarak barındırıldığı zaman, uygulama otomatik olarak sunucu yeniden başlatıldıktan sonra başlatılır.</span><span class="sxs-lookup"><span data-stu-id="c535d-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="c535d-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c535d-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c535d-107">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="c535d-107">Prerequisites</span></span>

* [<span data-ttu-id="c535d-108">ASP.NET Core SDK 2,1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="c535d-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="c535d-109">PowerShell 6,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="c535d-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="c535d-110">Çalışan hizmeti şablonu</span><span class="sxs-lookup"><span data-stu-id="c535d-110">Worker Service template</span></span>

<span data-ttu-id="c535d-111">ASP.NET Core Worker hizmeti şablonu, uzun süre çalışan hizmet uygulamalarını yazmak için bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="c535d-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="c535d-112">Şablonu bir Windows Hizmeti uygulamasının temeli olarak kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="c535d-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="c535d-113">.NET Core şablonundan bir çalışan hizmeti uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c535d-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="c535d-114">Çalışan hizmeti uygulamasını bir Windows hizmeti olarak çalışacak şekilde güncelleştirmek için [uygulama yapılandırma](#app-configuration) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="c535d-115">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="c535d-115">App configuration</span></span>

<span data-ttu-id="c535d-116">Uygulama, [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)için bir paket başvurusu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="c535d-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="c535d-117">`IHostBuilder.UseWindowsService`Ana bilgisayar oluşturulurken çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c535d-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="c535d-118">Uygulama bir Windows hizmeti olarak çalışıyorsa, yöntemi:</span><span class="sxs-lookup"><span data-stu-id="c535d-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="c535d-119">Ana bilgisayar ömrünü olarak `WindowsServiceLifetime`ayarlar.</span><span class="sxs-lookup"><span data-stu-id="c535d-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="c535d-120">[İçerik kökünü](xref:fundamentals/index#content-root) [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory)olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="c535d-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="c535d-121">Daha fazla bilgi için [geçerli dizin ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="c535d-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="c535d-122">Olay günlüğüne kaydetmeyi sağlar:</span><span class="sxs-lookup"><span data-stu-id="c535d-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="c535d-123">Uygulama adı varsayılan kaynak adı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c535d-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="c535d-124">Varsayılan günlük düzeyi, ana *Warning* bilgisayarı oluşturmak için çağıran `CreateDefaultBuilder` ASP.NET Core şablonuna dayalı bir uygulama için uyarı veya daha yüksek bir uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="c535d-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="c535d-125">Varsayılan günlük düzeyini *appSettings. JSON*/appSettings `Logging:EventLog:LogLevel:Default` içindeki anahtarla geçersiz kılın *. { Environment}. JSON* veya diğer yapılandırma sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="c535d-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="c535d-126">Yeni olay kaynakları yalnızca yöneticiler tarafından oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="c535d-127">Uygulama adı kullanılarak bir olay kaynağı oluşturuoluşturumadığında, *uygulama* kaynağına bir uyarı kaydedilir ve olay günlükleri devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="c535d-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="c535d-128">Program.cs `CreateHostBuilder` ' *Program.cs*de:</span><span class="sxs-lookup"><span data-stu-id="c535d-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="c535d-129">Aşağıdaki örnek uygulamalar bu konuya eşlik eder:</span><span class="sxs-lookup"><span data-stu-id="c535d-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="c535d-130">Arka plan çalışan hizmeti &ndash; örneği arka plan görevleri için [barındırılan Hizmetleri](xref:fundamentals/host/hosted-services) kullanan [çalışan hizmeti şablonunu](#worker-service-template) temel alan, Web dışı bir uygulama örneği.</span><span class="sxs-lookup"><span data-stu-id="c535d-130">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="c535d-131">Web App Service arka &ndash; plan Razor görevleri Için [barındırılan hizmetlerle](xref:fundamentals/host/hosted-services) bir Windows hizmeti olarak çalışan bir sayfalar Web uygulaması örneği.</span><span class="sxs-lookup"><span data-stu-id="c535d-131">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="c535d-132">MVC Kılavuzu için ve <xref:mvc/overview> <xref:migration/22-to-30>altındaki makalelere bakın.</span><span class="sxs-lookup"><span data-stu-id="c535d-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="c535d-133">Dağıtım türü</span><span class="sxs-lookup"><span data-stu-id="c535d-133">Deployment type</span></span>

<span data-ttu-id="c535d-134">Dağıtım senaryoları hakkında bilgi ve öneriler için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="c535d-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="c535d-135">SDK</span><span class="sxs-lookup"><span data-stu-id="c535d-135">SDK</span></span>

<span data-ttu-id="c535d-136">Razor Sayfaları veya MVC çerçevelerini kullanan bir Web uygulaması tabanlı hizmet için, proje dosyasında Web SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="c535d-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="c535d-137">Hizmet yalnızca arka plan görevlerini (örneğin, [barındırılan hizmetler](xref:fundamentals/host/hosted-services)) yürütülüyorsa, proje dosyasında çalışan SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="c535d-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="c535d-138">Çerçeveye bağımlı dağıtım (FDD)</span><span class="sxs-lookup"><span data-stu-id="c535d-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="c535d-139">Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core 'un paylaşılan sistem genelindeki bir sürümünün varlığına dayanır.</span><span class="sxs-lookup"><span data-stu-id="c535d-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="c535d-140">Bu makaledeki kılavuzdan sonra FDD senaryosu benimsendiği zaman SDK, *çerçeveye bağlı yürütülebilir dosya*olarak adlandırılan yürütülebilir bir dosya (*. exe*) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c535d-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="c535d-141">[Web SDK](#sdk)kullanıyorsanız, normalde bir ASP.NET Core uygulaması yayımlarken üretilen bir *Web. config* dosyası Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="c535d-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="c535d-142">*Web. config* dosyasının oluşturulmasını devre dışı bırakmak için, `<IsTransformWebConfigDisabled>` özelliğini öğesine `true`ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="c535d-143">Kendi içinde dağıtım (SCD)</span><span class="sxs-lookup"><span data-stu-id="c535d-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="c535d-144">Kendinden bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir Framework varlığına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="c535d-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="c535d-145">Çalışma zamanı ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="c535d-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="c535d-146">Hedef Framework 'ü içeren ' de `<PropertyGroup>` bir Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) bulunur:</span><span class="sxs-lookup"><span data-stu-id="c535d-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="c535d-147">Birden çok RID için yayımlamak için:</span><span class="sxs-lookup"><span data-stu-id="c535d-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="c535d-148">RID 'leri, noktalı virgülle ayrılmış bir liste olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="c535d-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="c535d-149">>(plural) adlı [ \<runtimetanımlayıcılar](/dotnet/core/tools/csproj#runtimeidentifiers) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="c535d-150">Daha fazla bilgi için bkz. [.NET Core RID kataloğu](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="c535d-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="c535d-151">Hizmet Kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="c535d-151">Service user account</span></span>

<span data-ttu-id="c535d-152">Bir hizmet için Kullanıcı hesabı oluşturmak için, bir yönetim PowerShell 6 komut kabuğundan [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="c535d-153">Windows 10 Ekim 2018 Güncelleştirmesi (sürüm 1809/Build 10.0.17763) veya sonraki sürümler:</span><span class="sxs-lookup"><span data-stu-id="c535d-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="c535d-154">Windows 10 Ekim 2018 (sürüm 1809/Build 10.0.17763) sürümünden önceki Windows IŞLETIM sistemlerinde:</span><span class="sxs-lookup"><span data-stu-id="c535d-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="c535d-155">İstendiğinde [güçlü bir parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.</span><span class="sxs-lookup"><span data-stu-id="c535d-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="c535d-156">`-AccountExpires` Parametresi [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ine bir süre sonu <xref:System.DateTime>ile sağlanmamışsa hesabın süresi dolmaz.</span><span class="sxs-lookup"><span data-stu-id="c535d-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="c535d-157">Daha fazla bilgi için bkz. [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [hizmet Kullanıcı hesapları](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="c535d-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="c535d-158">Active Directory kullanırken kullanıcıları yönetmeye yönelik alternatif bir yaklaşım, yönetilen hizmet hesaplarını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="c535d-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="c535d-159">Daha fazla bilgi için bkz. [Grup yönetilen hizmet hesaplarına genel bakış](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="c535d-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="c535d-160">Hizmet hakları olarak oturum açma</span><span class="sxs-lookup"><span data-stu-id="c535d-160">Log on as a service rights</span></span>

<span data-ttu-id="c535d-161">Hizmet Kullanıcı hesabı için *hizmet hakları olarak oturum* açma oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="c535d-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="c535d-162">Yerel Güvenlik Ilkesi düzenleyicisini, *secpol. msc*' i çalıştırarak açın.</span><span class="sxs-lookup"><span data-stu-id="c535d-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="c535d-163">**Yerel ilkeler** düğümünü genişletin ve **Kullanıcı hakları ataması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="c535d-164">**Hizmet olarak oturum** açma ilkesi açın.</span><span class="sxs-lookup"><span data-stu-id="c535d-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="c535d-165">**Kullanıcı veya Grup Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="c535d-166">Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (Kullanıcı hesabı) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="c535d-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="c535d-167">Kullanıcı hesabını (`{DOMAIN OR COMPUTER NAME\USER}`) nesne adı alanına yazın ve kullanıcıyı ilkeye eklemek için **Tamam** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="c535d-168">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-168">Select **Advanced**.</span></span> <span data-ttu-id="c535d-169">**Şimdi bul**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-169">Select **Find Now**.</span></span> <span data-ttu-id="c535d-170">Listeden Kullanıcı hesabını seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-170">Select the user account from the list.</span></span> <span data-ttu-id="c535d-171">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-171">Select **OK**.</span></span> <span data-ttu-id="c535d-172">Kullanıcıyı ilkeye eklemek için yeniden **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="c535d-173">Değişiklikleri kabul etmek için **Tamam ' ı** veya **Uygula** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="c535d-174">Windows hizmetini oluşturma ve yönetme</span><span class="sxs-lookup"><span data-stu-id="c535d-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="c535d-175">Hizmet oluşturma</span><span class="sxs-lookup"><span data-stu-id="c535d-175">Create a service</span></span>

<span data-ttu-id="c535d-176">Bir hizmeti kaydetmek için PowerShell komutlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="c535d-177">Bir yönetim PowerShell 6 komut kabuğundan aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="c535d-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="c535d-178">`{EXE PATH}`&ndash; Konaktaki uygulamanın klasörünün yolu (örneğin, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="c535d-178">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="c535d-179">Uygulamanın yürütülebilir dosyasını yola eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="c535d-180">Sondaki eğik çizgi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="c535d-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="c535d-181">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Hizmet Kullanıcı hesabı (örneğin, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="c535d-181">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="c535d-182">`{SERVICE NAME}`&ndash; Hizmet adı (örneğin, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="c535d-182">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="c535d-183">`{EXE FILE PATH}`&ndash; Uygulamanın yürütülebilir yolu (örneğin, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="c535d-183">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="c535d-184">Yürütülebilir dosyanın dosya adını uzantısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="c535d-185">`{DESCRIPTION}`&ndash; Hizmet açıklaması (örneğin, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="c535d-185">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="c535d-186">`{DISPLAY NAME}`&ndash; Hizmet görünen adı (örneğin, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="c535d-186">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="c535d-187">Bir hizmet başlatın</span><span class="sxs-lookup"><span data-stu-id="c535d-187">Start a service</span></span>

<span data-ttu-id="c535d-188">Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:</span><span class="sxs-lookup"><span data-stu-id="c535d-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="c535d-189">Komutun başlatılması birkaç saniye sürer.</span><span class="sxs-lookup"><span data-stu-id="c535d-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="c535d-190">Hizmetin durumunu belirleme</span><span class="sxs-lookup"><span data-stu-id="c535d-190">Determine a service's status</span></span>

<span data-ttu-id="c535d-191">Bir hizmetin durumunu denetlemek için aşağıdaki PowerShell 6 komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="c535d-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="c535d-192">Durum, aşağıdaki değerlerden biri olarak bildirilir:</span><span class="sxs-lookup"><span data-stu-id="c535d-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="c535d-193">Bir hizmet durdurun</span><span class="sxs-lookup"><span data-stu-id="c535d-193">Stop a service</span></span>

<span data-ttu-id="c535d-194">Aşağıdaki PowerShell 6 komutuyla bir hizmeti durdurun:</span><span class="sxs-lookup"><span data-stu-id="c535d-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="c535d-195">Hizmeti Kaldır</span><span class="sxs-lookup"><span data-stu-id="c535d-195">Remove a service</span></span>

<span data-ttu-id="c535d-196">Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki PowerShell 6 komutuyla bir hizmeti kaldırın:</span><span class="sxs-lookup"><span data-stu-id="c535d-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c535d-197">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="c535d-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c535d-198">Internet 'ten veya şirket ağından gelen isteklerle etkileşime geçen ve bir ara sunucu veya yük dengeleyicinin arkasındaki Hizmetler ek yapılandırma gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="c535d-199">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="c535d-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="c535d-200">Uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c535d-200">Configure endpoints</span></span>

<span data-ttu-id="c535d-201">Varsayılan olarak, ASP.NET Core öğesine `http://localhost:5000`bağlanır.</span><span class="sxs-lookup"><span data-stu-id="c535d-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="c535d-202">`ASPNETCORE_URLS` Ortam değişkenini ayarlayarak URL 'yi ve bağlantı noktasını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="c535d-203">Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="c535d-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="c535d-204">Yukarıdaki kılavuz, HTTPS uç noktaları için desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="c535d-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="c535d-205">Örneğin, bir Windows hizmeti ile kimlik doğrulaması kullanıldığında HTTPS için uygulamayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="c535d-206">Hizmet uç noktasının güvenliğini sağlamak için ASP.NET Core HTTPS geliştirme sertifikası kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c535d-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="c535d-207">Geçerli dizin ve içerik kökü</span><span class="sxs-lookup"><span data-stu-id="c535d-207">Current directory and content root</span></span>

<span data-ttu-id="c535d-208">Windows hizmeti için çağırarak <xref:System.IO.Directory.GetCurrentDirectory*> döndürülen geçerli çalışma dizini *C:\\Windows\\system32* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="c535d-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="c535d-209">*System32* klasörü, bir hizmetin dosyalarını (örneğin, ayarlar dosyaları) depolamak için uygun bir konum değildir.</span><span class="sxs-lookup"><span data-stu-id="c535d-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="c535d-210">Bir hizmetin varlık ve ayar dosyalarını sürdürmek ve erişmek için aşağıdaki yaklaşımlardan birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="c535d-211">ContentRootPath veya ContentRootFileProvider kullanın</span><span class="sxs-lookup"><span data-stu-id="c535d-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="c535d-212">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) kullanın veya <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> bir uygulamanın kaynaklarını bulun.</span><span class="sxs-lookup"><span data-stu-id="c535d-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="c535d-213">Uygulama bir hizmet olarak çalıştırıldığında, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory)olarak ayarlanır <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> .</span><span class="sxs-lookup"><span data-stu-id="c535d-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="c535d-214">Uygulamanın varsayılan ayar dosyaları, *appSettings. JSON* ve *appSettings. { '. JSON ortamı*, [konak oluşturma sırasında Createdefaultbuilder](xref:fundamentals/host/generic-host#set-up-a-host)' ı çağırarak uygulamanın içerik kökünden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="c535d-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="c535d-215">İçindeki <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>geliştirici kodu tarafından yüklenen diğer ayar dosyaları için, çağrısına <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="c535d-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="c535d-216">Aşağıdaki örnekte, *custom_settings. JSON* dosyası uygulamanın içerik kökünde bulunur ve açıkça bir temel yolu ayarlamadan yüklenir:</span><span class="sxs-lookup"><span data-stu-id="c535d-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="c535d-217">Bir Windows hizmeti uygulaması <xref:System.IO.Directory.GetCurrentDirectory*> geçerli dizini olarak *C:\\Windows\\system32* klasörünü döndürdüğünden kaynak yolunu almak için kullanmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="c535d-218">Hizmetin dosyalarını diskte uygun bir konumda depolayın</span><span class="sxs-lookup"><span data-stu-id="c535d-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="c535d-219">Dosyaları içeren klasöre kullanırken <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> ile mutlak bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="c535d-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="c535d-220">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="c535d-220">Troubleshoot</span></span>

<span data-ttu-id="c535d-221">Bir Windows hizmet uygulaması sorunlarını gidermek için bkz <xref:test/troubleshoot>..</span><span class="sxs-lookup"><span data-stu-id="c535d-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="c535d-222">Sık karşılaşılan hatalar</span><span class="sxs-lookup"><span data-stu-id="c535d-222">Common errors</span></span>

* <span data-ttu-id="c535d-223">PowerShell 'in eski veya yayın öncesi sürümü kullanımda.</span><span class="sxs-lookup"><span data-stu-id="c535d-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="c535d-224">Kayıtlı hizmet, [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutundan uygulamanın **yayımlanmış** çıktısını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="c535d-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="c535d-225">[DotNet Build](/dotnet/core/tools/dotnet-build) komutunun çıkışı, uygulama dağıtımı için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c535d-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="c535d-226">Yayımlanan varlıklar, dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:</span><span class="sxs-lookup"><span data-stu-id="c535d-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="c535d-227">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="c535d-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="c535d-228">*bin/Release/{Target Framework}/{RUNTIME tanımlayıcısı}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="c535d-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="c535d-229">Hizmet çalışır durumda değil.</span><span class="sxs-lookup"><span data-stu-id="c535d-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="c535d-230">Uygulamanın kullandığı kaynakların yolları (örneğin, sertifikalar) yanlış.</span><span class="sxs-lookup"><span data-stu-id="c535d-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="c535d-231">Bir Windows hizmetinin temel yolu *c:\\Windows\\system32*' dir.</span><span class="sxs-lookup"><span data-stu-id="c535d-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="c535d-232">Kullanıcı, hizmet hakları *olarak oturum* açma hakkına sahip değil.</span><span class="sxs-lookup"><span data-stu-id="c535d-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="c535d-233">`New-Service` PowerShell komutu çalıştırılırken kullanıcının parolasının kullanım dışı veya yanlış şekilde geçirilmesi.</span><span class="sxs-lookup"><span data-stu-id="c535d-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="c535d-234">Uygulama ASP.NET Core kimlik doğrulaması gerektiriyor, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="c535d-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="c535d-235">İstek URL 'SI bağlantı noktası yanlış veya uygulamada doğru şekilde yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="c535d-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="c535d-236">Sistem ve uygulama olay günlükleri</span><span class="sxs-lookup"><span data-stu-id="c535d-236">System and Application Event Logs</span></span>

<span data-ttu-id="c535d-237">Sistem ve uygulama olay günlüklerine erişin:</span><span class="sxs-lookup"><span data-stu-id="c535d-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="c535d-238">Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="c535d-239">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="c535d-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="c535d-240">Sistem olay günlüğünü açmak için **sistem** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="c535d-241">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="c535d-242">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="c535d-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="c535d-243">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="c535d-243">Run the app at a command prompt</span></span>

<span data-ttu-id="c535d-244">Birçok başlatma hatası olay günlüklerinde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="c535d-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="c535d-245">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c535d-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="c535d-246">Uygulamadan ek ayrıntı günlüğe kaydetmek için, [günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [geliştirme ortamında](xref:fundamentals/environments)uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="c535d-247">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="c535d-247">Clear package caches</span></span>

<span data-ttu-id="c535d-248">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="c535d-249">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="c535d-250">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="c535d-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="c535d-251">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="c535d-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="c535d-252">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="c535d-253">Paket önbelleklerini Temizleme, [NuGet. exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komut `nuget locals all -clear`yürütülebilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="c535d-254">*NuGet. exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c535d-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="c535d-255">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="c535d-256">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="c535d-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="c535d-257">Yavaş veya askıda olan uygulama</span><span class="sxs-lookup"><span data-stu-id="c535d-257">Slow or hanging app</span></span>

<span data-ttu-id="c535d-258">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="c535d-259">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="c535d-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="c535d-260">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="c535d-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="c535d-261">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="c535d-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="c535d-262">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) uygulama yürütülebilir adıyla çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c535d-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="c535d-263">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="c535d-264">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c535d-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="c535d-265">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="c535d-266">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="c535d-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="c535d-267">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="c535d-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="c535d-268">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="c535d-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="c535d-269">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="c535d-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="c535d-270">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="c535d-270">Analyze the dump</span></span>

<span data-ttu-id="c535d-271">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="c535d-272">Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="c535d-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c535d-273">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c535d-273">Additional resources</span></span>

* <span data-ttu-id="c535d-274">[Kestrel uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (https yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="c535d-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c535d-275">Bir ASP.NET Core uygulaması, IIS kullanmadan Windows [hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak Windows üzerinde barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="c535d-276">Windows hizmeti olarak barındırıldığı zaman, uygulama otomatik olarak sunucu yeniden başlatıldıktan sonra başlatılır.</span><span class="sxs-lookup"><span data-stu-id="c535d-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="c535d-277">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c535d-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c535d-278">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="c535d-278">Prerequisites</span></span>

* [<span data-ttu-id="c535d-279">ASP.NET Core SDK 2,1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="c535d-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="c535d-280">PowerShell 6,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="c535d-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="c535d-281">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="c535d-281">App configuration</span></span>

<span data-ttu-id="c535d-282">Uygulama [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) ve [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)için paket başvuruları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="c535d-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="c535d-283">Bir hizmetin dışında çalışırken test ve hata ayıklamak için, uygulamanın bir hizmet olarak mı yoksa bir konsol uygulaması mi çalıştığını belirleme kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="c535d-284">Hata ayıklayıcının ekli olduğunu veya bir `--console` anahtarın mevcut olup olmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="c535d-285">Her iki koşul de geçerliyse (uygulama bir hizmet olarak çalıştırılmadıysa), çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="c535d-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="c535d-286">Koşullar yanlışsa (uygulama bir hizmet olarak çalıştırılır):</span><span class="sxs-lookup"><span data-stu-id="c535d-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="c535d-287">Uygulamanın <xref:System.IO.Directory.SetCurrentDirectory*> yayımlanmış konumunun yolunu çağırın ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="c535d-288">Bir Windows <xref:System.IO.Directory.GetCurrentDirectory*> hizmeti uygulaması çağrıldığında *C:\\Windows\\system32* klasörünü <xref:System.IO.Directory.GetCurrentDirectory*> döndürdüğünden yolu almak için çağırmayın.</span><span class="sxs-lookup"><span data-stu-id="c535d-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="c535d-289">Daha fazla bilgi için [geçerli dizin ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="c535d-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="c535d-290">Bu adım, uygulamanın ' de `CreateWebHostBuilder`yapılandırılmadan önce gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="c535d-291">Uygulamayı <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> bir hizmet olarak çalıştırmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="c535d-292">[Komut satırı yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#command-line-configuration-provider) komut satırı bağımsız değişkenleri için ad-değer çiftleri gerektirdiğinden, bağımsız değişkenleri almadan önce `--console` <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> anahtar bağımsız değişkenlerden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="c535d-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="c535d-293">Windows olay günlüğü 'ne yazmak için EventLog sağlayıcısını öğesine <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="c535d-294">Günlük kaydı düzeyini appSettings içindeki `Logging:LogLevel:Default` anahtarla ayarlayın *. Production. JSON* dosyası.</span><span class="sxs-lookup"><span data-stu-id="c535d-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="c535d-295">Örnek uygulamadan aşağıdaki örnekte, `RunAsCustomService` uygulama içindeki ömür olaylarını işlemek için yerine <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c535d-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="c535d-296">Daha fazla bilgi için [olayları başlatma ve durdurma olaylarını](#handle-starting-and-stopping-events) inceleyin bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="c535d-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="c535d-297">Dağıtım türü</span><span class="sxs-lookup"><span data-stu-id="c535d-297">Deployment type</span></span>

<span data-ttu-id="c535d-298">Dağıtım senaryoları hakkında bilgi ve öneriler için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="c535d-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="c535d-299">SDK</span><span class="sxs-lookup"><span data-stu-id="c535d-299">SDK</span></span>

<span data-ttu-id="c535d-300">Razor Sayfaları veya MVC çerçevelerini kullanan bir Web uygulaması tabanlı hizmet için, proje dosyasında Web SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="c535d-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="c535d-301">Hizmet yalnızca arka plan görevlerini (örneğin, [barındırılan hizmetler](xref:fundamentals/host/hosted-services)) yürütülüyorsa, proje dosyasında çalışan SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="c535d-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="c535d-302">Çerçeveye bağımlı dağıtım (FDD)</span><span class="sxs-lookup"><span data-stu-id="c535d-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="c535d-303">Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core 'un paylaşılan sistem genelindeki bir sürümünün varlığına dayanır.</span><span class="sxs-lookup"><span data-stu-id="c535d-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="c535d-304">Bu makaledeki kılavuzdan sonra FDD senaryosu benimsendiği zaman SDK, *çerçeveye bağlı yürütülebilir dosya*olarak adlandırılan yürütülebilir bir dosya (*. exe*) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c535d-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="c535d-305">Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) ([\<runtimeıdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)), hedef Framework 'ü içerir.</span><span class="sxs-lookup"><span data-stu-id="c535d-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="c535d-306">Aşağıdaki örnekte, RID olarak `win7-x64`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c535d-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="c535d-307">`<SelfContained>` Özelliği olarak `false`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c535d-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="c535d-308">Bu özellikler SDK 'nın Windows için bir yürütülebilir (*. exe*) dosya ve paylaşılan .NET Core çerçevesine bağlı bir uygulama oluşturmasını ister.</span><span class="sxs-lookup"><span data-stu-id="c535d-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="c535d-309">Bir ASP.NET Core uygulaması yayımlandığında normalde üretilen bir *Web. config* dosyası, Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="c535d-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="c535d-310">*Web. config* dosyasının oluşturulmasını devre dışı bırakmak için, `<IsTransformWebConfigDisabled>` özelliğini öğesine `true`ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="c535d-311">Kendi içinde dağıtım (SCD)</span><span class="sxs-lookup"><span data-stu-id="c535d-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="c535d-312">Kendinden bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir Framework varlığına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="c535d-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="c535d-313">Çalışma zamanı ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="c535d-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="c535d-314">Hedef Framework 'ü içeren ' de `<PropertyGroup>` bir Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) bulunur:</span><span class="sxs-lookup"><span data-stu-id="c535d-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="c535d-315">Birden çok RID için yayımlamak için:</span><span class="sxs-lookup"><span data-stu-id="c535d-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="c535d-316">RID 'leri, noktalı virgülle ayrılmış bir liste olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="c535d-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="c535d-317">>(plural) adlı [ \<runtimetanımlayıcılar](/dotnet/core/tools/csproj#runtimeidentifiers) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="c535d-318">Daha fazla bilgi için bkz. [.NET Core RID kataloğu](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="c535d-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="c535d-319">Bir `<SelfContained>` özellik şu şekilde `true`ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="c535d-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="c535d-320">Hizmet Kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="c535d-320">Service user account</span></span>

<span data-ttu-id="c535d-321">Bir hizmet için Kullanıcı hesabı oluşturmak için, bir yönetim PowerShell 6 komut kabuğundan [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="c535d-322">Windows 10 Ekim 2018 Güncelleştirmesi (sürüm 1809/Build 10.0.17763) veya sonraki sürümler:</span><span class="sxs-lookup"><span data-stu-id="c535d-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="c535d-323">Windows 10 Ekim 2018 (sürüm 1809/Build 10.0.17763) sürümünden önceki Windows IŞLETIM sistemlerinde:</span><span class="sxs-lookup"><span data-stu-id="c535d-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="c535d-324">İstendiğinde [güçlü bir parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.</span><span class="sxs-lookup"><span data-stu-id="c535d-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="c535d-325">`-AccountExpires` Parametresi [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ine bir süre sonu <xref:System.DateTime>ile sağlanmamışsa hesabın süresi dolmaz.</span><span class="sxs-lookup"><span data-stu-id="c535d-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="c535d-326">Daha fazla bilgi için bkz. [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [hizmet Kullanıcı hesapları](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="c535d-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="c535d-327">Active Directory kullanırken kullanıcıları yönetmeye yönelik alternatif bir yaklaşım, yönetilen hizmet hesaplarını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="c535d-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="c535d-328">Daha fazla bilgi için bkz. [Grup yönetilen hizmet hesaplarına genel bakış](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="c535d-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="c535d-329">Hizmet hakları olarak oturum açma</span><span class="sxs-lookup"><span data-stu-id="c535d-329">Log on as a service rights</span></span>

<span data-ttu-id="c535d-330">Hizmet Kullanıcı hesabı için *hizmet hakları olarak oturum* açma oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="c535d-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="c535d-331">Yerel Güvenlik Ilkesi düzenleyicisini, *secpol. msc*' i çalıştırarak açın.</span><span class="sxs-lookup"><span data-stu-id="c535d-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="c535d-332">**Yerel ilkeler** düğümünü genişletin ve **Kullanıcı hakları ataması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="c535d-333">**Hizmet olarak oturum** açma ilkesi açın.</span><span class="sxs-lookup"><span data-stu-id="c535d-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="c535d-334">**Kullanıcı veya Grup Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="c535d-335">Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (Kullanıcı hesabı) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="c535d-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="c535d-336">Kullanıcı hesabını (`{DOMAIN OR COMPUTER NAME\USER}`) nesne adı alanına yazın ve kullanıcıyı ilkeye eklemek için **Tamam** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="c535d-337">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-337">Select **Advanced**.</span></span> <span data-ttu-id="c535d-338">**Şimdi bul**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-338">Select **Find Now**.</span></span> <span data-ttu-id="c535d-339">Listeden Kullanıcı hesabını seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-339">Select the user account from the list.</span></span> <span data-ttu-id="c535d-340">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-340">Select **OK**.</span></span> <span data-ttu-id="c535d-341">Kullanıcıyı ilkeye eklemek için yeniden **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="c535d-342">Değişiklikleri kabul etmek için **Tamam ' ı** veya **Uygula** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="c535d-343">Windows hizmetini oluşturma ve yönetme</span><span class="sxs-lookup"><span data-stu-id="c535d-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="c535d-344">Hizmet oluşturma</span><span class="sxs-lookup"><span data-stu-id="c535d-344">Create a service</span></span>

<span data-ttu-id="c535d-345">Bir hizmeti kaydetmek için PowerShell komutlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="c535d-346">Bir yönetim PowerShell 6 komut kabuğundan aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="c535d-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="c535d-347">`{EXE PATH}`&ndash; Konaktaki uygulamanın klasörünün yolu (örneğin, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="c535d-347">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="c535d-348">Uygulamanın yürütülebilir dosyasını yola eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="c535d-349">Sondaki eğik çizgi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="c535d-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="c535d-350">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Hizmet Kullanıcı hesabı (örneğin, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="c535d-350">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="c535d-351">`{SERVICE NAME}`&ndash; Hizmet adı (örneğin, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="c535d-351">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="c535d-352">`{EXE FILE PATH}`&ndash; Uygulamanın yürütülebilir yolu (örneğin, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="c535d-352">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="c535d-353">Yürütülebilir dosyanın dosya adını uzantısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="c535d-354">`{DESCRIPTION}`&ndash; Hizmet açıklaması (örneğin, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="c535d-354">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="c535d-355">`{DISPLAY NAME}`&ndash; Hizmet görünen adı (örneğin, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="c535d-355">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="c535d-356">Bir hizmet başlatın</span><span class="sxs-lookup"><span data-stu-id="c535d-356">Start a service</span></span>

<span data-ttu-id="c535d-357">Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:</span><span class="sxs-lookup"><span data-stu-id="c535d-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="c535d-358">Komutun başlatılması birkaç saniye sürer.</span><span class="sxs-lookup"><span data-stu-id="c535d-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="c535d-359">Hizmetin durumunu belirleme</span><span class="sxs-lookup"><span data-stu-id="c535d-359">Determine a service's status</span></span>

<span data-ttu-id="c535d-360">Bir hizmetin durumunu denetlemek için aşağıdaki PowerShell 6 komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="c535d-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="c535d-361">Durum, aşağıdaki değerlerden biri olarak bildirilir:</span><span class="sxs-lookup"><span data-stu-id="c535d-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="c535d-362">Bir hizmet durdurun</span><span class="sxs-lookup"><span data-stu-id="c535d-362">Stop a service</span></span>

<span data-ttu-id="c535d-363">Aşağıdaki PowerShell 6 komutuyla bir hizmeti durdurun:</span><span class="sxs-lookup"><span data-stu-id="c535d-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="c535d-364">Hizmeti Kaldır</span><span class="sxs-lookup"><span data-stu-id="c535d-364">Remove a service</span></span>

<span data-ttu-id="c535d-365">Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki PowerShell 6 komutuyla bir hizmeti kaldırın:</span><span class="sxs-lookup"><span data-stu-id="c535d-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="c535d-366">Olayları başlatma ve durdurma olaylarını işleme</span><span class="sxs-lookup"><span data-stu-id="c535d-366">Handle starting and stopping events</span></span>

<span data-ttu-id="c535d-367">, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>Ve <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> olaylarını işlemek için:</span><span class="sxs-lookup"><span data-stu-id="c535d-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="c535d-368">`OnStarting`, `OnStarted`Ve `OnStopping` yöntemleriyle türetilen <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> bir sınıf oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c535d-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="c535d-369">' A geçişi <xref:Microsoft.AspNetCore.Hosting.IWebHost> `CustomWebHostService` için bir genişletme yöntemi oluşturun <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="c535d-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="c535d-370">İçinde `Program.Main`, yerine `RunAsCustomService` genişletme yöntemini çağırın <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="c535d-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="c535d-371">Konumunu <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> görmek Için `Program.Main`, [dağıtım türü](#deployment-type) bölümünde gösterilen kod örneğine bakın.</span><span class="sxs-lookup"><span data-stu-id="c535d-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c535d-372">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="c535d-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c535d-373">Internet 'ten veya şirket ağından gelen isteklerle etkileşime geçen ve bir ara sunucu veya yük dengeleyicinin arkasındaki Hizmetler ek yapılandırma gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="c535d-374">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="c535d-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="c535d-375">Uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c535d-375">Configure endpoints</span></span>

<span data-ttu-id="c535d-376">Varsayılan olarak, ASP.NET Core öğesine `http://localhost:5000`bağlanır.</span><span class="sxs-lookup"><span data-stu-id="c535d-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="c535d-377">`ASPNETCORE_URLS` Ortam değişkenini ayarlayarak URL 'yi ve bağlantı noktasını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="c535d-378">Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="c535d-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="c535d-379">Yukarıdaki kılavuz, HTTPS uç noktaları için desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="c535d-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="c535d-380">Örneğin, bir Windows hizmeti ile kimlik doğrulaması kullanıldığında HTTPS için uygulamayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="c535d-381">Hizmet uç noktasının güvenliğini sağlamak için ASP.NET Core HTTPS geliştirme sertifikası kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c535d-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="c535d-382">Geçerli dizin ve içerik kökü</span><span class="sxs-lookup"><span data-stu-id="c535d-382">Current directory and content root</span></span>

<span data-ttu-id="c535d-383">Windows hizmeti için çağırarak <xref:System.IO.Directory.GetCurrentDirectory*> döndürülen geçerli çalışma dizini *C:\\Windows\\system32* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="c535d-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="c535d-384">*System32* klasörü, bir hizmetin dosyalarını (örneğin, ayarlar dosyaları) depolamak için uygun bir konum değildir.</span><span class="sxs-lookup"><span data-stu-id="c535d-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="c535d-385">Bir hizmetin varlık ve ayar dosyalarını sürdürmek ve erişmek için aşağıdaki yaklaşımlardan birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="c535d-386">Uygulamanın klasörü için içerik kök yolunu ayarla</span><span class="sxs-lookup"><span data-stu-id="c535d-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="c535d-387">, <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> Bir hizmet oluşturulduğunda `binPath` bağımsız değişkene aynı yol olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="c535d-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="c535d-388">Ayarlar dosyalarına yollar `GetCurrentDirectory` oluşturmak için çağırmak yerine, uygulamanın <xref:System.IO.Directory.SetCurrentDirectory*> [içerik kökünün](xref:fundamentals/index#content-root)yolunu çağırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="c535d-389">İçinde `Program.Main`, hizmetin yürütülebilir dosyasının yolunu belirleyin ve uygulamanın içerik kökünü oluşturmak için yolu kullanın:</span><span class="sxs-lookup"><span data-stu-id="c535d-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="c535d-390">Hizmetin dosyalarını diskte uygun bir konumda depolayın</span><span class="sxs-lookup"><span data-stu-id="c535d-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="c535d-391">Dosyaları içeren klasöre kullanırken <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> ile mutlak bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="c535d-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="c535d-392">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="c535d-392">Troubleshoot</span></span>

<span data-ttu-id="c535d-393">Bir Windows hizmet uygulaması sorunlarını gidermek için bkz <xref:test/troubleshoot>..</span><span class="sxs-lookup"><span data-stu-id="c535d-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="c535d-394">Sık karşılaşılan hatalar</span><span class="sxs-lookup"><span data-stu-id="c535d-394">Common errors</span></span>

* <span data-ttu-id="c535d-395">PowerShell 'in eski veya yayın öncesi sürümü kullanımda.</span><span class="sxs-lookup"><span data-stu-id="c535d-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="c535d-396">Kayıtlı hizmet, [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutundan uygulamanın **yayımlanmış** çıktısını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="c535d-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="c535d-397">[DotNet Build](/dotnet/core/tools/dotnet-build) komutunun çıkışı, uygulama dağıtımı için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c535d-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="c535d-398">Yayımlanan varlıklar, dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:</span><span class="sxs-lookup"><span data-stu-id="c535d-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="c535d-399">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="c535d-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="c535d-400">*bin/Release/{Target Framework}/{RUNTIME tanımlayıcısı}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="c535d-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="c535d-401">Hizmet çalışır durumda değil.</span><span class="sxs-lookup"><span data-stu-id="c535d-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="c535d-402">Uygulamanın kullandığı kaynakların yolları (örneğin, sertifikalar) yanlış.</span><span class="sxs-lookup"><span data-stu-id="c535d-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="c535d-403">Bir Windows hizmetinin temel yolu *c:\\Windows\\system32*' dir.</span><span class="sxs-lookup"><span data-stu-id="c535d-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="c535d-404">Kullanıcı, hizmet hakları *olarak oturum* açma hakkına sahip değil.</span><span class="sxs-lookup"><span data-stu-id="c535d-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="c535d-405">`New-Service` PowerShell komutu çalıştırılırken kullanıcının parolasının kullanım dışı veya yanlış şekilde geçirilmesi.</span><span class="sxs-lookup"><span data-stu-id="c535d-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="c535d-406">Uygulama ASP.NET Core kimlik doğrulaması gerektiriyor, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="c535d-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="c535d-407">İstek URL 'SI bağlantı noktası yanlış veya uygulamada doğru şekilde yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="c535d-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="c535d-408">Sistem ve uygulama olay günlükleri</span><span class="sxs-lookup"><span data-stu-id="c535d-408">System and Application Event Logs</span></span>

<span data-ttu-id="c535d-409">Sistem ve uygulama olay günlüklerine erişin:</span><span class="sxs-lookup"><span data-stu-id="c535d-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="c535d-410">Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="c535d-411">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="c535d-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="c535d-412">Sistem olay günlüğünü açmak için **sistem** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="c535d-413">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="c535d-414">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="c535d-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="c535d-415">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="c535d-415">Run the app at a command prompt</span></span>

<span data-ttu-id="c535d-416">Birçok başlatma hatası olay günlüklerinde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="c535d-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="c535d-417">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c535d-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="c535d-418">Uygulamadan ek ayrıntı günlüğe kaydetmek için, [günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [geliştirme ortamında](xref:fundamentals/environments)uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="c535d-419">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="c535d-419">Clear package caches</span></span>

<span data-ttu-id="c535d-420">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="c535d-421">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="c535d-422">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="c535d-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="c535d-423">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="c535d-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="c535d-424">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="c535d-425">Paket önbelleklerini Temizleme, [NuGet. exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komut `nuget locals all -clear`yürütülebilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="c535d-426">*NuGet. exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c535d-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="c535d-427">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="c535d-428">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="c535d-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="c535d-429">Yavaş veya askıda olan uygulama</span><span class="sxs-lookup"><span data-stu-id="c535d-429">Slow or hanging app</span></span>

<span data-ttu-id="c535d-430">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="c535d-431">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="c535d-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="c535d-432">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="c535d-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="c535d-433">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="c535d-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="c535d-434">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) uygulama yürütülebilir adıyla çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c535d-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="c535d-435">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="c535d-436">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c535d-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="c535d-437">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="c535d-438">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="c535d-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="c535d-439">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="c535d-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="c535d-440">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="c535d-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="c535d-441">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="c535d-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="c535d-442">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="c535d-442">Analyze the dump</span></span>

<span data-ttu-id="c535d-443">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="c535d-444">Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="c535d-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c535d-445">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c535d-445">Additional resources</span></span>

* <span data-ttu-id="c535d-446">[Kestrel uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (https yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="c535d-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c535d-447">Bir ASP.NET Core uygulaması, IIS kullanmadan Windows [hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak Windows üzerinde barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="c535d-448">Windows hizmeti olarak barındırıldığı zaman, uygulama otomatik olarak sunucu yeniden başlatıldıktan sonra başlatılır.</span><span class="sxs-lookup"><span data-stu-id="c535d-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="c535d-449">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c535d-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c535d-450">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="c535d-450">Prerequisites</span></span>

* [<span data-ttu-id="c535d-451">ASP.NET Core SDK 2,1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="c535d-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="c535d-452">PowerShell 6,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="c535d-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="c535d-453">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="c535d-453">App configuration</span></span>

<span data-ttu-id="c535d-454">Uygulama [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) ve [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)için paket başvuruları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="c535d-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="c535d-455">Bir hizmetin dışında çalışırken test ve hata ayıklamak için, uygulamanın bir hizmet olarak mı yoksa bir konsol uygulaması mi çalıştığını belirleme kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="c535d-456">Hata ayıklayıcının ekli olduğunu veya bir `--console` anahtarın mevcut olup olmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="c535d-457">Her iki koşul de geçerliyse (uygulama bir hizmet olarak çalıştırılmadıysa), çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="c535d-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="c535d-458">Koşullar yanlışsa (uygulama bir hizmet olarak çalıştırılır):</span><span class="sxs-lookup"><span data-stu-id="c535d-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="c535d-459">Uygulamanın <xref:System.IO.Directory.SetCurrentDirectory*> yayımlanmış konumunun yolunu çağırın ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="c535d-460">Bir Windows <xref:System.IO.Directory.GetCurrentDirectory*> hizmeti uygulaması çağrıldığında *C:\\Windows\\system32* klasörünü <xref:System.IO.Directory.GetCurrentDirectory*> döndürdüğünden yolu almak için çağırmayın.</span><span class="sxs-lookup"><span data-stu-id="c535d-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="c535d-461">Daha fazla bilgi için [geçerli dizin ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="c535d-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="c535d-462">Bu adım, uygulamanın ' de `CreateWebHostBuilder`yapılandırılmadan önce gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="c535d-463">Uygulamayı <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> bir hizmet olarak çalıştırmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="c535d-464">[Komut satırı yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#command-line-configuration-provider) komut satırı bağımsız değişkenleri için ad-değer çiftleri gerektirdiğinden, bağımsız değişkenleri almadan önce `--console` <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> anahtar bağımsız değişkenlerden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="c535d-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="c535d-465">Windows olay günlüğü 'ne yazmak için EventLog sağlayıcısını öğesine <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="c535d-466">Günlük kaydı düzeyini appSettings içindeki `Logging:LogLevel:Default` anahtarla ayarlayın *. Production. JSON* dosyası.</span><span class="sxs-lookup"><span data-stu-id="c535d-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="c535d-467">Örnek uygulamadan aşağıdaki örnekte, `RunAsCustomService` uygulama içindeki ömür olaylarını işlemek için yerine <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c535d-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="c535d-468">Daha fazla bilgi için [olayları başlatma ve durdurma olaylarını](#handle-starting-and-stopping-events) inceleyin bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="c535d-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="c535d-469">Dağıtım türü</span><span class="sxs-lookup"><span data-stu-id="c535d-469">Deployment type</span></span>

<span data-ttu-id="c535d-470">Dağıtım senaryoları hakkında bilgi ve öneriler için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="c535d-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="c535d-471">SDK</span><span class="sxs-lookup"><span data-stu-id="c535d-471">SDK</span></span>

<span data-ttu-id="c535d-472">Razor Sayfaları veya MVC çerçevelerini kullanan bir Web uygulaması tabanlı hizmet için, proje dosyasında Web SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="c535d-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="c535d-473">Hizmet yalnızca arka plan görevlerini (örneğin, [barındırılan hizmetler](xref:fundamentals/host/hosted-services)) yürütülüyorsa, proje dosyasında çalışan SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="c535d-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="c535d-474">Çerçeveye bağımlı dağıtım (FDD)</span><span class="sxs-lookup"><span data-stu-id="c535d-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="c535d-475">Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core 'un paylaşılan sistem genelindeki bir sürümünün varlığına dayanır.</span><span class="sxs-lookup"><span data-stu-id="c535d-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="c535d-476">Bu makaledeki kılavuzdan sonra FDD senaryosu benimsendiği zaman SDK, *çerçeveye bağlı yürütülebilir dosya*olarak adlandırılan yürütülebilir bir dosya (*. exe*) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c535d-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="c535d-477">Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) ([\<runtimeıdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)), hedef Framework 'ü içerir.</span><span class="sxs-lookup"><span data-stu-id="c535d-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="c535d-478">Aşağıdaki örnekte, RID olarak `win7-x64`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c535d-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="c535d-479">`<SelfContained>` Özelliği olarak `false`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c535d-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="c535d-480">Bu özellikler SDK 'nın Windows için bir yürütülebilir (*. exe*) dosya ve paylaşılan .NET Core çerçevesine bağlı bir uygulama oluşturmasını ister.</span><span class="sxs-lookup"><span data-stu-id="c535d-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="c535d-481">`<UseAppHost>` Özelliği olarak `true`ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c535d-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="c535d-482">Bu özellik, bir FDD için bir etkinleştirme yolu (yürütülebilir, *. exe*) ile hizmeti sağlar.</span><span class="sxs-lookup"><span data-stu-id="c535d-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="c535d-483">Bir ASP.NET Core uygulaması yayımlandığında normalde üretilen bir *Web. config* dosyası, Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="c535d-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="c535d-484">*Web. config* dosyasının oluşturulmasını devre dışı bırakmak için, `<IsTransformWebConfigDisabled>` özelliğini öğesine `true`ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="c535d-485">Kendi içinde dağıtım (SCD)</span><span class="sxs-lookup"><span data-stu-id="c535d-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="c535d-486">Kendinden bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir Framework varlığına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="c535d-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="c535d-487">Çalışma zamanı ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="c535d-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="c535d-488">Hedef Framework 'ü içeren ' de `<PropertyGroup>` bir Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) bulunur:</span><span class="sxs-lookup"><span data-stu-id="c535d-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="c535d-489">Birden çok RID için yayımlamak için:</span><span class="sxs-lookup"><span data-stu-id="c535d-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="c535d-490">RID 'leri, noktalı virgülle ayrılmış bir liste olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="c535d-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="c535d-491">>(plural) adlı [ \<runtimetanımlayıcılar](/dotnet/core/tools/csproj#runtimeidentifiers) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="c535d-492">Daha fazla bilgi için bkz. [.NET Core RID kataloğu](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="c535d-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="c535d-493">Bir `<SelfContained>` özellik şu şekilde `true`ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="c535d-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="c535d-494">Hizmet Kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="c535d-494">Service user account</span></span>

<span data-ttu-id="c535d-495">Bir hizmet için Kullanıcı hesabı oluşturmak için, bir yönetim PowerShell 6 komut kabuğundan [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="c535d-496">Windows 10 Ekim 2018 Güncelleştirmesi (sürüm 1809/Build 10.0.17763) veya sonraki sürümler:</span><span class="sxs-lookup"><span data-stu-id="c535d-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="c535d-497">Windows 10 Ekim 2018 (sürüm 1809/Build 10.0.17763) sürümünden önceki Windows IŞLETIM sistemlerinde:</span><span class="sxs-lookup"><span data-stu-id="c535d-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="c535d-498">İstendiğinde [güçlü bir parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.</span><span class="sxs-lookup"><span data-stu-id="c535d-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="c535d-499">`-AccountExpires` Parametresi [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ine bir süre sonu <xref:System.DateTime>ile sağlanmamışsa hesabın süresi dolmaz.</span><span class="sxs-lookup"><span data-stu-id="c535d-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="c535d-500">Daha fazla bilgi için bkz. [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [hizmet Kullanıcı hesapları](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="c535d-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="c535d-501">Active Directory kullanırken kullanıcıları yönetmeye yönelik alternatif bir yaklaşım, yönetilen hizmet hesaplarını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="c535d-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="c535d-502">Daha fazla bilgi için bkz. [Grup yönetilen hizmet hesaplarına genel bakış](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="c535d-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="c535d-503">Hizmet hakları olarak oturum açma</span><span class="sxs-lookup"><span data-stu-id="c535d-503">Log on as a service rights</span></span>

<span data-ttu-id="c535d-504">Hizmet Kullanıcı hesabı için *hizmet hakları olarak oturum* açma oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="c535d-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="c535d-505">Yerel Güvenlik Ilkesi düzenleyicisini, *secpol. msc*' i çalıştırarak açın.</span><span class="sxs-lookup"><span data-stu-id="c535d-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="c535d-506">**Yerel ilkeler** düğümünü genişletin ve **Kullanıcı hakları ataması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="c535d-507">**Hizmet olarak oturum** açma ilkesi açın.</span><span class="sxs-lookup"><span data-stu-id="c535d-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="c535d-508">**Kullanıcı veya Grup Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="c535d-509">Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (Kullanıcı hesabı) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="c535d-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="c535d-510">Kullanıcı hesabını (`{DOMAIN OR COMPUTER NAME\USER}`) nesne adı alanına yazın ve kullanıcıyı ilkeye eklemek için **Tamam** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="c535d-511">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-511">Select **Advanced**.</span></span> <span data-ttu-id="c535d-512">**Şimdi bul**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-512">Select **Find Now**.</span></span> <span data-ttu-id="c535d-513">Listeden Kullanıcı hesabını seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-513">Select the user account from the list.</span></span> <span data-ttu-id="c535d-514">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-514">Select **OK**.</span></span> <span data-ttu-id="c535d-515">Kullanıcıyı ilkeye eklemek için yeniden **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="c535d-516">Değişiklikleri kabul etmek için **Tamam ' ı** veya **Uygula** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="c535d-517">Windows hizmetini oluşturma ve yönetme</span><span class="sxs-lookup"><span data-stu-id="c535d-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="c535d-518">Hizmet oluşturma</span><span class="sxs-lookup"><span data-stu-id="c535d-518">Create a service</span></span>

<span data-ttu-id="c535d-519">Bir hizmeti kaydetmek için PowerShell komutlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="c535d-520">Bir yönetim PowerShell 6 komut kabuğundan aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="c535d-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="c535d-521">`{EXE PATH}`&ndash; Konaktaki uygulamanın klasörünün yolu (örneğin, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="c535d-521">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="c535d-522">Uygulamanın yürütülebilir dosyasını yola eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="c535d-523">Sondaki eğik çizgi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="c535d-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="c535d-524">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Hizmet Kullanıcı hesabı (örneğin, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="c535d-524">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="c535d-525">`{SERVICE NAME}`&ndash; Hizmet adı (örneğin, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="c535d-525">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="c535d-526">`{EXE FILE PATH}`&ndash; Uygulamanın yürütülebilir yolu (örneğin, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="c535d-526">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="c535d-527">Yürütülebilir dosyanın dosya adını uzantısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="c535d-528">`{DESCRIPTION}`&ndash; Hizmet açıklaması (örneğin, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="c535d-528">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="c535d-529">`{DISPLAY NAME}`&ndash; Hizmet görünen adı (örneğin, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="c535d-529">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="c535d-530">Bir hizmet başlatın</span><span class="sxs-lookup"><span data-stu-id="c535d-530">Start a service</span></span>

<span data-ttu-id="c535d-531">Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:</span><span class="sxs-lookup"><span data-stu-id="c535d-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="c535d-532">Komutun başlatılması birkaç saniye sürer.</span><span class="sxs-lookup"><span data-stu-id="c535d-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="c535d-533">Hizmetin durumunu belirleme</span><span class="sxs-lookup"><span data-stu-id="c535d-533">Determine a service's status</span></span>

<span data-ttu-id="c535d-534">Bir hizmetin durumunu denetlemek için aşağıdaki PowerShell 6 komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="c535d-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="c535d-535">Durum, aşağıdaki değerlerden biri olarak bildirilir:</span><span class="sxs-lookup"><span data-stu-id="c535d-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="c535d-536">Bir hizmet durdurun</span><span class="sxs-lookup"><span data-stu-id="c535d-536">Stop a service</span></span>

<span data-ttu-id="c535d-537">Aşağıdaki PowerShell 6 komutuyla bir hizmeti durdurun:</span><span class="sxs-lookup"><span data-stu-id="c535d-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="c535d-538">Hizmeti Kaldır</span><span class="sxs-lookup"><span data-stu-id="c535d-538">Remove a service</span></span>

<span data-ttu-id="c535d-539">Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki PowerShell 6 komutuyla bir hizmeti kaldırın:</span><span class="sxs-lookup"><span data-stu-id="c535d-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="c535d-540">Olayları başlatma ve durdurma olaylarını işleme</span><span class="sxs-lookup"><span data-stu-id="c535d-540">Handle starting and stopping events</span></span>

<span data-ttu-id="c535d-541">, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>Ve <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> olaylarını işlemek için:</span><span class="sxs-lookup"><span data-stu-id="c535d-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="c535d-542">`OnStarting`, `OnStarted`Ve `OnStopping` yöntemleriyle türetilen <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> bir sınıf oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c535d-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="c535d-543">' A geçişi <xref:Microsoft.AspNetCore.Hosting.IWebHost> `CustomWebHostService` için bir genişletme yöntemi oluşturun <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="c535d-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="c535d-544">İçinde `Program.Main`, yerine `RunAsCustomService` genişletme yöntemini çağırın <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="c535d-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="c535d-545">Konumunu <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> görmek Için `Program.Main`, [dağıtım türü](#deployment-type) bölümünde gösterilen kod örneğine bakın.</span><span class="sxs-lookup"><span data-stu-id="c535d-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c535d-546">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="c535d-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c535d-547">Internet 'ten veya şirket ağından gelen isteklerle etkileşime geçen ve bir ara sunucu veya yük dengeleyicinin arkasındaki Hizmetler ek yapılandırma gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="c535d-548">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="c535d-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="c535d-549">Uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c535d-549">Configure endpoints</span></span>

<span data-ttu-id="c535d-550">Varsayılan olarak, ASP.NET Core öğesine `http://localhost:5000`bağlanır.</span><span class="sxs-lookup"><span data-stu-id="c535d-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="c535d-551">`ASPNETCORE_URLS` Ortam değişkenini ayarlayarak URL 'yi ve bağlantı noktasını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="c535d-552">Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="c535d-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="c535d-553">Yukarıdaki kılavuz, HTTPS uç noktaları için desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="c535d-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="c535d-554">Örneğin, bir Windows hizmeti ile kimlik doğrulaması kullanıldığında HTTPS için uygulamayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="c535d-555">Hizmet uç noktasının güvenliğini sağlamak için ASP.NET Core HTTPS geliştirme sertifikası kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c535d-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="c535d-556">Geçerli dizin ve içerik kökü</span><span class="sxs-lookup"><span data-stu-id="c535d-556">Current directory and content root</span></span>

<span data-ttu-id="c535d-557">Windows hizmeti için çağırarak <xref:System.IO.Directory.GetCurrentDirectory*> döndürülen geçerli çalışma dizini *C:\\Windows\\system32* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="c535d-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="c535d-558">*System32* klasörü, bir hizmetin dosyalarını (örneğin, ayarlar dosyaları) depolamak için uygun bir konum değildir.</span><span class="sxs-lookup"><span data-stu-id="c535d-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="c535d-559">Bir hizmetin varlık ve ayar dosyalarını sürdürmek ve erişmek için aşağıdaki yaklaşımlardan birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c535d-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="c535d-560">Uygulamanın klasörü için içerik kök yolunu ayarla</span><span class="sxs-lookup"><span data-stu-id="c535d-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="c535d-561">, <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> Bir hizmet oluşturulduğunda `binPath` bağımsız değişkene aynı yol olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="c535d-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="c535d-562">Ayarlar dosyalarına yollar `GetCurrentDirectory` oluşturmak için çağırmak yerine, uygulamanın <xref:System.IO.Directory.SetCurrentDirectory*> [içerik kökünün](xref:fundamentals/index#content-root)yolunu çağırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="c535d-563">İçinde `Program.Main`, hizmetin yürütülebilir dosyasının yolunu belirleyin ve uygulamanın içerik kökünü oluşturmak için yolu kullanın:</span><span class="sxs-lookup"><span data-stu-id="c535d-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="c535d-564">Hizmetin dosyalarını diskte uygun bir konumda depolayın</span><span class="sxs-lookup"><span data-stu-id="c535d-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="c535d-565">Dosyaları içeren klasöre kullanırken <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> ile mutlak bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="c535d-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="c535d-566">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="c535d-566">Troubleshoot</span></span>

<span data-ttu-id="c535d-567">Bir Windows hizmet uygulaması sorunlarını gidermek için bkz <xref:test/troubleshoot>..</span><span class="sxs-lookup"><span data-stu-id="c535d-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="c535d-568">Sık karşılaşılan hatalar</span><span class="sxs-lookup"><span data-stu-id="c535d-568">Common errors</span></span>

* <span data-ttu-id="c535d-569">PowerShell 'in eski veya yayın öncesi sürümü kullanımda.</span><span class="sxs-lookup"><span data-stu-id="c535d-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="c535d-570">Kayıtlı hizmet, [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutundan uygulamanın **yayımlanmış** çıktısını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="c535d-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="c535d-571">[DotNet Build](/dotnet/core/tools/dotnet-build) komutunun çıkışı, uygulama dağıtımı için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c535d-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="c535d-572">Yayımlanan varlıklar, dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:</span><span class="sxs-lookup"><span data-stu-id="c535d-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="c535d-573">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="c535d-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="c535d-574">*bin/Release/{Target Framework}/{RUNTIME tanımlayıcısı}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="c535d-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="c535d-575">Hizmet çalışır durumda değil.</span><span class="sxs-lookup"><span data-stu-id="c535d-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="c535d-576">Uygulamanın kullandığı kaynakların yolları (örneğin, sertifikalar) yanlış.</span><span class="sxs-lookup"><span data-stu-id="c535d-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="c535d-577">Bir Windows hizmetinin temel yolu *c:\\Windows\\system32*' dir.</span><span class="sxs-lookup"><span data-stu-id="c535d-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="c535d-578">Kullanıcı, hizmet hakları *olarak oturum* açma hakkına sahip değil.</span><span class="sxs-lookup"><span data-stu-id="c535d-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="c535d-579">`New-Service` PowerShell komutu çalıştırılırken kullanıcının parolasının kullanım dışı veya yanlış şekilde geçirilmesi.</span><span class="sxs-lookup"><span data-stu-id="c535d-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="c535d-580">Uygulama ASP.NET Core kimlik doğrulaması gerektiriyor, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="c535d-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="c535d-581">İstek URL 'SI bağlantı noktası yanlış veya uygulamada doğru şekilde yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="c535d-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="c535d-582">Sistem ve uygulama olay günlükleri</span><span class="sxs-lookup"><span data-stu-id="c535d-582">System and Application Event Logs</span></span>

<span data-ttu-id="c535d-583">Sistem ve uygulama olay günlüklerine erişin:</span><span class="sxs-lookup"><span data-stu-id="c535d-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="c535d-584">Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="c535d-585">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="c535d-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="c535d-586">Sistem olay günlüğünü açmak için **sistem** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="c535d-587">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="c535d-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="c535d-588">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="c535d-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="c535d-589">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="c535d-589">Run the app at a command prompt</span></span>

<span data-ttu-id="c535d-590">Birçok başlatma hatası olay günlüklerinde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="c535d-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="c535d-591">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c535d-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="c535d-592">Uygulamadan ek ayrıntı günlüğe kaydetmek için, [günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [geliştirme ortamında](xref:fundamentals/environments)uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="c535d-593">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="c535d-593">Clear package caches</span></span>

<span data-ttu-id="c535d-594">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="c535d-595">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="c535d-596">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="c535d-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="c535d-597">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="c535d-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="c535d-598">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="c535d-599">Paket önbelleklerini Temizleme, [NuGet. exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komut `nuget locals all -clear`yürütülebilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="c535d-600">*NuGet. exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c535d-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="c535d-601">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="c535d-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="c535d-602">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="c535d-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="c535d-603">Yavaş veya askıda olan uygulama</span><span class="sxs-lookup"><span data-stu-id="c535d-603">Slow or hanging app</span></span>

<span data-ttu-id="c535d-604">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="c535d-605">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="c535d-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="c535d-606">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="c535d-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="c535d-607">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="c535d-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="c535d-608">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) uygulama yürütülebilir adıyla çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c535d-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="c535d-609">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c535d-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="c535d-610">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c535d-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="c535d-611">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="c535d-612">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="c535d-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="c535d-613">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="c535d-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="c535d-614">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="c535d-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="c535d-615">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="c535d-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="c535d-616">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="c535d-616">Analyze the dump</span></span>

<span data-ttu-id="c535d-617">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="c535d-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="c535d-618">Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="c535d-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c535d-619">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c535d-619">Additional resources</span></span>

* <span data-ttu-id="c535d-620">[Kestrel uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (https yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="c535d-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
