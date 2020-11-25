---
title: ASP.NET Core Azure Key Vault yapılandırma sağlayıcısı
author: rick-anderson
description: Çalışma zamanında yüklenen ad-değer çiftlerini kullanarak bir uygulamayı yapılandırmak için Azure Key Vault yapılandırma sağlayıcısını nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
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
uid: security/key-vault-configuration
ms.openlocfilehash: 7f5cd3de38f1e45d9b188c513a0e62ca658b2992
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035911"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="db623-103">ASP.NET Core Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="db623-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="db623-104">, [Andrew Stanton-nurte](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="db623-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="db623-105">Bu belgede, uygulama yapılandırma değerlerini Azure Key Vault gizliliklerden yüklemek için [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) yapılandırma sağlayıcısının nasıl kullanılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="db623-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="db623-106">Azure Key Vault, uygulama ve hizmetler tarafından kullanılan şifreleme anahtarlarını ve gizli dizileri koruma konusunda yardımcı olan bulut tabanlı bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="db623-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="db623-107">ASP.NET Core uygulamalarla Azure Key Vault kullanmaya yönelik yaygın senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="db623-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="db623-108">Hassas yapılandırma verilerine erişimi denetleme.</span><span class="sxs-lookup"><span data-stu-id="db623-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="db623-109">Yapılandırma verilerini depolarken FIPS 140-2 düzey 2 doğrulanan donanım güvenlik modülleri (HSM 'ler) gereksinimini karşılarsınız.</span><span class="sxs-lookup"><span data-stu-id="db623-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="db623-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db623-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="db623-111">Paketler</span><span class="sxs-lookup"><span data-stu-id="db623-111">Packages</span></span>

<span data-ttu-id="db623-112">Azure.Extensions.AspNetCore.Configyönlendirmeye bir paket başvurusu ekleyin [ . Gizli](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) dizi paketi.</span><span class="sxs-lookup"><span data-stu-id="db623-112">Add a package reference to the [Azure.Extensions.AspNetCore.Configuration.Secrets](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="db623-113">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="db623-113">Sample app</span></span>

<span data-ttu-id="db623-114">Örnek uygulama, `#define` *program.cs* dosyasının en üstünde yer aldığı ifadeye göre belirlenen iki moddan birinde çalışır:</span><span class="sxs-lookup"><span data-stu-id="db623-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="db623-115">`Certificate`: Azure Key Vault depolanan gizli dizileri erişmek için Azure Key Vault Istemci KIMLIĞI ve X. 509.952 sertifikası kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="db623-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="db623-116">Örneğin bu sürümü, Azure App Service dağıtılan herhangi bir konumdan veya ASP.NET Core uygulamasına hizmet veren herhangi bir konağa çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="db623-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="db623-117">`Managed`: Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasıyla Azure Key Vault üzere uygulamanın kimliğini doğrulamak için [Azure kaynakları Için yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="db623-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="db623-118">Kimlik doğrulaması için Yönetilen kimlikler kullanıldığında, bir Azure AD uygulama KIMLIĞI ve parolası (gizli anahtar) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="db623-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="db623-119">`Managed`Örneğin sürümünün Azure 'a dağıtılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="db623-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="db623-120">[Azure kaynakları Için Yönetilen kimlikler bölümünü kullanma](#use-managed-identities-for-azure-resources) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="db623-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="db623-121">Önişlemci yönergeleri () kullanarak örnek bir uygulamanın nasıl yapılandırılacağı hakkında daha fazla bilgi için `#define` bkz <xref:index#preprocessor-directives-in-sample-code> ..</span><span class="sxs-lookup"><span data-stu-id="db623-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="db623-122">Geliştirme ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="db623-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="db623-123">[Gizli dizi Yöneticisi aracını](xref:security/app-secrets)kullanarak gizli dizileri yerel olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="db623-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="db623-124">Örnek uygulama, geliştirme ortamındaki yerel makinede çalıştığında, gizlilikler yerel kullanıcı gizli dizileri deposundan yüklenir.</span><span class="sxs-lookup"><span data-stu-id="db623-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="db623-125">Gizli dizi Yöneticisi Aracı, `<UserSecretsId>` uygulamanın proje dosyasında bir özellik gerektirir.</span><span class="sxs-lookup"><span data-stu-id="db623-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="db623-126">Özellik değerini ( `{GUID}` ) herhangi bir BENZERSIZ GUID 'ye ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="db623-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="db623-127">Gizlilikler ad-değer çiftleri olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="db623-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="db623-128">Hiyerarşik değerler (yapılandırma bölümleri) `:` [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index) anahtarı adlarında ayırıcı olarak bir (iki nokta üst üste) kullanır.</span><span class="sxs-lookup"><span data-stu-id="db623-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="db623-129">Gizli dizi, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır; burada `{SECRET NAME}` ad ve `{SECRET VALUE}` değerdir:</span><span class="sxs-lookup"><span data-stu-id="db623-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="db623-130">Örnek uygulamanın gizli dizilerini ayarlamak için, projenin [içerik kökünden](xref:fundamentals/index#content-root) bir komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="db623-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="db623-131">Bu gizlilikler, [Azure Key Vault bölümündeki üretim ortamındaki gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında Azure Key Vault depolandığında, `_dev` sonek olarak değiştirilir `_prod` .</span><span class="sxs-lookup"><span data-stu-id="db623-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="db623-132">Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren bir görsel ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="db623-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="db623-133">Azure Key Vault ile üretim ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="db623-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="db623-134">Hızlı başlangıç tarafından sunulan yönergeler [: Azure CLI kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-cli) , bir Azure Key Vault oluşturmak ve örnek uygulama tarafından kullanılan gizli dizileri depolamak için burada özetlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="db623-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="db623-135">Daha ayrıntılı bilgi için konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="db623-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="db623-136">[Azure Portal](https://portal.azure.com/)aşağıdaki yöntemlerden birini kullanarak Azure Cloud Shell 'i açın:</span><span class="sxs-lookup"><span data-stu-id="db623-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="db623-137">Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="db623-138">Metin kutusunda "Azure CLı" arama dizesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="db623-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="db623-139">**Cloud Shell Başlat** düğmesini kullanarak tarayıcınızda Cloud Shell açın.</span><span class="sxs-lookup"><span data-stu-id="db623-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="db623-140">Azure portal sağ üst köşesindeki menüdeki **Cloud Shell** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="db623-141">Daha fazla bilgi için bkz. [Azure CLI](/cli/azure/) ve [Azure Cloud Shell Genel Bakış](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="db623-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="db623-142">Henüz kimlik doğrulamasından geçirilmemişse komutuyla oturum açın `az login` .</span><span class="sxs-lookup"><span data-stu-id="db623-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="db623-143">Aşağıdaki komutla bir kaynak grubu oluşturun; burada, `{RESOURCE GROUP NAME}` Yeni kaynak grubu için kaynak grubu adı ve `{LOCATION}` Azure bölgesi (Datacenter) olur:</span><span class="sxs-lookup"><span data-stu-id="db623-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="db623-144">Aşağıdaki komutla kaynak grubunda bir Anahtar Kasası oluşturun; burada `{KEY VAULT NAME}` Yeni anahtar kasasının adı ve `{LOCATION}` Azure bölgesi (Datacenter) bulunur:</span><span class="sxs-lookup"><span data-stu-id="db623-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="db623-145">Anahtar kasasında ad-değer çiftleri olarak gizli diziler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="db623-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="db623-146">Azure Key Vault gizli dizi adları, alfasayısal karakterler ve tireler ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="db623-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="db623-147">Hiyerarşik değerler (yapılandırma bölümleri) `--` ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="db623-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="db623-148">Genellikle [ASP.NET Core yapılandırmasındaki](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlandırmak için kullanılan iki nokta üst üste, Anahtar Kasası gizli adlarında izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="db623-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="db623-149">Bu nedenle, gizlilikler uygulamanın yapılandırmasına yüklendiğinde iki tire kullanılır ve iki nokta üst üste bulunur.</span><span class="sxs-lookup"><span data-stu-id="db623-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="db623-150">Aşağıdaki gizlilikler örnek uygulamayla kullanım içindir.</span><span class="sxs-lookup"><span data-stu-id="db623-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="db623-151">Değerler, `_prod` `_dev` Kullanıcı parolalarından geliştirme ortamında yüklenen sonek değerlerinden ayırt etmek için bir sonek içerir.</span><span class="sxs-lookup"><span data-stu-id="db623-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="db623-152">`{KEY VAULT NAME}`Önceki adımda oluşturduğunuz anahtar kasasının adıyla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="db623-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="db623-153">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanın</span><span class="sxs-lookup"><span data-stu-id="db623-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="db623-154">**Uygulama Azure dışında barındırıldığı zaman** bir anahtar kasasında kimlik doğrulaması yapmak IÇIN Azure AD, Azure Key Vault ve uygulamayı bir Azure ACTIVE DIRECTORY uygulama kimliği ve X. 509.440 sertifikası kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="db623-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="db623-155">Daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="db623-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="db623-156">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanılması desteklenmekle birlikte, Azure 'da bir uygulama barındırılırken [Azure kaynakları Için Yönetilen kimlikler](#use-managed-identities-for-azure-resources) kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="db623-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="db623-157">Yönetilen kimlikler, uygulamada veya geliştirme ortamında bir sertifikanın depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="db623-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="db623-158">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki ifade olarak AYARLANDıĞıNDA bir uygulama kimliği ve X. 509.440 sertifikası kullanır `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="db623-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="db623-159">PKCS # 12 Arşivi (*. pfx*) sertifikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="db623-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="db623-160">Sertifika oluşturma seçenekleri Windows ve [OpenSSL](https://www.openssl.org/) [üzerinde MakeCert](/windows/desktop/seccrypto/makecert) içerir.</span><span class="sxs-lookup"><span data-stu-id="db623-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="db623-161">Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükler.</span><span class="sxs-lookup"><span data-stu-id="db623-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="db623-162">Anahtarı verilebilir olarak işaretlemek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="db623-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="db623-163">Sertifikanın daha sonra bu işlemde kullanılan parmak izini aklınızda bulunur.</span><span class="sxs-lookup"><span data-stu-id="db623-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="db623-164">PKCS # 12 Arşivi (*. pfx*) sertifikasını der kodlu bir sertifika (*. cer*) olarak dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="db623-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="db623-165">Uygulamayı Azure AD 'ye kaydedin (**uygulama kayıtları**).</span><span class="sxs-lookup"><span data-stu-id="db623-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="db623-166">DER kodlu sertifikayı (*. cer*) Azure AD 'ye yükleyin:</span><span class="sxs-lookup"><span data-stu-id="db623-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="db623-167">Azure AD 'de uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="db623-168">**Sertifikalar & gizli** dizi sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="db623-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="db623-169">Ortak anahtarı içeren sertifikayı karşıya yüklemek için **sertifikayı karşıya yükle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="db623-170">*. Cer*, *. pek* veya *. CRT* sertifikası kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="db623-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="db623-171">Anahtar Kasası adı, uygulama KIMLIĞI ve sertifika parmak izini uygulamanın *appsettings.json* dosyasına depolayın.</span><span class="sxs-lookup"><span data-stu-id="db623-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="db623-172">Azure portal **ana** kasaları ' ne gidin.</span><span class="sxs-lookup"><span data-stu-id="db623-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="db623-173">Azure Key Vault bölümünde, [üretim ortamındaki gizli dizi deposunda](#secret-storage-in-the-production-environment-with-azure-key-vault) oluşturduğunuz anahtar kasasını seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="db623-174">**Erişim ilkeleri**' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="db623-175">**Erişim Ilkesi Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="db623-176">**Gizli izinleri** açın ve uygulamaya **Get** ve **list** izinleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="db623-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="db623-177">**Sorumlu Seç** ' i seçin ve kayıtlı uygulamayı ada göre seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="db623-178">**Seç** düğmesini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="db623-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="db623-179">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-179">Select **OK**.</span></span>
1. <span data-ttu-id="db623-180">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-180">Select **Save**.</span></span>
1. <span data-ttu-id="db623-181">Uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="db623-181">Deploy the app.</span></span>

<span data-ttu-id="db623-182">`Certificate`Örnek uygulama, yapılandırma değerlerini, `IConfigurationRoot` parola adı ile aynı adla alır:</span><span class="sxs-lookup"><span data-stu-id="db623-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="db623-183">Hiyerarşik olmayan değerler: için değeri `SecretName` ile elde edilir `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="db623-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="db623-184">Hiyerarşik değerler (bölümler): `:` (iki nokta üst üste) gösterimini veya `GetSection` genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="db623-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="db623-185">Yapılandırma değerini elde etmek için şu yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="db623-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="db623-186">X. 509.440 sertifikası işletim sistemi tarafından yönetiliyor.</span><span class="sxs-lookup"><span data-stu-id="db623-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="db623-187">Uygulama, **Addavzurekeykasasını** dosya tarafından sağlanan değerlerle çağırır *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="db623-187">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="db623-188">Örnek değerler:</span><span class="sxs-lookup"><span data-stu-id="db623-188">Example values:</span></span>

* <span data-ttu-id="db623-189">Anahtar Kasası adı: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="db623-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="db623-190">Uygulama KIMLIĞI: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="db623-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="db623-191">Sertifika parmak izi: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="db623-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="db623-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="db623-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="db623-193">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="db623-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="db623-194">Geliştirme ortamında, gizli anahtar değerleri sonek ile yüklenir `_dev` .</span><span class="sxs-lookup"><span data-stu-id="db623-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="db623-195">Üretim ortamında, değerler sonek ile yüklenir `_prod` .</span><span class="sxs-lookup"><span data-stu-id="db623-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="db623-196">Azure kaynakları için Yönetilen kimlikler kullanma</span><span class="sxs-lookup"><span data-stu-id="db623-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="db623-197">Azure **'a dağıtılan bir uygulama** , [Azure kaynakları için yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanarak uygulamanın, KIMLIK bilgileri (uygulama kimliği ve parola/istemci gizli anahtarı) olmadan Azure AD kimlik doğrulaması kullanarak Azure Key Vault kimlik doğrulamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="db623-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="db623-198">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki Ifade olarak ayarlandığında Azure kaynakları için Yönetilen kimlikler kullanır `Managed` .</span><span class="sxs-lookup"><span data-stu-id="db623-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="db623-199">Kasa adını uygulamanın *appsettings.json* dosyasına girin.</span><span class="sxs-lookup"><span data-stu-id="db623-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="db623-200">Örnek uygulama sürüme ayarlandığında bir uygulama KIMLIĞI ve parola (Istemci gizli anahtarı) gerektirmez `Managed` , bu nedenle bu yapılandırma girişlerini yoksayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="db623-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="db623-201">Uygulama Azure 'a dağıtılır ve Azure, yalnızca dosyada depolanan kasa adını kullanarak Azure Key Vault erişmek için uygulamanın kimliğini doğrular *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="db623-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="db623-202">Azure App Service için örnek uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="db623-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="db623-203">Azure App Service dağıtılan bir uygulama, hizmet oluşturulduğunda Azure AD 'ye otomatik olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="db623-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="db623-204">Aşağıdaki komutta kullanılmak üzere dağıtımdan nesne KIMLIĞINI edinin.</span><span class="sxs-lookup"><span data-stu-id="db623-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="db623-205">Nesne KIMLIĞI, **Identity** App Service panelindeki Azure Portal gösterilir.</span><span class="sxs-lookup"><span data-stu-id="db623-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="db623-206">Azure CLı ve uygulamanın nesne KIMLIĞINI kullanarak, uygulama `list` ve `get` anahtar kasasına erişim izinleri sağlayın:</span><span class="sxs-lookup"><span data-stu-id="db623-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="db623-207">Azure CLı, PowerShell veya Azure portal kullanarak **uygulamayı yeniden başlatın** .</span><span class="sxs-lookup"><span data-stu-id="db623-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="db623-208">Örnek uygulama:</span><span class="sxs-lookup"><span data-stu-id="db623-208">The sample app:</span></span>

* <span data-ttu-id="db623-209">, Sınıfının bir örneğini oluşturur `DefaultAzureCredential` , kimlik bilgileri Azure kaynakları için ortamdan bir erişim belirteci almaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="db623-209">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="db623-210">Örnekle yeni bir [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) oluşturulur `DefaultAzureCredential` .</span><span class="sxs-lookup"><span data-stu-id="db623-210">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="db623-211">`Azure.Security.KeyVault.Secrets.Secrets`Örnek, `Azure.Extensions.Aspnetcore.Configuration.Secrets` tüm gizli değerleri yükleyen ve çift tire ( `--` ) değerini anahtar adlarında iki nokta () ile değiştirir `:` .</span><span class="sxs-lookup"><span data-stu-id="db623-211">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="db623-212">Anahtar Kasası adı örnek değeri: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="db623-212">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="db623-213">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="db623-213">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="db623-214">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="db623-214">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="db623-215">Geliştirme ortamında, gizli değerler, `_dev` Kullanıcı gizli dizileri tarafından sağlandıklarından, soneke sahiptir.</span><span class="sxs-lookup"><span data-stu-id="db623-215">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="db623-216">Üretim ortamında, değerler `_prod` Azure Key Vault tarafından sağlandıklarından sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="db623-216">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="db623-217">Bir `Access denied` hata alırsanız, uygulamanın Azure AD 'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladıklarını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="db623-217">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="db623-218">Azure 'da hizmeti yeniden başlattığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="db623-218">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="db623-219">Sağlayıcıyı yönetilen bir kimlik ve bir Azure DevOps işlem hattı ile kullanma hakkında bilgi için bkz. [yönetilen hizmet KIMLIĞIYLE VM 'ye Azure Resource Manager hizmet bağlantısı oluşturma](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="db623-219">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="db623-220">Yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="db623-220">Configuration options</span></span>

<span data-ttu-id="db623-221">Addadzurekeykasası bir AzureKeyVaultConfigurationOptions kabul edebilir:</span><span class="sxs-lookup"><span data-stu-id="db623-221">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="db623-222">Özellik</span><span class="sxs-lookup"><span data-stu-id="db623-222">Property</span></span>         | <span data-ttu-id="db623-223">Açıklama</span><span class="sxs-lookup"><span data-stu-id="db623-223">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="db623-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` gizli dizi yüklemeyi denetlemek için kullanılan örnek.</span><span class="sxs-lookup"><span data-stu-id="db623-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="db623-225">`Timespan` anahtar kasasındaki değişiklikleri yoklamaya yönelik denemeler arasında bekleme.</span><span class="sxs-lookup"><span data-stu-id="db623-225">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="db623-226">Varsayılan değer `null` (yapılandırma yeniden yüklenmez).</span><span class="sxs-lookup"><span data-stu-id="db623-226">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="db623-227">Anahtar adı öneki kullanın</span><span class="sxs-lookup"><span data-stu-id="db623-227">Use a key name prefix</span></span>

<span data-ttu-id="db623-228">Addavzurekeykasası `Azure.Extensions.Aspnetcore.Configuration.Secrets` , Anahtar Kasası gizli dizilerini yapılandırma anahtarlarına nasıl dönüştürdüğünü denetlemenize olanak tanıyan, uygulamasını kabul eden bir aşırı yükleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="db623-228">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="db623-229">Örneğin, uygulama başlangıcında sağladığınız önek değerine göre gizli değerleri yüklemek için arabirimini uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="db623-229">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="db623-230">Bu, örneğin, uygulama sürümüne göre gizli dizileri yüklemeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="db623-230">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="db623-231">Birden çok uygulama için gizli dizileri aynı kasaya yerleştirmek veya çevresel gizli dizileri (örneğin, *geliştirme* ve *Üretim* gizlilikleri) aynı kasaya yerleştirmek için Anahtar Kasası gizli dizileri üzerinde ön ekleri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="db623-231">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="db623-232">Farklı uygulama ve geliştirme/üretim ortamlarının, uygulama ortamlarını en yüksek düzeyde güvenlik için yalıtmak üzere ayrı anahtar kasaları kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="db623-232">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="db623-233">Aşağıdaki örnekte, için anahtar kasasında (ve geliştirme ortamı için gizli Yönetim Aracı kullanılarak) bir gizli dizi oluşturulur `5000-AppSecret` (Anahtar Kasası gizli adlarında dönemlere izin verilmez).</span><span class="sxs-lookup"><span data-stu-id="db623-233">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="db623-234">Bu gizli anahtar, uygulamanın 5.0.0.0 sürümü için bir uygulama gizli anahtarı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="db623-234">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="db623-235">Uygulamanın başka bir sürümü olan 5.1.0.0, anahtar kasasına (ve gizli Yönetici Aracı kullanılarak) bir gizli dizi eklenir `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="db623-235">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="db623-236">Her bir uygulama sürümü sürümü sürümlü gizli değerini yapılandırma olarak yükler `AppSecret` , bu, gizli anahtarı yüklerken sürümü de kapatıyor.</span><span class="sxs-lookup"><span data-stu-id="db623-236">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="db623-237">Addadzurekeykasası özel bir ile çağırılır `Azure.Extensions.Aspnetcore.Configuration.Secrets` :</span><span class="sxs-lookup"><span data-stu-id="db623-237">AddAzureKeyVault is called with a custom `Azure.Extensions.Aspnetcore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="db623-238">`Azure.Extensions.Aspnetcore.Configuration.Secrets`Uygulama, doğru gizli anahtarı yapılandırmaya yüklemek için gizli dizi sürüm öneklerine tepki verir:</span><span class="sxs-lookup"><span data-stu-id="db623-238">The `Azure.Extensions.Aspnetcore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="db623-239">`Load` adı önekiyle başladığında bir gizli dizi yükler.</span><span class="sxs-lookup"><span data-stu-id="db623-239">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="db623-240">Diğer gizlilikler yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="db623-240">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="db623-241">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="db623-241">`GetKey`:</span></span>
  * <span data-ttu-id="db623-242">Gizli dizi adından öneki kaldırır.</span><span class="sxs-lookup"><span data-stu-id="db623-242">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="db623-243">`KeyDelimiter`Yapılandırmada kullanılan sınırlayıcı olan (genellikle iki nokta üst üste), herhangi bir ada sahip iki kısa çizgi koyar.</span><span class="sxs-lookup"><span data-stu-id="db623-243">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="db623-244">Azure Key Vault gizli dizi adlarında bir iki nokta üst üste izin vermez.</span><span class="sxs-lookup"><span data-stu-id="db623-244">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="db623-245">`Load`Yöntemi, sürüm ön ekine sahip olanları bulmak için kasa gizli dizileri aracılığıyla yinelenen bir sağlayıcı algoritması tarafından çağırılır.</span><span class="sxs-lookup"><span data-stu-id="db623-245">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="db623-246">İle bir sürüm ön eki bulunduğunda `Load` , algoritma, `GetKey` gizli anahtar adının yapılandırma adını döndürmek için yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="db623-246">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="db623-247">Gizlilik adından sürüm önekini kaldırır ve uygulamanın yapılandırma adı-değer çiftlerine yüklemek için gizli dizi adının geri kalanını döndürür.</span><span class="sxs-lookup"><span data-stu-id="db623-247">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="db623-248">Bu yaklaşım uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="db623-248">When this approach is implemented:</span></span>

1. <span data-ttu-id="db623-249">Uygulamanın proje dosyasında belirtilen sürümü.</span><span class="sxs-lookup"><span data-stu-id="db623-249">The app's version specified in the app's project file.</span></span> <span data-ttu-id="db623-250">Aşağıdaki örnekte, uygulamanın sürümü olarak ayarlanır `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="db623-250">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="db623-251">`<UserSecretsId>`Uygulamanın proje dosyasında bir özelliğin var olduğunu, burada Kullanıcı tarafından sağlanan BIR GUID olduğunu onaylayın `{GUID}` :</span><span class="sxs-lookup"><span data-stu-id="db623-251">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="db623-252">[Gizli dizi yöneticisi aracıyla](xref:security/app-secrets)aşağıdaki gizli dizileri yerel olarak kaydedin:</span><span class="sxs-lookup"><span data-stu-id="db623-252">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="db623-253">Gizlilikler, aşağıdaki Azure CLı komutları kullanılarak Azure Key Vault kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="db623-253">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="db623-254">Uygulama çalıştırıldığında, Anahtar Kasası gizli dizileri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="db623-254">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="db623-255">İçin dize gizli dizisi, uygulamanın `5000-AppSecret` Proje dosyasında () belirtilen uygulamanın sürümüyle eşleştirilir `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="db623-255">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="db623-256">Sürüm `5000` (tireyle birlikte), anahtar adından çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="db623-256">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="db623-257">Uygulamanın tamamında, anahtarla yapılandırmayı okumak `AppSecret` gizli değeri yükler.</span><span class="sxs-lookup"><span data-stu-id="db623-257">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="db623-258">Uygulamanın sürümü proje dosyasında olarak değiştirilirse `5.1.0.0` ve uygulama yeniden çalıştırıldığında, döndürülen gizli değer `5.1.0.0_secret_value_dev` geliştirme ortamında ve `5.1.0.0_secret_value_prod` üretimde bulunur.</span><span class="sxs-lookup"><span data-stu-id="db623-258">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="db623-259"><xref:Azure.Security.KeyVault.Secrets.SecretClient>Addavzurekeykasası için kendi uygulamanızı da sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="db623-259">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="db623-260">Özel istemci, uygulama genelinde istemcinin tek bir örneğini paylaşıma izin verir.</span><span class="sxs-lookup"><span data-stu-id="db623-260">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="db623-261">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="db623-261">Bind an array to a class</span></span>

<span data-ttu-id="db623-262">Sağlayıcı, bir POCO dizisine bağlamak için yapılandırma değerlerini bir diziye okuyabilme özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="db623-262">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="db623-263">Anahtarların iki nokta () ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken `:` , bir diziyi oluşturan anahtarları ( `:0:` ,,) ayırt etmek için bir sayısal anahtar kesimi kullanılır `:1:` &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="db623-263">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="db623-264">Daha fazla bilgi için bkz. [yapılandırma: diziyi bir sınıfa bağlama](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="db623-264">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="db623-265">Azure Key Vault anahtarlar ayırıcı olarak iki nokta üst üste kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="db623-265">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="db623-266">Bu konuda açıklanan yaklaşım, `--` hiyerarşik değerler (bölümler) için bir ayırıcı olarak çift tire () kullanır.</span><span class="sxs-lookup"><span data-stu-id="db623-266">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="db623-267">Dizi anahtarları çift tireler ve sayısal anahtar kesimleri ( `--0--` ,,) ile birlikte Azure Key Vault `--1--` depolanır &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="db623-267">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="db623-268">Bir JSON dosyası tarafından sunulan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="db623-268">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="db623-269">`WriteTo`Dizide günlüğe kaydetme hedeflerini açıklayan Iki Serilog girişi yansıtan iki nesne değişmez değeri *sinks* vardır:</span><span class="sxs-lookup"><span data-stu-id="db623-269">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="db623-270">Önceki JSON dosyasında gösterilen yapılandırma, Çift tire ( `--` ) gösterimi ve sayısal kesimleri kullanarak Azure Key Vault depolanır:</span><span class="sxs-lookup"><span data-stu-id="db623-270">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="db623-271">Anahtar</span><span class="sxs-lookup"><span data-stu-id="db623-271">Key</span></span> | <span data-ttu-id="db623-272">Değer</span><span class="sxs-lookup"><span data-stu-id="db623-272">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="db623-273">Gizli dizileri yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="db623-273">Reload secrets</span></span>

<span data-ttu-id="db623-274">Gizli diziler çağrılana kadar önbelleğe alınır `IConfigurationRoot.Reload()` .</span><span class="sxs-lookup"><span data-stu-id="db623-274">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="db623-275">Anahtar kasasındaki zaman aşımına uğradı, devre dışı ve güncelleştirilmiş gizli dizileri, yürütülene kadar uygulama tarafından dikkate alınmıyor `Reload` .</span><span class="sxs-lookup"><span data-stu-id="db623-275">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="db623-276">Devre dışı ve süre dolma parolaları</span><span class="sxs-lookup"><span data-stu-id="db623-276">Disabled and expired secrets</span></span>

<span data-ttu-id="db623-277">Devre dışı ve son kullanma parolası bir oluşturur <xref:Azure.RequestFailedException> .</span><span class="sxs-lookup"><span data-stu-id="db623-277">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="db623-278">Uygulamanın üretilmesini engellemek için, farklı bir yapılandırma sağlayıcısı kullanarak yapılandırmayı sağlayın veya devre dışı ya da süre dolma parolasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="db623-278">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="db623-279">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="db623-279">Troubleshoot</span></span>

<span data-ttu-id="db623-280">Uygulama, sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Core günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır.</span><span class="sxs-lookup"><span data-stu-id="db623-280">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="db623-281">Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:</span><span class="sxs-lookup"><span data-stu-id="db623-281">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="db623-282">Uygulama veya sertifika Azure Active Directory içinde doğru yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="db623-282">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="db623-283">Anahtar Kasası Azure Key Vault içinde yok.</span><span class="sxs-lookup"><span data-stu-id="db623-283">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="db623-284">Uygulamanın anahtar kasasına erişme yetkisi yok.</span><span class="sxs-lookup"><span data-stu-id="db623-284">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="db623-285">Erişim ilkesi `Get` ve `List` izinleri içermez.</span><span class="sxs-lookup"><span data-stu-id="db623-285">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="db623-286">Anahtar kasasında yapılandırma verileri (ad-değer çifti) yanlış olarak adlandırılmış, eksik, devre dışı veya zaman aşımına uğradı.</span><span class="sxs-lookup"><span data-stu-id="db623-286">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="db623-287">Uygulamanın Anahtar Kasası adı ( `KeyVaultName` ), Azure AD uygulama kimliği () veya Azure AD `AzureADApplicationId` sertifika parmak izi () ya da Azure AD `AzureADCertThumbprint` directoryıd () yanlış `AzureADDirectoryId` .</span><span class="sxs-lookup"><span data-stu-id="db623-287">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="db623-288">Yüklemeye çalıştığınız değer için uygulamada yapılandırma anahtarı (ad) yanlış.</span><span class="sxs-lookup"><span data-stu-id="db623-288">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="db623-289">Uygulama için erişim ilkesini anahtar kasasına eklerken, ilke oluşturulmuştur, ancak **erişim ilkeleri** Kullanıcı arabiriminde **Kaydet** düğmesi seçilmedi.</span><span class="sxs-lookup"><span data-stu-id="db623-289">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db623-290">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="db623-290">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="db623-291">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="db623-291">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="db623-292">Microsoft Azure: Key Vault belgeler</span><span class="sxs-lookup"><span data-stu-id="db623-292">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="db623-293">Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma</span><span class="sxs-lookup"><span data-stu-id="db623-293">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="db623-294">KeyVaultClient sınıfı</span><span class="sxs-lookup"><span data-stu-id="db623-294">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="db623-295">Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma</span><span class="sxs-lookup"><span data-stu-id="db623-295">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="db623-296">Öğretici: .NET 'te Azure Windows sanal makinesi ile Azure Key Vault kullanma</span><span class="sxs-lookup"><span data-stu-id="db623-296">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="db623-297">Bu belgede, Azure Key Vault gizliliklerden uygulama yapılandırma değerlerini yüklemek için [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) yapılandırma sağlayıcısının nasıl kullanılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="db623-297">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="db623-298">Azure Key Vault, uygulama ve hizmetler tarafından kullanılan şifreleme anahtarlarını ve gizli dizileri koruma konusunda yardımcı olan bulut tabanlı bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="db623-298">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="db623-299">ASP.NET Core uygulamalarla Azure Key Vault kullanmaya yönelik yaygın senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="db623-299">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="db623-300">Hassas yapılandırma verilerine erişimi denetleme.</span><span class="sxs-lookup"><span data-stu-id="db623-300">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="db623-301">Yapılandırma verilerini depolarken FIPS 140-2 düzey 2 doğrulanan donanım güvenlik modülleri (HSM 'ler) gereksinimini karşılarsınız.</span><span class="sxs-lookup"><span data-stu-id="db623-301">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="db623-302">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db623-302">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="db623-303">Paketler</span><span class="sxs-lookup"><span data-stu-id="db623-303">Packages</span></span>

<span data-ttu-id="db623-304">Microsoft.Extensions.Configyönlendirmeye bir paket başvurusu ekleyin [ . AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) paketi.</span><span class="sxs-lookup"><span data-stu-id="db623-304">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="db623-305">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="db623-305">Sample app</span></span>

<span data-ttu-id="db623-306">Örnek uygulama, `#define` *program.cs* dosyasının en üstünde yer aldığı ifadeye göre belirlenen iki moddan birinde çalışır:</span><span class="sxs-lookup"><span data-stu-id="db623-306">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="db623-307">`Certificate`: Azure Key Vault depolanan gizli dizileri erişmek için Azure Key Vault Istemci KIMLIĞI ve X. 509.952 sertifikası kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="db623-307">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="db623-308">Örneğin bu sürümü, Azure App Service dağıtılan herhangi bir konumdan veya ASP.NET Core uygulamasına hizmet veren herhangi bir konağa çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="db623-308">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="db623-309">`Managed`: Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasıyla Azure Key Vault üzere uygulamanın kimliğini doğrulamak için [Azure kaynakları Için yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="db623-309">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="db623-310">Kimlik doğrulaması için Yönetilen kimlikler kullanıldığında, bir Azure AD uygulama KIMLIĞI ve parolası (gizli anahtar) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="db623-310">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="db623-311">`Managed`Örneğin sürümünün Azure 'a dağıtılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="db623-311">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="db623-312">[Azure kaynakları Için Yönetilen kimlikler bölümünü kullanma](#use-managed-identities-for-azure-resources) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="db623-312">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="db623-313">Önişlemci yönergeleri () kullanarak örnek bir uygulamanın nasıl yapılandırılacağı hakkında daha fazla bilgi için `#define` bkz <xref:index#preprocessor-directives-in-sample-code> ..</span><span class="sxs-lookup"><span data-stu-id="db623-313">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="db623-314">Geliştirme ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="db623-314">Secret storage in the Development environment</span></span>

<span data-ttu-id="db623-315">[Gizli dizi Yöneticisi aracını](xref:security/app-secrets)kullanarak gizli dizileri yerel olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="db623-315">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="db623-316">Örnek uygulama, geliştirme ortamındaki yerel makinede çalıştığında, gizlilikler yerel kullanıcı gizli dizileri deposundan yüklenir.</span><span class="sxs-lookup"><span data-stu-id="db623-316">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="db623-317">Gizli dizi Yöneticisi Aracı, `<UserSecretsId>` uygulamanın proje dosyasında bir özellik gerektirir.</span><span class="sxs-lookup"><span data-stu-id="db623-317">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="db623-318">Özellik değerini ( `{GUID}` ) herhangi bir BENZERSIZ GUID 'ye ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="db623-318">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="db623-319">Gizlilikler ad-değer çiftleri olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="db623-319">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="db623-320">Hiyerarşik değerler (yapılandırma bölümleri) `:` [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index) anahtarı adlarında ayırıcı olarak bir (iki nokta üst üste) kullanır.</span><span class="sxs-lookup"><span data-stu-id="db623-320">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="db623-321">Gizli dizi, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır; burada `{SECRET NAME}` ad ve `{SECRET VALUE}` değerdir:</span><span class="sxs-lookup"><span data-stu-id="db623-321">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="db623-322">Örnek uygulamanın gizli dizilerini ayarlamak için, projenin [içerik kökünden](xref:fundamentals/index#content-root) bir komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="db623-322">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="db623-323">Bu gizlilikler, [Azure Key Vault bölümündeki üretim ortamındaki gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında Azure Key Vault depolandığında, `_dev` sonek olarak değiştirilir `_prod` .</span><span class="sxs-lookup"><span data-stu-id="db623-323">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="db623-324">Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren bir görsel ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="db623-324">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="db623-325">Azure Key Vault ile üretim ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="db623-325">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="db623-326">Hızlı başlangıç tarafından sunulan yönergeler [: Azure CLI kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-cli) , bir Azure Key Vault oluşturmak ve örnek uygulama tarafından kullanılan gizli dizileri depolamak için burada özetlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="db623-326">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="db623-327">Daha ayrıntılı bilgi için konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="db623-327">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="db623-328">[Azure Portal](https://portal.azure.com/)aşağıdaki yöntemlerden birini kullanarak Azure Cloud Shell 'i açın:</span><span class="sxs-lookup"><span data-stu-id="db623-328">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="db623-329">Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-329">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="db623-330">Metin kutusunda "Azure CLı" arama dizesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="db623-330">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="db623-331">**Cloud Shell Başlat** düğmesini kullanarak tarayıcınızda Cloud Shell açın.</span><span class="sxs-lookup"><span data-stu-id="db623-331">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="db623-332">Azure portal sağ üst köşesindeki menüdeki **Cloud Shell** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-332">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="db623-333">Daha fazla bilgi için bkz. [Azure CLI](/cli/azure/) ve [Azure Cloud Shell Genel Bakış](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="db623-333">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="db623-334">Henüz kimlik doğrulamasından geçirilmemişse komutuyla oturum açın `az login` .</span><span class="sxs-lookup"><span data-stu-id="db623-334">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="db623-335">Aşağıdaki komutla bir kaynak grubu oluşturun; burada, `{RESOURCE GROUP NAME}` Yeni kaynak grubu için kaynak grubu adı ve `{LOCATION}` Azure bölgesi (Datacenter) olur:</span><span class="sxs-lookup"><span data-stu-id="db623-335">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="db623-336">Aşağıdaki komutla kaynak grubunda bir Anahtar Kasası oluşturun; burada `{KEY VAULT NAME}` Yeni anahtar kasasının adı ve `{LOCATION}` Azure bölgesi (Datacenter) bulunur:</span><span class="sxs-lookup"><span data-stu-id="db623-336">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="db623-337">Anahtar kasasında ad-değer çiftleri olarak gizli diziler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="db623-337">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="db623-338">Azure Key Vault gizli dizi adları, alfasayısal karakterler ve tireler ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="db623-338">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="db623-339">Hiyerarşik değerler (yapılandırma bölümleri) `--` ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="db623-339">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="db623-340">Genellikle [ASP.NET Core yapılandırmasındaki](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlandırmak için kullanılan iki nokta üst üste, Anahtar Kasası gizli adlarında izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="db623-340">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="db623-341">Bu nedenle, gizlilikler uygulamanın yapılandırmasına yüklendiğinde iki tire kullanılır ve iki nokta üst üste bulunur.</span><span class="sxs-lookup"><span data-stu-id="db623-341">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="db623-342">Aşağıdaki gizlilikler örnek uygulamayla kullanım içindir.</span><span class="sxs-lookup"><span data-stu-id="db623-342">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="db623-343">Değerler, `_prod` `_dev` Kullanıcı parolalarından geliştirme ortamında yüklenen sonek değerlerinden ayırt etmek için bir sonek içerir.</span><span class="sxs-lookup"><span data-stu-id="db623-343">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="db623-344">`{KEY VAULT NAME}`Önceki adımda oluşturduğunuz anahtar kasasının adıyla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="db623-344">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="db623-345">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanın</span><span class="sxs-lookup"><span data-stu-id="db623-345">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="db623-346">**Uygulama Azure dışında barındırıldığı zaman** bir anahtar kasasında kimlik doğrulaması yapmak IÇIN Azure AD, Azure Key Vault ve uygulamayı bir Azure ACTIVE DIRECTORY uygulama kimliği ve X. 509.440 sertifikası kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="db623-346">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="db623-347">Daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="db623-347">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="db623-348">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanılması desteklenmekle birlikte, Azure 'da bir uygulama barındırılırken [Azure kaynakları Için Yönetilen kimlikler](#use-managed-identities-for-azure-resources) kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="db623-348">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="db623-349">Yönetilen kimlikler, uygulamada veya geliştirme ortamında bir sertifikanın depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="db623-349">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="db623-350">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki ifade olarak AYARLANDıĞıNDA bir uygulama kimliği ve X. 509.440 sertifikası kullanır `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="db623-350">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="db623-351">PKCS # 12 Arşivi (*. pfx*) sertifikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="db623-351">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="db623-352">Sertifika oluşturma seçenekleri Windows ve [OpenSSL](https://www.openssl.org/) [üzerinde MakeCert](/windows/desktop/seccrypto/makecert) içerir.</span><span class="sxs-lookup"><span data-stu-id="db623-352">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="db623-353">Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükler.</span><span class="sxs-lookup"><span data-stu-id="db623-353">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="db623-354">Anahtarı verilebilir olarak işaretlemek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="db623-354">Marking the key as exportable is optional.</span></span> <span data-ttu-id="db623-355">Sertifikanın daha sonra bu işlemde kullanılan parmak izini aklınızda bulunur.</span><span class="sxs-lookup"><span data-stu-id="db623-355">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="db623-356">PKCS # 12 Arşivi (*. pfx*) sertifikasını der kodlu bir sertifika (*. cer*) olarak dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="db623-356">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="db623-357">Uygulamayı Azure AD 'ye kaydedin (**uygulama kayıtları**).</span><span class="sxs-lookup"><span data-stu-id="db623-357">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="db623-358">DER kodlu sertifikayı (*. cer*) Azure AD 'ye yükleyin:</span><span class="sxs-lookup"><span data-stu-id="db623-358">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="db623-359">Azure AD 'de uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-359">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="db623-360">**Sertifikalar & gizli** dizi sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="db623-360">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="db623-361">Ortak anahtarı içeren sertifikayı karşıya yüklemek için **sertifikayı karşıya yükle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-361">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="db623-362">*. Cer*, *. pek* veya *. CRT* sertifikası kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="db623-362">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="db623-363">Anahtar Kasası adı, uygulama KIMLIĞI ve sertifika parmak izini uygulamanın *appsettings.json* dosyasına depolayın.</span><span class="sxs-lookup"><span data-stu-id="db623-363">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="db623-364">Azure portal **ana** kasaları ' ne gidin.</span><span class="sxs-lookup"><span data-stu-id="db623-364">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="db623-365">Azure Key Vault bölümünde, [üretim ortamındaki gizli dizi deposunda](#secret-storage-in-the-production-environment-with-azure-key-vault) oluşturduğunuz anahtar kasasını seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-365">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="db623-366">**Erişim ilkeleri**' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-366">Select **Access policies**.</span></span>
1. <span data-ttu-id="db623-367">**Erişim Ilkesi Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-367">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="db623-368">**Gizli izinleri** açın ve uygulamaya **Get** ve **list** izinleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="db623-368">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="db623-369">**Sorumlu Seç** ' i seçin ve kayıtlı uygulamayı ada göre seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-369">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="db623-370">**Seç** düğmesini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="db623-370">Select the **Select** button.</span></span>
1. <span data-ttu-id="db623-371">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-371">Select **OK**.</span></span>
1. <span data-ttu-id="db623-372">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="db623-372">Select **Save**.</span></span>
1. <span data-ttu-id="db623-373">Uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="db623-373">Deploy the app.</span></span>

<span data-ttu-id="db623-374">`Certificate`Örnek uygulama, yapılandırma değerlerini, `IConfigurationRoot` parola adı ile aynı adla alır:</span><span class="sxs-lookup"><span data-stu-id="db623-374">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="db623-375">Hiyerarşik olmayan değerler: için değeri `SecretName` ile elde edilir `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="db623-375">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="db623-376">Hiyerarşik değerler (bölümler): `:` (iki nokta üst üste) gösterimini veya `GetSection` genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="db623-376">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="db623-377">Yapılandırma değerini elde etmek için şu yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="db623-377">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="db623-378">X. 509.440 sertifikası işletim sistemi tarafından yönetiliyor.</span><span class="sxs-lookup"><span data-stu-id="db623-378">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="db623-379">Uygulama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> , dosya tarafından sağlanan değerlerle çağırır *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="db623-379">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="db623-380">Örnek değerler:</span><span class="sxs-lookup"><span data-stu-id="db623-380">Example values:</span></span>

* <span data-ttu-id="db623-381">Anahtar Kasası adı: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="db623-381">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="db623-382">Uygulama KIMLIĞI: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="db623-382">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="db623-383">Sertifika parmak izi: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="db623-383">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="db623-384">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="db623-384">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="db623-385">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="db623-385">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="db623-386">Geliştirme ortamında, gizli anahtar değerleri sonek ile yüklenir `_dev` .</span><span class="sxs-lookup"><span data-stu-id="db623-386">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="db623-387">Üretim ortamında, değerler sonek ile yüklenir `_prod` .</span><span class="sxs-lookup"><span data-stu-id="db623-387">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="db623-388">Azure kaynakları için Yönetilen kimlikler kullanma</span><span class="sxs-lookup"><span data-stu-id="db623-388">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="db623-389">Azure **'a dağıtılan bir uygulama** , [Azure kaynakları için yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanarak uygulamanın, KIMLIK bilgileri (uygulama kimliği ve parola/istemci gizli anahtarı) olmadan Azure AD kimlik doğrulaması kullanarak Azure Key Vault kimlik doğrulamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="db623-389">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="db623-390">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki Ifade olarak ayarlandığında Azure kaynakları için Yönetilen kimlikler kullanır `Managed` .</span><span class="sxs-lookup"><span data-stu-id="db623-390">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="db623-391">Kasa adını uygulamanın *appsettings.json* dosyasına girin.</span><span class="sxs-lookup"><span data-stu-id="db623-391">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="db623-392">Örnek uygulama sürüme ayarlandığında bir uygulama KIMLIĞI ve parola (Istemci gizli anahtarı) gerektirmez `Managed` , bu nedenle bu yapılandırma girişlerini yoksayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="db623-392">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="db623-393">Uygulama Azure 'a dağıtılır ve Azure, yalnızca dosyada depolanan kasa adını kullanarak Azure Key Vault erişmek için uygulamanın kimliğini doğrular *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="db623-393">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="db623-394">Azure App Service için örnek uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="db623-394">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="db623-395">Azure App Service dağıtılan bir uygulama, hizmet oluşturulduğunda Azure AD 'ye otomatik olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="db623-395">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="db623-396">Aşağıdaki komutta kullanılmak üzere dağıtımdan nesne KIMLIĞINI edinin.</span><span class="sxs-lookup"><span data-stu-id="db623-396">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="db623-397">Nesne KIMLIĞI, **Identity** App Service panelindeki Azure Portal gösterilir.</span><span class="sxs-lookup"><span data-stu-id="db623-397">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="db623-398">Azure CLı ve uygulamanın nesne KIMLIĞINI kullanarak, uygulama `list` ve `get` anahtar kasasına erişim izinleri sağlayın:</span><span class="sxs-lookup"><span data-stu-id="db623-398">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="db623-399">Azure CLı, PowerShell veya Azure portal kullanarak **uygulamayı yeniden başlatın** .</span><span class="sxs-lookup"><span data-stu-id="db623-399">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="db623-400">Örnek uygulama:</span><span class="sxs-lookup"><span data-stu-id="db623-400">The sample app:</span></span>

* <span data-ttu-id="db623-401">`AzureServiceTokenProvider`Bir bağlantı dizesi olmadan sınıfın bir örneğini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="db623-401">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="db623-402">Bir bağlantı dizesi sağlanmazsa, sağlayıcı Azure kaynakları için yönetilen kimliklerden bir erişim belirteci almaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="db623-402">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="db623-403"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> `AzureServiceTokenProvider` Örnek belirteci geri çağırması ile yeni bir oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="db623-403">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="db623-404"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Örnek, <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> tüm gizli değerleri yükleyen ve çift tire ( `--` ) değerini anahtar adlarında iki nokta () ile değiştirir `:` .</span><span class="sxs-lookup"><span data-stu-id="db623-404">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="db623-405">Anahtar Kasası adı örnek değeri: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="db623-405">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="db623-406">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="db623-406">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="db623-407">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="db623-407">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="db623-408">Geliştirme ortamında, gizli değerler, `_dev` Kullanıcı gizli dizileri tarafından sağlandıklarından, soneke sahiptir.</span><span class="sxs-lookup"><span data-stu-id="db623-408">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="db623-409">Üretim ortamında, değerler `_prod` Azure Key Vault tarafından sağlandıklarından sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="db623-409">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="db623-410">Bir `Access denied` hata alırsanız, uygulamanın Azure AD 'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladıklarını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="db623-410">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="db623-411">Azure 'da hizmeti yeniden başlattığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="db623-411">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="db623-412">Sağlayıcıyı yönetilen bir kimlik ve bir Azure DevOps işlem hattı ile kullanma hakkında bilgi için bkz. [yönetilen hizmet KIMLIĞIYLE VM 'ye Azure Resource Manager hizmet bağlantısı oluşturma](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="db623-412">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="db623-413">Anahtar adı öneki kullanın</span><span class="sxs-lookup"><span data-stu-id="db623-413">Use a key name prefix</span></span>

<span data-ttu-id="db623-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, Anahtar Kasası gizli dizilerini yapılandırma anahtarlarına nasıl dönüştürdüğünü denetlemenize olanak tanıyan, uygulamasını kabul eden bir aşırı yükleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="db623-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="db623-415">Örneğin, uygulama başlangıcında sağladığınız önek değerine göre gizli değerleri yüklemek için arabirimini uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="db623-415">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="db623-416">Bu, örneğin, uygulama sürümüne göre gizli dizileri yüklemeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="db623-416">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="db623-417">Birden çok uygulama için gizli dizileri aynı kasaya yerleştirmek veya çevresel gizli dizileri (örneğin, *geliştirme* ve *Üretim* gizlilikleri) aynı kasaya yerleştirmek için Anahtar Kasası gizli dizileri üzerinde ön ekleri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="db623-417">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="db623-418">Farklı uygulama ve geliştirme/üretim ortamlarının, uygulama ortamlarını en yüksek düzeyde güvenlik için yalıtmak üzere ayrı anahtar kasaları kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="db623-418">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="db623-419">Aşağıdaki örnekte, için anahtar kasasında (ve geliştirme ortamı için gizli Yönetim Aracı kullanılarak) bir gizli dizi oluşturulur `5000-AppSecret` (Anahtar Kasası gizli adlarında dönemlere izin verilmez).</span><span class="sxs-lookup"><span data-stu-id="db623-419">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="db623-420">Bu gizli anahtar, uygulamanın 5.0.0.0 sürümü için bir uygulama gizli anahtarı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="db623-420">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="db623-421">Uygulamanın başka bir sürümü olan 5.1.0.0, anahtar kasasına (ve gizli Yönetici Aracı kullanılarak) bir gizli dizi eklenir `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="db623-421">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="db623-422">Her bir uygulama sürümü sürümü sürümlü gizli değerini yapılandırma olarak yükler `AppSecret` , bu, gizli anahtarı yüklerken sürümü de kapatıyor.</span><span class="sxs-lookup"><span data-stu-id="db623-422">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="db623-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> özel bir ile çağrılır <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="db623-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="db623-424"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Uygulama, doğru gizli anahtarı yapılandırmaya yüklemek için gizli dizi sürüm öneklerine tepki verir:</span><span class="sxs-lookup"><span data-stu-id="db623-424">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="db623-425">`Load` adı önekiyle başladığında bir gizli dizi yükler.</span><span class="sxs-lookup"><span data-stu-id="db623-425">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="db623-426">Diğer gizlilikler yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="db623-426">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="db623-427">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="db623-427">`GetKey`:</span></span>
  * <span data-ttu-id="db623-428">Gizli dizi adından öneki kaldırır.</span><span class="sxs-lookup"><span data-stu-id="db623-428">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="db623-429">`KeyDelimiter`Yapılandırmada kullanılan sınırlayıcı olan (genellikle iki nokta üst üste), herhangi bir ada sahip iki kısa çizgi koyar.</span><span class="sxs-lookup"><span data-stu-id="db623-429">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="db623-430">Azure Key Vault gizli dizi adlarında bir iki nokta üst üste izin vermez.</span><span class="sxs-lookup"><span data-stu-id="db623-430">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="db623-431">`Load`Yöntemi, sürüm ön ekine sahip olanları bulmak için kasa gizli dizileri aracılığıyla yinelenen bir sağlayıcı algoritması tarafından çağırılır.</span><span class="sxs-lookup"><span data-stu-id="db623-431">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="db623-432">İle bir sürüm ön eki bulunduğunda `Load` , algoritma, `GetKey` gizli anahtar adının yapılandırma adını döndürmek için yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="db623-432">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="db623-433">Gizlilik adından sürüm önekini kaldırır ve uygulamanın yapılandırma adı-değer çiftlerine yüklemek için gizli dizi adının geri kalanını döndürür.</span><span class="sxs-lookup"><span data-stu-id="db623-433">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="db623-434">Bu yaklaşım uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="db623-434">When this approach is implemented:</span></span>

1. <span data-ttu-id="db623-435">Uygulamanın proje dosyasında belirtilen sürümü.</span><span class="sxs-lookup"><span data-stu-id="db623-435">The app's version specified in the app's project file.</span></span> <span data-ttu-id="db623-436">Aşağıdaki örnekte, uygulamanın sürümü olarak ayarlanır `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="db623-436">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="db623-437">`<UserSecretsId>`Uygulamanın proje dosyasında bir özelliğin var olduğunu, burada Kullanıcı tarafından sağlanan BIR GUID olduğunu onaylayın `{GUID}` :</span><span class="sxs-lookup"><span data-stu-id="db623-437">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="db623-438">[Gizli dizi yöneticisi aracıyla](xref:security/app-secrets)aşağıdaki gizli dizileri yerel olarak kaydedin:</span><span class="sxs-lookup"><span data-stu-id="db623-438">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="db623-439">Gizlilikler, aşağıdaki Azure CLı komutları kullanılarak Azure Key Vault kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="db623-439">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="db623-440">Uygulama çalıştırıldığında, Anahtar Kasası gizli dizileri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="db623-440">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="db623-441">İçin dize gizli dizisi, uygulamanın `5000-AppSecret` Proje dosyasında () belirtilen uygulamanın sürümüyle eşleştirilir `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="db623-441">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="db623-442">Sürüm `5000` (tireyle birlikte), anahtar adından çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="db623-442">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="db623-443">Uygulamanın tamamında, anahtarla yapılandırmayı okumak `AppSecret` gizli değeri yükler.</span><span class="sxs-lookup"><span data-stu-id="db623-443">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="db623-444">Uygulamanın sürümü proje dosyasında olarak değiştirilirse `5.1.0.0` ve uygulama yeniden çalıştırıldığında, döndürülen gizli değer `5.1.0.0_secret_value_dev` geliştirme ortamında ve `5.1.0.0_secret_value_prod` üretimde bulunur.</span><span class="sxs-lookup"><span data-stu-id="db623-444">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="db623-445">Ayrıca, kendi uygulamanızı da sağlayabilirsiniz <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="db623-445">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="db623-446">Özel istemci, uygulama genelinde istemcinin tek bir örneğini paylaşıma izin verir.</span><span class="sxs-lookup"><span data-stu-id="db623-446">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="db623-447">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="db623-447">Bind an array to a class</span></span>

<span data-ttu-id="db623-448">Sağlayıcı, bir POCO dizisine bağlamak için yapılandırma değerlerini bir diziye okuyabilme özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="db623-448">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="db623-449">Anahtarların iki nokta () ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken `:` , bir diziyi oluşturan anahtarları ( `:0:` ,,) ayırt etmek için bir sayısal anahtar kesimi kullanılır `:1:` &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="db623-449">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="db623-450">Daha fazla bilgi için bkz. [yapılandırma: diziyi bir sınıfa bağlama](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="db623-450">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="db623-451">Azure Key Vault anahtarlar ayırıcı olarak iki nokta üst üste kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="db623-451">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="db623-452">Bu konuda açıklanan yaklaşım, `--` hiyerarşik değerler (bölümler) için bir ayırıcı olarak çift tire () kullanır.</span><span class="sxs-lookup"><span data-stu-id="db623-452">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="db623-453">Dizi anahtarları çift tireler ve sayısal anahtar kesimleri ( `--0--` ,,) ile birlikte Azure Key Vault `--1--` depolanır &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="db623-453">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="db623-454">Bir JSON dosyası tarafından sunulan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="db623-454">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="db623-455">`WriteTo`Dizide günlüğe kaydetme hedeflerini açıklayan Iki Serilog girişi yansıtan iki nesne değişmez değeri *sinks* vardır:</span><span class="sxs-lookup"><span data-stu-id="db623-455">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="db623-456">Önceki JSON dosyasında gösterilen yapılandırma, Çift tire ( `--` ) gösterimi ve sayısal kesimleri kullanarak Azure Key Vault depolanır:</span><span class="sxs-lookup"><span data-stu-id="db623-456">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="db623-457">Anahtar</span><span class="sxs-lookup"><span data-stu-id="db623-457">Key</span></span> | <span data-ttu-id="db623-458">Değer</span><span class="sxs-lookup"><span data-stu-id="db623-458">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="db623-459">Gizli dizileri yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="db623-459">Reload secrets</span></span>

<span data-ttu-id="db623-460">Gizli diziler çağrılana kadar önbelleğe alınır `IConfigurationRoot.Reload()` .</span><span class="sxs-lookup"><span data-stu-id="db623-460">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="db623-461">Anahtar kasasındaki zaman aşımına uğradı, devre dışı ve güncelleştirilmiş gizli dizileri, yürütülene kadar uygulama tarafından dikkate alınmıyor `Reload` .</span><span class="sxs-lookup"><span data-stu-id="db623-461">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="db623-462">Devre dışı ve süre dolma parolaları</span><span class="sxs-lookup"><span data-stu-id="db623-462">Disabled and expired secrets</span></span>

<span data-ttu-id="db623-463">Devre dışı ve son kullanma parolası bir oluşturur <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="db623-463">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="db623-464">Uygulamanın üretilmesini engellemek için, farklı bir yapılandırma sağlayıcısı kullanarak yapılandırmayı sağlayın veya devre dışı ya da süre dolma parolasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="db623-464">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="db623-465">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="db623-465">Troubleshoot</span></span>

<span data-ttu-id="db623-466">Uygulama, sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Core günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır.</span><span class="sxs-lookup"><span data-stu-id="db623-466">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="db623-467">Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:</span><span class="sxs-lookup"><span data-stu-id="db623-467">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="db623-468">Uygulama veya sertifika Azure Active Directory içinde doğru yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="db623-468">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="db623-469">Anahtar Kasası Azure Key Vault içinde yok.</span><span class="sxs-lookup"><span data-stu-id="db623-469">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="db623-470">Uygulamanın anahtar kasasına erişme yetkisi yok.</span><span class="sxs-lookup"><span data-stu-id="db623-470">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="db623-471">Erişim ilkesi `Get` ve `List` izinleri içermez.</span><span class="sxs-lookup"><span data-stu-id="db623-471">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="db623-472">Anahtar kasasında yapılandırma verileri (ad-değer çifti) yanlış olarak adlandırılmış, eksik, devre dışı veya zaman aşımına uğradı.</span><span class="sxs-lookup"><span data-stu-id="db623-472">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="db623-473">Uygulamanın Anahtar Kasası adı ( `KeyVaultName` ), Azure AD uygulama kimliği ( `AzureADApplicationId` ) veya Azure AD sertifika parmak izi ( `AzureADCertThumbprint` ) yanlış.</span><span class="sxs-lookup"><span data-stu-id="db623-473">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="db623-474">Yüklemeye çalıştığınız değer için uygulamada yapılandırma anahtarı (ad) yanlış.</span><span class="sxs-lookup"><span data-stu-id="db623-474">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="db623-475">Uygulama için erişim ilkesini anahtar kasasına eklerken, ilke oluşturulmuştur, ancak **erişim ilkeleri** Kullanıcı arabiriminde **Kaydet** düğmesi seçilmedi.</span><span class="sxs-lookup"><span data-stu-id="db623-475">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db623-476">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="db623-476">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="db623-477">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="db623-477">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="db623-478">Microsoft Azure: Key Vault belgeler</span><span class="sxs-lookup"><span data-stu-id="db623-478">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="db623-479">Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma</span><span class="sxs-lookup"><span data-stu-id="db623-479">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="db623-480">KeyVaultClient sınıfı</span><span class="sxs-lookup"><span data-stu-id="db623-480">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="db623-481">Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma</span><span class="sxs-lookup"><span data-stu-id="db623-481">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="db623-482">Öğretici: .NET 'te Azure Windows sanal makinesi ile Azure Key Vault kullanma</span><span class="sxs-lookup"><span data-stu-id="db623-482">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
