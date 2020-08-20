---
title: ASP.NET Core ile IIS modülleri
author: rick-anderson
description: ASP.NET Core uygulamalar için etkin ve etkin olmayan IIS modüllerini ve IIS modüllerinin nasıl yönetileceğini bulun.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 7ddc1b0f80625fbc39ac49f305f745b005cbce46
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634676"
---
# <a name="iis-modules-with-aspnet-core"></a>ASP.NET Core ile IIS modülleri

Yerel IIS modüllerinden bazıları ve tüm IIS yönetilen modülleri, ASP.NET Core Apps isteklerini işleyemez. Birçok durumda ASP.NET Core, IIS yerel ve yönetilen modüller tarafından ele alınan senaryolara bir alternatif sağlar.

## <a name="native-modules"></a>Yerel modüller

Tablo, ASP.NET Core uygulamalar ve ASP.NET Core modülü ile işlevsel yerel IIS modüllerini gösterir.

| Modül | ASP.NET Core uygulamalarla işlevsel | ASP.NET Core seçeneği |
| --- | :---: | --- |
| **Anonim Kimlik Doğrulaması**<br>`AnonymousAuthenticationModule`                                  | Yes | |
| **Temel kimlik doğrulaması**<br>`BasicAuthenticationModule`                                          | Yes | |
| **İstemci sertifikası eşleme kimlik doğrulaması**<br>`CertificateMappingAuthenticationModule`      | Yes | |
| **CGI**<br>`CgiModule`                                                                           | No  | |
| **Yapılandırma doğrulaması**<br>`ConfigurationValidationModule`                                  | Yes | |
| **HTTP Hataları**<br>`CustomErrorModule`                                                           | No  | [Durum kodu sayfaları ara yazılımı](xref:fundamentals/error-handling#usestatuscodepages) |
| **Özel günlüğe kaydetme**<br>`CustomLoggingModule`                                                      | Yes | |
| **Varsayılan Belge**<br>`DefaultDocumentModule`                                                  | No  | [Varsayılan dosyalar ara yazılımı](xref:fundamentals/static-files#serve-a-default-document) |
| **Özet kimlik doğrulaması**<br>`DigestAuthenticationModule`                                        | Yes | |
| **Dizin Tarama**<br>`DirectoryListingModule`                                               | No  | [Dizin tarama ara yazılımı](xref:fundamentals/static-files#enable-directory-browsing) |
| **Dinamik sıkıştırma**<br>`DynamicCompressionModule`                                            | Yes | [Yanıt sıkıştırma ara yazılımı](xref:performance/response-compression) |
| **Başarısız Istek Izleme**<br>`FailedRequestsTracingModule`                                     | Yes | [Günlüğe kaydetme ASP.NET Core](xref:fundamentals/logging/index#tracesource-provider) |
| **Dosya önbelleğe alma**<br>`FileCacheModule`                                                            | No  | [Yanıt önbelleğe alma ara yazılımı](xref:performance/caching/middleware) |
| **HTTP önbelleği**<br>`HttpCacheModule`                                                            | No  | [Yanıt önbelleğe alma ara yazılımı](xref:performance/caching/middleware) |
| **HTTP Günlüğe Kaydetme**<br>`HttpLoggingModule`                                                          | Yes | [Günlüğe kaydetme ASP.NET Core](xref:fundamentals/logging/index) |
| **HTTP Yeniden Yönlendirme**<br>`HttpRedirectionModule`                                                  | Yes | [URL yeniden yazma ara yazılımı](xref:fundamentals/url-rewriting) |
| **HTTP Izleme**<br>`TracingModule`                                                              | Yes | |
| **IIS Istemci sertifikası eşleme kimlik doğrulaması**<br>`IISCertificateMappingAuthenticationModule` | Yes | |
| **IP ve Etki Alanı Kısıtlamaları**<br>`IpRestrictionModule`                                          | Yes | |
| **ISAPI filtreleri**<br>`IsapiFilterModule`                                                         | Yes | [Ara yazılım](xref:fundamentals/middleware/index) |
| **I**<br>`IsapiModule`                                                                       | Yes | [Ara yazılım](xref:fundamentals/middleware/index) |
| **Protokol desteği**<br>`ProtocolSupportModule`                                                  | Yes | |
| **İstek Filtreleme**<br>`RequestFilteringModule`                                                | Yes | [URL yeniden yazma ara yazılımı `IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **İstek İzleyicisi**<br>`RequestMonitorModule`                                                    | Yes | |
| **URL yeniden yazma**&#8224;<br>`RewriteModule`                                                      | Yes | [URL yeniden yazma ara yazılımı](xref:fundamentals/url-rewriting) |
| **Sunucu tarafı eklemeleri**<br>`ServerSideIncludeModule`                                            | No  | |
| **Statik sıkıştırma**<br>`StaticCompressionModule`                                              | No  | [Yanıt sıkıştırma ara yazılımı](xref:performance/response-compression) |
| **Statik İçerik**<br>`StaticFileModule`                                                         | No  | [Statik dosya ara yazılımı](xref:fundamentals/static-files) |
| **Belirteç önbelleğe alma**<br>`TokenCacheModule`                                                          | Yes | |
| **URI önbelleğe alma**<br>`UriCacheModule`                                                              | Yes | |
| **URL Yetkilendirmesi**<br>`UrlAuthorizationModule`                                                | Yes | [ASP.NET Core Identity](xref:security/authentication/identity) |
| **Windows Kimlik Doğrulaması**<br>`WindowsAuthenticationModule`                                      | Yes | |

&#8224;, `isFile` `isDirectory` [Dizin YAPıSıNDAKI](xref:host-and-deploy/directory-structure)değişiklikler nedeniyle URL yeniden yazma modülünün ve eşleşme türlerinin ASP.NET Core uygulamalarla birlikte çalışmıyor.

## <a name="managed-modules"></a>Yönetilen modüller

Yönetilen modüller, uygulama havuzunun .NET CLR sürümü **yönetilen kod olmadan**ayarlandığında barındırılan ASP.NET Core uygulamalarıyla işlevsel *değildir* . ASP.NET Core çeşitli durumlarda ara yazılım alternatifleri sunmaktadır.

| Modül                  | ASP.NET Core seçeneği |
| ----------------------- | ------------------- |
| AnonymousIdentification | |
| DefaultAuthentication   | |
| Dosya yetkilendirmesi       | |
| FormsAuthentication     | [Cookie Kimlik doğrulama ara yazılımı](xref:security/authentication/cookie) |
| OutputCache             | [Yanıt önbelleğe alma ara yazılımı](xref:performance/caching/middleware) |
| Profil                 | |
| 'Da             | |
| ScriptModule-4,0        | |
| Oturum                 | [Oturum ara yazılımı](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModule       | [URL yeniden yazma ara yazılımı](xref:fundamentals/url-rewriting) |
| UrlRoutingModule-4,0    | [ASP.NET Core Identity](xref:security/authentication/identity) |
| WindowsAuthentication   | |

## <a name="iis-manager-application-changes"></a>IIS Yöneticisi uygulama değişiklikleri

Ayarları yapılandırmak için IIS Yöneticisi kullanılırken, uygulamanın *web.config* dosyası değiştirilir. Bir uygulamayı dağıtırken ve *web.config*dahil olmak üzere, IIS Yöneticisi ile yapılan tüm değişiklikler dağıtılan *web.config* dosyası tarafından üzerine yazılır. Sunucunun *web.config* dosyasında değişiklik yapılırsa, sunucudaki güncelleştirilmiş *web.config* dosyasını hemen yerel projeye kopyalayın.

## <a name="disabling-iis-modules"></a>IIS modüllerini devre dışı bırakma

Bir uygulama için devre dışı bırakılması gereken sunucu düzeyinde bir IIS modülü yapılandırılırsa, uygulamanın *web.config* dosyasına ek olarak modülü devre dışı bırakabilirsiniz. Modülü yerinde bırakın ve bir yapılandırma ayarı (varsa) kullanarak devre dışı bırakın ya da modülü uygulamadan kaldırın.

### <a name="module-deactivation"></a>Modül devre dışı bırakma

Birçok modül, uygulamanın modül uygulamadan kaldırılmadan devre dışı olmasına izin veren bir yapılandırma ayarı sunar. Bu, bir modülün devre dışı bırakılması için en basit ve en hızlı yoldur. Örneğin, HTTP yeniden yönlendirme modülüweb.configöğesi ile devre dışı bırakılabilir `<httpRedirect>` : * *

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Yapılandırma ayarları ile modülleri devre dışı bırakma hakkında daha fazla bilgi için, [ \<system.webServer> IIS ](/iis/configuration/system.webServer/)'nin *alt öğeler* bölümündeki bağlantıları izleyin.

### <a name="module-removal"></a>Modül kaldırma

*web.config*bir ayarı olan bir modülü kaldırmak istiyorsanız modülün kilidini açın ve `<modules>` önce *web.config* bölümünün kilidini açın:

1. Modülün kilidini sunucu düzeyinde açın. IIS Yöneticisi **bağlantıları** kenar çubuğundan IIS sunucusunu seçin. **IIS** alanında **modüller** ' i açın. Listeden modülü seçin. Sağdaki **Eylemler** kenar çubuğunda, **Aç**' ı seçin. Modülün eylem girdisi **kilit**olarak görünürse, modülün kilidi zaten açık olur ve herhangi bir eylem gerekmez. *web.config* daha sonra kaldırmayı planladığınız sayıda modülün kilidini açın.

2. Uygulamayıweb.configbir bölüm olmadan dağıtın `<modules>` . * * Bir uygulama, IIS Yöneticisi 'nde ilk olarak bölümün kilidini açmadan bölüm içeren bir *web.config* dağıtılırsa `<modules>` , Configuration Manager bölümün kilidini açmaya çalışırken bir özel durum oluşturur. Bu nedenle, uygulamayı bir bölüm olmadan dağıtın `<modules>` .

3. `<modules>` *web.config*bölümünün kilidini açın. **Bağlantılar** kenar çubuğunda **sitelerde**Web sitesini seçin. **Yönetim** alanında, **yapılandırma düzenleyicisini**açın. Bölümü seçmek için gezinti denetimlerini kullanın `system.webServer/modules` . Sağdaki **Eylemler** kenar çubuğunda, bölümün **kilidini açmak** için öğesini seçin. Modül bölümü için eylem girişi **kilit bölümü**olarak görünürse modül bölümünün kilidi zaten açık olur ve herhangi bir eylem gerekmez.

4. `<modules>`Modülün uygulamadan kaldırılması için, bir öğesi ile uygulamanın yerel *web.config* dosyasına bir bölüm ekleyin `<remove>` . Birden çok `<remove>` modülü kaldırmak için birden çok öğe ekleyin. Sunucuda *web.config* değişiklik yapılırsa, projenin *web.config* dosyasında aynı değişiklikleri hemen yerel olarak yapın. Bu yaklaşımı kullanarak bir modülün kaldırılması, modülün sunucu üzerindeki diğer uygulamalarla kullanımını etkilemez.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

*web.config*kullanarak IIS Express modül eklemek veya kaldırmak için, bölümün kilidini açmak üzere *applicationHost.config* değiştirin `<modules>` :

1. *{APPLICATION root} \\.vs\config\applicationhost.config*açın.

1. `<section>`IIS modülleri için öğesini bulun ve ' `overrideModeDefault` den ' `Deny` ye geçin `Allow` :

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. `<location path="" overrideMode="Allow"><system.webServer><modules>` bölümünü bulun. Kaldırmak istediğiniz modüller için, ' dan ' a ayarlayın `lockItem` `true` `false` . Aşağıdaki örnekte, CGI modülünün kilidi açıldı:

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. `<modules>`Bölüm ve bağımsız modüllerin kilidi açıldıktan sonra, uygulamayı IIS Express çalıştırmak için uygulamanın *web.config* dosyasını kullanarak IIS modüllerini ekleme veya kaldırma ücretsizdir.

Ayrıca, *Appcmd.exe*Ile bir IIS modülü de kaldırılabilir. `MODULE_NAME`Komutuna ve komutuna şunu girin `APPLICATION_NAME` :

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Örneğin, `DynamicCompressionModule` varsayılan Web sitesinden öğesini kaldırın:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>En düşük modül yapılandırması

ASP.NET Core bir uygulamayı çalıştırmak için gereken tek modüller, anonim kimlik doğrulama modülüdür ve ASP.NET Core modülüdür.

URI önbelleğe alma modülü ( `UriCacheModule` ), IIS 'nin URL düzeyinde Web sitesi yapılandırmasını önbelleğe almasına izin verir. Bu modül olmadan, aynı URL sürekli olarak istendiği zaman bile IIS her istekte yapılandırmayı okumalı ve ayrıştıramalıdır. Yapılandırma ayrıştırılırken her istek önemli bir performans cezasına neden olur. *Barındırılan bir ASP.NET Core uygulamasının çalışması için URI önbelleğe alma modülü kesinlikle gerekli olmasa da, tüm ASP.NET Core dağıtımları için URI önbelleğe alma modülünün etkinleştirilmesini öneririz.*

HTTP önbelleğe alma modülü ( `HttpCacheModule` ), IIS çıkış önbelleğini ve ayrıca HTTP.sys önbelleğindeki öğeleri önbelleğe alma mantığını uygular. Bu modül olmadan içerik artık çekirdek modunda önbelleğe alınmaz ve önbellek profilleri yok sayılır. HTTP önbelleğe alma modülünün kaldırılması genellikle performans ve kaynak kullanımındaki olumsuz etkileri vardır. *Barındırılan bir ASP.NET Core uygulamasının çalışması için HTTP önbelleğe alma modülü kesinlikle gerekli olmasa da, tüm ASP.NET Core dağıtımları için HTTP önbelleğe alma modülünün etkinleştirilmesini öneririz.*

## <a name="additional-resources"></a>Ek kaynaklar

* [IIS mimarilerine giriş: IIS 'deki modüller](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [IIS modüllerine genel bakış](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [IIS 7,0 rollerini ve modüllerini özelleştirme](https://technet.microsoft.com/library/cc627313.aspx)
* [ISS \<system.webServer>](/iis/configuration/system.webServer/)
