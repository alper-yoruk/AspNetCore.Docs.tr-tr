---
title: ASP.NET Core'lu IIS modülleri
author: rick-anderson
description: ASP.NET Core uygulamaları ve IIS modüllerini nasıl yöneteceğimiz için etkin ve etkin olmayan IIS modüllerini keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 0f13ef3eb1da03960ef1fa54d33532b6ebbdc128
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657908"
---
# <a name="iis-modules-with-aspnet-core"></a>ASP.NET Core'lu IIS modülleri

Bazı yerel IIS modülleri ve Tüm IIS yönetilen modülleri ASP.NET Core uygulamaları için istekleri ni işleyemez. Birçok durumda, ASP.NET Core, IIS yerel ve yönetilen modüller tarafından ele verilen senaryolara bir alternatif sunar.

## <a name="native-modules"></a>Yerel modüller

Tablo, ASP.NET Core uygulamaları ve ASP.NET Çekirdek Modülü ile işlevsel olan yerel IIS modüllerini gösterir.

| Modül | ASP.NET Core uygulamalarıyla işlevsel | ASP.NET Çekirdek Seçeneği |
| --- | :---: | --- |
| **Anonim Kimlik Doğrulama**<br>`AnonymousAuthenticationModule`                                  | Evet | |
| **Temel Kimlik Doğrulama**<br>`BasicAuthenticationModule`                                          | Evet | |
| **İstemci Sertifikası Haritalama Kimlik Doğrulama**<br>`CertificateMappingAuthenticationModule`      | Evet | |
| **CGI**<br>`CgiModule`                                                                           | Hayır  | |
| **Yapılandırma Doğrulama**<br>`ConfigurationValidationModule`                                  | Evet | |
| **HTTP Hataları**<br>`CustomErrorModule`                                                           | Hayır  | [Durum Kodu Sayfaları Middleware](xref:fundamentals/error-handling#usestatuscodepages) |
| **Özel Günlük**<br>`CustomLoggingModule`                                                      | Evet | |
| **Varsayılan Belge**<br>`DefaultDocumentModule`                                                  | Hayır  | [Varsayılan Dosyalar Middleware](xref:fundamentals/static-files#serve-a-default-document) |
| **Özet Kimlik Doğrulama**<br>`DigestAuthenticationModule`                                        | Evet | |
| **Dizin Tarama**<br>`DirectoryListingModule`                                               | Hayır  | [Dizin Tarama Middleware](xref:fundamentals/static-files#enable-directory-browsing) |
| **Dinamik Sıkıştırma**<br>`DynamicCompressionModule`                                            | Evet | [Yanıt Sıkıştırma Middleware](xref:performance/response-compression) |
| **Başarısız İstekleri İzleme**<br>`FailedRequestsTracingModule`                                     | Evet | [ASP.NET Çekirdek Günlüğü](xref:fundamentals/logging/index#tracesource-provider) |
| **Dosya Önbelleğe Alma**<br>`FileCacheModule`                                                            | Hayır  | [Yanıtları Önbelleğe Alma Ara Yazılımı](xref:performance/caching/middleware) |
| **HTTP Önbelleğe Alma**<br>`HttpCacheModule`                                                            | Hayır  | [Yanıtları Önbelleğe Alma Ara Yazılımı](xref:performance/caching/middleware) |
| **HTTP Günlüğe Kaydetme**<br>`HttpLoggingModule`                                                          | Evet | [ASP.NET Çekirdek Günlüğü](xref:fundamentals/logging/index) |
| **HTTP Yeniden Yönlendirme**<br>`HttpRedirectionModule`                                                  | Evet | [URL Middleware Yeniden Yazma](xref:fundamentals/url-rewriting) |
| **HTTP İzleme**<br>`TracingModule`                                                              | Evet | |
| **IIS İstemci Sertifikası Haritalama Kimlik Doğrulaması**<br>`IISCertificateMappingAuthenticationModule` | Evet | |
| **IP ve Etki Alanı Kısıtlamaları**<br>`IpRestrictionModule`                                          | Evet | |
| **ISAPI Filtreleri**<br>`IsapiFilterModule`                                                         | Evet | [Ara yazılım](xref:fundamentals/middleware/index) |
| **ısapı**<br>`IsapiModule`                                                                       | Evet | [Ara yazılım](xref:fundamentals/middleware/index) |
| **Protokol Desteği**<br>`ProtocolSupportModule`                                                  | Evet | |
| **İstek Filtreleme**<br>`RequestFilteringModule`                                                | Evet | [URL Middleware Yeniden Yazma`IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **İstek İzleyicisi**<br>`RequestMonitorModule`                                                    | Evet | |
| **URL&#8224; Yeniden Yazma**<br>`RewriteModule`                                                      | Evet | [URL Middleware Yeniden Yazma](xref:fundamentals/url-rewriting) |
| **Sunucu Tarafı Içerir**<br>`ServerSideIncludeModule`                                            | Hayır  | |
| **Statik Sıkıştırma**<br>`StaticCompressionModule`                                              | Hayır  | [Yanıt Sıkıştırma Middleware](xref:performance/response-compression) |
| **Statik İçerik**<br>`StaticFileModule`                                                         | Hayır  | [Statik Dosya Middleware](xref:fundamentals/static-files) |
| **Belirteç Önbelleğe Alma**<br>`TokenCacheModule`                                                          | Evet | |
| **URI Önbelleğe Alma**<br>`UriCacheModule`                                                              | Evet | |
| **URL Yetkilendirmesi**<br>`UrlAuthorizationModule`                                                | Evet | [ASP.NET Çekirdek Kimlik](xref:security/authentication/identity) |
| **Windows Kimlik Doğrulama**<br>`WindowsAuthenticationModule`                                      | Evet | |

&#8224;URL Yeniden Yazma `isFile` Modülü `isDirectory` ve maç türleri [dizin yapısındaki](xref:host-and-deploy/directory-structure)değişiklikler nedeniyle ASP.NET Core uygulamaları ile çalışmıyor.

## <a name="managed-modules"></a>Yönetilen modüller

Yönetilen modüller, uygulama havuzunun .NET CLR sürümü **Yönetilen Kod Yok**olarak ayarlandığında barındırılan ASP.NET Core uygulamalarıyla işlevsel *değildir.* ASP.NET Core çeşitli durumlarda ara yazılım alternatifleri sunar.

| Modül                  | ASP.NET Çekirdek Seçeneği |
| ----------------------- | ------------------- |
| Anonymousıdentification | |
| Varsayılan Kimlik Doğrulama   | |
| Dosya Yetkilendirme       | |
| Formlar Orijinalasyon     | [Çerez Kimlik Doğrulama Middleware](xref:security/authentication/cookie) |
| Outputcache             | [Yanıtları Önbelleğe Alma Ara Yazılımı](xref:performance/caching/middleware) |
| Profil                 | |
| Rolemanager             | |
| Komut Dosyası Modülü-4.0        | |
| Oturum                 | [Oturum Middleware](xref:fundamentals/app-state) |
| UrlYetkilendirme        | |
| UrlMappingsModülü       | [URL Middleware Yeniden Yazma](xref:fundamentals/url-rewriting) |
| UrlRoutingModülü-4.0    | [ASP.NET Çekirdek Kimlik](xref:security/authentication/identity) |
| Windows Kimlik Doğrulama   | |

## <a name="iis-manager-application-changes"></a>IIS Manager uygulama değişiklikleri

Ayarları yapılandırmak için IIS Manager kullanırken, uygulamanın *web.config* dosyası değiştirilir. Bir uygulama dağıtılıyorsa ve *web.config*dahilse, IIS Manager ile yapılan tüm değişiklikler dağıtılan *web.config* dosyası tarafından üzerine yazılır. Sunucunun *web.config* dosyasında değişiklik yapılırsa, sunucudaki güncelleştirilmiş *web.config* dosyasını hemen yerel projeye kopyalayın.

## <a name="disabling-iis-modules"></a>IIS modüllerinin devre dışı bırakılması

Bir IIS modülü bir uygulama için devre dışı bırakılmış olması gereken sunucu düzeyinde yapılandırılırsa, uygulamanın *web.config* dosyasına eklenen bir ek modülü devre dışı kılabilir. Modülü yerinde bırakın ve bir yapılandırma ayarı kullanarak devre dışı bırakın (varsa) veya modülü uygulamadan kaldırın.

### <a name="module-deactivation"></a>Modül devre dışı bırakma

Birçok modül, modülü uygulamadan çıkarmadan devre dışı bırakılmalarını sağlayan bir yapılandırma ayarı sunar. Bu, bir modülü devre dışı bırakmanın en basit ve en hızlı yoludur. Örneğin, HTTP Yönlendirme Modülü `<httpRedirect>` *web.config*öğesi ile devre dışı olabilir:

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Yapılandırma ayarlarıile modülleri devre dışı bırakma hakkında daha fazla bilgi için, [IIS \<system.webServer>'nin ](/iis/configuration/system.webServer/) *Alt Öğeler* bölümündeki bağlantıları izleyin.

### <a name="module-removal"></a>Modül kaldırma

*web.config'de*ayarı olan bir modülü kaldırmayı seçerseniz, `<modules>` modülün kilidini açın ve önce *web.config* bölümünün kilidini açın:

1. Sunucu düzeyinde modülün kilidini açın. IIS Manager **Connections** kenar çubuğundaki IIS sunucusunu seçin. **IIS** alanındaki **modülleri** açın. Listedeki modülü seçin. Sağdaki **Eylemler** kenar çubuğunda **Kilit Aç'ı**seçin. Modülün eylem girişi **Kilit**olarak görünüyorsa, modülün kilidi zaten açık ve herhangi bir eylem gerekmez. Daha sonra *web.config'den* kaldırmayı planladığınız kadar modülün kilidini açın.

2. Uygulamayı `<modules>` *web.config'de*bir bölüm olmadan dağıtın. Bir uygulama, IIS Yöneticisi'ndeki `<modules>` bölümün kilidini açmadan bölümü içeren bir *web.config* ile dağıtılırsa, Configuration Manager bölümün kilidini açmaya çalışırken bir özel durum oluşturur. Bu nedenle, uygulamayı `<modules>` bir bölüm olmadan dağıtın.

3. `<modules>` *web.config*bölümünün kilidini açın. **Bağlantılar** kenar çubuğunda, **Siteler'deki**web sitesini seçin. **Yönetim** alanında, Yapılandırma **Düzenleyicisi'ni**açın. Bölümü seçmek için gezinti `system.webServer/modules` denetimlerini kullanın. Sağdaki **Eylemler** kenar çubuğunda, bölümün **Kilidini Açmak'ı** seçin. Modül bölümü için eylem girişi **Kilit Bölümü**olarak görünüyorsa, modül bölümü zaten kilitlenir ve herhangi bir eylem gerekmez.

4. Modülü `<modules>` uygulamadan kaldırmak için bir öğe içeren uygulamanın yerel *web.config* dosyasına bir `<remove>` bölüm ekleyin. Birden `<remove>` çok modülü kaldırmak için birden çok öğe ekleyin. Sunucuda *web.config* değişiklikleri yapılırsa, hemen aynı değişiklikleri projenin *web.config* dosyasında yerel olarak yapın. Bu yaklaşımı kullanarak bir modülün kaldırılması, modülün sunucudaki diğer uygulamalarla kullanımını etkilemez.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

*Web.config*kullanarak IIS Express için modül eklemek veya kaldırmak `<modules>` için, bölümün kilidini açmak için *applicationHost.config'i* değiştirin:

1. Açık *{APPLICATION\\ROOT} .vs\config\applicationhost.config*.

1. IIS `<section>` modülleri için öğeyi `overrideModeDefault` `Deny` bulun `Allow`ve aşağıdakilere değiştirin:

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. `<location path="" overrideMode="Allow"><system.webServer><modules>` bölümünü bulun. Kaldırmak istediğiniz tüm modüller için `lockItem` `true` `false`, ' dan Aşağıdaki örnekte, CGI Modülü nün kilidi açılır:

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. `<modules>` Bölüm ve modüllerin kilidi açıldıktan sonra, uygulamayı IIS Express'te çalıştırmak için uygulamanın *web.config* dosyasını kullanarak IIS modüllerini eklemekte veya kaldırmakta özgürdür.

Bir IIS modülü de *Appcmd.exe*ile kaldırılabilir. `MODULE_NAME` Ve `APPLICATION_NAME` komutu sağlayın:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Örneğin, Varsayılan `DynamicCompressionModule` Web Sitesinden kaldırın:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>Minimum modül yapılandırması

ASP.NET Core uygulamasını çalıştırmak için gereken tek modül anonim kimlik doğrulama modülü ve ASP.NET Çekirdek Modülüdür.

URI Önbelleğe`UriCacheModule`Alma Modülü ( ) IIS'nin web sitesi yapılandırmasını URL düzeyinde önbelleğe almasına olanak tanır. Bu modül olmadan, IIS her istekte yapılandırmayı okumalı ve ayrıştırmalıdır, aynı URL tekrar tekrar talep edilse bile. Yapılandırmayı ayrıştığa her istek önemli bir performans cezası ile sonuçlanır. *URI Önbelleğe Alma Modülü, barındırılan bir ASP.NET Core uygulamasının çalışması için kesinlikle gerekli olmasa da, URI Önbelleğe Alma Modülü'nün tüm ASP.NET Core dağıtımları için etkinleştirilmesini öneririz.*

HTTP Önbelleğe`HttpCacheModule`Alma Modülü ( ) IIS çıkış önbelleğini ve ayrıca HTTP.sys önbelleğindeki öğeleri önbelleğe alma mantığını uygular. Bu modül olmadan, içerik artık çekirdek modunda önbelleğe verilmez ve önbellek profilleri yoksayılır. HTTP Önbelleğe Alma Modülü'nün kaldırılması genellikle performans ve kaynak kullanımı üzerinde olumsuz etkilere sahiptir. *HTTP Önbelleğe Alma Modülü, barındırılan bir ASP.NET Core uygulamasının çalışması için kesinlikle gerekli olmasa da, HTTP Önbelleğe Alma Modülü'nün tüm ASP.NET Core dağıtımları için etkinleştirilmesini öneririz.*

## <a name="additional-resources"></a>Ek kaynaklar

* [IIS Mimarilerine Giriş: IIS'deki Modüller](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [IIS Modüllerine Genel Bakış](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [IIS 7.0 Rolleri ve Modüllerini Özelleştirme](https://technet.microsoft.com/library/cc627313.aspx)
* [IIS \<system.webServer>](/iis/configuration/system.webServer/)
