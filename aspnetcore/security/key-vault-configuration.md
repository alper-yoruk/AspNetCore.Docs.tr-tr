---
title: ASP.NET Core'da Azure Key Vault Yapılandırma Sağlayıcısı
author: rick-anderson
description: Çalışma zamanında yüklenen ad değeri çiftleri kullanarak bir uygulamayı yapılandırmak için Azure Key Vault Yapılandırma Sağlayıcısı'nı nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228172"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="23f41-103">ASP.NET Core'da Azure Key Vault Yapılandırma Sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="23f41-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="23f41-104">Yazar: [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="23f41-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="23f41-105">Bu belge, Uygulama yapılandırma değerlerini Azure Key Vault sırlarından yüklemek için [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Yapılandırma Sağlayıcısı'nın nasıl kullanılacağını açıklar.</span><span class="sxs-lookup"><span data-stu-id="23f41-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="23f41-106">Azure Key Vault, uygulamalar ve hizmetler tarafından kullanılan şifreleme anahtarlarının ve sırların korunmasına yardımcı olan bulut tabanlı bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="23f41-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="23f41-107">Azure Key Vault'u ASP.NET Core uygulamalarıyla kullanmak için sık karşılaşılan senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="23f41-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="23f41-108">Hassas yapılandırma verilerine erişimi denetleme.</span><span class="sxs-lookup"><span data-stu-id="23f41-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="23f41-109">Yapılandırma verilerini depolarken FIPS 140-2 Düzey 2 doğrulanmış Donanım Güvenlik Modülleri (HSM'ler) gereksinimini karşılar.</span><span class="sxs-lookup"><span data-stu-id="23f41-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="23f41-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="23f41-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="23f41-111">Paketler</span><span class="sxs-lookup"><span data-stu-id="23f41-111">Packages</span></span>

<span data-ttu-id="23f41-112">[Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23f41-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="23f41-113">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="23f41-113">Sample app</span></span>

<span data-ttu-id="23f41-114">Örnek uygulama, `#define` *Program.cs* dosyasının üst kısmındaki ifadeyle belirlenen iki moddan birinde çalışır:</span><span class="sxs-lookup"><span data-stu-id="23f41-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="23f41-115">`Certificate`&ndash; Azure Key Vault'ta depolanan sırlara erişmek için Azure Key Vault İstemci Kimliği ve X.509 sertifikası kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="23f41-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="23f41-116">Örneğin bu sürümü herhangi bir konumdan çalıştırılabilir, Azure Uygulama Hizmeti'ne veya ASP.NET Core uygulamasına hizmet verebilecek herhangi bir ana bilgisayara dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="23f41-117">`Managed`&ndash; Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulaması ile azure anahtar atlamasına kimlik doğrulamak [için Azure kaynakları için Yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="23f41-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="23f41-118">Kimlik doğrulaması için yönetilen kimlikleri kullanırken, Azure AD Uygulama Kimliği ve Parola (İstemci Sırrı) gerekmez.</span><span class="sxs-lookup"><span data-stu-id="23f41-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="23f41-119">Örneğin `Managed` sürümü Azure'a dağıtılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="23f41-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="23f41-120">[Azure kaynakları için Yönetilen Kimlikleri Kullan](#use-managed-identities-for-azure-resources) bölümündeki kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="23f41-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="23f41-121">Örnek bir uygulamanın önişlemci yönergelerini kullanarak nasıl`#define`yapılandırılacakları hakkında daha fazla bilgi için ( <xref:index#preprocessor-directives-in-sample-code>bkz.</span><span class="sxs-lookup"><span data-stu-id="23f41-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="23f41-122">Geliştirme ortamında gizli depolama</span><span class="sxs-lookup"><span data-stu-id="23f41-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="23f41-123">[Gizli Yönetici aracını](xref:security/app-secrets)kullanarak sırları yerel olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="23f41-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="23f41-124">Örnek uygulama Geliştirme ortamındayerel makinede çalıştığında, sırlar yerel Secret Manager mağazasından yüklenir.</span><span class="sxs-lookup"><span data-stu-id="23f41-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="23f41-125">Gizli Yönetici aracı, `<UserSecretsId>` uygulamanın proje dosyasında bir özellik gerektirir.</span><span class="sxs-lookup"><span data-stu-id="23f41-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="23f41-126">Özellik değerini (`{GUID}`) herhangi bir benzersiz GUID'e ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="23f41-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="23f41-127">Sırlar ad değeri çiftleri olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="23f41-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="23f41-128">Hiyerarşik değerler (yapılandırma bölümleri), Core yapılandırma anahtar adlarında `:` [ASP.NET](xref:fundamentals/configuration/index) ayırıcı olarak (üst üste) kullanır.</span><span class="sxs-lookup"><span data-stu-id="23f41-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="23f41-129">Gizli Yönetici, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır `{SECRET VALUE}` , adı ve değeri: `{SECRET NAME}`</span><span class="sxs-lookup"><span data-stu-id="23f41-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="23f41-130">Örnek uygulamanın sırlarını ayarlamak için projenin [içerik kökünden](xref:fundamentals/index#content-root) komut kabuğunda aşağıdaki komutları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="23f41-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="23f41-131">Bu sırlar Azure Key Vault bölümünden [Üretim ortamındaki Gizli depolama alanında](#secret-storage-in-the-production-environment-with-azure-key-vault) `_dev` Azure Key Vault'ta depolandığında, sonek ' olarak `_prod`değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="23f41-132">Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren görsel bir ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="23f41-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="23f41-133">Azure Key Vault ile Üretim ortamında gizli depolama</span><span class="sxs-lookup"><span data-stu-id="23f41-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="23f41-134">Quickstart tarafından sağlanan yönergeler: Azure CLI konusunu [kullanarak Azure Key Vault'tan bir sır ayarlayın ve alın,](/azure/key-vault/quick-create-cli) azure anahtar kasası oluşturmak ve örnek uygulama tarafından kullanılan sırları depolamak için burada özetlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="23f41-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="23f41-135">Daha fazla bilgi için konuya bakın.</span><span class="sxs-lookup"><span data-stu-id="23f41-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="23f41-136">[Azure portalında](https://portal.azure.com/)aşağıdaki yöntemlerden herhangi birini kullanarak Azure Bulutu'nu açın:</span><span class="sxs-lookup"><span data-stu-id="23f41-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="23f41-137">Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="23f41-138">Metin kutusundaki "Azure CLI" arama dizesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="23f41-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="23f41-139">**Başlat Bulut Shell** düğmesiyle tarayıcınızda Cloud Shell'i açın.</span><span class="sxs-lookup"><span data-stu-id="23f41-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="23f41-140">Azure portalının sağ üst köşesindeki menüdeki **Bulut Kabuğu** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="23f41-141">Daha fazla bilgi için Azure [CLI](/cli/azure/) ve [Azure Bulut BulutU'na Genel Bakış](/azure/cloud-shell/overview)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="23f41-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="23f41-142">Kimliğinizi doğrulamıyorsanız, `az login` komutla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="23f41-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="23f41-143">Aşağıdaki komuta sahip bir kaynak `{RESOURCE GROUP NAME}` grubu oluşturun, yeni kaynak grubunun `{LOCATION}` kaynak grubu adı ve Azure bölgesi (veri merkezi):</span><span class="sxs-lookup"><span data-stu-id="23f41-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="23f41-144">Kaynak grubunda, yeni anahtar kasasının `{KEY VAULT NAME}` `{LOCATION}` adı ve Azure bölgesinin (veri merkezi) bulunduğu aşağıdaki komutla bir anahtar kasası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="23f41-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="23f41-145">Anahtar kasasında ad değeri çiftleri olarak sırlar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="23f41-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="23f41-146">Azure Key Vault gizli adları alfasayısal karakterler ve tirelerle sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="23f41-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="23f41-147">Hiyerarşik değerler (yapılandırma `--` bölümleri) ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="23f41-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="23f41-148">Normalde [ASP.NET Core yapılandırmasında](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlamak için kullanılan üst üste, anahtar kasası gizli adlarına izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="23f41-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="23f41-149">Bu nedenle, iki tire kullanılır ve sırları uygulamanın yapılandırmasına yüklendiğinde bir iki nokta üst üste takas edilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="23f41-150">Aşağıdaki sırlar örnek uygulama ile kullanım içindir.</span><span class="sxs-lookup"><span data-stu-id="23f41-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="23f41-151">Değerler, Geliştirme `_prod` ortamında yüklenen sonek `_dev` değerlerinden Kullanıcı Sırları'ndan ayırt etmek için bir sonek içerir.</span><span class="sxs-lookup"><span data-stu-id="23f41-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="23f41-152">Önceki `{KEY VAULT NAME}` adımda oluşturduğunuz anahtar tonozunun adı ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="23f41-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="23f41-153">Azure'da barındırılan olmayan uygulamalar için Uygulama Kimliği ve X.509 sertifikasını kullanma</span><span class="sxs-lookup"><span data-stu-id="23f41-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="23f41-154">Azure AD, Azure Anahtar Kasası ve uygulamayı, **uygulama Azure dışında barındırıldığında**önemli bir kasaya kimlik doğrulamak için Azure Active Directory Application ID ve X.509 sertifikasını kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="23f41-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="23f41-155">Daha fazla bilgi için [anahtarlar, sırlar ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates)bilgi alabiliyorum.</span><span class="sxs-lookup"><span data-stu-id="23f41-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="23f41-156">Azure'da barındırılan uygulamalar için Uygulama Kimliği ve X.509 sertifikası kullanılması desteklenmiş olsa da, Azure'da bir uygulamayı barındırırken [Azure kaynakları için Yönetilen kimlikleri](#use-managed-identities-for-azure-resources) kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="23f41-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="23f41-157">Yönetilen kimlikler, uygulamada veya geliştirme ortamında sertifika depolamayı gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="23f41-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="23f41-158">Örnek uygulama, `#define` *Program.cs* dosyasının `Certificate`üst kısmındaki ifade .</span><span class="sxs-lookup"><span data-stu-id="23f41-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="23f41-159">Bir PKCS#12 arşivi (*.pfx*) sertifikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="23f41-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="23f41-160">Sertifika oluşturma seçenekleri arasında [Windows'ta MakeCert](/windows/desktop/seccrypto/makecert) ve [OpenSSL](https://www.openssl.org/)yer almaktadır.</span><span class="sxs-lookup"><span data-stu-id="23f41-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="23f41-161">Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükleyin.</span><span class="sxs-lookup"><span data-stu-id="23f41-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="23f41-162">Anahtarı dışa aktarılabilir olarak işaretlemek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="23f41-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="23f41-163">Sertifikanın bu işlemin ilerleyen saatlerinde kullanılan parmak izine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="23f41-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="23f41-164">PKCS#12 arşivini (*.pfx*) sertifikasını DER kodlu sertifika (*.cer)* olarak dışa aktarın.</span><span class="sxs-lookup"><span data-stu-id="23f41-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="23f41-165">Uygulamayı Azure AD **(Uygulama kayıtları)** ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="23f41-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="23f41-166">DER kodlu sertifikayı (*.cer*) Azure AD'ye yükleyin:</span><span class="sxs-lookup"><span data-stu-id="23f41-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="23f41-167">Azure AD'de uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="23f41-168">**Sertifikalar & sırları**gidin.</span><span class="sxs-lookup"><span data-stu-id="23f41-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="23f41-169">Ortak anahtarı içeren sertifikayı yüklemek için **Yükleme sertifikasını** seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="23f41-170">Bir *.cer*, *.pem*, veya *.crt* sertifikası kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="23f41-171">Anahtar kasa adını, Uygulama Kimliğini ve sertifika parmak izini uygulamanın *appsettings.json* dosyasında saklayın.</span><span class="sxs-lookup"><span data-stu-id="23f41-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="23f41-172">Azure portalındaki **Anahtar kasalarına** gidin.</span><span class="sxs-lookup"><span data-stu-id="23f41-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="23f41-173">[Azure Key Vault bölümüyle Üretim ortamındaki Gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında oluşturduğunuz anahtar kasasını seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="23f41-174">**Erişim ilkeleri**'ni seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="23f41-175">**Erişim İlkesi Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="23f41-176">**Gizli izinleri** açın ve uygulamaya **Al** ve **Liste** izinleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="23f41-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="23f41-177">**Ana para seçin** ve kayıtlı uygulamayı ada göre seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="23f41-178">**Seç** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="23f41-179">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-179">Select **OK**.</span></span>
1. <span data-ttu-id="23f41-180">**Kaydet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-180">Select **Save**.</span></span>
1. <span data-ttu-id="23f41-181">Uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="23f41-181">Deploy the app.</span></span>

<span data-ttu-id="23f41-182">Örnek `Certificate` uygulama yapılandırma değerlerini gizli `IConfigurationRoot` adla aynı ada sahip olarak alır:</span><span class="sxs-lookup"><span data-stu-id="23f41-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="23f41-183">Hiyerarşik olmayan değerler: Değer `SecretName` . `config["SecretName"]`</span><span class="sxs-lookup"><span data-stu-id="23f41-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="23f41-184">Hiyerarşik değerler (bölümler): (iki nokta `GetSection` üst üste) gösterimini veya uzantı yöntemini kullanın. `:`</span><span class="sxs-lookup"><span data-stu-id="23f41-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="23f41-185">Yapılandırma değerini elde etmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="23f41-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="23f41-186">X.509 sertifikası işletim sistemi tarafından yönetilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="23f41-187">Uygulama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings.json* dosyası tarafından sağlanan değerleri çağırır:</span><span class="sxs-lookup"><span data-stu-id="23f41-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="23f41-188">Örnek değerler:</span><span class="sxs-lookup"><span data-stu-id="23f41-188">Example values:</span></span>

* <span data-ttu-id="23f41-189">Anahtar kasa adı:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="23f41-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="23f41-190">Başvuru Kimliği:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="23f41-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="23f41-191">Sertifika parmak izi:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="23f41-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="23f41-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="23f41-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="23f41-193">Uygulamayı çalıştırdığınızda, bir web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="23f41-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="23f41-194">Geliştirme ortamında, gizli değerler `_dev` sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="23f41-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="23f41-195">Üretim ortamında, değerler `_prod` sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="23f41-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="23f41-196">Azure kaynakları için Yönetilen kimlikleri kullanma</span><span class="sxs-lookup"><span data-stu-id="23f41-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="23f41-197">**Azure'a dağıtılan bir uygulama,** Azure [kaynakları için Yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanabilir ve bu da uygulamanın uygulamada depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasını (Uygulama Kimliği ve Parola/İstemci Gizli) kullanarak Azure Anahtar Kasası ile kimlik doğrulamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="23f41-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="23f41-198">Örnek uygulama, *Program.cs* dosyasının üst `#define` kısmındaki ifade . `Managed`</span><span class="sxs-lookup"><span data-stu-id="23f41-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="23f41-199">Uygulamanın *appsettings.json* dosyasına kasa adını girin.</span><span class="sxs-lookup"><span data-stu-id="23f41-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="23f41-200">Örnek `Managed` uygulama, sürüme ayarlandığında Uygulama Kimliği ve Parola (İstemci Gizli) gerektirmez, bu nedenle bu yapılandırma girişlerini yok sayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="23f41-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="23f41-201">Uygulama Azure'a dağıtılır ve Azure, *uygulama ayarları.json* dosyasında depolanan kasa adını kullanarak Azure Key Vault'a erişmek için uygulamanın kimliğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="23f41-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="23f41-202">Örnek uygulamayı Azure Uygulama Hizmeti'ne dağıtın.</span><span class="sxs-lookup"><span data-stu-id="23f41-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="23f41-203">Hizmet oluşturulduğunda Azure Uygulama Hizmeti'ne dağıtılan bir uygulama otomatik olarak Azure AD'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="23f41-204">Aşağıdaki komutta kullanılmak üzere dağıtımdan Nesne Kimliği'ni edinin.</span><span class="sxs-lookup"><span data-stu-id="23f41-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="23f41-205">Nesne Kimliği, Uygulama Hizmetinin **Kimlik** panelindeki Azure portalında gösterilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="23f41-206">Azure CLI'yi ve uygulamanın Nesne Kimliğini `list` kullanarak, uygulamaya anahtar kasasına erişmek için izin ler sağlayın: `get`</span><span class="sxs-lookup"><span data-stu-id="23f41-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="23f41-207">Uygulamayı Azure CLI, PowerShell veya Azure portalını kullanarak **yeniden başlatın.**</span><span class="sxs-lookup"><span data-stu-id="23f41-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="23f41-208">Örnek uygulama:</span><span class="sxs-lookup"><span data-stu-id="23f41-208">The sample app:</span></span>

* <span data-ttu-id="23f41-209">Bağlantı dizesi `AzureServiceTokenProvider` olmayan sınıfın bir örneğini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="23f41-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="23f41-210">Bağlantı dizesi sağlanmadığı zaman, sağlayıcı Azure kaynakları için Yönetilen kimliklerden bir erişim belirteci elde etmeye çalışır.</span><span class="sxs-lookup"><span data-stu-id="23f41-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="23f41-211">Örnek <xref:Microsoft.Azure.KeyVault.KeyVaultClient> çağrı geri aramaile birlikte yeni bir oluşturulur. `AzureServiceTokenProvider`</span><span class="sxs-lookup"><span data-stu-id="23f41-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="23f41-212">Örnek, <xref:Microsoft.Azure.KeyVault.KeyVaultClient> tüm gizli değerleri <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> yükler ve anahtar adlarında iki noktalı`--`( ) ile`:`çift tireli ( ) değiştirir varsayılan bir uygulama ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="23f41-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="23f41-213">Anahtar kasa sıcadı örnek değeri:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="23f41-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="23f41-214">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="23f41-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="23f41-215">Uygulamayı çalıştırdığınızda, bir web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="23f41-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="23f41-216">Geliştirme ortamında, kullanıcı sırları `_dev` tarafından sağlandığı için gizli değerler sonek ekine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="23f41-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="23f41-217">Üretim ortamında, değerler `_prod` Azure Anahtar Kasası tarafından sağlandığı için sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="23f41-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="23f41-218">Bir `Access denied` hata alırsanız, uygulamanın Azure AD'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="23f41-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="23f41-219">Hizmeti Azure'da yeniden başlattığınızı doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="23f41-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="23f41-220">Sağlayıcının yönetilen bir kimliğe ve Azure DevOps ardışık kuruluşuna sahip kullanımı hakkında bilgi [için](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)bkz.</span><span class="sxs-lookup"><span data-stu-id="23f41-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="23f41-221">Yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="23f41-221">Configuration options</span></span>

<span data-ttu-id="23f41-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>bir <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>kabul edebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="23f41-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="23f41-223">Özellik</span><span class="sxs-lookup"><span data-stu-id="23f41-223">Property</span></span>         | <span data-ttu-id="23f41-224">Açıklama</span><span class="sxs-lookup"><span data-stu-id="23f41-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="23f41-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>değerleri almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="23f41-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="23f41-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>örnek gizli yükleme kontrol etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="23f41-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="23f41-227">`Timespan`değişiklikler için anahtar kasa yoklama girişimleri arasında beklemek.</span><span class="sxs-lookup"><span data-stu-id="23f41-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="23f41-228">Varsayılan değer `null` (yapılandırma yeniden yüklenmez).</span><span class="sxs-lookup"><span data-stu-id="23f41-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="23f41-229">Anahtar kasa sıyrık URI.</span><span class="sxs-lookup"><span data-stu-id="23f41-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="23f41-230">Anahtar adı öneki kullanma</span><span class="sxs-lookup"><span data-stu-id="23f41-230">Use a key name prefix</span></span>

<span data-ttu-id="23f41-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>anahtar kasa sırlarını yapılandırma tuşlarına <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>nasıl dönüştürüldüğünü kontrol etmenizi sağlayan bir uygulamayı kabul eden aşırı yük sağlar.</span><span class="sxs-lookup"><span data-stu-id="23f41-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="23f41-232">Örneğin, uygulama başlangıç sırasında sağladığınız bir önek değerine göre gizli değerleri yüklemek için arabirimi uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="23f41-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="23f41-233">Bu, örneğin, uygulamanın sürümüne göre sırları yüklemenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="23f41-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="23f41-234">Birden çok uygulamanın sırlarını aynı anahtar kasaya yerleştirmek veya çevresel sırları (örneğin *geliştirme* ve *üretim* sırları) aynı kasaya yerleştirmek için anahtar kasa sırlarındaki önekleri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="23f41-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="23f41-235">Farklı uygulamaların ve geliştirme/üretim ortamlarının, uygulama ortamlarını en üst düzeyde güvenlik için yalıtmak için ayrı anahtar kasaları kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="23f41-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="23f41-236">Aşağıdaki örnekte, anahtar kasasında `5000-AppSecret` (ve Geliştirme ortamı için Gizli Yönetici aracı kullanılarak) (anahtar kasa gizli adlarında dönemlere izin verilmez) bir sır oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="23f41-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="23f41-237">Bu gizli sürüm 5.0.0.0 için bir uygulama gizli temsil eder.</span><span class="sxs-lookup"><span data-stu-id="23f41-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="23f41-238">Uygulamanın başka bir sürümü için, 5.1.0.0, bir sır için anahtar tonoz `5100-AppSecret`(ve Secret Manager aracı kullanılarak) eklenir.</span><span class="sxs-lookup"><span data-stu-id="23f41-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="23f41-239">Her uygulama sürümü, sürümdeki gizli değerini `AppSecret`yapılandırmasına yükler, sırrı yüklerken sürümü sıyırır.</span><span class="sxs-lookup"><span data-stu-id="23f41-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="23f41-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>bir özel <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>ile denir:</span><span class="sxs-lookup"><span data-stu-id="23f41-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="23f41-241">Uygulama, <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> uygun sırrı yapılandırmaya yüklemek için sırların sürüm öneklerine tepki verir:</span><span class="sxs-lookup"><span data-stu-id="23f41-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="23f41-242">`Load`adı önek ile başladığında bir sır yükler.</span><span class="sxs-lookup"><span data-stu-id="23f41-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="23f41-243">Diğer sırlar dolu değil.</span><span class="sxs-lookup"><span data-stu-id="23f41-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="23f41-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="23f41-244">`GetKey`:</span></span>
  * <span data-ttu-id="23f41-245">Öneki gizli addan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="23f41-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="23f41-246">Yapılandırmada kullanılan sınır dışı layıcı `KeyDelimiter`(genellikle bir üst üste) olan herhangi bir addaki iki tireyi değiştirir.</span><span class="sxs-lookup"><span data-stu-id="23f41-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="23f41-247">Azure Anahtar Kasası gizli adlarda üst üste izin vermez.</span><span class="sxs-lookup"><span data-stu-id="23f41-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="23f41-248">Yöntem, `Load` sürüm önekine sahip olanları bulmak için kasa sırlarını yineleyen bir sağlayıcı algoritması tarafından çağrılır.</span><span class="sxs-lookup"><span data-stu-id="23f41-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="23f41-249">Bir sürüm öneki ile `Load`bulunduğunda, `GetKey` algoritma gizli adın yapılandırma adını döndürmek için yöntemi kullanır.</span><span class="sxs-lookup"><span data-stu-id="23f41-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="23f41-250">Gizlinin adından sürüm önekini siler ve uygulamanın yapılandırma adı değeri çiftleri içine yüklemek için gizli adın geri kalanını döndürür.</span><span class="sxs-lookup"><span data-stu-id="23f41-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="23f41-251">Bu yaklaşım uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="23f41-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="23f41-252">Uygulamanın proje dosyasında belirtilen uygulamanın sürümü.</span><span class="sxs-lookup"><span data-stu-id="23f41-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="23f41-253">Aşağıdaki örnekte, uygulamanın sürümü aşağıdaki `5.0.0.0`şekilde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="23f41-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="23f41-254">Uygulamanın `<UserSecretsId>` proje dosyasında bir özelliğin bulunduğunu `{GUID}` ve kullanıcı tarafından sağlanan GUID'in bulunduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="23f41-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="23f41-255">Aşağıdaki sırları Gizli Yönetici [aracıyla](xref:security/app-secrets)yerel olarak kaydedin:</span><span class="sxs-lookup"><span data-stu-id="23f41-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="23f41-256">Sırlar Aşağıdaki Azure CLI komutları kullanılarak Azure Anahtar Kasası'na kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="23f41-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="23f41-257">Uygulama çalıştırıldığında, anahtar kasa sırları yüklenir.</span><span class="sxs-lookup"><span data-stu-id="23f41-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="23f41-258">String `5000-AppSecret` secret, uygulamanın proje dosyasında belirtilen uygulamanın sürümüyle eşleşir (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="23f41-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="23f41-259">Sürüm, `5000` (tire ile), anahtar adından çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="23f41-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="23f41-260">Uygulama boyunca, anahtarla `AppSecret` yapılandırmayı okuma gizli değeri yükler.</span><span class="sxs-lookup"><span data-stu-id="23f41-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="23f41-261">Uygulamanın sürümü proje dosyasında `5.1.0.0` değiştirilirse ve uygulama yeniden çalıştırılırsa, döndürülen gizli değer Geliştirme ortamında ve `5.1.0.0_secret_value_dev` `5.1.0.0_secret_value_prod` Üretim'de olur.</span><span class="sxs-lookup"><span data-stu-id="23f41-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="23f41-262">Ayrıca kendi <xref:Microsoft.Azure.KeyVault.KeyVaultClient> uygulama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="23f41-263">Özel bir istemci, uygulama genelinde istemcinin tek bir örneğini paylaşmaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="23f41-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="23f41-264">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="23f41-264">Bind an array to a class</span></span>

<span data-ttu-id="23f41-265">Sağlayıcı, bir POCO dizisine bağlanmak için yapılandırma değerlerini bir dizihalinde okuyabiliyor.</span><span class="sxs-lookup"><span data-stu-id="23f41-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="23f41-266">Tuşların iki nokta üst üste (`:`) ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken,`:0:`bir `:1:` &hellip; `:{n}:`diziyi oluşturan anahtarları ayırt etmek için sayısal bir anahtar kesimi kullanılır .</span><span class="sxs-lookup"><span data-stu-id="23f41-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="23f41-267">Daha fazla bilgi için yapılandırma: [Bir diziyi sınıfa bağlayın.](xref:fundamentals/configuration/index#bind-an-array-to-a-class)</span><span class="sxs-lookup"><span data-stu-id="23f41-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="23f41-268">Azure Anahtar Kasası tuşları ayırıcı olarak üst üste kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="23f41-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="23f41-269">Bu konuda açıklanan yaklaşım, hiyerarşik`--`değerler (bölümler) için ayırıcı olarak çift tire ( ) kullanır.</span><span class="sxs-lookup"><span data-stu-id="23f41-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="23f41-270">Dizi anahtarları, çift tireli ve sayısal anahtar segmentleri`--0--`(, , `--1--` &hellip; `--{n}--`, ) ile Azure Key Vault'ta depolanır.</span><span class="sxs-lookup"><span data-stu-id="23f41-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="23f41-271">JSON dosyası tarafından sağlanan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="23f41-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="23f41-272">`WriteTo` Dizide tanımlanan ve günlüğe kaydetme için hedefleri açıklayan iki Serilog *lavabosu*olan iki nesne literals vardır:</span><span class="sxs-lookup"><span data-stu-id="23f41-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="23f41-273">Önceki JSON dosyasında gösterilen yapılandırma, çift çizgi (`--`) gösterimi ve sayısal segmentler kullanılarak Azure Key Vault'ta depolanır:</span><span class="sxs-lookup"><span data-stu-id="23f41-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="23f41-274">Anahtar</span><span class="sxs-lookup"><span data-stu-id="23f41-274">Key</span></span> | <span data-ttu-id="23f41-275">Değer</span><span class="sxs-lookup"><span data-stu-id="23f41-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="23f41-276">Sırları yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="23f41-276">Reload secrets</span></span>

<span data-ttu-id="23f41-277">Sırlar çağrılana `IConfigurationRoot.Reload()` kadar önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="23f41-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="23f41-278">Anahtar kasasında süresi dolmuş, devre dışı bırakılmış ve `Reload` güncelleştirilmiş sırlar yürütülene kadar uygulama tarafından saygı duyulmuyor.</span><span class="sxs-lookup"><span data-stu-id="23f41-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="23f41-279">Devre dışı bırakılmış ve süresi dolmuş sırlar</span><span class="sxs-lookup"><span data-stu-id="23f41-279">Disabled and expired secrets</span></span>

<span data-ttu-id="23f41-280">Devre dışı bırakılmış <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>ve süresi dolmuş sırlar bir atmak .</span><span class="sxs-lookup"><span data-stu-id="23f41-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="23f41-281">Uygulamanın atmasını önlemek için, yapılandırmayı farklı bir yapılandırma sağlayıcısı kullanarak sağlayın veya devre dışı bırakılan veya süresi dolmuş sırrı güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="23f41-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="23f41-282">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="23f41-282">Troubleshoot</span></span>

<span data-ttu-id="23f41-283">Uygulama sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Çekirdek Günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır.</span><span class="sxs-lookup"><span data-stu-id="23f41-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="23f41-284">Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:</span><span class="sxs-lookup"><span data-stu-id="23f41-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="23f41-285">Uygulama veya sertifika Azure Etkin Dizini'nde doğru şekilde yapılandırılmamıştır.</span><span class="sxs-lookup"><span data-stu-id="23f41-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="23f41-286">Anahtar kasası Azure Key Vault'ta yok.</span><span class="sxs-lookup"><span data-stu-id="23f41-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="23f41-287">Uygulamanın anahtar kasasına erişme yetkisi yok.</span><span class="sxs-lookup"><span data-stu-id="23f41-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="23f41-288">Erişim ilkesi, izinleri `Get` `List` içermez ve içermez.</span><span class="sxs-lookup"><span data-stu-id="23f41-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="23f41-289">Anahtar kasasında, yapılandırma verileri (ad değeri çifti) yanlış adlandırılır, eksik, devre dışı bırakılır veya süresi dolmuş olur.</span><span class="sxs-lookup"><span data-stu-id="23f41-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="23f41-290">Uygulama, yanlış anahtar kasa`KeyVaultName`adı ( ),`AzureADApplicationId`Azure AD Application Id`AzureADCertThumbprint`( ), veya Azure AD sertifikası parmak izi ( ) vardır.</span><span class="sxs-lookup"><span data-stu-id="23f41-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="23f41-291">Yapılandırma anahtarı (ad) yüklemeye çalıştığınız değer için uygulamada yanlıştır.</span><span class="sxs-lookup"><span data-stu-id="23f41-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="23f41-292">Uygulamanın erişim ilkesini anahtar kasasına eklerken, ilke oluşturuldu, ancak **Access ilkeleri** UI'sinde **Kaydet** düğmesi seçilmedi.</span><span class="sxs-lookup"><span data-stu-id="23f41-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="23f41-293">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="23f41-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="23f41-294">Microsoft Azure: Anahtar Kasası</span><span class="sxs-lookup"><span data-stu-id="23f41-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="23f41-295">Microsoft Azure: Önemli Kasa Belgeleri</span><span class="sxs-lookup"><span data-stu-id="23f41-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="23f41-296">Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma</span><span class="sxs-lookup"><span data-stu-id="23f41-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="23f41-297">KeyVaultMüşteri Sınıfı</span><span class="sxs-lookup"><span data-stu-id="23f41-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="23f41-298">Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma</span><span class="sxs-lookup"><span data-stu-id="23f41-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="23f41-299">Öğretici: .NET'te Azure Windows Virtual Machine ile Azure Key Vault nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="23f41-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="23f41-300">Bu belge, Uygulama yapılandırma değerlerini Azure Key Vault sırlarından yüklemek için [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Yapılandırma Sağlayıcısı'nın nasıl kullanılacağını açıklar.</span><span class="sxs-lookup"><span data-stu-id="23f41-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="23f41-301">Azure Key Vault, uygulamalar ve hizmetler tarafından kullanılan şifreleme anahtarlarının ve sırların korunmasına yardımcı olan bulut tabanlı bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="23f41-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="23f41-302">Azure Key Vault'u ASP.NET Core uygulamalarıyla kullanmak için sık karşılaşılan senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="23f41-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="23f41-303">Hassas yapılandırma verilerine erişimi denetleme.</span><span class="sxs-lookup"><span data-stu-id="23f41-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="23f41-304">Yapılandırma verilerini depolarken FIPS 140-2 Düzey 2 doğrulanmış Donanım Güvenlik Modülleri (HSM'ler) gereksinimini karşılar.</span><span class="sxs-lookup"><span data-stu-id="23f41-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="23f41-305">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="23f41-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="23f41-306">Paketler</span><span class="sxs-lookup"><span data-stu-id="23f41-306">Packages</span></span>

<span data-ttu-id="23f41-307">[Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="23f41-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="23f41-308">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="23f41-308">Sample app</span></span>

<span data-ttu-id="23f41-309">Örnek uygulama, `#define` *Program.cs* dosyasının üst kısmındaki ifadeyle belirlenen iki moddan birinde çalışır:</span><span class="sxs-lookup"><span data-stu-id="23f41-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="23f41-310">`Certificate`&ndash; Azure Key Vault'ta depolanan sırlara erişmek için Azure Key Vault İstemci Kimliği ve X.509 sertifikası kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="23f41-310">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="23f41-311">Örneğin bu sürümü herhangi bir konumdan çalıştırılabilir, Azure Uygulama Hizmeti'ne veya ASP.NET Core uygulamasına hizmet verebilecek herhangi bir ana bilgisayara dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="23f41-312">`Managed`&ndash; Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulaması ile azure anahtar atlamasına kimlik doğrulamak [için Azure kaynakları için Yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="23f41-312">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="23f41-313">Kimlik doğrulaması için yönetilen kimlikleri kullanırken, Azure AD Uygulama Kimliği ve Parola (İstemci Sırrı) gerekmez.</span><span class="sxs-lookup"><span data-stu-id="23f41-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="23f41-314">Örneğin `Managed` sürümü Azure'a dağıtılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="23f41-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="23f41-315">[Azure kaynakları için Yönetilen Kimlikleri Kullan](#use-managed-identities-for-azure-resources) bölümündeki kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="23f41-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="23f41-316">Örnek bir uygulamanın önişlemci yönergelerini kullanarak nasıl`#define`yapılandırılacakları hakkında daha fazla bilgi için ( <xref:index#preprocessor-directives-in-sample-code>bkz.</span><span class="sxs-lookup"><span data-stu-id="23f41-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="23f41-317">Geliştirme ortamında gizli depolama</span><span class="sxs-lookup"><span data-stu-id="23f41-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="23f41-318">[Gizli Yönetici aracını](xref:security/app-secrets)kullanarak sırları yerel olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="23f41-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="23f41-319">Örnek uygulama Geliştirme ortamındayerel makinede çalıştığında, sırlar yerel Secret Manager mağazasından yüklenir.</span><span class="sxs-lookup"><span data-stu-id="23f41-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="23f41-320">Gizli Yönetici aracı, `<UserSecretsId>` uygulamanın proje dosyasında bir özellik gerektirir.</span><span class="sxs-lookup"><span data-stu-id="23f41-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="23f41-321">Özellik değerini (`{GUID}`) herhangi bir benzersiz GUID'e ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="23f41-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="23f41-322">Sırlar ad değeri çiftleri olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="23f41-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="23f41-323">Hiyerarşik değerler (yapılandırma bölümleri), Core yapılandırma anahtar adlarında `:` [ASP.NET](xref:fundamentals/configuration/index) ayırıcı olarak (üst üste) kullanır.</span><span class="sxs-lookup"><span data-stu-id="23f41-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="23f41-324">Gizli Yönetici, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır `{SECRET VALUE}` , adı ve değeri: `{SECRET NAME}`</span><span class="sxs-lookup"><span data-stu-id="23f41-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="23f41-325">Örnek uygulamanın sırlarını ayarlamak için projenin [içerik kökünden](xref:fundamentals/index#content-root) komut kabuğunda aşağıdaki komutları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="23f41-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="23f41-326">Bu sırlar Azure Key Vault bölümünden [Üretim ortamındaki Gizli depolama alanında](#secret-storage-in-the-production-environment-with-azure-key-vault) `_dev` Azure Key Vault'ta depolandığında, sonek ' olarak `_prod`değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="23f41-327">Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren görsel bir ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="23f41-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="23f41-328">Azure Key Vault ile Üretim ortamında gizli depolama</span><span class="sxs-lookup"><span data-stu-id="23f41-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="23f41-329">Quickstart tarafından sağlanan yönergeler: Azure CLI konusunu [kullanarak Azure Key Vault'tan bir sır ayarlayın ve alın,](/azure/key-vault/quick-create-cli) azure anahtar kasası oluşturmak ve örnek uygulama tarafından kullanılan sırları depolamak için burada özetlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="23f41-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="23f41-330">Daha fazla bilgi için konuya bakın.</span><span class="sxs-lookup"><span data-stu-id="23f41-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="23f41-331">[Azure portalında](https://portal.azure.com/)aşağıdaki yöntemlerden herhangi birini kullanarak Azure Bulutu'nu açın:</span><span class="sxs-lookup"><span data-stu-id="23f41-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="23f41-332">Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="23f41-333">Metin kutusundaki "Azure CLI" arama dizesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="23f41-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="23f41-334">**Başlat Bulut Shell** düğmesiyle tarayıcınızda Cloud Shell'i açın.</span><span class="sxs-lookup"><span data-stu-id="23f41-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="23f41-335">Azure portalının sağ üst köşesindeki menüdeki **Bulut Kabuğu** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="23f41-336">Daha fazla bilgi için Azure [CLI](/cli/azure/) ve [Azure Bulut BulutU'na Genel Bakış](/azure/cloud-shell/overview)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="23f41-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="23f41-337">Kimliğinizi doğrulamıyorsanız, `az login` komutla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="23f41-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="23f41-338">Aşağıdaki komuta sahip bir kaynak `{RESOURCE GROUP NAME}` grubu oluşturun, yeni kaynak grubunun `{LOCATION}` kaynak grubu adı ve Azure bölgesi (veri merkezi):</span><span class="sxs-lookup"><span data-stu-id="23f41-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="23f41-339">Kaynak grubunda, yeni anahtar kasasının `{KEY VAULT NAME}` `{LOCATION}` adı ve Azure bölgesinin (veri merkezi) bulunduğu aşağıdaki komutla bir anahtar kasası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="23f41-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="23f41-340">Anahtar kasasında ad değeri çiftleri olarak sırlar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="23f41-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="23f41-341">Azure Key Vault gizli adları alfasayısal karakterler ve tirelerle sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="23f41-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="23f41-342">Hiyerarşik değerler (yapılandırma `--` bölümleri) ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="23f41-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="23f41-343">Normalde [ASP.NET Core yapılandırmasında](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlamak için kullanılan üst üste, anahtar kasası gizli adlarına izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="23f41-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="23f41-344">Bu nedenle, iki tire kullanılır ve sırları uygulamanın yapılandırmasına yüklendiğinde bir iki nokta üst üste takas edilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="23f41-345">Aşağıdaki sırlar örnek uygulama ile kullanım içindir.</span><span class="sxs-lookup"><span data-stu-id="23f41-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="23f41-346">Değerler, Geliştirme `_prod` ortamında yüklenen sonek `_dev` değerlerinden Kullanıcı Sırları'ndan ayırt etmek için bir sonek içerir.</span><span class="sxs-lookup"><span data-stu-id="23f41-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="23f41-347">Önceki `{KEY VAULT NAME}` adımda oluşturduğunuz anahtar tonozunun adı ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="23f41-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="23f41-348">Azure'da barındırılan olmayan uygulamalar için Uygulama Kimliği ve X.509 sertifikasını kullanma</span><span class="sxs-lookup"><span data-stu-id="23f41-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="23f41-349">Azure AD, Azure Anahtar Kasası ve uygulamayı, **uygulama Azure dışında barındırıldığında**önemli bir kasaya kimlik doğrulamak için Azure Active Directory Application ID ve X.509 sertifikasını kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="23f41-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="23f41-350">Daha fazla bilgi için [anahtarlar, sırlar ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates)bilgi alabiliyorum.</span><span class="sxs-lookup"><span data-stu-id="23f41-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="23f41-351">Azure'da barındırılan uygulamalar için Uygulama Kimliği ve X.509 sertifikası kullanılması desteklenmiş olsa da, Azure'da bir uygulamayı barındırırken [Azure kaynakları için Yönetilen kimlikleri](#use-managed-identities-for-azure-resources) kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="23f41-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="23f41-352">Yönetilen kimlikler, uygulamada veya geliştirme ortamında sertifika depolamayı gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="23f41-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="23f41-353">Örnek uygulama, `#define` *Program.cs* dosyasının `Certificate`üst kısmındaki ifade .</span><span class="sxs-lookup"><span data-stu-id="23f41-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="23f41-354">Bir PKCS#12 arşivi (*.pfx*) sertifikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="23f41-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="23f41-355">Sertifika oluşturma seçenekleri arasında [Windows'ta MakeCert](/windows/desktop/seccrypto/makecert) ve [OpenSSL](https://www.openssl.org/)yer almaktadır.</span><span class="sxs-lookup"><span data-stu-id="23f41-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="23f41-356">Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükleyin.</span><span class="sxs-lookup"><span data-stu-id="23f41-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="23f41-357">Anahtarı dışa aktarılabilir olarak işaretlemek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="23f41-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="23f41-358">Sertifikanın bu işlemin ilerleyen saatlerinde kullanılan parmak izine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="23f41-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="23f41-359">PKCS#12 arşivini (*.pfx*) sertifikasını DER kodlu sertifika (*.cer)* olarak dışa aktarın.</span><span class="sxs-lookup"><span data-stu-id="23f41-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="23f41-360">Uygulamayı Azure AD **(Uygulama kayıtları)** ile kaydedin.</span><span class="sxs-lookup"><span data-stu-id="23f41-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="23f41-361">DER kodlu sertifikayı (*.cer*) Azure AD'ye yükleyin:</span><span class="sxs-lookup"><span data-stu-id="23f41-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="23f41-362">Azure AD'de uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="23f41-363">**Sertifikalar & sırları**gidin.</span><span class="sxs-lookup"><span data-stu-id="23f41-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="23f41-364">Ortak anahtarı içeren sertifikayı yüklemek için **Yükleme sertifikasını** seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="23f41-365">Bir *.cer*, *.pem*, veya *.crt* sertifikası kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="23f41-366">Anahtar kasa adını, Uygulama Kimliğini ve sertifika parmak izini uygulamanın *appsettings.json* dosyasında saklayın.</span><span class="sxs-lookup"><span data-stu-id="23f41-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="23f41-367">Azure portalındaki **Anahtar kasalarına** gidin.</span><span class="sxs-lookup"><span data-stu-id="23f41-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="23f41-368">[Azure Key Vault bölümüyle Üretim ortamındaki Gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında oluşturduğunuz anahtar kasasını seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="23f41-369">**Erişim ilkeleri**'ni seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="23f41-370">**Erişim İlkesi Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="23f41-371">**Gizli izinleri** açın ve uygulamaya **Al** ve **Liste** izinleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="23f41-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="23f41-372">**Ana para seçin** ve kayıtlı uygulamayı ada göre seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="23f41-373">**Seç** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="23f41-374">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-374">Select **OK**.</span></span>
1. <span data-ttu-id="23f41-375">**Kaydet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="23f41-375">Select **Save**.</span></span>
1. <span data-ttu-id="23f41-376">Uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="23f41-376">Deploy the app.</span></span>

<span data-ttu-id="23f41-377">Örnek `Certificate` uygulama yapılandırma değerlerini gizli `IConfigurationRoot` adla aynı ada sahip olarak alır:</span><span class="sxs-lookup"><span data-stu-id="23f41-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="23f41-378">Hiyerarşik olmayan değerler: Değer `SecretName` . `config["SecretName"]`</span><span class="sxs-lookup"><span data-stu-id="23f41-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="23f41-379">Hiyerarşik değerler (bölümler): (iki nokta `GetSection` üst üste) gösterimini veya uzantı yöntemini kullanın. `:`</span><span class="sxs-lookup"><span data-stu-id="23f41-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="23f41-380">Yapılandırma değerini elde etmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="23f41-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="23f41-381">X.509 sertifikası işletim sistemi tarafından yönetilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="23f41-382">Uygulama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings.json* dosyası tarafından sağlanan değerleri çağırır:</span><span class="sxs-lookup"><span data-stu-id="23f41-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="23f41-383">Örnek değerler:</span><span class="sxs-lookup"><span data-stu-id="23f41-383">Example values:</span></span>

* <span data-ttu-id="23f41-384">Anahtar kasa adı:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="23f41-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="23f41-385">Başvuru Kimliği:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="23f41-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="23f41-386">Sertifika parmak izi:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="23f41-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="23f41-387">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="23f41-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="23f41-388">Uygulamayı çalıştırdığınızda, bir web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="23f41-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="23f41-389">Geliştirme ortamında, gizli değerler `_dev` sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="23f41-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="23f41-390">Üretim ortamında, değerler `_prod` sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="23f41-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="23f41-391">Azure kaynakları için Yönetilen kimlikleri kullanma</span><span class="sxs-lookup"><span data-stu-id="23f41-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="23f41-392">**Azure'a dağıtılan bir uygulama,** Azure [kaynakları için Yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanabilir ve bu da uygulamanın uygulamada depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasını (Uygulama Kimliği ve Parola/İstemci Gizli) kullanarak Azure Anahtar Kasası ile kimlik doğrulamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="23f41-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="23f41-393">Örnek uygulama, *Program.cs* dosyasının üst `#define` kısmındaki ifade . `Managed`</span><span class="sxs-lookup"><span data-stu-id="23f41-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="23f41-394">Uygulamanın *appsettings.json* dosyasına kasa adını girin.</span><span class="sxs-lookup"><span data-stu-id="23f41-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="23f41-395">Örnek `Managed` uygulama, sürüme ayarlandığında Uygulama Kimliği ve Parola (İstemci Gizli) gerektirmez, bu nedenle bu yapılandırma girişlerini yok sayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="23f41-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="23f41-396">Uygulama Azure'a dağıtılır ve Azure, *uygulama ayarları.json* dosyasında depolanan kasa adını kullanarak Azure Key Vault'a erişmek için uygulamanın kimliğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="23f41-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="23f41-397">Örnek uygulamayı Azure Uygulama Hizmeti'ne dağıtın.</span><span class="sxs-lookup"><span data-stu-id="23f41-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="23f41-398">Hizmet oluşturulduğunda Azure Uygulama Hizmeti'ne dağıtılan bir uygulama otomatik olarak Azure AD'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="23f41-399">Aşağıdaki komutta kullanılmak üzere dağıtımdan Nesne Kimliği'ni edinin.</span><span class="sxs-lookup"><span data-stu-id="23f41-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="23f41-400">Nesne Kimliği, Uygulama Hizmetinin **Kimlik** panelindeki Azure portalında gösterilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="23f41-401">Azure CLI'yi ve uygulamanın Nesne Kimliğini `list` kullanarak, uygulamaya anahtar kasasına erişmek için izin ler sağlayın: `get`</span><span class="sxs-lookup"><span data-stu-id="23f41-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="23f41-402">Uygulamayı Azure CLI, PowerShell veya Azure portalını kullanarak **yeniden başlatın.**</span><span class="sxs-lookup"><span data-stu-id="23f41-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="23f41-403">Örnek uygulama:</span><span class="sxs-lookup"><span data-stu-id="23f41-403">The sample app:</span></span>

* <span data-ttu-id="23f41-404">Bağlantı dizesi `AzureServiceTokenProvider` olmayan sınıfın bir örneğini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="23f41-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="23f41-405">Bağlantı dizesi sağlanmadığı zaman, sağlayıcı Azure kaynakları için Yönetilen kimliklerden bir erişim belirteci elde etmeye çalışır.</span><span class="sxs-lookup"><span data-stu-id="23f41-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="23f41-406">Örnek <xref:Microsoft.Azure.KeyVault.KeyVaultClient> çağrı geri aramaile birlikte yeni bir oluşturulur. `AzureServiceTokenProvider`</span><span class="sxs-lookup"><span data-stu-id="23f41-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="23f41-407">Örnek, <xref:Microsoft.Azure.KeyVault.KeyVaultClient> tüm gizli değerleri <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> yükler ve anahtar adlarında iki noktalı`--`( ) ile`:`çift tireli ( ) değiştirir varsayılan bir uygulama ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="23f41-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="23f41-408">Anahtar kasa sıcadı örnek değeri:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="23f41-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="23f41-409">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="23f41-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="23f41-410">Uygulamayı çalıştırdığınızda, bir web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="23f41-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="23f41-411">Geliştirme ortamında, kullanıcı sırları `_dev` tarafından sağlandığı için gizli değerler sonek ekine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="23f41-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="23f41-412">Üretim ortamında, değerler `_prod` Azure Anahtar Kasası tarafından sağlandığı için sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="23f41-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="23f41-413">Bir `Access denied` hata alırsanız, uygulamanın Azure AD'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="23f41-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="23f41-414">Hizmeti Azure'da yeniden başlattığınızı doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="23f41-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="23f41-415">Sağlayıcının yönetilen bir kimliğe ve Azure DevOps ardışık kuruluşuna sahip kullanımı hakkında bilgi [için](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)bkz.</span><span class="sxs-lookup"><span data-stu-id="23f41-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="23f41-416">Anahtar adı öneki kullanma</span><span class="sxs-lookup"><span data-stu-id="23f41-416">Use a key name prefix</span></span>

<span data-ttu-id="23f41-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>anahtar kasa sırlarını yapılandırma tuşlarına <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>nasıl dönüştürüldüğünü kontrol etmenizi sağlayan bir uygulamayı kabul eden aşırı yük sağlar.</span><span class="sxs-lookup"><span data-stu-id="23f41-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="23f41-418">Örneğin, uygulama başlangıç sırasında sağladığınız bir önek değerine göre gizli değerleri yüklemek için arabirimi uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="23f41-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="23f41-419">Bu, örneğin, uygulamanın sürümüne göre sırları yüklemenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="23f41-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="23f41-420">Birden çok uygulamanın sırlarını aynı anahtar kasaya yerleştirmek veya çevresel sırları (örneğin *geliştirme* ve *üretim* sırları) aynı kasaya yerleştirmek için anahtar kasa sırlarındaki önekleri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="23f41-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="23f41-421">Farklı uygulamaların ve geliştirme/üretim ortamlarının, uygulama ortamlarını en üst düzeyde güvenlik için yalıtmak için ayrı anahtar kasaları kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="23f41-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="23f41-422">Aşağıdaki örnekte, anahtar kasasında `5000-AppSecret` (ve Geliştirme ortamı için Gizli Yönetici aracı kullanılarak) (anahtar kasa gizli adlarında dönemlere izin verilmez) bir sır oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="23f41-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="23f41-423">Bu gizli sürüm 5.0.0.0 için bir uygulama gizli temsil eder.</span><span class="sxs-lookup"><span data-stu-id="23f41-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="23f41-424">Uygulamanın başka bir sürümü için, 5.1.0.0, bir sır için anahtar tonoz `5100-AppSecret`(ve Secret Manager aracı kullanılarak) eklenir.</span><span class="sxs-lookup"><span data-stu-id="23f41-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="23f41-425">Her uygulama sürümü, sürümdeki gizli değerini `AppSecret`yapılandırmasına yükler, sırrı yüklerken sürümü sıyırır.</span><span class="sxs-lookup"><span data-stu-id="23f41-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="23f41-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>bir özel <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>ile denir:</span><span class="sxs-lookup"><span data-stu-id="23f41-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="23f41-427">Uygulama, <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> uygun sırrı yapılandırmaya yüklemek için sırların sürüm öneklerine tepki verir:</span><span class="sxs-lookup"><span data-stu-id="23f41-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="23f41-428">`Load`adı önek ile başladığında bir sır yükler.</span><span class="sxs-lookup"><span data-stu-id="23f41-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="23f41-429">Diğer sırlar dolu değil.</span><span class="sxs-lookup"><span data-stu-id="23f41-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="23f41-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="23f41-430">`GetKey`:</span></span>
  * <span data-ttu-id="23f41-431">Öneki gizli addan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="23f41-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="23f41-432">Yapılandırmada kullanılan sınır dışı layıcı `KeyDelimiter`(genellikle bir üst üste) olan herhangi bir addaki iki tireyi değiştirir.</span><span class="sxs-lookup"><span data-stu-id="23f41-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="23f41-433">Azure Anahtar Kasası gizli adlarda üst üste izin vermez.</span><span class="sxs-lookup"><span data-stu-id="23f41-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="23f41-434">Yöntem, `Load` sürüm önekine sahip olanları bulmak için kasa sırlarını yineleyen bir sağlayıcı algoritması tarafından çağrılır.</span><span class="sxs-lookup"><span data-stu-id="23f41-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="23f41-435">Bir sürüm öneki ile `Load`bulunduğunda, `GetKey` algoritma gizli adın yapılandırma adını döndürmek için yöntemi kullanır.</span><span class="sxs-lookup"><span data-stu-id="23f41-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="23f41-436">Gizlinin adından sürüm önekini siler ve uygulamanın yapılandırma adı değeri çiftleri içine yüklemek için gizli adın geri kalanını döndürür.</span><span class="sxs-lookup"><span data-stu-id="23f41-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="23f41-437">Bu yaklaşım uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="23f41-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="23f41-438">Uygulamanın proje dosyasında belirtilen uygulamanın sürümü.</span><span class="sxs-lookup"><span data-stu-id="23f41-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="23f41-439">Aşağıdaki örnekte, uygulamanın sürümü aşağıdaki `5.0.0.0`şekilde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="23f41-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="23f41-440">Uygulamanın `<UserSecretsId>` proje dosyasında bir özelliğin bulunduğunu `{GUID}` ve kullanıcı tarafından sağlanan GUID'in bulunduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="23f41-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="23f41-441">Aşağıdaki sırları Gizli Yönetici [aracıyla](xref:security/app-secrets)yerel olarak kaydedin:</span><span class="sxs-lookup"><span data-stu-id="23f41-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="23f41-442">Sırlar Aşağıdaki Azure CLI komutları kullanılarak Azure Anahtar Kasası'na kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="23f41-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="23f41-443">Uygulama çalıştırıldığında, anahtar kasa sırları yüklenir.</span><span class="sxs-lookup"><span data-stu-id="23f41-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="23f41-444">String `5000-AppSecret` secret, uygulamanın proje dosyasında belirtilen uygulamanın sürümüyle eşleşir (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="23f41-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="23f41-445">Sürüm, `5000` (tire ile), anahtar adından çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="23f41-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="23f41-446">Uygulama boyunca, anahtarla `AppSecret` yapılandırmayı okuma gizli değeri yükler.</span><span class="sxs-lookup"><span data-stu-id="23f41-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="23f41-447">Uygulamanın sürümü proje dosyasında `5.1.0.0` değiştirilirse ve uygulama yeniden çalıştırılırsa, döndürülen gizli değer Geliştirme ortamında ve `5.1.0.0_secret_value_dev` `5.1.0.0_secret_value_prod` Üretim'de olur.</span><span class="sxs-lookup"><span data-stu-id="23f41-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="23f41-448">Ayrıca kendi <xref:Microsoft.Azure.KeyVault.KeyVaultClient> uygulama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="23f41-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="23f41-449">Özel bir istemci, uygulama genelinde istemcinin tek bir örneğini paylaşmaya izin verir.</span><span class="sxs-lookup"><span data-stu-id="23f41-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="23f41-450">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="23f41-450">Bind an array to a class</span></span>

<span data-ttu-id="23f41-451">Sağlayıcı, bir POCO dizisine bağlanmak için yapılandırma değerlerini bir dizihalinde okuyabiliyor.</span><span class="sxs-lookup"><span data-stu-id="23f41-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="23f41-452">Tuşların iki nokta üst üste (`:`) ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken,`:0:`bir `:1:` &hellip; `:{n}:`diziyi oluşturan anahtarları ayırt etmek için sayısal bir anahtar kesimi kullanılır .</span><span class="sxs-lookup"><span data-stu-id="23f41-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="23f41-453">Daha fazla bilgi için yapılandırma: [Bir diziyi sınıfa bağlayın.](xref:fundamentals/configuration/index#bind-an-array-to-a-class)</span><span class="sxs-lookup"><span data-stu-id="23f41-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="23f41-454">Azure Anahtar Kasası tuşları ayırıcı olarak üst üste kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="23f41-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="23f41-455">Bu konuda açıklanan yaklaşım, hiyerarşik`--`değerler (bölümler) için ayırıcı olarak çift tire ( ) kullanır.</span><span class="sxs-lookup"><span data-stu-id="23f41-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="23f41-456">Dizi anahtarları, çift tireli ve sayısal anahtar segmentleri`--0--`(, , `--1--` &hellip; `--{n}--`, ) ile Azure Key Vault'ta depolanır.</span><span class="sxs-lookup"><span data-stu-id="23f41-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="23f41-457">JSON dosyası tarafından sağlanan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="23f41-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="23f41-458">`WriteTo` Dizide tanımlanan ve günlüğe kaydetme için hedefleri açıklayan iki Serilog *lavabosu*olan iki nesne literals vardır:</span><span class="sxs-lookup"><span data-stu-id="23f41-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="23f41-459">Önceki JSON dosyasında gösterilen yapılandırma, çift çizgi (`--`) gösterimi ve sayısal segmentler kullanılarak Azure Key Vault'ta depolanır:</span><span class="sxs-lookup"><span data-stu-id="23f41-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="23f41-460">Anahtar</span><span class="sxs-lookup"><span data-stu-id="23f41-460">Key</span></span> | <span data-ttu-id="23f41-461">Değer</span><span class="sxs-lookup"><span data-stu-id="23f41-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="23f41-462">Sırları yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="23f41-462">Reload secrets</span></span>

<span data-ttu-id="23f41-463">Sırlar çağrılana `IConfigurationRoot.Reload()` kadar önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="23f41-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="23f41-464">Anahtar kasasında süresi dolmuş, devre dışı bırakılmış ve `Reload` güncelleştirilmiş sırlar yürütülene kadar uygulama tarafından saygı duyulmuyor.</span><span class="sxs-lookup"><span data-stu-id="23f41-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="23f41-465">Devre dışı bırakılmış ve süresi dolmuş sırlar</span><span class="sxs-lookup"><span data-stu-id="23f41-465">Disabled and expired secrets</span></span>

<span data-ttu-id="23f41-466">Devre dışı bırakılmış <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>ve süresi dolmuş sırlar bir atmak .</span><span class="sxs-lookup"><span data-stu-id="23f41-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="23f41-467">Uygulamanın atmasını önlemek için, yapılandırmayı farklı bir yapılandırma sağlayıcısı kullanarak sağlayın veya devre dışı bırakılan veya süresi dolmuş sırrı güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="23f41-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="23f41-468">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="23f41-468">Troubleshoot</span></span>

<span data-ttu-id="23f41-469">Uygulama sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Çekirdek Günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır.</span><span class="sxs-lookup"><span data-stu-id="23f41-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="23f41-470">Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:</span><span class="sxs-lookup"><span data-stu-id="23f41-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="23f41-471">Uygulama veya sertifika Azure Etkin Dizini'nde doğru şekilde yapılandırılmamıştır.</span><span class="sxs-lookup"><span data-stu-id="23f41-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="23f41-472">Anahtar kasası Azure Key Vault'ta yok.</span><span class="sxs-lookup"><span data-stu-id="23f41-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="23f41-473">Uygulamanın anahtar kasasına erişme yetkisi yok.</span><span class="sxs-lookup"><span data-stu-id="23f41-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="23f41-474">Erişim ilkesi, izinleri `Get` `List` içermez ve içermez.</span><span class="sxs-lookup"><span data-stu-id="23f41-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="23f41-475">Anahtar kasasında, yapılandırma verileri (ad değeri çifti) yanlış adlandırılır, eksik, devre dışı bırakılır veya süresi dolmuş olur.</span><span class="sxs-lookup"><span data-stu-id="23f41-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="23f41-476">Uygulama, yanlış anahtar kasa`KeyVaultName`adı ( ),`AzureADApplicationId`Azure AD Application Id`AzureADCertThumbprint`( ), veya Azure AD sertifikası parmak izi ( ) vardır.</span><span class="sxs-lookup"><span data-stu-id="23f41-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="23f41-477">Yapılandırma anahtarı (ad) yüklemeye çalıştığınız değer için uygulamada yanlıştır.</span><span class="sxs-lookup"><span data-stu-id="23f41-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="23f41-478">Uygulamanın erişim ilkesini anahtar kasasına eklerken, ilke oluşturuldu, ancak **Access ilkeleri** UI'sinde **Kaydet** düğmesi seçilmedi.</span><span class="sxs-lookup"><span data-stu-id="23f41-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="23f41-479">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="23f41-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="23f41-480">Microsoft Azure: Anahtar Kasası</span><span class="sxs-lookup"><span data-stu-id="23f41-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="23f41-481">Microsoft Azure: Önemli Kasa Belgeleri</span><span class="sxs-lookup"><span data-stu-id="23f41-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="23f41-482">Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma</span><span class="sxs-lookup"><span data-stu-id="23f41-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="23f41-483">KeyVaultMüşteri Sınıfı</span><span class="sxs-lookup"><span data-stu-id="23f41-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="23f41-484">Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma</span><span class="sxs-lookup"><span data-stu-id="23f41-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="23f41-485">Öğretici: .NET'te Azure Windows Virtual Machine ile Azure Key Vault nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="23f41-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

