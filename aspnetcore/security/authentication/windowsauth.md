---
title: ASP.NET Core 'da Windows kimlik doğrulamasını yapılandırma
author: scottaddie
description: IIS ve HTTP.sys için ASP.NET Core Windows kimlik doğrulamasını nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330696"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="94eda-103">ASP.NET Core 'da Windows kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="94eda-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="94eda-104">[Scott Ade](https://twitter.com/Scott_Addie) tarafından</span><span class="sxs-lookup"><span data-stu-id="94eda-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="94eda-105">Windows kimlik doğrulaması (Negotiate, Kerberos veya NTLM kimlik doğrulaması olarak da bilinir), [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)veya [HTTP.sys](xref:fundamentals/servers/httpsys)ile barındırılan ASP.NET Core uygulamalar için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="94eda-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="94eda-106">Windows kimlik doğrulaması (Negotiate, Kerberos veya NTLM kimlik doğrulaması olarak da bilinir), [IIS](xref:host-and-deploy/iis/index) veya [HTTP.sys](xref:fundamentals/servers/httpsys)ile barındırılan ASP.NET Core uygulamalar için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="94eda-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="94eda-107">Windows kimlik doğrulaması, ASP.NET Core uygulama kullanıcılarının kimliğini doğrulamak için işletim sistemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="94eda-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="94eda-108">Sunucunuz, kullanıcıları tanımlamak için Active Directory etki alanı kimliklerini veya Windows hesaplarını kullanarak bir kurumsal ağda çalıştığında Windows kimlik doğrulamasını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="94eda-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="94eda-109">Windows kimlik doğrulaması, kullanıcıların, istemci uygulamalarının ve Web sunucularının aynı Windows etki alanına ait olduğu intranet ortamları için idealdir.</span><span class="sxs-lookup"><span data-stu-id="94eda-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="94eda-110">Windows kimlik doğrulaması, HTTP/2 ile desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="94eda-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="94eda-111">Kimlik doğrulaması sorunları HTTP/2 Yanıtlarına gönderilebilir, ancak kimlik doğrulamadan önce istemci HTTP/1.1 sürümüne indirgenmelidir.</span><span class="sxs-lookup"><span data-stu-id="94eda-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="94eda-112">Proxy ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="94eda-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="94eda-113">Windows kimlik doğrulaması, birincil olarak bir intranette kullanılan ve bir ara sunucu veya yük dengeleyicinin istemciler ve sunucular arasındaki trafiği genellikle işlemeyen bir durum bilgisi olan senaryodur.</span><span class="sxs-lookup"><span data-stu-id="94eda-113">Windows Authentication is a stateful scenario primarily used in an intranet, where a proxy or load balancer doesn't usually handle traffic between clients and servers.</span></span> <span data-ttu-id="94eda-114">Bir ara sunucu veya yük dengeleyici kullanılırsa, Windows kimlik doğrulaması yalnızca proxy veya yük dengeleyici için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="94eda-114">If a proxy or load balancer is used, Windows Authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="94eda-115">Kimlik doğrulamasını işler.</span><span class="sxs-lookup"><span data-stu-id="94eda-115">Handles the authentication.</span></span>
* <span data-ttu-id="94eda-116">Kullanıcı kimlik doğrulama bilgilerini uygulamaya geçirir (örneğin, bir istek üstbilgisinde), kimlik doğrulama bilgileri üzerinde davranır.</span><span class="sxs-lookup"><span data-stu-id="94eda-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="94eda-117">Proxy 'lerin ve yük dengeleyicilerin kullanıldığı ortamlarda Windows kimlik doğrulamasına alternatif olarak, OpenID Connect (OıDC) ile Federasyon Hizmetleri (ADFS) Active Directory.</span><span class="sxs-lookup"><span data-stu-id="94eda-117">An alternative to Windows Authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="94eda-118">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="94eda-118">IIS/IIS Express</span></span>

<span data-ttu-id="94eda-119"><xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> İçinde (ad alanı) çağırarak kimlik doğrulama hizmetleri ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="94eda-119">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="94eda-120">Başlatma ayarları (hata ayıklayıcı)</span><span class="sxs-lookup"><span data-stu-id="94eda-120">Launch settings (debugger)</span></span>

<span data-ttu-id="94eda-121">Başlatma ayarları için yapılandırma yalnızca IIS Express dosya *üzerindeki özellikleri/launchSettings.js* etkiler ve Windows kimlik doğrulaması için IIS 'yi yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="94eda-121">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="94eda-122">Sunucu yapılandırması [IIS](#iis) bölümünde açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="94eda-122">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="94eda-123">Visual Studio veya .NET Core CLI ile kullanılabilen **Web uygulaması** şablonu, Windows kimlik doğrulamasını destekleyecek şekilde yapılandırılabilir ve bu, dosyadaki *özellikleri/launchSettings.js* otomatik olarak güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="94eda-123">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94eda-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94eda-124">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94eda-125">**Yeni proje**</span><span class="sxs-lookup"><span data-stu-id="94eda-125">**New project**</span></span>

1. <span data-ttu-id="94eda-126">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="94eda-126">Create a new project.</span></span>
1. <span data-ttu-id="94eda-127">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="94eda-128">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-128">Select **Next**.</span></span>
1. <span data-ttu-id="94eda-129">**Proje adı** alanına bir ad girin.</span><span class="sxs-lookup"><span data-stu-id="94eda-129">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="94eda-130">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="94eda-130">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="94eda-131">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-131">Select **Create**.</span></span>
1. <span data-ttu-id="94eda-132">**Kimlik doğrulaması**altında **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-132">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="94eda-133">**Kimlik doğrulamasını Değiştir** penceresinde, **Windows kimlik doğrulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-133">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="94eda-134">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-134">Select **OK**.</span></span>
1. <span data-ttu-id="94eda-135">**Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-135">Select **Web Application**.</span></span>
1. <span data-ttu-id="94eda-136">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-136">Select **Create**.</span></span>

<span data-ttu-id="94eda-137">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="94eda-137">Run the app.</span></span> <span data-ttu-id="94eda-138">Kullanıcı adı, işlenmiş uygulamanın kullanıcı arabiriminde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="94eda-138">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="94eda-139">**Mevcut proje**</span><span class="sxs-lookup"><span data-stu-id="94eda-139">**Existing project**</span></span>

<span data-ttu-id="94eda-140">Projenin özellikleri Windows kimlik doğrulamasını etkinleştirir ve anonim kimlik doğrulamasını devre dışı bırakır:</span><span class="sxs-lookup"><span data-stu-id="94eda-140">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="94eda-141">**Çözüm Gezgini** ' de projeye sağ tıklayın ve **Özellikler**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-141">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="94eda-142">**Hata Ayıkla** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-142">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="94eda-143">**Anonim kimlik doğrulamasını etkinleştir**onay kutusunun işaretini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="94eda-143">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="94eda-144">**Windows kimlik doğrulamasını etkinleştir**onay kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="94eda-144">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="94eda-145">Özellik sayfasını kaydedin ve kapatın.</span><span class="sxs-lookup"><span data-stu-id="94eda-145">Save and close the property page.</span></span>

<span data-ttu-id="94eda-146">Alternatif olarak, Özellikler `iisSettings` dosyadaki *launchSettings.js* düğümünde yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="94eda-146">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[<span data-ttu-id="94eda-147">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="94eda-147">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="94eda-148">**Yeni proje**</span><span class="sxs-lookup"><span data-stu-id="94eda-148">**New project**</span></span>

<span data-ttu-id="94eda-149">Bağımsız değişkenle [DotNet New](/dotnet/core/tools/dotnet-new) komutunu yürütün `webapp` (ASP.NET Core Web uygulaması) ve `--auth Windows` anahtar:</span><span class="sxs-lookup"><span data-stu-id="94eda-149">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```dotnetcli
dotnet new webapp --auth Windows
```

<span data-ttu-id="94eda-150">**Mevcut proje**</span><span class="sxs-lookup"><span data-stu-id="94eda-150">**Existing project**</span></span>

<span data-ttu-id="94eda-151">`iisSettings`Dosyadaki *launchSettings.js* düğümünü güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="94eda-151">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="94eda-152">Mevcut bir projeyi değiştirirken, proje dosyasının [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) **veya** [Microsoft. aspnetcore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet paketi için bir paket başvurusu içerdiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="94eda-152">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="94eda-153">IIS</span><span class="sxs-lookup"><span data-stu-id="94eda-153">IIS</span></span>

<span data-ttu-id="94eda-154">IIS, ASP.NET Core uygulamalarını barındırmak için [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module) kullanır.</span><span class="sxs-lookup"><span data-stu-id="94eda-154">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="94eda-155">Windows kimlik doğrulaması, *web.config* dosyası aracılığıyla IIS için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="94eda-155">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="94eda-156">Aşağıdaki bölümlerde aşağıdakilerin nasıl yapılacağı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="94eda-156">The following sections show how to:</span></span>

* <span data-ttu-id="94eda-157">Uygulama dağıtıldığında sunucuda Windows kimlik doğrulamasını etkinleştiren bir yerel *web.config* dosyası sağlayın.</span><span class="sxs-lookup"><span data-stu-id="94eda-157">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="94eda-158">Sunucuda zaten dağıtılan bir ASP.NET Core uygulamasının *web.config* dosyasını YAPıLANDıRMAK Için IIS Yöneticisi 'ni kullanın.</span><span class="sxs-lookup"><span data-stu-id="94eda-158">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="94eda-159">Daha önce yapmadıysanız, IIS 'yi ASP.NET Core uygulamaları barındıracak şekilde etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="94eda-159">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="94eda-160">Daha fazla bilgi için bkz. <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="94eda-160">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="94eda-161">Windows kimlik doğrulaması için IIS rol hizmetini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="94eda-161">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="94eda-162">Daha fazla bilgi için bkz. [IIS rol hizmetlerinde Windows kimlik doğrulamasını etkinleştirme (bkz. 2. adım)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="94eda-162">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="94eda-163">[IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , varsayılan olarak isteklerin kimliğini otomatik olarak doğrulamak için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="94eda-163">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="94eda-164">Daha fazla bilgi için bkz. [IIS Ile Windows üzerinde ana bilgisayar ASP.NET Core: IIS seçenekleri (Automatıcauthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="94eda-164">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="94eda-165">ASP.NET Core modülü, Windows kimlik doğrulama belirtecini varsayılan olarak uygulamaya iletecek şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="94eda-165">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="94eda-166">Daha fazla bilgi için bkz. [ASP.NET Core modülü yapılandırma başvurusu: aspNetCore öğesinin öznitelikleri](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="94eda-166">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="94eda-167">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="94eda-167">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="94eda-168">**Projeyi yayımlamadan ve dağıtmaya başlamadan önce** aşağıdaki *web.config* dosyasını proje köküne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="94eda-168">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="94eda-169">Proje, .NET Core SDK ( `<IsTransformWebConfigDisabled>` Proje dosyasında özelliği olarak ayarlanmış olmayan) tarafından yayımlandığında `true` , yayımlanan *web.config* dosyası `<location><system.webServer><security><authentication>` bölümünü içerir.</span><span class="sxs-lookup"><span data-stu-id="94eda-169">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="94eda-170">Özelliği hakkında daha fazla bilgi için `<IsTransformWebConfigDisabled>` bkz <xref:host-and-deploy/iis/index#webconfig-file> ..</span><span class="sxs-lookup"><span data-stu-id="94eda-170">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="94eda-171">**Projeyi yayımladıktan ve dağıttıktan sonra** IIS yöneticisiyle sunucu tarafı yapılandırması gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="94eda-171">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="94eda-172">IIS Yöneticisi 'nde, **Bağlantılar** kenar çubuğu ' nun **sıteler** düğümü altında IIS sitesini seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-172">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="94eda-173">**IIS** alanında **kimlik doğrulaması** ' na çift tıklayın.</span><span class="sxs-lookup"><span data-stu-id="94eda-173">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="94eda-174">**Anonim kimlik doğrulamasını**seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-174">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="94eda-175">**Eylemler** kenar çubuğunda **devre dışı bırak** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-175">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="94eda-176">**Windows kimlik doğrulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-176">Select **Windows Authentication**.</span></span> <span data-ttu-id="94eda-177">**Eylemler** kenar çubuğunda **Etkinleştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="94eda-177">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="94eda-178">Bu eylemler çekilirken, IIS Yöneticisi uygulamanın *web.config* dosyasını değiştirir.</span><span class="sxs-lookup"><span data-stu-id="94eda-178">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="94eda-179">`<system.webServer><security><authentication>`Ve için güncelleştirilmiş ayarlarla bir düğüm eklenir `anonymousAuthentication` `windowsAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="94eda-179">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="94eda-180">`<system.webServer>`IIS Yöneticisi tarafından *web.config* dosyasına eklenen bölüm, `<location>` uygulama yayımlandığında .NET Core SDK tarafından eklenen uygulama bölümünün dışındadır.</span><span class="sxs-lookup"><span data-stu-id="94eda-180">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="94eda-181">Bölüm düğümün dışına eklendiğinden `<location>` , ayarlar herhangi bir [alt uygulama](xref:host-and-deploy/iis/index#sub-applications) tarafından geçerli uygulamaya devralınır.</span><span class="sxs-lookup"><span data-stu-id="94eda-181">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="94eda-182">Devralmayı önlemek için, eklenen `<security>` bölümü `<location><system.webServer>` .NET Core SDK bölümün içine taşıyın.</span><span class="sxs-lookup"><span data-stu-id="94eda-182">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="94eda-183">IIS Yöneticisi IIS yapılandırmasını eklemek için kullanıldığında, yalnızca sunucudaki *web.config* dosyasını etkiler.</span><span class="sxs-lookup"><span data-stu-id="94eda-183">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="94eda-184">Sunucunun *web.config* kopyası projenin *web.config* dosyası tarafından değiştirilirse uygulamanın sonraki dağıtımı sunucudaki ayarların üzerine yazabilir.</span><span class="sxs-lookup"><span data-stu-id="94eda-184">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="94eda-185">Ayarları yönetmek için aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="94eda-185">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="94eda-186">Dağıtım sırasında dosyanın üzerine yazıldıktan sonra *web.config* dosyasındaki ayarları SıFıRLAMAK Için IIS Yöneticisi 'ni kullanın.</span><span class="sxs-lookup"><span data-stu-id="94eda-186">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="94eda-187">Ayarlarla yerel olarak uygulamaya bir *web.config dosyası* ekleyin.</span><span class="sxs-lookup"><span data-stu-id="94eda-187">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="94eda-188">Kestrel</span><span class="sxs-lookup"><span data-stu-id="94eda-188">Kestrel</span></span>

<span data-ttu-id="94eda-189">Windows, Linux ve macOS 'ta Negotiate ve Kerberos kullanarak Windows kimlik doğrulamasını desteklemek için [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet paketi [Kestrel](xref:fundamentals/servers/kestrel) ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="94eda-189">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate and Kerberos on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="94eda-190">Kimlik bilgileri bir bağlantı üzerindeki istekler arasında kalıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="94eda-190">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="94eda-191">*Proxy, Kestrel ile bir 1:1 bağlantı benzeşimi (kalıcı bağlantı) tutmadığı müddetçe, Negotiate kimlik doğrulaması proxy ile kullanılmamalıdır.*</span><span class="sxs-lookup"><span data-stu-id="94eda-191">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="94eda-192">Negotiate işleyicisi, temeldeki sunucunun Windows kimlik doğrulamasını yerel olarak destekleyip desteklemediğini ve etkinleştirilip etkinleştirilmediğini algılar.</span><span class="sxs-lookup"><span data-stu-id="94eda-192">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="94eda-193">Sunucu Windows kimlik doğrulamasını destekliyorsa, ancak devre dışıysa sunucu uygulamasını etkinleştirmenizi isteyen bir hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="94eda-193">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="94eda-194">Sunucuda Windows kimlik doğrulaması etkinleştirildiğinde, anlaşma işleyicisi saydam olarak buna iletilir.</span><span class="sxs-lookup"><span data-stu-id="94eda-194">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

<span data-ttu-id="94eda-195">Ve ' i çağırarak kimlik doğrulama hizmetleri ekleyin <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="94eda-195">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> in `Startup.ConfigureServices`:</span></span>

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="94eda-196">Şunları çağırarak kimlik doğrulama ara yazılımı ekleyin <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="94eda-196">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();
```

<span data-ttu-id="94eda-197">Ara yazılım hakkında daha fazla bilgi için bkz <xref:fundamentals/middleware/index> ..</span><span class="sxs-lookup"><span data-stu-id="94eda-197">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="94eda-198">Anonim isteklere izin verilir.</span><span class="sxs-lookup"><span data-stu-id="94eda-198">Anonymous requests are allowed.</span></span> <span data-ttu-id="94eda-199">Kimlik doğrulaması için anonim istekleri zorluk [ASP.NET Core yetkilendirmeyi](xref:security/authorization/introduction) kullanın.</span><span class="sxs-lookup"><span data-stu-id="94eda-199">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="94eda-200">Windows ortam yapılandırması</span><span class="sxs-lookup"><span data-stu-id="94eda-200">Windows environment configuration</span></span>

<span data-ttu-id="94eda-201">[Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) bileşeni, Kullanıcı modu kimlik doğrulaması gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="94eda-201">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="94eda-202">Hizmet sorumlusu adları (SPN 'Ler) makine hesabını değil, hizmeti çalıştıran kullanıcı hesabına eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="94eda-202">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="94eda-203">`setspn -S HTTP/myservername.mydomain.com myuser`Yönetim komut kabuğu 'nda yürütün.</span><span class="sxs-lookup"><span data-stu-id="94eda-203">Execute `setspn -S HTTP/myservername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="94eda-204">Linux ve macOS ortam yapılandırması</span><span class="sxs-lookup"><span data-stu-id="94eda-204">Linux and macOS environment configuration</span></span>

<span data-ttu-id="94eda-205">Linux veya macOS makinesini bir Windows etki alanına katılmaya yönelik yönergeler, [Windows kimlik doğrulaması-Kerberos makalesini kullanarak SQL Server Azure Data Studio bağlanma](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) ' da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="94eda-205">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="94eda-206">Yönergeler, etki alanındaki Linux makinesi için bir makine hesabı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="94eda-206">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="94eda-207">SPN 'Ler bu makine hesabına eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="94eda-207">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="94eda-208">[Windows kimlik doğrulaması-Kerberos makalesini kullanarak SQL Server Azure Data Studio bağlanma](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) ' daki kılavuzdan sonra, `python-software-properties` gerekirse öğesini ile değiştirin `python3-software-properties` .</span><span class="sxs-lookup"><span data-stu-id="94eda-208">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="94eda-209">Linux veya macOS makinesi etki alanına katıldığında, SPN 'Ler içeren bir [keytab dosyası](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) sağlamak için ek adımlar gerekir:</span><span class="sxs-lookup"><span data-stu-id="94eda-209">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="94eda-210">Etki alanı denetleyicisinde, makine hesabına yeni Web hizmeti SPN 'Leri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="94eda-210">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="94eda-211">Bir keytab dosyası oluşturmak için [Ktpass](/windows-server/administration/windows-commands/ktpass) kullanın:</span><span class="sxs-lookup"><span data-stu-id="94eda-211">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="94eda-212">Bazı alanların gösterildiği gibi büyük harfle belirtilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="94eda-212">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="94eda-213">Keytab dosyasını Linux veya macOS makinesine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="94eda-213">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="94eda-214">Bir ortam değişkeni aracılığıyla keytab dosyasını seçin:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="94eda-214">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="94eda-215">`klist`Şu anda kullanılabilecek SPN 'leri göstermek için çağırın.</span><span class="sxs-lookup"><span data-stu-id="94eda-215">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="94eda-216">Bir keytab dosyası, etki alanı erişimi kimlik bilgilerini içerir ve buna uygun şekilde korunması gerekir.</span><span class="sxs-lookup"><span data-stu-id="94eda-216">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="94eda-217">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="94eda-217">HTTP.sys</span></span>

<span data-ttu-id="94eda-218">[HTTP.sys](xref:fundamentals/servers/httpsys) , NEGOTIATE, NTLM veya temel kimlik doğrulaması kullanarak çekirdek modu Windows kimlik doğrulamasını destekler.</span><span class="sxs-lookup"><span data-stu-id="94eda-218">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="94eda-219"><xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> İçinde (ad alanı) çağırarak kimlik doğrulama hizmetleri ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="94eda-219">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="94eda-220">Uygulamanın Web konağını, Windows kimlik doğrulaması (*program.cs*) ile HTTP.sys kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="94eda-220">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="94eda-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*><xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName>ad alanında.</span><span class="sxs-lookup"><span data-stu-id="94eda-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="94eda-222">Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="94eda-222">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="94eda-223">Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP.sys desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="94eda-223">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="94eda-224">Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="94eda-224">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="94eda-225">Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="94eda-225">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="94eda-226">HTTP.sys nano sunucu 1709 veya sonraki bir sürümde desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="94eda-226">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="94eda-227">Windows kimlik doğrulaması 'nı ve nano sunucu ile HTTP.sys kullanmak için, bir [sunucu çekirdeği (Microsoft/windowsservercore) kapsayıcısı](https://hub.docker.com/r/microsoft/windowsservercore/)kullanın.</span><span class="sxs-lookup"><span data-stu-id="94eda-227">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="94eda-228">Sunucu Çekirdeği hakkında daha fazla bilgi için bkz. [Windows Server 'Da Sunucu Çekirdeği yükleme seçeneği nedir?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="94eda-228">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="94eda-229">Kullanıcıları yetkilendir</span><span class="sxs-lookup"><span data-stu-id="94eda-229">Authorize users</span></span>

<span data-ttu-id="94eda-230">Anonim erişimin yapılandırma durumu, `[Authorize]` ve `[AllowAnonymous]` özniteliklerinin uygulamada kullanılma şeklini belirler.</span><span class="sxs-lookup"><span data-stu-id="94eda-230">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="94eda-231">Aşağıdaki iki bölümde, anonim erişimin izin verilmeyen ve izin verilen yapılandırma durumlarının nasıl işleneceği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="94eda-231">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="94eda-232">Anonim erişime izin verme</span><span class="sxs-lookup"><span data-stu-id="94eda-232">Disallow anonymous access</span></span>

<span data-ttu-id="94eda-233">Windows kimlik doğrulaması etkinleştirildiğinde ve anonim erişim devre dışı bırakıldığında, `[Authorize]` ve `[AllowAnonymous]` özniteliklerinin etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="94eda-233">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="94eda-234">Bir IIS sitesi anonim erişime izin vermeyecek şekilde yapılandırıldıysa, istek uygulamaya hiçbir şekilde ulaşmaz.</span><span class="sxs-lookup"><span data-stu-id="94eda-234">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="94eda-235">Bu nedenle, `[AllowAnonymous]` öznitelik geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="94eda-235">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="94eda-236">Anonim erişime izin ver</span><span class="sxs-lookup"><span data-stu-id="94eda-236">Allow anonymous access</span></span>

<span data-ttu-id="94eda-237">Hem Windows kimlik doğrulaması hem de anonim erişim etkinleştirildiğinde, `[Authorize]` ve özniteliklerini kullanın `[AllowAnonymous]` .</span><span class="sxs-lookup"><span data-stu-id="94eda-237">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="94eda-238">`[Authorize]`Özniteliği, uygulamanın kimlik doğrulaması gerektiren uç noktaların güvenliğini sağlamanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="94eda-238">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="94eda-239">`[AllowAnonymous]`Özniteliği, `[Authorize]` anonim erişime izin veren uygulamalardaki özniteliği geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="94eda-239">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="94eda-240">Öznitelik kullanım ayrıntıları için bkz <xref:security/authorization/simple> ..</span><span class="sxs-lookup"><span data-stu-id="94eda-240">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="94eda-241">Varsayılan olarak, bir sayfaya erişim yetkisi olmayan kullanıcılara boş bir HTTP 403 yanıtı sunulur.</span><span class="sxs-lookup"><span data-stu-id="94eda-241">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="94eda-242">[Statuscodepages ara yazılımı](xref:fundamentals/error-handling#usestatuscodepages) , kullanıcılara daha iyi bir "erişim reddedildi" deneyimi sunacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="94eda-242">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="94eda-243">Kimliğe bürünme</span><span class="sxs-lookup"><span data-stu-id="94eda-243">Impersonation</span></span>

<span data-ttu-id="94eda-244">ASP.NET Core kimliğe bürünme uygulamaz.</span><span class="sxs-lookup"><span data-stu-id="94eda-244">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="94eda-245">Uygulamalar, uygulama havuzu veya işlem kimliği kullanarak tüm istekler için uygulamanın kimliğiyle çalışır.</span><span class="sxs-lookup"><span data-stu-id="94eda-245">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="94eda-246">Uygulamanın bir kullanıcı adına bir eylem gerçekleştirmesi gerekiyorsa, içindeki bir [Terminal satır içi ara yazılım](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) Içinde [WindowsIdentity. runınkimliğine](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) bürünme kullanın `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="94eda-246">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="94eda-247">Bu bağlamda tek bir eylem çalıştırın ve sonra bağlamı kapatın.</span><span class="sxs-lookup"><span data-stu-id="94eda-247">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="94eda-248">`RunImpersonated`, zaman uyumsuz işlemleri desteklemez ve karmaşık senaryolar için kullanılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="94eda-248">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="94eda-249">Örneğin, tüm isteklerin veya ara yazılım zincirlerinin sarmalanması desteklenmez veya önerilmez.</span><span class="sxs-lookup"><span data-stu-id="94eda-249">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="94eda-250">[Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) paketi Windows, Linux ve MacOS 'ta kimlik doğrulamaya izin sağlarken, kimliğe bürünme yalnızca Windows 'da desteklenir.</span><span class="sxs-lookup"><span data-stu-id="94eda-250">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="94eda-251">Talep dönüşümleri</span><span class="sxs-lookup"><span data-stu-id="94eda-251">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="94eda-252">IIS ile barındırırken, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="94eda-252">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="94eda-253">Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="94eda-253">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="94eda-254">Daha fazla bilgi ve talep dönüştürmelerini etkinleştiren bir kod örneği için bkz <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> ..</span><span class="sxs-lookup"><span data-stu-id="94eda-254">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="94eda-255">IIS işlem içi modunda barındırırken, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="94eda-255">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="94eda-256">Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="94eda-256">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="94eda-257">İşlem içinde barındırırken talep dönüştürmelerini etkinleştiren daha fazla bilgi ve kod örneği için bkz <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> ..</span><span class="sxs-lookup"><span data-stu-id="94eda-257">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="94eda-258">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="94eda-258">Additional resources</span></span>

* [<span data-ttu-id="94eda-259">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="94eda-259">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
