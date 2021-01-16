---
title: Windows hizmetinde konak ASP.NET Core
author: rick-anderson
description: ASP.NET Core uygulamasının bir Windows hizmetinde nasıl barındıralınacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: 63267bf938c6d16b8a1b13940a4b3f8a02d1a1e4
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252753"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="10f42-103">Windows hizmetinde konak ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10f42-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10f42-104">Bir ASP.NET Core uygulaması, IIS kullanmadan Windows [hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak Windows üzerinde barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="10f42-105">Windows hizmeti olarak barındırıldığı zaman, uygulama otomatik olarak sunucu yeniden başlatıldıktan sonra başlatılır.</span><span class="sxs-lookup"><span data-stu-id="10f42-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="10f42-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="10f42-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="10f42-107">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="10f42-107">Prerequisites</span></span>

* [<span data-ttu-id="10f42-108">ASP.NET Core SDK 2,1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="10f42-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="10f42-109">PowerShell 6,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="10f42-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="10f42-110">Çalışan hizmeti şablonu</span><span class="sxs-lookup"><span data-stu-id="10f42-110">Worker Service template</span></span>

<span data-ttu-id="10f42-111">ASP.NET Core Worker hizmeti şablonu, uzun süre çalışan hizmet uygulamalarını yazmak için bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="10f42-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="10f42-112">Şablonu bir Windows Hizmeti uygulamasının temeli olarak kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="10f42-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="10f42-113">.NET Core şablonundan bir çalışan hizmeti uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="10f42-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="10f42-114">Çalışan hizmeti uygulamasını bir Windows hizmeti olarak çalışacak şekilde güncelleştirmek için [uygulama yapılandırma](#app-configuration) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="10f42-115">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="10f42-115">App configuration</span></span>

<span data-ttu-id="10f42-116">Uygulama, [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)için bir paket başvurusu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="10f42-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="10f42-117">`IHostBuilder.UseWindowsService` Ana bilgisayar oluşturulurken çağrılır.</span><span class="sxs-lookup"><span data-stu-id="10f42-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="10f42-118">Uygulama bir Windows hizmeti olarak çalışıyorsa, yöntemi:</span><span class="sxs-lookup"><span data-stu-id="10f42-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="10f42-119">Ana bilgisayar ömrünü olarak ayarlar `WindowsServiceLifetime` .</span><span class="sxs-lookup"><span data-stu-id="10f42-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="10f42-120">[İçerik kökünü](xref:fundamentals/index#content-root) [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory)olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="10f42-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="10f42-121">Daha fazla bilgi için [geçerli dizin ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="10f42-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="10f42-122">Olay günlüğüne kaydetmeyi sağlar:</span><span class="sxs-lookup"><span data-stu-id="10f42-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="10f42-123">Uygulama adı varsayılan kaynak adı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="10f42-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="10f42-124">Varsayılan günlük düzeyi,  `CreateDefaultBuilder` ana bilgisayarı oluşturmak için çağıran ASP.NET Core şablonuna dayalı bir uygulama için uyarı veya daha yüksek bir uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="10f42-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="10f42-125">Varsayılan günlük düzeyini `Logging:EventLog:LogLevel:Default` appSettings içindeki anahtarla geçersiz kılın *appsettings.json* / *. { Environment}. JSON* veya diğer yapılandırma sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="10f42-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="10f42-126">Yeni olay kaynakları yalnızca yöneticiler tarafından oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="10f42-127">Uygulama adı kullanılarak bir olay kaynağı oluşturuoluşturumadığında, *uygulama* kaynağına bir uyarı kaydedilir ve olay günlükleri devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="10f42-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="10f42-128">`CreateHostBuilder` *Program.cs*' de:</span><span class="sxs-lookup"><span data-stu-id="10f42-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="10f42-129">Aşağıdaki örnek uygulamalar bu konuya eşlik eder:</span><span class="sxs-lookup"><span data-stu-id="10f42-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="10f42-130">Arka plan çalışan hizmeti örneği: arka plan görevleri için [barındırılan Hizmetleri](xref:fundamentals/host/hosted-services) kullanan [çalışan hizmeti şablonunu](#worker-service-template) temel alan, Web olmayan bir uygulama örneği.</span><span class="sxs-lookup"><span data-stu-id="10f42-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="10f42-131">Web App Service örneği: Razor arka plan görevleri için [barındırılan hizmetlerle](xref:fundamentals/host/hosted-services) Windows hizmeti olarak çalışan bir sayfalar Web uygulaması örneği.</span><span class="sxs-lookup"><span data-stu-id="10f42-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="10f42-132">MVC Kılavuzu için ve altındaki makalelere bakın <xref:mvc/overview> <xref:migration/22-to-30> .</span><span class="sxs-lookup"><span data-stu-id="10f42-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="10f42-133">Dağıtım türü</span><span class="sxs-lookup"><span data-stu-id="10f42-133">Deployment type</span></span>

<span data-ttu-id="10f42-134">Dağıtım senaryoları hakkında bilgi ve öneriler için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="10f42-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="10f42-135">SDK</span><span class="sxs-lookup"><span data-stu-id="10f42-135">SDK</span></span>

<span data-ttu-id="10f42-136">Sayfaları veya MVC çerçevelerini kullanan bir Web uygulaması tabanlı hizmet için Razor , proje dosyasında Web SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="10f42-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="10f42-137">Hizmet yalnızca arka plan görevlerini (örneğin, [barındırılan hizmetler](xref:fundamentals/host/hosted-services)) yürütülüyorsa, proje dosyasında çalışan SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="10f42-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="10f42-138">Çerçeveye bağımlı dağıtım (FDD)</span><span class="sxs-lookup"><span data-stu-id="10f42-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="10f42-139">Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core 'un paylaşılan sistem genelindeki bir sürümünün varlığına dayanır.</span><span class="sxs-lookup"><span data-stu-id="10f42-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="10f42-140">Bu makaledeki kılavuzdan sonra FDD senaryosu benimsendiği zaman SDK, *çerçeveye bağlı yürütülebilir dosya* olarak adlandırılan yürütülebilir bir dosya (*. exe*) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="10f42-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="10f42-141">[Web SDK 'sını](#sdk)kullanıyorsanız, normalde bir ASP.NET Core uygulaması yayımlarken üretilen bir *web.config* dosyası Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="10f42-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="10f42-142">*web.config* dosyasının oluşturulmasını devre dışı bırakmak için, `<IsTransformWebConfigDisabled>` özelliği öğesine ekleyin `true` .</span><span class="sxs-lookup"><span data-stu-id="10f42-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="10f42-143">Kendi içinde dağıtım (SCD)</span><span class="sxs-lookup"><span data-stu-id="10f42-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="10f42-144">Kendinden bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir Framework varlığına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="10f42-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="10f42-145">Çalışma zamanı ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="10f42-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="10f42-146">Hedef Framework 'ü içeren ' de bir Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) bulunur `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="10f42-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="10f42-147">Birden çok RID için yayımlamak için:</span><span class="sxs-lookup"><span data-stu-id="10f42-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="10f42-148">RID 'leri, noktalı virgülle ayrılmış bir liste olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="10f42-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="10f42-149">Özellik adını [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural) kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="10f42-150">Daha fazla bilgi için bkz. [.NET Core RID kataloğu](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="10f42-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="10f42-151">Hizmet Kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="10f42-151">Service user account</span></span>

<span data-ttu-id="10f42-152">Bir hizmet için Kullanıcı hesabı oluşturmak için, bir yönetim PowerShell 6 komut kabuğundan [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ini kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="10f42-153">Windows 10 Ekim 2018 Güncelleştirmesi (sürüm 1809/Build 10.0.17763) veya sonraki sürümler:</span><span class="sxs-lookup"><span data-stu-id="10f42-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="10f42-154">Windows 10 Ekim 2018 (sürüm 1809/Build 10.0.17763) sürümünden önceki Windows IŞLETIM sistemlerinde:</span><span class="sxs-lookup"><span data-stu-id="10f42-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="10f42-155">İstendiğinde [güçlü bir parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.</span><span class="sxs-lookup"><span data-stu-id="10f42-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="10f42-156">`-AccountExpires`Parametresi [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ine bir süre sonu ile sağlanmamışsa <xref:System.DateTime> hesabın süresi dolmaz.</span><span class="sxs-lookup"><span data-stu-id="10f42-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="10f42-157">Daha fazla bilgi için bkz. [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [hizmet Kullanıcı hesapları](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="10f42-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="10f42-158">Active Directory kullanırken kullanıcıları yönetmeye yönelik alternatif bir yaklaşım, yönetilen hizmet hesaplarını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="10f42-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="10f42-159">Daha fazla bilgi için bkz. [Grup yönetilen hizmet hesaplarına genel bakış](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="10f42-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="10f42-160">Hizmet hakları olarak oturum açma</span><span class="sxs-lookup"><span data-stu-id="10f42-160">Log on as a service rights</span></span>

<span data-ttu-id="10f42-161">Hizmet Kullanıcı hesabı için *hizmet hakları olarak oturum* açma oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="10f42-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="10f42-162">Yerel Güvenlik Ilkesi düzenleyicisini, *secpol. msc*' i çalıştırarak açın.</span><span class="sxs-lookup"><span data-stu-id="10f42-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="10f42-163">**Yerel ilkeler** düğümünü genişletin ve **Kullanıcı hakları ataması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="10f42-164">**Hizmet olarak oturum** açma ilkesi açın.</span><span class="sxs-lookup"><span data-stu-id="10f42-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="10f42-165">**Kullanıcı veya Grup Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="10f42-166">Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (Kullanıcı hesabı) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="10f42-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="10f42-167">Kullanıcı hesabını ( `{DOMAIN OR COMPUTER NAME\USER}` ) nesne adı alanına yazın ve kullanıcıyı ilkeye eklemek Için **Tamam** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="10f42-168">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-168">Select **Advanced**.</span></span> <span data-ttu-id="10f42-169">**Şimdi bul**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-169">Select **Find Now**.</span></span> <span data-ttu-id="10f42-170">Listeden Kullanıcı hesabını seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-170">Select the user account from the list.</span></span> <span data-ttu-id="10f42-171">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-171">Select **OK**.</span></span> <span data-ttu-id="10f42-172">Kullanıcıyı ilkeye eklemek için yeniden **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="10f42-173">Değişiklikleri kabul etmek için **Tamam ' ı** veya **Uygula** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="10f42-174">Windows hizmetini oluşturma ve yönetme</span><span class="sxs-lookup"><span data-stu-id="10f42-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="10f42-175">Hizmet oluşturma</span><span class="sxs-lookup"><span data-stu-id="10f42-175">Create a service</span></span>

<span data-ttu-id="10f42-176">Bir hizmeti kaydetmek için PowerShell komutlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="10f42-177">Bir yönetim PowerShell 6 komut kabuğundan aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="10f42-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="10f42-178">`{EXE PATH}`: Konaktaki uygulamanın klasörünün yolu (örneğin, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="10f42-179">Uygulamanın yürütülebilir dosyasını yola eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="10f42-180">Sondaki eğik çizgi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="10f42-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="10f42-181">`{DOMAIN OR COMPUTER NAME\USER}`: Hizmet Kullanıcı hesabı (örneğin, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="10f42-182">`{SERVICE NAME}`: Hizmet adı (örneğin, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="10f42-183">`{EXE FILE PATH}`: Uygulamanın yürütülebilir yolu (örneğin, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="10f42-184">Yürütülebilir dosyanın dosya adını uzantısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="10f42-185">`{DESCRIPTION}`: Hizmet açıklaması (örneğin, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="10f42-186">`{DISPLAY NAME}`: Hizmet görünen adı (örneğin, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="10f42-187">Bir hizmet başlatın</span><span class="sxs-lookup"><span data-stu-id="10f42-187">Start a service</span></span>

<span data-ttu-id="10f42-188">Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:</span><span class="sxs-lookup"><span data-stu-id="10f42-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="10f42-189">Komutun başlatılması birkaç saniye sürer.</span><span class="sxs-lookup"><span data-stu-id="10f42-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="10f42-190">Hizmetin durumunu belirleme</span><span class="sxs-lookup"><span data-stu-id="10f42-190">Determine a service's status</span></span>

<span data-ttu-id="10f42-191">Bir hizmetin durumunu denetlemek için aşağıdaki PowerShell 6 komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="10f42-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="10f42-192">Durum, aşağıdaki değerlerden biri olarak bildirilir:</span><span class="sxs-lookup"><span data-stu-id="10f42-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="10f42-193">Bir hizmet durdurun</span><span class="sxs-lookup"><span data-stu-id="10f42-193">Stop a service</span></span>

<span data-ttu-id="10f42-194">Aşağıdaki PowerShell 6 komutuyla bir hizmeti durdurun:</span><span class="sxs-lookup"><span data-stu-id="10f42-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="10f42-195">Hizmeti Kaldır</span><span class="sxs-lookup"><span data-stu-id="10f42-195">Remove a service</span></span>

<span data-ttu-id="10f42-196">Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki PowerShell 6 komutuyla bir hizmeti kaldırın:</span><span class="sxs-lookup"><span data-stu-id="10f42-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="10f42-197">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="10f42-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="10f42-198">Internet 'ten veya şirket ağından gelen isteklerle etkileşime geçen ve bir ara sunucu veya yük dengeleyicinin arkasındaki Hizmetler ek yapılandırma gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="10f42-199">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="10f42-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="10f42-200">Uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="10f42-200">Configure endpoints</span></span>

<span data-ttu-id="10f42-201">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="10f42-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="10f42-202">Ortam değişkenini ayarlayarak URL 'YI ve bağlantı noktasını yapılandırın `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="10f42-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="10f42-203">Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="10f42-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <xref:fundamentals/servers/kestrel/endpoints>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

* <xref:fundamentals/servers/kestrel#endpoint-configuration>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="10f42-204">Yukarıdaki kılavuz, HTTPS uç noktaları için desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="10f42-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="10f42-205">Örneğin, bir Windows hizmeti ile kimlik doğrulaması kullanıldığında HTTPS için uygulamayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="10f42-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="10f42-206">Hizmet uç noktasının güvenliğini sağlamak için ASP.NET Core HTTPS geliştirme sertifikası kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="10f42-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="10f42-207">Geçerli dizin ve içerik kökü</span><span class="sxs-lookup"><span data-stu-id="10f42-207">Current directory and content root</span></span>

<span data-ttu-id="10f42-208">Windows hizmeti için çağırarak döndürülen geçerli çalışma dizini <xref:System.IO.Directory.GetCurrentDirectory%2A> *C: \\ Windows \\ System32* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="10f42-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory%2A> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="10f42-209">*System32* klasörü, bir hizmetin dosyalarını (örneğin, ayarlar dosyaları) depolamak için uygun bir konum değildir.</span><span class="sxs-lookup"><span data-stu-id="10f42-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="10f42-210">Bir hizmetin varlık ve ayar dosyalarını sürdürmek ve erişmek için aşağıdaki yaklaşımlardan birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="10f42-211">ContentRootPath veya ContentRootFileProvider kullanın</span><span class="sxs-lookup"><span data-stu-id="10f42-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="10f42-212">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) kullanın veya <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> bir uygulamanın kaynaklarını bulun.</span><span class="sxs-lookup"><span data-stu-id="10f42-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="10f42-213">Uygulama bir hizmet olarak çalıştırıldığında, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory)olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="10f42-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="10f42-214">Uygulamanın varsayılan ayar dosyaları *appsettings.json* ve *appSettings. { '. JSON ortamı*, [konak oluşturma sırasında Createdefaultbuilder](xref:fundamentals/host/generic-host#set-up-a-host)' ı çağırarak uygulamanın içerik kökünden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="10f42-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="10f42-215">İçindeki geliştirici kodu tarafından yüklenen diğer ayar dosyaları için <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> , çağrısına gerek yoktur <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> .</span><span class="sxs-lookup"><span data-stu-id="10f42-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>.</span></span> <span data-ttu-id="10f42-216">Aşağıdaki örnekte, dosyadaki *custom_settings.js* uygulamanın içerik kökünde bulunur ve açıkça bir temel yolu ayarlamadan yüklenir:</span><span class="sxs-lookup"><span data-stu-id="10f42-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="10f42-217">Bir <xref:System.IO.Directory.GetCurrentDirectory%2A> Windows hizmeti uygulaması geçerli dizini olarak *C: \\ Windows \\ System32* klasörünü döndürdüğünden kaynak yolunu almak için kullanmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory%2A> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="10f42-218">Hizmetin dosyalarını diskte uygun bir konumda depolayın</span><span class="sxs-lookup"><span data-stu-id="10f42-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="10f42-219"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>Dosyaları içeren klasöre kullanırken ile mutlak bir yol belirtin <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="10f42-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="10f42-220">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="10f42-220">Troubleshoot</span></span>

<span data-ttu-id="10f42-221">Bir Windows hizmet uygulaması sorunlarını gidermek için bkz <xref:test/troubleshoot> ..</span><span class="sxs-lookup"><span data-stu-id="10f42-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="10f42-222">Sık karşılaşılan hatalar</span><span class="sxs-lookup"><span data-stu-id="10f42-222">Common errors</span></span>

* <span data-ttu-id="10f42-223">PowerShell 'in eski veya yayın öncesi sürümü kullanımda.</span><span class="sxs-lookup"><span data-stu-id="10f42-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="10f42-224">Kayıtlı hizmet, [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutundan uygulamanın **yayımlanmış** çıktısını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="10f42-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="10f42-225">[DotNet Build](/dotnet/core/tools/dotnet-build) komutunun çıkışı, uygulama dağıtımı için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="10f42-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="10f42-226">Yayımlanan varlıklar, dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:</span><span class="sxs-lookup"><span data-stu-id="10f42-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="10f42-227">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="10f42-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="10f42-228">*bin/Release/{Target Framework}/{RUNTIME tanımlayıcısı}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="10f42-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="10f42-229">Hizmet çalışır durumda değil.</span><span class="sxs-lookup"><span data-stu-id="10f42-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="10f42-230">Uygulamanın kullandığı kaynakların yolları (örneğin, sertifikalar) yanlış.</span><span class="sxs-lookup"><span data-stu-id="10f42-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="10f42-231">Bir Windows hizmetinin temel yolu *c: \\ Windows \\ System32*' dir.</span><span class="sxs-lookup"><span data-stu-id="10f42-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="10f42-232">Kullanıcı, hizmet hakları *olarak oturum* açma hakkına sahip değil.</span><span class="sxs-lookup"><span data-stu-id="10f42-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="10f42-233">PowerShell komutu çalıştırılırken kullanıcının parolasının kullanım dışı veya yanlış şekilde geçirilmesi `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="10f42-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="10f42-234">Uygulama ASP.NET Core kimlik doğrulaması gerektiriyor, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="10f42-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="10f42-235">İstek URL 'SI bağlantı noktası yanlış veya uygulamada doğru şekilde yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="10f42-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="10f42-236">Sistem ve uygulama olay günlükleri</span><span class="sxs-lookup"><span data-stu-id="10f42-236">System and Application Event Logs</span></span>

<span data-ttu-id="10f42-237">Sistem ve uygulama olay günlüklerine erişin:</span><span class="sxs-lookup"><span data-stu-id="10f42-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="10f42-238">Başlat menüsünü açın, *Olay Görüntüleyicisi* araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="10f42-239">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="10f42-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="10f42-240">Sistem olay günlüğünü açmak için **sistem** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="10f42-241">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="10f42-242">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="10f42-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="10f42-243">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="10f42-243">Run the app at a command prompt</span></span>

<span data-ttu-id="10f42-244">Birçok başlatma hatası olay günlüklerinde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="10f42-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="10f42-245">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10f42-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="10f42-246">Uygulamadan ek ayrıntı günlüğe kaydetmek için, [günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [geliştirme ortamında](xref:fundamentals/environments)uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="10f42-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="10f42-247">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="10f42-247">Clear package caches</span></span>

<span data-ttu-id="10f42-248">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="10f42-249">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="10f42-250">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="10f42-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="10f42-251">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="10f42-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="10f42-252">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="10f42-253">Paket önbelleklerini Temizleme, [nuget.exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komutu yürütülebilir `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="10f42-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="10f42-254">*nuget.exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="10f42-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="10f42-255">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="10f42-256">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="10f42-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="10f42-257">Yavaş veya askıda olan uygulama</span><span class="sxs-lookup"><span data-stu-id="10f42-257">Slow or hanging app</span></span>

<span data-ttu-id="10f42-258">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="10f42-259">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="10f42-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="10f42-260">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="10f42-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="10f42-261">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="10f42-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="10f42-262">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) uygulama yürütülebilir adıyla çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="10f42-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="10f42-263">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="10f42-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="10f42-264">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="10f42-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="10f42-265">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="10f42-266">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="10f42-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="10f42-267">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="10f42-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="10f42-268">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="10f42-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="10f42-269">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="10f42-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="10f42-270">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="10f42-270">Analyze the dump</span></span>

<span data-ttu-id="10f42-271">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="10f42-272">Daha fazla bilgi için bkz. [User-Mode döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="10f42-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10f42-273">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="10f42-273">Additional resources</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="10f42-274">[Kestrel uç noktası yapılandırması](xref:fundamentals/servers/kestrel/endpoints) (https yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="10f42-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"
* <span data-ttu-id="10f42-275">[Kestrel uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (https yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="10f42-275">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="10f42-276">Bir ASP.NET Core uygulaması, IIS kullanmadan Windows [hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak Windows üzerinde barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-276">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="10f42-277">Windows hizmeti olarak barındırıldığı zaman, uygulama otomatik olarak sunucu yeniden başlatıldıktan sonra başlatılır.</span><span class="sxs-lookup"><span data-stu-id="10f42-277">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="10f42-278">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="10f42-278">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="10f42-279">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="10f42-279">Prerequisites</span></span>

* [<span data-ttu-id="10f42-280">ASP.NET Core SDK 2,1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="10f42-280">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="10f42-281">PowerShell 6,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="10f42-281">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="10f42-282">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="10f42-282">App configuration</span></span>

<span data-ttu-id="10f42-283">Uygulama [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) ve [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)için paket başvuruları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="10f42-283">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="10f42-284">Bir hizmetin dışında çalışırken test ve hata ayıklamak için, uygulamanın bir hizmet olarak mı yoksa bir konsol uygulaması mi çalıştığını belirleme kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-284">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="10f42-285">Hata ayıklayıcının ekli olduğunu veya bir anahtarın mevcut olup olmadığını denetleyin `--console` .</span><span class="sxs-lookup"><span data-stu-id="10f42-285">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="10f42-286">Her iki koşul de geçerliyse (uygulama bir hizmet olarak çalıştırılmadıysa), çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="10f42-286">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="10f42-287">Koşullar yanlışsa (uygulama bir hizmet olarak çalıştırılır):</span><span class="sxs-lookup"><span data-stu-id="10f42-287">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="10f42-288"><xref:System.IO.Directory.SetCurrentDirectory*>Uygulamanın yayımlanmış konumunun yolunu çağırın ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-288">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="10f42-289"><xref:System.IO.Directory.GetCurrentDirectory*>Bir Windows hizmeti uygulaması çağrıldığında *C: \\ Windows \\ System32* klasörünü döndürdüğünden yolu almak için çağırmayın <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="10f42-289">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="10f42-290">Daha fazla bilgi için [geçerli dizin ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="10f42-290">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="10f42-291">Bu adım, uygulamanın ' de yapılandırılmadan önce gerçekleştirilir `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="10f42-291">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="10f42-292"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>Uygulamayı bir hizmet olarak çalıştırmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="10f42-292">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="10f42-293">[Komut satırı yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#command-line-configuration-provider) komut satırı bağımsız değişkenleri için ad-değer çiftleri gerektirdiğinden, `--console` bağımsız değişkenleri almadan önce anahtar bağımsız değişkenlerden kaldırılır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="10f42-293">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="10f42-294">Windows olay günlüğü 'ne yazmak için EventLog sağlayıcısını öğesine ekleyin <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="10f42-294">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="10f42-295">Günlüğe kaydetme düzeyini `Logging:LogLevel:Default` dosyadaki *appsettings.Production.js* anahtarla ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="10f42-295">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="10f42-296">Örnek uygulamadan aşağıdaki örnekte, `RunAsCustomService` <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> uygulama içindeki ömür olaylarını işlemek için yerine çağrılır.</span><span class="sxs-lookup"><span data-stu-id="10f42-296">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="10f42-297">Daha fazla bilgi için [olayları başlatma ve durdurma olaylarını](#handle-starting-and-stopping-events) inceleyin bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="10f42-297">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="10f42-298">Dağıtım türü</span><span class="sxs-lookup"><span data-stu-id="10f42-298">Deployment type</span></span>

<span data-ttu-id="10f42-299">Dağıtım senaryoları hakkında bilgi ve öneriler için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="10f42-299">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="10f42-300">SDK</span><span class="sxs-lookup"><span data-stu-id="10f42-300">SDK</span></span>

<span data-ttu-id="10f42-301">Sayfaları veya MVC çerçevelerini kullanan bir Web uygulaması tabanlı hizmet için Razor , proje dosyasında Web SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="10f42-301">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="10f42-302">Hizmet yalnızca arka plan görevlerini (örneğin, [barındırılan hizmetler](xref:fundamentals/host/hosted-services)) yürütülüyorsa, proje dosyasında çalışan SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="10f42-302">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="10f42-303">Çerçeveye bağımlı dağıtım (FDD)</span><span class="sxs-lookup"><span data-stu-id="10f42-303">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="10f42-304">Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core 'un paylaşılan sistem genelindeki bir sürümünün varlığına dayanır.</span><span class="sxs-lookup"><span data-stu-id="10f42-304">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="10f42-305">Bu makaledeki kılavuzdan sonra FDD senaryosu benimsendiği zaman SDK, *çerçeveye bağlı yürütülebilir dosya* olarak adlandırılan yürütülebilir bir dosya (*. exe*) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="10f42-305">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="10f42-306">Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) hedef Framework 'ü içerir.</span><span class="sxs-lookup"><span data-stu-id="10f42-306">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="10f42-307">Aşağıdaki örnekte, RID olarak ayarlanır `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="10f42-307">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="10f42-308">`<SelfContained>` özelliği `false` olarak ayarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="10f42-308">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="10f42-309">Bu özellikler SDK 'nın Windows için bir yürütülebilir (*. exe*) dosya ve paylaşılan .NET Core çerçevesine bağlı bir uygulama oluşturmasını ister.</span><span class="sxs-lookup"><span data-stu-id="10f42-309">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="10f42-310">Bir ASP.NET Core uygulaması yayımlandığında normalde oluşturulan *web.config* dosyası, Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="10f42-310">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="10f42-311">*web.config* dosyasının oluşturulmasını devre dışı bırakmak için, `<IsTransformWebConfigDisabled>` özelliği öğesine ekleyin `true` .</span><span class="sxs-lookup"><span data-stu-id="10f42-311">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="10f42-312">Kendi içinde dağıtım (SCD)</span><span class="sxs-lookup"><span data-stu-id="10f42-312">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="10f42-313">Kendinden bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir Framework varlığına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="10f42-313">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="10f42-314">Çalışma zamanı ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="10f42-314">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="10f42-315">Hedef Framework 'ü içeren ' de bir Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) bulunur `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="10f42-315">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="10f42-316">Birden çok RID için yayımlamak için:</span><span class="sxs-lookup"><span data-stu-id="10f42-316">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="10f42-317">RID 'leri, noktalı virgülle ayrılmış bir liste olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="10f42-317">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="10f42-318">Özellik adını [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural) kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-318">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="10f42-319">Daha fazla bilgi için bkz. [.NET Core RID kataloğu](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="10f42-319">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="10f42-320">Bir `<SelfContained>` özellik şu şekilde ayarlanır `true` :</span><span class="sxs-lookup"><span data-stu-id="10f42-320">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="10f42-321">Hizmet Kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="10f42-321">Service user account</span></span>

<span data-ttu-id="10f42-322">Bir hizmet için Kullanıcı hesabı oluşturmak için, bir yönetim PowerShell 6 komut kabuğundan [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ini kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-322">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="10f42-323">Windows 10 Ekim 2018 Güncelleştirmesi (sürüm 1809/Build 10.0.17763) veya sonraki sürümler:</span><span class="sxs-lookup"><span data-stu-id="10f42-323">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="10f42-324">Windows 10 Ekim 2018 (sürüm 1809/Build 10.0.17763) sürümünden önceki Windows IŞLETIM sistemlerinde:</span><span class="sxs-lookup"><span data-stu-id="10f42-324">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="10f42-325">İstendiğinde [güçlü bir parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.</span><span class="sxs-lookup"><span data-stu-id="10f42-325">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="10f42-326">`-AccountExpires`Parametresi [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ine bir süre sonu ile sağlanmamışsa <xref:System.DateTime> hesabın süresi dolmaz.</span><span class="sxs-lookup"><span data-stu-id="10f42-326">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="10f42-327">Daha fazla bilgi için bkz. [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [hizmet Kullanıcı hesapları](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="10f42-327">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="10f42-328">Active Directory kullanırken kullanıcıları yönetmeye yönelik alternatif bir yaklaşım, yönetilen hizmet hesaplarını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="10f42-328">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="10f42-329">Daha fazla bilgi için bkz. [Grup yönetilen hizmet hesaplarına genel bakış](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="10f42-329">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="10f42-330">Hizmet hakları olarak oturum açma</span><span class="sxs-lookup"><span data-stu-id="10f42-330">Log on as a service rights</span></span>

<span data-ttu-id="10f42-331">Hizmet Kullanıcı hesabı için *hizmet hakları olarak oturum* açma oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="10f42-331">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="10f42-332">Yerel Güvenlik Ilkesi düzenleyicisini, *secpol. msc*' i çalıştırarak açın.</span><span class="sxs-lookup"><span data-stu-id="10f42-332">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="10f42-333">**Yerel ilkeler** düğümünü genişletin ve **Kullanıcı hakları ataması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-333">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="10f42-334">**Hizmet olarak oturum** açma ilkesi açın.</span><span class="sxs-lookup"><span data-stu-id="10f42-334">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="10f42-335">**Kullanıcı veya Grup Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-335">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="10f42-336">Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (Kullanıcı hesabı) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="10f42-336">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="10f42-337">Kullanıcı hesabını ( `{DOMAIN OR COMPUTER NAME\USER}` ) nesne adı alanına yazın ve kullanıcıyı ilkeye eklemek Için **Tamam** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-337">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="10f42-338">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-338">Select **Advanced**.</span></span> <span data-ttu-id="10f42-339">**Şimdi bul**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-339">Select **Find Now**.</span></span> <span data-ttu-id="10f42-340">Listeden Kullanıcı hesabını seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-340">Select the user account from the list.</span></span> <span data-ttu-id="10f42-341">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-341">Select **OK**.</span></span> <span data-ttu-id="10f42-342">Kullanıcıyı ilkeye eklemek için yeniden **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-342">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="10f42-343">Değişiklikleri kabul etmek için **Tamam ' ı** veya **Uygula** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-343">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="10f42-344">Windows hizmetini oluşturma ve yönetme</span><span class="sxs-lookup"><span data-stu-id="10f42-344">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="10f42-345">Hizmet oluşturma</span><span class="sxs-lookup"><span data-stu-id="10f42-345">Create a service</span></span>

<span data-ttu-id="10f42-346">Bir hizmeti kaydetmek için PowerShell komutlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-346">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="10f42-347">Bir yönetim PowerShell 6 komut kabuğundan aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="10f42-347">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="10f42-348">`{EXE PATH}`: Konaktaki uygulamanın klasörünün yolu (örneğin, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-348">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="10f42-349">Uygulamanın yürütülebilir dosyasını yola eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-349">Don't include the app's executable in the path.</span></span> <span data-ttu-id="10f42-350">Sondaki eğik çizgi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="10f42-350">A trailing slash isn't required.</span></span>
* <span data-ttu-id="10f42-351">`{DOMAIN OR COMPUTER NAME\USER}`: Hizmet Kullanıcı hesabı (örneğin, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-351">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="10f42-352">`{SERVICE NAME}`: Hizmet adı (örneğin, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-352">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="10f42-353">`{EXE FILE PATH}`: Uygulamanın yürütülebilir yolu (örneğin, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-353">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="10f42-354">Yürütülebilir dosyanın dosya adını uzantısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-354">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="10f42-355">`{DESCRIPTION}`: Hizmet açıklaması (örneğin, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-355">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="10f42-356">`{DISPLAY NAME}`: Hizmet görünen adı (örneğin, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-356">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="10f42-357">Bir hizmet başlatın</span><span class="sxs-lookup"><span data-stu-id="10f42-357">Start a service</span></span>

<span data-ttu-id="10f42-358">Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:</span><span class="sxs-lookup"><span data-stu-id="10f42-358">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="10f42-359">Komutun başlatılması birkaç saniye sürer.</span><span class="sxs-lookup"><span data-stu-id="10f42-359">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="10f42-360">Hizmetin durumunu belirleme</span><span class="sxs-lookup"><span data-stu-id="10f42-360">Determine a service's status</span></span>

<span data-ttu-id="10f42-361">Bir hizmetin durumunu denetlemek için aşağıdaki PowerShell 6 komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="10f42-361">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="10f42-362">Durum, aşağıdaki değerlerden biri olarak bildirilir:</span><span class="sxs-lookup"><span data-stu-id="10f42-362">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="10f42-363">Bir hizmet durdurun</span><span class="sxs-lookup"><span data-stu-id="10f42-363">Stop a service</span></span>

<span data-ttu-id="10f42-364">Aşağıdaki PowerShell 6 komutuyla bir hizmeti durdurun:</span><span class="sxs-lookup"><span data-stu-id="10f42-364">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="10f42-365">Hizmeti Kaldır</span><span class="sxs-lookup"><span data-stu-id="10f42-365">Remove a service</span></span>

<span data-ttu-id="10f42-366">Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki PowerShell 6 komutuyla bir hizmeti kaldırın:</span><span class="sxs-lookup"><span data-stu-id="10f42-366">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="10f42-367">Olayları başlatma ve durdurma olaylarını işleme</span><span class="sxs-lookup"><span data-stu-id="10f42-367">Handle starting and stopping events</span></span>

<span data-ttu-id="10f42-368"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> Ve olaylarını işlemek için <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="10f42-368">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="10f42-369"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> `OnStarting` , `OnStarted` Ve yöntemleriyle türetilen bir sınıf oluşturun `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="10f42-369">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="10f42-370">' A geçişi için bir genişletme yöntemi oluşturun <xref:Microsoft.AspNetCore.Hosting.IWebHost> `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="10f42-370">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="10f42-371">İçinde `Program.Main` , `RunAsCustomService` yerine genişletme yöntemini çağırın <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="10f42-371">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="10f42-372">Konumunu görmek için <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> `Program.Main` , [dağıtım türü](#deployment-type) bölümünde gösterilen kod örneğine bakın.</span><span class="sxs-lookup"><span data-stu-id="10f42-372">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="10f42-373">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="10f42-373">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="10f42-374">Internet 'ten veya şirket ağından gelen isteklerle etkileşime geçen ve bir ara sunucu veya yük dengeleyicinin arkasındaki Hizmetler ek yapılandırma gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-374">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="10f42-375">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="10f42-375">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="10f42-376">Uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="10f42-376">Configure endpoints</span></span>

<span data-ttu-id="10f42-377">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="10f42-377">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="10f42-378">Ortam değişkenini ayarlayarak URL 'YI ve bağlantı noktasını yapılandırın `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="10f42-378">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="10f42-379">Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="10f42-379">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="10f42-380">Yukarıdaki kılavuz, HTTPS uç noktaları için desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="10f42-380">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="10f42-381">Örneğin, bir Windows hizmeti ile kimlik doğrulaması kullanıldığında HTTPS için uygulamayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="10f42-381">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="10f42-382">Hizmet uç noktasının güvenliğini sağlamak için ASP.NET Core HTTPS geliştirme sertifikası kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="10f42-382">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="10f42-383">Geçerli dizin ve içerik kökü</span><span class="sxs-lookup"><span data-stu-id="10f42-383">Current directory and content root</span></span>

<span data-ttu-id="10f42-384">Windows hizmeti için çağırarak döndürülen geçerli çalışma dizini <xref:System.IO.Directory.GetCurrentDirectory*> *C: \\ Windows \\ System32* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="10f42-384">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="10f42-385">*System32* klasörü, bir hizmetin dosyalarını (örneğin, ayarlar dosyaları) depolamak için uygun bir konum değildir.</span><span class="sxs-lookup"><span data-stu-id="10f42-385">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="10f42-386">Bir hizmetin varlık ve ayar dosyalarını sürdürmek ve erişmek için aşağıdaki yaklaşımlardan birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-386">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="10f42-387">Uygulamanın klasörü için içerik kök yolunu ayarla</span><span class="sxs-lookup"><span data-stu-id="10f42-387">Set the content root path to the app's folder</span></span>

<span data-ttu-id="10f42-388">, <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> `binPath` Bir hizmet oluşturulduğunda bağımsız değişkene aynı yol olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="10f42-388">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="10f42-389">`GetCurrentDirectory`Ayarlar dosyalarına yollar oluşturmak için çağırmak yerine, <xref:System.IO.Directory.SetCurrentDirectory*> uygulamanın [içerik kökünün](xref:fundamentals/index#content-root)yolunu çağırın.</span><span class="sxs-lookup"><span data-stu-id="10f42-389">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="10f42-390">İçinde `Program.Main` , hizmetin yürütülebilir dosyasının yolunu belirleyin ve uygulamanın içerik kökünü oluşturmak için yolu kullanın:</span><span class="sxs-lookup"><span data-stu-id="10f42-390">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="10f42-391">Hizmetin dosyalarını diskte uygun bir konumda depolayın</span><span class="sxs-lookup"><span data-stu-id="10f42-391">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="10f42-392"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Dosyaları içeren klasöre kullanırken ile mutlak bir yol belirtin <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="10f42-392">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="10f42-393">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="10f42-393">Troubleshoot</span></span>

<span data-ttu-id="10f42-394">Bir Windows hizmet uygulaması sorunlarını gidermek için bkz <xref:test/troubleshoot> ..</span><span class="sxs-lookup"><span data-stu-id="10f42-394">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="10f42-395">Sık karşılaşılan hatalar</span><span class="sxs-lookup"><span data-stu-id="10f42-395">Common errors</span></span>

* <span data-ttu-id="10f42-396">PowerShell 'in eski veya yayın öncesi sürümü kullanımda.</span><span class="sxs-lookup"><span data-stu-id="10f42-396">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="10f42-397">Kayıtlı hizmet, [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutundan uygulamanın **yayımlanmış** çıktısını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="10f42-397">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="10f42-398">[DotNet Build](/dotnet/core/tools/dotnet-build) komutunun çıkışı, uygulama dağıtımı için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="10f42-398">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="10f42-399">Yayımlanan varlıklar, dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:</span><span class="sxs-lookup"><span data-stu-id="10f42-399">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="10f42-400">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="10f42-400">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="10f42-401">*bin/Release/{Target Framework}/{RUNTIME tanımlayıcısı}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="10f42-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="10f42-402">Hizmet çalışır durumda değil.</span><span class="sxs-lookup"><span data-stu-id="10f42-402">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="10f42-403">Uygulamanın kullandığı kaynakların yolları (örneğin, sertifikalar) yanlış.</span><span class="sxs-lookup"><span data-stu-id="10f42-403">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="10f42-404">Bir Windows hizmetinin temel yolu *c: \\ Windows \\ System32*' dir.</span><span class="sxs-lookup"><span data-stu-id="10f42-404">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="10f42-405">Kullanıcı, hizmet hakları *olarak oturum* açma hakkına sahip değil.</span><span class="sxs-lookup"><span data-stu-id="10f42-405">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="10f42-406">PowerShell komutu çalıştırılırken kullanıcının parolasının kullanım dışı veya yanlış şekilde geçirilmesi `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="10f42-406">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="10f42-407">Uygulama ASP.NET Core kimlik doğrulaması gerektiriyor, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="10f42-407">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="10f42-408">İstek URL 'SI bağlantı noktası yanlış veya uygulamada doğru şekilde yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="10f42-408">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="10f42-409">Sistem ve uygulama olay günlükleri</span><span class="sxs-lookup"><span data-stu-id="10f42-409">System and Application Event Logs</span></span>

<span data-ttu-id="10f42-410">Sistem ve uygulama olay günlüklerine erişin:</span><span class="sxs-lookup"><span data-stu-id="10f42-410">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="10f42-411">Başlat menüsünü açın, *Olay Görüntüleyicisi* araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-411">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="10f42-412">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="10f42-412">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="10f42-413">Sistem olay günlüğünü açmak için **sistem** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-413">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="10f42-414">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-414">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="10f42-415">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="10f42-415">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="10f42-416">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="10f42-416">Run the app at a command prompt</span></span>

<span data-ttu-id="10f42-417">Birçok başlatma hatası olay günlüklerinde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="10f42-417">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="10f42-418">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10f42-418">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="10f42-419">Uygulamadan ek ayrıntı günlüğe kaydetmek için, [günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [geliştirme ortamında](xref:fundamentals/environments)uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="10f42-419">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="10f42-420">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="10f42-420">Clear package caches</span></span>

<span data-ttu-id="10f42-421">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-421">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="10f42-422">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-422">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="10f42-423">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="10f42-423">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="10f42-424">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="10f42-424">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="10f42-425">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-425">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="10f42-426">Paket önbelleklerini Temizleme, [nuget.exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komutu yürütülebilir `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="10f42-426">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="10f42-427">*nuget.exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="10f42-427">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="10f42-428">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-428">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="10f42-429">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="10f42-429">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="10f42-430">Yavaş veya askıda olan uygulama</span><span class="sxs-lookup"><span data-stu-id="10f42-430">Slow or hanging app</span></span>

<span data-ttu-id="10f42-431">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-431">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="10f42-432">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="10f42-432">App crashes or encounters an exception</span></span>

<span data-ttu-id="10f42-433">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="10f42-433">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="10f42-434">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="10f42-434">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="10f42-435">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) uygulama yürütülebilir adıyla çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="10f42-435">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="10f42-436">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="10f42-436">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="10f42-437">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="10f42-437">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="10f42-438">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-438">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="10f42-439">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="10f42-439">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="10f42-440">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="10f42-440">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="10f42-441">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="10f42-441">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="10f42-442">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="10f42-442">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="10f42-443">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="10f42-443">Analyze the dump</span></span>

<span data-ttu-id="10f42-444">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-444">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="10f42-445">Daha fazla bilgi için bkz. [User-Mode döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="10f42-445">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10f42-446">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="10f42-446">Additional resources</span></span>

* <span data-ttu-id="10f42-447">[Kestrel uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (https yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="10f42-447">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="10f42-448">Bir ASP.NET Core uygulaması, IIS kullanmadan Windows [hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak Windows üzerinde barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-448">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="10f42-449">Windows hizmeti olarak barındırıldığı zaman, uygulama otomatik olarak sunucu yeniden başlatıldıktan sonra başlatılır.</span><span class="sxs-lookup"><span data-stu-id="10f42-449">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="10f42-450">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="10f42-450">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="10f42-451">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="10f42-451">Prerequisites</span></span>

* [<span data-ttu-id="10f42-452">ASP.NET Core SDK 2,1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="10f42-452">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="10f42-453">PowerShell 6,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="10f42-453">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="10f42-454">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="10f42-454">App configuration</span></span>

<span data-ttu-id="10f42-455">Uygulama [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) ve [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)için paket başvuruları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="10f42-455">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="10f42-456">Bir hizmetin dışında çalışırken test ve hata ayıklamak için, uygulamanın bir hizmet olarak mı yoksa bir konsol uygulaması mi çalıştığını belirleme kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-456">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="10f42-457">Hata ayıklayıcının ekli olduğunu veya bir anahtarın mevcut olup olmadığını denetleyin `--console` .</span><span class="sxs-lookup"><span data-stu-id="10f42-457">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="10f42-458">Her iki koşul de geçerliyse (uygulama bir hizmet olarak çalıştırılmadıysa), çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="10f42-458">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="10f42-459">Koşullar yanlışsa (uygulama bir hizmet olarak çalıştırılır):</span><span class="sxs-lookup"><span data-stu-id="10f42-459">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="10f42-460"><xref:System.IO.Directory.SetCurrentDirectory*>Uygulamanın yayımlanmış konumunun yolunu çağırın ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-460">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="10f42-461"><xref:System.IO.Directory.GetCurrentDirectory*>Bir Windows hizmeti uygulaması çağrıldığında *C: \\ Windows \\ System32* klasörünü döndürdüğünden yolu almak için çağırmayın <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="10f42-461">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="10f42-462">Daha fazla bilgi için [geçerli dizin ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="10f42-462">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="10f42-463">Bu adım, uygulamanın ' de yapılandırılmadan önce gerçekleştirilir `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="10f42-463">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="10f42-464"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>Uygulamayı bir hizmet olarak çalıştırmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="10f42-464">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="10f42-465">[Komut satırı yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#command-line-configuration-provider) komut satırı bağımsız değişkenleri için ad-değer çiftleri gerektirdiğinden, `--console` bağımsız değişkenleri almadan önce anahtar bağımsız değişkenlerden kaldırılır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="10f42-465">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="10f42-466">Windows olay günlüğü 'ne yazmak için EventLog sağlayıcısını öğesine ekleyin <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="10f42-466">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="10f42-467">Günlüğe kaydetme düzeyini `Logging:LogLevel:Default` dosyadaki *appsettings.Production.js* anahtarla ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="10f42-467">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="10f42-468">Örnek uygulamadan aşağıdaki örnekte, `RunAsCustomService` <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> uygulama içindeki ömür olaylarını işlemek için yerine çağrılır.</span><span class="sxs-lookup"><span data-stu-id="10f42-468">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="10f42-469">Daha fazla bilgi için [olayları başlatma ve durdurma olaylarını](#handle-starting-and-stopping-events) inceleyin bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="10f42-469">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="10f42-470">Dağıtım türü</span><span class="sxs-lookup"><span data-stu-id="10f42-470">Deployment type</span></span>

<span data-ttu-id="10f42-471">Dağıtım senaryoları hakkında bilgi ve öneriler için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="10f42-471">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="10f42-472">SDK</span><span class="sxs-lookup"><span data-stu-id="10f42-472">SDK</span></span>

<span data-ttu-id="10f42-473">Sayfaları veya MVC çerçevelerini kullanan bir Web uygulaması tabanlı hizmet için Razor , proje dosyasında Web SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="10f42-473">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="10f42-474">Hizmet yalnızca arka plan görevlerini (örneğin, [barındırılan hizmetler](xref:fundamentals/host/hosted-services)) yürütülüyorsa, proje dosyasında çalışan SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="10f42-474">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="10f42-475">Çerçeveye bağımlı dağıtım (FDD)</span><span class="sxs-lookup"><span data-stu-id="10f42-475">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="10f42-476">Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core 'un paylaşılan sistem genelindeki bir sürümünün varlığına dayanır.</span><span class="sxs-lookup"><span data-stu-id="10f42-476">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="10f42-477">Bu makaledeki kılavuzdan sonra FDD senaryosu benimsendiği zaman SDK, *çerçeveye bağlı yürütülebilir dosya* olarak adlandırılan yürütülebilir bir dosya (*. exe*) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="10f42-477">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="10f42-478">Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) hedef Framework 'ü içerir.</span><span class="sxs-lookup"><span data-stu-id="10f42-478">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="10f42-479">Aşağıdaki örnekte, RID olarak ayarlanır `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="10f42-479">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="10f42-480">`<SelfContained>` özelliği `false` olarak ayarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="10f42-480">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="10f42-481">Bu özellikler SDK 'nın Windows için bir yürütülebilir (*. exe*) dosya ve paylaşılan .NET Core çerçevesine bağlı bir uygulama oluşturmasını ister.</span><span class="sxs-lookup"><span data-stu-id="10f42-481">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="10f42-482">`<UseAppHost>` özelliği `true` olarak ayarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="10f42-482">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="10f42-483">Bu özellik, bir FDD için bir etkinleştirme yolu (yürütülebilir, *. exe*) ile hizmeti sağlar.</span><span class="sxs-lookup"><span data-stu-id="10f42-483">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="10f42-484">Bir ASP.NET Core uygulaması yayımlandığında normalde oluşturulan *web.config* dosyası, Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="10f42-484">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="10f42-485">*web.config* dosyasının oluşturulmasını devre dışı bırakmak için, `<IsTransformWebConfigDisabled>` özelliği öğesine ekleyin `true` .</span><span class="sxs-lookup"><span data-stu-id="10f42-485">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="10f42-486">Kendi içinde dağıtım (SCD)</span><span class="sxs-lookup"><span data-stu-id="10f42-486">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="10f42-487">Kendinden bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir Framework varlığına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="10f42-487">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="10f42-488">Çalışma zamanı ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="10f42-488">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="10f42-489">Hedef Framework 'ü içeren ' de bir Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) bulunur `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="10f42-489">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="10f42-490">Birden çok RID için yayımlamak için:</span><span class="sxs-lookup"><span data-stu-id="10f42-490">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="10f42-491">RID 'leri, noktalı virgülle ayrılmış bir liste olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="10f42-491">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="10f42-492">Özellik adını [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural) kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-492">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="10f42-493">Daha fazla bilgi için bkz. [.NET Core RID kataloğu](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="10f42-493">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="10f42-494">Bir `<SelfContained>` özellik şu şekilde ayarlanır `true` :</span><span class="sxs-lookup"><span data-stu-id="10f42-494">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="10f42-495">Hizmet Kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="10f42-495">Service user account</span></span>

<span data-ttu-id="10f42-496">Bir hizmet için Kullanıcı hesabı oluşturmak için, bir yönetim PowerShell 6 komut kabuğundan [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ini kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-496">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="10f42-497">Windows 10 Ekim 2018 Güncelleştirmesi (sürüm 1809/Build 10.0.17763) veya sonraki sürümler:</span><span class="sxs-lookup"><span data-stu-id="10f42-497">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="10f42-498">Windows 10 Ekim 2018 (sürüm 1809/Build 10.0.17763) sürümünden önceki Windows IŞLETIM sistemlerinde:</span><span class="sxs-lookup"><span data-stu-id="10f42-498">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="10f42-499">İstendiğinde [güçlü bir parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.</span><span class="sxs-lookup"><span data-stu-id="10f42-499">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="10f42-500">`-AccountExpires`Parametresi [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ine bir süre sonu ile sağlanmamışsa <xref:System.DateTime> hesabın süresi dolmaz.</span><span class="sxs-lookup"><span data-stu-id="10f42-500">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="10f42-501">Daha fazla bilgi için bkz. [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [hizmet Kullanıcı hesapları](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="10f42-501">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="10f42-502">Active Directory kullanırken kullanıcıları yönetmeye yönelik alternatif bir yaklaşım, yönetilen hizmet hesaplarını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="10f42-502">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="10f42-503">Daha fazla bilgi için bkz. [Grup yönetilen hizmet hesaplarına genel bakış](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="10f42-503">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="10f42-504">Hizmet hakları olarak oturum açma</span><span class="sxs-lookup"><span data-stu-id="10f42-504">Log on as a service rights</span></span>

<span data-ttu-id="10f42-505">Hizmet Kullanıcı hesabı için *hizmet hakları olarak oturum* açma oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="10f42-505">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="10f42-506">Yerel Güvenlik Ilkesi düzenleyicisini, *secpol. msc*' i çalıştırarak açın.</span><span class="sxs-lookup"><span data-stu-id="10f42-506">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="10f42-507">**Yerel ilkeler** düğümünü genişletin ve **Kullanıcı hakları ataması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-507">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="10f42-508">**Hizmet olarak oturum** açma ilkesi açın.</span><span class="sxs-lookup"><span data-stu-id="10f42-508">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="10f42-509">**Kullanıcı veya Grup Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-509">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="10f42-510">Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (Kullanıcı hesabı) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="10f42-510">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="10f42-511">Kullanıcı hesabını ( `{DOMAIN OR COMPUTER NAME\USER}` ) nesne adı alanına yazın ve kullanıcıyı ilkeye eklemek Için **Tamam** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-511">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="10f42-512">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-512">Select **Advanced**.</span></span> <span data-ttu-id="10f42-513">**Şimdi bul**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-513">Select **Find Now**.</span></span> <span data-ttu-id="10f42-514">Listeden Kullanıcı hesabını seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-514">Select the user account from the list.</span></span> <span data-ttu-id="10f42-515">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-515">Select **OK**.</span></span> <span data-ttu-id="10f42-516">Kullanıcıyı ilkeye eklemek için yeniden **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-516">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="10f42-517">Değişiklikleri kabul etmek için **Tamam ' ı** veya **Uygula** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-517">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="10f42-518">Windows hizmetini oluşturma ve yönetme</span><span class="sxs-lookup"><span data-stu-id="10f42-518">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="10f42-519">Hizmet oluşturma</span><span class="sxs-lookup"><span data-stu-id="10f42-519">Create a service</span></span>

<span data-ttu-id="10f42-520">Bir hizmeti kaydetmek için PowerShell komutlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-520">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="10f42-521">Bir yönetim PowerShell 6 komut kabuğundan aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="10f42-521">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="10f42-522">`{EXE PATH}`: Konaktaki uygulamanın klasörünün yolu (örneğin, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-522">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="10f42-523">Uygulamanın yürütülebilir dosyasını yola eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-523">Don't include the app's executable in the path.</span></span> <span data-ttu-id="10f42-524">Sondaki eğik çizgi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="10f42-524">A trailing slash isn't required.</span></span>
* <span data-ttu-id="10f42-525">`{DOMAIN OR COMPUTER NAME\USER}`: Hizmet Kullanıcı hesabı (örneğin, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-525">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="10f42-526">`{SERVICE NAME}`: Hizmet adı (örneğin, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-526">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="10f42-527">`{EXE FILE PATH}`: Uygulamanın yürütülebilir yolu (örneğin, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-527">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="10f42-528">Yürütülebilir dosyanın dosya adını uzantısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-528">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="10f42-529">`{DESCRIPTION}`: Hizmet açıklaması (örneğin, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-529">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="10f42-530">`{DISPLAY NAME}`: Hizmet görünen adı (örneğin, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="10f42-530">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="10f42-531">Bir hizmet başlatın</span><span class="sxs-lookup"><span data-stu-id="10f42-531">Start a service</span></span>

<span data-ttu-id="10f42-532">Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:</span><span class="sxs-lookup"><span data-stu-id="10f42-532">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="10f42-533">Komutun başlatılması birkaç saniye sürer.</span><span class="sxs-lookup"><span data-stu-id="10f42-533">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="10f42-534">Hizmetin durumunu belirleme</span><span class="sxs-lookup"><span data-stu-id="10f42-534">Determine a service's status</span></span>

<span data-ttu-id="10f42-535">Bir hizmetin durumunu denetlemek için aşağıdaki PowerShell 6 komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="10f42-535">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="10f42-536">Durum, aşağıdaki değerlerden biri olarak bildirilir:</span><span class="sxs-lookup"><span data-stu-id="10f42-536">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="10f42-537">Bir hizmet durdurun</span><span class="sxs-lookup"><span data-stu-id="10f42-537">Stop a service</span></span>

<span data-ttu-id="10f42-538">Aşağıdaki PowerShell 6 komutuyla bir hizmeti durdurun:</span><span class="sxs-lookup"><span data-stu-id="10f42-538">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="10f42-539">Hizmeti Kaldır</span><span class="sxs-lookup"><span data-stu-id="10f42-539">Remove a service</span></span>

<span data-ttu-id="10f42-540">Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki PowerShell 6 komutuyla bir hizmeti kaldırın:</span><span class="sxs-lookup"><span data-stu-id="10f42-540">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="10f42-541">Olayları başlatma ve durdurma olaylarını işleme</span><span class="sxs-lookup"><span data-stu-id="10f42-541">Handle starting and stopping events</span></span>

<span data-ttu-id="10f42-542"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> Ve olaylarını işlemek için <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="10f42-542">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="10f42-543"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> `OnStarting` , `OnStarted` Ve yöntemleriyle türetilen bir sınıf oluşturun `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="10f42-543">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="10f42-544">' A geçişi için bir genişletme yöntemi oluşturun <xref:Microsoft.AspNetCore.Hosting.IWebHost> `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="10f42-544">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="10f42-545">İçinde `Program.Main` , `RunAsCustomService` yerine genişletme yöntemini çağırın <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="10f42-545">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="10f42-546">Konumunu görmek için <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> `Program.Main` , [dağıtım türü](#deployment-type) bölümünde gösterilen kod örneğine bakın.</span><span class="sxs-lookup"><span data-stu-id="10f42-546">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="10f42-547">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="10f42-547">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="10f42-548">Internet 'ten veya şirket ağından gelen isteklerle etkileşime geçen ve bir ara sunucu veya yük dengeleyicinin arkasındaki Hizmetler ek yapılandırma gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-548">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="10f42-549">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="10f42-549">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="10f42-550">Uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="10f42-550">Configure endpoints</span></span>

<span data-ttu-id="10f42-551">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="10f42-551">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="10f42-552">Ortam değişkenini ayarlayarak URL 'YI ve bağlantı noktasını yapılandırın `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="10f42-552">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="10f42-553">Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="10f42-553">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="10f42-554">Yukarıdaki kılavuz, HTTPS uç noktaları için desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="10f42-554">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="10f42-555">Örneğin, bir Windows hizmeti ile kimlik doğrulaması kullanıldığında HTTPS için uygulamayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="10f42-555">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="10f42-556">Hizmet uç noktasının güvenliğini sağlamak için ASP.NET Core HTTPS geliştirme sertifikası kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="10f42-556">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="10f42-557">Geçerli dizin ve içerik kökü</span><span class="sxs-lookup"><span data-stu-id="10f42-557">Current directory and content root</span></span>

<span data-ttu-id="10f42-558">Windows hizmeti için çağırarak döndürülen geçerli çalışma dizini <xref:System.IO.Directory.GetCurrentDirectory*> *C: \\ Windows \\ System32* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="10f42-558">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="10f42-559">*System32* klasörü, bir hizmetin dosyalarını (örneğin, ayarlar dosyaları) depolamak için uygun bir konum değildir.</span><span class="sxs-lookup"><span data-stu-id="10f42-559">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="10f42-560">Bir hizmetin varlık ve ayar dosyalarını sürdürmek ve erişmek için aşağıdaki yaklaşımlardan birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="10f42-560">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="10f42-561">Uygulamanın klasörü için içerik kök yolunu ayarla</span><span class="sxs-lookup"><span data-stu-id="10f42-561">Set the content root path to the app's folder</span></span>

<span data-ttu-id="10f42-562">, <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> `binPath` Bir hizmet oluşturulduğunda bağımsız değişkene aynı yol olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="10f42-562">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="10f42-563">`GetCurrentDirectory`Ayarlar dosyalarına yollar oluşturmak için çağırmak yerine, <xref:System.IO.Directory.SetCurrentDirectory*> uygulamanın [içerik kökünün](xref:fundamentals/index#content-root)yolunu çağırın.</span><span class="sxs-lookup"><span data-stu-id="10f42-563">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="10f42-564">İçinde `Program.Main` , hizmetin yürütülebilir dosyasının yolunu belirleyin ve uygulamanın içerik kökünü oluşturmak için yolu kullanın:</span><span class="sxs-lookup"><span data-stu-id="10f42-564">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="10f42-565">Hizmetin dosyalarını diskte uygun bir konumda depolayın</span><span class="sxs-lookup"><span data-stu-id="10f42-565">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="10f42-566"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Dosyaları içeren klasöre kullanırken ile mutlak bir yol belirtin <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="10f42-566">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="10f42-567">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="10f42-567">Troubleshoot</span></span>

<span data-ttu-id="10f42-568">Bir Windows hizmet uygulaması sorunlarını gidermek için bkz <xref:test/troubleshoot> ..</span><span class="sxs-lookup"><span data-stu-id="10f42-568">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="10f42-569">Sık karşılaşılan hatalar</span><span class="sxs-lookup"><span data-stu-id="10f42-569">Common errors</span></span>

* <span data-ttu-id="10f42-570">PowerShell 'in eski veya yayın öncesi sürümü kullanımda.</span><span class="sxs-lookup"><span data-stu-id="10f42-570">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="10f42-571">Kayıtlı hizmet, [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutundan uygulamanın **yayımlanmış** çıktısını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="10f42-571">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="10f42-572">[DotNet Build](/dotnet/core/tools/dotnet-build) komutunun çıkışı, uygulama dağıtımı için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="10f42-572">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="10f42-573">Yayımlanan varlıklar, dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:</span><span class="sxs-lookup"><span data-stu-id="10f42-573">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="10f42-574">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="10f42-574">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="10f42-575">*bin/Release/{Target Framework}/{RUNTIME tanımlayıcısı}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="10f42-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="10f42-576">Hizmet çalışır durumda değil.</span><span class="sxs-lookup"><span data-stu-id="10f42-576">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="10f42-577">Uygulamanın kullandığı kaynakların yolları (örneğin, sertifikalar) yanlış.</span><span class="sxs-lookup"><span data-stu-id="10f42-577">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="10f42-578">Bir Windows hizmetinin temel yolu *c: \\ Windows \\ System32*' dir.</span><span class="sxs-lookup"><span data-stu-id="10f42-578">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="10f42-579">Kullanıcı, hizmet hakları *olarak oturum* açma hakkına sahip değil.</span><span class="sxs-lookup"><span data-stu-id="10f42-579">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="10f42-580">PowerShell komutu çalıştırılırken kullanıcının parolasının kullanım dışı veya yanlış şekilde geçirilmesi `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="10f42-580">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="10f42-581">Uygulama ASP.NET Core kimlik doğrulaması gerektiriyor, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="10f42-581">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="10f42-582">İstek URL 'SI bağlantı noktası yanlış veya uygulamada doğru şekilde yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="10f42-582">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="10f42-583">Sistem ve uygulama olay günlükleri</span><span class="sxs-lookup"><span data-stu-id="10f42-583">System and Application Event Logs</span></span>

<span data-ttu-id="10f42-584">Sistem ve uygulama olay günlüklerine erişin:</span><span class="sxs-lookup"><span data-stu-id="10f42-584">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="10f42-585">Başlat menüsünü açın, *Olay Görüntüleyicisi* araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-585">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="10f42-586">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="10f42-586">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="10f42-587">Sistem olay günlüğünü açmak için **sistem** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-587">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="10f42-588">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="10f42-588">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="10f42-589">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="10f42-589">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="10f42-590">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="10f42-590">Run the app at a command prompt</span></span>

<span data-ttu-id="10f42-591">Birçok başlatma hatası olay günlüklerinde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="10f42-591">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="10f42-592">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="10f42-592">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="10f42-593">Uygulamadan ek ayrıntı günlüğe kaydetmek için, [günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [geliştirme ortamında](xref:fundamentals/environments)uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="10f42-593">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="10f42-594">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="10f42-594">Clear package caches</span></span>

<span data-ttu-id="10f42-595">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-595">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="10f42-596">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-596">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="10f42-597">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="10f42-597">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="10f42-598">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="10f42-598">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="10f42-599">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-599">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="10f42-600">Paket önbelleklerini Temizleme, [nuget.exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komutu yürütülebilir `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="10f42-600">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="10f42-601">*nuget.exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="10f42-601">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="10f42-602">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="10f42-602">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="10f42-603">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="10f42-603">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="10f42-604">Yavaş veya askıda olan uygulama</span><span class="sxs-lookup"><span data-stu-id="10f42-604">Slow or hanging app</span></span>

<span data-ttu-id="10f42-605">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-605">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="10f42-606">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="10f42-606">App crashes or encounters an exception</span></span>

<span data-ttu-id="10f42-607">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="10f42-607">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="10f42-608">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="10f42-608">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="10f42-609">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) uygulama yürütülebilir adıyla çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="10f42-609">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="10f42-610">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="10f42-610">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="10f42-611">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="10f42-611">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="10f42-612">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-612">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="10f42-613">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="10f42-613">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="10f42-614">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="10f42-614">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="10f42-615">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="10f42-615">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="10f42-616">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="10f42-616">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="10f42-617">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="10f42-617">Analyze the dump</span></span>

<span data-ttu-id="10f42-618">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="10f42-618">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="10f42-619">Daha fazla bilgi için bkz. [User-Mode döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="10f42-619">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10f42-620">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="10f42-620">Additional resources</span></span>

* <span data-ttu-id="10f42-621">[Kestrel uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (https yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="10f42-621">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
