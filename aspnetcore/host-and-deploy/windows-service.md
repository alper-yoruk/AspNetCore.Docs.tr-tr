---
title: Windows Hizmetinde Ana Bilgisayar ASP.NET Core
author: rick-anderson
description: Windows Hizmetinde bir ASP.NET Core uygulamasını nasıl barındırılacın öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 5cb61d330df7e15fbd54396207792596ae018fd3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417587"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="b9ec9-103">Windows Hizmetinde Ana Bilgisayar ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9ec9-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b9ec9-104">Bir ASP.NET Core uygulaması, IIS kullanmadan [Windows'da Windows Hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="b9ec9-105">Windows Hizmeti olarak barındırıldığında, uygulama sunucu yeniden başlatıldıktan sonra otomatik olarak başlar.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="b9ec9-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b9ec9-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b9ec9-107">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="b9ec9-107">Prerequisites</span></span>

* [<span data-ttu-id="b9ec9-108">ASP.NET Core SDK 2.1 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="b9ec9-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="b9ec9-109">PowerShell 6.2 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="b9ec9-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="b9ec9-110">İşçi Hizmeti şablonu</span><span class="sxs-lookup"><span data-stu-id="b9ec9-110">Worker Service template</span></span>

<span data-ttu-id="b9ec9-111">ASP.NET Çekirdek İşçi Hizmeti şablonu, uzun süre çalışan hizmet uygulamalarını yazmak için bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="b9ec9-112">Şablonu bir Windows Hizmeti uygulaması için temel olarak kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="b9ec9-113">.NET Core şablonundan bir İşçi Hizmeti uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="b9ec9-114">Windows Hizmeti olarak çalıştırılabilmek için İşçi Hizmeti uygulamasını güncellemek için [Uygulama yapılandırma](#app-configuration) bölümündeki kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="b9ec9-115">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b9ec9-115">App configuration</span></span>

<span data-ttu-id="b9ec9-116">Uygulama [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)için bir paket başvuru gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="b9ec9-117">`IHostBuilder.UseWindowsService`ana bilgisayar inşa ederken denir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="b9ec9-118">Uygulama Windows Hizmeti olarak çalışıyorsa, yöntem:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="b9ec9-119">Ana bilgisayar ömrünü `WindowsServiceLifetime`' ne ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="b9ec9-120">İçerik [kökünü](xref:fundamentals/index#content-root) [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="b9ec9-121">Daha fazla bilgi için [Geçerli dizini ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="b9ec9-122">Olay günlüğüne günlüğe kaydetmeyi sağlar:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="b9ec9-123">Uygulama adı varsayılan kaynak adı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="b9ec9-124">Varsayılan günlük düzeyi, ana bilgisayarı oluşturmak için çağrıda `CreateDefaultBuilder` bulunan ASP.NET Core şablonuna dayalı bir uygulama için *Uyarı* veya daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="b9ec9-125">`Logging:EventLog:LogLevel:Default` *appsettings.json*/appsettings'teki anahtarla varsayılan günlük düzeyini geçersiz*kılın.{ Environment}.json* veya diğer yapılandırma sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="b9ec9-126">Yalnızca yöneticiler yeni olay kaynakları oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="b9ec9-127">Bir olay kaynağı uygulama adı kullanılarak oluşturuladığında, uygulama *kaynağına* bir uyarı günlüğe kaydedilir ve olay günlükleri devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="b9ec9-128">`CreateHostBuilder` *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="b9ec9-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="b9ec9-129">Aşağıdaki örnek uygulamalar bu konuya eşlik eder:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="b9ec9-130">Arka Plan &ndash; İşçi Sat›r›m Örneği Arka plan görevleri için [barındır›lan hizmetleri](xref:fundamentals/host/hosted-services) kullanan İşçi [Hizmeti şablonuna](#worker-service-template) dayanan web olmayan bir uygulama örneği.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-130">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="b9ec9-131">Web App &ndash; Service Örnek Arka plan görevleri için [barındırılan hizmetlerle](xref:fundamentals/host/hosted-services) windows hizmeti olarak çalışan Bir Jilet Sayfaları web uygulaması örneği.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-131">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="b9ec9-132">MVC kılavuzu için, altındaki <xref:mvc/overview> <xref:migration/22-to-30>makalelere bakın ve .</span><span class="sxs-lookup"><span data-stu-id="b9ec9-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="b9ec9-133">Dağıtım türü</span><span class="sxs-lookup"><span data-stu-id="b9ec9-133">Deployment type</span></span>

<span data-ttu-id="b9ec9-134">Dağıtım senaryoları hakkında bilgi ve tavsiye için [bkz.](/dotnet/core/deploying/)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="b9ec9-135">SDK</span><span class="sxs-lookup"><span data-stu-id="b9ec9-135">SDK</span></span>

<span data-ttu-id="b9ec9-136">Razor Pages veya MVC çerçevelerini kullanan web uygulaması tabanlı bir hizmet için, proje dosyasında Web SDK'yı belirtin:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="b9ec9-137">Hizmet yalnızca arka plan görevlerini yürütürse (örneğin, [barındırılan hizmetler),](xref:fundamentals/host/hosted-services)Proje dosyasında İşçi SDK'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="b9ec9-138">Çerçeveye bağımlı dağıtım (FDD)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="b9ec9-139">Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core'un sistem genelinde paylaşılan bir sürümünün bulunmasına dayanır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="b9ec9-140">FDD senaryosu bu makaledeki kılavuzdan sonra kabul edildiğinde, *SDK, çerçeveye bağımlı yürütülebilir*olarak adlandırılan bir yürütülebilir (*.exe*) üretir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="b9ec9-141">Bir ASP.NET Core uygulaması yayımlanırken normalde üretilen *web SDK* kullanıyorsanız, bir Windows Hizmetleri uygulaması için gereksizdir. [Web SDK](#sdk)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="b9ec9-142">*web.config* dosyasının oluşturulmasını devre dışı bırakabilmek `<IsTransformWebConfigDisabled>` `true`için, ayarlanan özelliği ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="b9ec9-143">Bağımsız dağıtım (SCD)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="b9ec9-144">Bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir çerçevenin varlığına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="b9ec9-145">Çalışma süresi ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="b9ec9-146">Bir Windows [Runtime Tanımlayıcısı (RID)](/dotnet/core/rid-catalog) `<PropertyGroup>` hedef çerçeveyi içeren dahildir:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="b9ec9-147">Birden çok RID için yayımlamak için:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="b9ec9-148">RID'leri yarı sütunlu sınırlı bir listede sağlayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="b9ec9-149">[ \<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (çoğul) özellik adını kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="b9ec9-150">Daha fazla bilgi için [.NET Core RID Kataloğu'na](/dotnet/core/rid-catalog)bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="b9ec9-151">Hizmet kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="b9ec9-151">Service user account</span></span>

<span data-ttu-id="b9ec9-152">Bir hizmet için kullanıcı hesabı oluşturmak için, yönetimsel powershell 6 komut kabuğundan [Yeni-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet'ini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="b9ec9-153">Windows 10 Ekim 2018 Güncelleştirmesi'nde (sürüm 1809/build 10.0.17763) veya daha sonra:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="b9ec9-154">Windows 10 Ekim 2018 Güncelleştirmesinden önceki Windows İşletim Sistemi'nde (sürüm 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="b9ec9-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="b9ec9-155">İstendiğinde güçlü bir [parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="b9ec9-156">Parametre, sona ermiş <xref:System.DateTime> [yeni yerel kullanıcı](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet'e sağatılmadığı sürece, hesabın süresi dolmaz. `-AccountExpires`</span><span class="sxs-lookup"><span data-stu-id="b9ec9-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="b9ec9-157">Daha fazla bilgi için [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [Hizmet Kullanıcı Hesapları'na](/windows/desktop/services/service-user-accounts)bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="b9ec9-158">Active Directory kullanırken kullanıcıları yönetmek için alternatif bir yaklaşım Yönetilen Hizmet Hesapları kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="b9ec9-159">Daha fazla bilgi için [Grup Yönetilen Hizmet Hesaplarına Genel Bakış'a](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="b9ec9-160">Hizmet hakları olarak oturum açma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-160">Log on as a service rights</span></span>

<span data-ttu-id="b9ec9-161">Bir hizmet kullanıcı hesabı için hizmet hakları *olarak Oturum Açma'yı* kurmak için:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="b9ec9-162">*Secpol.msc*çalıştırarak Yerel Güvenlik Politikası düzenleyicisini açın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="b9ec9-163">Yerel **İlkeler** düğümünü genişletin ve **Kullanıcı Hakları Ataması'nı**seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="b9ec9-164">Hizmet ilkesi **olarak Oturum Aç'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="b9ec9-165">**Kullanıcı veya Grup Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="b9ec9-166">Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (kullanıcı hesabı) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="b9ec9-167">Nesne adı alanına`{DOMAIN OR COMPUTER NAME\USER}`kullanıcı hesabı ( ) yazın ve kullanıcıyı ilkeye eklemek için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="b9ec9-168">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-168">Select **Advanced**.</span></span> <span data-ttu-id="b9ec9-169">**Şimdi Bul'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-169">Select **Find Now**.</span></span> <span data-ttu-id="b9ec9-170">Listeden kullanıcı hesabını seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-170">Select the user account from the list.</span></span> <span data-ttu-id="b9ec9-171">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-171">Select **OK**.</span></span> <span data-ttu-id="b9ec9-172">Kullanıcıyı ilkeye eklemek için tekrar **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="b9ec9-173">Değişiklikleri kabul etmek için **Tamam** veya **Uygula'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="b9ec9-174">Windows Hizmeti oluşturma ve yönetme</span><span class="sxs-lookup"><span data-stu-id="b9ec9-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="b9ec9-175">Hizmet oluşturma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-175">Create a service</span></span>

<span data-ttu-id="b9ec9-176">Bir hizmeti kaydetmek için PowerShell komutlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="b9ec9-177">İdari powershell 6 komut uyruktan aşağıdaki komutları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="b9ec9-178">`{EXE PATH}`&ndash; Uygulamanın ana bilgisayardaki klasörüne giden yol `d:\myservice`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-178">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="b9ec9-179">Uygulamanın çalıştırılabilir uygulamasını yola eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="b9ec9-180">İzbırakan bir kesik gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="b9ec9-181">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Hizmet kullanıcı hesabı (örneğin, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-181">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="b9ec9-182">`{SERVICE NAME}`&ndash; Hizmet adı (örneğin, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-182">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="b9ec9-183">`{EXE FILE PATH}`&ndash; Uygulamanın çalıştırılabilir yolu (örneğin, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-183">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="b9ec9-184">Yürütülebilir dosya adını uzantılı olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="b9ec9-185">`{DESCRIPTION}`&ndash; Hizmet açıklaması (örneğin, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-185">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="b9ec9-186">`{DISPLAY NAME}`&ndash; Hizmet görüntü adı (örneğin, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-186">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="b9ec9-187">Bir hizmet başlatın</span><span class="sxs-lookup"><span data-stu-id="b9ec9-187">Start a service</span></span>

<span data-ttu-id="b9ec9-188">Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="b9ec9-189">Komutun hizmeti başlatması birkaç saniye sürer.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="b9ec9-190">Bir hizmetin durumunu belirleme</span><span class="sxs-lookup"><span data-stu-id="b9ec9-190">Determine a service's status</span></span>

<span data-ttu-id="b9ec9-191">Bir hizmetin durumunu kontrol etmek için aşağıdaki PowerShell 6 komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="b9ec9-192">Durum aşağıdaki değerlerden biri olarak bildirilir:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="b9ec9-193">Bir hizmet durdurun</span><span class="sxs-lookup"><span data-stu-id="b9ec9-193">Stop a service</span></span>

<span data-ttu-id="b9ec9-194">Aşağıdaki Powershell 6 komutuna sahip bir hizmeti durdurun:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="b9ec9-195">Bir hizmeti kaldırma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-195">Remove a service</span></span>

<span data-ttu-id="b9ec9-196">Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki Powershell 6 komutuna sahip bir hizmeti kaldırın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b9ec9-197">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="b9ec9-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b9ec9-198">Internet'ten veya şirket ağından gelen isteklerle etkileşimde olan ve proxy veya yük dengeleyicisinin arkasında olan hizmetler ek yapılandırma gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="b9ec9-199">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="b9ec9-200">Uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-200">Configure endpoints</span></span>

<span data-ttu-id="b9ec9-201">Varsayılan olarak, ASP.NET Core `http://localhost:5000`bağlanır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="b9ec9-202">Ortam değişkenini ayarlayarak `ASPNETCORE_URLS` URL'yi ve bağlantı noktasını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="b9ec9-203">Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="b9ec9-204">Önceki kılavuz, HTTPS uç noktaları nın desteğini kapsar.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="b9ec9-205">Örneğin, kimlik doğrulama bir Windows Hizmeti ile kullanıldığında uygulamayı HTTPS için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="b9ec9-206">Bir hizmet bitiş noktasını güvence altına almak için ASP.NET Core HTTPS geliştirme sertifikasının kullanımı desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="b9ec9-207">Geçerli dizin ve içerik kökü</span><span class="sxs-lookup"><span data-stu-id="b9ec9-207">Current directory and content root</span></span>

<span data-ttu-id="b9ec9-208">Bir Windows Hizmeti için <xref:System.IO.Directory.GetCurrentDirectory*> arayarak döndürülen geçerli çalışma dizini *C:\\WINDOWS\\system32* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="b9ec9-209">*System32* klasörü, bir hizmetin dosyalarını depolamak için uygun bir konum değildir (örneğin, ayarlar dosyaları).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="b9ec9-210">Bir hizmetin varlıklarını ve ayar dosyalarını korumak ve bu dosyalara erişmek için aşağıdaki yaklaşımlardan birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="b9ec9-211">ContentRootPath veya ContentRootFileProvider kullanın</span><span class="sxs-lookup"><span data-stu-id="b9ec9-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="b9ec9-212">[IHostEnvironment.ContentRootPath'i](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) kullanın veya <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> bir uygulamanın kaynaklarını bulun.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="b9ec9-213">Uygulama bir hizmet olarak <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> çalıştığında, <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> [AppContext.BaseDirectory'ye](xref:System.AppContext.BaseDirectory)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="b9ec9-214">Uygulamanın varsayılan ayarları dosyaları, *appsettings.json* ve *appsettings.{ Environment}.json*, [ana bilgisayar inşaatı sırasında CreateDefaultBuilder'ı](xref:fundamentals/host/generic-host#set-up-a-host)arayarak uygulamanın içerik kökünden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="b9ec9-215">Geliştirici koduyla <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>yüklenen diğer ayarlar için aramanız <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>gerekmez.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b9ec9-216">Aşağıdaki örnekte, *custom_settings.json* dosyası uygulamanın içerik kökünde bulunur ve açıkça bir temel yol belirlemeden yüklenir:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="b9ec9-217">Bir Windows Service <xref:System.IO.Directory.GetCurrentDirectory*> uygulaması *C:\\WINDOWS\\system32* klasörünü geçerli dizini olarak döndürür, çünkü bir kaynak yolu elde etmek için kullanmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="b9ec9-218">Bir hizmetin dosyalarını diskte uygun bir konumda depolama</span><span class="sxs-lookup"><span data-stu-id="b9ec9-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="b9ec9-219">Dosyaları içeren klasöre <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> bir <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> kullanırken ile mutlak bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b9ec9-220">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="b9ec9-220">Troubleshoot</span></span>

<span data-ttu-id="b9ec9-221">Bir Windows Hizmeti uygulamasını gidermek <xref:test/troubleshoot>için bkz.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="b9ec9-222">Sık karşılaşılan hatalar</span><span class="sxs-lookup"><span data-stu-id="b9ec9-222">Common errors</span></span>

* <span data-ttu-id="b9ec9-223">PowerShell'in eski veya ön sürüm sürümü kullanımdadır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="b9ec9-224">Kayıtlı hizmet, uygulamanın [yayınlanan dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutundan **çıkan** çıktısını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="b9ec9-225">[Dotnet yapı](/dotnet/core/tools/dotnet-build) komutunun çıktısı uygulama dağıtımı için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="b9ec9-226">Yayımlanmış varlıklar dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="b9ec9-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="b9ec9-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="b9ec9-229">Hizmet RUNNING durumunda değil.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="b9ec9-230">Uygulamanın kullandığı kaynaklara giden yollar (örneğin, sertifikalar) yanlıştır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="b9ec9-231">Bir Windows Hizmetinin temel yolu *c:\\Windows\\System32.*</span><span class="sxs-lookup"><span data-stu-id="b9ec9-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="b9ec9-232">Kullanıcının hizmet olarak *Oturum* Açma'sı yoktur.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="b9ec9-233">PowerShell komutunu çalıştırırken kullanıcının parolasının `New-Service` süresi doldu veya yanlış geçti.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="b9ec9-234">Uygulama ASP.NET Çekirdek kimlik doğrulaması gerektirir, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamıştır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="b9ec9-235">İstek URL bağlantı noktası yanlış veya uygulamada doğru yapılandırılmamıştır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="b9ec9-236">Sistem ve Uygulama Etkinlik Günlükleri</span><span class="sxs-lookup"><span data-stu-id="b9ec9-236">System and Application Event Logs</span></span>

<span data-ttu-id="b9ec9-237">Sistem ve Uygulama Etkinlik Günlüklerine Erişin:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="b9ec9-238">Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="b9ec9-239">**Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="b9ec9-240">Sistem Olay Günlüğü'nü açmak için **Sistem'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="b9ec9-241">Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="b9ec9-242">Başarısız uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="b9ec9-243">Uygulamayı komut istemiyle çalıştırma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-243">Run the app at a command prompt</span></span>

<span data-ttu-id="b9ec9-244">Birçok başlangıç hatası olay günlüklerinde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="b9ec9-245">Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="b9ec9-246">Uygulamadan ek ayrıntı günlüğe kaydetmek [için, günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [uygulamayı Geliştirme ortamında](xref:fundamentals/environments)çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="b9ec9-247">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="b9ec9-247">Clear package caches</span></span>

<span data-ttu-id="b9ec9-248">İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="b9ec9-249">Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="b9ec9-250">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="b9ec9-251">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="b9ec9-252">[Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="b9ec9-253">Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="b9ec9-254">*nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="b9ec9-255">Projeyi geri yükleyin ve yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="b9ec9-256">Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="b9ec9-257">Yavaş veya asma uygulaması</span><span class="sxs-lookup"><span data-stu-id="b9ec9-257">Slow or hanging app</span></span>

<span data-ttu-id="b9ec9-258">*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="b9ec9-259">Uygulama çöküyor veya bir özel durumla karşılaşıyor</span><span class="sxs-lookup"><span data-stu-id="b9ec9-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="b9ec9-260">Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="b9ec9-261">Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun</span><span class="sxs-lookup"><span data-stu-id="b9ec9-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="b9ec9-262">[EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) uygulama çalıştırılabilir adla çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="b9ec9-263">Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="b9ec9-264">Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="b9ec9-265">Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="b9ec9-266">PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="b9ec9-267">Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="b9ec9-268">Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır</span><span class="sxs-lookup"><span data-stu-id="b9ec9-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="b9ec9-269">Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="b9ec9-270">Dökümü analiz edin</span><span class="sxs-lookup"><span data-stu-id="b9ec9-270">Analyze the dump</span></span>

<span data-ttu-id="b9ec9-271">Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="b9ec9-272">Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9ec9-273">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b9ec9-273">Additional resources</span></span>

* <span data-ttu-id="b9ec9-274">[Kestrel uç nokta yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b9ec9-275">Bir ASP.NET Core uygulaması, IIS kullanmadan [Windows'da Windows Hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="b9ec9-276">Windows Hizmeti olarak barındırıldığında, uygulama sunucu yeniden başlatıldıktan sonra otomatik olarak başlar.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="b9ec9-277">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b9ec9-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b9ec9-278">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="b9ec9-278">Prerequisites</span></span>

* [<span data-ttu-id="b9ec9-279">ASP.NET Core SDK 2.1 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="b9ec9-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="b9ec9-280">PowerShell 6.2 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="b9ec9-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="b9ec9-281">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b9ec9-281">App configuration</span></span>

<span data-ttu-id="b9ec9-282">Uygulama [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) ve [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)için paket referansları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="b9ec9-283">Bir hizmetin dışında çalışırken test etmek ve hata ayıklamak için, uygulamanın bir hizmet veya konsol uygulaması olarak mı çalıştığını belirlemek için kod ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="b9ec9-284">Hata ayıklayıcının bağlı olup olmadığını `--console` veya bir anahtarın olup olmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="b9ec9-285">Her iki koşul da doğruysa (uygulama bir hizmet <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>olarak çalıştırılmıyorsa), arayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="b9ec9-286">Koşullar yanlışsa (uygulama bir hizmet olarak çalıştırılır):</span><span class="sxs-lookup"><span data-stu-id="b9ec9-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="b9ec9-287">Uygulamanın yayımlanmış konumuna giden yolu arayın <xref:System.IO.Directory.SetCurrentDirectory*> ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="b9ec9-288">Bir Windows <xref:System.IO.Directory.GetCurrentDirectory*> Service uygulaması çağrıldığında *C:\\WINDOWS\\system32* klasörünü döndürdüğünde <xref:System.IO.Directory.GetCurrentDirectory*> yolu almak için aramayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="b9ec9-289">Daha fazla bilgi için [Geçerli dizini ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="b9ec9-290">Bu adım, uygulama 'de `CreateWebHostBuilder`yapılandırılmadan önce gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="b9ec9-291">Uygulamayı <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> hizmet olarak çalıştırmak için arayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="b9ec9-292">Komut [satırı Yapılandırma Sağlayıcısı](xref:fundamentals/configuration/index#command-line-configuration-provider) komut satırı bağımsız değişkenleri için ad `--console` değeri çiftleri gerektirdiğinden, geçiş bağımsız değişkenleri almadan önce <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bağımsız değişkenlerden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="b9ec9-293">Windows Olay Günlüğü'ne yazmak için <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>EventLog sağlayıcısını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="b9ec9-294">Uygulama ayarlarındaki `Logging:LogLevel:Default` anahtarla günlük düzeyini *ayarlayın. Production.json* dosyası.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="b9ec9-295">Örnek uygulamadan aşağıdaki örnekte, `RunAsCustomService` uygulama <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> içindeki yaşam boyu olayları işlemek için yerine çağrılır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="b9ec9-296">Daha fazla bilgi [için, Tanıtıcı başlangıç ve durdurma olayları](#handle-starting-and-stopping-events) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="b9ec9-297">Dağıtım türü</span><span class="sxs-lookup"><span data-stu-id="b9ec9-297">Deployment type</span></span>

<span data-ttu-id="b9ec9-298">Dağıtım senaryoları hakkında bilgi ve tavsiye için [bkz.](/dotnet/core/deploying/)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="b9ec9-299">SDK</span><span class="sxs-lookup"><span data-stu-id="b9ec9-299">SDK</span></span>

<span data-ttu-id="b9ec9-300">Razor Pages veya MVC çerçevelerini kullanan web uygulaması tabanlı bir hizmet için, proje dosyasında Web SDK'yı belirtin:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="b9ec9-301">Hizmet yalnızca arka plan görevlerini yürütürse (örneğin, [barındırılan hizmetler),](xref:fundamentals/host/hosted-services)Proje dosyasında İşçi SDK'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="b9ec9-302">Çerçeveye bağımlı dağıtım (FDD)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="b9ec9-303">Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core'un sistem genelinde paylaşılan bir sürümünün bulunmasına dayanır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="b9ec9-304">FDD senaryosu bu makaledeki kılavuzdan sonra kabul edildiğinde, *SDK, çerçeveye bağımlı yürütülebilir*olarak adlandırılan bir yürütülebilir (*.exe*) üretir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="b9ec9-305">Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>) ](/dotnet/core/tools/csproj#runtimeidentifier)hedef çerçeveiçerir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="b9ec9-306">Aşağıdaki örnekte, RID `win7-x64`olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="b9ec9-307">`false`Özellik. `<SelfContained>`</span><span class="sxs-lookup"><span data-stu-id="b9ec9-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="b9ec9-308">Bu özellikler, SDK'ya Windows için yürütülebilir (*.exe*) dosyası ve paylaşılan .NET Core çerçevesine bağlı bir uygulama oluşturması talimatını verir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="b9ec9-309">Normalde bir ASP.NET Core uygulaması yayımlarken üretilen *bir web.config* dosyası, bir Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="b9ec9-310">*web.config* dosyasının oluşturulmasını devre dışı bırakabilmek `<IsTransformWebConfigDisabled>` `true`için, ayarlanan özelliği ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="b9ec9-311">Bağımsız dağıtım (SCD)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="b9ec9-312">Bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir çerçevenin varlığına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="b9ec9-313">Çalışma süresi ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="b9ec9-314">Bir Windows [Runtime Tanımlayıcısı (RID)](/dotnet/core/rid-catalog) `<PropertyGroup>` hedef çerçeveyi içeren dahildir:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="b9ec9-315">Birden çok RID için yayımlamak için:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="b9ec9-316">RID'leri yarı sütunlu sınırlı bir listede sağlayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="b9ec9-317">[ \<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (çoğul) özellik adını kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="b9ec9-318">Daha fazla bilgi için [.NET Core RID Kataloğu'na](/dotnet/core/rid-catalog)bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="b9ec9-319">Bir `<SelfContained>` özellik ayarlanır: `true`</span><span class="sxs-lookup"><span data-stu-id="b9ec9-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="b9ec9-320">Hizmet kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="b9ec9-320">Service user account</span></span>

<span data-ttu-id="b9ec9-321">Bir hizmet için kullanıcı hesabı oluşturmak için, yönetimsel powershell 6 komut kabuğundan [Yeni-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet'ini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="b9ec9-322">Windows 10 Ekim 2018 Güncelleştirmesi'nde (sürüm 1809/build 10.0.17763) veya daha sonra:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="b9ec9-323">Windows 10 Ekim 2018 Güncelleştirmesinden önceki Windows İşletim Sistemi'nde (sürüm 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="b9ec9-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="b9ec9-324">İstendiğinde güçlü bir [parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="b9ec9-325">Parametre, sona ermiş <xref:System.DateTime> [yeni yerel kullanıcı](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet'e sağatılmadığı sürece, hesabın süresi dolmaz. `-AccountExpires`</span><span class="sxs-lookup"><span data-stu-id="b9ec9-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="b9ec9-326">Daha fazla bilgi için [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [Hizmet Kullanıcı Hesapları'na](/windows/desktop/services/service-user-accounts)bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="b9ec9-327">Active Directory kullanırken kullanıcıları yönetmek için alternatif bir yaklaşım Yönetilen Hizmet Hesapları kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="b9ec9-328">Daha fazla bilgi için [Grup Yönetilen Hizmet Hesaplarına Genel Bakış'a](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="b9ec9-329">Hizmet hakları olarak oturum açma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-329">Log on as a service rights</span></span>

<span data-ttu-id="b9ec9-330">Bir hizmet kullanıcı hesabı için hizmet hakları *olarak Oturum Açma'yı* kurmak için:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="b9ec9-331">*Secpol.msc*çalıştırarak Yerel Güvenlik Politikası düzenleyicisini açın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="b9ec9-332">Yerel **İlkeler** düğümünü genişletin ve **Kullanıcı Hakları Ataması'nı**seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="b9ec9-333">Hizmet ilkesi **olarak Oturum Aç'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="b9ec9-334">**Kullanıcı veya Grup Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="b9ec9-335">Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (kullanıcı hesabı) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="b9ec9-336">Nesne adı alanına`{DOMAIN OR COMPUTER NAME\USER}`kullanıcı hesabı ( ) yazın ve kullanıcıyı ilkeye eklemek için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="b9ec9-337">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-337">Select **Advanced**.</span></span> <span data-ttu-id="b9ec9-338">**Şimdi Bul'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-338">Select **Find Now**.</span></span> <span data-ttu-id="b9ec9-339">Listeden kullanıcı hesabını seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-339">Select the user account from the list.</span></span> <span data-ttu-id="b9ec9-340">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-340">Select **OK**.</span></span> <span data-ttu-id="b9ec9-341">Kullanıcıyı ilkeye eklemek için tekrar **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="b9ec9-342">Değişiklikleri kabul etmek için **Tamam** veya **Uygula'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="b9ec9-343">Windows Hizmeti oluşturma ve yönetme</span><span class="sxs-lookup"><span data-stu-id="b9ec9-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="b9ec9-344">Hizmet oluşturma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-344">Create a service</span></span>

<span data-ttu-id="b9ec9-345">Bir hizmeti kaydetmek için PowerShell komutlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="b9ec9-346">İdari powershell 6 komut uyruktan aşağıdaki komutları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="b9ec9-347">`{EXE PATH}`&ndash; Uygulamanın ana bilgisayardaki klasörüne giden yol `d:\myservice`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-347">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="b9ec9-348">Uygulamanın çalıştırılabilir uygulamasını yola eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="b9ec9-349">İzbırakan bir kesik gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="b9ec9-350">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Hizmet kullanıcı hesabı (örneğin, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-350">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="b9ec9-351">`{SERVICE NAME}`&ndash; Hizmet adı (örneğin, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-351">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="b9ec9-352">`{EXE FILE PATH}`&ndash; Uygulamanın çalıştırılabilir yolu (örneğin, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-352">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="b9ec9-353">Yürütülebilir dosya adını uzantılı olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="b9ec9-354">`{DESCRIPTION}`&ndash; Hizmet açıklaması (örneğin, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-354">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="b9ec9-355">`{DISPLAY NAME}`&ndash; Hizmet görüntü adı (örneğin, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-355">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="b9ec9-356">Bir hizmet başlatın</span><span class="sxs-lookup"><span data-stu-id="b9ec9-356">Start a service</span></span>

<span data-ttu-id="b9ec9-357">Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="b9ec9-358">Komutun hizmeti başlatması birkaç saniye sürer.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="b9ec9-359">Bir hizmetin durumunu belirleme</span><span class="sxs-lookup"><span data-stu-id="b9ec9-359">Determine a service's status</span></span>

<span data-ttu-id="b9ec9-360">Bir hizmetin durumunu kontrol etmek için aşağıdaki PowerShell 6 komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="b9ec9-361">Durum aşağıdaki değerlerden biri olarak bildirilir:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="b9ec9-362">Bir hizmet durdurun</span><span class="sxs-lookup"><span data-stu-id="b9ec9-362">Stop a service</span></span>

<span data-ttu-id="b9ec9-363">Aşağıdaki Powershell 6 komutuna sahip bir hizmeti durdurun:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="b9ec9-364">Bir hizmeti kaldırma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-364">Remove a service</span></span>

<span data-ttu-id="b9ec9-365">Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki Powershell 6 komutuna sahip bir hizmeti kaldırın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="b9ec9-366">Olayları başlatma ve durdurma işlemlerini işleme</span><span class="sxs-lookup"><span data-stu-id="b9ec9-366">Handle starting and stopping events</span></span>

<span data-ttu-id="b9ec9-367">Ve <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> olayları <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>işlemek için:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="b9ec9-368">, `OnStarted`, ve `OnStopping` yöntemleri <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> ile türetilmiştir bir sınıf oluşturun: `OnStarting`</span><span class="sxs-lookup"><span data-stu-id="b9ec9-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="b9ec9-369">Bunun için <xref:Microsoft.AspNetCore.Hosting.IWebHost> bir uzantı `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*>yöntemi oluşturun:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="b9ec9-370">In `Program.Main`, `RunAsCustomService` yerine uzantı <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>yöntemi ni arayın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="b9ec9-371">In'in <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> `Program.Main`yerini görmek için [Dağıtım türü](#deployment-type) bölümünde gösterilen kod örneğine bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b9ec9-372">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="b9ec9-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b9ec9-373">Internet'ten veya şirket ağından gelen isteklerle etkileşimde olan ve proxy veya yük dengeleyicisinin arkasında olan hizmetler ek yapılandırma gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="b9ec9-374">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="b9ec9-375">Uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-375">Configure endpoints</span></span>

<span data-ttu-id="b9ec9-376">Varsayılan olarak, ASP.NET Core `http://localhost:5000`bağlanır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="b9ec9-377">Ortam değişkenini ayarlayarak `ASPNETCORE_URLS` URL'yi ve bağlantı noktasını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="b9ec9-378">Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="b9ec9-379">Önceki kılavuz, HTTPS uç noktaları nın desteğini kapsar.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="b9ec9-380">Örneğin, kimlik doğrulama bir Windows Hizmeti ile kullanıldığında uygulamayı HTTPS için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="b9ec9-381">Bir hizmet bitiş noktasını güvence altına almak için ASP.NET Core HTTPS geliştirme sertifikasının kullanımı desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="b9ec9-382">Geçerli dizin ve içerik kökü</span><span class="sxs-lookup"><span data-stu-id="b9ec9-382">Current directory and content root</span></span>

<span data-ttu-id="b9ec9-383">Bir Windows Hizmeti için <xref:System.IO.Directory.GetCurrentDirectory*> arayarak döndürülen geçerli çalışma dizini *C:\\WINDOWS\\system32* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="b9ec9-384">*System32* klasörü, bir hizmetin dosyalarını depolamak için uygun bir konum değildir (örneğin, ayarlar dosyaları).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="b9ec9-385">Bir hizmetin varlıklarını ve ayar dosyalarını korumak ve bu dosyalara erişmek için aşağıdaki yaklaşımlardan birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="b9ec9-386">İçerik kökü yolunu uygulamanın klasörüne ayarlama</span><span class="sxs-lookup"><span data-stu-id="b9ec9-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="b9ec9-387">Bir <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> hizmet oluşturulduğunda `binPath` bağımsız değişkene sağlanan aynı yoldur.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="b9ec9-388">Ayarlar dosyalarına giden yollar oluşturmak için aramak `GetCurrentDirectory` yerine, uygulamanın içerik <xref:System.IO.Directory.SetCurrentDirectory*> [köküne](xref:fundamentals/index#content-root)giden yolu arayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="b9ec9-389">' `Program.Main`de, hizmetin yürütülebilir klasörüne giden yolu belirleyin ve uygulamanın içerik kökünü oluşturmak için yolu kullanın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="b9ec9-390">Bir hizmetin dosyalarını diskte uygun bir konumda depolama</span><span class="sxs-lookup"><span data-stu-id="b9ec9-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="b9ec9-391">Dosyaları içeren klasöre <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> bir <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> kullanırken ile mutlak bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b9ec9-392">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="b9ec9-392">Troubleshoot</span></span>

<span data-ttu-id="b9ec9-393">Bir Windows Hizmeti uygulamasını gidermek <xref:test/troubleshoot>için bkz.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="b9ec9-394">Sık karşılaşılan hatalar</span><span class="sxs-lookup"><span data-stu-id="b9ec9-394">Common errors</span></span>

* <span data-ttu-id="b9ec9-395">PowerShell'in eski veya ön sürüm sürümü kullanımdadır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="b9ec9-396">Kayıtlı hizmet, uygulamanın [yayınlanan dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutundan **çıkan** çıktısını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="b9ec9-397">[Dotnet yapı](/dotnet/core/tools/dotnet-build) komutunun çıktısı uygulama dağıtımı için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="b9ec9-398">Yayımlanmış varlıklar dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="b9ec9-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="b9ec9-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="b9ec9-401">Hizmet RUNNING durumunda değil.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="b9ec9-402">Uygulamanın kullandığı kaynaklara giden yollar (örneğin, sertifikalar) yanlıştır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="b9ec9-403">Bir Windows Hizmetinin temel yolu *c:\\Windows\\System32.*</span><span class="sxs-lookup"><span data-stu-id="b9ec9-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="b9ec9-404">Kullanıcının hizmet olarak *Oturum* Açma'sı yoktur.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="b9ec9-405">PowerShell komutunu çalıştırırken kullanıcının parolasının `New-Service` süresi doldu veya yanlış geçti.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="b9ec9-406">Uygulama ASP.NET Çekirdek kimlik doğrulaması gerektirir, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamıştır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="b9ec9-407">İstek URL bağlantı noktası yanlış veya uygulamada doğru yapılandırılmamıştır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="b9ec9-408">Sistem ve Uygulama Etkinlik Günlükleri</span><span class="sxs-lookup"><span data-stu-id="b9ec9-408">System and Application Event Logs</span></span>

<span data-ttu-id="b9ec9-409">Sistem ve Uygulama Etkinlik Günlüklerine Erişin:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="b9ec9-410">Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="b9ec9-411">**Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="b9ec9-412">Sistem Olay Günlüğü'nü açmak için **Sistem'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="b9ec9-413">Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="b9ec9-414">Başarısız uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="b9ec9-415">Uygulamayı komut istemiyle çalıştırma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-415">Run the app at a command prompt</span></span>

<span data-ttu-id="b9ec9-416">Birçok başlangıç hatası olay günlüklerinde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="b9ec9-417">Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="b9ec9-418">Uygulamadan ek ayrıntı günlüğe kaydetmek [için, günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [uygulamayı Geliştirme ortamında](xref:fundamentals/environments)çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="b9ec9-419">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="b9ec9-419">Clear package caches</span></span>

<span data-ttu-id="b9ec9-420">İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="b9ec9-421">Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="b9ec9-422">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="b9ec9-423">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="b9ec9-424">[Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="b9ec9-425">Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="b9ec9-426">*nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="b9ec9-427">Projeyi geri yükleyin ve yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="b9ec9-428">Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="b9ec9-429">Yavaş veya asma uygulaması</span><span class="sxs-lookup"><span data-stu-id="b9ec9-429">Slow or hanging app</span></span>

<span data-ttu-id="b9ec9-430">*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="b9ec9-431">Uygulama çöküyor veya bir özel durumla karşılaşıyor</span><span class="sxs-lookup"><span data-stu-id="b9ec9-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="b9ec9-432">Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="b9ec9-433">Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun</span><span class="sxs-lookup"><span data-stu-id="b9ec9-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="b9ec9-434">[EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) uygulama çalıştırılabilir adla çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="b9ec9-435">Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="b9ec9-436">Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="b9ec9-437">Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="b9ec9-438">PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="b9ec9-439">Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="b9ec9-440">Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır</span><span class="sxs-lookup"><span data-stu-id="b9ec9-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="b9ec9-441">Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="b9ec9-442">Dökümü analiz edin</span><span class="sxs-lookup"><span data-stu-id="b9ec9-442">Analyze the dump</span></span>

<span data-ttu-id="b9ec9-443">Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="b9ec9-444">Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9ec9-445">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b9ec9-445">Additional resources</span></span>

* <span data-ttu-id="b9ec9-446">[Kestrel uç nokta yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b9ec9-447">Bir ASP.NET Core uygulaması, IIS kullanmadan [Windows'da Windows Hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="b9ec9-448">Windows Hizmeti olarak barındırıldığında, uygulama sunucu yeniden başlatıldıktan sonra otomatik olarak başlar.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="b9ec9-449">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b9ec9-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b9ec9-450">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="b9ec9-450">Prerequisites</span></span>

* [<span data-ttu-id="b9ec9-451">ASP.NET Core SDK 2.1 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="b9ec9-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="b9ec9-452">PowerShell 6.2 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="b9ec9-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="b9ec9-453">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b9ec9-453">App configuration</span></span>

<span data-ttu-id="b9ec9-454">Uygulama [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) ve [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)için paket referansları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="b9ec9-455">Bir hizmetin dışında çalışırken test etmek ve hata ayıklamak için, uygulamanın bir hizmet veya konsol uygulaması olarak mı çalıştığını belirlemek için kod ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="b9ec9-456">Hata ayıklayıcının bağlı olup olmadığını `--console` veya bir anahtarın olup olmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="b9ec9-457">Her iki koşul da doğruysa (uygulama bir hizmet <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>olarak çalıştırılmıyorsa), arayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="b9ec9-458">Koşullar yanlışsa (uygulama bir hizmet olarak çalıştırılır):</span><span class="sxs-lookup"><span data-stu-id="b9ec9-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="b9ec9-459">Uygulamanın yayımlanmış konumuna giden yolu arayın <xref:System.IO.Directory.SetCurrentDirectory*> ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="b9ec9-460">Bir Windows <xref:System.IO.Directory.GetCurrentDirectory*> Service uygulaması çağrıldığında *C:\\WINDOWS\\system32* klasörünü döndürdüğünde <xref:System.IO.Directory.GetCurrentDirectory*> yolu almak için aramayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="b9ec9-461">Daha fazla bilgi için [Geçerli dizini ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="b9ec9-462">Bu adım, uygulama 'de `CreateWebHostBuilder`yapılandırılmadan önce gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="b9ec9-463">Uygulamayı <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> hizmet olarak çalıştırmak için arayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="b9ec9-464">Komut [satırı Yapılandırma Sağlayıcısı](xref:fundamentals/configuration/index#command-line-configuration-provider) komut satırı bağımsız değişkenleri için ad `--console` değeri çiftleri gerektirdiğinden, geçiş bağımsız değişkenleri almadan önce <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bağımsız değişkenlerden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="b9ec9-465">Windows Olay Günlüğü'ne yazmak için <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>EventLog sağlayıcısını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="b9ec9-466">Uygulama ayarlarındaki `Logging:LogLevel:Default` anahtarla günlük düzeyini *ayarlayın. Production.json* dosyası.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="b9ec9-467">Örnek uygulamadan aşağıdaki örnekte, `RunAsCustomService` uygulama <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> içindeki yaşam boyu olayları işlemek için yerine çağrılır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="b9ec9-468">Daha fazla bilgi [için, Tanıtıcı başlangıç ve durdurma olayları](#handle-starting-and-stopping-events) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="b9ec9-469">Dağıtım türü</span><span class="sxs-lookup"><span data-stu-id="b9ec9-469">Deployment type</span></span>

<span data-ttu-id="b9ec9-470">Dağıtım senaryoları hakkında bilgi ve tavsiye için [bkz.](/dotnet/core/deploying/)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="b9ec9-471">SDK</span><span class="sxs-lookup"><span data-stu-id="b9ec9-471">SDK</span></span>

<span data-ttu-id="b9ec9-472">Razor Pages veya MVC çerçevelerini kullanan web uygulaması tabanlı bir hizmet için, proje dosyasında Web SDK'yı belirtin:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="b9ec9-473">Hizmet yalnızca arka plan görevlerini yürütürse (örneğin, [barındırılan hizmetler),](xref:fundamentals/host/hosted-services)Proje dosyasında İşçi SDK'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="b9ec9-474">Çerçeveye bağımlı dağıtım (FDD)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="b9ec9-475">Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core'un sistem genelinde paylaşılan bir sürümünün bulunmasına dayanır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="b9ec9-476">FDD senaryosu bu makaledeki kılavuzdan sonra kabul edildiğinde, *SDK, çerçeveye bağımlı yürütülebilir*olarak adlandırılan bir yürütülebilir (*.exe*) üretir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="b9ec9-477">Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>) ](/dotnet/core/tools/csproj#runtimeidentifier)hedef çerçeveiçerir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="b9ec9-478">Aşağıdaki örnekte, RID `win7-x64`olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="b9ec9-479">`false`Özellik. `<SelfContained>`</span><span class="sxs-lookup"><span data-stu-id="b9ec9-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="b9ec9-480">Bu özellikler, SDK'ya Windows için yürütülebilir (*.exe*) dosyası ve paylaşılan .NET Core çerçevesine bağlı bir uygulama oluşturması talimatını verir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="b9ec9-481">`true`Özellik. `<UseAppHost>`</span><span class="sxs-lookup"><span data-stu-id="b9ec9-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="b9ec9-482">Bu özellik, hizmete bir FDD için etkinleştirme yolu (yürütülebilir, *.exe)* sağlar.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="b9ec9-483">Normalde bir ASP.NET Core uygulaması yayımlarken üretilen *bir web.config* dosyası, bir Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="b9ec9-484">*web.config* dosyasının oluşturulmasını devre dışı bırakabilmek `<IsTransformWebConfigDisabled>` `true`için, ayarlanan özelliği ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="b9ec9-485">Bağımsız dağıtım (SCD)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="b9ec9-486">Bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir çerçevenin varlığına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="b9ec9-487">Çalışma süresi ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="b9ec9-488">Bir Windows [Runtime Tanımlayıcısı (RID)](/dotnet/core/rid-catalog) `<PropertyGroup>` hedef çerçeveyi içeren dahildir:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="b9ec9-489">Birden çok RID için yayımlamak için:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="b9ec9-490">RID'leri yarı sütunlu sınırlı bir listede sağlayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="b9ec9-491">[ \<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (çoğul) özellik adını kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="b9ec9-492">Daha fazla bilgi için [.NET Core RID Kataloğu'na](/dotnet/core/rid-catalog)bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="b9ec9-493">Bir `<SelfContained>` özellik ayarlanır: `true`</span><span class="sxs-lookup"><span data-stu-id="b9ec9-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="b9ec9-494">Hizmet kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="b9ec9-494">Service user account</span></span>

<span data-ttu-id="b9ec9-495">Bir hizmet için kullanıcı hesabı oluşturmak için, yönetimsel powershell 6 komut kabuğundan [Yeni-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet'ini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="b9ec9-496">Windows 10 Ekim 2018 Güncelleştirmesi'nde (sürüm 1809/build 10.0.17763) veya daha sonra:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="b9ec9-497">Windows 10 Ekim 2018 Güncelleştirmesinden önceki Windows İşletim Sistemi'nde (sürüm 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="b9ec9-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="b9ec9-498">İstendiğinde güçlü bir [parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="b9ec9-499">Parametre, sona ermiş <xref:System.DateTime> [yeni yerel kullanıcı](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet'e sağatılmadığı sürece, hesabın süresi dolmaz. `-AccountExpires`</span><span class="sxs-lookup"><span data-stu-id="b9ec9-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="b9ec9-500">Daha fazla bilgi için [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [Hizmet Kullanıcı Hesapları'na](/windows/desktop/services/service-user-accounts)bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="b9ec9-501">Active Directory kullanırken kullanıcıları yönetmek için alternatif bir yaklaşım Yönetilen Hizmet Hesapları kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="b9ec9-502">Daha fazla bilgi için [Grup Yönetilen Hizmet Hesaplarına Genel Bakış'a](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="b9ec9-503">Hizmet hakları olarak oturum açma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-503">Log on as a service rights</span></span>

<span data-ttu-id="b9ec9-504">Bir hizmet kullanıcı hesabı için hizmet hakları *olarak Oturum Açma'yı* kurmak için:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="b9ec9-505">*Secpol.msc*çalıştırarak Yerel Güvenlik Politikası düzenleyicisini açın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="b9ec9-506">Yerel **İlkeler** düğümünü genişletin ve **Kullanıcı Hakları Ataması'nı**seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="b9ec9-507">Hizmet ilkesi **olarak Oturum Aç'ı** açın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="b9ec9-508">**Kullanıcı veya Grup Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="b9ec9-509">Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (kullanıcı hesabı) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="b9ec9-510">Nesne adı alanına`{DOMAIN OR COMPUTER NAME\USER}`kullanıcı hesabı ( ) yazın ve kullanıcıyı ilkeye eklemek için **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="b9ec9-511">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-511">Select **Advanced**.</span></span> <span data-ttu-id="b9ec9-512">**Şimdi Bul'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-512">Select **Find Now**.</span></span> <span data-ttu-id="b9ec9-513">Listeden kullanıcı hesabını seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-513">Select the user account from the list.</span></span> <span data-ttu-id="b9ec9-514">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-514">Select **OK**.</span></span> <span data-ttu-id="b9ec9-515">Kullanıcıyı ilkeye eklemek için tekrar **Tamam'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="b9ec9-516">Değişiklikleri kabul etmek için **Tamam** veya **Uygula'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="b9ec9-517">Windows Hizmeti oluşturma ve yönetme</span><span class="sxs-lookup"><span data-stu-id="b9ec9-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="b9ec9-518">Hizmet oluşturma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-518">Create a service</span></span>

<span data-ttu-id="b9ec9-519">Bir hizmeti kaydetmek için PowerShell komutlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="b9ec9-520">İdari powershell 6 komut uyruktan aşağıdaki komutları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="b9ec9-521">`{EXE PATH}`&ndash; Uygulamanın ana bilgisayardaki klasörüne giden yol `d:\myservice`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-521">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="b9ec9-522">Uygulamanın çalıştırılabilir uygulamasını yola eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="b9ec9-523">İzbırakan bir kesik gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="b9ec9-524">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Hizmet kullanıcı hesabı (örneğin, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-524">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="b9ec9-525">`{SERVICE NAME}`&ndash; Hizmet adı (örneğin, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-525">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="b9ec9-526">`{EXE FILE PATH}`&ndash; Uygulamanın çalıştırılabilir yolu (örneğin, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-526">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="b9ec9-527">Yürütülebilir dosya adını uzantılı olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="b9ec9-528">`{DESCRIPTION}`&ndash; Hizmet açıklaması (örneğin, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-528">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="b9ec9-529">`{DISPLAY NAME}`&ndash; Hizmet görüntü adı (örneğin, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-529">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="b9ec9-530">Bir hizmet başlatın</span><span class="sxs-lookup"><span data-stu-id="b9ec9-530">Start a service</span></span>

<span data-ttu-id="b9ec9-531">Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="b9ec9-532">Komutun hizmeti başlatması birkaç saniye sürer.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="b9ec9-533">Bir hizmetin durumunu belirleme</span><span class="sxs-lookup"><span data-stu-id="b9ec9-533">Determine a service's status</span></span>

<span data-ttu-id="b9ec9-534">Bir hizmetin durumunu kontrol etmek için aşağıdaki PowerShell 6 komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="b9ec9-535">Durum aşağıdaki değerlerden biri olarak bildirilir:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="b9ec9-536">Bir hizmet durdurun</span><span class="sxs-lookup"><span data-stu-id="b9ec9-536">Stop a service</span></span>

<span data-ttu-id="b9ec9-537">Aşağıdaki Powershell 6 komutuna sahip bir hizmeti durdurun:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="b9ec9-538">Bir hizmeti kaldırma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-538">Remove a service</span></span>

<span data-ttu-id="b9ec9-539">Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki Powershell 6 komutuna sahip bir hizmeti kaldırın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="b9ec9-540">Olayları başlatma ve durdurma işlemlerini işleme</span><span class="sxs-lookup"><span data-stu-id="b9ec9-540">Handle starting and stopping events</span></span>

<span data-ttu-id="b9ec9-541">Ve <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> olayları <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>işlemek için:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="b9ec9-542">, `OnStarted`, ve `OnStopping` yöntemleri <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> ile türetilmiştir bir sınıf oluşturun: `OnStarting`</span><span class="sxs-lookup"><span data-stu-id="b9ec9-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="b9ec9-543">Bunun için <xref:Microsoft.AspNetCore.Hosting.IWebHost> bir uzantı `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*>yöntemi oluşturun:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="b9ec9-544">In `Program.Main`, `RunAsCustomService` yerine uzantı <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>yöntemi ni arayın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="b9ec9-545">In'in <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> `Program.Main`yerini görmek için [Dağıtım türü](#deployment-type) bölümünde gösterilen kod örneğine bakın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b9ec9-546">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="b9ec9-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b9ec9-547">Internet'ten veya şirket ağından gelen isteklerle etkileşimde olan ve proxy veya yük dengeleyicisinin arkasında olan hizmetler ek yapılandırma gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="b9ec9-548">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="b9ec9-549">Uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-549">Configure endpoints</span></span>

<span data-ttu-id="b9ec9-550">Varsayılan olarak, ASP.NET Core `http://localhost:5000`bağlanır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="b9ec9-551">Ortam değişkenini ayarlayarak `ASPNETCORE_URLS` URL'yi ve bağlantı noktasını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="b9ec9-552">Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="b9ec9-553">Önceki kılavuz, HTTPS uç noktaları nın desteğini kapsar.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="b9ec9-554">Örneğin, kimlik doğrulama bir Windows Hizmeti ile kullanıldığında uygulamayı HTTPS için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="b9ec9-555">Bir hizmet bitiş noktasını güvence altına almak için ASP.NET Core HTTPS geliştirme sertifikasının kullanımı desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="b9ec9-556">Geçerli dizin ve içerik kökü</span><span class="sxs-lookup"><span data-stu-id="b9ec9-556">Current directory and content root</span></span>

<span data-ttu-id="b9ec9-557">Bir Windows Hizmeti için <xref:System.IO.Directory.GetCurrentDirectory*> arayarak döndürülen geçerli çalışma dizini *C:\\WINDOWS\\system32* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="b9ec9-558">*System32* klasörü, bir hizmetin dosyalarını depolamak için uygun bir konum değildir (örneğin, ayarlar dosyaları).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="b9ec9-559">Bir hizmetin varlıklarını ve ayar dosyalarını korumak ve bu dosyalara erişmek için aşağıdaki yaklaşımlardan birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="b9ec9-560">İçerik kökü yolunu uygulamanın klasörüne ayarlama</span><span class="sxs-lookup"><span data-stu-id="b9ec9-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="b9ec9-561">Bir <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> hizmet oluşturulduğunda `binPath` bağımsız değişkene sağlanan aynı yoldur.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="b9ec9-562">Ayarlar dosyalarına giden yollar oluşturmak için aramak `GetCurrentDirectory` yerine, uygulamanın içerik <xref:System.IO.Directory.SetCurrentDirectory*> [köküne](xref:fundamentals/index#content-root)giden yolu arayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="b9ec9-563">' `Program.Main`de, hizmetin yürütülebilir klasörüne giden yolu belirleyin ve uygulamanın içerik kökünü oluşturmak için yolu kullanın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="b9ec9-564">Bir hizmetin dosyalarını diskte uygun bir konumda depolama</span><span class="sxs-lookup"><span data-stu-id="b9ec9-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="b9ec9-565">Dosyaları içeren klasöre <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> bir <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> kullanırken ile mutlak bir yol belirtin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b9ec9-566">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="b9ec9-566">Troubleshoot</span></span>

<span data-ttu-id="b9ec9-567">Bir Windows Hizmeti uygulamasını gidermek <xref:test/troubleshoot>için bkz.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="b9ec9-568">Sık karşılaşılan hatalar</span><span class="sxs-lookup"><span data-stu-id="b9ec9-568">Common errors</span></span>

* <span data-ttu-id="b9ec9-569">PowerShell'in eski veya ön sürüm sürümü kullanımdadır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="b9ec9-570">Kayıtlı hizmet, uygulamanın [yayınlanan dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutundan **çıkan** çıktısını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="b9ec9-571">[Dotnet yapı](/dotnet/core/tools/dotnet-build) komutunun çıktısı uygulama dağıtımı için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="b9ec9-572">Yayımlanmış varlıklar dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="b9ec9-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="b9ec9-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="b9ec9-575">Hizmet RUNNING durumunda değil.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="b9ec9-576">Uygulamanın kullandığı kaynaklara giden yollar (örneğin, sertifikalar) yanlıştır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="b9ec9-577">Bir Windows Hizmetinin temel yolu *c:\\Windows\\System32.*</span><span class="sxs-lookup"><span data-stu-id="b9ec9-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="b9ec9-578">Kullanıcının hizmet olarak *Oturum* Açma'sı yoktur.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="b9ec9-579">PowerShell komutunu çalıştırırken kullanıcının parolasının `New-Service` süresi doldu veya yanlış geçti.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="b9ec9-580">Uygulama ASP.NET Çekirdek kimlik doğrulaması gerektirir, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamıştır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="b9ec9-581">İstek URL bağlantı noktası yanlış veya uygulamada doğru yapılandırılmamıştır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="b9ec9-582">Sistem ve Uygulama Etkinlik Günlükleri</span><span class="sxs-lookup"><span data-stu-id="b9ec9-582">System and Application Event Logs</span></span>

<span data-ttu-id="b9ec9-583">Sistem ve Uygulama Etkinlik Günlüklerine Erişin:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="b9ec9-584">Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="b9ec9-585">**Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="b9ec9-586">Sistem Olay Günlüğü'nü açmak için **Sistem'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="b9ec9-587">Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="b9ec9-588">Başarısız uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="b9ec9-589">Uygulamayı komut istemiyle çalıştırma</span><span class="sxs-lookup"><span data-stu-id="b9ec9-589">Run the app at a command prompt</span></span>

<span data-ttu-id="b9ec9-590">Birçok başlangıç hatası olay günlüklerinde yararlı bilgiler üretmez.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="b9ec9-591">Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="b9ec9-592">Uygulamadan ek ayrıntı günlüğe kaydetmek [için, günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [uygulamayı Geliştirme ortamında](xref:fundamentals/environments)çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="b9ec9-593">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="b9ec9-593">Clear package caches</span></span>

<span data-ttu-id="b9ec9-594">İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="b9ec9-595">Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="b9ec9-596">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="b9ec9-597">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="b9ec9-598">[Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="b9ec9-599">Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="b9ec9-600">*nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="b9ec9-601">Projeyi geri yükleyin ve yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="b9ec9-602">Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="b9ec9-603">Yavaş veya asma uygulaması</span><span class="sxs-lookup"><span data-stu-id="b9ec9-603">Slow or hanging app</span></span>

<span data-ttu-id="b9ec9-604">*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="b9ec9-605">Uygulama çöküyor veya bir özel durumla karşılaşıyor</span><span class="sxs-lookup"><span data-stu-id="b9ec9-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="b9ec9-606">Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="b9ec9-607">Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun</span><span class="sxs-lookup"><span data-stu-id="b9ec9-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="b9ec9-608">[EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) uygulama çalıştırılabilir adla çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="b9ec9-609">Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="b9ec9-610">Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9ec9-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="b9ec9-611">Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="b9ec9-612">PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="b9ec9-613">Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).</span><span class="sxs-lookup"><span data-stu-id="b9ec9-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="b9ec9-614">Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır</span><span class="sxs-lookup"><span data-stu-id="b9ec9-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="b9ec9-615">Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="b9ec9-616">Dökümü analiz edin</span><span class="sxs-lookup"><span data-stu-id="b9ec9-616">Analyze the dump</span></span>

<span data-ttu-id="b9ec9-617">Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="b9ec9-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="b9ec9-618">Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9ec9-619">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b9ec9-619">Additional resources</span></span>

* <span data-ttu-id="b9ec9-620">[Kestrel uç nokta yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="b9ec9-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
