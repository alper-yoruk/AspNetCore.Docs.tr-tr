# <a name="key-vault-configuration-provider-sample-app"></a>Key Vault yapılandırma sağlayıcısı örnek uygulaması

Bu örnek Azure Key Vault yapılandırma sağlayıcısının kullanımını gösterir.

Örnek, `#define` *program.cs* dosyasının en üstünde yer aldığı ifadeye göre belirlenen iki moddan birinde çalışır. Yönergeler için bkz. [örnek kodda Önişlemci yönergeleri](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code):

* `Certificate`: Azure Key Vault depolanan gizli dizileri erişmek için Azure Key Vault Istemci KIMLIĞI ve X. 509.952 sertifikası kullanımını gösterir. Örneğin bu sürümü, Azure App Service dağıtılan herhangi bir konumdan veya ASP.NET Core uygulamasına hizmet veren herhangi bir konağa çalıştırılabilir.
* `Managed`: Uygulamanın kodunda veya yapılandırmasında kimlik bilgileri olmadan Azure AD kimlik doğrulamasıyla Azure Key Vault üzere uygulamanın kimliğini doğrulamak için Azure 'un [yönetilen hizmet kimliği](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) nasıl kullanılacağını gösterir. Uygulamanın Azure Key Vault kimliğini doğrulamak için Azure AD Istemci KIMLIĞI ve gizli anahtarı gerekli değildir. Bu örnek, yönetilen kimlik scearnio öğesini araştırmak için Azure App Service dağıtılmalıdır.

Daha fazla bilgi için bkz. [Azure Key Vault yapılandırma sağlayıcısı](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration).
