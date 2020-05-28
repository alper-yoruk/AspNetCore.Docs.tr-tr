---
<span data-ttu-id="420d3-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="420d3-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="420d3-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="420d3-102">'Blazor'</span></span>
- <span data-ttu-id="420d3-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="420d3-103">'Identity'</span></span>
- <span data-ttu-id="420d3-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="420d3-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="420d3-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="420d3-105">'Razor'</span></span>
- <span data-ttu-id="420d3-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="420d3-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="420d3-107">Windows hizmetinde konak ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="420d3-107">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="420d3-108">Bir ASP.NET Core uygulaması, IIS kullanmadan Windows [hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak Windows üzerinde barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-108">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="420d3-109">Windows hizmeti olarak barındırıldığı zaman, uygulama otomatik olarak sunucu yeniden başlatıldıktan sonra başlatılır.</span><span class="sxs-lookup"><span data-stu-id="420d3-109">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="420d3-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="420d3-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="420d3-111">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="420d3-111">Prerequisites</span></span>

* [<span data-ttu-id="420d3-112">ASP.NET Core SDK 2,1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="420d3-112">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="420d3-113">PowerShell 6,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="420d3-113">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="420d3-114">Çalışan hizmeti şablonu</span><span class="sxs-lookup"><span data-stu-id="420d3-114">Worker Service template</span></span>

<span data-ttu-id="420d3-115">ASP.NET Core Worker hizmeti şablonu, uzun süre çalışan hizmet uygulamalarını yazmak için bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="420d3-115">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="420d3-116">Şablonu bir Windows Hizmeti uygulamasının temeli olarak kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="420d3-116">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="420d3-117">.NET Core şablonundan bir çalışan hizmeti uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="420d3-117">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="420d3-118">Çalışan hizmeti uygulamasını bir Windows hizmeti olarak çalışacak şekilde güncelleştirmek için [uygulama yapılandırma](#app-configuration) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-118">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="420d3-119">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="420d3-119">App configuration</span></span>

<span data-ttu-id="420d3-120">Uygulama, [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)için bir paket başvurusu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="420d3-120">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="420d3-121">`IHostBuilder.UseWindowsService`Ana bilgisayar oluşturulurken çağrılır.</span><span class="sxs-lookup"><span data-stu-id="420d3-121">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="420d3-122">Uygulama bir Windows hizmeti olarak çalışıyorsa, yöntemi:</span><span class="sxs-lookup"><span data-stu-id="420d3-122">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="420d3-123">Ana bilgisayar ömrünü olarak ayarlar `WindowsServiceLifetime` .</span><span class="sxs-lookup"><span data-stu-id="420d3-123">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="420d3-124">[İçerik kökünü](xref:fundamentals/index#content-root) [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory)olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="420d3-124">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="420d3-125">Daha fazla bilgi için [geçerli dizin ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="420d3-125">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="420d3-126">Olay günlüğüne kaydetmeyi sağlar:</span><span class="sxs-lookup"><span data-stu-id="420d3-126">Enables logging to the event log:</span></span>
  * <span data-ttu-id="420d3-127">Uygulama adı varsayılan kaynak adı olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="420d3-127">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="420d3-128">Varsayılan günlük düzeyi, *Warning* `CreateDefaultBuilder` ana bilgisayarı oluşturmak için çağıran ASP.NET Core şablonuna dayalı bir uygulama için uyarı veya daha yüksek bir uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="420d3-128">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="420d3-129">Varsayılan günlük düzeyini `Logging:EventLog:LogLevel:Default` *appSettings. JSON*appSettings içindeki anahtarla geçersiz kılın / *. { Environment}. JSON* veya diğer yapılandırma sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="420d3-129">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="420d3-130">Yeni olay kaynakları yalnızca yöneticiler tarafından oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-130">Only administrators can create new event sources.</span></span> <span data-ttu-id="420d3-131">Uygulama adı kullanılarak bir olay kaynağı oluşturuoluşturumadığında, *uygulama* kaynağına bir uyarı kaydedilir ve olay günlükleri devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="420d3-131">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="420d3-132">`CreateHostBuilder` *Program.cs*' de:</span><span class="sxs-lookup"><span data-stu-id="420d3-132">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="420d3-133">Aşağıdaki örnek uygulamalar bu konuya eşlik eder:</span><span class="sxs-lookup"><span data-stu-id="420d3-133">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="420d3-134">Arka plan çalışan hizmeti örneği: arka plan görevleri için [barındırılan Hizmetleri](xref:fundamentals/host/hosted-services) kullanan [çalışan hizmeti şablonunu](#worker-service-template) temel alan, Web olmayan bir uygulama örneği.</span><span class="sxs-lookup"><span data-stu-id="420d3-134">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="420d3-135">Web App Service örneği: Razor arka plan görevleri için [barındırılan hizmetlerle](xref:fundamentals/host/hosted-services) Windows hizmeti olarak çalışan bir sayfalar Web uygulaması örneği.</span><span class="sxs-lookup"><span data-stu-id="420d3-135">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="420d3-136">MVC Kılavuzu için ve altındaki makalelere bakın <xref:mvc/overview> <xref:migration/22-to-30> .</span><span class="sxs-lookup"><span data-stu-id="420d3-136">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="420d3-137">Dağıtım türü</span><span class="sxs-lookup"><span data-stu-id="420d3-137">Deployment type</span></span>

<span data-ttu-id="420d3-138">Dağıtım senaryoları hakkında bilgi ve öneriler için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="420d3-138">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="420d3-139">SDK</span><span class="sxs-lookup"><span data-stu-id="420d3-139">SDK</span></span>

<span data-ttu-id="420d3-140">Sayfaları veya MVC çerçevelerini kullanan bir Web uygulaması tabanlı hizmet için Razor , proje dosyasında Web SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="420d3-140">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="420d3-141">Hizmet yalnızca arka plan görevlerini (örneğin, [barındırılan hizmetler](xref:fundamentals/host/hosted-services)) yürütülüyorsa, proje dosyasında çalışan SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="420d3-141">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="420d3-142">Çerçeveye bağımlı dağıtım (FDD)</span><span class="sxs-lookup"><span data-stu-id="420d3-142">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="420d3-143">Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core 'un paylaşılan sistem genelindeki bir sürümünün varlığına dayanır.</span><span class="sxs-lookup"><span data-stu-id="420d3-143">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="420d3-144">Bu makaledeki kılavuzdan sonra FDD senaryosu benimsendiği zaman SDK, *çerçeveye bağlı yürütülebilir dosya*olarak adlandırılan yürütülebilir bir dosya (*. exe*) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="420d3-144">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="420d3-145">[Web SDK](#sdk)kullanıyorsanız, normalde bir ASP.NET Core uygulaması yayımlarken üretilen bir *Web. config* dosyası Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="420d3-145">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="420d3-146">*Web. config* dosyasının oluşturulmasını devre dışı bırakmak için, `<IsTransformWebConfigDisabled>` özelliğini öğesine ekleyin `true` .</span><span class="sxs-lookup"><span data-stu-id="420d3-146">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="420d3-147">Kendi içinde dağıtım (SCD)</span><span class="sxs-lookup"><span data-stu-id="420d3-147">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="420d3-148">Kendinden bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir Framework varlığına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="420d3-148">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="420d3-149">Çalışma zamanı ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="420d3-149">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="420d3-150">Hedef Framework 'ü içeren ' de bir Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) bulunur `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="420d3-150">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="420d3-151">Birden çok RID için yayımlamak için:</span><span class="sxs-lookup"><span data-stu-id="420d3-151">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="420d3-152">RID 'leri, noktalı virgülle ayrılmış bir liste olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="420d3-152">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="420d3-153">Özellik adını [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural) kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-153">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="420d3-154">Daha fazla bilgi için bkz. [.NET Core RID kataloğu](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="420d3-154">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="420d3-155">Hizmet Kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="420d3-155">Service user account</span></span>

<span data-ttu-id="420d3-156">Bir hizmet için Kullanıcı hesabı oluşturmak için, bir yönetim PowerShell 6 komut kabuğundan [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ini kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-156">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="420d3-157">Windows 10 Ekim 2018 Güncelleştirmesi (sürüm 1809/Build 10.0.17763) veya sonraki sürümler:</span><span class="sxs-lookup"><span data-stu-id="420d3-157">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="420d3-158">Windows 10 Ekim 2018 (sürüm 1809/Build 10.0.17763) sürümünden önceki Windows IŞLETIM sistemlerinde:</span><span class="sxs-lookup"><span data-stu-id="420d3-158">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="420d3-159">İstendiğinde [güçlü bir parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.</span><span class="sxs-lookup"><span data-stu-id="420d3-159">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="420d3-160">`-AccountExpires`Parametresi [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ine bir süre sonu ile sağlanmamışsa <xref:System.DateTime> hesabın süresi dolmaz.</span><span class="sxs-lookup"><span data-stu-id="420d3-160">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="420d3-161">Daha fazla bilgi için bkz. [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [hizmet Kullanıcı hesapları](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="420d3-161">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="420d3-162">Active Directory kullanırken kullanıcıları yönetmeye yönelik alternatif bir yaklaşım, yönetilen hizmet hesaplarını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="420d3-162">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="420d3-163">Daha fazla bilgi için bkz. [Grup yönetilen hizmet hesaplarına genel bakış](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="420d3-163">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="420d3-164">Hizmet hakları olarak oturum açma</span><span class="sxs-lookup"><span data-stu-id="420d3-164">Log on as a service rights</span></span>

<span data-ttu-id="420d3-165">Hizmet Kullanıcı hesabı için *hizmet hakları olarak oturum* açma oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="420d3-165">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="420d3-166">Yerel Güvenlik Ilkesi düzenleyicisini, *secpol. msc*' i çalıştırarak açın.</span><span class="sxs-lookup"><span data-stu-id="420d3-166">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="420d3-167">**Yerel ilkeler** düğümünü genişletin ve **Kullanıcı hakları ataması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-167">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="420d3-168">**Hizmet olarak oturum** açma ilkesi açın.</span><span class="sxs-lookup"><span data-stu-id="420d3-168">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="420d3-169">**Kullanıcı veya Grup Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-169">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="420d3-170">Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (Kullanıcı hesabı) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="420d3-170">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="420d3-171">Kullanıcı hesabını ( `{DOMAIN OR COMPUTER NAME\USER}` ) nesne adı alanına yazın ve kullanıcıyı ilkeye eklemek Için **Tamam** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-171">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="420d3-172">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-172">Select **Advanced**.</span></span> <span data-ttu-id="420d3-173">**Şimdi bul**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-173">Select **Find Now**.</span></span> <span data-ttu-id="420d3-174">Listeden Kullanıcı hesabını seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-174">Select the user account from the list.</span></span> <span data-ttu-id="420d3-175">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-175">Select **OK**.</span></span> <span data-ttu-id="420d3-176">Kullanıcıyı ilkeye eklemek için yeniden **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-176">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="420d3-177">Değişiklikleri kabul etmek için **Tamam ' ı** veya **Uygula** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-177">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="420d3-178">Windows hizmetini oluşturma ve yönetme</span><span class="sxs-lookup"><span data-stu-id="420d3-178">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="420d3-179">Hizmet oluşturma</span><span class="sxs-lookup"><span data-stu-id="420d3-179">Create a service</span></span>

<span data-ttu-id="420d3-180">Bir hizmeti kaydetmek için PowerShell komutlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-180">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="420d3-181">Bir yönetim PowerShell 6 komut kabuğundan aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="420d3-181">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="420d3-182">`{EXE PATH}`: Konaktaki uygulamanın klasörünün yolu (örneğin, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-182">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="420d3-183">Uygulamanın yürütülebilir dosyasını yola eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-183">Don't include the app's executable in the path.</span></span> <span data-ttu-id="420d3-184">Sondaki eğik çizgi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="420d3-184">A trailing slash isn't required.</span></span>
* <span data-ttu-id="420d3-185">`{DOMAIN OR COMPUTER NAME\USER}`: Hizmet Kullanıcı hesabı (örneğin, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-185">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="420d3-186">`{SERVICE NAME}`: Hizmet adı (örneğin, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-186">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="420d3-187">`{EXE FILE PATH}`: Uygulamanın yürütülebilir yolu (örneğin, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-187">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="420d3-188">Yürütülebilir dosyanın dosya adını uzantısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-188">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="420d3-189">`{DESCRIPTION}`: Hizmet açıklaması (örneğin, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-189">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="420d3-190">`{DISPLAY NAME}`: Hizmet görünen adı (örneğin, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-190">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="420d3-191">Bir hizmet başlatın</span><span class="sxs-lookup"><span data-stu-id="420d3-191">Start a service</span></span>

<span data-ttu-id="420d3-192">Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:</span><span class="sxs-lookup"><span data-stu-id="420d3-192">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="420d3-193">Komutun başlatılması birkaç saniye sürer.</span><span class="sxs-lookup"><span data-stu-id="420d3-193">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="420d3-194">Hizmetin durumunu belirleme</span><span class="sxs-lookup"><span data-stu-id="420d3-194">Determine a service's status</span></span>

<span data-ttu-id="420d3-195">Bir hizmetin durumunu denetlemek için aşağıdaki PowerShell 6 komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="420d3-195">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="420d3-196">Durum, aşağıdaki değerlerden biri olarak bildirilir:</span><span class="sxs-lookup"><span data-stu-id="420d3-196">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="420d3-197">Bir hizmet durdurun</span><span class="sxs-lookup"><span data-stu-id="420d3-197">Stop a service</span></span>

<span data-ttu-id="420d3-198">Aşağıdaki PowerShell 6 komutuyla bir hizmeti durdurun:</span><span class="sxs-lookup"><span data-stu-id="420d3-198">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="420d3-199">Hizmeti Kaldır</span><span class="sxs-lookup"><span data-stu-id="420d3-199">Remove a service</span></span>

<span data-ttu-id="420d3-200">Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki PowerShell 6 komutuyla bir hizmeti kaldırın:</span><span class="sxs-lookup"><span data-stu-id="420d3-200">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="420d3-201">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="420d3-201">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="420d3-202">Internet 'ten veya şirket ağından gelen isteklerle etkileşime geçen ve bir ara sunucu veya yük dengeleyicinin arkasındaki Hizmetler ek yapılandırma gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-202">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="420d3-203">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="420d3-203">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="420d3-204">Uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="420d3-204">Configure endpoints</span></span>

<span data-ttu-id="420d3-205">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="420d3-205">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="420d3-206">Ortam değişkenini ayarlayarak URL 'YI ve bağlantı noktasını yapılandırın `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="420d3-206">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="420d3-207">Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="420d3-207">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="420d3-208">Yukarıdaki kılavuz, HTTPS uç noktaları için desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="420d3-208">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="420d3-209">Örneğin, bir Windows hizmeti ile kimlik doğrulaması kullanıldığında HTTPS için uygulamayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="420d3-209">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="420d3-210">Hizmet uç noktasının güvenliğini sağlamak için ASP.NET Core HTTPS geliştirme sertifikası kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="420d3-210">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="420d3-211">Geçerli dizin ve içerik kökü</span><span class="sxs-lookup"><span data-stu-id="420d3-211">Current directory and content root</span></span>

<span data-ttu-id="420d3-212">Windows hizmeti için çağırarak döndürülen geçerli çalışma dizini <xref:System.IO.Directory.GetCurrentDirectory*> *C: \\ Windows \\ System32* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="420d3-212">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="420d3-213">*System32* klasörü, bir hizmetin dosyalarını (örneğin, ayarlar dosyaları) depolamak için uygun bir konum değildir.</span><span class="sxs-lookup"><span data-stu-id="420d3-213">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="420d3-214">Bir hizmetin varlık ve ayar dosyalarını sürdürmek ve erişmek için aşağıdaki yaklaşımlardan birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-214">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="420d3-215">ContentRootPath veya ContentRootFileProvider kullanın</span><span class="sxs-lookup"><span data-stu-id="420d3-215">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="420d3-216">[Ihostenvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) kullanın veya <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> bir uygulamanın kaynaklarını bulun.</span><span class="sxs-lookup"><span data-stu-id="420d3-216">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="420d3-217">Uygulama bir hizmet olarak çalıştırıldığında, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory)olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="420d3-217">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="420d3-218">Uygulamanın varsayılan ayar dosyaları, *appSettings. JSON* ve *appSettings. { '. JSON ortamı*, [konak oluşturma sırasında Createdefaultbuilder](xref:fundamentals/host/generic-host#set-up-a-host)' ı çağırarak uygulamanın içerik kökünden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="420d3-218">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="420d3-219">İçindeki geliştirici kodu tarafından yüklenen diğer ayar dosyaları için <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> , çağrısına gerek yoktur <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> .</span><span class="sxs-lookup"><span data-stu-id="420d3-219">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="420d3-220">Aşağıdaki örnekte, *custom_settings. JSON* dosyası uygulamanın içerik kökünde bulunur ve açıkça bir temel yolu ayarlamadan yüklenir:</span><span class="sxs-lookup"><span data-stu-id="420d3-220">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="420d3-221">Bir <xref:System.IO.Directory.GetCurrentDirectory*> Windows hizmeti uygulaması geçerli dizini olarak *C: \\ Windows \\ System32* klasörünü döndürdüğünden kaynak yolunu almak için kullanmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-221">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="420d3-222">Hizmetin dosyalarını diskte uygun bir konumda depolayın</span><span class="sxs-lookup"><span data-stu-id="420d3-222">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="420d3-223"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Dosyaları içeren klasöre kullanırken ile mutlak bir yol belirtin <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="420d3-223">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="420d3-224">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="420d3-224">Troubleshoot</span></span>

<span data-ttu-id="420d3-225">Bir Windows hizmet uygulaması sorunlarını gidermek için bkz <xref:test/troubleshoot> ..</span><span class="sxs-lookup"><span data-stu-id="420d3-225">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="420d3-226">Sık karşılaşılan hatalar</span><span class="sxs-lookup"><span data-stu-id="420d3-226">Common errors</span></span>

* <span data-ttu-id="420d3-227">PowerShell 'in eski veya yayın öncesi sürümü kullanımda.</span><span class="sxs-lookup"><span data-stu-id="420d3-227">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="420d3-228">Kayıtlı hizmet, [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutundan uygulamanın **yayımlanmış** çıktısını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="420d3-228">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="420d3-229">[DotNet Build](/dotnet/core/tools/dotnet-build) komutunun çıkışı, uygulama dağıtımı için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="420d3-229">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="420d3-230">Yayımlanan varlıklar, dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:</span><span class="sxs-lookup"><span data-stu-id="420d3-230">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="420d3-231">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="420d3-231">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="420d3-232">*bin/Release/{Target Framework}/{RUNTIME tanımlayıcısı}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="420d3-232">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="420d3-233">Hizmet çalışır durumda değil.</span><span class="sxs-lookup"><span data-stu-id="420d3-233">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="420d3-234">Uygulamanın kullandığı kaynakların yolları (örneğin, sertifikalar) yanlış.</span><span class="sxs-lookup"><span data-stu-id="420d3-234">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="420d3-235">Bir Windows hizmetinin temel yolu *c: \\ Windows \\ System32*' dir.</span><span class="sxs-lookup"><span data-stu-id="420d3-235">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="420d3-236">Kullanıcı, hizmet hakları *olarak oturum* açma hakkına sahip değil.</span><span class="sxs-lookup"><span data-stu-id="420d3-236">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="420d3-237">PowerShell komutu çalıştırılırken kullanıcının parolasının kullanım dışı veya yanlış şekilde geçirilmesi `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="420d3-237">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="420d3-238">Uygulama ASP.NET Core kimlik doğrulaması gerektiriyor, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="420d3-238">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="420d3-239">İstek URL 'SI bağlantı noktası yanlış veya uygulamada doğru şekilde yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="420d3-239">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="420d3-240">Sistem ve uygulama olay günlükleri</span><span class="sxs-lookup"><span data-stu-id="420d3-240">System and Application Event Logs</span></span>

<span data-ttu-id="420d3-241">Sistem ve uygulama olay günlüklerine erişin:</span><span class="sxs-lookup"><span data-stu-id="420d3-241">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="420d3-242">Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-242">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="420d3-243">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="420d3-243">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="420d3-244">Sistem olay günlüğünü açmak için **sistem** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-244">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="420d3-245">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-245">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="420d3-246">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="420d3-246">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="420d3-247">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="420d3-247">Run the app at a command prompt</span></span>

<span data-ttu-id="420d3-248">Birçok başlatma hatası olay günlüklerinde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="420d3-248">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="420d3-249">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="420d3-249">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="420d3-250">Uygulamadan ek ayrıntı günlüğe kaydetmek için, [günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [geliştirme ortamında](xref:fundamentals/environments)uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="420d3-250">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="420d3-251">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="420d3-251">Clear package caches</span></span>

<span data-ttu-id="420d3-252">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-252">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="420d3-253">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-253">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="420d3-254">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="420d3-254">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="420d3-255">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="420d3-255">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="420d3-256">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-256">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="420d3-257">Paket önbelleklerini Temizleme, [NuGet. exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komut yürütülebilir `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="420d3-257">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="420d3-258">*NuGet. exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="420d3-258">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="420d3-259">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-259">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="420d3-260">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="420d3-260">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="420d3-261">Yavaş veya askıda olan uygulama</span><span class="sxs-lookup"><span data-stu-id="420d3-261">Slow or hanging app</span></span>

<span data-ttu-id="420d3-262">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-262">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="420d3-263">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="420d3-263">App crashes or encounters an exception</span></span>

<span data-ttu-id="420d3-264">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="420d3-264">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="420d3-265">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="420d3-265">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="420d3-266">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) uygulama yürütülebilir adıyla çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="420d3-266">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="420d3-267">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="420d3-267">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="420d3-268">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="420d3-268">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="420d3-269">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-269">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="420d3-270">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="420d3-270">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="420d3-271">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="420d3-271">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="420d3-272">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="420d3-272">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="420d3-273">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="420d3-273">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="420d3-274">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="420d3-274">Analyze the dump</span></span>

<span data-ttu-id="420d3-275">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-275">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="420d3-276">Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="420d3-276">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="420d3-277">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="420d3-277">Additional resources</span></span>

* <span data-ttu-id="420d3-278">[Kestrel uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (https yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="420d3-278">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="420d3-279">Bir ASP.NET Core uygulaması, IIS kullanmadan Windows [hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak Windows üzerinde barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-279">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="420d3-280">Windows hizmeti olarak barındırıldığı zaman, uygulama otomatik olarak sunucu yeniden başlatıldıktan sonra başlatılır.</span><span class="sxs-lookup"><span data-stu-id="420d3-280">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="420d3-281">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="420d3-281">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="420d3-282">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="420d3-282">Prerequisites</span></span>

* [<span data-ttu-id="420d3-283">ASP.NET Core SDK 2,1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="420d3-283">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="420d3-284">PowerShell 6,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="420d3-284">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="420d3-285">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="420d3-285">App configuration</span></span>

<span data-ttu-id="420d3-286">Uygulama [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) ve [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)için paket başvuruları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="420d3-286">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="420d3-287">Bir hizmetin dışında çalışırken test ve hata ayıklamak için, uygulamanın bir hizmet olarak mı yoksa bir konsol uygulaması mi çalıştığını belirleme kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-287">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="420d3-288">Hata ayıklayıcının ekli olduğunu veya bir anahtarın mevcut olup olmadığını denetleyin `--console` .</span><span class="sxs-lookup"><span data-stu-id="420d3-288">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="420d3-289">Her iki koşul de geçerliyse (uygulama bir hizmet olarak çalıştırılmadıysa), çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="420d3-289">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="420d3-290">Koşullar yanlışsa (uygulama bir hizmet olarak çalıştırılır):</span><span class="sxs-lookup"><span data-stu-id="420d3-290">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="420d3-291"><xref:System.IO.Directory.SetCurrentDirectory*>Uygulamanın yayımlanmış konumunun yolunu çağırın ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-291">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="420d3-292"><xref:System.IO.Directory.GetCurrentDirectory*>Bir Windows hizmeti uygulaması çağrıldığında *C: \\ Windows \\ System32* klasörünü döndürdüğünden yolu almak için çağırmayın <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="420d3-292">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="420d3-293">Daha fazla bilgi için [geçerli dizin ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="420d3-293">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="420d3-294">Bu adım, uygulamanın ' de yapılandırılmadan önce gerçekleştirilir `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="420d3-294">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="420d3-295"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>Uygulamayı bir hizmet olarak çalıştırmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="420d3-295">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="420d3-296">[Komut satırı yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#command-line-configuration-provider) komut satırı bağımsız değişkenleri için ad-değer çiftleri gerektirdiğinden, `--console` bağımsız değişkenleri almadan önce anahtar bağımsız değişkenlerden kaldırılır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="420d3-296">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="420d3-297">Windows olay günlüğü 'ne yazmak için EventLog sağlayıcısını öğesine ekleyin <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="420d3-297">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="420d3-298">Günlük kaydı düzeyini `Logging:LogLevel:Default` appSettings içindeki anahtarla ayarlayın *. Production. JSON* dosyası.</span><span class="sxs-lookup"><span data-stu-id="420d3-298">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="420d3-299">Örnek uygulamadan aşağıdaki örnekte, `RunAsCustomService` <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> uygulama içindeki ömür olaylarını işlemek için yerine çağrılır.</span><span class="sxs-lookup"><span data-stu-id="420d3-299">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="420d3-300">Daha fazla bilgi için [olayları başlatma ve durdurma olaylarını](#handle-starting-and-stopping-events) inceleyin bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="420d3-300">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="420d3-301">Dağıtım türü</span><span class="sxs-lookup"><span data-stu-id="420d3-301">Deployment type</span></span>

<span data-ttu-id="420d3-302">Dağıtım senaryoları hakkında bilgi ve öneriler için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="420d3-302">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="420d3-303">SDK</span><span class="sxs-lookup"><span data-stu-id="420d3-303">SDK</span></span>

<span data-ttu-id="420d3-304">Sayfaları veya MVC çerçevelerini kullanan bir Web uygulaması tabanlı hizmet için Razor , proje dosyasında Web SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="420d3-304">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="420d3-305">Hizmet yalnızca arka plan görevlerini (örneğin, [barındırılan hizmetler](xref:fundamentals/host/hosted-services)) yürütülüyorsa, proje dosyasında çalışan SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="420d3-305">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="420d3-306">Çerçeveye bağımlı dağıtım (FDD)</span><span class="sxs-lookup"><span data-stu-id="420d3-306">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="420d3-307">Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core 'un paylaşılan sistem genelindeki bir sürümünün varlığına dayanır.</span><span class="sxs-lookup"><span data-stu-id="420d3-307">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="420d3-308">Bu makaledeki kılavuzdan sonra FDD senaryosu benimsendiği zaman SDK, *çerçeveye bağlı yürütülebilir dosya*olarak adlandırılan yürütülebilir bir dosya (*. exe*) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="420d3-308">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="420d3-309">Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) hedef Framework 'ü içerir.</span><span class="sxs-lookup"><span data-stu-id="420d3-309">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="420d3-310">Aşağıdaki örnekte, RID olarak ayarlanır `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="420d3-310">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="420d3-311">`<SelfContained>`Özelliği olarak ayarlanır `false` .</span><span class="sxs-lookup"><span data-stu-id="420d3-311">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="420d3-312">Bu özellikler SDK 'nın Windows için bir yürütülebilir (*. exe*) dosya ve paylaşılan .NET Core çerçevesine bağlı bir uygulama oluşturmasını ister.</span><span class="sxs-lookup"><span data-stu-id="420d3-312">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="420d3-313">Bir ASP.NET Core uygulaması yayımlandığında normalde üretilen bir *Web. config* dosyası, Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="420d3-313">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="420d3-314">*Web. config* dosyasının oluşturulmasını devre dışı bırakmak için, `<IsTransformWebConfigDisabled>` özelliğini öğesine ekleyin `true` .</span><span class="sxs-lookup"><span data-stu-id="420d3-314">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="420d3-315">Kendi içinde dağıtım (SCD)</span><span class="sxs-lookup"><span data-stu-id="420d3-315">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="420d3-316">Kendinden bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir Framework varlığına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="420d3-316">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="420d3-317">Çalışma zamanı ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="420d3-317">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="420d3-318">Hedef Framework 'ü içeren ' de bir Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) bulunur `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="420d3-318">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="420d3-319">Birden çok RID için yayımlamak için:</span><span class="sxs-lookup"><span data-stu-id="420d3-319">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="420d3-320">RID 'leri, noktalı virgülle ayrılmış bir liste olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="420d3-320">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="420d3-321">Özellik adını [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural) kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-321">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="420d3-322">Daha fazla bilgi için bkz. [.NET Core RID kataloğu](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="420d3-322">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="420d3-323">Bir `<SelfContained>` özellik şu şekilde ayarlanır `true` :</span><span class="sxs-lookup"><span data-stu-id="420d3-323">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="420d3-324">Hizmet Kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="420d3-324">Service user account</span></span>

<span data-ttu-id="420d3-325">Bir hizmet için Kullanıcı hesabı oluşturmak için, bir yönetim PowerShell 6 komut kabuğundan [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ini kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-325">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="420d3-326">Windows 10 Ekim 2018 Güncelleştirmesi (sürüm 1809/Build 10.0.17763) veya sonraki sürümler:</span><span class="sxs-lookup"><span data-stu-id="420d3-326">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="420d3-327">Windows 10 Ekim 2018 (sürüm 1809/Build 10.0.17763) sürümünden önceki Windows IŞLETIM sistemlerinde:</span><span class="sxs-lookup"><span data-stu-id="420d3-327">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="420d3-328">İstendiğinde [güçlü bir parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.</span><span class="sxs-lookup"><span data-stu-id="420d3-328">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="420d3-329">`-AccountExpires`Parametresi [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ine bir süre sonu ile sağlanmamışsa <xref:System.DateTime> hesabın süresi dolmaz.</span><span class="sxs-lookup"><span data-stu-id="420d3-329">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="420d3-330">Daha fazla bilgi için bkz. [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [hizmet Kullanıcı hesapları](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="420d3-330">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="420d3-331">Active Directory kullanırken kullanıcıları yönetmeye yönelik alternatif bir yaklaşım, yönetilen hizmet hesaplarını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="420d3-331">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="420d3-332">Daha fazla bilgi için bkz. [Grup yönetilen hizmet hesaplarına genel bakış](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="420d3-332">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="420d3-333">Hizmet hakları olarak oturum açma</span><span class="sxs-lookup"><span data-stu-id="420d3-333">Log on as a service rights</span></span>

<span data-ttu-id="420d3-334">Hizmet Kullanıcı hesabı için *hizmet hakları olarak oturum* açma oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="420d3-334">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="420d3-335">Yerel Güvenlik Ilkesi düzenleyicisini, *secpol. msc*' i çalıştırarak açın.</span><span class="sxs-lookup"><span data-stu-id="420d3-335">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="420d3-336">**Yerel ilkeler** düğümünü genişletin ve **Kullanıcı hakları ataması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-336">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="420d3-337">**Hizmet olarak oturum** açma ilkesi açın.</span><span class="sxs-lookup"><span data-stu-id="420d3-337">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="420d3-338">**Kullanıcı veya Grup Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-338">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="420d3-339">Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (Kullanıcı hesabı) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="420d3-339">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="420d3-340">Kullanıcı hesabını ( `{DOMAIN OR COMPUTER NAME\USER}` ) nesne adı alanına yazın ve kullanıcıyı ilkeye eklemek Için **Tamam** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-340">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="420d3-341">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-341">Select **Advanced**.</span></span> <span data-ttu-id="420d3-342">**Şimdi bul**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-342">Select **Find Now**.</span></span> <span data-ttu-id="420d3-343">Listeden Kullanıcı hesabını seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-343">Select the user account from the list.</span></span> <span data-ttu-id="420d3-344">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-344">Select **OK**.</span></span> <span data-ttu-id="420d3-345">Kullanıcıyı ilkeye eklemek için yeniden **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-345">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="420d3-346">Değişiklikleri kabul etmek için **Tamam ' ı** veya **Uygula** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-346">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="420d3-347">Windows hizmetini oluşturma ve yönetme</span><span class="sxs-lookup"><span data-stu-id="420d3-347">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="420d3-348">Hizmet oluşturma</span><span class="sxs-lookup"><span data-stu-id="420d3-348">Create a service</span></span>

<span data-ttu-id="420d3-349">Bir hizmeti kaydetmek için PowerShell komutlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-349">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="420d3-350">Bir yönetim PowerShell 6 komut kabuğundan aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="420d3-350">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="420d3-351">`{EXE PATH}`: Konaktaki uygulamanın klasörünün yolu (örneğin, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-351">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="420d3-352">Uygulamanın yürütülebilir dosyasını yola eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-352">Don't include the app's executable in the path.</span></span> <span data-ttu-id="420d3-353">Sondaki eğik çizgi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="420d3-353">A trailing slash isn't required.</span></span>
* <span data-ttu-id="420d3-354">`{DOMAIN OR COMPUTER NAME\USER}`: Hizmet Kullanıcı hesabı (örneğin, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-354">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="420d3-355">`{SERVICE NAME}`: Hizmet adı (örneğin, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-355">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="420d3-356">`{EXE FILE PATH}`: Uygulamanın yürütülebilir yolu (örneğin, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-356">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="420d3-357">Yürütülebilir dosyanın dosya adını uzantısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-357">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="420d3-358">`{DESCRIPTION}`: Hizmet açıklaması (örneğin, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-358">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="420d3-359">`{DISPLAY NAME}`: Hizmet görünen adı (örneğin, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-359">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="420d3-360">Bir hizmet başlatın</span><span class="sxs-lookup"><span data-stu-id="420d3-360">Start a service</span></span>

<span data-ttu-id="420d3-361">Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:</span><span class="sxs-lookup"><span data-stu-id="420d3-361">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="420d3-362">Komutun başlatılması birkaç saniye sürer.</span><span class="sxs-lookup"><span data-stu-id="420d3-362">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="420d3-363">Hizmetin durumunu belirleme</span><span class="sxs-lookup"><span data-stu-id="420d3-363">Determine a service's status</span></span>

<span data-ttu-id="420d3-364">Bir hizmetin durumunu denetlemek için aşağıdaki PowerShell 6 komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="420d3-364">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="420d3-365">Durum, aşağıdaki değerlerden biri olarak bildirilir:</span><span class="sxs-lookup"><span data-stu-id="420d3-365">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="420d3-366">Bir hizmet durdurun</span><span class="sxs-lookup"><span data-stu-id="420d3-366">Stop a service</span></span>

<span data-ttu-id="420d3-367">Aşağıdaki PowerShell 6 komutuyla bir hizmeti durdurun:</span><span class="sxs-lookup"><span data-stu-id="420d3-367">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="420d3-368">Hizmeti Kaldır</span><span class="sxs-lookup"><span data-stu-id="420d3-368">Remove a service</span></span>

<span data-ttu-id="420d3-369">Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki PowerShell 6 komutuyla bir hizmeti kaldırın:</span><span class="sxs-lookup"><span data-stu-id="420d3-369">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="420d3-370">Olayları başlatma ve durdurma olaylarını işleme</span><span class="sxs-lookup"><span data-stu-id="420d3-370">Handle starting and stopping events</span></span>

<span data-ttu-id="420d3-371"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> Ve olaylarını işlemek için <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="420d3-371">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="420d3-372"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> `OnStarting` , `OnStarted` Ve yöntemleriyle türetilen bir sınıf oluşturun `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="420d3-372">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="420d3-373">' A geçişi için bir genişletme yöntemi oluşturun <xref:Microsoft.AspNetCore.Hosting.IWebHost> `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="420d3-373">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="420d3-374">İçinde `Program.Main` , `RunAsCustomService` yerine genişletme yöntemini çağırın <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="420d3-374">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="420d3-375">Konumunu görmek için <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> `Program.Main` , [dağıtım türü](#deployment-type) bölümünde gösterilen kod örneğine bakın.</span><span class="sxs-lookup"><span data-stu-id="420d3-375">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="420d3-376">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="420d3-376">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="420d3-377">Internet 'ten veya şirket ağından gelen isteklerle etkileşime geçen ve bir ara sunucu veya yük dengeleyicinin arkasındaki Hizmetler ek yapılandırma gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-377">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="420d3-378">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="420d3-378">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="420d3-379">Uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="420d3-379">Configure endpoints</span></span>

<span data-ttu-id="420d3-380">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="420d3-380">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="420d3-381">Ortam değişkenini ayarlayarak URL 'YI ve bağlantı noktasını yapılandırın `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="420d3-381">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="420d3-382">Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="420d3-382">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="420d3-383">Yukarıdaki kılavuz, HTTPS uç noktaları için desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="420d3-383">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="420d3-384">Örneğin, bir Windows hizmeti ile kimlik doğrulaması kullanıldığında HTTPS için uygulamayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="420d3-384">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="420d3-385">Hizmet uç noktasının güvenliğini sağlamak için ASP.NET Core HTTPS geliştirme sertifikası kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="420d3-385">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="420d3-386">Geçerli dizin ve içerik kökü</span><span class="sxs-lookup"><span data-stu-id="420d3-386">Current directory and content root</span></span>

<span data-ttu-id="420d3-387">Windows hizmeti için çağırarak döndürülen geçerli çalışma dizini <xref:System.IO.Directory.GetCurrentDirectory*> *C: \\ Windows \\ System32* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="420d3-387">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="420d3-388">*System32* klasörü, bir hizmetin dosyalarını (örneğin, ayarlar dosyaları) depolamak için uygun bir konum değildir.</span><span class="sxs-lookup"><span data-stu-id="420d3-388">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="420d3-389">Bir hizmetin varlık ve ayar dosyalarını sürdürmek ve erişmek için aşağıdaki yaklaşımlardan birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-389">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="420d3-390">Uygulamanın klasörü için içerik kök yolunu ayarla</span><span class="sxs-lookup"><span data-stu-id="420d3-390">Set the content root path to the app's folder</span></span>

<span data-ttu-id="420d3-391">, <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> `binPath` Bir hizmet oluşturulduğunda bağımsız değişkene aynı yol olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="420d3-391">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="420d3-392">`GetCurrentDirectory`Ayarlar dosyalarına yollar oluşturmak için çağırmak yerine, <xref:System.IO.Directory.SetCurrentDirectory*> uygulamanın [içerik kökünün](xref:fundamentals/index#content-root)yolunu çağırın.</span><span class="sxs-lookup"><span data-stu-id="420d3-392">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="420d3-393">İçinde `Program.Main` , hizmetin yürütülebilir dosyasının yolunu belirleyin ve uygulamanın içerik kökünü oluşturmak için yolu kullanın:</span><span class="sxs-lookup"><span data-stu-id="420d3-393">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="420d3-394">Hizmetin dosyalarını diskte uygun bir konumda depolayın</span><span class="sxs-lookup"><span data-stu-id="420d3-394">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="420d3-395"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Dosyaları içeren klasöre kullanırken ile mutlak bir yol belirtin <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="420d3-395">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="420d3-396">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="420d3-396">Troubleshoot</span></span>

<span data-ttu-id="420d3-397">Bir Windows hizmet uygulaması sorunlarını gidermek için bkz <xref:test/troubleshoot> ..</span><span class="sxs-lookup"><span data-stu-id="420d3-397">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="420d3-398">Sık karşılaşılan hatalar</span><span class="sxs-lookup"><span data-stu-id="420d3-398">Common errors</span></span>

* <span data-ttu-id="420d3-399">PowerShell 'in eski veya yayın öncesi sürümü kullanımda.</span><span class="sxs-lookup"><span data-stu-id="420d3-399">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="420d3-400">Kayıtlı hizmet, [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutundan uygulamanın **yayımlanmış** çıktısını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="420d3-400">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="420d3-401">[DotNet Build](/dotnet/core/tools/dotnet-build) komutunun çıkışı, uygulama dağıtımı için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="420d3-401">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="420d3-402">Yayımlanan varlıklar, dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:</span><span class="sxs-lookup"><span data-stu-id="420d3-402">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="420d3-403">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="420d3-403">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="420d3-404">*bin/Release/{Target Framework}/{RUNTIME tanımlayıcısı}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="420d3-404">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="420d3-405">Hizmet çalışır durumda değil.</span><span class="sxs-lookup"><span data-stu-id="420d3-405">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="420d3-406">Uygulamanın kullandığı kaynakların yolları (örneğin, sertifikalar) yanlış.</span><span class="sxs-lookup"><span data-stu-id="420d3-406">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="420d3-407">Bir Windows hizmetinin temel yolu *c: \\ Windows \\ System32*' dir.</span><span class="sxs-lookup"><span data-stu-id="420d3-407">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="420d3-408">Kullanıcı, hizmet hakları *olarak oturum* açma hakkına sahip değil.</span><span class="sxs-lookup"><span data-stu-id="420d3-408">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="420d3-409">PowerShell komutu çalıştırılırken kullanıcının parolasının kullanım dışı veya yanlış şekilde geçirilmesi `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="420d3-409">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="420d3-410">Uygulama ASP.NET Core kimlik doğrulaması gerektiriyor, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="420d3-410">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="420d3-411">İstek URL 'SI bağlantı noktası yanlış veya uygulamada doğru şekilde yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="420d3-411">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="420d3-412">Sistem ve uygulama olay günlükleri</span><span class="sxs-lookup"><span data-stu-id="420d3-412">System and Application Event Logs</span></span>

<span data-ttu-id="420d3-413">Sistem ve uygulama olay günlüklerine erişin:</span><span class="sxs-lookup"><span data-stu-id="420d3-413">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="420d3-414">Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-414">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="420d3-415">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="420d3-415">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="420d3-416">Sistem olay günlüğünü açmak için **sistem** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-416">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="420d3-417">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-417">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="420d3-418">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="420d3-418">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="420d3-419">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="420d3-419">Run the app at a command prompt</span></span>

<span data-ttu-id="420d3-420">Birçok başlatma hatası olay günlüklerinde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="420d3-420">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="420d3-421">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="420d3-421">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="420d3-422">Uygulamadan ek ayrıntı günlüğe kaydetmek için, [günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [geliştirme ortamında](xref:fundamentals/environments)uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="420d3-422">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="420d3-423">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="420d3-423">Clear package caches</span></span>

<span data-ttu-id="420d3-424">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-424">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="420d3-425">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-425">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="420d3-426">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="420d3-426">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="420d3-427">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="420d3-427">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="420d3-428">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-428">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="420d3-429">Paket önbelleklerini Temizleme, [NuGet. exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komut yürütülebilir `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="420d3-429">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="420d3-430">*NuGet. exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="420d3-430">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="420d3-431">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-431">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="420d3-432">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="420d3-432">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="420d3-433">Yavaş veya askıda olan uygulama</span><span class="sxs-lookup"><span data-stu-id="420d3-433">Slow or hanging app</span></span>

<span data-ttu-id="420d3-434">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-434">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="420d3-435">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="420d3-435">App crashes or encounters an exception</span></span>

<span data-ttu-id="420d3-436">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="420d3-436">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="420d3-437">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="420d3-437">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="420d3-438">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) uygulama yürütülebilir adıyla çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="420d3-438">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="420d3-439">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="420d3-439">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="420d3-440">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="420d3-440">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="420d3-441">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-441">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="420d3-442">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="420d3-442">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="420d3-443">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="420d3-443">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="420d3-444">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="420d3-444">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="420d3-445">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="420d3-445">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="420d3-446">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="420d3-446">Analyze the dump</span></span>

<span data-ttu-id="420d3-447">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-447">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="420d3-448">Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="420d3-448">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="420d3-449">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="420d3-449">Additional resources</span></span>

* <span data-ttu-id="420d3-450">[Kestrel uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (https yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="420d3-450">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="420d3-451">Bir ASP.NET Core uygulaması, IIS kullanmadan Windows [hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak Windows üzerinde barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-451">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="420d3-452">Windows hizmeti olarak barındırıldığı zaman, uygulama otomatik olarak sunucu yeniden başlatıldıktan sonra başlatılır.</span><span class="sxs-lookup"><span data-stu-id="420d3-452">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="420d3-453">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="420d3-453">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="420d3-454">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="420d3-454">Prerequisites</span></span>

* [<span data-ttu-id="420d3-455">ASP.NET Core SDK 2,1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="420d3-455">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="420d3-456">PowerShell 6,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="420d3-456">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="420d3-457">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="420d3-457">App configuration</span></span>

<span data-ttu-id="420d3-458">Uygulama [Microsoft. AspNetCore. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) ve [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)için paket başvuruları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="420d3-458">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="420d3-459">Bir hizmetin dışında çalışırken test ve hata ayıklamak için, uygulamanın bir hizmet olarak mı yoksa bir konsol uygulaması mi çalıştığını belirleme kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-459">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="420d3-460">Hata ayıklayıcının ekli olduğunu veya bir anahtarın mevcut olup olmadığını denetleyin `--console` .</span><span class="sxs-lookup"><span data-stu-id="420d3-460">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="420d3-461">Her iki koşul de geçerliyse (uygulama bir hizmet olarak çalıştırılmadıysa), çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> .</span><span class="sxs-lookup"><span data-stu-id="420d3-461">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="420d3-462">Koşullar yanlışsa (uygulama bir hizmet olarak çalıştırılır):</span><span class="sxs-lookup"><span data-stu-id="420d3-462">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="420d3-463"><xref:System.IO.Directory.SetCurrentDirectory*>Uygulamanın yayımlanmış konumunun yolunu çağırın ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-463">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="420d3-464"><xref:System.IO.Directory.GetCurrentDirectory*>Bir Windows hizmeti uygulaması çağrıldığında *C: \\ Windows \\ System32* klasörünü döndürdüğünden yolu almak için çağırmayın <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="420d3-464">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="420d3-465">Daha fazla bilgi için [geçerli dizin ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="420d3-465">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="420d3-466">Bu adım, uygulamanın ' de yapılandırılmadan önce gerçekleştirilir `CreateWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="420d3-466">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="420d3-467"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>Uygulamayı bir hizmet olarak çalıştırmak için çağırın.</span><span class="sxs-lookup"><span data-stu-id="420d3-467">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="420d3-468">[Komut satırı yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#command-line-configuration-provider) komut satırı bağımsız değişkenleri için ad-değer çiftleri gerektirdiğinden, `--console` bağımsız değişkenleri almadan önce anahtar bağımsız değişkenlerden kaldırılır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="420d3-468">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="420d3-469">Windows olay günlüğü 'ne yazmak için EventLog sağlayıcısını öğesine ekleyin <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> .</span><span class="sxs-lookup"><span data-stu-id="420d3-469">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="420d3-470">Günlük kaydı düzeyini `Logging:LogLevel:Default` appSettings içindeki anahtarla ayarlayın *. Production. JSON* dosyası.</span><span class="sxs-lookup"><span data-stu-id="420d3-470">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="420d3-471">Örnek uygulamadan aşağıdaki örnekte, `RunAsCustomService` <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> uygulama içindeki ömür olaylarını işlemek için yerine çağrılır.</span><span class="sxs-lookup"><span data-stu-id="420d3-471">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="420d3-472">Daha fazla bilgi için [olayları başlatma ve durdurma olaylarını](#handle-starting-and-stopping-events) inceleyin bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="420d3-472">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="420d3-473">Dağıtım türü</span><span class="sxs-lookup"><span data-stu-id="420d3-473">Deployment type</span></span>

<span data-ttu-id="420d3-474">Dağıtım senaryoları hakkında bilgi ve öneriler için bkz. [.NET Core uygulama dağıtımı](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="420d3-474">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="420d3-475">SDK</span><span class="sxs-lookup"><span data-stu-id="420d3-475">SDK</span></span>

<span data-ttu-id="420d3-476">Sayfaları veya MVC çerçevelerini kullanan bir Web uygulaması tabanlı hizmet için Razor , proje dosyasında Web SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="420d3-476">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="420d3-477">Hizmet yalnızca arka plan görevlerini (örneğin, [barındırılan hizmetler](xref:fundamentals/host/hosted-services)) yürütülüyorsa, proje dosyasında çalışan SDK 'sını belirtin:</span><span class="sxs-lookup"><span data-stu-id="420d3-477">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="420d3-478">Çerçeveye bağımlı dağıtım (FDD)</span><span class="sxs-lookup"><span data-stu-id="420d3-478">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="420d3-479">Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core 'un paylaşılan sistem genelindeki bir sürümünün varlığına dayanır.</span><span class="sxs-lookup"><span data-stu-id="420d3-479">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="420d3-480">Bu makaledeki kılavuzdan sonra FDD senaryosu benimsendiği zaman SDK, *çerçeveye bağlı yürütülebilir dosya*olarak adlandırılan yürütülebilir bir dosya (*. exe*) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="420d3-480">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="420d3-481">Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) hedef Framework 'ü içerir.</span><span class="sxs-lookup"><span data-stu-id="420d3-481">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="420d3-482">Aşağıdaki örnekte, RID olarak ayarlanır `win7-x64` .</span><span class="sxs-lookup"><span data-stu-id="420d3-482">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="420d3-483">`<SelfContained>`Özelliği olarak ayarlanır `false` .</span><span class="sxs-lookup"><span data-stu-id="420d3-483">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="420d3-484">Bu özellikler SDK 'nın Windows için bir yürütülebilir (*. exe*) dosya ve paylaşılan .NET Core çerçevesine bağlı bir uygulama oluşturmasını ister.</span><span class="sxs-lookup"><span data-stu-id="420d3-484">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="420d3-485">`<UseAppHost>`Özelliği olarak ayarlanır `true` .</span><span class="sxs-lookup"><span data-stu-id="420d3-485">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="420d3-486">Bu özellik, bir FDD için bir etkinleştirme yolu (yürütülebilir, *. exe*) ile hizmeti sağlar.</span><span class="sxs-lookup"><span data-stu-id="420d3-486">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="420d3-487">Bir ASP.NET Core uygulaması yayımlandığında normalde üretilen bir *Web. config* dosyası, Windows Hizmetleri uygulaması için gereksizdir.</span><span class="sxs-lookup"><span data-stu-id="420d3-487">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="420d3-488">*Web. config* dosyasının oluşturulmasını devre dışı bırakmak için, `<IsTransformWebConfigDisabled>` özelliğini öğesine ekleyin `true` .</span><span class="sxs-lookup"><span data-stu-id="420d3-488">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="420d3-489">Kendi içinde dağıtım (SCD)</span><span class="sxs-lookup"><span data-stu-id="420d3-489">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="420d3-490">Kendinden bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir Framework varlığına güvenmez.</span><span class="sxs-lookup"><span data-stu-id="420d3-490">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="420d3-491">Çalışma zamanı ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="420d3-491">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="420d3-492">Hedef Framework 'ü içeren ' de bir Windows [çalışma zamanı tanımlayıcısı (RID)](/dotnet/core/rid-catalog) bulunur `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="420d3-492">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="420d3-493">Birden çok RID için yayımlamak için:</span><span class="sxs-lookup"><span data-stu-id="420d3-493">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="420d3-494">RID 'leri, noktalı virgülle ayrılmış bir liste olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="420d3-494">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="420d3-495">Özellik adını [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural) kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-495">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="420d3-496">Daha fazla bilgi için bkz. [.NET Core RID kataloğu](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="420d3-496">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="420d3-497">Bir `<SelfContained>` özellik şu şekilde ayarlanır `true` :</span><span class="sxs-lookup"><span data-stu-id="420d3-497">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="420d3-498">Hizmet Kullanıcı hesabı</span><span class="sxs-lookup"><span data-stu-id="420d3-498">Service user account</span></span>

<span data-ttu-id="420d3-499">Bir hizmet için Kullanıcı hesabı oluşturmak için, bir yönetim PowerShell 6 komut kabuğundan [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ini kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-499">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="420d3-500">Windows 10 Ekim 2018 Güncelleştirmesi (sürüm 1809/Build 10.0.17763) veya sonraki sürümler:</span><span class="sxs-lookup"><span data-stu-id="420d3-500">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="420d3-501">Windows 10 Ekim 2018 (sürüm 1809/Build 10.0.17763) sürümünden önceki Windows IŞLETIM sistemlerinde:</span><span class="sxs-lookup"><span data-stu-id="420d3-501">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="420d3-502">İstendiğinde [güçlü bir parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.</span><span class="sxs-lookup"><span data-stu-id="420d3-502">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="420d3-503">`-AccountExpires`Parametresi [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet 'ine bir süre sonu ile sağlanmamışsa <xref:System.DateTime> hesabın süresi dolmaz.</span><span class="sxs-lookup"><span data-stu-id="420d3-503">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="420d3-504">Daha fazla bilgi için bkz. [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [hizmet Kullanıcı hesapları](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="420d3-504">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="420d3-505">Active Directory kullanırken kullanıcıları yönetmeye yönelik alternatif bir yaklaşım, yönetilen hizmet hesaplarını kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="420d3-505">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="420d3-506">Daha fazla bilgi için bkz. [Grup yönetilen hizmet hesaplarına genel bakış](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="420d3-506">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="420d3-507">Hizmet hakları olarak oturum açma</span><span class="sxs-lookup"><span data-stu-id="420d3-507">Log on as a service rights</span></span>

<span data-ttu-id="420d3-508">Hizmet Kullanıcı hesabı için *hizmet hakları olarak oturum* açma oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="420d3-508">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="420d3-509">Yerel Güvenlik Ilkesi düzenleyicisini, *secpol. msc*' i çalıştırarak açın.</span><span class="sxs-lookup"><span data-stu-id="420d3-509">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="420d3-510">**Yerel ilkeler** düğümünü genişletin ve **Kullanıcı hakları ataması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-510">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="420d3-511">**Hizmet olarak oturum** açma ilkesi açın.</span><span class="sxs-lookup"><span data-stu-id="420d3-511">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="420d3-512">**Kullanıcı veya Grup Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-512">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="420d3-513">Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (Kullanıcı hesabı) sağlayın:</span><span class="sxs-lookup"><span data-stu-id="420d3-513">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="420d3-514">Kullanıcı hesabını ( `{DOMAIN OR COMPUTER NAME\USER}` ) nesne adı alanına yazın ve kullanıcıyı ilkeye eklemek Için **Tamam** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-514">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="420d3-515">**Gelişmiş**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-515">Select **Advanced**.</span></span> <span data-ttu-id="420d3-516">**Şimdi bul**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-516">Select **Find Now**.</span></span> <span data-ttu-id="420d3-517">Listeden Kullanıcı hesabını seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-517">Select the user account from the list.</span></span> <span data-ttu-id="420d3-518">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-518">Select **OK**.</span></span> <span data-ttu-id="420d3-519">Kullanıcıyı ilkeye eklemek için yeniden **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-519">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="420d3-520">Değişiklikleri kabul etmek için **Tamam ' ı** veya **Uygula** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-520">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="420d3-521">Windows hizmetini oluşturma ve yönetme</span><span class="sxs-lookup"><span data-stu-id="420d3-521">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="420d3-522">Hizmet oluşturma</span><span class="sxs-lookup"><span data-stu-id="420d3-522">Create a service</span></span>

<span data-ttu-id="420d3-523">Bir hizmeti kaydetmek için PowerShell komutlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-523">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="420d3-524">Bir yönetim PowerShell 6 komut kabuğundan aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="420d3-524">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="420d3-525">`{EXE PATH}`: Konaktaki uygulamanın klasörünün yolu (örneğin, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-525">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="420d3-526">Uygulamanın yürütülebilir dosyasını yola eklemeyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-526">Don't include the app's executable in the path.</span></span> <span data-ttu-id="420d3-527">Sondaki eğik çizgi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="420d3-527">A trailing slash isn't required.</span></span>
* <span data-ttu-id="420d3-528">`{DOMAIN OR COMPUTER NAME\USER}`: Hizmet Kullanıcı hesabı (örneğin, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-528">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="420d3-529">`{SERVICE NAME}`: Hizmet adı (örneğin, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-529">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="420d3-530">`{EXE FILE PATH}`: Uygulamanın yürütülebilir yolu (örneğin, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-530">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="420d3-531">Yürütülebilir dosyanın dosya adını uzantısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-531">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="420d3-532">`{DESCRIPTION}`: Hizmet açıklaması (örneğin, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-532">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="420d3-533">`{DISPLAY NAME}`: Hizmet görünen adı (örneğin, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="420d3-533">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="420d3-534">Bir hizmet başlatın</span><span class="sxs-lookup"><span data-stu-id="420d3-534">Start a service</span></span>

<span data-ttu-id="420d3-535">Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:</span><span class="sxs-lookup"><span data-stu-id="420d3-535">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="420d3-536">Komutun başlatılması birkaç saniye sürer.</span><span class="sxs-lookup"><span data-stu-id="420d3-536">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="420d3-537">Hizmetin durumunu belirleme</span><span class="sxs-lookup"><span data-stu-id="420d3-537">Determine a service's status</span></span>

<span data-ttu-id="420d3-538">Bir hizmetin durumunu denetlemek için aşağıdaki PowerShell 6 komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="420d3-538">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="420d3-539">Durum, aşağıdaki değerlerden biri olarak bildirilir:</span><span class="sxs-lookup"><span data-stu-id="420d3-539">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="420d3-540">Bir hizmet durdurun</span><span class="sxs-lookup"><span data-stu-id="420d3-540">Stop a service</span></span>

<span data-ttu-id="420d3-541">Aşağıdaki PowerShell 6 komutuyla bir hizmeti durdurun:</span><span class="sxs-lookup"><span data-stu-id="420d3-541">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="420d3-542">Hizmeti Kaldır</span><span class="sxs-lookup"><span data-stu-id="420d3-542">Remove a service</span></span>

<span data-ttu-id="420d3-543">Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki PowerShell 6 komutuyla bir hizmeti kaldırın:</span><span class="sxs-lookup"><span data-stu-id="420d3-543">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="420d3-544">Olayları başlatma ve durdurma olaylarını işleme</span><span class="sxs-lookup"><span data-stu-id="420d3-544">Handle starting and stopping events</span></span>

<span data-ttu-id="420d3-545"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> Ve olaylarını işlemek için <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :</span><span class="sxs-lookup"><span data-stu-id="420d3-545">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="420d3-546"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> `OnStarting` , `OnStarted` Ve yöntemleriyle türetilen bir sınıf oluşturun `OnStopping` :</span><span class="sxs-lookup"><span data-stu-id="420d3-546">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="420d3-547">' A geçişi için bir genişletme yöntemi oluşturun <xref:Microsoft.AspNetCore.Hosting.IWebHost> `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*> :</span><span class="sxs-lookup"><span data-stu-id="420d3-547">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="420d3-548">İçinde `Program.Main` , `RunAsCustomService` yerine genişletme yöntemini çağırın <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :</span><span class="sxs-lookup"><span data-stu-id="420d3-548">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="420d3-549">Konumunu görmek için <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> `Program.Main` , [dağıtım türü](#deployment-type) bölümünde gösterilen kod örneğine bakın.</span><span class="sxs-lookup"><span data-stu-id="420d3-549">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="420d3-550">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="420d3-550">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="420d3-551">Internet 'ten veya şirket ağından gelen isteklerle etkileşime geçen ve bir ara sunucu veya yük dengeleyicinin arkasındaki Hizmetler ek yapılandırma gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-551">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="420d3-552">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="420d3-552">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="420d3-553">Uç noktaları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="420d3-553">Configure endpoints</span></span>

<span data-ttu-id="420d3-554">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="420d3-554">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="420d3-555">Ortam değişkenini ayarlayarak URL 'YI ve bağlantı noktasını yapılandırın `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="420d3-555">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="420d3-556">Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:</span><span class="sxs-lookup"><span data-stu-id="420d3-556">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="420d3-557">Yukarıdaki kılavuz, HTTPS uç noktaları için desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="420d3-557">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="420d3-558">Örneğin, bir Windows hizmeti ile kimlik doğrulaması kullanıldığında HTTPS için uygulamayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="420d3-558">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="420d3-559">Hizmet uç noktasının güvenliğini sağlamak için ASP.NET Core HTTPS geliştirme sertifikası kullanılması desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="420d3-559">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="420d3-560">Geçerli dizin ve içerik kökü</span><span class="sxs-lookup"><span data-stu-id="420d3-560">Current directory and content root</span></span>

<span data-ttu-id="420d3-561">Windows hizmeti için çağırarak döndürülen geçerli çalışma dizini <xref:System.IO.Directory.GetCurrentDirectory*> *C: \\ Windows \\ System32* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="420d3-561">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="420d3-562">*System32* klasörü, bir hizmetin dosyalarını (örneğin, ayarlar dosyaları) depolamak için uygun bir konum değildir.</span><span class="sxs-lookup"><span data-stu-id="420d3-562">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="420d3-563">Bir hizmetin varlık ve ayar dosyalarını sürdürmek ve erişmek için aşağıdaki yaklaşımlardan birini kullanın.</span><span class="sxs-lookup"><span data-stu-id="420d3-563">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="420d3-564">Uygulamanın klasörü için içerik kök yolunu ayarla</span><span class="sxs-lookup"><span data-stu-id="420d3-564">Set the content root path to the app's folder</span></span>

<span data-ttu-id="420d3-565">, <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> `binPath` Bir hizmet oluşturulduğunda bağımsız değişkene aynı yol olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="420d3-565">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="420d3-566">`GetCurrentDirectory`Ayarlar dosyalarına yollar oluşturmak için çağırmak yerine, <xref:System.IO.Directory.SetCurrentDirectory*> uygulamanın [içerik kökünün](xref:fundamentals/index#content-root)yolunu çağırın.</span><span class="sxs-lookup"><span data-stu-id="420d3-566">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="420d3-567">İçinde `Program.Main` , hizmetin yürütülebilir dosyasının yolunu belirleyin ve uygulamanın içerik kökünü oluşturmak için yolu kullanın:</span><span class="sxs-lookup"><span data-stu-id="420d3-567">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="420d3-568">Hizmetin dosyalarını diskte uygun bir konumda depolayın</span><span class="sxs-lookup"><span data-stu-id="420d3-568">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="420d3-569"><xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>Dosyaları içeren klasöre kullanırken ile mutlak bir yol belirtin <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="420d3-569">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="420d3-570">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="420d3-570">Troubleshoot</span></span>

<span data-ttu-id="420d3-571">Bir Windows hizmet uygulaması sorunlarını gidermek için bkz <xref:test/troubleshoot> ..</span><span class="sxs-lookup"><span data-stu-id="420d3-571">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="420d3-572">Sık karşılaşılan hatalar</span><span class="sxs-lookup"><span data-stu-id="420d3-572">Common errors</span></span>

* <span data-ttu-id="420d3-573">PowerShell 'in eski veya yayın öncesi sürümü kullanımda.</span><span class="sxs-lookup"><span data-stu-id="420d3-573">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="420d3-574">Kayıtlı hizmet, [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutundan uygulamanın **yayımlanmış** çıktısını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="420d3-574">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="420d3-575">[DotNet Build](/dotnet/core/tools/dotnet-build) komutunun çıkışı, uygulama dağıtımı için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="420d3-575">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="420d3-576">Yayımlanan varlıklar, dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:</span><span class="sxs-lookup"><span data-stu-id="420d3-576">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="420d3-577">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="420d3-577">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="420d3-578">*bin/Release/{Target Framework}/{RUNTIME tanımlayıcısı}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="420d3-578">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="420d3-579">Hizmet çalışır durumda değil.</span><span class="sxs-lookup"><span data-stu-id="420d3-579">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="420d3-580">Uygulamanın kullandığı kaynakların yolları (örneğin, sertifikalar) yanlış.</span><span class="sxs-lookup"><span data-stu-id="420d3-580">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="420d3-581">Bir Windows hizmetinin temel yolu *c: \\ Windows \\ System32*' dir.</span><span class="sxs-lookup"><span data-stu-id="420d3-581">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="420d3-582">Kullanıcı, hizmet hakları *olarak oturum* açma hakkına sahip değil.</span><span class="sxs-lookup"><span data-stu-id="420d3-582">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="420d3-583">PowerShell komutu çalıştırılırken kullanıcının parolasının kullanım dışı veya yanlış şekilde geçirilmesi `New-Service` .</span><span class="sxs-lookup"><span data-stu-id="420d3-583">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="420d3-584">Uygulama ASP.NET Core kimlik doğrulaması gerektiriyor, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="420d3-584">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="420d3-585">İstek URL 'SI bağlantı noktası yanlış veya uygulamada doğru şekilde yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="420d3-585">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="420d3-586">Sistem ve uygulama olay günlükleri</span><span class="sxs-lookup"><span data-stu-id="420d3-586">System and Application Event Logs</span></span>

<span data-ttu-id="420d3-587">Sistem ve uygulama olay günlüklerine erişin:</span><span class="sxs-lookup"><span data-stu-id="420d3-587">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="420d3-588">Başlat menüsünü açın, *Olay Görüntüleyicisi*araması yapın ve **Olay Görüntüleyicisi** uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-588">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="420d3-589">**Olay Görüntüleyicisi**, **Windows günlükleri** düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="420d3-589">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="420d3-590">Sistem olay günlüğünü açmak için **sistem** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-590">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="420d3-591">Uygulama olay günlüğünü açmak için **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="420d3-591">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="420d3-592">Hatalı uygulamayla ilişkili hataları arayın.</span><span class="sxs-lookup"><span data-stu-id="420d3-592">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="420d3-593">Uygulamayı komut isteminden çalıştırma</span><span class="sxs-lookup"><span data-stu-id="420d3-593">Run the app at a command prompt</span></span>

<span data-ttu-id="420d3-594">Birçok başlatma hatası olay günlüklerinde yararlı bilgiler oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="420d3-594">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="420d3-595">Uygulamayı barındırma sisteminde bir komut isteminde çalıştırarak bazı hataların nedenini bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="420d3-595">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="420d3-596">Uygulamadan ek ayrıntı günlüğe kaydetmek için, [günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [geliştirme ortamında](xref:fundamentals/environments)uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="420d3-596">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="420d3-597">Paket önbelleklerini temizle</span><span class="sxs-lookup"><span data-stu-id="420d3-597">Clear package caches</span></span>

<span data-ttu-id="420d3-598">Çalışan bir uygulama, geliştirme makinesindeki .NET Core SDK yükseltmeden veya uygulama içindeki paket sürümlerini değiştirirken hemen başarısız olabilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-598">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="420d3-599">Bazı durumlarda, önemli paketler ana yükseltmeler gerçekleştirirken bir uygulamayı bozabilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-599">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="420d3-600">Bu sorunların çoğu aşağıdaki yönergeleri izleyerek düzeltilebilir:</span><span class="sxs-lookup"><span data-stu-id="420d3-600">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="420d3-601">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="420d3-601">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="420d3-602">Bir komut kabuğundan [DotNet NuGet yerelleri, Tümünü Temizle](/dotnet/core/tools/dotnet-nuget-locals) ' i yürüterek paket önbelleklerini temizleyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-602">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="420d3-603">Paket önbelleklerini Temizleme, [NuGet. exe](https://www.nuget.org/downloads) aracı ile de gerçekleştirilebilir ve komut yürütülebilir `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="420d3-603">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="420d3-604">*NuGet. exe* , Windows masaüstü işletim sistemiyle birlikte paketlenmiş bir yüklemedir ve [NuGet Web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="420d3-604">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="420d3-605">Projeyi geri yükleyin ve yeniden derleyin.</span><span class="sxs-lookup"><span data-stu-id="420d3-605">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="420d3-606">Uygulamayı yeniden dağıtmadan önce sunucusundaki dağıtım klasöründeki tüm dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="420d3-606">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="420d3-607">Yavaş veya askıda olan uygulama</span><span class="sxs-lookup"><span data-stu-id="420d3-607">Slow or hanging app</span></span>

<span data-ttu-id="420d3-608">*Kilitlenme dökümü* , sistem belleğinin bir anlık görüntüsüdür ve uygulama kilitlenmesinin, başlatma hatasının veya yavaş uygulamanın nedenini belirlemenize yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-608">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="420d3-609">Uygulama kilitleniyor veya bir özel durumla karşılaşırsa</span><span class="sxs-lookup"><span data-stu-id="420d3-609">App crashes or encounters an exception</span></span>

<span data-ttu-id="420d3-610">Windows Hata Bildirimi bir döküm edinin ve çözümleyin [(WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="420d3-610">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="420d3-611">Kilitlenme dökümü dosyalarını tutmak için bir klasör oluşturun `c:\dumps` .</span><span class="sxs-lookup"><span data-stu-id="420d3-611">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="420d3-612">[Enabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) uygulama yürütülebilir adıyla çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="420d3-612">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="420d3-613">Uygulamayı kilitlenmenin oluşmasına neden olan koşullar altında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="420d3-613">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="420d3-614">Kilitlenme gerçekleştirildikten sonra, [Disabledökümler PowerShell betiğini](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="420d3-614">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="420d3-615">Uygulama kilitlenmeleri ve döküm koleksiyonu tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-615">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="420d3-616">PowerShell betiği, WER 'i uygulama başına en fazla beş döküm toplayacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="420d3-616">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="420d3-617">Kilitlenme dökümleri büyük miktarda disk alanı kaplar (her birine kadar çok gigabayt kadar).</span><span class="sxs-lookup"><span data-stu-id="420d3-617">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="420d3-618">Uygulama askıda kalıyor, başlatma sırasında başarısız oluyor veya normal şekilde çalışıyor</span><span class="sxs-lookup"><span data-stu-id="420d3-618">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="420d3-619">Bir uygulama *askıda* kaldığında (yanıt vermeyi keser ancak kilitlenmez), başlatma sırasında başarısız olur veya normal şekilde çalışır. [Kullanıcı modu döküm dosyaları:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) döküm oluşturmak için uygun bir aracı seçmek üzere en iyi aracı seçme.</span><span class="sxs-lookup"><span data-stu-id="420d3-619">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="420d3-620">Dökümü çözümle</span><span class="sxs-lookup"><span data-stu-id="420d3-620">Analyze the dump</span></span>

<span data-ttu-id="420d3-621">Bir döküm çeşitli yaklaşımlar kullanılarak analiz edilebilir.</span><span class="sxs-lookup"><span data-stu-id="420d3-621">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="420d3-622">Daha fazla bilgi için bkz. [Kullanıcı modu döküm dosyasını çözümleme](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="420d3-622">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="420d3-623">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="420d3-623">Additional resources</span></span>

* <span data-ttu-id="420d3-624">[Kestrel uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (https yapılandırması ve SNI desteği içerir)</span><span class="sxs-lookup"><span data-stu-id="420d3-624">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
