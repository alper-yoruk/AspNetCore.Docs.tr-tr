---
<span data-ttu-id="1b85c-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1b85c-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b85c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-102">'Blazor'</span></span>
- <span data-ttu-id="1b85c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b85c-103">'Identity'</span></span>
- <span data-ttu-id="1b85c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b85c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b85c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-105">'Razor'</span></span>
- <span data-ttu-id="1b85c-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1b85c-106">'SignalR' uid:</span></span> 

---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="1b85c-107">ASP.NET Core Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="1b85c-107">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="1b85c-108">, [Andrew Stanton-nurte](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="1b85c-108">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1b85c-109">Bu belgede, Azure Key Vault gizliliklerden uygulama yapılandırma değerlerini yüklemek için [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) yapılandırma sağlayıcısının nasıl kullanılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-109">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="1b85c-110">Azure Key Vault, uygulama ve hizmetler tarafından kullanılan şifreleme anahtarlarını ve gizli dizileri koruma konusunda yardımcı olan bulut tabanlı bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-110">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="1b85c-111">ASP.NET Core uygulamalarla Azure Key Vault kullanmaya yönelik yaygın senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="1b85c-111">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="1b85c-112">Hassas yapılandırma verilerine erişimi denetleme.</span><span class="sxs-lookup"><span data-stu-id="1b85c-112">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="1b85c-113">Yapılandırma verilerini depolarken FIPS 140-2 düzey 2 doğrulanan donanım güvenlik modülleri (HSM 'ler) gereksinimini karşılarsınız.</span><span class="sxs-lookup"><span data-stu-id="1b85c-113">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="1b85c-114">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1b85c-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="1b85c-115">Paketler</span><span class="sxs-lookup"><span data-stu-id="1b85c-115">Packages</span></span>

<span data-ttu-id="1b85c-116">[Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-116">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="1b85c-117">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="1b85c-117">Sample app</span></span>

<span data-ttu-id="1b85c-118">Örnek uygulama, `#define` *program.cs* dosyasının en üstünde yer aldığı ifadeye göre belirlenen iki moddan birinde çalışır:</span><span class="sxs-lookup"><span data-stu-id="1b85c-118">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="1b85c-119">`Certificate`: Azure Key Vault depolanan gizli dizileri erişmek için Azure Key Vault Istemci KIMLIĞI ve X. 509.952 sertifikası kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-119">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="1b85c-120">Örneğin bu sürümü, Azure App Service dağıtılan herhangi bir konumdan veya ASP.NET Core uygulamasına hizmet veren herhangi bir konağa çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-120">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="1b85c-121">`Managed`: Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasıyla Azure Key Vault üzere uygulamanın kimliğini doğrulamak için [Azure kaynakları Için yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-121">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="1b85c-122">Kimlik doğrulaması için Yönetilen kimlikler kullanıldığında, bir Azure AD uygulama KIMLIĞI ve parolası (gizli anahtar) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-122">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="1b85c-123">`Managed`Örneğin sürümünün Azure 'a dağıtılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-123">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="1b85c-124">[Azure kaynakları Için Yönetilen kimlikler bölümünü kullanma](#use-managed-identities-for-azure-resources) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-124">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="1b85c-125">Önişlemci yönergeleri () kullanarak örnek bir uygulamanın nasıl yapılandırılacağı hakkında daha fazla bilgi için `#define` bkz <xref:index#preprocessor-directives-in-sample-code> ..</span><span class="sxs-lookup"><span data-stu-id="1b85c-125">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="1b85c-126">Geliştirme ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="1b85c-126">Secret storage in the Development environment</span></span>

<span data-ttu-id="1b85c-127">[Gizli dizi Yöneticisi aracını](xref:security/app-secrets)kullanarak gizli dizileri yerel olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-127">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="1b85c-128">Örnek uygulama, geliştirme ortamındaki yerel makinede çalıştığında, gizli anahtar Yöneticisi deposundan gizli diziler yüklenir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-128">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="1b85c-129">Gizli dizi Yöneticisi Aracı, `<UserSecretsId>` uygulamanın proje dosyasında bir özellik gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-129">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="1b85c-130">Özellik değerini ( `{GUID}` ) herhangi bir BENZERSIZ GUID 'ye ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="1b85c-130">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="1b85c-131">Gizlilikler ad-değer çiftleri olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1b85c-131">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="1b85c-132">Hiyerarşik değerler (yapılandırma bölümleri) `:` [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index) anahtarı adlarında ayırıcı olarak bir (iki nokta üst üste) kullanır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-132">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="1b85c-133">Gizli dizi, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır; burada `{SECRET NAME}` ad ve `{SECRET VALUE}` değerdir:</span><span class="sxs-lookup"><span data-stu-id="1b85c-133">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="1b85c-134">Örnek uygulamanın gizli dizilerini ayarlamak için, projenin [içerik kökünden](xref:fundamentals/index#content-root) bir komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="1b85c-134">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="1b85c-135">Bu gizlilikler, [Azure Key Vault bölümündeki üretim ortamındaki gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında Azure Key Vault depolandığında, `_dev` sonek olarak değiştirilir `_prod` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-135">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="1b85c-136">Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren bir görsel ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="1b85c-136">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="1b85c-137">Azure Key Vault ile üretim ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="1b85c-137">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="1b85c-138">Hızlı başlangıç tarafından sunulan yönergeler [: Azure CLI kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-cli) , bir Azure Key Vault oluşturmak ve örnek uygulama tarafından kullanılan gizli dizileri depolamak için burada özetlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-138">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="1b85c-139">Daha ayrıntılı bilgi için konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-139">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="1b85c-140">[Azure Portal](https://portal.azure.com/)aşağıdaki yöntemlerden birini kullanarak Azure Cloud Shell 'i açın:</span><span class="sxs-lookup"><span data-stu-id="1b85c-140">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="1b85c-141">Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-141">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="1b85c-142">Metin kutusunda "Azure CLı" arama dizesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-142">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="1b85c-143">**Cloud Shell Başlat** düğmesini kullanarak tarayıcınızda Cloud Shell açın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-143">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="1b85c-144">Azure portal sağ üst köşesindeki menüdeki **Cloud Shell** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-144">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="1b85c-145">Daha fazla bilgi için bkz. [Azure CLI](/cli/azure/) ve [Azure Cloud Shell Genel Bakış](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="1b85c-145">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="1b85c-146">Henüz kimlik doğrulamasından geçirilmemişse komutuyla oturum açın `az login` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-146">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="1b85c-147">Aşağıdaki komutla bir kaynak grubu oluşturun; burada, `{RESOURCE GROUP NAME}` Yeni kaynak grubu için kaynak grubu adı ve `{LOCATION}` Azure bölgesi (Datacenter) olur:</span><span class="sxs-lookup"><span data-stu-id="1b85c-147">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="1b85c-148">Aşağıdaki komutla kaynak grubunda bir Anahtar Kasası oluşturun; burada `{KEY VAULT NAME}` Yeni anahtar kasasının adı ve `{LOCATION}` Azure bölgesi (Datacenter) bulunur:</span><span class="sxs-lookup"><span data-stu-id="1b85c-148">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="1b85c-149">Anahtar kasasında ad-değer çiftleri olarak gizli diziler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1b85c-149">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="1b85c-150">Azure Key Vault gizli dizi adları, alfasayısal karakterler ve tireler ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-150">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="1b85c-151">Hiyerarşik değerler (yapılandırma bölümleri) `--` ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-151">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="1b85c-152">Genellikle [ASP.NET Core yapılandırmasındaki](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlandırmak için kullanılan iki nokta üst üste, Anahtar Kasası gizli adlarında izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="1b85c-152">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="1b85c-153">Bu nedenle, gizlilikler uygulamanın yapılandırmasına yüklendiğinde iki tire kullanılır ve iki nokta üst üste bulunur.</span><span class="sxs-lookup"><span data-stu-id="1b85c-153">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="1b85c-154">Aşağıdaki gizlilikler örnek uygulamayla kullanım içindir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-154">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="1b85c-155">Değerler, `_prod` `_dev` Kullanıcı parolalarından geliştirme ortamında yüklenen sonek değerlerinden ayırt etmek için bir sonek içerir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-155">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="1b85c-156">`{KEY VAULT NAME}`Önceki adımda oluşturduğunuz anahtar kasasının adıyla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="1b85c-156">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="1b85c-157">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanın</span><span class="sxs-lookup"><span data-stu-id="1b85c-157">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="1b85c-158">**Uygulama Azure dışında barındırıldığı zaman**bir anahtar kasasında kimlik doğrulaması yapmak IÇIN Azure AD, Azure Key Vault ve uygulamayı bir Azure ACTIVE DIRECTORY uygulama kimliği ve X. 509.440 sertifikası kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-158">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="1b85c-159">Daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="1b85c-159">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="1b85c-160">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanılması desteklenmekle birlikte, Azure 'da bir uygulama barındırılırken [Azure kaynakları Için Yönetilen kimlikler](#use-managed-identities-for-azure-resources) kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="1b85c-160">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="1b85c-161">Yönetilen kimlikler, uygulamada veya geliştirme ortamında bir sertifikanın depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="1b85c-161">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="1b85c-162">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki ifade olarak AYARLANDıĞıNDA bir uygulama kimliği ve X. 509.440 sertifikası kullanır `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-162">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="1b85c-163">PKCS # 12 Arşivi (*. pfx*) sertifikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1b85c-163">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="1b85c-164">Sertifika oluşturma seçenekleri Windows ve [OpenSSL](https://www.openssl.org/) [üzerinde MakeCert](/windows/desktop/seccrypto/makecert) içerir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-164">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="1b85c-165">Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükler.</span><span class="sxs-lookup"><span data-stu-id="1b85c-165">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="1b85c-166">Anahtarı verilebilir olarak işaretlemek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-166">Marking the key as exportable is optional.</span></span> <span data-ttu-id="1b85c-167">Sertifikanın daha sonra bu işlemde kullanılan parmak izini aklınızda bulunur.</span><span class="sxs-lookup"><span data-stu-id="1b85c-167">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="1b85c-168">PKCS # 12 Arşivi (*. pfx*) sertifikasını der kodlu bir sertifika (*. cer*) olarak dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-168">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="1b85c-169">Uygulamayı Azure AD 'ye kaydedin (**uygulama kayıtları**).</span><span class="sxs-lookup"><span data-stu-id="1b85c-169">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="1b85c-170">DER kodlu sertifikayı (*. cer*) Azure AD 'ye yükleyin:</span><span class="sxs-lookup"><span data-stu-id="1b85c-170">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="1b85c-171">Azure AD 'de uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-171">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="1b85c-172">**Sertifikalar & gizli**dizi sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-172">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="1b85c-173">Ortak anahtarı içeren sertifikayı karşıya yüklemek için **sertifikayı karşıya yükle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-173">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="1b85c-174">*. Cer*, *. pek*veya *. CRT* sertifikası kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-174">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="1b85c-175">Anahtar Kasası adı, uygulama KIMLIĞI ve sertifika parmak izini uygulamanın *appSettings. JSON* dosyasında depolayın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-175">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="1b85c-176">Azure portal **ana** kasaları ' ne gidin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-176">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="1b85c-177">Azure Key Vault bölümünde, [üretim ortamındaki gizli dizi deposunda](#secret-storage-in-the-production-environment-with-azure-key-vault) oluşturduğunuz anahtar kasasını seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-177">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="1b85c-178">**Erişim ilkeleri**'ni seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-178">Select **Access policies**.</span></span>
1. <span data-ttu-id="1b85c-179">**Erişim Ilkesi Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-179">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="1b85c-180">**Gizli izinleri** açın ve uygulamaya **Get** ve **list** izinleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-180">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="1b85c-181">**Sorumlu Seç** ' i seçin ve kayıtlı uygulamayı ada göre seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="1b85c-182">**Seç** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="1b85c-183">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-183">Select **OK**.</span></span>
1. <span data-ttu-id="1b85c-184">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-184">Select **Save**.</span></span>
1. <span data-ttu-id="1b85c-185">Uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-185">Deploy the app.</span></span>

<span data-ttu-id="1b85c-186">`Certificate`Örnek uygulama, yapılandırma değerlerini, `IConfigurationRoot` parola adı ile aynı adla alır:</span><span class="sxs-lookup"><span data-stu-id="1b85c-186">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="1b85c-187">Hiyerarşik olmayan değerler: için değeri `SecretName` ile elde edilir `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-187">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="1b85c-188">Hiyerarşik değerler (bölümler): `:` (iki nokta üst üste) gösterimini veya `GetSection` genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-188">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="1b85c-189">Yapılandırma değerini elde etmek için şu yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="1b85c-189">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="1b85c-190">X. 509.440 sertifikası işletim sistemi tarafından yönetiliyor.</span><span class="sxs-lookup"><span data-stu-id="1b85c-190">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="1b85c-191">Uygulama, <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appSettings. JSON* dosyası tarafından sağlanan değerlerle çağırır:</span><span class="sxs-lookup"><span data-stu-id="1b85c-191">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="1b85c-192">Örnek değerler:</span><span class="sxs-lookup"><span data-stu-id="1b85c-192">Example values:</span></span>

* <span data-ttu-id="1b85c-193">Anahtar Kasası adı:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="1b85c-193">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="1b85c-194">Uygulama KIMLIĞI:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="1b85c-194">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="1b85c-195">Sertifika parmak izi:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="1b85c-195">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="1b85c-196">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="1b85c-196">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="1b85c-197">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-197">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="1b85c-198">Geliştirme ortamında, gizli anahtar değerleri sonek ile yüklenir `_dev` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-198">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="1b85c-199">Üretim ortamında, değerler sonek ile yüklenir `_prod` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-199">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="1b85c-200">Azure kaynakları için Yönetilen kimlikler kullanma</span><span class="sxs-lookup"><span data-stu-id="1b85c-200">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="1b85c-201">Azure **'a dağıtılan bir uygulama** , [Azure kaynakları için yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanarak uygulamanın, KIMLIK bilgileri (uygulama kimliği ve parola/istemci gizli anahtarı) olmadan Azure AD kimlik doğrulaması kullanarak Azure Key Vault kimlik doğrulamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-201">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="1b85c-202">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki Ifade olarak ayarlandığında Azure kaynakları için Yönetilen kimlikler kullanır `Managed` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-202">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="1b85c-203">Uygulamanın *appSettings. JSON* dosyasına kasa adını girin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-203">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="1b85c-204">Örnek uygulama sürüme ayarlandığında bir uygulama KIMLIĞI ve parola (Istemci gizli anahtarı) gerektirmez `Managed` , bu nedenle bu yapılandırma girişlerini yoksayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1b85c-204">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="1b85c-205">Uygulama Azure 'a dağıtılır ve Azure, yalnızca *appSettings. JSON* dosyasında depolanan kasa adını kullanarak Azure Key Vault erişmek için uygulamanın kimliğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="1b85c-205">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="1b85c-206">Azure App Service için örnek uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-206">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="1b85c-207">Azure App Service dağıtılan bir uygulama, hizmet oluşturulduğunda Azure AD 'ye otomatik olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-207">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="1b85c-208">Aşağıdaki komutta kullanılmak üzere dağıtımdan nesne KIMLIĞINI edinin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-208">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="1b85c-209">Nesne KIMLIĞI, **Identity** App Service panelindeki Azure Portal gösterilir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-209">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="1b85c-210">Azure CLı ve uygulamanın nesne KIMLIĞINI kullanarak, uygulama `list` ve `get` anahtar kasasına erişim izinleri sağlayın:</span><span class="sxs-lookup"><span data-stu-id="1b85c-210">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="1b85c-211">Azure CLı, PowerShell veya Azure portal kullanarak **uygulamayı yeniden başlatın** .</span><span class="sxs-lookup"><span data-stu-id="1b85c-211">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="1b85c-212">Örnek uygulama:</span><span class="sxs-lookup"><span data-stu-id="1b85c-212">The sample app:</span></span>

* <span data-ttu-id="1b85c-213">`AzureServiceTokenProvider`Bir bağlantı dizesi olmadan sınıfın bir örneğini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1b85c-213">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="1b85c-214">Bir bağlantı dizesi sağlanmazsa, sağlayıcı Azure kaynakları için yönetilen kimliklerden bir erişim belirteci almaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-214">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="1b85c-215"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> `AzureServiceTokenProvider` Örnek belirteci geri çağırması ile yeni bir oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1b85c-215">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="1b85c-216"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Örnek, <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> tüm gizli değerleri yükleyen ve çift tire ( `--` ) değerini anahtar adlarında iki nokta () ile değiştirir `:` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-216">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="1b85c-217">Anahtar Kasası adı örnek değeri:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="1b85c-217">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="1b85c-218">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="1b85c-218">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="1b85c-219">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-219">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="1b85c-220">Geliştirme ortamında, gizli değerler, `_dev` Kullanıcı gizli dizileri tarafından sağlandıklarından, soneke sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-220">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="1b85c-221">Üretim ortamında, değerler `_prod` Azure Key Vault tarafından sağlandıklarından sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-221">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="1b85c-222">Bir `Access denied` hata alırsanız, uygulamanın Azure AD 'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladıklarını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-222">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="1b85c-223">Azure 'da hizmeti yeniden başlattığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="1b85c-223">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="1b85c-224">Sağlayıcıyı yönetilen bir kimlik ve bir Azure DevOps işlem hattı ile kullanma hakkında bilgi için bkz. [yönetilen hizmet KIMLIĞIYLE VM 'ye Azure Resource Manager hizmet bağlantısı oluşturma](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="1b85c-224">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="1b85c-225">Yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="1b85c-225">Configuration options</span></span>

<span data-ttu-id="1b85c-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>şunları kabul edebilir <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :</span><span class="sxs-lookup"><span data-stu-id="1b85c-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="1b85c-227">Özellik</span><span class="sxs-lookup"><span data-stu-id="1b85c-227">Property</span></span>         | <span data-ttu-id="1b85c-228">Açıklama</span><span class="sxs-lookup"><span data-stu-id="1b85c-228">Description</span></span> |
| ---
<span data-ttu-id="1b85c-229">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1b85c-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b85c-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-230">'Blazor'</span></span>
- <span data-ttu-id="1b85c-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b85c-231">'Identity'</span></span>
- <span data-ttu-id="1b85c-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b85c-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b85c-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-233">'Razor'</span></span>
- <span data-ttu-id="1b85c-234">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1b85c-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b85c-235">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1b85c-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b85c-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-236">'Blazor'</span></span>
- <span data-ttu-id="1b85c-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b85c-237">'Identity'</span></span>
- <span data-ttu-id="1b85c-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b85c-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b85c-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-239">'Razor'</span></span>
- <span data-ttu-id="1b85c-240">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1b85c-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b85c-241">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1b85c-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b85c-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-242">'Blazor'</span></span>
- <span data-ttu-id="1b85c-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b85c-243">'Identity'</span></span>
- <span data-ttu-id="1b85c-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b85c-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b85c-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-245">'Razor'</span></span>
- <span data-ttu-id="1b85c-246">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1b85c-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b85c-247">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1b85c-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b85c-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-248">'Blazor'</span></span>
- <span data-ttu-id="1b85c-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b85c-249">'Identity'</span></span>
- <span data-ttu-id="1b85c-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b85c-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b85c-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-251">'Razor'</span></span>
- <span data-ttu-id="1b85c-252">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1b85c-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b85c-253">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1b85c-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b85c-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-254">'Blazor'</span></span>
- <span data-ttu-id="1b85c-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b85c-255">'Identity'</span></span>
- <span data-ttu-id="1b85c-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b85c-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b85c-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-257">'Razor'</span></span>
- <span data-ttu-id="1b85c-258">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1b85c-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b85c-259">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1b85c-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b85c-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-260">'Blazor'</span></span>
- <span data-ttu-id="1b85c-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b85c-261">'Identity'</span></span>
- <span data-ttu-id="1b85c-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b85c-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b85c-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-263">'Razor'</span></span>
- <span data-ttu-id="1b85c-264">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1b85c-264">'SignalR' uid:</span></span> 

<span data-ttu-id="1b85c-265">-------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1b85c-265">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b85c-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-266">'Blazor'</span></span>
- <span data-ttu-id="1b85c-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b85c-267">'Identity'</span></span>
- <span data-ttu-id="1b85c-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b85c-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b85c-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-269">'Razor'</span></span>
- <span data-ttu-id="1b85c-270">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1b85c-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b85c-271">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1b85c-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b85c-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-272">'Blazor'</span></span>
- <span data-ttu-id="1b85c-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b85c-273">'Identity'</span></span>
- <span data-ttu-id="1b85c-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b85c-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b85c-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-275">'Razor'</span></span>
- <span data-ttu-id="1b85c-276">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1b85c-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="1b85c-277">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1b85c-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1b85c-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-278">'Blazor'</span></span>
- <span data-ttu-id="1b85c-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1b85c-279">'Identity'</span></span>
- <span data-ttu-id="1b85c-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1b85c-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="1b85c-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1b85c-281">'Razor'</span></span>
- <span data-ttu-id="1b85c-282">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1b85c-282">'SignalR' uid:</span></span> 

<span data-ttu-id="1b85c-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> değerlerini almak için kullanmak üzere.</span><span class="sxs-lookup"><span data-stu-id="1b85c-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> <span data-ttu-id="1b85c-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> gizli dizi yüklemeyi denetlemek için kullanılan örnek.</span><span class="sxs-lookup"><span data-stu-id="1b85c-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> <span data-ttu-id="1b85c-285">| | `ReloadInterval` | `Timespan` anahtar kasasındaki değişiklikleri yoklamaya yönelik denemeler arasında bekleme.</span><span class="sxs-lookup"><span data-stu-id="1b85c-285">| | `ReloadInterval` | `Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="1b85c-286">Varsayılan değer `null` (yapılandırma yeniden yüklenmez).</span><span class="sxs-lookup"><span data-stu-id="1b85c-286">The default value is `null` (configuration isn't reloaded).</span></span> <span data-ttu-id="1b85c-287">| | `Vault`          | Anahtar Kasası URI 'SI.</span><span class="sxs-lookup"><span data-stu-id="1b85c-287">| | `Vault`          | Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="1b85c-288">Anahtar adı öneki kullanın</span><span class="sxs-lookup"><span data-stu-id="1b85c-288">Use a key name prefix</span></span>

<span data-ttu-id="1b85c-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, Anahtar Kasası gizli dizilerini yapılandırma anahtarlarına nasıl dönüştürdüğünü denetlemenize olanak tanıyan, uygulamasını kabul eden bir aşırı yükleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="1b85c-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="1b85c-290">Örneğin, uygulama başlangıcında sağladığınız önek değerine göre gizli değerleri yüklemek için arabirimini uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1b85c-290">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="1b85c-291">Bu, örneğin, uygulama sürümüne göre gizli dizileri yüklemeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="1b85c-291">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="1b85c-292">Birden çok uygulama için gizli dizileri aynı kasaya yerleştirmek veya çevresel gizli dizileri (örneğin, *geliştirme* ve *Üretim* gizlilikleri) aynı kasaya yerleştirmek için Anahtar Kasası gizli dizileri üzerinde ön ekleri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-292">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="1b85c-293">Farklı uygulama ve geliştirme/üretim ortamlarının, uygulama ortamlarını en yüksek düzeyde güvenlik için yalıtmak üzere ayrı anahtar kasaları kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="1b85c-293">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="1b85c-294">Aşağıdaki örnekte, için anahtar kasasında (ve geliştirme ortamı için gizli Yönetim Aracı kullanılarak) bir gizli dizi oluşturulur `5000-AppSecret` (Anahtar Kasası gizli adlarında dönemlere izin verilmez).</span><span class="sxs-lookup"><span data-stu-id="1b85c-294">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="1b85c-295">Bu gizli anahtar, uygulamanın 5.0.0.0 sürümü için bir uygulama gizli anahtarı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1b85c-295">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="1b85c-296">Uygulamanın başka bir sürümü olan 5.1.0.0, anahtar kasasına (ve gizli Yönetici Aracı kullanılarak) bir gizli dizi eklenir `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-296">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="1b85c-297">Her bir uygulama sürümü sürümü sürümlü gizli değerini yapılandırma olarak yükler `AppSecret` , bu, gizli anahtarı yüklerken sürümü de kapatıyor.</span><span class="sxs-lookup"><span data-stu-id="1b85c-297">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="1b85c-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>özel bir ile çağrılır <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="1b85c-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="1b85c-299"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Uygulama, doğru gizli anahtarı yapılandırmaya yüklemek için gizli dizi sürüm öneklerine tepki verir:</span><span class="sxs-lookup"><span data-stu-id="1b85c-299">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="1b85c-300">`Load`adı önekiyle başladığında bir gizli dizi yükler.</span><span class="sxs-lookup"><span data-stu-id="1b85c-300">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="1b85c-301">Diğer gizlilikler yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="1b85c-301">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="1b85c-302">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="1b85c-302">`GetKey`:</span></span>
  * <span data-ttu-id="1b85c-303">Gizli dizi adından öneki kaldırır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-303">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="1b85c-304">`KeyDelimiter`Yapılandırmada kullanılan sınırlayıcı olan (genellikle iki nokta üst üste), herhangi bir ada sahip iki kısa çizgi koyar.</span><span class="sxs-lookup"><span data-stu-id="1b85c-304">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="1b85c-305">Azure Key Vault gizli dizi adlarında bir iki nokta üst üste izin vermez.</span><span class="sxs-lookup"><span data-stu-id="1b85c-305">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="1b85c-306">`Load`Yöntemi, sürüm ön ekine sahip olanları bulmak için kasa gizli dizileri aracılığıyla yinelenen bir sağlayıcı algoritması tarafından çağırılır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-306">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="1b85c-307">İle bir sürüm ön eki bulunduğunda `Load` , algoritma, `GetKey` gizli anahtar adının yapılandırma adını döndürmek için yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-307">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="1b85c-308">Gizlilik adından sürüm önekini kaldırır ve uygulamanın yapılandırma adı-değer çiftlerine yüklemek için gizli dizi adının geri kalanını döndürür.</span><span class="sxs-lookup"><span data-stu-id="1b85c-308">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="1b85c-309">Bu yaklaşım uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="1b85c-309">When this approach is implemented:</span></span>

1. <span data-ttu-id="1b85c-310">Uygulamanın proje dosyasında belirtilen sürümü.</span><span class="sxs-lookup"><span data-stu-id="1b85c-310">The app's version specified in the app's project file.</span></span> <span data-ttu-id="1b85c-311">Aşağıdaki örnekte, uygulamanın sürümü olarak ayarlanır `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="1b85c-311">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="1b85c-312">`<UserSecretsId>`Uygulamanın proje dosyasında bir özelliğin var olduğunu, burada Kullanıcı tarafından sağlanan BIR GUID olduğunu onaylayın `{GUID}` :</span><span class="sxs-lookup"><span data-stu-id="1b85c-312">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="1b85c-313">[Gizli dizi yöneticisi aracıyla](xref:security/app-secrets)aşağıdaki gizli dizileri yerel olarak kaydedin:</span><span class="sxs-lookup"><span data-stu-id="1b85c-313">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="1b85c-314">Gizlilikler, aşağıdaki Azure CLı komutları kullanılarak Azure Key Vault kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="1b85c-314">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="1b85c-315">Uygulama çalıştırıldığında, Anahtar Kasası gizli dizileri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-315">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="1b85c-316">İçin dize gizli dizisi, uygulamanın `5000-AppSecret` Proje dosyasında () belirtilen uygulamanın sürümüyle eşleştirilir `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-316">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="1b85c-317">Sürüm `5000` (tireyle birlikte), anahtar adından çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-317">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="1b85c-318">Uygulamanın tamamında, anahtarla yapılandırmayı okumak `AppSecret` gizli değeri yükler.</span><span class="sxs-lookup"><span data-stu-id="1b85c-318">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="1b85c-319">Uygulamanın sürümü proje dosyasında olarak değiştirilirse `5.1.0.0` ve uygulama yeniden çalıştırıldığında, döndürülen gizli değer `5.1.0.0_secret_value_dev` geliştirme ortamında ve `5.1.0.0_secret_value_prod` üretimde bulunur.</span><span class="sxs-lookup"><span data-stu-id="1b85c-319">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="1b85c-320">Ayrıca, kendi uygulamanızı da sağlayabilirsiniz <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="1b85c-320">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="1b85c-321">Özel istemci, uygulama genelinde istemcinin tek bir örneğini paylaşıma izin verir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-321">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="1b85c-322">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="1b85c-322">Bind an array to a class</span></span>

<span data-ttu-id="1b85c-323">Sağlayıcı, bir POCO dizisine bağlamak için yapılandırma değerlerini bir diziye okuyabilme özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-323">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="1b85c-324">Anahtarların iki nokta () ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken `:` , bir diziyi oluşturan anahtarları ( `:0:` ,,) ayırt etmek için bir sayısal anahtar kesimi kullanılır `:1:` &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-324">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="1b85c-325">Daha fazla bilgi için bkz. [yapılandırma: diziyi bir sınıfa bağlama](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="1b85c-325">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="1b85c-326">Azure Key Vault anahtarlar ayırıcı olarak iki nokta üst üste kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="1b85c-326">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="1b85c-327">Bu konuda açıklanan yaklaşım, `--` hiyerarşik değerler (bölümler) için bir ayırıcı olarak çift tire () kullanır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-327">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="1b85c-328">Dizi anahtarları çift tireler ve sayısal anahtar kesimleri ( `--0--` ,,) ile birlikte Azure Key Vault `--1--` depolanır &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-328">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="1b85c-329">Bir JSON dosyası tarafından sunulan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-329">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="1b85c-330">`WriteTo`Dizide günlüğe kaydetme hedeflerini açıklayan Iki Serilog girişi yansıtan iki nesne değişmez değeri *sinks*vardır:</span><span class="sxs-lookup"><span data-stu-id="1b85c-330">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="1b85c-331">Önceki JSON dosyasında gösterilen yapılandırma, Çift tire ( `--` ) gösterimi ve sayısal kesimleri kullanarak Azure Key Vault depolanır:</span><span class="sxs-lookup"><span data-stu-id="1b85c-331">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="1b85c-332">Anahtar</span><span class="sxs-lookup"><span data-stu-id="1b85c-332">Key</span></span> | <span data-ttu-id="1b85c-333">Değer</span><span class="sxs-lookup"><span data-stu-id="1b85c-333">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="1b85c-334">Gizli dizileri yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="1b85c-334">Reload secrets</span></span>

<span data-ttu-id="1b85c-335">Gizli diziler çağrılana kadar önbelleğe alınır `IConfigurationRoot.Reload()` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-335">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="1b85c-336">Anahtar kasasındaki zaman aşımına uğradı, devre dışı ve güncelleştirilmiş gizli dizileri, yürütülene kadar uygulama tarafından dikkate alınmıyor `Reload` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-336">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="1b85c-337">Devre dışı ve süre dolma parolaları</span><span class="sxs-lookup"><span data-stu-id="1b85c-337">Disabled and expired secrets</span></span>

<span data-ttu-id="1b85c-338">Devre dışı ve son kullanma parolası bir oluşturur <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="1b85c-338">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="1b85c-339">Uygulamanın üretilmesini engellemek için, farklı bir yapılandırma sağlayıcısı kullanarak yapılandırmayı sağlayın veya devre dışı ya da süre dolma parolasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-339">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1b85c-340">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="1b85c-340">Troubleshoot</span></span>

<span data-ttu-id="1b85c-341">Uygulama, sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Core günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-341">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="1b85c-342">Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:</span><span class="sxs-lookup"><span data-stu-id="1b85c-342">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="1b85c-343">Uygulama veya sertifika Azure Active Directory içinde doğru yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="1b85c-343">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="1b85c-344">Anahtar Kasası Azure Key Vault içinde yok.</span><span class="sxs-lookup"><span data-stu-id="1b85c-344">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="1b85c-345">Uygulamanın anahtar kasasına erişme yetkisi yok.</span><span class="sxs-lookup"><span data-stu-id="1b85c-345">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="1b85c-346">Erişim ilkesi `Get` ve `List` izinleri içermez.</span><span class="sxs-lookup"><span data-stu-id="1b85c-346">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="1b85c-347">Anahtar kasasında yapılandırma verileri (ad-değer çifti) yanlış olarak adlandırılmış, eksik, devre dışı veya zaman aşımına uğradı.</span><span class="sxs-lookup"><span data-stu-id="1b85c-347">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="1b85c-348">Uygulamanın Anahtar Kasası adı ( `KeyVaultName` ), Azure AD uygulama kimliği ( `AzureADApplicationId` ) veya Azure AD sertifika parmak izi ( `AzureADCertThumbprint` ) yanlış.</span><span class="sxs-lookup"><span data-stu-id="1b85c-348">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="1b85c-349">Yüklemeye çalıştığınız değer için uygulamada yapılandırma anahtarı (ad) yanlış.</span><span class="sxs-lookup"><span data-stu-id="1b85c-349">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="1b85c-350">Uygulama için erişim ilkesini anahtar kasasına eklerken, ilke oluşturulmuştur, ancak **erişim ilkeleri** Kullanıcı arabiriminde **Kaydet** düğmesi seçilmedi.</span><span class="sxs-lookup"><span data-stu-id="1b85c-350">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1b85c-351">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1b85c-351">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="1b85c-352">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="1b85c-352">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="1b85c-353">Microsoft Azure: Key Vault belgeler</span><span class="sxs-lookup"><span data-stu-id="1b85c-353">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="1b85c-354">Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma</span><span class="sxs-lookup"><span data-stu-id="1b85c-354">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="1b85c-355">KeyVaultClient sınıfı</span><span class="sxs-lookup"><span data-stu-id="1b85c-355">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="1b85c-356">Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma</span><span class="sxs-lookup"><span data-stu-id="1b85c-356">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="1b85c-357">Öğretici: .NET 'te Azure Windows sanal makinesi ile Azure Key Vault kullanma</span><span class="sxs-lookup"><span data-stu-id="1b85c-357">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1b85c-358">Bu belgede, Azure Key Vault gizliliklerden uygulama yapılandırma değerlerini yüklemek için [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) yapılandırma sağlayıcısının nasıl kullanılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-358">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="1b85c-359">Azure Key Vault, uygulama ve hizmetler tarafından kullanılan şifreleme anahtarlarını ve gizli dizileri koruma konusunda yardımcı olan bulut tabanlı bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-359">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="1b85c-360">ASP.NET Core uygulamalarla Azure Key Vault kullanmaya yönelik yaygın senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="1b85c-360">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="1b85c-361">Hassas yapılandırma verilerine erişimi denetleme.</span><span class="sxs-lookup"><span data-stu-id="1b85c-361">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="1b85c-362">Yapılandırma verilerini depolarken FIPS 140-2 düzey 2 doğrulanan donanım güvenlik modülleri (HSM 'ler) gereksinimini karşılarsınız.</span><span class="sxs-lookup"><span data-stu-id="1b85c-362">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="1b85c-363">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1b85c-363">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="1b85c-364">Paketler</span><span class="sxs-lookup"><span data-stu-id="1b85c-364">Packages</span></span>

<span data-ttu-id="1b85c-365">[Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) paketine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-365">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="1b85c-366">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="1b85c-366">Sample app</span></span>

<span data-ttu-id="1b85c-367">Örnek uygulama, `#define` *program.cs* dosyasının en üstünde yer aldığı ifadeye göre belirlenen iki moddan birinde çalışır:</span><span class="sxs-lookup"><span data-stu-id="1b85c-367">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="1b85c-368">`Certificate`: Azure Key Vault depolanan gizli dizileri erişmek için Azure Key Vault Istemci KIMLIĞI ve X. 509.952 sertifikası kullanımını gösterir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-368">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="1b85c-369">Örneğin bu sürümü, Azure App Service dağıtılan herhangi bir konumdan veya ASP.NET Core uygulamasına hizmet veren herhangi bir konağa çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-369">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="1b85c-370">`Managed`: Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasıyla Azure Key Vault üzere uygulamanın kimliğini doğrulamak için [Azure kaynakları Için yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-370">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="1b85c-371">Kimlik doğrulaması için Yönetilen kimlikler kullanıldığında, bir Azure AD uygulama KIMLIĞI ve parolası (gizli anahtar) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-371">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="1b85c-372">`Managed`Örneğin sürümünün Azure 'a dağıtılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-372">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="1b85c-373">[Azure kaynakları Için Yönetilen kimlikler bölümünü kullanma](#use-managed-identities-for-azure-resources) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-373">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="1b85c-374">Önişlemci yönergeleri () kullanarak örnek bir uygulamanın nasıl yapılandırılacağı hakkında daha fazla bilgi için `#define` bkz <xref:index#preprocessor-directives-in-sample-code> ..</span><span class="sxs-lookup"><span data-stu-id="1b85c-374">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="1b85c-375">Geliştirme ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="1b85c-375">Secret storage in the Development environment</span></span>

<span data-ttu-id="1b85c-376">[Gizli dizi Yöneticisi aracını](xref:security/app-secrets)kullanarak gizli dizileri yerel olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-376">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="1b85c-377">Örnek uygulama, geliştirme ortamındaki yerel makinede çalıştığında, gizli anahtar Yöneticisi deposundan gizli diziler yüklenir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-377">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="1b85c-378">Gizli dizi Yöneticisi Aracı, `<UserSecretsId>` uygulamanın proje dosyasında bir özellik gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-378">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="1b85c-379">Özellik değerini ( `{GUID}` ) herhangi bir BENZERSIZ GUID 'ye ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="1b85c-379">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="1b85c-380">Gizlilikler ad-değer çiftleri olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1b85c-380">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="1b85c-381">Hiyerarşik değerler (yapılandırma bölümleri) `:` [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index) anahtarı adlarında ayırıcı olarak bir (iki nokta üst üste) kullanır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-381">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="1b85c-382">Gizli dizi, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır; burada `{SECRET NAME}` ad ve `{SECRET VALUE}` değerdir:</span><span class="sxs-lookup"><span data-stu-id="1b85c-382">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="1b85c-383">Örnek uygulamanın gizli dizilerini ayarlamak için, projenin [içerik kökünden](xref:fundamentals/index#content-root) bir komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="1b85c-383">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="1b85c-384">Bu gizlilikler, [Azure Key Vault bölümündeki üretim ortamındaki gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında Azure Key Vault depolandığında, `_dev` sonek olarak değiştirilir `_prod` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-384">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="1b85c-385">Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren bir görsel ipucu sağlar.</span><span class="sxs-lookup"><span data-stu-id="1b85c-385">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="1b85c-386">Azure Key Vault ile üretim ortamında gizli dizi</span><span class="sxs-lookup"><span data-stu-id="1b85c-386">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="1b85c-387">Hızlı başlangıç tarafından sunulan yönergeler [: Azure CLI kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-cli) , bir Azure Key Vault oluşturmak ve örnek uygulama tarafından kullanılan gizli dizileri depolamak için burada özetlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-387">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="1b85c-388">Daha ayrıntılı bilgi için konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-388">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="1b85c-389">[Azure Portal](https://portal.azure.com/)aşağıdaki yöntemlerden birini kullanarak Azure Cloud Shell 'i açın:</span><span class="sxs-lookup"><span data-stu-id="1b85c-389">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="1b85c-390">Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-390">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="1b85c-391">Metin kutusunda "Azure CLı" arama dizesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-391">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="1b85c-392">**Cloud Shell Başlat** düğmesini kullanarak tarayıcınızda Cloud Shell açın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-392">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="1b85c-393">Azure portal sağ üst köşesindeki menüdeki **Cloud Shell** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-393">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="1b85c-394">Daha fazla bilgi için bkz. [Azure CLI](/cli/azure/) ve [Azure Cloud Shell Genel Bakış](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="1b85c-394">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="1b85c-395">Henüz kimlik doğrulamasından geçirilmemişse komutuyla oturum açın `az login` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-395">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="1b85c-396">Aşağıdaki komutla bir kaynak grubu oluşturun; burada, `{RESOURCE GROUP NAME}` Yeni kaynak grubu için kaynak grubu adı ve `{LOCATION}` Azure bölgesi (Datacenter) olur:</span><span class="sxs-lookup"><span data-stu-id="1b85c-396">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="1b85c-397">Aşağıdaki komutla kaynak grubunda bir Anahtar Kasası oluşturun; burada `{KEY VAULT NAME}` Yeni anahtar kasasının adı ve `{LOCATION}` Azure bölgesi (Datacenter) bulunur:</span><span class="sxs-lookup"><span data-stu-id="1b85c-397">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="1b85c-398">Anahtar kasasında ad-değer çiftleri olarak gizli diziler oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1b85c-398">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="1b85c-399">Azure Key Vault gizli dizi adları, alfasayısal karakterler ve tireler ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-399">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="1b85c-400">Hiyerarşik değerler (yapılandırma bölümleri) `--` ayırıcı olarak (iki tire) kullanır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-400">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="1b85c-401">Genellikle [ASP.NET Core yapılandırmasındaki](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlandırmak için kullanılan iki nokta üst üste, Anahtar Kasası gizli adlarında izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="1b85c-401">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="1b85c-402">Bu nedenle, gizlilikler uygulamanın yapılandırmasına yüklendiğinde iki tire kullanılır ve iki nokta üst üste bulunur.</span><span class="sxs-lookup"><span data-stu-id="1b85c-402">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="1b85c-403">Aşağıdaki gizlilikler örnek uygulamayla kullanım içindir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-403">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="1b85c-404">Değerler, `_prod` `_dev` Kullanıcı parolalarından geliştirme ortamında yüklenen sonek değerlerinden ayırt etmek için bir sonek içerir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-404">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="1b85c-405">`{KEY VAULT NAME}`Önceki adımda oluşturduğunuz anahtar kasasının adıyla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="1b85c-405">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="1b85c-406">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanın</span><span class="sxs-lookup"><span data-stu-id="1b85c-406">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="1b85c-407">**Uygulama Azure dışında barındırıldığı zaman**bir anahtar kasasında kimlik doğrulaması yapmak IÇIN Azure AD, Azure Key Vault ve uygulamayı bir Azure ACTIVE DIRECTORY uygulama kimliği ve X. 509.440 sertifikası kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-407">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="1b85c-408">Daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="1b85c-408">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="1b85c-409">Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanılması desteklenmekle birlikte, Azure 'da bir uygulama barındırılırken [Azure kaynakları Için Yönetilen kimlikler](#use-managed-identities-for-azure-resources) kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="1b85c-409">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="1b85c-410">Yönetilen kimlikler, uygulamada veya geliştirme ortamında bir sertifikanın depolanmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="1b85c-410">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="1b85c-411">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki ifade olarak AYARLANDıĞıNDA bir uygulama kimliği ve X. 509.440 sertifikası kullanır `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-411">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="1b85c-412">PKCS # 12 Arşivi (*. pfx*) sertifikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1b85c-412">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="1b85c-413">Sertifika oluşturma seçenekleri Windows ve [OpenSSL](https://www.openssl.org/) [üzerinde MakeCert](/windows/desktop/seccrypto/makecert) içerir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-413">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="1b85c-414">Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükler.</span><span class="sxs-lookup"><span data-stu-id="1b85c-414">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="1b85c-415">Anahtarı verilebilir olarak işaretlemek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-415">Marking the key as exportable is optional.</span></span> <span data-ttu-id="1b85c-416">Sertifikanın daha sonra bu işlemde kullanılan parmak izini aklınızda bulunur.</span><span class="sxs-lookup"><span data-stu-id="1b85c-416">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="1b85c-417">PKCS # 12 Arşivi (*. pfx*) sertifikasını der kodlu bir sertifika (*. cer*) olarak dışarı aktarın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-417">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="1b85c-418">Uygulamayı Azure AD 'ye kaydedin (**uygulama kayıtları**).</span><span class="sxs-lookup"><span data-stu-id="1b85c-418">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="1b85c-419">DER kodlu sertifikayı (*. cer*) Azure AD 'ye yükleyin:</span><span class="sxs-lookup"><span data-stu-id="1b85c-419">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="1b85c-420">Azure AD 'de uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-420">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="1b85c-421">**Sertifikalar & gizli**dizi sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-421">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="1b85c-422">Ortak anahtarı içeren sertifikayı karşıya yüklemek için **sertifikayı karşıya yükle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-422">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="1b85c-423">*. Cer*, *. pek*veya *. CRT* sertifikası kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-423">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="1b85c-424">Anahtar Kasası adı, uygulama KIMLIĞI ve sertifika parmak izini uygulamanın *appSettings. JSON* dosyasında depolayın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-424">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="1b85c-425">Azure portal **ana** kasaları ' ne gidin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-425">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="1b85c-426">Azure Key Vault bölümünde, [üretim ortamındaki gizli dizi deposunda](#secret-storage-in-the-production-environment-with-azure-key-vault) oluşturduğunuz anahtar kasasını seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-426">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="1b85c-427">**Erişim ilkeleri**'ni seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-427">Select **Access policies**.</span></span>
1. <span data-ttu-id="1b85c-428">**Erişim Ilkesi Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-428">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="1b85c-429">**Gizli izinleri** açın ve uygulamaya **Get** ve **list** izinleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-429">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="1b85c-430">**Sorumlu Seç** ' i seçin ve kayıtlı uygulamayı ada göre seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-430">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="1b85c-431">**Seç** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-431">Select the **Select** button.</span></span>
1. <span data-ttu-id="1b85c-432">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-432">Select **OK**.</span></span>
1. <span data-ttu-id="1b85c-433">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-433">Select **Save**.</span></span>
1. <span data-ttu-id="1b85c-434">Uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-434">Deploy the app.</span></span>

<span data-ttu-id="1b85c-435">`Certificate`Örnek uygulama, yapılandırma değerlerini, `IConfigurationRoot` parola adı ile aynı adla alır:</span><span class="sxs-lookup"><span data-stu-id="1b85c-435">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="1b85c-436">Hiyerarşik olmayan değerler: için değeri `SecretName` ile elde edilir `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-436">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="1b85c-437">Hiyerarşik değerler (bölümler): `:` (iki nokta üst üste) gösterimini veya `GetSection` genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-437">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="1b85c-438">Yapılandırma değerini elde etmek için şu yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="1b85c-438">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="1b85c-439">X. 509.440 sertifikası işletim sistemi tarafından yönetiliyor.</span><span class="sxs-lookup"><span data-stu-id="1b85c-439">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="1b85c-440">Uygulama, <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appSettings. JSON* dosyası tarafından sağlanan değerlerle çağırır:</span><span class="sxs-lookup"><span data-stu-id="1b85c-440">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="1b85c-441">Örnek değerler:</span><span class="sxs-lookup"><span data-stu-id="1b85c-441">Example values:</span></span>

* <span data-ttu-id="1b85c-442">Anahtar Kasası adı:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="1b85c-442">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="1b85c-443">Uygulama KIMLIĞI:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="1b85c-443">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="1b85c-444">Sertifika parmak izi:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="1b85c-444">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="1b85c-445">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="1b85c-445">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="1b85c-446">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-446">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="1b85c-447">Geliştirme ortamında, gizli anahtar değerleri sonek ile yüklenir `_dev` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-447">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="1b85c-448">Üretim ortamında, değerler sonek ile yüklenir `_prod` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-448">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="1b85c-449">Azure kaynakları için Yönetilen kimlikler kullanma</span><span class="sxs-lookup"><span data-stu-id="1b85c-449">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="1b85c-450">Azure **'a dağıtılan bir uygulama** , [Azure kaynakları için yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanarak uygulamanın, KIMLIK bilgileri (uygulama kimliği ve parola/istemci gizli anahtarı) olmadan Azure AD kimlik doğrulaması kullanarak Azure Key Vault kimlik doğrulamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-450">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="1b85c-451">Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki Ifade olarak ayarlandığında Azure kaynakları için Yönetilen kimlikler kullanır `Managed` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-451">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="1b85c-452">Uygulamanın *appSettings. JSON* dosyasına kasa adını girin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-452">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="1b85c-453">Örnek uygulama sürüme ayarlandığında bir uygulama KIMLIĞI ve parola (Istemci gizli anahtarı) gerektirmez `Managed` , bu nedenle bu yapılandırma girişlerini yoksayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1b85c-453">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="1b85c-454">Uygulama Azure 'a dağıtılır ve Azure, yalnızca *appSettings. JSON* dosyasında depolanan kasa adını kullanarak Azure Key Vault erişmek için uygulamanın kimliğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="1b85c-454">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="1b85c-455">Azure App Service için örnek uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-455">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="1b85c-456">Azure App Service dağıtılan bir uygulama, hizmet oluşturulduğunda Azure AD 'ye otomatik olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-456">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="1b85c-457">Aşağıdaki komutta kullanılmak üzere dağıtımdan nesne KIMLIĞINI edinin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-457">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="1b85c-458">Nesne KIMLIĞI, **Identity** App Service panelindeki Azure Portal gösterilir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-458">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="1b85c-459">Azure CLı ve uygulamanın nesne KIMLIĞINI kullanarak, uygulama `list` ve `get` anahtar kasasına erişim izinleri sağlayın:</span><span class="sxs-lookup"><span data-stu-id="1b85c-459">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="1b85c-460">Azure CLı, PowerShell veya Azure portal kullanarak **uygulamayı yeniden başlatın** .</span><span class="sxs-lookup"><span data-stu-id="1b85c-460">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="1b85c-461">Örnek uygulama:</span><span class="sxs-lookup"><span data-stu-id="1b85c-461">The sample app:</span></span>

* <span data-ttu-id="1b85c-462">`AzureServiceTokenProvider`Bir bağlantı dizesi olmadan sınıfın bir örneğini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1b85c-462">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="1b85c-463">Bir bağlantı dizesi sağlanmazsa, sağlayıcı Azure kaynakları için yönetilen kimliklerden bir erişim belirteci almaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-463">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="1b85c-464"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> `AzureServiceTokenProvider` Örnek belirteci geri çağırması ile yeni bir oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1b85c-464">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="1b85c-465"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Örnek, <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> tüm gizli değerleri yükleyen ve çift tire ( `--` ) değerini anahtar adlarında iki nokta () ile değiştirir `:` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-465">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="1b85c-466">Anahtar Kasası adı örnek değeri:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="1b85c-466">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="1b85c-467">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="1b85c-467">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="1b85c-468">Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-468">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="1b85c-469">Geliştirme ortamında, gizli değerler, `_dev` Kullanıcı gizli dizileri tarafından sağlandıklarından, soneke sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-469">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="1b85c-470">Üretim ortamında, değerler `_prod` Azure Key Vault tarafından sağlandıklarından sonek ile yüklenir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-470">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="1b85c-471">Bir `Access denied` hata alırsanız, uygulamanın Azure AD 'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladıklarını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-471">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="1b85c-472">Azure 'da hizmeti yeniden başlattığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="1b85c-472">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="1b85c-473">Sağlayıcıyı yönetilen bir kimlik ve bir Azure DevOps işlem hattı ile kullanma hakkında bilgi için bkz. [yönetilen hizmet KIMLIĞIYLE VM 'ye Azure Resource Manager hizmet bağlantısı oluşturma](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="1b85c-473">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="1b85c-474">Anahtar adı öneki kullanın</span><span class="sxs-lookup"><span data-stu-id="1b85c-474">Use a key name prefix</span></span>

<span data-ttu-id="1b85c-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, Anahtar Kasası gizli dizilerini yapılandırma anahtarlarına nasıl dönüştürdüğünü denetlemenize olanak tanıyan, uygulamasını kabul eden bir aşırı yükleme sağlar.</span><span class="sxs-lookup"><span data-stu-id="1b85c-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="1b85c-476">Örneğin, uygulama başlangıcında sağladığınız önek değerine göre gizli değerleri yüklemek için arabirimini uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1b85c-476">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="1b85c-477">Bu, örneğin, uygulama sürümüne göre gizli dizileri yüklemeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="1b85c-477">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="1b85c-478">Birden çok uygulama için gizli dizileri aynı kasaya yerleştirmek veya çevresel gizli dizileri (örneğin, *geliştirme* ve *Üretim* gizlilikleri) aynı kasaya yerleştirmek için Anahtar Kasası gizli dizileri üzerinde ön ekleri kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="1b85c-478">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="1b85c-479">Farklı uygulama ve geliştirme/üretim ortamlarının, uygulama ortamlarını en yüksek düzeyde güvenlik için yalıtmak üzere ayrı anahtar kasaları kullanmasını öneririz.</span><span class="sxs-lookup"><span data-stu-id="1b85c-479">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="1b85c-480">Aşağıdaki örnekte, için anahtar kasasında (ve geliştirme ortamı için gizli Yönetim Aracı kullanılarak) bir gizli dizi oluşturulur `5000-AppSecret` (Anahtar Kasası gizli adlarında dönemlere izin verilmez).</span><span class="sxs-lookup"><span data-stu-id="1b85c-480">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="1b85c-481">Bu gizli anahtar, uygulamanın 5.0.0.0 sürümü için bir uygulama gizli anahtarı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1b85c-481">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="1b85c-482">Uygulamanın başka bir sürümü olan 5.1.0.0, anahtar kasasına (ve gizli Yönetici Aracı kullanılarak) bir gizli dizi eklenir `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-482">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="1b85c-483">Her bir uygulama sürümü sürümü sürümlü gizli değerini yapılandırma olarak yükler `AppSecret` , bu, gizli anahtarı yüklerken sürümü de kapatıyor.</span><span class="sxs-lookup"><span data-stu-id="1b85c-483">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="1b85c-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>özel bir ile çağrılır <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="1b85c-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="1b85c-485"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Uygulama, doğru gizli anahtarı yapılandırmaya yüklemek için gizli dizi sürüm öneklerine tepki verir:</span><span class="sxs-lookup"><span data-stu-id="1b85c-485">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="1b85c-486">`Load`adı önekiyle başladığında bir gizli dizi yükler.</span><span class="sxs-lookup"><span data-stu-id="1b85c-486">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="1b85c-487">Diğer gizlilikler yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="1b85c-487">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="1b85c-488">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="1b85c-488">`GetKey`:</span></span>
  * <span data-ttu-id="1b85c-489">Gizli dizi adından öneki kaldırır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-489">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="1b85c-490">`KeyDelimiter`Yapılandırmada kullanılan sınırlayıcı olan (genellikle iki nokta üst üste), herhangi bir ada sahip iki kısa çizgi koyar.</span><span class="sxs-lookup"><span data-stu-id="1b85c-490">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="1b85c-491">Azure Key Vault gizli dizi adlarında bir iki nokta üst üste izin vermez.</span><span class="sxs-lookup"><span data-stu-id="1b85c-491">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="1b85c-492">`Load`Yöntemi, sürüm ön ekine sahip olanları bulmak için kasa gizli dizileri aracılığıyla yinelenen bir sağlayıcı algoritması tarafından çağırılır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-492">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="1b85c-493">İle bir sürüm ön eki bulunduğunda `Load` , algoritma, `GetKey` gizli anahtar adının yapılandırma adını döndürmek için yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-493">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="1b85c-494">Gizlilik adından sürüm önekini kaldırır ve uygulamanın yapılandırma adı-değer çiftlerine yüklemek için gizli dizi adının geri kalanını döndürür.</span><span class="sxs-lookup"><span data-stu-id="1b85c-494">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="1b85c-495">Bu yaklaşım uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="1b85c-495">When this approach is implemented:</span></span>

1. <span data-ttu-id="1b85c-496">Uygulamanın proje dosyasında belirtilen sürümü.</span><span class="sxs-lookup"><span data-stu-id="1b85c-496">The app's version specified in the app's project file.</span></span> <span data-ttu-id="1b85c-497">Aşağıdaki örnekte, uygulamanın sürümü olarak ayarlanır `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="1b85c-497">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="1b85c-498">`<UserSecretsId>`Uygulamanın proje dosyasında bir özelliğin var olduğunu, burada Kullanıcı tarafından sağlanan BIR GUID olduğunu onaylayın `{GUID}` :</span><span class="sxs-lookup"><span data-stu-id="1b85c-498">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="1b85c-499">[Gizli dizi yöneticisi aracıyla](xref:security/app-secrets)aşağıdaki gizli dizileri yerel olarak kaydedin:</span><span class="sxs-lookup"><span data-stu-id="1b85c-499">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="1b85c-500">Gizlilikler, aşağıdaki Azure CLı komutları kullanılarak Azure Key Vault kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="1b85c-500">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="1b85c-501">Uygulama çalıştırıldığında, Anahtar Kasası gizli dizileri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-501">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="1b85c-502">İçin dize gizli dizisi, uygulamanın `5000-AppSecret` Proje dosyasında () belirtilen uygulamanın sürümüyle eşleştirilir `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-502">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="1b85c-503">Sürüm `5000` (tireyle birlikte), anahtar adından çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-503">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="1b85c-504">Uygulamanın tamamında, anahtarla yapılandırmayı okumak `AppSecret` gizli değeri yükler.</span><span class="sxs-lookup"><span data-stu-id="1b85c-504">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="1b85c-505">Uygulamanın sürümü proje dosyasında olarak değiştirilirse `5.1.0.0` ve uygulama yeniden çalıştırıldığında, döndürülen gizli değer `5.1.0.0_secret_value_dev` geliştirme ortamında ve `5.1.0.0_secret_value_prod` üretimde bulunur.</span><span class="sxs-lookup"><span data-stu-id="1b85c-505">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="1b85c-506">Ayrıca, kendi uygulamanızı da sağlayabilirsiniz <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="1b85c-506">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="1b85c-507">Özel istemci, uygulama genelinde istemcinin tek bir örneğini paylaşıma izin verir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-507">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="1b85c-508">Bir diziyi sınıfa bağlama</span><span class="sxs-lookup"><span data-stu-id="1b85c-508">Bind an array to a class</span></span>

<span data-ttu-id="1b85c-509">Sağlayıcı, bir POCO dizisine bağlamak için yapılandırma değerlerini bir diziye okuyabilme özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1b85c-509">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="1b85c-510">Anahtarların iki nokta () ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken `:` , bir diziyi oluşturan anahtarları ( `:0:` ,,) ayırt etmek için bir sayısal anahtar kesimi kullanılır `:1:` &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-510">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="1b85c-511">Daha fazla bilgi için bkz. [yapılandırma: diziyi bir sınıfa bağlama](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="1b85c-511">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="1b85c-512">Azure Key Vault anahtarlar ayırıcı olarak iki nokta üst üste kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="1b85c-512">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="1b85c-513">Bu konuda açıklanan yaklaşım, `--` hiyerarşik değerler (bölümler) için bir ayırıcı olarak çift tire () kullanır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-513">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="1b85c-514">Dizi anahtarları çift tireler ve sayısal anahtar kesimleri ( `--0--` ,,) ile birlikte Azure Key Vault `--1--` depolanır &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-514">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="1b85c-515">Bir JSON dosyası tarafından sunulan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-515">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="1b85c-516">`WriteTo`Dizide günlüğe kaydetme hedeflerini açıklayan Iki Serilog girişi yansıtan iki nesne değişmez değeri *sinks*vardır:</span><span class="sxs-lookup"><span data-stu-id="1b85c-516">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="1b85c-517">Önceki JSON dosyasında gösterilen yapılandırma, Çift tire ( `--` ) gösterimi ve sayısal kesimleri kullanarak Azure Key Vault depolanır:</span><span class="sxs-lookup"><span data-stu-id="1b85c-517">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="1b85c-518">Anahtar</span><span class="sxs-lookup"><span data-stu-id="1b85c-518">Key</span></span> | <span data-ttu-id="1b85c-519">Değer</span><span class="sxs-lookup"><span data-stu-id="1b85c-519">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="1b85c-520">Gizli dizileri yeniden yükleme</span><span class="sxs-lookup"><span data-stu-id="1b85c-520">Reload secrets</span></span>

<span data-ttu-id="1b85c-521">Gizli diziler çağrılana kadar önbelleğe alınır `IConfigurationRoot.Reload()` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-521">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="1b85c-522">Anahtar kasasındaki zaman aşımına uğradı, devre dışı ve güncelleştirilmiş gizli dizileri, yürütülene kadar uygulama tarafından dikkate alınmıyor `Reload` .</span><span class="sxs-lookup"><span data-stu-id="1b85c-522">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="1b85c-523">Devre dışı ve süre dolma parolaları</span><span class="sxs-lookup"><span data-stu-id="1b85c-523">Disabled and expired secrets</span></span>

<span data-ttu-id="1b85c-524">Devre dışı ve son kullanma parolası bir oluşturur <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="1b85c-524">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="1b85c-525">Uygulamanın üretilmesini engellemek için, farklı bir yapılandırma sağlayıcısı kullanarak yapılandırmayı sağlayın veya devre dışı ya da süre dolma parolasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="1b85c-525">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1b85c-526">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="1b85c-526">Troubleshoot</span></span>

<span data-ttu-id="1b85c-527">Uygulama, sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Core günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır.</span><span class="sxs-lookup"><span data-stu-id="1b85c-527">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="1b85c-528">Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:</span><span class="sxs-lookup"><span data-stu-id="1b85c-528">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="1b85c-529">Uygulama veya sertifika Azure Active Directory içinde doğru yapılandırılmamış.</span><span class="sxs-lookup"><span data-stu-id="1b85c-529">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="1b85c-530">Anahtar Kasası Azure Key Vault içinde yok.</span><span class="sxs-lookup"><span data-stu-id="1b85c-530">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="1b85c-531">Uygulamanın anahtar kasasına erişme yetkisi yok.</span><span class="sxs-lookup"><span data-stu-id="1b85c-531">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="1b85c-532">Erişim ilkesi `Get` ve `List` izinleri içermez.</span><span class="sxs-lookup"><span data-stu-id="1b85c-532">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="1b85c-533">Anahtar kasasında yapılandırma verileri (ad-değer çifti) yanlış olarak adlandırılmış, eksik, devre dışı veya zaman aşımına uğradı.</span><span class="sxs-lookup"><span data-stu-id="1b85c-533">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="1b85c-534">Uygulamanın Anahtar Kasası adı ( `KeyVaultName` ), Azure AD uygulama kimliği ( `AzureADApplicationId` ) veya Azure AD sertifika parmak izi ( `AzureADCertThumbprint` ) yanlış.</span><span class="sxs-lookup"><span data-stu-id="1b85c-534">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="1b85c-535">Yüklemeye çalıştığınız değer için uygulamada yapılandırma anahtarı (ad) yanlış.</span><span class="sxs-lookup"><span data-stu-id="1b85c-535">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="1b85c-536">Uygulama için erişim ilkesini anahtar kasasına eklerken, ilke oluşturulmuştur, ancak **erişim ilkeleri** Kullanıcı arabiriminde **Kaydet** düğmesi seçilmedi.</span><span class="sxs-lookup"><span data-stu-id="1b85c-536">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1b85c-537">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1b85c-537">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="1b85c-538">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="1b85c-538">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="1b85c-539">Microsoft Azure: Key Vault belgeler</span><span class="sxs-lookup"><span data-stu-id="1b85c-539">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="1b85c-540">Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma</span><span class="sxs-lookup"><span data-stu-id="1b85c-540">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="1b85c-541">KeyVaultClient sınıfı</span><span class="sxs-lookup"><span data-stu-id="1b85c-541">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="1b85c-542">Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma</span><span class="sxs-lookup"><span data-stu-id="1b85c-542">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="1b85c-543">Öğretici: .NET 'te Azure Windows sanal makinesi ile Azure Key Vault kullanma</span><span class="sxs-lookup"><span data-stu-id="1b85c-543">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

