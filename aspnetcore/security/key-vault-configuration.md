---
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>ASP.NET Core Azure Key Vault yapılandırma sağlayıcısı

, [Andrew Stanton-nurte](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

Bu belgede, Azure Key Vault gizliliklerden uygulama yapılandırma değerlerini yüklemek için [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) yapılandırma sağlayıcısının nasıl kullanılacağı açıklanmaktadır. Azure Key Vault, uygulama ve hizmetler tarafından kullanılan şifreleme anahtarlarını ve gizli dizileri koruma konusunda yardımcı olan bulut tabanlı bir hizmettir. ASP.NET Core uygulamalarla Azure Key Vault kullanmaya yönelik yaygın senaryolar şunlardır:

* Hassas yapılandırma verilerine erişimi denetleme.
* Yapılandırma verilerini depolarken FIPS 140-2 düzey 2 doğrulanan donanım güvenlik modülleri (HSM 'ler) gereksinimini karşılarsınız.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Paketler

[Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) paketine bir paket başvurusu ekleyin.

## <a name="sample-app"></a>Örnek uygulama

Örnek uygulama, `#define` *program.cs* dosyasının en üstünde yer aldığı ifadeye göre belirlenen iki moddan birinde çalışır:

* `Certificate`: Azure Key Vault depolanan gizli dizileri erişmek için Azure Key Vault Istemci KIMLIĞI ve X. 509.952 sertifikası kullanımını gösterir. Örneğin bu sürümü, Azure App Service dağıtılan herhangi bir konumdan veya ASP.NET Core uygulamasına hizmet veren herhangi bir konağa çalıştırılabilir.
* `Managed`: Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasıyla Azure Key Vault üzere uygulamanın kimliğini doğrulamak için [Azure kaynakları Için yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir. Kimlik doğrulaması için Yönetilen kimlikler kullanıldığında, bir Azure AD uygulama KIMLIĞI ve parolası (gizli anahtar) gerekli değildir. `Managed`Örneğin sürümünün Azure 'a dağıtılması gerekir. [Azure kaynakları Için Yönetilen kimlikler bölümünü kullanma](#use-managed-identities-for-azure-resources) bölümündeki yönergeleri izleyin.

Önişlemci yönergeleri () kullanarak örnek bir uygulamanın nasıl yapılandırılacağı hakkında daha fazla bilgi için `#define` bkz <xref:index#preprocessor-directives-in-sample-code> ..

## <a name="secret-storage-in-the-development-environment"></a>Geliştirme ortamında gizli dizi

[Gizli dizi Yöneticisi aracını](xref:security/app-secrets)kullanarak gizli dizileri yerel olarak ayarlayın. Örnek uygulama, geliştirme ortamındaki yerel makinede çalıştığında, gizli anahtar Yöneticisi deposundan gizli diziler yüklenir.

Gizli dizi Yöneticisi Aracı, `<UserSecretsId>` uygulamanın proje dosyasında bir özellik gerektirir. Özellik değerini ( `{GUID}` ) herhangi bir BENZERSIZ GUID 'ye ayarlayın:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Gizlilikler ad-değer çiftleri olarak oluşturulur. Hiyerarşik değerler (yapılandırma bölümleri) `:` [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index) anahtarı adlarında ayırıcı olarak bir (iki nokta üst üste) kullanır.

Gizli dizi, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır; burada `{SECRET NAME}` ad ve `{SECRET VALUE}` değerdir:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Örnek uygulamanın gizli dizilerini ayarlamak için, projenin [içerik kökünden](xref:fundamentals/index#content-root) bir komut kabuğu 'nda aşağıdaki komutları yürütün:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Bu gizlilikler, [Azure Key Vault bölümündeki üretim ortamındaki gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında Azure Key Vault depolandığında, `_dev` sonek olarak değiştirilir `_prod` . Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren bir görsel ipucu sağlar.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault ile üretim ortamında gizli dizi

Hızlı başlangıç tarafından sunulan yönergeler [: Azure CLI kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-cli) , bir Azure Key Vault oluşturmak ve örnek uygulama tarafından kullanılan gizli dizileri depolamak için burada özetlenmiştir. Daha ayrıntılı bilgi için konusuna bakın.

1. [Azure Portal](https://portal.azure.com/)aşağıdaki yöntemlerden birini kullanarak Azure Cloud Shell 'i açın:

   * Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin. Metin kutusunda "Azure CLı" arama dizesini kullanın.
   * **Cloud Shell Başlat** düğmesini kullanarak tarayıcınızda Cloud Shell açın.
   * Azure portal sağ üst köşesindeki menüdeki **Cloud Shell** düğmesini seçin.

   Daha fazla bilgi için bkz. [Azure CLI](/cli/azure/) ve [Azure Cloud Shell Genel Bakış](/azure/cloud-shell/overview).

1. Henüz kimlik doğrulamasından geçirilmemişse komutuyla oturum açın `az login` .

1. Aşağıdaki komutla bir kaynak grubu oluşturun; burada, `{RESOURCE GROUP NAME}` Yeni kaynak grubu için kaynak grubu adı ve `{LOCATION}` Azure bölgesi (Datacenter) olur:

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Aşağıdaki komutla kaynak grubunda bir Anahtar Kasası oluşturun; burada `{KEY VAULT NAME}` Yeni anahtar kasasının adı ve `{LOCATION}` Azure bölgesi (Datacenter) bulunur:

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Anahtar kasasında ad-değer çiftleri olarak gizli diziler oluşturun.

   Azure Key Vault gizli dizi adları, alfasayısal karakterler ve tireler ile sınırlıdır. Hiyerarşik değerler (yapılandırma bölümleri) `--` ayırıcı olarak (iki tire) kullanır. Genellikle [ASP.NET Core yapılandırmasındaki](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlandırmak için kullanılan iki nokta üst üste, Anahtar Kasası gizli adlarında izin verilmez. Bu nedenle, gizlilikler uygulamanın yapılandırmasına yüklendiğinde iki tire kullanılır ve iki nokta üst üste bulunur.

   Aşağıdaki gizlilikler örnek uygulamayla kullanım içindir. Değerler, `_prod` `_dev` Kullanıcı parolalarından geliştirme ortamında yüklenen sonek değerlerinden ayırt etmek için bir sonek içerir. `{KEY VAULT NAME}`Önceki adımda oluşturduğunuz anahtar kasasının adıyla değiştirin:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanın

**Uygulama Azure dışında barındırıldığı zaman**bir anahtar kasasında kimlik doğrulaması yapmak IÇIN Azure AD, Azure Key Vault ve uygulamayı bir Azure ACTIVE DIRECTORY uygulama kimliği ve X. 509.440 sertifikası kullanacak şekilde yapılandırın. Daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanılması desteklenmekle birlikte, Azure 'da bir uygulama barındırılırken [Azure kaynakları Için Yönetilen kimlikler](#use-managed-identities-for-azure-resources) kullanmanızı öneririz. Yönetilen kimlikler, uygulamada veya geliştirme ortamında bir sertifikanın depolanmasını gerektirmez.

Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki ifade olarak AYARLANDıĞıNDA bir uygulama kimliği ve X. 509.440 sertifikası kullanır `Certificate` .

1. PKCS # 12 Arşivi (*. pfx*) sertifikası oluşturun. Sertifika oluşturma seçenekleri Windows ve [OpenSSL](https://www.openssl.org/) [üzerinde MakeCert](/windows/desktop/seccrypto/makecert) içerir.
1. Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükler. Anahtarı verilebilir olarak işaretlemek isteğe bağlıdır. Sertifikanın daha sonra bu işlemde kullanılan parmak izini aklınızda bulunur.
1. PKCS # 12 Arşivi (*. pfx*) sertifikasını der kodlu bir sertifika (*. cer*) olarak dışarı aktarın.
1. Uygulamayı Azure AD 'ye kaydedin (**uygulama kayıtları**).
1. DER kodlu sertifikayı (*. cer*) Azure AD 'ye yükleyin:
   1. Azure AD 'de uygulamayı seçin.
   1. **Sertifikalar & gizli**dizi sayfasına gidin.
   1. Ortak anahtarı içeren sertifikayı karşıya yüklemek için **sertifikayı karşıya yükle** ' yi seçin. *. Cer*, *. pek*veya *. CRT* sertifikası kabul edilebilir.
1. Anahtar Kasası adı, uygulama KIMLIĞI ve sertifika parmak izini uygulamanın *appSettings. JSON* dosyasında depolayın.
1. Azure portal **ana** kasaları ' ne gidin.
1. Azure Key Vault bölümünde, [üretim ortamındaki gizli dizi deposunda](#secret-storage-in-the-production-environment-with-azure-key-vault) oluşturduğunuz anahtar kasasını seçin.
1. **Erişim ilkeleri**'ni seçin.
1. **Erişim Ilkesi Ekle**' yi seçin.
1. **Gizli izinleri** açın ve uygulamaya **Get** ve **list** izinleri sağlayın.
1. **Sorumlu Seç** ' i seçin ve kayıtlı uygulamayı ada göre seçin. **Seç** düğmesini seçin.
1. **Tamam**’ı seçin.
1. **Kaydet**’i seçin.
1. Uygulamayı dağıtın.

`Certificate`Örnek uygulama, yapılandırma değerlerini, `IConfigurationRoot` parola adı ile aynı adla alır:

* Hiyerarşik olmayan değerler: için değeri `SecretName` ile elde edilir `config["SecretName"]` .
* Hiyerarşik değerler (bölümler): `:` (iki nokta üst üste) gösterimini veya `GetSection` genişletme yöntemini kullanın. Yapılandırma değerini elde etmek için şu yaklaşımlardan birini kullanın:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X. 509.440 sertifikası işletim sistemi tarafından yönetiliyor. Uygulama, <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appSettings. JSON* dosyası tarafından sağlanan değerlerle çağırır:

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Örnek değerler:

* Anahtar Kasası adı:`contosovault`
* Uygulama KIMLIĞI:`627e911e-43cc-61d4-992e-12db9c81b413`
* Sertifika parmak izi:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appSettings. JSON*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir. Geliştirme ortamında, gizli anahtar değerleri sonek ile yüklenir `_dev` . Üretim ortamında, değerler sonek ile yüklenir `_prod` .

## <a name="use-managed-identities-for-azure-resources"></a>Azure kaynakları için Yönetilen kimlikler kullanma

Azure **'a dağıtılan bir uygulama** , [Azure kaynakları için yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanarak uygulamanın, KIMLIK bilgileri (uygulama kimliği ve parola/istemci gizli anahtarı) olmadan Azure AD kimlik doğrulaması kullanarak Azure Key Vault kimlik doğrulamasına olanak tanır.

Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki Ifade olarak ayarlandığında Azure kaynakları için Yönetilen kimlikler kullanır `Managed` .

Uygulamanın *appSettings. JSON* dosyasına kasa adını girin. Örnek uygulama sürüme ayarlandığında bir uygulama KIMLIĞI ve parola (Istemci gizli anahtarı) gerektirmez `Managed` , bu nedenle bu yapılandırma girişlerini yoksayabilirsiniz. Uygulama Azure 'a dağıtılır ve Azure, yalnızca *appSettings. JSON* dosyasında depolanan kasa adını kullanarak Azure Key Vault erişmek için uygulamanın kimliğini doğrular.

Azure App Service için örnek uygulamayı dağıtın.

Azure App Service dağıtılan bir uygulama, hizmet oluşturulduğunda Azure AD 'ye otomatik olarak kaydedilir. Aşağıdaki komutta kullanılmak üzere dağıtımdan nesne KIMLIĞINI edinin. Nesne KIMLIĞI, **Identity** App Service panelindeki Azure Portal gösterilir.

Azure CLı ve uygulamanın nesne KIMLIĞINI kullanarak, uygulama `list` ve `get` anahtar kasasına erişim izinleri sağlayın:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Azure CLı, PowerShell veya Azure portal kullanarak **uygulamayı yeniden başlatın** .

Örnek uygulama:

* `AzureServiceTokenProvider`Bir bağlantı dizesi olmadan sınıfın bir örneğini oluşturur. Bir bağlantı dizesi sağlanmazsa, sağlayıcı Azure kaynakları için yönetilen kimliklerden bir erişim belirteci almaya çalışır.
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient> `AzureServiceTokenProvider` Örnek belirteci geri çağırması ile yeni bir oluşturulur.
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>Örnek, <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> tüm gizli değerleri yükleyen ve çift tire ( `--` ) değerini anahtar adlarında iki nokta () ile değiştirir `:` .

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Anahtar Kasası adı örnek değeri:`contosovault`
    
*appSettings. JSON*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir. Geliştirme ortamında, gizli değerler, `_dev` Kullanıcı gizli dizileri tarafından sağlandıklarından, soneke sahiptir. Üretim ortamında, değerler `_prod` Azure Key Vault tarafından sağlandıklarından sonek ile yüklenir.

Bir `Access denied` hata alırsanız, uygulamanın Azure AD 'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladıklarını doğrulayın. Azure 'da hizmeti yeniden başlattığınızdan emin olun.

Sağlayıcıyı yönetilen bir kimlik ve bir Azure DevOps işlem hattı ile kullanma hakkında bilgi için bkz. [yönetilen hizmet KIMLIĞIYLE VM 'ye Azure Resource Manager hizmet bağlantısı oluşturma](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="configuration-options"></a>Yapılandırma seçenekleri

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>şunları kabul edebilir <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Özellik         | Açıklama |
| ---
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> değerlerini almak için kullanmak üzere. | | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> gizli dizi yüklemeyi denetlemek için kullanılan örnek. | | `ReloadInterval` | `Timespan` anahtar kasasındaki değişiklikleri yoklamaya yönelik denemeler arasında bekleme. Varsayılan değer `null` (yapılandırma yeniden yüklenmez). | | `Vault`          | Anahtar Kasası URI 'SI. |

## <a name="use-a-key-name-prefix"></a>Anahtar adı öneki kullanın

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, Anahtar Kasası gizli dizilerini yapılandırma anahtarlarına nasıl dönüştürdüğünü denetlemenize olanak tanıyan, uygulamasını kabul eden bir aşırı yükleme sağlar. Örneğin, uygulama başlangıcında sağladığınız önek değerine göre gizli değerleri yüklemek için arabirimini uygulayabilirsiniz. Bu, örneğin, uygulama sürümüne göre gizli dizileri yüklemeyi sağlar.

> [!WARNING]
> Birden çok uygulama için gizli dizileri aynı kasaya yerleştirmek veya çevresel gizli dizileri (örneğin, *geliştirme* ve *Üretim* gizlilikleri) aynı kasaya yerleştirmek için Anahtar Kasası gizli dizileri üzerinde ön ekleri kullanmayın. Farklı uygulama ve geliştirme/üretim ortamlarının, uygulama ortamlarını en yüksek düzeyde güvenlik için yalıtmak üzere ayrı anahtar kasaları kullanmasını öneririz.

Aşağıdaki örnekte, için anahtar kasasında (ve geliştirme ortamı için gizli Yönetim Aracı kullanılarak) bir gizli dizi oluşturulur `5000-AppSecret` (Anahtar Kasası gizli adlarında dönemlere izin verilmez). Bu gizli anahtar, uygulamanın 5.0.0.0 sürümü için bir uygulama gizli anahtarı temsil eder. Uygulamanın başka bir sürümü olan 5.1.0.0, anahtar kasasına (ve gizli Yönetici Aracı kullanılarak) bir gizli dizi eklenir `5100-AppSecret` . Her bir uygulama sürümü sürümü sürümlü gizli değerini yapılandırma olarak yükler `AppSecret` , bu, gizli anahtarı yüklerken sürümü de kapatıyor.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>özel bir ile çağrılır <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Uygulama, doğru gizli anahtarı yapılandırmaya yüklemek için gizli dizi sürüm öneklerine tepki verir:

* `Load`adı önekiyle başladığında bir gizli dizi yükler. Diğer gizlilikler yüklenmez.
* `GetKey`:
  * Gizli dizi adından öneki kaldırır.
  * `KeyDelimiter`Yapılandırmada kullanılan sınırlayıcı olan (genellikle iki nokta üst üste), herhangi bir ada sahip iki kısa çizgi koyar. Azure Key Vault gizli dizi adlarında bir iki nokta üst üste izin vermez.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

`Load`Yöntemi, sürüm ön ekine sahip olanları bulmak için kasa gizli dizileri aracılığıyla yinelenen bir sağlayıcı algoritması tarafından çağırılır. İle bir sürüm ön eki bulunduğunda `Load` , algoritma, `GetKey` gizli anahtar adının yapılandırma adını döndürmek için yöntemini kullanır. Gizlilik adından sürüm önekini kaldırır ve uygulamanın yapılandırma adı-değer çiftlerine yüklemek için gizli dizi adının geri kalanını döndürür.

Bu yaklaşım uygulandığında:

1. Uygulamanın proje dosyasında belirtilen sürümü. Aşağıdaki örnekte, uygulamanın sürümü olarak ayarlanır `5.0.0.0` :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. `<UserSecretsId>`Uygulamanın proje dosyasında bir özelliğin var olduğunu, burada Kullanıcı tarafından sağlanan BIR GUID olduğunu onaylayın `{GUID}` :

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   [Gizli dizi yöneticisi aracıyla](xref:security/app-secrets)aşağıdaki gizli dizileri yerel olarak kaydedin:

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Gizlilikler, aşağıdaki Azure CLı komutları kullanılarak Azure Key Vault kaydedilir:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Uygulama çalıştırıldığında, Anahtar Kasası gizli dizileri yüklenir. İçin dize gizli dizisi, uygulamanın `5000-AppSecret` Proje dosyasında () belirtilen uygulamanın sürümüyle eşleştirilir `5.0.0.0` .

1. Sürüm `5000` (tireyle birlikte), anahtar adından çıkarılır. Uygulamanın tamamında, anahtarla yapılandırmayı okumak `AppSecret` gizli değeri yükler.

1. Uygulamanın sürümü proje dosyasında olarak değiştirilirse `5.1.0.0` ve uygulama yeniden çalıştırıldığında, döndürülen gizli değer `5.1.0.0_secret_value_dev` geliştirme ortamında ve `5.1.0.0_secret_value_prod` üretimde bulunur.

> [!NOTE]
> Ayrıca, kendi uygulamanızı da sağlayabilirsiniz <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> . Özel istemci, uygulama genelinde istemcinin tek bir örneğini paylaşıma izin verir.

## <a name="bind-an-array-to-a-class"></a>Bir diziyi sınıfa bağlama

Sağlayıcı, bir POCO dizisine bağlamak için yapılandırma değerlerini bir diziye okuyabilme özelliğine sahiptir.

Anahtarların iki nokta () ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken `:` , bir diziyi oluşturan anahtarları ( `:0:` ,,) ayırt etmek için bir sayısal anahtar kesimi kullanılır `:1:` &hellip; `:{n}:` . Daha fazla bilgi için bkz. [yapılandırma: diziyi bir sınıfa bağlama](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Azure Key Vault anahtarlar ayırıcı olarak iki nokta üst üste kullanamaz. Bu konuda açıklanan yaklaşım, `--` hiyerarşik değerler (bölümler) için bir ayırıcı olarak çift tire () kullanır. Dizi anahtarları çift tireler ve sayısal anahtar kesimleri ( `--0--` ,,) ile birlikte Azure Key Vault `--1--` depolanır &hellip; `--{n}--` .

Bir JSON dosyası tarafından sunulan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin. `WriteTo`Dizide günlüğe kaydetme hedeflerini açıklayan Iki Serilog girişi yansıtan iki nesne değişmez değeri *sinks*vardır:

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

Önceki JSON dosyasında gösterilen yapılandırma, Çift tire ( `--` ) gösterimi ve sayısal kesimleri kullanarak Azure Key Vault depolanır:

| Anahtar | Değer |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Gizli dizileri yeniden yükleme

Gizli diziler çağrılana kadar önbelleğe alınır `IConfigurationRoot.Reload()` . Anahtar kasasındaki zaman aşımına uğradı, devre dışı ve güncelleştirilmiş gizli dizileri, yürütülene kadar uygulama tarafından dikkate alınmıyor `Reload` .

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Devre dışı ve süre dolma parolaları

Devre dışı ve son kullanma parolası bir oluşturur <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> . Uygulamanın üretilmesini engellemek için, farklı bir yapılandırma sağlayıcısı kullanarak yapılandırmayı sağlayın veya devre dışı ya da süre dolma parolasını güncelleştirin.

## <a name="troubleshoot"></a>Sorun giderme

Uygulama, sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Core günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır. Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:

* Uygulama veya sertifika Azure Active Directory içinde doğru yapılandırılmamış.
* Anahtar Kasası Azure Key Vault içinde yok.
* Uygulamanın anahtar kasasına erişme yetkisi yok.
* Erişim ilkesi `Get` ve `List` izinleri içermez.
* Anahtar kasasında yapılandırma verileri (ad-değer çifti) yanlış olarak adlandırılmış, eksik, devre dışı veya zaman aşımına uğradı.
* Uygulamanın Anahtar Kasası adı ( `KeyVaultName` ), Azure AD uygulama kimliği ( `AzureADApplicationId` ) veya Azure AD sertifika parmak izi ( `AzureADCertThumbprint` ) yanlış.
* Yüklemeye çalıştığınız değer için uygulamada yapılandırma anahtarı (ad) yanlış.
* Uygulama için erişim ilkesini anahtar kasasına eklerken, ilke oluşturulmuştur, ancak **erişim ilkeleri** Kullanıcı arabiriminde **Kaydet** düğmesi seçilmedi.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault belgeler](/azure/key-vault/)
* [Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient sınıfı](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-net)
* [Öğretici: .NET 'te Azure Windows sanal makinesi ile Azure Key Vault kullanma](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu belgede, Azure Key Vault gizliliklerden uygulama yapılandırma değerlerini yüklemek için [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) yapılandırma sağlayıcısının nasıl kullanılacağı açıklanmaktadır. Azure Key Vault, uygulama ve hizmetler tarafından kullanılan şifreleme anahtarlarını ve gizli dizileri koruma konusunda yardımcı olan bulut tabanlı bir hizmettir. ASP.NET Core uygulamalarla Azure Key Vault kullanmaya yönelik yaygın senaryolar şunlardır:

* Hassas yapılandırma verilerine erişimi denetleme.
* Yapılandırma verilerini depolarken FIPS 140-2 düzey 2 doğrulanan donanım güvenlik modülleri (HSM 'ler) gereksinimini karşılarsınız.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Paketler

[Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) paketine bir paket başvurusu ekleyin.

## <a name="sample-app"></a>Örnek uygulama

Örnek uygulama, `#define` *program.cs* dosyasının en üstünde yer aldığı ifadeye göre belirlenen iki moddan birinde çalışır:

* `Certificate`: Azure Key Vault depolanan gizli dizileri erişmek için Azure Key Vault Istemci KIMLIĞI ve X. 509.952 sertifikası kullanımını gösterir. Örneğin bu sürümü, Azure App Service dağıtılan herhangi bir konumdan veya ASP.NET Core uygulamasına hizmet veren herhangi bir konağa çalıştırılabilir.
* `Managed`: Uygulamanın kodunda veya yapılandırmasında depolanan kimlik bilgileri olmadan Azure AD kimlik doğrulamasıyla Azure Key Vault üzere uygulamanın kimliğini doğrulamak için [Azure kaynakları Için yönetilen kimliklerin](/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir. Kimlik doğrulaması için Yönetilen kimlikler kullanıldığında, bir Azure AD uygulama KIMLIĞI ve parolası (gizli anahtar) gerekli değildir. `Managed`Örneğin sürümünün Azure 'a dağıtılması gerekir. [Azure kaynakları Için Yönetilen kimlikler bölümünü kullanma](#use-managed-identities-for-azure-resources) bölümündeki yönergeleri izleyin.

Önişlemci yönergeleri () kullanarak örnek bir uygulamanın nasıl yapılandırılacağı hakkında daha fazla bilgi için `#define` bkz <xref:index#preprocessor-directives-in-sample-code> ..

## <a name="secret-storage-in-the-development-environment"></a>Geliştirme ortamında gizli dizi

[Gizli dizi Yöneticisi aracını](xref:security/app-secrets)kullanarak gizli dizileri yerel olarak ayarlayın. Örnek uygulama, geliştirme ortamındaki yerel makinede çalıştığında, gizli anahtar Yöneticisi deposundan gizli diziler yüklenir.

Gizli dizi Yöneticisi Aracı, `<UserSecretsId>` uygulamanın proje dosyasında bir özellik gerektirir. Özellik değerini ( `{GUID}` ) herhangi bir BENZERSIZ GUID 'ye ayarlayın:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Gizlilikler ad-değer çiftleri olarak oluşturulur. Hiyerarşik değerler (yapılandırma bölümleri) `:` [ASP.NET Core yapılandırma](xref:fundamentals/configuration/index) anahtarı adlarında ayırıcı olarak bir (iki nokta üst üste) kullanır.

Gizli dizi, projenin [içerik köküne](xref:fundamentals/index#content-root)açılan bir komut kabuğundan kullanılır; burada `{SECRET NAME}` ad ve `{SECRET VALUE}` değerdir:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Örnek uygulamanın gizli dizilerini ayarlamak için, projenin [içerik kökünden](xref:fundamentals/index#content-root) bir komut kabuğu 'nda aşağıdaki komutları yürütün:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Bu gizlilikler, [Azure Key Vault bölümündeki üretim ortamındaki gizli depolama](#secret-storage-in-the-production-environment-with-azure-key-vault) alanında Azure Key Vault depolandığında, `_dev` sonek olarak değiştirilir `_prod` . Sonek, uygulamanın çıktısında yapılandırma değerlerinin kaynağını gösteren bir görsel ipucu sağlar.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault ile üretim ortamında gizli dizi

Hızlı başlangıç tarafından sunulan yönergeler [: Azure CLI kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-cli) , bir Azure Key Vault oluşturmak ve örnek uygulama tarafından kullanılan gizli dizileri depolamak için burada özetlenmiştir. Daha ayrıntılı bilgi için konusuna bakın.

1. [Azure Portal](https://portal.azure.com/)aşağıdaki yöntemlerden birini kullanarak Azure Cloud Shell 'i açın:

   * Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin. Metin kutusunda "Azure CLı" arama dizesini kullanın.
   * **Cloud Shell Başlat** düğmesini kullanarak tarayıcınızda Cloud Shell açın.
   * Azure portal sağ üst köşesindeki menüdeki **Cloud Shell** düğmesini seçin.

   Daha fazla bilgi için bkz. [Azure CLI](/cli/azure/) ve [Azure Cloud Shell Genel Bakış](/azure/cloud-shell/overview).

1. Henüz kimlik doğrulamasından geçirilmemişse komutuyla oturum açın `az login` .

1. Aşağıdaki komutla bir kaynak grubu oluşturun; burada, `{RESOURCE GROUP NAME}` Yeni kaynak grubu için kaynak grubu adı ve `{LOCATION}` Azure bölgesi (Datacenter) olur:

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Aşağıdaki komutla kaynak grubunda bir Anahtar Kasası oluşturun; burada `{KEY VAULT NAME}` Yeni anahtar kasasının adı ve `{LOCATION}` Azure bölgesi (Datacenter) bulunur:

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Anahtar kasasında ad-değer çiftleri olarak gizli diziler oluşturun.

   Azure Key Vault gizli dizi adları, alfasayısal karakterler ve tireler ile sınırlıdır. Hiyerarşik değerler (yapılandırma bölümleri) `--` ayırıcı olarak (iki tire) kullanır. Genellikle [ASP.NET Core yapılandırmasındaki](xref:fundamentals/configuration/index)bir alt anahtardan bir bölümü sınırlandırmak için kullanılan iki nokta üst üste, Anahtar Kasası gizli adlarında izin verilmez. Bu nedenle, gizlilikler uygulamanın yapılandırmasına yüklendiğinde iki tire kullanılır ve iki nokta üst üste bulunur.

   Aşağıdaki gizlilikler örnek uygulamayla kullanım içindir. Değerler, `_prod` `_dev` Kullanıcı parolalarından geliştirme ortamında yüklenen sonek değerlerinden ayırt etmek için bir sonek içerir. `{KEY VAULT NAME}`Önceki adımda oluşturduğunuz anahtar kasasının adıyla değiştirin:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanın

**Uygulama Azure dışında barındırıldığı zaman**bir anahtar kasasında kimlik doğrulaması yapmak IÇIN Azure AD, Azure Key Vault ve uygulamayı bir Azure ACTIVE DIRECTORY uygulama kimliği ve X. 509.440 sertifikası kullanacak şekilde yapılandırın. Daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Azure 'da barındırılan uygulamalar için uygulama KIMLIĞI ve X. 509.440 sertifikası kullanılması desteklenmekle birlikte, Azure 'da bir uygulama barındırılırken [Azure kaynakları Için Yönetilen kimlikler](#use-managed-identities-for-azure-resources) kullanmanızı öneririz. Yönetilen kimlikler, uygulamada veya geliştirme ortamında bir sertifikanın depolanmasını gerektirmez.

Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki ifade olarak AYARLANDıĞıNDA bir uygulama kimliği ve X. 509.440 sertifikası kullanır `Certificate` .

1. PKCS # 12 Arşivi (*. pfx*) sertifikası oluşturun. Sertifika oluşturma seçenekleri Windows ve [OpenSSL](https://www.openssl.org/) [üzerinde MakeCert](/windows/desktop/seccrypto/makecert) içerir.
1. Sertifikayı geçerli kullanıcının kişisel sertifika deposuna yükler. Anahtarı verilebilir olarak işaretlemek isteğe bağlıdır. Sertifikanın daha sonra bu işlemde kullanılan parmak izini aklınızda bulunur.
1. PKCS # 12 Arşivi (*. pfx*) sertifikasını der kodlu bir sertifika (*. cer*) olarak dışarı aktarın.
1. Uygulamayı Azure AD 'ye kaydedin (**uygulama kayıtları**).
1. DER kodlu sertifikayı (*. cer*) Azure AD 'ye yükleyin:
   1. Azure AD 'de uygulamayı seçin.
   1. **Sertifikalar & gizli**dizi sayfasına gidin.
   1. Ortak anahtarı içeren sertifikayı karşıya yüklemek için **sertifikayı karşıya yükle** ' yi seçin. *. Cer*, *. pek*veya *. CRT* sertifikası kabul edilebilir.
1. Anahtar Kasası adı, uygulama KIMLIĞI ve sertifika parmak izini uygulamanın *appSettings. JSON* dosyasında depolayın.
1. Azure portal **ana** kasaları ' ne gidin.
1. Azure Key Vault bölümünde, [üretim ortamındaki gizli dizi deposunda](#secret-storage-in-the-production-environment-with-azure-key-vault) oluşturduğunuz anahtar kasasını seçin.
1. **Erişim ilkeleri**'ni seçin.
1. **Erişim Ilkesi Ekle**' yi seçin.
1. **Gizli izinleri** açın ve uygulamaya **Get** ve **list** izinleri sağlayın.
1. **Sorumlu Seç** ' i seçin ve kayıtlı uygulamayı ada göre seçin. **Seç** düğmesini seçin.
1. **Tamam**’ı seçin.
1. **Kaydet**’i seçin.
1. Uygulamayı dağıtın.

`Certificate`Örnek uygulama, yapılandırma değerlerini, `IConfigurationRoot` parola adı ile aynı adla alır:

* Hiyerarşik olmayan değerler: için değeri `SecretName` ile elde edilir `config["SecretName"]` .
* Hiyerarşik değerler (bölümler): `:` (iki nokta üst üste) gösterimini veya `GetSection` genişletme yöntemini kullanın. Yapılandırma değerini elde etmek için şu yaklaşımlardan birini kullanın:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X. 509.440 sertifikası işletim sistemi tarafından yönetiliyor. Uygulama, <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> *appSettings. JSON* dosyası tarafından sağlanan değerlerle çağırır:

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Örnek değerler:

* Anahtar Kasası adı:`contosovault`
* Uygulama KIMLIĞI:`627e911e-43cc-61d4-992e-12db9c81b413`
* Sertifika parmak izi:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appSettings. JSON*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir. Geliştirme ortamında, gizli anahtar değerleri sonek ile yüklenir `_dev` . Üretim ortamında, değerler sonek ile yüklenir `_prod` .

## <a name="use-managed-identities-for-azure-resources"></a>Azure kaynakları için Yönetilen kimlikler kullanma

Azure **'a dağıtılan bir uygulama** , [Azure kaynakları için yönetilen kimliklerden](/azure/active-directory/managed-identities-azure-resources/overview)yararlanarak uygulamanın, KIMLIK bilgileri (uygulama kimliği ve parola/istemci gizli anahtarı) olmadan Azure AD kimlik doğrulaması kullanarak Azure Key Vault kimlik doğrulamasına olanak tanır.

Örnek uygulama, `#define` *program.cs* dosyasının en üstündeki Ifade olarak ayarlandığında Azure kaynakları için Yönetilen kimlikler kullanır `Managed` .

Uygulamanın *appSettings. JSON* dosyasına kasa adını girin. Örnek uygulama sürüme ayarlandığında bir uygulama KIMLIĞI ve parola (Istemci gizli anahtarı) gerektirmez `Managed` , bu nedenle bu yapılandırma girişlerini yoksayabilirsiniz. Uygulama Azure 'a dağıtılır ve Azure, yalnızca *appSettings. JSON* dosyasında depolanan kasa adını kullanarak Azure Key Vault erişmek için uygulamanın kimliğini doğrular.

Azure App Service için örnek uygulamayı dağıtın.

Azure App Service dağıtılan bir uygulama, hizmet oluşturulduğunda Azure AD 'ye otomatik olarak kaydedilir. Aşağıdaki komutta kullanılmak üzere dağıtımdan nesne KIMLIĞINI edinin. Nesne KIMLIĞI, **Identity** App Service panelindeki Azure Portal gösterilir.

Azure CLı ve uygulamanın nesne KIMLIĞINI kullanarak, uygulama `list` ve `get` anahtar kasasına erişim izinleri sağlayın:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Azure CLı, PowerShell veya Azure portal kullanarak **uygulamayı yeniden başlatın** .

Örnek uygulama:

* `AzureServiceTokenProvider`Bir bağlantı dizesi olmadan sınıfın bir örneğini oluşturur. Bir bağlantı dizesi sağlanmazsa, sağlayıcı Azure kaynakları için yönetilen kimliklerden bir erişim belirteci almaya çalışır.
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient> `AzureServiceTokenProvider` Örnek belirteci geri çağırması ile yeni bir oluşturulur.
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>Örnek, <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> tüm gizli değerleri yükleyen ve çift tire ( `--` ) değerini anahtar adlarında iki nokta () ile değiştirir `:` .

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Anahtar Kasası adı örnek değeri:`contosovault`
    
*appSettings. JSON*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Uygulamayı çalıştırdığınızda, bir Web sayfası yüklenen gizli değerleri gösterir. Geliştirme ortamında, gizli değerler, `_dev` Kullanıcı gizli dizileri tarafından sağlandıklarından, soneke sahiptir. Üretim ortamında, değerler `_prod` Azure Key Vault tarafından sağlandıklarından sonek ile yüklenir.

Bir `Access denied` hata alırsanız, uygulamanın Azure AD 'ye kayıtlı olduğunu ve anahtar kasasına erişim sağladıklarını doğrulayın. Azure 'da hizmeti yeniden başlattığınızdan emin olun.

Sağlayıcıyı yönetilen bir kimlik ve bir Azure DevOps işlem hattı ile kullanma hakkında bilgi için bkz. [yönetilen hizmet KIMLIĞIYLE VM 'ye Azure Resource Manager hizmet bağlantısı oluşturma](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="use-a-key-name-prefix"></a>Anahtar adı öneki kullanın

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, Anahtar Kasası gizli dizilerini yapılandırma anahtarlarına nasıl dönüştürdüğünü denetlemenize olanak tanıyan, uygulamasını kabul eden bir aşırı yükleme sağlar. Örneğin, uygulama başlangıcında sağladığınız önek değerine göre gizli değerleri yüklemek için arabirimini uygulayabilirsiniz. Bu, örneğin, uygulama sürümüne göre gizli dizileri yüklemeyi sağlar.

> [!WARNING]
> Birden çok uygulama için gizli dizileri aynı kasaya yerleştirmek veya çevresel gizli dizileri (örneğin, *geliştirme* ve *Üretim* gizlilikleri) aynı kasaya yerleştirmek için Anahtar Kasası gizli dizileri üzerinde ön ekleri kullanmayın. Farklı uygulama ve geliştirme/üretim ortamlarının, uygulama ortamlarını en yüksek düzeyde güvenlik için yalıtmak üzere ayrı anahtar kasaları kullanmasını öneririz.

Aşağıdaki örnekte, için anahtar kasasında (ve geliştirme ortamı için gizli Yönetim Aracı kullanılarak) bir gizli dizi oluşturulur `5000-AppSecret` (Anahtar Kasası gizli adlarında dönemlere izin verilmez). Bu gizli anahtar, uygulamanın 5.0.0.0 sürümü için bir uygulama gizli anahtarı temsil eder. Uygulamanın başka bir sürümü olan 5.1.0.0, anahtar kasasına (ve gizli Yönetici Aracı kullanılarak) bir gizli dizi eklenir `5100-AppSecret` . Her bir uygulama sürümü sürümü sürümlü gizli değerini yapılandırma olarak yükler `AppSecret` , bu, gizli anahtarı yüklerken sürümü de kapatıyor.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>özel bir ile çağrılır <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Uygulama, doğru gizli anahtarı yapılandırmaya yüklemek için gizli dizi sürüm öneklerine tepki verir:

* `Load`adı önekiyle başladığında bir gizli dizi yükler. Diğer gizlilikler yüklenmez.
* `GetKey`:
  * Gizli dizi adından öneki kaldırır.
  * `KeyDelimiter`Yapılandırmada kullanılan sınırlayıcı olan (genellikle iki nokta üst üste), herhangi bir ada sahip iki kısa çizgi koyar. Azure Key Vault gizli dizi adlarında bir iki nokta üst üste izin vermez.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

`Load`Yöntemi, sürüm ön ekine sahip olanları bulmak için kasa gizli dizileri aracılığıyla yinelenen bir sağlayıcı algoritması tarafından çağırılır. İle bir sürüm ön eki bulunduğunda `Load` , algoritma, `GetKey` gizli anahtar adının yapılandırma adını döndürmek için yöntemini kullanır. Gizlilik adından sürüm önekini kaldırır ve uygulamanın yapılandırma adı-değer çiftlerine yüklemek için gizli dizi adının geri kalanını döndürür.

Bu yaklaşım uygulandığında:

1. Uygulamanın proje dosyasında belirtilen sürümü. Aşağıdaki örnekte, uygulamanın sürümü olarak ayarlanır `5.0.0.0` :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. `<UserSecretsId>`Uygulamanın proje dosyasında bir özelliğin var olduğunu, burada Kullanıcı tarafından sağlanan BIR GUID olduğunu onaylayın `{GUID}` :

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   [Gizli dizi yöneticisi aracıyla](xref:security/app-secrets)aşağıdaki gizli dizileri yerel olarak kaydedin:

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Gizlilikler, aşağıdaki Azure CLı komutları kullanılarak Azure Key Vault kaydedilir:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Uygulama çalıştırıldığında, Anahtar Kasası gizli dizileri yüklenir. İçin dize gizli dizisi, uygulamanın `5000-AppSecret` Proje dosyasında () belirtilen uygulamanın sürümüyle eşleştirilir `5.0.0.0` .

1. Sürüm `5000` (tireyle birlikte), anahtar adından çıkarılır. Uygulamanın tamamında, anahtarla yapılandırmayı okumak `AppSecret` gizli değeri yükler.

1. Uygulamanın sürümü proje dosyasında olarak değiştirilirse `5.1.0.0` ve uygulama yeniden çalıştırıldığında, döndürülen gizli değer `5.1.0.0_secret_value_dev` geliştirme ortamında ve `5.1.0.0_secret_value_prod` üretimde bulunur.

> [!NOTE]
> Ayrıca, kendi uygulamanızı da sağlayabilirsiniz <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> . Özel istemci, uygulama genelinde istemcinin tek bir örneğini paylaşıma izin verir.

## <a name="bind-an-array-to-a-class"></a>Bir diziyi sınıfa bağlama

Sağlayıcı, bir POCO dizisine bağlamak için yapılandırma değerlerini bir diziye okuyabilme özelliğine sahiptir.

Anahtarların iki nokta () ayırıcılar içermesine izin veren bir yapılandırma kaynağından okurken `:` , bir diziyi oluşturan anahtarları ( `:0:` ,,) ayırt etmek için bir sayısal anahtar kesimi kullanılır `:1:` &hellip; `:{n}:` . Daha fazla bilgi için bkz. [yapılandırma: diziyi bir sınıfa bağlama](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Azure Key Vault anahtarlar ayırıcı olarak iki nokta üst üste kullanamaz. Bu konuda açıklanan yaklaşım, `--` hiyerarşik değerler (bölümler) için bir ayırıcı olarak çift tire () kullanır. Dizi anahtarları çift tireler ve sayısal anahtar kesimleri ( `--0--` ,,) ile birlikte Azure Key Vault `--1--` depolanır &hellip; `--{n}--` .

Bir JSON dosyası tarafından sunulan aşağıdaki [Serilog](https://serilog.net/) günlük sağlayıcısı yapılandırmasını inceleyin. `WriteTo`Dizide günlüğe kaydetme hedeflerini açıklayan Iki Serilog girişi yansıtan iki nesne değişmez değeri *sinks*vardır:

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

Önceki JSON dosyasında gösterilen yapılandırma, Çift tire ( `--` ) gösterimi ve sayısal kesimleri kullanarak Azure Key Vault depolanır:

| Anahtar | Değer |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Gizli dizileri yeniden yükleme

Gizli diziler çağrılana kadar önbelleğe alınır `IConfigurationRoot.Reload()` . Anahtar kasasındaki zaman aşımına uğradı, devre dışı ve güncelleştirilmiş gizli dizileri, yürütülene kadar uygulama tarafından dikkate alınmıyor `Reload` .

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Devre dışı ve süre dolma parolaları

Devre dışı ve son kullanma parolası bir oluşturur <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> . Uygulamanın üretilmesini engellemek için, farklı bir yapılandırma sağlayıcısı kullanarak yapılandırmayı sağlayın veya devre dışı ya da süre dolma parolasını güncelleştirin.

## <a name="troubleshoot"></a>Sorun giderme

Uygulama, sağlayıcıyı kullanarak yapılandırmayı yükleyemediğinde, [ASP.NET Core günlük altyapısına](xref:fundamentals/logging/index)bir hata iletisi yazılır. Aşağıdaki koşullar yapılandırmanın yüklenmesine engel olur:

* Uygulama veya sertifika Azure Active Directory içinde doğru yapılandırılmamış.
* Anahtar Kasası Azure Key Vault içinde yok.
* Uygulamanın anahtar kasasına erişme yetkisi yok.
* Erişim ilkesi `Get` ve `List` izinleri içermez.
* Anahtar kasasında yapılandırma verileri (ad-değer çifti) yanlış olarak adlandırılmış, eksik, devre dışı veya zaman aşımına uğradı.
* Uygulamanın Anahtar Kasası adı ( `KeyVaultName` ), Azure AD uygulama kimliği ( `AzureADApplicationId` ) veya Azure AD sertifika parmak izi ( `AzureADCertThumbprint` ) yanlış.
* Yüklemeye çalıştığınız değer için uygulamada yapılandırma anahtarı (ad) yanlış.
* Uygulama için erişim ilkesini anahtar kasasına eklerken, ilke oluşturulmuştur, ancak **erişim ilkeleri** Kullanıcı arabiriminde **Kaydet** düğmesi seçilmedi.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault belgeler](/azure/key-vault/)
* [Azure Key Vault için HSM korumalı anahtarlar oluşturma ve aktarma](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient sınıfı](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Hızlı Başlangıç: .NET web uygulaması kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma](/azure/key-vault/quick-create-net)
* [Öğretici: .NET 'te Azure Windows sanal makinesi ile Azure Key Vault kullanma](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

