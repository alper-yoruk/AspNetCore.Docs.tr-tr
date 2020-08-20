---
title: ASP.NET Core Azure Key Vault yapılandırma sağlayıcısı
author: rick-anderson
description: Çalışma zamanında yüklenen ad-değer çiftlerini kullanarak bir uygulamayı yapılandırmak için Azure Key Vault yapılandırma sağlayıcısını nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
ms.openlocfilehash: 32967e039671721852b8e421fe5a08763b23e418
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629788"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="f082c-103">ASP.NET Core Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="f082c-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="f082c-104">, [Andrew Stanton-nurte](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="f082c-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f082c-105">Bu belgede, Azure Key Vault gizliliklerden uygulama yapılandırma değerlerini yüklemek için [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) yapılandırma sağlayıcısının nasıl kullanılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f082c-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="f082c-106">Azure Key Vault, uygulama ve hizmetler tarafından kullanılan şifreleme anahtarlarını ve gizli dizileri koruma konusunda yardımcı olan bulut tabanlı bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="f082c-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="f082c-107">ASP.NET Core uygulamalarla Azure Key Vault kullanmaya yönelik yaygın senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="f082c-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="f082c-108">Hassas yapılandırma verilerine erişimi denetleme.</span><span class="sxs-lookup"><span data-stu-id="f082c-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="f082c-109">Yapılandırma verilerini depolarken FIPS 140-2 düzey 2 doğrulanan donanım güvenlik modülleri (HSM 'ler) gereksinimini karşılarsınız.</span><span class="sxs-lookup"><span data-stu-id="f082c-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="f082c-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f082c-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="f082c-111">Paketler</span><span class="sxs-lookup"><span data-stu-id="f082c-111">Packages</span></span>

<span data-ttu-id="f082c-112">Microsoft.Extensions.Configyönlendirmeye bir paket başvurusu ekleyin [ . AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) paketi.</span><span class="sxs-lookup"><span data-stu-id="f082c-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="f082c-113">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="f082c-113">Sample app</span></span>

<span data-ttu-id="f082c-114">Örnek uygulama, `#define` *program.cs* dosyasının en üstünde yer aldığı ifadeye göre belirlenen iki moddan birinde çalışır:</span><span class="sxs-lookup"><span data-stu-id="f082c-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="f082c-115">`Certificate`: Azure Key Vault depolanan gizli dizileri erişmek için Azure Key Vault Istemci KIMLIĞI ve X. 509.952 sertifikası kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f082c-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="f082c-116">Örneğin bu sürümü, Azure App Service dağıtılan herhangi bir konumdan veya ASP.NET Core uygulamasına hizmet veren herhangi bir konağa çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f082c-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="f082c-117">`Managed`: Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasıyla Azure Key Vault üzere uygulamanın kimliğini doğrulamak için [Azure kaynakları Için yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f082c-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="f082c-118">Kimlik doğrulaması için Yönetilen kimlikler kullanıldığında, bir Azure AD uygulama KIMLIĞI ve parolası (gizli anahtar) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="f082c-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="f082c-119">`Managed`Örneğin sürümünün Azure 'a dağıtılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f082c-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="f082c-120">[Azure kaynakları Için Yönetilen kimlikler bölümünü kullanma](#use-managed-identities-for-azure-resources) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="f082c-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="f082c-121">Önişlemci yönergeleri () kullanarak örnek bir uygulamanın nasıl yapılandırılacağı hakkında daha fazla bilgi için `#define` bkz <xref:index#preprocessor-directives-in-sample-code> ..</span><span class="sxs-lookup"><span data-stu-id="f082c-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="f082c-122">Geliştirme ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="f082c-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="f082c-123">[Gizli dizi Yöneticisi aracını](xref:security/app-secrets)kullanarak gizli dizileri yerel olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f082c-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="f082c-124">Örnek uygulama, geliştirme ortamındaki yerel makinede çalıştığında, gizli anahtar Yöneticisi deposundan gizli diziler yüklenir.</span><span class="sxs-lookup"><span data-stu-id="f082c-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="f082c-125">Gizli dizi Yöneticisi Aracı, `<UserSecretsId>` uygulamanın proje dosyasında bir özellik gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f082c-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="f082c-126">Özellik değerini ( `{GUID}` ) herhangi bir BENZERSIZ GUID 'ye ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="f082c-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="f082c-127">Gizlilikler ad-değer çiftleri olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f082c-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="f082c-128">Hiyerarşik değerler (yapılandırma bölümleri) `:` [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index) anahtarı adlarında ayırıcı olarak bir (iki nokta üst üste) kullanır.</span><span class="sxs-lookup"><span data-stu-id="f082c-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="f082c-129">Gizli dizi, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır; burada `{SECRET NAME}` ad ve `{SECRET VALUE}` değerdir:</span><span class="sxs-lookup"><span data-stu-id="f082c-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="f082c-130">Örnek uygulamanın gizli dizilerini ayarlamak için, projenin [içerik kökünden](xref:fundamentals/index#content-root) bir komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="f082c-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="f082c-131">Bu gizlilikler, [Azure Key Vault bölümündeki üretim ortamındaki gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında Azure Key Vault depolandığında, `_dev` sonek olarak değiştirilir `_prod` .</span><span class="sxs-lookup"><span data-stu-id="f082c-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="f082c-132">Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren bir görsel ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="f082c-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="f082c-133">Azure Key Vault ile üretim ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="f082c-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="f082c-134">Hızlı başlangıç tarafından sunulan yönergeler [: Azure CLI kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-cli) , bir Azure Key Vault oluşturmak ve örnek uygulama tarafından kullanılan gizli dizileri depolamak için burada özetlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="f082c-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="f082c-135">Daha ayrıntılı bilgi için konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="f082c-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="f082c-136">[Azure Portal](https://portal.azure.com/)aşağıdaki yöntemlerden birini kullanarak Azure Cloud Shell 'i açın:</span><span class="sxs-lookup"><span data-stu-id="f082c-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="f082c-137">Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="f082c-138">Metin kutusunda "Azure CLı" arama dizesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f082c-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="f082c-139">**Cloud Shell Başlat** düğmesini kullanarak tarayıcınızda Cloud Shell açın.</span><span class="sxs-lookup"><span data-stu-id="f082c-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="f082c-140">Azure portal sağ üst köşesindeki menüdeki **Cloud Shell** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="f082c-141">Daha fazla bilgi için bkz. [Azure CLI](/cli/azure/) ve [Azure Cloud Shell Genel Bakış](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="f082c-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="f082c-142">Henüz kimlik doğrulamasından geçirilmemişse komutuyla oturum açın `az login` .</span><span class="sxs-lookup"><span data-stu-id="f082c-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="f082c-143">Aşağıdaki komutla bir kaynak grubu oluşturun; burada, `{RESOURCE GROUP NAME}` Yeni kaynak grubu için kaynak grubu adı ve `{LOCATION}` Azure bölgesi (Datacenter) olur:</span><span class="sxs-lookup"><span data-stu-id="f082c-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="f082c-144">Aşağıdaki komutla kaynak grubunda bir Anahtar Kasası oluşturun; burada `{KEY VAULT NAME}` Yeni anahtar kasasının adı ve `{LOCATION}` Azure bölgesi (Datacenter) bulunur:</span><span class="sxs-lookup"><span data-stu-id="f082c-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="f082c-145">Anahtar kasasında ad-değer çiftleri olarak gizli diziler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f082c-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="f082c-146">Azure Key Vault gizli dizi adları, alfasayısal karakterler ve tireler ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="f082c-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="f082c-147">Hiyerarşik değerler (yapılandırma bölümleri) `--` ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="f082c-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="f082c-148">Genellikle [ASP.NET Core yapılandırmasındaki](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlandırmak için kullanılan iki nokta üst üste, Anahtar Kasası gizli adlarında izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="f082c-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="f082c-149">Bu nedenle, gizlilikler uygulamanın yapılandırmasına yüklendiğinde iki tire kullanılır ve iki nokta üst üste bulunur.</span><span class="sxs-lookup"><span data-stu-id="f082c-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="f082c-150">Aşağıdaki gizlilikler örnek uygulamayla kullanım içindir.</span><span class="sxs-lookup"><span data-stu-id="f082c-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="f082c-151">Değerler, `_prod` `_dev` Kullanıcı parolalarından geliştirme ortamında yüklenen sonek değerlerinden ayırt etmek için bir sonek içerir.</span><span class="sxs-lookup"><span data-stu-id="f082c-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="f082c-152">`{KEY VAULT NAME}`Önceki adımda oluşturduğunuz anahtar kasasının adıyla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f082c-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="f082c-153">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanın</span><span class="sxs-lookup"><span data-stu-id="f082c-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="f082c-154">**Uygulama Azure dışında barındırıldığı zaman**bir anahtar kasasında kimlik doğrulaması yapmak IÇIN Azure AD, Azure Key Vault ve uygulamayı bir Azure ACTIVE DIRECTORY uygulama kimliği ve X. 509.440 sertifikası kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f082c-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="f082c-155">Daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="f082c-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="f082c-156">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanılması desteklenmekle birlikte, Azure 'da bir uygulama barındırılırken [Azure kaynakları Için Yönetilen kimlikler](#use-managed-identities-for-azure-resources) kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="f082c-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="f082c-157">Yönetilen kimlikler, uygulamada veya geliştirme ortamında bir sertifikanın depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="f082c-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="f082c-158">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki ifade olarak AYARLANDıĞıNDA bir uygulama kimliği ve X. 509.440 sertifikası kullanır `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="f082c-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="f082c-159">PKCS # 12 Arşivi (*. pfx*) sertifikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f082c-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="f082c-160">Sertifika oluşturma seçenekleri Windows ve [OpenSSL](https://www.openssl.org/) [üzerinde MakeCert](/windows/desktop/seccrypto/makecert) içerir.</span><span class="sxs-lookup"><span data-stu-id="f082c-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="f082c-161">Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükler.</span><span class="sxs-lookup"><span data-stu-id="f082c-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="f082c-162">Anahtarı verilebilir olarak işaretlemek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f082c-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="f082c-163">Sertifikanın daha sonra bu işlemde kullanılan parmak izini aklınızda bulunur.</span><span class="sxs-lookup"><span data-stu-id="f082c-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="f082c-164">PKCS # 12 Arşivi (*. pfx*) sertifikasını der kodlu bir sertifika (*. cer*) olarak dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="f082c-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="f082c-165">Uygulamayı Azure AD 'ye kaydedin (**uygulama kayıtları**).</span><span class="sxs-lookup"><span data-stu-id="f082c-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="f082c-166">DER kodlu sertifikayı (*. cer*) Azure AD 'ye yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f082c-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="f082c-167">Azure AD 'de uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="f082c-168">**Sertifikalar & gizli**dizi sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="f082c-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="f082c-169">Ortak anahtarı içeren sertifikayı karşıya yüklemek için **sertifikayı karşıya yükle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="f082c-170">*. Cer*, *. pek*veya *. CRT* sertifikası kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="f082c-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="f082c-171">Anahtar Kasası adı, uygulama KIMLIĞI ve sertifika parmak izini, uygulamanın *appsettings.js* dosyada depolayın.</span><span class="sxs-lookup"><span data-stu-id="f082c-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="f082c-172">Azure portal **ana** kasaları ' ne gidin.</span><span class="sxs-lookup"><span data-stu-id="f082c-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="f082c-173">Azure Key Vault bölümünde, [üretim ortamındaki gizli dizi deposunda](#secret-storage-in-the-production-environment-with-azure-key-vault) oluşturduğunuz anahtar kasasını seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="f082c-174">**Erişim ilkeleri**' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="f082c-175">**Erişim Ilkesi Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="f082c-176">**Gizli izinleri** açın ve uygulamaya **Get** ve **list** izinleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f082c-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="f082c-177">**Sorumlu Seç** ' i seçin ve kayıtlı uygulamayı ada göre seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="f082c-178">**Seç** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="f082c-179">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-179">Select **OK**.</span></span>
1. <span data-ttu-id="f082c-180">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-180">Select **Save**.</span></span>
1. <span data-ttu-id="f082c-181">Uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f082c-181">Deploy the app.</span></span>

<span data-ttu-id="f082c-182">`Certificate`Örnek uygulama, yapılandırma değerlerini, `IConfigurationRoot` parola adı ile aynı adla alır:</span><span class="sxs-lookup"><span data-stu-id="f082c-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="f082c-183">Hiyerarşik olmayan değerler: için değeri `SecretName` ile elde edilir `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="f082c-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="f082c-184">Hiyerarşik değerler (bölümler): `:` (iki nokta üst üste) gösterimini veya `GetSection` genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f082c-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="f082c-185">Yapılandırma değerini elde etmek için şu yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f082c-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="f082c-186">X. 509.440 sertifikası işletim sistemi tarafından yönetiliyor.</span><span class="sxs-lookup"><span data-stu-id="f082c-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="f082c-187">Uygulama, <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings.js* dosya tarafından sağlanan değerlerle çağırır:</span><span class="sxs-lookup"><span data-stu-id="f082c-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="f082c-188">Örnek değerler:</span><span class="sxs-lookup"><span data-stu-id="f082c-188">Example values:</span></span>

* <span data-ttu-id="f082c-189">Anahtar Kasası adı: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="f082c-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="f082c-190">Uygulama KIMLIĞI: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="f082c-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="f082c-191">Sertifika parmak izi: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="f082c-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="f082c-192">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="f082c-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="f082c-193">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="f082c-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="f082c-194">Geliştirme ortamında, gizli anahtar değerleri sonek ile yüklenir `_dev` .</span><span class="sxs-lookup"><span data-stu-id="f082c-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="f082c-195">Üretim ortamında, değerler sonek ile yüklenir `_prod` .</span><span class="sxs-lookup"><span data-stu-id="f082c-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="f082c-196">Azure kaynakları için Yönetilen kimlikler kullanma</span><span class="sxs-lookup"><span data-stu-id="f082c-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="f082c-197">Azure **'a dağıtılan bir uygulama** , [Azure kaynakları için yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanarak uygulamanın, KIMLIK bilgileri (uygulama kimliği ve parola/istemci gizli anahtarı) olmadan Azure AD kimlik doğrulaması kullanarak Azure Key Vault kimlik doğrulamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f082c-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="f082c-198">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki Ifade olarak ayarlandığında Azure kaynakları için Yönetilen kimlikler kullanır `Managed` .</span><span class="sxs-lookup"><span data-stu-id="f082c-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="f082c-199">Dosyanın \* üzerine uygulamanınappsettings.js\* kasa adını girin.</span><span class="sxs-lookup"><span data-stu-id="f082c-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="f082c-200">Örnek uygulama sürüme ayarlandığında bir uygulama KIMLIĞI ve parola (Istemci gizli anahtarı) gerektirmez `Managed` , bu nedenle bu yapılandırma girişlerini yoksayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f082c-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="f082c-201">Uygulama Azure 'a dağıtılır ve Azure, yalnızca *appsettings.json* dosyasında depolanan kasa adını kullanarak Azure Key Vault erişmek için uygulamanın kimliğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="f082c-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="f082c-202">Azure App Service için örnek uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f082c-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="f082c-203">Azure App Service dağıtılan bir uygulama, hizmet oluşturulduğunda Azure AD 'ye otomatik olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f082c-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="f082c-204">Aşağıdaki komutta kullanılmak üzere dağıtımdan nesne KIMLIĞINI edinin.</span><span class="sxs-lookup"><span data-stu-id="f082c-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="f082c-205">Nesne KIMLIĞI, **Identity** App Service panelindeki Azure Portal gösterilir.</span><span class="sxs-lookup"><span data-stu-id="f082c-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="f082c-206">Azure CLı ve uygulamanın nesne KIMLIĞINI kullanarak, uygulama `list` ve `get` anahtar kasasına erişim izinleri sağlayın:</span><span class="sxs-lookup"><span data-stu-id="f082c-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="f082c-207">Azure CLı, PowerShell veya Azure portal kullanarak **uygulamayı yeniden başlatın** .</span><span class="sxs-lookup"><span data-stu-id="f082c-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="f082c-208">Örnek uygulama:</span><span class="sxs-lookup"><span data-stu-id="f082c-208">The sample app:</span></span>

* <span data-ttu-id="f082c-209">`AzureServiceTokenProvider`Bir bağlantı dizesi olmadan sınıfın bir örneğini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f082c-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="f082c-210">Bir bağlantı dizesi sağlanmazsa, sağlayıcı Azure kaynakları için yönetilen kimliklerden bir erişim belirteci almaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="f082c-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="f082c-211"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> `AzureServiceTokenProvider` Örnek belirteci geri çağırması ile yeni bir oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f082c-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="f082c-212"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Örnek, <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> tüm gizli değerleri yükleyen ve çift tire ( `--` ) değerini anahtar adlarında iki nokta () ile değiştirir `:` .</span><span class="sxs-lookup"><span data-stu-id="f082c-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="f082c-213">Anahtar Kasası adı örnek değeri: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="f082c-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="f082c-214">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="f082c-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="f082c-215">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="f082c-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="f082c-216">Geliştirme ortamında, gizli değerler, `_dev` Kullanıcı gizli dizileri tarafından sağlandıklarından, soneke sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f082c-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="f082c-217">Üretim ortamında, değerler `_prod` Azure Key Vault tarafından sağlandıklarından sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="f082c-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="f082c-218">Bir `Access denied` hata alırsanız, uygulamanın Azure AD 'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladıklarını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f082c-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="f082c-219">Azure 'da hizmeti yeniden başlattığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="f082c-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="f082c-220">Sağlayıcıyı yönetilen bir kimlik ve bir Azure DevOps işlem hattı ile kullanma hakkında bilgi için bkz. [yönetilen hizmet KIMLIĞIYLE VM 'ye Azure Resource Manager hizmet bağlantısı oluşturma](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="f082c-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="f082c-221">Yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="f082c-221">Configuration options</span></span>

<span data-ttu-id="f082c-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> şunları kabul edebilir <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :</span><span class="sxs-lookup"><span data-stu-id="f082c-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="f082c-223">Özellik</span><span class="sxs-lookup"><span data-stu-id="f082c-223">Property</span></span>         | <span data-ttu-id="f082c-224">Açıklama</span><span class="sxs-lookup"><span data-stu-id="f082c-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="f082c-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> değerlerini almak için kullanmak üzere.</span><span class="sxs-lookup"><span data-stu-id="f082c-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="f082c-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> gizli dizi yüklemeyi denetlemek için kullanılan örnek.</span><span class="sxs-lookup"><span data-stu-id="f082c-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="f082c-227">`Timespan` anahtar kasasındaki değişiklikleri yoklamaya yönelik denemeler arasında bekleme.</span><span class="sxs-lookup"><span data-stu-id="f082c-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="f082c-228">Varsayılan değer `null` (yapılandırma yeniden yüklenmez).</span><span class="sxs-lookup"><span data-stu-id="f082c-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="f082c-229">Anahtar Kasası URI 'SI.</span><span class="sxs-lookup"><span data-stu-id="f082c-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="f082c-230">Anahtar adı öneki kullanın</span><span class="sxs-lookup"><span data-stu-id="f082c-230">Use a key name prefix</span></span>

<span data-ttu-id="f082c-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, Anahtar Kasası gizli dizilerini yapılandırma anahtarlarına nasıl dönüştürdüğünü denetlemenize olanak tanıyan, uygulamasını kabul eden bir aşırı yükleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="f082c-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="f082c-232">Örneğin, uygulama başlangıcında sağladığınız önek değerine göre gizli değerleri yüklemek için arabirimini uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f082c-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="f082c-233">Bu, örneğin, uygulama sürümüne göre gizli dizileri yüklemeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f082c-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="f082c-234">Birden çok uygulama için gizli dizileri aynı kasaya yerleştirmek veya çevresel gizli dizileri (örneğin, *geliştirme* ve *Üretim* gizlilikleri) aynı kasaya yerleştirmek için Anahtar Kasası gizli dizileri üzerinde ön ekleri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="f082c-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="f082c-235">Farklı uygulama ve geliştirme/üretim ortamlarının, uygulama ortamlarını en yüksek düzeyde güvenlik için yalıtmak üzere ayrı anahtar kasaları kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="f082c-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="f082c-236">Aşağıdaki örnekte, için anahtar kasasında (ve geliştirme ortamı için gizli Yönetim Aracı kullanılarak) bir gizli dizi oluşturulur `5000-AppSecret` (Anahtar Kasası gizli adlarında dönemlere izin verilmez).</span><span class="sxs-lookup"><span data-stu-id="f082c-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="f082c-237">Bu gizli anahtar, uygulamanın 5.0.0.0 sürümü için bir uygulama gizli anahtarı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f082c-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="f082c-238">Uygulamanın başka bir sürümü olan 5.1.0.0, anahtar kasasına (ve gizli Yönetici Aracı kullanılarak) bir gizli dizi eklenir `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="f082c-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="f082c-239">Her bir uygulama sürümü sürümü sürümlü gizli değerini yapılandırma olarak yükler `AppSecret` , bu, gizli anahtarı yüklerken sürümü de kapatıyor.</span><span class="sxs-lookup"><span data-stu-id="f082c-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="f082c-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> özel bir ile çağrılır <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="f082c-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="f082c-241"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Uygulama, doğru gizli anahtarı yapılandırmaya yüklemek için gizli dizi sürüm öneklerine tepki verir:</span><span class="sxs-lookup"><span data-stu-id="f082c-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="f082c-242">`Load` adı önekiyle başladığında bir gizli dizi yükler.</span><span class="sxs-lookup"><span data-stu-id="f082c-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="f082c-243">Diğer gizlilikler yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="f082c-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="f082c-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="f082c-244">`GetKey`:</span></span>
  * <span data-ttu-id="f082c-245">Gizli dizi adından öneki kaldırır.</span><span class="sxs-lookup"><span data-stu-id="f082c-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="f082c-246">`KeyDelimiter`Yapılandırmada kullanılan sınırlayıcı olan (genellikle iki nokta üst üste), herhangi bir ada sahip iki kısa çizgi koyar.</span><span class="sxs-lookup"><span data-stu-id="f082c-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="f082c-247">Azure Key Vault gizli dizi adlarında bir iki nokta üst üste izin vermez.</span><span class="sxs-lookup"><span data-stu-id="f082c-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="f082c-248">`Load`Yöntemi, sürüm ön ekine sahip olanları bulmak için kasa gizli dizileri aracılığıyla yinelenen bir sağlayıcı algoritması tarafından çağırılır.</span><span class="sxs-lookup"><span data-stu-id="f082c-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="f082c-249">İle bir sürüm ön eki bulunduğunda `Load` , algoritma, `GetKey` gizli anahtar adının yapılandırma adını döndürmek için yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="f082c-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="f082c-250">Gizlilik adından sürüm önekini kaldırır ve uygulamanın yapılandırma adı-değer çiftlerine yüklemek için gizli dizi adının geri kalanını döndürür.</span><span class="sxs-lookup"><span data-stu-id="f082c-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="f082c-251">Bu yaklaşım uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="f082c-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="f082c-252">Uygulamanın proje dosyasında belirtilen sürümü.</span><span class="sxs-lookup"><span data-stu-id="f082c-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="f082c-253">Aşağıdaki örnekte, uygulamanın sürümü olarak ayarlanır `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="f082c-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f082c-254">`<UserSecretsId>`Uygulamanın proje dosyasında bir özelliğin var olduğunu, burada Kullanıcı tarafından sağlanan BIR GUID olduğunu onaylayın `{GUID}` :</span><span class="sxs-lookup"><span data-stu-id="f082c-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="f082c-255">[Gizli dizi yöneticisi aracıyla](xref:security/app-secrets)aşağıdaki gizli dizileri yerel olarak kaydedin:</span><span class="sxs-lookup"><span data-stu-id="f082c-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="f082c-256">Gizlilikler, aşağıdaki Azure CLı komutları kullanılarak Azure Key Vault kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="f082c-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="f082c-257">Uygulama çalıştırıldığında, Anahtar Kasası gizli dizileri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="f082c-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="f082c-258">İçin dize gizli dizisi, uygulamanın `5000-AppSecret` Proje dosyasında () belirtilen uygulamanın sürümüyle eşleştirilir `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="f082c-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="f082c-259">Sürüm `5000` (tireyle birlikte), anahtar adından çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="f082c-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="f082c-260">Uygulamanın tamamında, anahtarla yapılandırmayı okumak `AppSecret` gizli değeri yükler.</span><span class="sxs-lookup"><span data-stu-id="f082c-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="f082c-261">Uygulamanın sürümü proje dosyasında olarak değiştirilirse `5.1.0.0` ve uygulama yeniden çalıştırıldığında, döndürülen gizli değer `5.1.0.0_secret_value_dev` geliştirme ortamında ve `5.1.0.0_secret_value_prod` üretimde bulunur.</span><span class="sxs-lookup"><span data-stu-id="f082c-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="f082c-262">Ayrıca, kendi uygulamanızı da sağlayabilirsiniz <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="f082c-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="f082c-263">Özel istemci, uygulama genelinde istemcinin tek bir örneğini paylaşıma izin verir.</span><span class="sxs-lookup"><span data-stu-id="f082c-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="f082c-264">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="f082c-264">Bind an array to a class</span></span>

<span data-ttu-id="f082c-265">Sağlayıcı, bir POCO dizisine bağlamak için yapılandırma değerlerini bir diziye okuyabilme özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f082c-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="f082c-266">Anahtarların iki nokta () ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken `:` , bir diziyi oluşturan anahtarları ( `:0:` ,,) ayırt etmek için bir sayısal anahtar kesimi kullanılır `:1:` &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="f082c-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="f082c-267">Daha fazla bilgi için bkz. [yapılandırma: diziyi bir sınıfa bağlama](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="f082c-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="f082c-268">Azure Key Vault anahtarlar ayırıcı olarak iki nokta üst üste kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="f082c-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="f082c-269">Bu konuda açıklanan yaklaşım, `--` hiyerarşik değerler (bölümler) için bir ayırıcı olarak çift tire () kullanır.</span><span class="sxs-lookup"><span data-stu-id="f082c-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="f082c-270">Dizi anahtarları çift tireler ve sayısal anahtar kesimleri ( `--0--` ,,) ile birlikte Azure Key Vault `--1--` depolanır &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="f082c-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="f082c-271">Bir JSON dosyası tarafından sunulan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f082c-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="f082c-272">`WriteTo`Dizide günlüğe kaydetme hedeflerini açıklayan Iki Serilog girişi yansıtan iki nesne değişmez değeri *sinks*vardır:</span><span class="sxs-lookup"><span data-stu-id="f082c-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="f082c-273">Önceki JSON dosyasında gösterilen yapılandırma, Çift tire ( `--` ) gösterimi ve sayısal kesimleri kullanarak Azure Key Vault depolanır:</span><span class="sxs-lookup"><span data-stu-id="f082c-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="f082c-274">Anahtar</span><span class="sxs-lookup"><span data-stu-id="f082c-274">Key</span></span> | <span data-ttu-id="f082c-275">Değer</span><span class="sxs-lookup"><span data-stu-id="f082c-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="f082c-276">Gizli dizileri yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="f082c-276">Reload secrets</span></span>

<span data-ttu-id="f082c-277">Gizli diziler çağrılana kadar önbelleğe alınır `IConfigurationRoot.Reload()` .</span><span class="sxs-lookup"><span data-stu-id="f082c-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="f082c-278">Anahtar kasasındaki zaman aşımına uğradı, devre dışı ve güncelleştirilmiş gizli dizileri, yürütülene kadar uygulama tarafından dikkate alınmıyor `Reload` .</span><span class="sxs-lookup"><span data-stu-id="f082c-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="f082c-279">Devre dışı ve süre dolma parolaları</span><span class="sxs-lookup"><span data-stu-id="f082c-279">Disabled and expired secrets</span></span>

<span data-ttu-id="f082c-280">Devre dışı ve son kullanma parolası bir oluşturur <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="f082c-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="f082c-281">Uygulamanın üretilmesini engellemek için, farklı bir yapılandırma sağlayıcısı kullanarak yapılandırmayı sağlayın veya devre dışı ya da süre dolma parolasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="f082c-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f082c-282">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="f082c-282">Troubleshoot</span></span>

<span data-ttu-id="f082c-283">Uygulama, sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Core günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır.</span><span class="sxs-lookup"><span data-stu-id="f082c-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="f082c-284">Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:</span><span class="sxs-lookup"><span data-stu-id="f082c-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="f082c-285">Uygulama veya sertifika Azure Active Directory içinde doğru yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="f082c-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="f082c-286">Anahtar Kasası Azure Key Vault içinde yok.</span><span class="sxs-lookup"><span data-stu-id="f082c-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="f082c-287">Uygulamanın anahtar kasasına erişme yetkisi yok.</span><span class="sxs-lookup"><span data-stu-id="f082c-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="f082c-288">Erişim ilkesi `Get` ve `List` izinleri içermez.</span><span class="sxs-lookup"><span data-stu-id="f082c-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="f082c-289">Anahtar kasasında yapılandırma verileri (ad-değer çifti) yanlış olarak adlandırılmış, eksik, devre dışı veya zaman aşımına uğradı.</span><span class="sxs-lookup"><span data-stu-id="f082c-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="f082c-290">Uygulamanın Anahtar Kasası adı ( `KeyVaultName` ), Azure AD uygulama kimliği ( `AzureADApplicationId` ) veya Azure AD sertifika parmak izi ( `AzureADCertThumbprint` ) yanlış.</span><span class="sxs-lookup"><span data-stu-id="f082c-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="f082c-291">Yüklemeye çalıştığınız değer için uygulamada yapılandırma anahtarı (ad) yanlış.</span><span class="sxs-lookup"><span data-stu-id="f082c-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="f082c-292">Uygulama için erişim ilkesini anahtar kasasına eklerken, ilke oluşturulmuştur, ancak **erişim ilkeleri** Kullanıcı arabiriminde **Kaydet** düğmesi seçilmedi.</span><span class="sxs-lookup"><span data-stu-id="f082c-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f082c-293">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f082c-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="f082c-294">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="f082c-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="f082c-295">Microsoft Azure: Key Vault belgeler</span><span class="sxs-lookup"><span data-stu-id="f082c-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="f082c-296">Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma</span><span class="sxs-lookup"><span data-stu-id="f082c-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="f082c-297">KeyVaultClient sınıfı</span><span class="sxs-lookup"><span data-stu-id="f082c-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="f082c-298">Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma</span><span class="sxs-lookup"><span data-stu-id="f082c-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="f082c-299">Öğretici: .NET 'te Azure Windows sanal makinesi ile Azure Key Vault kullanma</span><span class="sxs-lookup"><span data-stu-id="f082c-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f082c-300">Bu belgede, Azure Key Vault gizliliklerden uygulama yapılandırma değerlerini yüklemek için [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) yapılandırma sağlayıcısının nasıl kullanılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f082c-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="f082c-301">Azure Key Vault, uygulama ve hizmetler tarafından kullanılan şifreleme anahtarlarını ve gizli dizileri koruma konusunda yardımcı olan bulut tabanlı bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="f082c-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="f082c-302">ASP.NET Core uygulamalarla Azure Key Vault kullanmaya yönelik yaygın senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="f082c-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="f082c-303">Hassas yapılandırma verilerine erişimi denetleme.</span><span class="sxs-lookup"><span data-stu-id="f082c-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="f082c-304">Yapılandırma verilerini depolarken FIPS 140-2 düzey 2 doğrulanan donanım güvenlik modülleri (HSM 'ler) gereksinimini karşılarsınız.</span><span class="sxs-lookup"><span data-stu-id="f082c-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="f082c-305">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f082c-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="f082c-306">Paketler</span><span class="sxs-lookup"><span data-stu-id="f082c-306">Packages</span></span>

<span data-ttu-id="f082c-307">Microsoft.Extensions.Configyönlendirmeye bir paket başvurusu ekleyin [ . AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) paketi.</span><span class="sxs-lookup"><span data-stu-id="f082c-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="f082c-308">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="f082c-308">Sample app</span></span>

<span data-ttu-id="f082c-309">Örnek uygulama, `#define` *program.cs* dosyasının en üstünde yer aldığı ifadeye göre belirlenen iki moddan birinde çalışır:</span><span class="sxs-lookup"><span data-stu-id="f082c-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="f082c-310">`Certificate`: Azure Key Vault depolanan gizli dizileri erişmek için Azure Key Vault Istemci KIMLIĞI ve X. 509.952 sertifikası kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f082c-310">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="f082c-311">Örneğin bu sürümü, Azure App Service dağıtılan herhangi bir konumdan veya ASP.NET Core uygulamasına hizmet veren herhangi bir konağa çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f082c-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="f082c-312">`Managed`: Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasıyla Azure Key Vault üzere uygulamanın kimliğini doğrulamak için [Azure kaynakları Için yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="f082c-312">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="f082c-313">Kimlik doğrulaması için Yönetilen kimlikler kullanıldığında, bir Azure AD uygulama KIMLIĞI ve parolası (gizli anahtar) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="f082c-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="f082c-314">`Managed`Örneğin sürümünün Azure 'a dağıtılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f082c-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="f082c-315">[Azure kaynakları Için Yönetilen kimlikler bölümünü kullanma](#use-managed-identities-for-azure-resources) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="f082c-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="f082c-316">Önişlemci yönergeleri () kullanarak örnek bir uygulamanın nasıl yapılandırılacağı hakkında daha fazla bilgi için `#define` bkz <xref:index#preprocessor-directives-in-sample-code> ..</span><span class="sxs-lookup"><span data-stu-id="f082c-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="f082c-317">Geliştirme ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="f082c-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="f082c-318">[Gizli dizi Yöneticisi aracını](xref:security/app-secrets)kullanarak gizli dizileri yerel olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f082c-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="f082c-319">Örnek uygulama, geliştirme ortamındaki yerel makinede çalıştığında, gizli anahtar Yöneticisi deposundan gizli diziler yüklenir.</span><span class="sxs-lookup"><span data-stu-id="f082c-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="f082c-320">Gizli dizi Yöneticisi Aracı, `<UserSecretsId>` uygulamanın proje dosyasında bir özellik gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f082c-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="f082c-321">Özellik değerini ( `{GUID}` ) herhangi bir BENZERSIZ GUID 'ye ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="f082c-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="f082c-322">Gizlilikler ad-değer çiftleri olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f082c-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="f082c-323">Hiyerarşik değerler (yapılandırma bölümleri) `:` [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index) anahtarı adlarında ayırıcı olarak bir (iki nokta üst üste) kullanır.</span><span class="sxs-lookup"><span data-stu-id="f082c-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="f082c-324">Gizli dizi, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır; burada `{SECRET NAME}` ad ve `{SECRET VALUE}` değerdir:</span><span class="sxs-lookup"><span data-stu-id="f082c-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="f082c-325">Örnek uygulamanın gizli dizilerini ayarlamak için, projenin [içerik kökünden](xref:fundamentals/index#content-root) bir komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="f082c-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="f082c-326">Bu gizlilikler, [Azure Key Vault bölümündeki üretim ortamındaki gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında Azure Key Vault depolandığında, `_dev` sonek olarak değiştirilir `_prod` .</span><span class="sxs-lookup"><span data-stu-id="f082c-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="f082c-327">Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren bir görsel ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="f082c-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="f082c-328">Azure Key Vault ile üretim ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="f082c-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="f082c-329">Hızlı başlangıç tarafından sunulan yönergeler [: Azure CLI kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-cli) , bir Azure Key Vault oluşturmak ve örnek uygulama tarafından kullanılan gizli dizileri depolamak için burada özetlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="f082c-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="f082c-330">Daha ayrıntılı bilgi için konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="f082c-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="f082c-331">[Azure Portal](https://portal.azure.com/)aşağıdaki yöntemlerden birini kullanarak Azure Cloud Shell 'i açın:</span><span class="sxs-lookup"><span data-stu-id="f082c-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="f082c-332">Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="f082c-333">Metin kutusunda "Azure CLı" arama dizesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f082c-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="f082c-334">**Cloud Shell Başlat** düğmesini kullanarak tarayıcınızda Cloud Shell açın.</span><span class="sxs-lookup"><span data-stu-id="f082c-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="f082c-335">Azure portal sağ üst köşesindeki menüdeki **Cloud Shell** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="f082c-336">Daha fazla bilgi için bkz. [Azure CLI](/cli/azure/) ve [Azure Cloud Shell Genel Bakış](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="f082c-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="f082c-337">Henüz kimlik doğrulamasından geçirilmemişse komutuyla oturum açın `az login` .</span><span class="sxs-lookup"><span data-stu-id="f082c-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="f082c-338">Aşağıdaki komutla bir kaynak grubu oluşturun; burada, `{RESOURCE GROUP NAME}` Yeni kaynak grubu için kaynak grubu adı ve `{LOCATION}` Azure bölgesi (Datacenter) olur:</span><span class="sxs-lookup"><span data-stu-id="f082c-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="f082c-339">Aşağıdaki komutla kaynak grubunda bir Anahtar Kasası oluşturun; burada `{KEY VAULT NAME}` Yeni anahtar kasasının adı ve `{LOCATION}` Azure bölgesi (Datacenter) bulunur:</span><span class="sxs-lookup"><span data-stu-id="f082c-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="f082c-340">Anahtar kasasında ad-değer çiftleri olarak gizli diziler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f082c-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="f082c-341">Azure Key Vault gizli dizi adları, alfasayısal karakterler ve tireler ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="f082c-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="f082c-342">Hiyerarşik değerler (yapılandırma bölümleri) `--` ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="f082c-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="f082c-343">Genellikle [ASP.NET Core yapılandırmasındaki](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlandırmak için kullanılan iki nokta üst üste, Anahtar Kasası gizli adlarında izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="f082c-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="f082c-344">Bu nedenle, gizlilikler uygulamanın yapılandırmasına yüklendiğinde iki tire kullanılır ve iki nokta üst üste bulunur.</span><span class="sxs-lookup"><span data-stu-id="f082c-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="f082c-345">Aşağıdaki gizlilikler örnek uygulamayla kullanım içindir.</span><span class="sxs-lookup"><span data-stu-id="f082c-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="f082c-346">Değerler, `_prod` `_dev` Kullanıcı parolalarından geliştirme ortamında yüklenen sonek değerlerinden ayırt etmek için bir sonek içerir.</span><span class="sxs-lookup"><span data-stu-id="f082c-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="f082c-347">`{KEY VAULT NAME}`Önceki adımda oluşturduğunuz anahtar kasasının adıyla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f082c-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="f082c-348">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanın</span><span class="sxs-lookup"><span data-stu-id="f082c-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="f082c-349">**Uygulama Azure dışında barındırıldığı zaman**bir anahtar kasasında kimlik doğrulaması yapmak IÇIN Azure AD, Azure Key Vault ve uygulamayı bir Azure ACTIVE DIRECTORY uygulama kimliği ve X. 509.440 sertifikası kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f082c-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="f082c-350">Daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="f082c-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="f082c-351">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanılması desteklenmekle birlikte, Azure 'da bir uygulama barındırılırken [Azure kaynakları Için Yönetilen kimlikler](#use-managed-identities-for-azure-resources) kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="f082c-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="f082c-352">Yönetilen kimlikler, uygulamada veya geliştirme ortamında bir sertifikanın depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="f082c-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="f082c-353">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki ifade olarak AYARLANDıĞıNDA bir uygulama kimliği ve X. 509.440 sertifikası kullanır `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="f082c-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="f082c-354">PKCS # 12 Arşivi (*. pfx*) sertifikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f082c-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="f082c-355">Sertifika oluşturma seçenekleri Windows ve [OpenSSL](https://www.openssl.org/) [üzerinde MakeCert](/windows/desktop/seccrypto/makecert) içerir.</span><span class="sxs-lookup"><span data-stu-id="f082c-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="f082c-356">Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükler.</span><span class="sxs-lookup"><span data-stu-id="f082c-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="f082c-357">Anahtarı verilebilir olarak işaretlemek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f082c-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="f082c-358">Sertifikanın daha sonra bu işlemde kullanılan parmak izini aklınızda bulunur.</span><span class="sxs-lookup"><span data-stu-id="f082c-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="f082c-359">PKCS # 12 Arşivi (*. pfx*) sertifikasını der kodlu bir sertifika (*. cer*) olarak dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="f082c-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="f082c-360">Uygulamayı Azure AD 'ye kaydedin (**uygulama kayıtları**).</span><span class="sxs-lookup"><span data-stu-id="f082c-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="f082c-361">DER kodlu sertifikayı (*. cer*) Azure AD 'ye yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f082c-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="f082c-362">Azure AD 'de uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="f082c-363">**Sertifikalar & gizli**dizi sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="f082c-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="f082c-364">Ortak anahtarı içeren sertifikayı karşıya yüklemek için **sertifikayı karşıya yükle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="f082c-365">*. Cer*, *. pek*veya *. CRT* sertifikası kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="f082c-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="f082c-366">Anahtar Kasası adı, uygulama KIMLIĞI ve sertifika parmak izini, uygulamanın *appsettings.js* dosyada depolayın.</span><span class="sxs-lookup"><span data-stu-id="f082c-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="f082c-367">Azure portal **ana** kasaları ' ne gidin.</span><span class="sxs-lookup"><span data-stu-id="f082c-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="f082c-368">Azure Key Vault bölümünde, [üretim ortamındaki gizli dizi deposunda](#secret-storage-in-the-production-environment-with-azure-key-vault) oluşturduğunuz anahtar kasasını seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="f082c-369">**Erişim ilkeleri**' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="f082c-370">**Erişim Ilkesi Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="f082c-371">**Gizli izinleri** açın ve uygulamaya **Get** ve **list** izinleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f082c-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="f082c-372">**Sorumlu Seç** ' i seçin ve kayıtlı uygulamayı ada göre seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="f082c-373">**Seç** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="f082c-374">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-374">Select **OK**.</span></span>
1. <span data-ttu-id="f082c-375">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="f082c-375">Select **Save**.</span></span>
1. <span data-ttu-id="f082c-376">Uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f082c-376">Deploy the app.</span></span>

<span data-ttu-id="f082c-377">`Certificate`Örnek uygulama, yapılandırma değerlerini, `IConfigurationRoot` parola adı ile aynı adla alır:</span><span class="sxs-lookup"><span data-stu-id="f082c-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="f082c-378">Hiyerarşik olmayan değerler: için değeri `SecretName` ile elde edilir `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="f082c-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="f082c-379">Hiyerarşik değerler (bölümler): `:` (iki nokta üst üste) gösterimini veya `GetSection` genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f082c-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="f082c-380">Yapılandırma değerini elde etmek için şu yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f082c-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="f082c-381">X. 509.440 sertifikası işletim sistemi tarafından yönetiliyor.</span><span class="sxs-lookup"><span data-stu-id="f082c-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="f082c-382">Uygulama, <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings.js* dosya tarafından sağlanan değerlerle çağırır:</span><span class="sxs-lookup"><span data-stu-id="f082c-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="f082c-383">Örnek değerler:</span><span class="sxs-lookup"><span data-stu-id="f082c-383">Example values:</span></span>

* <span data-ttu-id="f082c-384">Anahtar Kasası adı: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="f082c-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="f082c-385">Uygulama KIMLIĞI: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="f082c-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="f082c-386">Sertifika parmak izi: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="f082c-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="f082c-387">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="f082c-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="f082c-388">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="f082c-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="f082c-389">Geliştirme ortamında, gizli anahtar değerleri sonek ile yüklenir `_dev` .</span><span class="sxs-lookup"><span data-stu-id="f082c-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="f082c-390">Üretim ortamında, değerler sonek ile yüklenir `_prod` .</span><span class="sxs-lookup"><span data-stu-id="f082c-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="f082c-391">Azure kaynakları için Yönetilen kimlikler kullanma</span><span class="sxs-lookup"><span data-stu-id="f082c-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="f082c-392">Azure **'a dağıtılan bir uygulama** , [Azure kaynakları için yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanarak uygulamanın, KIMLIK bilgileri (uygulama kimliği ve parola/istemci gizli anahtarı) olmadan Azure AD kimlik doğrulaması kullanarak Azure Key Vault kimlik doğrulamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f082c-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="f082c-393">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki Ifade olarak ayarlandığında Azure kaynakları için Yönetilen kimlikler kullanır `Managed` .</span><span class="sxs-lookup"><span data-stu-id="f082c-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="f082c-394">Dosyanın \* üzerine uygulamanınappsettings.js\* kasa adını girin.</span><span class="sxs-lookup"><span data-stu-id="f082c-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="f082c-395">Örnek uygulama sürüme ayarlandığında bir uygulama KIMLIĞI ve parola (Istemci gizli anahtarı) gerektirmez `Managed` , bu nedenle bu yapılandırma girişlerini yoksayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f082c-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="f082c-396">Uygulama Azure 'a dağıtılır ve Azure, yalnızca *appsettings.json* dosyasında depolanan kasa adını kullanarak Azure Key Vault erişmek için uygulamanın kimliğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="f082c-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="f082c-397">Azure App Service için örnek uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f082c-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="f082c-398">Azure App Service dağıtılan bir uygulama, hizmet oluşturulduğunda Azure AD 'ye otomatik olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f082c-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="f082c-399">Aşağıdaki komutta kullanılmak üzere dağıtımdan nesne KIMLIĞINI edinin.</span><span class="sxs-lookup"><span data-stu-id="f082c-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="f082c-400">Nesne KIMLIĞI, **Identity** App Service panelindeki Azure Portal gösterilir.</span><span class="sxs-lookup"><span data-stu-id="f082c-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="f082c-401">Azure CLı ve uygulamanın nesne KIMLIĞINI kullanarak, uygulama `list` ve `get` anahtar kasasına erişim izinleri sağlayın:</span><span class="sxs-lookup"><span data-stu-id="f082c-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="f082c-402">Azure CLı, PowerShell veya Azure portal kullanarak **uygulamayı yeniden başlatın** .</span><span class="sxs-lookup"><span data-stu-id="f082c-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="f082c-403">Örnek uygulama:</span><span class="sxs-lookup"><span data-stu-id="f082c-403">The sample app:</span></span>

* <span data-ttu-id="f082c-404">`AzureServiceTokenProvider`Bir bağlantı dizesi olmadan sınıfın bir örneğini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f082c-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="f082c-405">Bir bağlantı dizesi sağlanmazsa, sağlayıcı Azure kaynakları için yönetilen kimliklerden bir erişim belirteci almaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="f082c-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="f082c-406"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> `AzureServiceTokenProvider` Örnek belirteci geri çağırması ile yeni bir oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f082c-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="f082c-407"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Örnek, <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> tüm gizli değerleri yükleyen ve çift tire ( `--` ) değerini anahtar adlarında iki nokta () ile değiştirir `:` .</span><span class="sxs-lookup"><span data-stu-id="f082c-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="f082c-408">Anahtar Kasası adı örnek değeri: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="f082c-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="f082c-409">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="f082c-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="f082c-410">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="f082c-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="f082c-411">Geliştirme ortamında, gizli değerler, `_dev` Kullanıcı gizli dizileri tarafından sağlandıklarından, soneke sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f082c-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="f082c-412">Üretim ortamında, değerler `_prod` Azure Key Vault tarafından sağlandıklarından sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="f082c-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="f082c-413">Bir `Access denied` hata alırsanız, uygulamanın Azure AD 'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladıklarını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="f082c-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="f082c-414">Azure 'da hizmeti yeniden başlattığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="f082c-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="f082c-415">Sağlayıcıyı yönetilen bir kimlik ve bir Azure DevOps işlem hattı ile kullanma hakkında bilgi için bkz. [yönetilen hizmet KIMLIĞIYLE VM 'ye Azure Resource Manager hizmet bağlantısı oluşturma](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="f082c-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="f082c-416">Anahtar adı öneki kullanın</span><span class="sxs-lookup"><span data-stu-id="f082c-416">Use a key name prefix</span></span>

<span data-ttu-id="f082c-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, Anahtar Kasası gizli dizilerini yapılandırma anahtarlarına nasıl dönüştürdüğünü denetlemenize olanak tanıyan, uygulamasını kabul eden bir aşırı yükleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="f082c-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="f082c-418">Örneğin, uygulama başlangıcında sağladığınız önek değerine göre gizli değerleri yüklemek için arabirimini uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f082c-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="f082c-419">Bu, örneğin, uygulama sürümüne göre gizli dizileri yüklemeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f082c-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="f082c-420">Birden çok uygulama için gizli dizileri aynı kasaya yerleştirmek veya çevresel gizli dizileri (örneğin, *geliştirme* ve *Üretim* gizlilikleri) aynı kasaya yerleştirmek için Anahtar Kasası gizli dizileri üzerinde ön ekleri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="f082c-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="f082c-421">Farklı uygulama ve geliştirme/üretim ortamlarının, uygulama ortamlarını en yüksek düzeyde güvenlik için yalıtmak üzere ayrı anahtar kasaları kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="f082c-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="f082c-422">Aşağıdaki örnekte, için anahtar kasasında (ve geliştirme ortamı için gizli Yönetim Aracı kullanılarak) bir gizli dizi oluşturulur `5000-AppSecret` (Anahtar Kasası gizli adlarında dönemlere izin verilmez).</span><span class="sxs-lookup"><span data-stu-id="f082c-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="f082c-423">Bu gizli anahtar, uygulamanın 5.0.0.0 sürümü için bir uygulama gizli anahtarı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f082c-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="f082c-424">Uygulamanın başka bir sürümü olan 5.1.0.0, anahtar kasasına (ve gizli Yönetici Aracı kullanılarak) bir gizli dizi eklenir `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="f082c-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="f082c-425">Her bir uygulama sürümü sürümü sürümlü gizli değerini yapılandırma olarak yükler `AppSecret` , bu, gizli anahtarı yüklerken sürümü de kapatıyor.</span><span class="sxs-lookup"><span data-stu-id="f082c-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="f082c-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> özel bir ile çağrılır <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="f082c-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="f082c-427"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Uygulama, doğru gizli anahtarı yapılandırmaya yüklemek için gizli dizi sürüm öneklerine tepki verir:</span><span class="sxs-lookup"><span data-stu-id="f082c-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="f082c-428">`Load` adı önekiyle başladığında bir gizli dizi yükler.</span><span class="sxs-lookup"><span data-stu-id="f082c-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="f082c-429">Diğer gizlilikler yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="f082c-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="f082c-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="f082c-430">`GetKey`:</span></span>
  * <span data-ttu-id="f082c-431">Gizli dizi adından öneki kaldırır.</span><span class="sxs-lookup"><span data-stu-id="f082c-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="f082c-432">`KeyDelimiter`Yapılandırmada kullanılan sınırlayıcı olan (genellikle iki nokta üst üste), herhangi bir ada sahip iki kısa çizgi koyar.</span><span class="sxs-lookup"><span data-stu-id="f082c-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="f082c-433">Azure Key Vault gizli dizi adlarında bir iki nokta üst üste izin vermez.</span><span class="sxs-lookup"><span data-stu-id="f082c-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="f082c-434">`Load`Yöntemi, sürüm ön ekine sahip olanları bulmak için kasa gizli dizileri aracılığıyla yinelenen bir sağlayıcı algoritması tarafından çağırılır.</span><span class="sxs-lookup"><span data-stu-id="f082c-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="f082c-435">İle bir sürüm ön eki bulunduğunda `Load` , algoritma, `GetKey` gizli anahtar adının yapılandırma adını döndürmek için yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="f082c-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="f082c-436">Gizlilik adından sürüm önekini kaldırır ve uygulamanın yapılandırma adı-değer çiftlerine yüklemek için gizli dizi adının geri kalanını döndürür.</span><span class="sxs-lookup"><span data-stu-id="f082c-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="f082c-437">Bu yaklaşım uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="f082c-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="f082c-438">Uygulamanın proje dosyasında belirtilen sürümü.</span><span class="sxs-lookup"><span data-stu-id="f082c-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="f082c-439">Aşağıdaki örnekte, uygulamanın sürümü olarak ayarlanır `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="f082c-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f082c-440">`<UserSecretsId>`Uygulamanın proje dosyasında bir özelliğin var olduğunu, burada Kullanıcı tarafından sağlanan BIR GUID olduğunu onaylayın `{GUID}` :</span><span class="sxs-lookup"><span data-stu-id="f082c-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="f082c-441">[Gizli dizi yöneticisi aracıyla](xref:security/app-secrets)aşağıdaki gizli dizileri yerel olarak kaydedin:</span><span class="sxs-lookup"><span data-stu-id="f082c-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="f082c-442">Gizlilikler, aşağıdaki Azure CLı komutları kullanılarak Azure Key Vault kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="f082c-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="f082c-443">Uygulama çalıştırıldığında, Anahtar Kasası gizli dizileri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="f082c-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="f082c-444">İçin dize gizli dizisi, uygulamanın `5000-AppSecret` Proje dosyasında () belirtilen uygulamanın sürümüyle eşleştirilir `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="f082c-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="f082c-445">Sürüm `5000` (tireyle birlikte), anahtar adından çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="f082c-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="f082c-446">Uygulamanın tamamında, anahtarla yapılandırmayı okumak `AppSecret` gizli değeri yükler.</span><span class="sxs-lookup"><span data-stu-id="f082c-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="f082c-447">Uygulamanın sürümü proje dosyasında olarak değiştirilirse `5.1.0.0` ve uygulama yeniden çalıştırıldığında, döndürülen gizli değer `5.1.0.0_secret_value_dev` geliştirme ortamında ve `5.1.0.0_secret_value_prod` üretimde bulunur.</span><span class="sxs-lookup"><span data-stu-id="f082c-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="f082c-448">Ayrıca, kendi uygulamanızı da sağlayabilirsiniz <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="f082c-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="f082c-449">Özel istemci, uygulama genelinde istemcinin tek bir örneğini paylaşıma izin verir.</span><span class="sxs-lookup"><span data-stu-id="f082c-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="f082c-450">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="f082c-450">Bind an array to a class</span></span>

<span data-ttu-id="f082c-451">Sağlayıcı, bir POCO dizisine bağlamak için yapılandırma değerlerini bir diziye okuyabilme özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f082c-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="f082c-452">Anahtarların iki nokta () ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken `:` , bir diziyi oluşturan anahtarları ( `:0:` ,,) ayırt etmek için bir sayısal anahtar kesimi kullanılır `:1:` &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="f082c-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="f082c-453">Daha fazla bilgi için bkz. [yapılandırma: diziyi bir sınıfa bağlama](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="f082c-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="f082c-454">Azure Key Vault anahtarlar ayırıcı olarak iki nokta üst üste kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="f082c-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="f082c-455">Bu konuda açıklanan yaklaşım, `--` hiyerarşik değerler (bölümler) için bir ayırıcı olarak çift tire () kullanır.</span><span class="sxs-lookup"><span data-stu-id="f082c-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="f082c-456">Dizi anahtarları çift tireler ve sayısal anahtar kesimleri ( `--0--` ,,) ile birlikte Azure Key Vault `--1--` depolanır &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="f082c-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="f082c-457">Bir JSON dosyası tarafından sunulan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f082c-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="f082c-458">`WriteTo`Dizide günlüğe kaydetme hedeflerini açıklayan Iki Serilog girişi yansıtan iki nesne değişmez değeri *sinks*vardır:</span><span class="sxs-lookup"><span data-stu-id="f082c-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="f082c-459">Önceki JSON dosyasında gösterilen yapılandırma, Çift tire ( `--` ) gösterimi ve sayısal kesimleri kullanarak Azure Key Vault depolanır:</span><span class="sxs-lookup"><span data-stu-id="f082c-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="f082c-460">Anahtar</span><span class="sxs-lookup"><span data-stu-id="f082c-460">Key</span></span> | <span data-ttu-id="f082c-461">Değer</span><span class="sxs-lookup"><span data-stu-id="f082c-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="f082c-462">Gizli dizileri yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="f082c-462">Reload secrets</span></span>

<span data-ttu-id="f082c-463">Gizli diziler çağrılana kadar önbelleğe alınır `IConfigurationRoot.Reload()` .</span><span class="sxs-lookup"><span data-stu-id="f082c-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="f082c-464">Anahtar kasasındaki zaman aşımına uğradı, devre dışı ve güncelleştirilmiş gizli dizileri, yürütülene kadar uygulama tarafından dikkate alınmıyor `Reload` .</span><span class="sxs-lookup"><span data-stu-id="f082c-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="f082c-465">Devre dışı ve süre dolma parolaları</span><span class="sxs-lookup"><span data-stu-id="f082c-465">Disabled and expired secrets</span></span>

<span data-ttu-id="f082c-466">Devre dışı ve son kullanma parolası bir oluşturur <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="f082c-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="f082c-467">Uygulamanın üretilmesini engellemek için, farklı bir yapılandırma sağlayıcısı kullanarak yapılandırmayı sağlayın veya devre dışı ya da süre dolma parolasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="f082c-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f082c-468">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="f082c-468">Troubleshoot</span></span>

<span data-ttu-id="f082c-469">Uygulama, sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Core günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır.</span><span class="sxs-lookup"><span data-stu-id="f082c-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="f082c-470">Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:</span><span class="sxs-lookup"><span data-stu-id="f082c-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="f082c-471">Uygulama veya sertifika Azure Active Directory içinde doğru yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="f082c-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="f082c-472">Anahtar Kasası Azure Key Vault içinde yok.</span><span class="sxs-lookup"><span data-stu-id="f082c-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="f082c-473">Uygulamanın anahtar kasasına erişme yetkisi yok.</span><span class="sxs-lookup"><span data-stu-id="f082c-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="f082c-474">Erişim ilkesi `Get` ve `List` izinleri içermez.</span><span class="sxs-lookup"><span data-stu-id="f082c-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="f082c-475">Anahtar kasasında yapılandırma verileri (ad-değer çifti) yanlış olarak adlandırılmış, eksik, devre dışı veya zaman aşımına uğradı.</span><span class="sxs-lookup"><span data-stu-id="f082c-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="f082c-476">Uygulamanın Anahtar Kasası adı ( `KeyVaultName` ), Azure AD uygulama kimliği ( `AzureADApplicationId` ) veya Azure AD sertifika parmak izi ( `AzureADCertThumbprint` ) yanlış.</span><span class="sxs-lookup"><span data-stu-id="f082c-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="f082c-477">Yüklemeye çalıştığınız değer için uygulamada yapılandırma anahtarı (ad) yanlış.</span><span class="sxs-lookup"><span data-stu-id="f082c-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="f082c-478">Uygulama için erişim ilkesini anahtar kasasına eklerken, ilke oluşturulmuştur, ancak **erişim ilkeleri** Kullanıcı arabiriminde **Kaydet** düğmesi seçilmedi.</span><span class="sxs-lookup"><span data-stu-id="f082c-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f082c-479">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f082c-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="f082c-480">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="f082c-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="f082c-481">Microsoft Azure: Key Vault belgeler</span><span class="sxs-lookup"><span data-stu-id="f082c-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="f082c-482">Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma</span><span class="sxs-lookup"><span data-stu-id="f082c-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="f082c-483">KeyVaultClient sınıfı</span><span class="sxs-lookup"><span data-stu-id="f082c-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="f082c-484">Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma</span><span class="sxs-lookup"><span data-stu-id="f082c-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="f082c-485">Öğretici: .NET 'te Azure Windows sanal makinesi ile Azure Key Vault kullanma</span><span class="sxs-lookup"><span data-stu-id="f082c-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

