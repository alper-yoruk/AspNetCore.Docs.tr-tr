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
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>ASP.NET Core'da Azure Key Vault Yapılandırma Sağlayıcısı

Yazar: [Andrew Stanton-Nurse](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

Bu belge, Uygulama yapılandırma değerlerini Azure Key Vault sırlarından yüklemek için [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Yapılandırma Sağlayıcısı'nın nasıl kullanılacağını açıklar. Azure Key Vault, uygulamalar ve hizmetler tarafından kullanılan şifreleme anahtarlarının ve sırların korunmasına yardımcı olan bulut tabanlı bir hizmettir. Azure Key Vault'u ASP.NET Core uygulamalarıyla kullanmak için sık karşılaşılan senaryolar şunlardır:

* Hassas yapılandırma verilerine erişimi denetleme.
* Yapılandırma verilerini depolarken FIPS 140-2 Düzey 2 doğrulanmış Donanım Güvenlik Modülleri (HSM'ler) gereksinimini karşılar.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Paketler

[Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) paketine bir paket başvurusu ekleyin.

## <a name="sample-app"></a>Örnek uygulama

Örnek uygulama, `#define` *Program.cs* dosyasının üst kısmındaki ifadeyle belirlenen iki moddan birinde çalışır:

* `Certificate`&ndash; Azure Key Vault'ta depolanan sırlara erişmek için Azure Key Vault İstemci Kimliği ve X.509 sertifikası kullanımını gösterir. Örneğin bu sürümü herhangi bir konumdan çalıştırılabilir, Azure Uygulama Hizmeti'ne veya ASP.NET Core uygulamasına hizmet verebilecek herhangi bir ana bilgisayara dağıtılabilir.
* `Managed`&ndash; Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulaması ile azure anahtar atlamasına kimlik doğrulamak [için Azure kaynakları için Yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir. Kimlik doğrulaması için yönetilen kimlikleri kullanırken, Azure AD Uygulama Kimliği ve Parola (İstemci Sırrı) gerekmez. Örneğin `Managed` sürümü Azure'a dağıtılmalıdır. [Azure kaynakları için Yönetilen Kimlikleri Kullan](#use-managed-identities-for-azure-resources) bölümündeki kılavuzu izleyin.

Örnek bir uygulamanın önişlemci yönergelerini kullanarak nasıl`#define`yapılandırılacakları hakkında daha fazla bilgi için ( <xref:index#preprocessor-directives-in-sample-code>bkz.

## <a name="secret-storage-in-the-development-environment"></a>Geliştirme ortamında gizli depolama

[Gizli Yönetici aracını](xref:security/app-secrets)kullanarak sırları yerel olarak ayarlayın. Örnek uygulama Geliştirme ortamındayerel makinede çalıştığında, sırlar yerel Secret Manager mağazasından yüklenir.

Gizli Yönetici aracı, `<UserSecretsId>` uygulamanın proje dosyasında bir özellik gerektirir. Özellik değerini (`{GUID}`) herhangi bir benzersiz GUID'e ayarlayın:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Sırlar ad değeri çiftleri olarak oluşturulur. Hiyerarşik değerler (yapılandırma bölümleri), Core yapılandırma anahtar adlarında `:` [ASP.NET](xref:fundamentals/configuration/index) ayırıcı olarak (üst üste) kullanır.

Gizli Yönetici, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır `{SECRET VALUE}` , adı ve değeri: `{SECRET NAME}`

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Örnek uygulamanın sırlarını ayarlamak için projenin [içerik kökünden](xref:fundamentals/index#content-root) komut kabuğunda aşağıdaki komutları uygulayın:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Bu sırlar Azure Key Vault bölümünden [Üretim ortamındaki Gizli depolama alanında](#secret-storage-in-the-production-environment-with-azure-key-vault) `_dev` Azure Key Vault'ta depolandığında, sonek ' olarak `_prod`değiştirilir. Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren görsel bir ipucu sağlar.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault ile Üretim ortamında gizli depolama

Quickstart tarafından sağlanan yönergeler: Azure CLI konusunu [kullanarak Azure Key Vault'tan bir sır ayarlayın ve alın,](/azure/key-vault/quick-create-cli) azure anahtar kasası oluşturmak ve örnek uygulama tarafından kullanılan sırları depolamak için burada özetlenmiştir. Daha fazla bilgi için konuya bakın.

1. [Azure portalında](https://portal.azure.com/)aşağıdaki yöntemlerden herhangi birini kullanarak Azure Bulutu'nu açın:

   * Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin. Metin kutusundaki "Azure CLI" arama dizesini kullanın.
   * **Başlat Bulut Shell** düğmesiyle tarayıcınızda Cloud Shell'i açın.
   * Azure portalının sağ üst köşesindeki menüdeki **Bulut Kabuğu** düğmesini seçin.

   Daha fazla bilgi için Azure [CLI](/cli/azure/) ve [Azure Bulut BulutU'na Genel Bakış](/azure/cloud-shell/overview)bölümüne bakın.

1. Kimliğinizi doğrulamıyorsanız, `az login` komutla oturum açın.

1. Aşağıdaki komuta sahip bir kaynak `{RESOURCE GROUP NAME}` grubu oluşturun, yeni kaynak grubunun `{LOCATION}` kaynak grubu adı ve Azure bölgesi (veri merkezi):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Kaynak grubunda, yeni anahtar kasasının `{KEY VAULT NAME}` `{LOCATION}` adı ve Azure bölgesinin (veri merkezi) bulunduğu aşağıdaki komutla bir anahtar kasası oluşturun:

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Anahtar kasasında ad değeri çiftleri olarak sırlar oluşturun.

   Azure Key Vault gizli adları alfasayısal karakterler ve tirelerle sınırlıdır. Hiyerarşik değerler (yapılandırma `--` bölümleri) ayırıcı olarak (iki tire) kullanır. Normalde [ASP.NET Core yapılandırmasında](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlamak için kullanılan üst üste, anahtar kasası gizli adlarına izin verilmez. Bu nedenle, iki tire kullanılır ve sırları uygulamanın yapılandırmasına yüklendiğinde bir iki nokta üst üste takas edilir.

   Aşağıdaki sırlar örnek uygulama ile kullanım içindir. Değerler, Geliştirme `_prod` ortamında yüklenen sonek `_dev` değerlerinden Kullanıcı Sırları'ndan ayırt etmek için bir sonek içerir. Önceki `{KEY VAULT NAME}` adımda oluşturduğunuz anahtar tonozunun adı ile değiştirin:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure'da barındırılan olmayan uygulamalar için Uygulama Kimliği ve X.509 sertifikasını kullanma

Azure AD, Azure Anahtar Kasası ve uygulamayı, **uygulama Azure dışında barındırıldığında**önemli bir kasaya kimlik doğrulamak için Azure Active Directory Application ID ve X.509 sertifikasını kullanacak şekilde yapılandırın. Daha fazla bilgi için [anahtarlar, sırlar ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates)bilgi alabiliyorum.

> [!NOTE]
> Azure'da barındırılan uygulamalar için Uygulama Kimliği ve X.509 sertifikası kullanılması desteklenmiş olsa da, Azure'da bir uygulamayı barındırırken [Azure kaynakları için Yönetilen kimlikleri](#use-managed-identities-for-azure-resources) kullanmanızı öneririz. Yönetilen kimlikler, uygulamada veya geliştirme ortamında sertifika depolamayı gerektirmez.

Örnek uygulama, `#define` *Program.cs* dosyasının `Certificate`üst kısmındaki ifade .

1. Bir PKCS#12 arşivi (*.pfx*) sertifikası oluşturun. Sertifika oluşturma seçenekleri arasında [Windows'ta MakeCert](/windows/desktop/seccrypto/makecert) ve [OpenSSL](https://www.openssl.org/)yer almaktadır.
1. Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükleyin. Anahtarı dışa aktarılabilir olarak işaretlemek isteğe bağlıdır. Sertifikanın bu işlemin ilerleyen saatlerinde kullanılan parmak izine dikkat edin.
1. PKCS#12 arşivini (*.pfx*) sertifikasını DER kodlu sertifika (*.cer)* olarak dışa aktarın.
1. Uygulamayı Azure AD **(Uygulama kayıtları)** ile kaydedin.
1. DER kodlu sertifikayı (*.cer*) Azure AD'ye yükleyin:
   1. Azure AD'de uygulamayı seçin.
   1. **Sertifikalar & sırları**gidin.
   1. Ortak anahtarı içeren sertifikayı yüklemek için **Yükleme sertifikasını** seçin. Bir *.cer*, *.pem*, veya *.crt* sertifikası kabul edilebilir.
1. Anahtar kasa adını, Uygulama Kimliğini ve sertifika parmak izini uygulamanın *appsettings.json* dosyasında saklayın.
1. Azure portalındaki **Anahtar kasalarına** gidin.
1. [Azure Key Vault bölümüyle Üretim ortamındaki Gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında oluşturduğunuz anahtar kasasını seçin.
1. **Erişim ilkeleri**'ni seçin.
1. **Erişim İlkesi Ekle'yi**seçin.
1. **Gizli izinleri** açın ve uygulamaya **Al** ve **Liste** izinleri sağlayın.
1. **Ana para seçin** ve kayıtlı uygulamayı ada göre seçin. **Seç** düğmesini seçin.
1. **Tamam'ı**seçin.
1. **Kaydet'i**seçin.
1. Uygulamayı dağıtın.

Örnek `Certificate` uygulama yapılandırma değerlerini gizli `IConfigurationRoot` adla aynı ada sahip olarak alır:

* Hiyerarşik olmayan değerler: Değer `SecretName` . `config["SecretName"]`
* Hiyerarşik değerler (bölümler): (iki nokta `GetSection` üst üste) gösterimini veya uzantı yöntemini kullanın. `:` Yapılandırma değerini elde etmek için aşağıdaki yaklaşımlardan birini kullanın:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 sertifikası işletim sistemi tarafından yönetilir. Uygulama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings.json* dosyası tarafından sağlanan değerleri çağırır:

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Örnek değerler:

* Anahtar kasa adı:`contosovault`
* Başvuru Kimliği:`627e911e-43cc-61d4-992e-12db9c81b413`
* Sertifika parmak izi:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Uygulamayı çalıştırdığınızda, bir web sayfası yüklenen gizli değerleri gösterir. Geliştirme ortamında, gizli değerler `_dev` sonek ile yüklenir. Üretim ortamında, değerler `_prod` sonek ile yüklenir.

## <a name="use-managed-identities-for-azure-resources"></a>Azure kaynakları için Yönetilen kimlikleri kullanma

**Azure'a dağıtılan bir uygulama,** Azure [kaynakları için Yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanabilir ve bu da uygulamanın uygulamada depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasını (Uygulama Kimliği ve Parola/İstemci Gizli) kullanarak Azure Anahtar Kasası ile kimlik doğrulamasını sağlar.

Örnek uygulama, *Program.cs* dosyasının üst `#define` kısmındaki ifade . `Managed`

Uygulamanın *appsettings.json* dosyasına kasa adını girin. Örnek `Managed` uygulama, sürüme ayarlandığında Uygulama Kimliği ve Parola (İstemci Gizli) gerektirmez, bu nedenle bu yapılandırma girişlerini yok sayabilirsiniz. Uygulama Azure'a dağıtılır ve Azure, *uygulama ayarları.json* dosyasında depolanan kasa adını kullanarak Azure Key Vault'a erişmek için uygulamanın kimliğini doğrular.

Örnek uygulamayı Azure Uygulama Hizmeti'ne dağıtın.

Hizmet oluşturulduğunda Azure Uygulama Hizmeti'ne dağıtılan bir uygulama otomatik olarak Azure AD'ye kaydedilir. Aşağıdaki komutta kullanılmak üzere dağıtımdan Nesne Kimliği'ni edinin. Nesne Kimliği, Uygulama Hizmetinin **Kimlik** panelindeki Azure portalında gösterilir.

Azure CLI'yi ve uygulamanın Nesne Kimliğini `list` kullanarak, uygulamaya anahtar kasasına erişmek için izin ler sağlayın: `get`

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Uygulamayı Azure CLI, PowerShell veya Azure portalını kullanarak **yeniden başlatın.**

Örnek uygulama:

* Bağlantı dizesi `AzureServiceTokenProvider` olmayan sınıfın bir örneğini oluşturur. Bağlantı dizesi sağlanmadığı zaman, sağlayıcı Azure kaynakları için Yönetilen kimliklerden bir erişim belirteci elde etmeye çalışır.
* Örnek <xref:Microsoft.Azure.KeyVault.KeyVaultClient> çağrı geri aramaile birlikte yeni bir oluşturulur. `AzureServiceTokenProvider`
* Örnek, <xref:Microsoft.Azure.KeyVault.KeyVaultClient> tüm gizli değerleri <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> yükler ve anahtar adlarında iki noktalı`--`( ) ile`:`çift tireli ( ) değiştirir varsayılan bir uygulama ile kullanılır.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Anahtar kasa sıcadı örnek değeri:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Uygulamayı çalıştırdığınızda, bir web sayfası yüklenen gizli değerleri gösterir. Geliştirme ortamında, kullanıcı sırları `_dev` tarafından sağlandığı için gizli değerler sonek ekine sahiptir. Üretim ortamında, değerler `_prod` Azure Anahtar Kasası tarafından sağlandığı için sonek ile yüklenir.

Bir `Access denied` hata alırsanız, uygulamanın Azure AD'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladığını doğrulayın. Hizmeti Azure'da yeniden başlattığınızı doğrulayın.

Sağlayıcının yönetilen bir kimliğe ve Azure DevOps ardışık kuruluşuna sahip kullanımı hakkında bilgi [için](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)bkz.

## <a name="configuration-options"></a>Yapılandırma seçenekleri

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>bir <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>kabul edebilirsiniz:

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Özellik         | Açıklama |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>değerleri almak için kullanılır. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>örnek gizli yükleme kontrol etmek için kullanılır. |
| `ReloadInterval` | `Timespan`değişiklikler için anahtar kasa yoklama girişimleri arasında beklemek. Varsayılan değer `null` (yapılandırma yeniden yüklenmez). |
| `Vault`          | Anahtar kasa sıyrık URI. |

## <a name="use-a-key-name-prefix"></a>Anahtar adı öneki kullanma

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>anahtar kasa sırlarını yapılandırma tuşlarına <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>nasıl dönüştürüldüğünü kontrol etmenizi sağlayan bir uygulamayı kabul eden aşırı yük sağlar. Örneğin, uygulama başlangıç sırasında sağladığınız bir önek değerine göre gizli değerleri yüklemek için arabirimi uygulayabilirsiniz. Bu, örneğin, uygulamanın sürümüne göre sırları yüklemenize olanak tanır.

> [!WARNING]
> Birden çok uygulamanın sırlarını aynı anahtar kasaya yerleştirmek veya çevresel sırları (örneğin *geliştirme* ve *üretim* sırları) aynı kasaya yerleştirmek için anahtar kasa sırlarındaki önekleri kullanmayın. Farklı uygulamaların ve geliştirme/üretim ortamlarının, uygulama ortamlarını en üst düzeyde güvenlik için yalıtmak için ayrı anahtar kasaları kullanmasını öneririz.

Aşağıdaki örnekte, anahtar kasasında `5000-AppSecret` (ve Geliştirme ortamı için Gizli Yönetici aracı kullanılarak) (anahtar kasa gizli adlarında dönemlere izin verilmez) bir sır oluşturulur. Bu gizli sürüm 5.0.0.0 için bir uygulama gizli temsil eder. Uygulamanın başka bir sürümü için, 5.1.0.0, bir sır için anahtar tonoz `5100-AppSecret`(ve Secret Manager aracı kullanılarak) eklenir. Her uygulama sürümü, sürümdeki gizli değerini `AppSecret`yapılandırmasına yükler, sırrı yüklerken sürümü sıyırır.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>bir özel <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>ile denir:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

Uygulama, <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> uygun sırrı yapılandırmaya yüklemek için sırların sürüm öneklerine tepki verir:

* `Load`adı önek ile başladığında bir sır yükler. Diğer sırlar dolu değil.
* `GetKey`:
  * Öneki gizli addan kaldırır.
  * Yapılandırmada kullanılan sınır dışı layıcı `KeyDelimiter`(genellikle bir üst üste) olan herhangi bir addaki iki tireyi değiştirir. Azure Anahtar Kasası gizli adlarda üst üste izin vermez.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Yöntem, `Load` sürüm önekine sahip olanları bulmak için kasa sırlarını yineleyen bir sağlayıcı algoritması tarafından çağrılır. Bir sürüm öneki ile `Load`bulunduğunda, `GetKey` algoritma gizli adın yapılandırma adını döndürmek için yöntemi kullanır. Gizlinin adından sürüm önekini siler ve uygulamanın yapılandırma adı değeri çiftleri içine yüklemek için gizli adın geri kalanını döndürür.

Bu yaklaşım uygulandığında:

1. Uygulamanın proje dosyasında belirtilen uygulamanın sürümü. Aşağıdaki örnekte, uygulamanın sürümü aşağıdaki `5.0.0.0`şekilde ayarlanır:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Uygulamanın `<UserSecretsId>` proje dosyasında bir özelliğin bulunduğunu `{GUID}` ve kullanıcı tarafından sağlanan GUID'in bulunduğunu doğrulayın:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Aşağıdaki sırları Gizli Yönetici [aracıyla](xref:security/app-secrets)yerel olarak kaydedin:

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Sırlar Aşağıdaki Azure CLI komutları kullanılarak Azure Anahtar Kasası'na kaydedilir:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Uygulama çalıştırıldığında, anahtar kasa sırları yüklenir. String `5000-AppSecret` secret, uygulamanın proje dosyasında belirtilen uygulamanın sürümüyle eşleşir (`5.0.0.0`).

1. Sürüm, `5000` (tire ile), anahtar adından çıkarılır. Uygulama boyunca, anahtarla `AppSecret` yapılandırmayı okuma gizli değeri yükler.

1. Uygulamanın sürümü proje dosyasında `5.1.0.0` değiştirilirse ve uygulama yeniden çalıştırılırsa, döndürülen gizli değer Geliştirme ortamında ve `5.1.0.0_secret_value_dev` `5.1.0.0_secret_value_prod` Üretim'de olur.

> [!NOTE]
> Ayrıca kendi <xref:Microsoft.Azure.KeyVault.KeyVaultClient> uygulama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>sağlayabilir. Özel bir istemci, uygulama genelinde istemcinin tek bir örneğini paylaşmaya izin verir.

## <a name="bind-an-array-to-a-class"></a>Bir diziyi sınıfa bağlama

Sağlayıcı, bir POCO dizisine bağlanmak için yapılandırma değerlerini bir dizihalinde okuyabiliyor.

Tuşların iki nokta üst üste (`:`) ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken,`:0:`bir `:1:` &hellip; `:{n}:`diziyi oluşturan anahtarları ayırt etmek için sayısal bir anahtar kesimi kullanılır . Daha fazla bilgi için yapılandırma: [Bir diziyi sınıfa bağlayın.](xref:fundamentals/configuration/index#bind-an-array-to-a-class)

Azure Anahtar Kasası tuşları ayırıcı olarak üst üste kullanamaz. Bu konuda açıklanan yaklaşım, hiyerarşik`--`değerler (bölümler) için ayırıcı olarak çift tire ( ) kullanır. Dizi anahtarları, çift tireli ve sayısal anahtar segmentleri`--0--`(, , `--1--` &hellip; `--{n}--`, ) ile Azure Key Vault'ta depolanır.

JSON dosyası tarafından sağlanan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin. `WriteTo` Dizide tanımlanan ve günlüğe kaydetme için hedefleri açıklayan iki Serilog *lavabosu*olan iki nesne literals vardır:

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

Önceki JSON dosyasında gösterilen yapılandırma, çift çizgi (`--`) gösterimi ve sayısal segmentler kullanılarak Azure Key Vault'ta depolanır:

| Anahtar | Değer |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Sırları yeniden yükleme

Sırlar çağrılana `IConfigurationRoot.Reload()` kadar önbelleğe alınır. Anahtar kasasında süresi dolmuş, devre dışı bırakılmış ve `Reload` güncelleştirilmiş sırlar yürütülene kadar uygulama tarafından saygı duyulmuyor.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Devre dışı bırakılmış ve süresi dolmuş sırlar

Devre dışı bırakılmış <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>ve süresi dolmuş sırlar bir atmak . Uygulamanın atmasını önlemek için, yapılandırmayı farklı bir yapılandırma sağlayıcısı kullanarak sağlayın veya devre dışı bırakılan veya süresi dolmuş sırrı güncelleştirin.

## <a name="troubleshoot"></a>Sorun giderme

Uygulama sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Çekirdek Günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır. Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:

* Uygulama veya sertifika Azure Etkin Dizini'nde doğru şekilde yapılandırılmamıştır.
* Anahtar kasası Azure Key Vault'ta yok.
* Uygulamanın anahtar kasasına erişme yetkisi yok.
* Erişim ilkesi, izinleri `Get` `List` içermez ve içermez.
* Anahtar kasasında, yapılandırma verileri (ad değeri çifti) yanlış adlandırılır, eksik, devre dışı bırakılır veya süresi dolmuş olur.
* Uygulama, yanlış anahtar kasa`KeyVaultName`adı ( ),`AzureADApplicationId`Azure AD Application Id`AzureADCertThumbprint`( ), veya Azure AD sertifikası parmak izi ( ) vardır.
* Yapılandırma anahtarı (ad) yüklemeye çalıştığınız değer için uygulamada yanlıştır.
* Uygulamanın erişim ilkesini anahtar kasasına eklerken, ilke oluşturuldu, ancak **Access ilkeleri** UI'sinde **Kaydet** düğmesi seçilmedi.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Anahtar Kasası](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Önemli Kasa Belgeleri](/azure/key-vault/)
* [Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultMüşteri Sınıfı](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-net)
* [Öğretici: .NET'te Azure Windows Virtual Machine ile Azure Key Vault nasıl kullanılır?](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu belge, Uygulama yapılandırma değerlerini Azure Key Vault sırlarından yüklemek için [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Yapılandırma Sağlayıcısı'nın nasıl kullanılacağını açıklar. Azure Key Vault, uygulamalar ve hizmetler tarafından kullanılan şifreleme anahtarlarının ve sırların korunmasına yardımcı olan bulut tabanlı bir hizmettir. Azure Key Vault'u ASP.NET Core uygulamalarıyla kullanmak için sık karşılaşılan senaryolar şunlardır:

* Hassas yapılandırma verilerine erişimi denetleme.
* Yapılandırma verilerini depolarken FIPS 140-2 Düzey 2 doğrulanmış Donanım Güvenlik Modülleri (HSM'ler) gereksinimini karşılar.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Paketler

[Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) paketine bir paket başvurusu ekleyin.

## <a name="sample-app"></a>Örnek uygulama

Örnek uygulama, `#define` *Program.cs* dosyasının üst kısmındaki ifadeyle belirlenen iki moddan birinde çalışır:

* `Certificate`&ndash; Azure Key Vault'ta depolanan sırlara erişmek için Azure Key Vault İstemci Kimliği ve X.509 sertifikası kullanımını gösterir. Örneğin bu sürümü herhangi bir konumdan çalıştırılabilir, Azure Uygulama Hizmeti'ne veya ASP.NET Core uygulamasına hizmet verebilecek herhangi bir ana bilgisayara dağıtılabilir.
* `Managed`&ndash; Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulaması ile azure anahtar atlamasına kimlik doğrulamak [için Azure kaynakları için Yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir. Kimlik doğrulaması için yönetilen kimlikleri kullanırken, Azure AD Uygulama Kimliği ve Parola (İstemci Sırrı) gerekmez. Örneğin `Managed` sürümü Azure'a dağıtılmalıdır. [Azure kaynakları için Yönetilen Kimlikleri Kullan](#use-managed-identities-for-azure-resources) bölümündeki kılavuzu izleyin.

Örnek bir uygulamanın önişlemci yönergelerini kullanarak nasıl`#define`yapılandırılacakları hakkında daha fazla bilgi için ( <xref:index#preprocessor-directives-in-sample-code>bkz.

## <a name="secret-storage-in-the-development-environment"></a>Geliştirme ortamında gizli depolama

[Gizli Yönetici aracını](xref:security/app-secrets)kullanarak sırları yerel olarak ayarlayın. Örnek uygulama Geliştirme ortamındayerel makinede çalıştığında, sırlar yerel Secret Manager mağazasından yüklenir.

Gizli Yönetici aracı, `<UserSecretsId>` uygulamanın proje dosyasında bir özellik gerektirir. Özellik değerini (`{GUID}`) herhangi bir benzersiz GUID'e ayarlayın:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Sırlar ad değeri çiftleri olarak oluşturulur. Hiyerarşik değerler (yapılandırma bölümleri), Core yapılandırma anahtar adlarında `:` [ASP.NET](xref:fundamentals/configuration/index) ayırıcı olarak (üst üste) kullanır.

Gizli Yönetici, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır `{SECRET VALUE}` , adı ve değeri: `{SECRET NAME}`

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Örnek uygulamanın sırlarını ayarlamak için projenin [içerik kökünden](xref:fundamentals/index#content-root) komut kabuğunda aşağıdaki komutları uygulayın:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Bu sırlar Azure Key Vault bölümünden [Üretim ortamındaki Gizli depolama alanında](#secret-storage-in-the-production-environment-with-azure-key-vault) `_dev` Azure Key Vault'ta depolandığında, sonek ' olarak `_prod`değiştirilir. Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren görsel bir ipucu sağlar.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault ile Üretim ortamında gizli depolama

Quickstart tarafından sağlanan yönergeler: Azure CLI konusunu [kullanarak Azure Key Vault'tan bir sır ayarlayın ve alın,](/azure/key-vault/quick-create-cli) azure anahtar kasası oluşturmak ve örnek uygulama tarafından kullanılan sırları depolamak için burada özetlenmiştir. Daha fazla bilgi için konuya bakın.

1. [Azure portalında](https://portal.azure.com/)aşağıdaki yöntemlerden herhangi birini kullanarak Azure Bulutu'nu açın:

   * Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin. Metin kutusundaki "Azure CLI" arama dizesini kullanın.
   * **Başlat Bulut Shell** düğmesiyle tarayıcınızda Cloud Shell'i açın.
   * Azure portalının sağ üst köşesindeki menüdeki **Bulut Kabuğu** düğmesini seçin.

   Daha fazla bilgi için Azure [CLI](/cli/azure/) ve [Azure Bulut BulutU'na Genel Bakış](/azure/cloud-shell/overview)bölümüne bakın.

1. Kimliğinizi doğrulamıyorsanız, `az login` komutla oturum açın.

1. Aşağıdaki komuta sahip bir kaynak `{RESOURCE GROUP NAME}` grubu oluşturun, yeni kaynak grubunun `{LOCATION}` kaynak grubu adı ve Azure bölgesi (veri merkezi):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Kaynak grubunda, yeni anahtar kasasının `{KEY VAULT NAME}` `{LOCATION}` adı ve Azure bölgesinin (veri merkezi) bulunduğu aşağıdaki komutla bir anahtar kasası oluşturun:

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Anahtar kasasında ad değeri çiftleri olarak sırlar oluşturun.

   Azure Key Vault gizli adları alfasayısal karakterler ve tirelerle sınırlıdır. Hiyerarşik değerler (yapılandırma `--` bölümleri) ayırıcı olarak (iki tire) kullanır. Normalde [ASP.NET Core yapılandırmasında](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlamak için kullanılan üst üste, anahtar kasası gizli adlarına izin verilmez. Bu nedenle, iki tire kullanılır ve sırları uygulamanın yapılandırmasına yüklendiğinde bir iki nokta üst üste takas edilir.

   Aşağıdaki sırlar örnek uygulama ile kullanım içindir. Değerler, Geliştirme `_prod` ortamında yüklenen sonek `_dev` değerlerinden Kullanıcı Sırları'ndan ayırt etmek için bir sonek içerir. Önceki `{KEY VAULT NAME}` adımda oluşturduğunuz anahtar tonozunun adı ile değiştirin:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure'da barındırılan olmayan uygulamalar için Uygulama Kimliği ve X.509 sertifikasını kullanma

Azure AD, Azure Anahtar Kasası ve uygulamayı, **uygulama Azure dışında barındırıldığında**önemli bir kasaya kimlik doğrulamak için Azure Active Directory Application ID ve X.509 sertifikasını kullanacak şekilde yapılandırın. Daha fazla bilgi için [anahtarlar, sırlar ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates)bilgi alabiliyorum.

> [!NOTE]
> Azure'da barındırılan uygulamalar için Uygulama Kimliği ve X.509 sertifikası kullanılması desteklenmiş olsa da, Azure'da bir uygulamayı barındırırken [Azure kaynakları için Yönetilen kimlikleri](#use-managed-identities-for-azure-resources) kullanmanızı öneririz. Yönetilen kimlikler, uygulamada veya geliştirme ortamında sertifika depolamayı gerektirmez.

Örnek uygulama, `#define` *Program.cs* dosyasının `Certificate`üst kısmındaki ifade .

1. Bir PKCS#12 arşivi (*.pfx*) sertifikası oluşturun. Sertifika oluşturma seçenekleri arasında [Windows'ta MakeCert](/windows/desktop/seccrypto/makecert) ve [OpenSSL](https://www.openssl.org/)yer almaktadır.
1. Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükleyin. Anahtarı dışa aktarılabilir olarak işaretlemek isteğe bağlıdır. Sertifikanın bu işlemin ilerleyen saatlerinde kullanılan parmak izine dikkat edin.
1. PKCS#12 arşivini (*.pfx*) sertifikasını DER kodlu sertifika (*.cer)* olarak dışa aktarın.
1. Uygulamayı Azure AD **(Uygulama kayıtları)** ile kaydedin.
1. DER kodlu sertifikayı (*.cer*) Azure AD'ye yükleyin:
   1. Azure AD'de uygulamayı seçin.
   1. **Sertifikalar & sırları**gidin.
   1. Ortak anahtarı içeren sertifikayı yüklemek için **Yükleme sertifikasını** seçin. Bir *.cer*, *.pem*, veya *.crt* sertifikası kabul edilebilir.
1. Anahtar kasa adını, Uygulama Kimliğini ve sertifika parmak izini uygulamanın *appsettings.json* dosyasında saklayın.
1. Azure portalındaki **Anahtar kasalarına** gidin.
1. [Azure Key Vault bölümüyle Üretim ortamındaki Gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında oluşturduğunuz anahtar kasasını seçin.
1. **Erişim ilkeleri**'ni seçin.
1. **Erişim İlkesi Ekle'yi**seçin.
1. **Gizli izinleri** açın ve uygulamaya **Al** ve **Liste** izinleri sağlayın.
1. **Ana para seçin** ve kayıtlı uygulamayı ada göre seçin. **Seç** düğmesini seçin.
1. **Tamam'ı**seçin.
1. **Kaydet'i**seçin.
1. Uygulamayı dağıtın.

Örnek `Certificate` uygulama yapılandırma değerlerini gizli `IConfigurationRoot` adla aynı ada sahip olarak alır:

* Hiyerarşik olmayan değerler: Değer `SecretName` . `config["SecretName"]`
* Hiyerarşik değerler (bölümler): (iki nokta `GetSection` üst üste) gösterimini veya uzantı yöntemini kullanın. `:` Yapılandırma değerini elde etmek için aşağıdaki yaklaşımlardan birini kullanın:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 sertifikası işletim sistemi tarafından yönetilir. Uygulama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appsettings.json* dosyası tarafından sağlanan değerleri çağırır:

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Örnek değerler:

* Anahtar kasa adı:`contosovault`
* Başvuru Kimliği:`627e911e-43cc-61d4-992e-12db9c81b413`
* Sertifika parmak izi:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Uygulamayı çalıştırdığınızda, bir web sayfası yüklenen gizli değerleri gösterir. Geliştirme ortamında, gizli değerler `_dev` sonek ile yüklenir. Üretim ortamında, değerler `_prod` sonek ile yüklenir.

## <a name="use-managed-identities-for-azure-resources"></a>Azure kaynakları için Yönetilen kimlikleri kullanma

**Azure'a dağıtılan bir uygulama,** Azure [kaynakları için Yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanabilir ve bu da uygulamanın uygulamada depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasını (Uygulama Kimliği ve Parola/İstemci Gizli) kullanarak Azure Anahtar Kasası ile kimlik doğrulamasını sağlar.

Örnek uygulama, *Program.cs* dosyasının üst `#define` kısmındaki ifade . `Managed`

Uygulamanın *appsettings.json* dosyasına kasa adını girin. Örnek `Managed` uygulama, sürüme ayarlandığında Uygulama Kimliği ve Parola (İstemci Gizli) gerektirmez, bu nedenle bu yapılandırma girişlerini yok sayabilirsiniz. Uygulama Azure'a dağıtılır ve Azure, *uygulama ayarları.json* dosyasında depolanan kasa adını kullanarak Azure Key Vault'a erişmek için uygulamanın kimliğini doğrular.

Örnek uygulamayı Azure Uygulama Hizmeti'ne dağıtın.

Hizmet oluşturulduğunda Azure Uygulama Hizmeti'ne dağıtılan bir uygulama otomatik olarak Azure AD'ye kaydedilir. Aşağıdaki komutta kullanılmak üzere dağıtımdan Nesne Kimliği'ni edinin. Nesne Kimliği, Uygulama Hizmetinin **Kimlik** panelindeki Azure portalında gösterilir.

Azure CLI'yi ve uygulamanın Nesne Kimliğini `list` kullanarak, uygulamaya anahtar kasasına erişmek için izin ler sağlayın: `get`

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Uygulamayı Azure CLI, PowerShell veya Azure portalını kullanarak **yeniden başlatın.**

Örnek uygulama:

* Bağlantı dizesi `AzureServiceTokenProvider` olmayan sınıfın bir örneğini oluşturur. Bağlantı dizesi sağlanmadığı zaman, sağlayıcı Azure kaynakları için Yönetilen kimliklerden bir erişim belirteci elde etmeye çalışır.
* Örnek <xref:Microsoft.Azure.KeyVault.KeyVaultClient> çağrı geri aramaile birlikte yeni bir oluşturulur. `AzureServiceTokenProvider`
* Örnek, <xref:Microsoft.Azure.KeyVault.KeyVaultClient> tüm gizli değerleri <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> yükler ve anahtar adlarında iki noktalı`--`( ) ile`:`çift tireli ( ) değiştirir varsayılan bir uygulama ile kullanılır.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Anahtar kasa sıcadı örnek değeri:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Uygulamayı çalıştırdığınızda, bir web sayfası yüklenen gizli değerleri gösterir. Geliştirme ortamında, kullanıcı sırları `_dev` tarafından sağlandığı için gizli değerler sonek ekine sahiptir. Üretim ortamında, değerler `_prod` Azure Anahtar Kasası tarafından sağlandığı için sonek ile yüklenir.

Bir `Access denied` hata alırsanız, uygulamanın Azure AD'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladığını doğrulayın. Hizmeti Azure'da yeniden başlattığınızı doğrulayın.

Sağlayıcının yönetilen bir kimliğe ve Azure DevOps ardışık kuruluşuna sahip kullanımı hakkında bilgi [için](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)bkz.

## <a name="use-a-key-name-prefix"></a>Anahtar adı öneki kullanma

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>anahtar kasa sırlarını yapılandırma tuşlarına <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>nasıl dönüştürüldüğünü kontrol etmenizi sağlayan bir uygulamayı kabul eden aşırı yük sağlar. Örneğin, uygulama başlangıç sırasında sağladığınız bir önek değerine göre gizli değerleri yüklemek için arabirimi uygulayabilirsiniz. Bu, örneğin, uygulamanın sürümüne göre sırları yüklemenize olanak tanır.

> [!WARNING]
> Birden çok uygulamanın sırlarını aynı anahtar kasaya yerleştirmek veya çevresel sırları (örneğin *geliştirme* ve *üretim* sırları) aynı kasaya yerleştirmek için anahtar kasa sırlarındaki önekleri kullanmayın. Farklı uygulamaların ve geliştirme/üretim ortamlarının, uygulama ortamlarını en üst düzeyde güvenlik için yalıtmak için ayrı anahtar kasaları kullanmasını öneririz.

Aşağıdaki örnekte, anahtar kasasında `5000-AppSecret` (ve Geliştirme ortamı için Gizli Yönetici aracı kullanılarak) (anahtar kasa gizli adlarında dönemlere izin verilmez) bir sır oluşturulur. Bu gizli sürüm 5.0.0.0 için bir uygulama gizli temsil eder. Uygulamanın başka bir sürümü için, 5.1.0.0, bir sır için anahtar tonoz `5100-AppSecret`(ve Secret Manager aracı kullanılarak) eklenir. Her uygulama sürümü, sürümdeki gizli değerini `AppSecret`yapılandırmasına yükler, sırrı yüklerken sürümü sıyırır.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>bir özel <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>ile denir:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

Uygulama, <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> uygun sırrı yapılandırmaya yüklemek için sırların sürüm öneklerine tepki verir:

* `Load`adı önek ile başladığında bir sır yükler. Diğer sırlar dolu değil.
* `GetKey`:
  * Öneki gizli addan kaldırır.
  * Yapılandırmada kullanılan sınır dışı layıcı `KeyDelimiter`(genellikle bir üst üste) olan herhangi bir addaki iki tireyi değiştirir. Azure Anahtar Kasası gizli adlarda üst üste izin vermez.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Yöntem, `Load` sürüm önekine sahip olanları bulmak için kasa sırlarını yineleyen bir sağlayıcı algoritması tarafından çağrılır. Bir sürüm öneki ile `Load`bulunduğunda, `GetKey` algoritma gizli adın yapılandırma adını döndürmek için yöntemi kullanır. Gizlinin adından sürüm önekini siler ve uygulamanın yapılandırma adı değeri çiftleri içine yüklemek için gizli adın geri kalanını döndürür.

Bu yaklaşım uygulandığında:

1. Uygulamanın proje dosyasında belirtilen uygulamanın sürümü. Aşağıdaki örnekte, uygulamanın sürümü aşağıdaki `5.0.0.0`şekilde ayarlanır:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Uygulamanın `<UserSecretsId>` proje dosyasında bir özelliğin bulunduğunu `{GUID}` ve kullanıcı tarafından sağlanan GUID'in bulunduğunu doğrulayın:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Aşağıdaki sırları Gizli Yönetici [aracıyla](xref:security/app-secrets)yerel olarak kaydedin:

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Sırlar Aşağıdaki Azure CLI komutları kullanılarak Azure Anahtar Kasası'na kaydedilir:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Uygulama çalıştırıldığında, anahtar kasa sırları yüklenir. String `5000-AppSecret` secret, uygulamanın proje dosyasında belirtilen uygulamanın sürümüyle eşleşir (`5.0.0.0`).

1. Sürüm, `5000` (tire ile), anahtar adından çıkarılır. Uygulama boyunca, anahtarla `AppSecret` yapılandırmayı okuma gizli değeri yükler.

1. Uygulamanın sürümü proje dosyasında `5.1.0.0` değiştirilirse ve uygulama yeniden çalıştırılırsa, döndürülen gizli değer Geliştirme ortamında ve `5.1.0.0_secret_value_dev` `5.1.0.0_secret_value_prod` Üretim'de olur.

> [!NOTE]
> Ayrıca kendi <xref:Microsoft.Azure.KeyVault.KeyVaultClient> uygulama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>sağlayabilir. Özel bir istemci, uygulama genelinde istemcinin tek bir örneğini paylaşmaya izin verir.

## <a name="bind-an-array-to-a-class"></a>Bir diziyi sınıfa bağlama

Sağlayıcı, bir POCO dizisine bağlanmak için yapılandırma değerlerini bir dizihalinde okuyabiliyor.

Tuşların iki nokta üst üste (`:`) ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken,`:0:`bir `:1:` &hellip; `:{n}:`diziyi oluşturan anahtarları ayırt etmek için sayısal bir anahtar kesimi kullanılır . Daha fazla bilgi için yapılandırma: [Bir diziyi sınıfa bağlayın.](xref:fundamentals/configuration/index#bind-an-array-to-a-class)

Azure Anahtar Kasası tuşları ayırıcı olarak üst üste kullanamaz. Bu konuda açıklanan yaklaşım, hiyerarşik`--`değerler (bölümler) için ayırıcı olarak çift tire ( ) kullanır. Dizi anahtarları, çift tireli ve sayısal anahtar segmentleri`--0--`(, , `--1--` &hellip; `--{n}--`, ) ile Azure Key Vault'ta depolanır.

JSON dosyası tarafından sağlanan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin. `WriteTo` Dizide tanımlanan ve günlüğe kaydetme için hedefleri açıklayan iki Serilog *lavabosu*olan iki nesne literals vardır:

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

Önceki JSON dosyasında gösterilen yapılandırma, çift çizgi (`--`) gösterimi ve sayısal segmentler kullanılarak Azure Key Vault'ta depolanır:

| Anahtar | Değer |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Sırları yeniden yükleme

Sırlar çağrılana `IConfigurationRoot.Reload()` kadar önbelleğe alınır. Anahtar kasasında süresi dolmuş, devre dışı bırakılmış ve `Reload` güncelleştirilmiş sırlar yürütülene kadar uygulama tarafından saygı duyulmuyor.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Devre dışı bırakılmış ve süresi dolmuş sırlar

Devre dışı bırakılmış <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>ve süresi dolmuş sırlar bir atmak . Uygulamanın atmasını önlemek için, yapılandırmayı farklı bir yapılandırma sağlayıcısı kullanarak sağlayın veya devre dışı bırakılan veya süresi dolmuş sırrı güncelleştirin.

## <a name="troubleshoot"></a>Sorun giderme

Uygulama sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Çekirdek Günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır. Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:

* Uygulama veya sertifika Azure Etkin Dizini'nde doğru şekilde yapılandırılmamıştır.
* Anahtar kasası Azure Key Vault'ta yok.
* Uygulamanın anahtar kasasına erişme yetkisi yok.
* Erişim ilkesi, izinleri `Get` `List` içermez ve içermez.
* Anahtar kasasında, yapılandırma verileri (ad değeri çifti) yanlış adlandırılır, eksik, devre dışı bırakılır veya süresi dolmuş olur.
* Uygulama, yanlış anahtar kasa`KeyVaultName`adı ( ),`AzureADApplicationId`Azure AD Application Id`AzureADCertThumbprint`( ), veya Azure AD sertifikası parmak izi ( ) vardır.
* Yapılandırma anahtarı (ad) yüklemeye çalıştığınız değer için uygulamada yanlıştır.
* Uygulamanın erişim ilkesini anahtar kasasına eklerken, ilke oluşturuldu, ancak **Access ilkeleri** UI'sinde **Kaydet** düğmesi seçilmedi.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Anahtar Kasası](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Önemli Kasa Belgeleri](/azure/key-vault/)
* [Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultMüşteri Sınıfı](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-net)
* [Öğretici: .NET'te Azure Windows Virtual Machine ile Azure Key Vault nasıl kullanılır?](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

