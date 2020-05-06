---
title: ASP.NET Core Azure Key Vault yapılandırma sağlayıcısı
author: rick-anderson
description: Çalışma zamanında yüklenen ad-değer çiftlerini kullanarak bir uygulamayı yapılandırmak için Azure Key Vault yapılandırma sağlayıcısını nasıl kullanacağınızı öğrenin.
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
uid: security/key-vault-configuration
ms.openlocfilehash: 62c8b58dfa0272b1edc48f7e8b41475970ffd492
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777390"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="3e5bf-103">ASP.NET Core Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="3e5bf-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="3e5bf-104">, [Andrew Stanton-nurte](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="3e5bf-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e5bf-105">Bu belgede, Azure Key Vault gizliliklerden uygulama yapılandırma değerlerini yüklemek için [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) yapılandırma sağlayıcısının nasıl kullanılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="3e5bf-106">Azure Key Vault, uygulama ve hizmetler tarafından kullanılan şifreleme anahtarlarını ve gizli dizileri koruma konusunda yardımcı olan bulut tabanlı bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="3e5bf-107">ASP.NET Core uygulamalarla Azure Key Vault kullanmaya yönelik yaygın senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="3e5bf-108">Hassas yapılandırma verilerine erişimi denetleme.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="3e5bf-109">Yapılandırma verilerini depolarken FIPS 140-2 düzey 2 doğrulanan donanım güvenlik modülleri (HSM 'ler) gereksinimini karşılarsınız.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="3e5bf-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3e5bf-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="3e5bf-111">Paketler</span><span class="sxs-lookup"><span data-stu-id="3e5bf-111">Packages</span></span>

<span data-ttu-id="3e5bf-112">[Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="3e5bf-113">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="3e5bf-113">Sample app</span></span>

<span data-ttu-id="3e5bf-114">Örnek uygulama, `#define` *program.cs* dosyasının en üstünde yer aldığı ifadeye göre belirlenen iki moddan birinde çalışır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="3e5bf-115">`Certificate`&ndash; Azure Key Vault depolanan gizli dizileri erişmek Için Azure Key Vault istemci kimliği ve X. 509.952 sertifikası kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="3e5bf-116">Örneğin bu sürümü, Azure App Service dağıtılan herhangi bir konumdan veya ASP.NET Core uygulamasına hizmet veren herhangi bir konağa çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="3e5bf-117">`Managed`&ndash; Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasıyla Azure Key Vault üzere uygulamanın kimliğini doğrulamak için [Azure kaynakları için yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="3e5bf-118">Kimlik doğrulaması için Yönetilen kimlikler kullanıldığında, bir Azure AD uygulama KIMLIĞI ve parolası (gizli anahtar) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="3e5bf-119">Örneğin `Managed` sürümünün Azure 'a dağıtılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="3e5bf-120">[Azure kaynakları Için Yönetilen kimlikler bölümünü kullanma](#use-managed-identities-for-azure-resources) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="3e5bf-121">Önişlemci yönergeleri (`#define`) kullanarak örnek bir uygulamanın nasıl yapılandırılacağı hakkında daha fazla bilgi için bkz <xref:index#preprocessor-directives-in-sample-code>..</span><span class="sxs-lookup"><span data-stu-id="3e5bf-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="3e5bf-122">Geliştirme ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="3e5bf-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="3e5bf-123">[Gizli dizi Yöneticisi aracını](xref:security/app-secrets)kullanarak gizli dizileri yerel olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="3e5bf-124">Örnek uygulama, geliştirme ortamındaki yerel makinede çalıştığında, gizli anahtar Yöneticisi deposundan gizli diziler yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="3e5bf-125">Gizli dizi Yöneticisi Aracı, uygulamanın `<UserSecretsId>` proje dosyasında bir özellik gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="3e5bf-126">Özellik değerini (`{GUID}`) herhangi BIR benzersiz GUID 'ye ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="3e5bf-127">Gizlilikler ad-değer çiftleri olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="3e5bf-128">Hiyerarşik değerler (yapılandırma bölümleri) `:` [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index) anahtarı adlarında ayırıcı olarak bir (iki nokta üst üste) kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="3e5bf-129">Gizli dizi, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır; burada `{SECRET NAME}` ad ve `{SECRET VALUE}` değerdir:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="3e5bf-130">Örnek uygulamanın gizli dizilerini ayarlamak için, projenin [içerik kökünden](xref:fundamentals/index#content-root) bir komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="3e5bf-131">Bu gizlilikler, [Azure Key Vault bölümündeki üretim ortamındaki gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında Azure Key Vault depolandığında, `_dev` sonek olarak `_prod`değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="3e5bf-132">Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren bir görsel ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="3e5bf-133">Azure Key Vault ile üretim ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="3e5bf-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="3e5bf-134">Hızlı başlangıç tarafından sunulan yönergeler [: Azure CLI kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-cli) , bir Azure Key Vault oluşturmak ve örnek uygulama tarafından kullanılan gizli dizileri depolamak için burada özetlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="3e5bf-135">Daha ayrıntılı bilgi için konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="3e5bf-136">[Azure Portal](https://portal.azure.com/)aşağıdaki yöntemlerden birini kullanarak Azure Cloud Shell 'i açın:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="3e5bf-137">Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="3e5bf-138">Metin kutusunda "Azure CLı" arama dizesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="3e5bf-139">**Cloud Shell Başlat** düğmesini kullanarak tarayıcınızda Cloud Shell açın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="3e5bf-140">Azure portal sağ üst köşesindeki menüdeki **Cloud Shell** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="3e5bf-141">Daha fazla bilgi için bkz. [Azure CLI](/cli/azure/) ve [Azure Cloud Shell Genel Bakış](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="3e5bf-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="3e5bf-142">Henüz kimlik doğrulamasından geçirilmemişse `az login` komutuyla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="3e5bf-143">Aşağıdaki komutla bir kaynak grubu oluşturun; burada `{RESOURCE GROUP NAME}` , yeni kaynak grubu için kaynak grubu adı ve `{LOCATION}` Azure bölgesi (Datacenter) olur:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="3e5bf-144">Aşağıdaki komutla kaynak grubunda bir Anahtar Kasası oluşturun; burada `{KEY VAULT NAME}` yeni anahtar kasasının adı ve `{LOCATION}` Azure bölgesi (Datacenter) bulunur:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="3e5bf-145">Anahtar kasasında ad-değer çiftleri olarak gizli diziler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="3e5bf-146">Azure Key Vault gizli dizi adları, alfasayısal karakterler ve tireler ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="3e5bf-147">Hiyerarşik değerler (yapılandırma bölümleri) ayırıcı `--` olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="3e5bf-148">Genellikle [ASP.NET Core yapılandırmasındaki](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlandırmak için kullanılan iki nokta üst üste, Anahtar Kasası gizli adlarında izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="3e5bf-149">Bu nedenle, gizlilikler uygulamanın yapılandırmasına yüklendiğinde iki tire kullanılır ve iki nokta üst üste bulunur.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="3e5bf-150">Aşağıdaki gizlilikler örnek uygulamayla kullanım içindir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="3e5bf-151">Değerler, Kullanıcı parolalarından geliştirme ortamında yüklenen `_prod` `_dev` sonek değerlerinden ayırt etmek için bir sonek içerir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="3e5bf-152">Önceki `{KEY VAULT NAME}` adımda oluşturduğunuz anahtar kasasının adıyla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="3e5bf-153">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanın</span><span class="sxs-lookup"><span data-stu-id="3e5bf-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="3e5bf-154">**Uygulama Azure dışında barındırıldığı zaman**bir anahtar kasasında kimlik doğrulaması yapmak IÇIN Azure AD, Azure Key Vault ve uygulamayı bir Azure ACTIVE DIRECTORY uygulama kimliği ve X. 509.440 sertifikası kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="3e5bf-155">Daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="3e5bf-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="3e5bf-156">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanılması desteklenmekle birlikte, Azure 'da bir uygulama barındırılırken [Azure kaynakları Için Yönetilen kimlikler](#use-managed-identities-for-azure-resources) kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="3e5bf-157">Yönetilen kimlikler, uygulamada veya geliştirme ortamında bir sertifikanın depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="3e5bf-158">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki Ifade olarak `Certificate`ayarlandığında bir uygulama kimliği ve X. 509.440 sertifikası kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="3e5bf-159">PKCS # 12 Arşivi (*. pfx*) sertifikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="3e5bf-160">Sertifika oluşturma seçenekleri Windows ve [OpenSSL](https://www.openssl.org/) [üzerinde MakeCert](/windows/desktop/seccrypto/makecert) içerir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="3e5bf-161">Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükler.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="3e5bf-162">Anahtarı verilebilir olarak işaretlemek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="3e5bf-163">Sertifikanın daha sonra bu işlemde kullanılan parmak izini aklınızda bulunur.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="3e5bf-164">PKCS # 12 Arşivi (*. pfx*) sertifikasını der kodlu bir sertifika (*. cer*) olarak dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="3e5bf-165">Uygulamayı Azure AD 'ye kaydedin (**uygulama kayıtları**).</span><span class="sxs-lookup"><span data-stu-id="3e5bf-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="3e5bf-166">DER kodlu sertifikayı (*. cer*) Azure AD 'ye yükleyin:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="3e5bf-167">Azure AD 'de uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="3e5bf-168">**Sertifikalar & gizli**dizi sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="3e5bf-169">Ortak anahtarı içeren sertifikayı karşıya yüklemek için **sertifikayı karşıya yükle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="3e5bf-170">*. Cer*, *. pek*veya *. CRT* sertifikası kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="3e5bf-171">Anahtar Kasası adı, uygulama KIMLIĞI ve sertifika parmak izini uygulamanın *appSettings. JSON* dosyasında depolayın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="3e5bf-172">Azure portal **ana** kasaları ' ne gidin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="3e5bf-173">Azure Key Vault bölümünde, [üretim ortamındaki gizli dizi deposunda](#secret-storage-in-the-production-environment-with-azure-key-vault) oluşturduğunuz anahtar kasasını seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="3e5bf-174">**Erişim ilkeleri**'ni seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="3e5bf-175">**Erişim Ilkesi Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="3e5bf-176">**Gizli izinleri** açın ve uygulamaya **Get** ve **list** izinleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="3e5bf-177">**Sorumlu Seç** ' i seçin ve kayıtlı uygulamayı ada göre seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="3e5bf-178">**Seç** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="3e5bf-179">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-179">Select **OK**.</span></span>
1. <span data-ttu-id="3e5bf-180">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-180">Select **Save**.</span></span>
1. <span data-ttu-id="3e5bf-181">Uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-181">Deploy the app.</span></span>

<span data-ttu-id="3e5bf-182">`Certificate` Örnek uygulama, yapılandırma değerlerini, parola adı `IConfigurationRoot` ile aynı adla alır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="3e5bf-183">Hiyerarşik olmayan değerler: için `SecretName` değeri ile `config["SecretName"]`elde edilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="3e5bf-184">Hiyerarşik değerler (bölümler): ( `:` iki nokta üst üste) gösterimini `GetSection` veya genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="3e5bf-185">Yapılandırma değerini elde etmek için şu yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="3e5bf-186">X. 509.440 sertifikası işletim sistemi tarafından yönetiliyor.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="3e5bf-187">Uygulama, <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appSettings. JSON* dosyası tarafından sağlanan değerlerle çağırır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="3e5bf-188">Örnek değerler:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-188">Example values:</span></span>

* <span data-ttu-id="3e5bf-189">Anahtar Kasası adı:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="3e5bf-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="3e5bf-190">Uygulama KIMLIĞI:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="3e5bf-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="3e5bf-191">Sertifika parmak izi:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="3e5bf-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="3e5bf-192">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="3e5bf-193">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="3e5bf-194">Geliştirme ortamında, gizli anahtar değerleri `_dev` sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="3e5bf-195">Üretim ortamında, değerler `_prod` sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="3e5bf-196">Azure kaynakları için Yönetilen kimlikler kullanma</span><span class="sxs-lookup"><span data-stu-id="3e5bf-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="3e5bf-197">Azure **'a dağıtılan bir uygulama** , [Azure kaynakları için yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanarak uygulamanın, KIMLIK bilgileri (uygulama kimliği ve parola/istemci gizli anahtarı) olmadan Azure AD kimlik doğrulaması kullanarak Azure Key Vault kimlik doğrulamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="3e5bf-198">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki ifade olarak `Managed`ayarlandığında Azure kaynakları için Yönetilen kimlikler kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="3e5bf-199">Uygulamanın *appSettings. JSON* dosyasına kasa adını girin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="3e5bf-200">Örnek uygulama `Managed` sürüme ayarlandığında BIR uygulama kimliği ve parola (Istemci gizli anahtarı) gerektirmez, bu nedenle bu yapılandırma girişlerini yoksayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="3e5bf-201">Uygulama Azure 'a dağıtılır ve Azure, yalnızca *appSettings. JSON* dosyasında depolanan kasa adını kullanarak Azure Key Vault erişmek için uygulamanın kimliğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="3e5bf-202">Azure App Service için örnek uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="3e5bf-203">Azure App Service dağıtılan bir uygulama, hizmet oluşturulduğunda Azure AD 'ye otomatik olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="3e5bf-204">Aşağıdaki komutta kullanılmak üzere dağıtımdan nesne KIMLIĞINI edinin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="3e5bf-205">Nesne KIMLIĞI, App Service **kimlik** panelinde Azure Portal gösterilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="3e5bf-206">Azure CLı ve uygulamanın nesne KIMLIĞINI kullanarak, uygulama `list` ve `get` anahtar kasasına erişim izinleri sağlayın:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="3e5bf-207">Azure CLı, PowerShell veya Azure portal kullanarak **uygulamayı yeniden başlatın** .</span><span class="sxs-lookup"><span data-stu-id="3e5bf-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="3e5bf-208">Örnek uygulama:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-208">The sample app:</span></span>

* <span data-ttu-id="3e5bf-209">Bir bağlantı dizesi olmadan `AzureServiceTokenProvider` sınıfın bir örneğini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="3e5bf-210">Bir bağlantı dizesi sağlanmazsa, sağlayıcı Azure kaynakları için yönetilen kimliklerden bir erişim belirteci almaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="3e5bf-211">Örnek belirteci <xref:Microsoft.Azure.KeyVault.KeyVaultClient> geri çağırması ile yeni bir oluşturulur. `AzureServiceTokenProvider`</span><span class="sxs-lookup"><span data-stu-id="3e5bf-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="3e5bf-212"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> Örnek <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , tüm gizli değerleri yükleyen ve çift tire (`--`) değerini anahtar adlarında iki nokta (`:`) ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="3e5bf-213">Anahtar Kasası adı örnek değeri:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="3e5bf-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="3e5bf-214">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="3e5bf-215">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="3e5bf-216">Geliştirme ortamında, gizli değerler, Kullanıcı gizli dizileri `_dev` tarafından sağlandıklarından, soneke sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="3e5bf-217">Üretim ortamında, değerler Azure Key Vault tarafından sağlandıklarından `_prod` sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="3e5bf-218">Bir `Access denied` hata alırsanız, UYGULAMANıN Azure AD 'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladıklarını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="3e5bf-219">Azure 'da hizmeti yeniden başlattığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="3e5bf-220">Sağlayıcıyı yönetilen bir kimlik ve bir Azure DevOps işlem hattı ile kullanma hakkında bilgi için bkz. [yönetilen hizmet KIMLIĞIYLE VM 'ye Azure Resource Manager hizmet bağlantısı oluşturma](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="3e5bf-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="3e5bf-221">Yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="3e5bf-221">Configuration options</span></span>

<span data-ttu-id="3e5bf-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*><xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>şunları kabul edebilir:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="3e5bf-223">Özellik</span><span class="sxs-lookup"><span data-stu-id="3e5bf-223">Property</span></span>         | <span data-ttu-id="3e5bf-224">Açıklama</span><span class="sxs-lookup"><span data-stu-id="3e5bf-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="3e5bf-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>değerlerini almak için kullanmak üzere.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="3e5bf-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>gizli dizi yüklemeyi denetlemek için kullanılan örnek.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="3e5bf-227">`Timespan`anahtar kasasındaki değişiklikleri yoklamaya yönelik denemeler arasında bekleme.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="3e5bf-228">Varsayılan değer (yapılandırma `null` yeniden yüklenmez).</span><span class="sxs-lookup"><span data-stu-id="3e5bf-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="3e5bf-229">Anahtar Kasası URI 'SI.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="3e5bf-230">Anahtar adı öneki kullanın</span><span class="sxs-lookup"><span data-stu-id="3e5bf-230">Use a key name prefix</span></span>

<span data-ttu-id="3e5bf-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, Anahtar Kasası gizli dizilerini yapılandırma anahtarlarına nasıl dönüştürdüğünü denetlemenize olanak tanıyan, uygulamasını kabul eden bir aşırı yükleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="3e5bf-232">Örneğin, uygulama başlangıcında sağladığınız önek değerine göre gizli değerleri yüklemek için arabirimini uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="3e5bf-233">Bu, örneğin, uygulama sürümüne göre gizli dizileri yüklemeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="3e5bf-234">Birden çok uygulama için gizli dizileri aynı kasaya yerleştirmek veya çevresel gizli dizileri (örneğin, *geliştirme* ve *Üretim* gizlilikleri) aynı kasaya yerleştirmek için Anahtar Kasası gizli dizileri üzerinde ön ekleri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="3e5bf-235">Farklı uygulama ve geliştirme/üretim ortamlarının, uygulama ortamlarını en yüksek düzeyde güvenlik için yalıtmak üzere ayrı anahtar kasaları kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="3e5bf-236">Aşağıdaki örnekte, için `5000-AppSecret` anahtar kasasında (ve geliştirme ortamı Için gizli Yönetim Aracı kullanılarak) bir gizli dizi oluşturulur (Anahtar Kasası gizli adlarında dönemlere izin verilmez).</span><span class="sxs-lookup"><span data-stu-id="3e5bf-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="3e5bf-237">Bu gizli anahtar, uygulamanın 5.0.0.0 sürümü için bir uygulama gizli anahtarı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="3e5bf-238">Uygulamanın başka bir sürümü olan 5.1.0.0, anahtar kasasına (ve gizli Yönetici Aracı kullanılarak) bir gizli dizi eklenir `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="3e5bf-239">Her bir uygulama sürümü sürümü sürümlü gizli değerini yapılandırma olarak `AppSecret`yükler, bu, gizli anahtarı yüklerken sürümü de kapatıyor.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="3e5bf-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>özel <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>bir ile çağrılır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="3e5bf-241"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Uygulama, doğru gizli anahtarı yapılandırmaya yüklemek için gizli dizi sürüm öneklerine tepki verir:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="3e5bf-242">`Load`adı önekiyle başladığında bir gizli dizi yükler.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="3e5bf-243">Diğer gizlilikler yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="3e5bf-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-244">`GetKey`:</span></span>
  * <span data-ttu-id="3e5bf-245">Gizli dizi adından öneki kaldırır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="3e5bf-246">Yapılandırmada kullanılan sınırlayıcı olan (genellikle iki nokta `KeyDelimiter`üst üste), herhangi bir ada sahip iki kısa çizgi koyar.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="3e5bf-247">Azure Key Vault gizli dizi adlarında bir iki nokta üst üste izin vermez.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="3e5bf-248">`Load` Yöntemi, sürüm ön ekine sahip olanları bulmak için kasa gizli dizileri aracılığıyla yinelenen bir sağlayıcı algoritması tarafından çağırılır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="3e5bf-249">İle `Load`bir sürüm ön eki bulunduğunda, algoritma, gizli anahtar adının `GetKey` yapılandırma adını döndürmek için yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="3e5bf-250">Gizlilik adından sürüm önekini kaldırır ve uygulamanın yapılandırma adı-değer çiftlerine yüklemek için gizli dizi adının geri kalanını döndürür.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="3e5bf-251">Bu yaklaşım uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="3e5bf-252">Uygulamanın proje dosyasında belirtilen sürümü.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="3e5bf-253">Aşağıdaki örnekte, uygulamanın sürümü olarak `5.0.0.0`ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="3e5bf-254">Uygulamanın proje dosyasında `<UserSecretsId>` bir özelliğin var olduğunu, burada `{GUID}` Kullanıcı tarafından sağlanan bir GUID olduğunu onaylayın:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="3e5bf-255">[Gizli dizi yöneticisi aracıyla](xref:security/app-secrets)aşağıdaki gizli dizileri yerel olarak kaydedin:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="3e5bf-256">Gizlilikler, aşağıdaki Azure CLı komutları kullanılarak Azure Key Vault kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="3e5bf-257">Uygulama çalıştırıldığında, Anahtar Kasası gizli dizileri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="3e5bf-258">İçin `5000-AppSecret` dize gizli dizisi, uygulamanın proje dosyasında (`5.0.0.0`) belirtilen uygulamanın sürümüyle eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="3e5bf-259">Sürüm `5000` (tireyle birlikte), anahtar adından çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="3e5bf-260">Uygulamanın tamamında, anahtarla `AppSecret` yapılandırmayı okumak gizli değeri yükler.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="3e5bf-261">Uygulamanın sürümü proje dosyasında olarak `5.1.0.0` değiştirilirse ve uygulama yeniden çalıştırıldığında, döndürülen gizli değer geliştirme ortamında ve `5.1.0.0_secret_value_dev` `5.1.0.0_secret_value_prod` üretimde bulunur.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="3e5bf-262">Ayrıca, kendi <xref:Microsoft.Azure.KeyVault.KeyVaultClient> uygulamanızı da sağlayabilirsiniz <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="3e5bf-263">Özel istemci, uygulama genelinde istemcinin tek bir örneğini paylaşıma izin verir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="3e5bf-264">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="3e5bf-264">Bind an array to a class</span></span>

<span data-ttu-id="3e5bf-265">Sağlayıcı, bir POCO dizisine bağlamak için yapılandırma değerlerini bir diziye okuyabilme özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="3e5bf-266">Anahtarların iki nokta`:`() ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken, bir diziyi oluşturan anahtarları (`:0:`, `:1:`, &hellip; `:{n}:`) ayırt etmek için bir sayısal anahtar kesimi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="3e5bf-267">Daha fazla bilgi için bkz. [yapılandırma: diziyi bir sınıfa bağlama](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="3e5bf-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="3e5bf-268">Azure Key Vault anahtarlar ayırıcı olarak iki nokta üst üste kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="3e5bf-269">Bu konuda açıklanan yaklaşım, hiyerarşik değerler (bölümler)`--`için bir ayırıcı olarak çift tire () kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="3e5bf-270">Dizi anahtarları çift tireler ve sayısal anahtar kesimleri`--0--`(, `--1--`, &hellip; `--{n}--`) ile birlikte Azure Key Vault depolanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="3e5bf-271">Bir JSON dosyası tarafından sunulan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="3e5bf-272">Dizide günlüğe kaydetme hedeflerini açıklayan iki Serilog girişi yansıtan iki nesne değişmez değeri vardır: *sinks* `WriteTo`</span><span class="sxs-lookup"><span data-stu-id="3e5bf-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="3e5bf-273">Önceki JSON dosyasında gösterilen yapılandırma, Çift tire (`--`) gösterimi ve sayısal kesimleri kullanarak Azure Key Vault depolanır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="3e5bf-274">Anahtar</span><span class="sxs-lookup"><span data-stu-id="3e5bf-274">Key</span></span> | <span data-ttu-id="3e5bf-275">Değer</span><span class="sxs-lookup"><span data-stu-id="3e5bf-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="3e5bf-276">Gizli dizileri yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="3e5bf-276">Reload secrets</span></span>

<span data-ttu-id="3e5bf-277">Gizli diziler çağrılana `IConfigurationRoot.Reload()` kadar önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="3e5bf-278">Anahtar kasasındaki zaman aşımına uğradı, devre dışı ve güncelleştirilmiş gizli dizileri, yürütülene kadar `Reload` uygulama tarafından dikkate alınmıyor.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="3e5bf-279">Devre dışı ve süre dolma parolaları</span><span class="sxs-lookup"><span data-stu-id="3e5bf-279">Disabled and expired secrets</span></span>

<span data-ttu-id="3e5bf-280">Devre dışı ve son kullanma parolası <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="3e5bf-281">Uygulamanın üretilmesini engellemek için, farklı bir yapılandırma sağlayıcısı kullanarak yapılandırmayı sağlayın veya devre dışı ya da süre dolma parolasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="3e5bf-282">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="3e5bf-282">Troubleshoot</span></span>

<span data-ttu-id="3e5bf-283">Uygulama, sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Core günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="3e5bf-284">Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="3e5bf-285">Uygulama veya sertifika Azure Active Directory içinde doğru yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="3e5bf-286">Anahtar Kasası Azure Key Vault içinde yok.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="3e5bf-287">Uygulamanın anahtar kasasına erişme yetkisi yok.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="3e5bf-288">Erişim ilkesi ve `List` izinleri içermez `Get` .</span><span class="sxs-lookup"><span data-stu-id="3e5bf-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="3e5bf-289">Anahtar kasasında yapılandırma verileri (ad-değer çifti) yanlış olarak adlandırılmış, eksik, devre dışı veya zaman aşımına uğradı.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="3e5bf-290">Uygulamanın Anahtar Kasası adı (`KeyVaultName`), Azure AD uygulama kimliği (`AzureADApplicationId`) veya Azure AD sertifika parmak izi (`AzureADCertThumbprint`) yanlış.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="3e5bf-291">Yüklemeye çalıştığınız değer için uygulamada yapılandırma anahtarı (ad) yanlış.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="3e5bf-292">Uygulama için erişim ilkesini anahtar kasasına eklerken, ilke oluşturulmuştur, ancak **erişim ilkeleri** Kullanıcı arabiriminde **Kaydet** düğmesi seçilmedi.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e5bf-293">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3e5bf-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="3e5bf-294">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="3e5bf-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="3e5bf-295">Microsoft Azure: Key Vault belgeler</span><span class="sxs-lookup"><span data-stu-id="3e5bf-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="3e5bf-296">Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma</span><span class="sxs-lookup"><span data-stu-id="3e5bf-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="3e5bf-297">KeyVaultClient sınıfı</span><span class="sxs-lookup"><span data-stu-id="3e5bf-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="3e5bf-298">Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma</span><span class="sxs-lookup"><span data-stu-id="3e5bf-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="3e5bf-299">Öğretici: .NET 'te Azure Windows sanal makinesi ile Azure Key Vault kullanma</span><span class="sxs-lookup"><span data-stu-id="3e5bf-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3e5bf-300">Bu belgede, Azure Key Vault gizliliklerden uygulama yapılandırma değerlerini yüklemek için [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) yapılandırma sağlayıcısının nasıl kullanılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="3e5bf-301">Azure Key Vault, uygulama ve hizmetler tarafından kullanılan şifreleme anahtarlarını ve gizli dizileri koruma konusunda yardımcı olan bulut tabanlı bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="3e5bf-302">ASP.NET Core uygulamalarla Azure Key Vault kullanmaya yönelik yaygın senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="3e5bf-303">Hassas yapılandırma verilerine erişimi denetleme.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="3e5bf-304">Yapılandırma verilerini depolarken FIPS 140-2 düzey 2 doğrulanan donanım güvenlik modülleri (HSM 'ler) gereksinimini karşılarsınız.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="3e5bf-305">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3e5bf-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="3e5bf-306">Paketler</span><span class="sxs-lookup"><span data-stu-id="3e5bf-306">Packages</span></span>

<span data-ttu-id="3e5bf-307">[Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="3e5bf-308">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="3e5bf-308">Sample app</span></span>

<span data-ttu-id="3e5bf-309">Örnek uygulama, `#define` *program.cs* dosyasının en üstünde yer aldığı ifadeye göre belirlenen iki moddan birinde çalışır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="3e5bf-310">`Certificate`&ndash; Azure Key Vault depolanan gizli dizileri erişmek Için Azure Key Vault istemci kimliği ve X. 509.952 sertifikası kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-310">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="3e5bf-311">Örneğin bu sürümü, Azure App Service dağıtılan herhangi bir konumdan veya ASP.NET Core uygulamasına hizmet veren herhangi bir konağa çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="3e5bf-312">`Managed`&ndash; Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasıyla Azure Key Vault üzere uygulamanın kimliğini doğrulamak için [Azure kaynakları için yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-312">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="3e5bf-313">Kimlik doğrulaması için Yönetilen kimlikler kullanıldığında, bir Azure AD uygulama KIMLIĞI ve parolası (gizli anahtar) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="3e5bf-314">Örneğin `Managed` sürümünün Azure 'a dağıtılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="3e5bf-315">[Azure kaynakları Için Yönetilen kimlikler bölümünü kullanma](#use-managed-identities-for-azure-resources) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="3e5bf-316">Önişlemci yönergeleri (`#define`) kullanarak örnek bir uygulamanın nasıl yapılandırılacağı hakkında daha fazla bilgi için bkz <xref:index#preprocessor-directives-in-sample-code>..</span><span class="sxs-lookup"><span data-stu-id="3e5bf-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="3e5bf-317">Geliştirme ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="3e5bf-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="3e5bf-318">[Gizli dizi Yöneticisi aracını](xref:security/app-secrets)kullanarak gizli dizileri yerel olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="3e5bf-319">Örnek uygulama, geliştirme ortamındaki yerel makinede çalıştığında, gizli anahtar Yöneticisi deposundan gizli diziler yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="3e5bf-320">Gizli dizi Yöneticisi Aracı, uygulamanın `<UserSecretsId>` proje dosyasında bir özellik gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="3e5bf-321">Özellik değerini (`{GUID}`) herhangi BIR benzersiz GUID 'ye ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="3e5bf-322">Gizlilikler ad-değer çiftleri olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="3e5bf-323">Hiyerarşik değerler (yapılandırma bölümleri) `:` [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index) anahtarı adlarında ayırıcı olarak bir (iki nokta üst üste) kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="3e5bf-324">Gizli dizi, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır; burada `{SECRET NAME}` ad ve `{SECRET VALUE}` değerdir:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="3e5bf-325">Örnek uygulamanın gizli dizilerini ayarlamak için, projenin [içerik kökünden](xref:fundamentals/index#content-root) bir komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="3e5bf-326">Bu gizlilikler, [Azure Key Vault bölümündeki üretim ortamındaki gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında Azure Key Vault depolandığında, `_dev` sonek olarak `_prod`değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="3e5bf-327">Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren bir görsel ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="3e5bf-328">Azure Key Vault ile üretim ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="3e5bf-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="3e5bf-329">Hızlı başlangıç tarafından sunulan yönergeler [: Azure CLI kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-cli) , bir Azure Key Vault oluşturmak ve örnek uygulama tarafından kullanılan gizli dizileri depolamak için burada özetlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="3e5bf-330">Daha ayrıntılı bilgi için konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="3e5bf-331">[Azure Portal](https://portal.azure.com/)aşağıdaki yöntemlerden birini kullanarak Azure Cloud Shell 'i açın:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="3e5bf-332">Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="3e5bf-333">Metin kutusunda "Azure CLı" arama dizesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="3e5bf-334">**Cloud Shell Başlat** düğmesini kullanarak tarayıcınızda Cloud Shell açın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="3e5bf-335">Azure portal sağ üst köşesindeki menüdeki **Cloud Shell** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="3e5bf-336">Daha fazla bilgi için bkz. [Azure CLI](/cli/azure/) ve [Azure Cloud Shell Genel Bakış](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="3e5bf-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="3e5bf-337">Henüz kimlik doğrulamasından geçirilmemişse `az login` komutuyla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="3e5bf-338">Aşağıdaki komutla bir kaynak grubu oluşturun; burada `{RESOURCE GROUP NAME}` , yeni kaynak grubu için kaynak grubu adı ve `{LOCATION}` Azure bölgesi (Datacenter) olur:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="3e5bf-339">Aşağıdaki komutla kaynak grubunda bir Anahtar Kasası oluşturun; burada `{KEY VAULT NAME}` yeni anahtar kasasının adı ve `{LOCATION}` Azure bölgesi (Datacenter) bulunur:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="3e5bf-340">Anahtar kasasında ad-değer çiftleri olarak gizli diziler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="3e5bf-341">Azure Key Vault gizli dizi adları, alfasayısal karakterler ve tireler ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="3e5bf-342">Hiyerarşik değerler (yapılandırma bölümleri) ayırıcı `--` olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="3e5bf-343">Genellikle [ASP.NET Core yapılandırmasındaki](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlandırmak için kullanılan iki nokta üst üste, Anahtar Kasası gizli adlarında izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="3e5bf-344">Bu nedenle, gizlilikler uygulamanın yapılandırmasına yüklendiğinde iki tire kullanılır ve iki nokta üst üste bulunur.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="3e5bf-345">Aşağıdaki gizlilikler örnek uygulamayla kullanım içindir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="3e5bf-346">Değerler, Kullanıcı parolalarından geliştirme ortamında yüklenen `_prod` `_dev` sonek değerlerinden ayırt etmek için bir sonek içerir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="3e5bf-347">Önceki `{KEY VAULT NAME}` adımda oluşturduğunuz anahtar kasasının adıyla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="3e5bf-348">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanın</span><span class="sxs-lookup"><span data-stu-id="3e5bf-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="3e5bf-349">**Uygulama Azure dışında barındırıldığı zaman**bir anahtar kasasında kimlik doğrulaması yapmak IÇIN Azure AD, Azure Key Vault ve uygulamayı bir Azure ACTIVE DIRECTORY uygulama kimliği ve X. 509.440 sertifikası kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="3e5bf-350">Daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="3e5bf-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="3e5bf-351">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanılması desteklenmekle birlikte, Azure 'da bir uygulama barındırılırken [Azure kaynakları Için Yönetilen kimlikler](#use-managed-identities-for-azure-resources) kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="3e5bf-352">Yönetilen kimlikler, uygulamada veya geliştirme ortamında bir sertifikanın depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="3e5bf-353">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki Ifade olarak `Certificate`ayarlandığında bir uygulama kimliği ve X. 509.440 sertifikası kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="3e5bf-354">PKCS # 12 Arşivi (*. pfx*) sertifikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="3e5bf-355">Sertifika oluşturma seçenekleri Windows ve [OpenSSL](https://www.openssl.org/) [üzerinde MakeCert](/windows/desktop/seccrypto/makecert) içerir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="3e5bf-356">Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükler.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="3e5bf-357">Anahtarı verilebilir olarak işaretlemek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="3e5bf-358">Sertifikanın daha sonra bu işlemde kullanılan parmak izini aklınızda bulunur.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="3e5bf-359">PKCS # 12 Arşivi (*. pfx*) sertifikasını der kodlu bir sertifika (*. cer*) olarak dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="3e5bf-360">Uygulamayı Azure AD 'ye kaydedin (**uygulama kayıtları**).</span><span class="sxs-lookup"><span data-stu-id="3e5bf-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="3e5bf-361">DER kodlu sertifikayı (*. cer*) Azure AD 'ye yükleyin:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="3e5bf-362">Azure AD 'de uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="3e5bf-363">**Sertifikalar & gizli**dizi sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="3e5bf-364">Ortak anahtarı içeren sertifikayı karşıya yüklemek için **sertifikayı karşıya yükle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="3e5bf-365">*. Cer*, *. pek*veya *. CRT* sertifikası kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="3e5bf-366">Anahtar Kasası adı, uygulama KIMLIĞI ve sertifika parmak izini uygulamanın *appSettings. JSON* dosyasında depolayın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="3e5bf-367">Azure portal **ana** kasaları ' ne gidin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="3e5bf-368">Azure Key Vault bölümünde, [üretim ortamındaki gizli dizi deposunda](#secret-storage-in-the-production-environment-with-azure-key-vault) oluşturduğunuz anahtar kasasını seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="3e5bf-369">**Erişim ilkeleri**'ni seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="3e5bf-370">**Erişim Ilkesi Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="3e5bf-371">**Gizli izinleri** açın ve uygulamaya **Get** ve **list** izinleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="3e5bf-372">**Sorumlu Seç** ' i seçin ve kayıtlı uygulamayı ada göre seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="3e5bf-373">**Seç** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="3e5bf-374">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-374">Select **OK**.</span></span>
1. <span data-ttu-id="3e5bf-375">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-375">Select **Save**.</span></span>
1. <span data-ttu-id="3e5bf-376">Uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-376">Deploy the app.</span></span>

<span data-ttu-id="3e5bf-377">`Certificate` Örnek uygulama, yapılandırma değerlerini, parola adı `IConfigurationRoot` ile aynı adla alır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="3e5bf-378">Hiyerarşik olmayan değerler: için `SecretName` değeri ile `config["SecretName"]`elde edilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="3e5bf-379">Hiyerarşik değerler (bölümler): ( `:` iki nokta üst üste) gösterimini `GetSection` veya genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="3e5bf-380">Yapılandırma değerini elde etmek için şu yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="3e5bf-381">X. 509.440 sertifikası işletim sistemi tarafından yönetiliyor.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="3e5bf-382">Uygulama, <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appSettings. JSON* dosyası tarafından sağlanan değerlerle çağırır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="3e5bf-383">Örnek değerler:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-383">Example values:</span></span>

* <span data-ttu-id="3e5bf-384">Anahtar Kasası adı:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="3e5bf-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="3e5bf-385">Uygulama KIMLIĞI:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="3e5bf-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="3e5bf-386">Sertifika parmak izi:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="3e5bf-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="3e5bf-387">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="3e5bf-388">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="3e5bf-389">Geliştirme ortamında, gizli anahtar değerleri `_dev` sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="3e5bf-390">Üretim ortamında, değerler `_prod` sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="3e5bf-391">Azure kaynakları için Yönetilen kimlikler kullanma</span><span class="sxs-lookup"><span data-stu-id="3e5bf-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="3e5bf-392">Azure **'a dağıtılan bir uygulama** , [Azure kaynakları için yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanarak uygulamanın, KIMLIK bilgileri (uygulama kimliği ve parola/istemci gizli anahtarı) olmadan Azure AD kimlik doğrulaması kullanarak Azure Key Vault kimlik doğrulamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="3e5bf-393">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki ifade olarak `Managed`ayarlandığında Azure kaynakları için Yönetilen kimlikler kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="3e5bf-394">Uygulamanın *appSettings. JSON* dosyasına kasa adını girin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="3e5bf-395">Örnek uygulama `Managed` sürüme ayarlandığında BIR uygulama kimliği ve parola (Istemci gizli anahtarı) gerektirmez, bu nedenle bu yapılandırma girişlerini yoksayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="3e5bf-396">Uygulama Azure 'a dağıtılır ve Azure, yalnızca *appSettings. JSON* dosyasında depolanan kasa adını kullanarak Azure Key Vault erişmek için uygulamanın kimliğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="3e5bf-397">Azure App Service için örnek uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="3e5bf-398">Azure App Service dağıtılan bir uygulama, hizmet oluşturulduğunda Azure AD 'ye otomatik olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="3e5bf-399">Aşağıdaki komutta kullanılmak üzere dağıtımdan nesne KIMLIĞINI edinin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="3e5bf-400">Nesne KIMLIĞI, App Service **kimlik** panelinde Azure Portal gösterilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="3e5bf-401">Azure CLı ve uygulamanın nesne KIMLIĞINI kullanarak, uygulama `list` ve `get` anahtar kasasına erişim izinleri sağlayın:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="3e5bf-402">Azure CLı, PowerShell veya Azure portal kullanarak **uygulamayı yeniden başlatın** .</span><span class="sxs-lookup"><span data-stu-id="3e5bf-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="3e5bf-403">Örnek uygulama:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-403">The sample app:</span></span>

* <span data-ttu-id="3e5bf-404">Bir bağlantı dizesi olmadan `AzureServiceTokenProvider` sınıfın bir örneğini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="3e5bf-405">Bir bağlantı dizesi sağlanmazsa, sağlayıcı Azure kaynakları için yönetilen kimliklerden bir erişim belirteci almaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="3e5bf-406">Örnek belirteci <xref:Microsoft.Azure.KeyVault.KeyVaultClient> geri çağırması ile yeni bir oluşturulur. `AzureServiceTokenProvider`</span><span class="sxs-lookup"><span data-stu-id="3e5bf-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="3e5bf-407"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> Örnek <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , tüm gizli değerleri yükleyen ve çift tire (`--`) değerini anahtar adlarında iki nokta (`:`) ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="3e5bf-408">Anahtar Kasası adı örnek değeri:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="3e5bf-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="3e5bf-409">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="3e5bf-410">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="3e5bf-411">Geliştirme ortamında, gizli değerler, Kullanıcı gizli dizileri `_dev` tarafından sağlandıklarından, soneke sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="3e5bf-412">Üretim ortamında, değerler Azure Key Vault tarafından sağlandıklarından `_prod` sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="3e5bf-413">Bir `Access denied` hata alırsanız, UYGULAMANıN Azure AD 'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladıklarını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="3e5bf-414">Azure 'da hizmeti yeniden başlattığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="3e5bf-415">Sağlayıcıyı yönetilen bir kimlik ve bir Azure DevOps işlem hattı ile kullanma hakkında bilgi için bkz. [yönetilen hizmet KIMLIĞIYLE VM 'ye Azure Resource Manager hizmet bağlantısı oluşturma](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="3e5bf-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="3e5bf-416">Anahtar adı öneki kullanın</span><span class="sxs-lookup"><span data-stu-id="3e5bf-416">Use a key name prefix</span></span>

<span data-ttu-id="3e5bf-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, Anahtar Kasası gizli dizilerini yapılandırma anahtarlarına nasıl dönüştürdüğünü denetlemenize olanak tanıyan, uygulamasını kabul eden bir aşırı yükleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="3e5bf-418">Örneğin, uygulama başlangıcında sağladığınız önek değerine göre gizli değerleri yüklemek için arabirimini uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="3e5bf-419">Bu, örneğin, uygulama sürümüne göre gizli dizileri yüklemeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="3e5bf-420">Birden çok uygulama için gizli dizileri aynı kasaya yerleştirmek veya çevresel gizli dizileri (örneğin, *geliştirme* ve *Üretim* gizlilikleri) aynı kasaya yerleştirmek için Anahtar Kasası gizli dizileri üzerinde ön ekleri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="3e5bf-421">Farklı uygulama ve geliştirme/üretim ortamlarının, uygulama ortamlarını en yüksek düzeyde güvenlik için yalıtmak üzere ayrı anahtar kasaları kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="3e5bf-422">Aşağıdaki örnekte, için `5000-AppSecret` anahtar kasasında (ve geliştirme ortamı Için gizli Yönetim Aracı kullanılarak) bir gizli dizi oluşturulur (Anahtar Kasası gizli adlarında dönemlere izin verilmez).</span><span class="sxs-lookup"><span data-stu-id="3e5bf-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="3e5bf-423">Bu gizli anahtar, uygulamanın 5.0.0.0 sürümü için bir uygulama gizli anahtarı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="3e5bf-424">Uygulamanın başka bir sürümü olan 5.1.0.0, anahtar kasasına (ve gizli Yönetici Aracı kullanılarak) bir gizli dizi eklenir `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="3e5bf-425">Her bir uygulama sürümü sürümü sürümlü gizli değerini yapılandırma olarak `AppSecret`yükler, bu, gizli anahtarı yüklerken sürümü de kapatıyor.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="3e5bf-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>özel <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>bir ile çağrılır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="3e5bf-427"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Uygulama, doğru gizli anahtarı yapılandırmaya yüklemek için gizli dizi sürüm öneklerine tepki verir:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="3e5bf-428">`Load`adı önekiyle başladığında bir gizli dizi yükler.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="3e5bf-429">Diğer gizlilikler yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="3e5bf-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-430">`GetKey`:</span></span>
  * <span data-ttu-id="3e5bf-431">Gizli dizi adından öneki kaldırır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="3e5bf-432">Yapılandırmada kullanılan sınırlayıcı olan (genellikle iki nokta `KeyDelimiter`üst üste), herhangi bir ada sahip iki kısa çizgi koyar.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="3e5bf-433">Azure Key Vault gizli dizi adlarında bir iki nokta üst üste izin vermez.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="3e5bf-434">`Load` Yöntemi, sürüm ön ekine sahip olanları bulmak için kasa gizli dizileri aracılığıyla yinelenen bir sağlayıcı algoritması tarafından çağırılır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="3e5bf-435">İle `Load`bir sürüm ön eki bulunduğunda, algoritma, gizli anahtar adının `GetKey` yapılandırma adını döndürmek için yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="3e5bf-436">Gizlilik adından sürüm önekini kaldırır ve uygulamanın yapılandırma adı-değer çiftlerine yüklemek için gizli dizi adının geri kalanını döndürür.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="3e5bf-437">Bu yaklaşım uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="3e5bf-438">Uygulamanın proje dosyasında belirtilen sürümü.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="3e5bf-439">Aşağıdaki örnekte, uygulamanın sürümü olarak `5.0.0.0`ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="3e5bf-440">Uygulamanın proje dosyasında `<UserSecretsId>` bir özelliğin var olduğunu, burada `{GUID}` Kullanıcı tarafından sağlanan bir GUID olduğunu onaylayın:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="3e5bf-441">[Gizli dizi yöneticisi aracıyla](xref:security/app-secrets)aşağıdaki gizli dizileri yerel olarak kaydedin:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="3e5bf-442">Gizlilikler, aşağıdaki Azure CLı komutları kullanılarak Azure Key Vault kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="3e5bf-443">Uygulama çalıştırıldığında, Anahtar Kasası gizli dizileri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="3e5bf-444">İçin `5000-AppSecret` dize gizli dizisi, uygulamanın proje dosyasında (`5.0.0.0`) belirtilen uygulamanın sürümüyle eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="3e5bf-445">Sürüm `5000` (tireyle birlikte), anahtar adından çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="3e5bf-446">Uygulamanın tamamında, anahtarla `AppSecret` yapılandırmayı okumak gizli değeri yükler.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="3e5bf-447">Uygulamanın sürümü proje dosyasında olarak `5.1.0.0` değiştirilirse ve uygulama yeniden çalıştırıldığında, döndürülen gizli değer geliştirme ortamında ve `5.1.0.0_secret_value_dev` `5.1.0.0_secret_value_prod` üretimde bulunur.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="3e5bf-448">Ayrıca, kendi <xref:Microsoft.Azure.KeyVault.KeyVaultClient> uygulamanızı da sağlayabilirsiniz <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="3e5bf-449">Özel istemci, uygulama genelinde istemcinin tek bir örneğini paylaşıma izin verir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="3e5bf-450">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="3e5bf-450">Bind an array to a class</span></span>

<span data-ttu-id="3e5bf-451">Sağlayıcı, bir POCO dizisine bağlamak için yapılandırma değerlerini bir diziye okuyabilme özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="3e5bf-452">Anahtarların iki nokta`:`() ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken, bir diziyi oluşturan anahtarları (`:0:`, `:1:`, &hellip; `:{n}:`) ayırt etmek için bir sayısal anahtar kesimi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="3e5bf-453">Daha fazla bilgi için bkz. [yapılandırma: diziyi bir sınıfa bağlama](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="3e5bf-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="3e5bf-454">Azure Key Vault anahtarlar ayırıcı olarak iki nokta üst üste kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="3e5bf-455">Bu konuda açıklanan yaklaşım, hiyerarşik değerler (bölümler)`--`için bir ayırıcı olarak çift tire () kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="3e5bf-456">Dizi anahtarları çift tireler ve sayısal anahtar kesimleri`--0--`(, `--1--`, &hellip; `--{n}--`) ile birlikte Azure Key Vault depolanır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="3e5bf-457">Bir JSON dosyası tarafından sunulan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="3e5bf-458">Dizide günlüğe kaydetme hedeflerini açıklayan iki Serilog girişi yansıtan iki nesne değişmez değeri vardır: *sinks* `WriteTo`</span><span class="sxs-lookup"><span data-stu-id="3e5bf-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="3e5bf-459">Önceki JSON dosyasında gösterilen yapılandırma, Çift tire (`--`) gösterimi ve sayısal kesimleri kullanarak Azure Key Vault depolanır:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="3e5bf-460">Anahtar</span><span class="sxs-lookup"><span data-stu-id="3e5bf-460">Key</span></span> | <span data-ttu-id="3e5bf-461">Değer</span><span class="sxs-lookup"><span data-stu-id="3e5bf-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="3e5bf-462">Gizli dizileri yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="3e5bf-462">Reload secrets</span></span>

<span data-ttu-id="3e5bf-463">Gizli diziler çağrılana `IConfigurationRoot.Reload()` kadar önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="3e5bf-464">Anahtar kasasındaki zaman aşımına uğradı, devre dışı ve güncelleştirilmiş gizli dizileri, yürütülene kadar `Reload` uygulama tarafından dikkate alınmıyor.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="3e5bf-465">Devre dışı ve süre dolma parolaları</span><span class="sxs-lookup"><span data-stu-id="3e5bf-465">Disabled and expired secrets</span></span>

<span data-ttu-id="3e5bf-466">Devre dışı ve son kullanma parolası <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="3e5bf-467">Uygulamanın üretilmesini engellemek için, farklı bir yapılandırma sağlayıcısı kullanarak yapılandırmayı sağlayın veya devre dışı ya da süre dolma parolasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="3e5bf-468">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="3e5bf-468">Troubleshoot</span></span>

<span data-ttu-id="3e5bf-469">Uygulama, sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Core günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="3e5bf-470">Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:</span><span class="sxs-lookup"><span data-stu-id="3e5bf-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="3e5bf-471">Uygulama veya sertifika Azure Active Directory içinde doğru yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="3e5bf-472">Anahtar Kasası Azure Key Vault içinde yok.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="3e5bf-473">Uygulamanın anahtar kasasına erişme yetkisi yok.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="3e5bf-474">Erişim ilkesi ve `List` izinleri içermez `Get` .</span><span class="sxs-lookup"><span data-stu-id="3e5bf-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="3e5bf-475">Anahtar kasasında yapılandırma verileri (ad-değer çifti) yanlış olarak adlandırılmış, eksik, devre dışı veya zaman aşımına uğradı.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="3e5bf-476">Uygulamanın Anahtar Kasası adı (`KeyVaultName`), Azure AD uygulama kimliği (`AzureADApplicationId`) veya Azure AD sertifika parmak izi (`AzureADCertThumbprint`) yanlış.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="3e5bf-477">Yüklemeye çalıştığınız değer için uygulamada yapılandırma anahtarı (ad) yanlış.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="3e5bf-478">Uygulama için erişim ilkesini anahtar kasasına eklerken, ilke oluşturulmuştur, ancak **erişim ilkeleri** Kullanıcı arabiriminde **Kaydet** düğmesi seçilmedi.</span><span class="sxs-lookup"><span data-stu-id="3e5bf-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e5bf-479">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3e5bf-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="3e5bf-480">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="3e5bf-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="3e5bf-481">Microsoft Azure: Key Vault belgeler</span><span class="sxs-lookup"><span data-stu-id="3e5bf-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="3e5bf-482">Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma</span><span class="sxs-lookup"><span data-stu-id="3e5bf-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="3e5bf-483">KeyVaultClient sınıfı</span><span class="sxs-lookup"><span data-stu-id="3e5bf-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="3e5bf-484">Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma</span><span class="sxs-lookup"><span data-stu-id="3e5bf-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="3e5bf-485">Öğretici: .NET 'te Azure Windows sanal makinesi ile Azure Key Vault kullanma</span><span class="sxs-lookup"><span data-stu-id="3e5bf-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

