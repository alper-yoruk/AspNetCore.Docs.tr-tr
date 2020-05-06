---
title: ASP.NET Core ile IIS modülleri
author: rick-anderson
description: ASP.NET Core uygulamalar için etkin ve etkin olmayan IIS modüllerini ve IIS modüllerinin nasıl yönetileceğini bulun.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 7262b9ea18e4cf6acd278d087fcc44262f8f9c80
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775953"
---
# <a name="iis-modules-with-aspnet-core"></a>ASP.NET Core ile IIS modülleri

Yerel IIS modüllerinden bazıları ve tüm IIS yönetilen modülleri, ASP.NET Core Apps isteklerini işleyemez. Birçok durumda ASP.NET Core, IIS yerel ve yönetilen modüller tarafından ele alınan senaryolara bir alternatif sağlar.

## <a name="native-modules"></a>Yerel modüller

Tablo, ASP.NET Core uygulamalar ve ASP.NET Core modülü ile işlevsel yerel IIS modüllerini gösterir.

| Modül | ASP.NET Core uygulamalarla işlevsel | ASP.NET Core seçeneği |
| --- | :---: | --- |
| **Anonim kimlik doğrulaması**<br>`AnonymousAuthenticationModule`                                  | Yes | |
| **Temel kimlik doğrulaması**<br>`BasicAuthenticationModule`                                          | Yes | |
| **İstemci sertifikası eşleme kimlik doğrulaması**<br>`CertificateMappingAuthenticationModule`      | Yes | |
| **CGI**<br>`CgiModule`                                                                           | Hayır  | |
| **Yapılandırma doğrulaması**<br>`ConfigurationValidationModule`                                  | Yes | |
| **HTTP Hataları**<br>`CustomErrorModule`                                                           | Hayır  | [Durum kodu sayfaları ara yazılımı](xref:fundamentals/error-handling#usestatuscodepages) |
| **Özel günlüğe kaydetme**<br>`CustomLoggingModule`                                                      | Yes | |
| **Varsayılan Belge**<br>`DefaultDocumentModule`                                                  | Hayır  | [Varsayılan dosyalar ara yazılımı](xref:fundamentals/static-files#serve-a-default-document) |
| **Özet kimlik doğrulaması**<br>`DigestAuthenticationModule`                                        | Yes | |
| **Dizin Tarama**<br>`DirectoryListingModule`                                               | Hayır  | [Dizin tarama ara yazılımı](xref:fundamentals/static-files#enable-directory-browsing) |
| **Dinamik sıkıştırma**<br>`DynamicCompressionModule`                                            | Yes | [Yanıt sıkıştırma ara yazılımı](xref:performance/response-compression) |
| **Başarısız Istek Izleme**<br>`FailedRequestsTracingModule`                                     | Yes | [Günlüğe kaydetme ASP.NET Core](xref:fundamentals/logging/index#tracesource-provider) |
| **Dosya önbelleğe alma**<br>`FileCacheModule`                                                            | Hayır  | [Yanıt önbelleğe alma ara yazılımı](xref:performance/caching/middleware) |
| **HTTP önbelleği**<br>`HttpCacheModule`                                                            | Hayır  | [Yanıt önbelleğe alma ara yazılımı](xref:performance/caching/middleware) |
| **HTTP Günlüğe Kaydetme**<br>`HttpLoggingModule`                                                          | Yes | [Günlüğe kaydetme ASP.NET Core](xref:fundamentals/logging/index) |
| **HTTP Yeniden Yönlendirme**<br>`HttpRedirectionModule`                                                  | Yes | [URL yeniden yazma ara yazılımı](xref:fundamentals/url-rewriting) |
| **HTTP Izleme**<br>`TracingModule`                                                              | Yes | |
| **IIS Istemci sertifikası eşleme kimlik doğrulaması**<br>`IISCertificateMappingAuthenticationModule` | Yes | |
| **IP ve Etki Alanı Kısıtlamaları**<br>`IpRestrictionModule`                                          | Yes | |
| **ISAPI Filtreleri**<br>`IsapiFilterModule`                                                         | Yes | [Ara yazılım](xref:fundamentals/middleware/index) |
| **I**<br>`IsapiModule`                                                                       | Yes | [Ara yazılım](xref:fundamentals/middleware/index) |
| **Protokol desteği**<br>`ProtocolSupportModule`                                                  | Yes | |
| **İstek Filtreleme**<br>`RequestFilteringModule`                                                | Yes | [URL yeniden yazma ara yazılımı`IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **İstek İzleyicisi**<br>`RequestMonitorModule`                                                    | Yes | |
| **URL yeniden yazma**&#8224;<br>`RewriteModule`                                                      | Yes | [URL yeniden yazma ara yazılımı](xref:fundamentals/url-rewriting) |
| **Sunucu tarafı eklemeleri**<br>`ServerSideIncludeModule`                                            | Hayır  | |
| **Statik sıkıştırma**<br>`StaticCompressionModule`                                              | Hayır  | [Yanıt sıkıştırma ara yazılımı](xref:performance/response-compression) |
| **Statik İçerik**<br>`StaticFileModule`                                                         | Hayır  | [Statik dosya ara yazılımı](xref:fundamentals/static-files) |
| **Belirteç önbelleğe alma**<br>`TokenCacheModule`                                                          | Yes | |
| **URI önbelleğe alma**<br>`UriCacheModule`                                                              | Yes | |
| **URL Yetkilendirmesi**<br>`UrlAuthorizationModule`                                                | Yes | [ASP.NET Core kimliği](xref:security/authentication/identity) |
| **Windows Kimlik Doğrulaması**<br>`WindowsAuthenticationModule`                                      | Yes | |

&#8224;, [dizin yapısındaki](xref:host-and-deploy/directory-structure)DEĞIŞIKLIKLER nedeniyle `isFile` URL `isDirectory` yeniden yazma modülünün ve eşleşme türlerinin ASP.NET Core uygulamalarla birlikte çalışmıyor.

## <a name="managed-modules"></a>Yönetilen modüller

Yönetilen modüller, uygulama havuzunun .NET CLR sürümü **yönetilen kod olmadan**ayarlandığında barındırılan ASP.NET Core uygulamalarıyla işlevsel *değildir* . ASP.NET Core çeşitli durumlarda ara yazılım alternatifleri sunmaktadır.

| Modül                  | ASP.NET Core seçeneği |
| ----------------------- | ------------------- |
| AnonymousIdentification | |
| DefaultAuthentication   | |
| Dosya yetkilendirmesi       | |
| FormsAuthentication     | [Tanımlama bilgisi kimlik doğrulama ara yazılımı](xref:security/authentication/cookie) |
| OutputCache             | [Yanıt önbelleğe alma ara yazılımı](xref:performance/caching/middleware) |
| Profil                 | |
| 'Da             | |
| ScriptModule-4,0        | |
| Oturum                 | [Oturum ara yazılımı](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModule       | [URL yeniden yazma ara yazılımı](xref:fundamentals/url-rewriting) |
| UrlRoutingModule-4,0    | [ASP.NET CoreIdentity](xref:security/authentication/identity) |
| WindowsAuthentication   | |

## <a name="iis-manager-application-changes"></a>IIS Yöneticisi uygulama değişiklikleri

Ayarları yapılandırmak için IIS Yöneticisi kullanılırken, uygulamanın *Web. config* dosyası değiştirilir. Bir uygulamayı dağıtırken ve *Web. config*dahil olmak üzere, IIS Yöneticisi ile yapılan tüm değişikliklerin üzerine dağıtılan *Web. config* dosyası tarafından üzerine yazılır. Sunucunun *Web. config* dosyasında değişiklik yapılırsa, sunucudaki güncelleştirilmiş *Web. config* dosyasını hemen yerel projeye kopyalayın.

## <a name="disabling-iis-modules"></a>IIS modüllerini devre dışı bırakma

Bir uygulama için devre dışı bırakılması gereken sunucu düzeyinde bir IIS modülü yapılandırılırsa, uygulamanın *Web. config* dosyasına ek olarak modülü devre dışı bırakabilirsiniz. Modülü yerinde bırakın ve bir yapılandırma ayarı (varsa) kullanarak devre dışı bırakın ya da modülü uygulamadan kaldırın.

### <a name="module-deactivation"></a>Modül devre dışı bırakma

Birçok modül, uygulamanın modül uygulamadan kaldırılmadan devre dışı olmasına izin veren bir yapılandırma ayarı sunar. Bu, bir modülün devre dışı bırakılması için en basit ve en hızlı yoldur. Örneğin, HTTP yeniden yönlendirme modülü `<httpRedirect>` *Web. config*içindeki öğesiyle devre dışı bırakılabilir:

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Yapılandırma ayarları ile modülleri devre dışı bırakma hakkında daha fazla bilgi için [IIS \<system. webserver>](/iis/configuration/system.webServer/) *alt öğeler* bölümündeki bağlantıları izleyin.

### <a name="module-removal"></a>Modül kaldırma

Bir modülü *Web. config*'deki bir ayarla kaldırmak istiyorsanız modülün kilidini açın ve önce `<modules>` *Web. config* bölümünün kilidini açın:

1. Modülün kilidini sunucu düzeyinde açın. IIS Yöneticisi **bağlantıları** kenar çubuğundan IIS sunucusunu seçin. **IIS** alanında **modüller** ' i açın. Listeden modülü seçin. Sağdaki **Eylemler** kenar çubuğunda, **Aç**' ı seçin. Modülün eylem girdisi **kilit**olarak görünürse, modülün kilidi zaten açık olur ve herhangi bir eylem gerekmez. *Web. config* 'den daha sonra kaldırmayı planladığınız sayıda modülün kilidini açın.

2. Uygulamayı *Web. config*'de `<modules>` bir bölüm olmadan dağıtın. Bir uygulama, IIS Yöneticisi 'nde ilk olarak bölümünün kilidini açmadan `<modules>` bölümü içeren bir *Web. config* ile dağıtılırsa, bölümün kilidini açmaya çalışırken Configuration Manager bir özel durum oluşturur. Bu nedenle, uygulamayı bir `<modules>` bölüm olmadan dağıtın.

3. `<modules>` *Web. config*bölümünün kilidini açın. **Bağlantılar** kenar çubuğunda **sitelerde**Web sitesini seçin. **Yönetim** alanında, **yapılandırma düzenleyicisini**açın. `system.webServer/modules` Bölümü seçmek için gezinti denetimlerini kullanın. Sağdaki **Eylemler** kenar çubuğunda, bölümün **kilidini açmak** için öğesini seçin. Modül bölümü için eylem girişi **kilit bölümü**olarak görünürse modül bölümünün kilidi zaten açık olur ve herhangi bir eylem gerekmez.

4. Uygulamanın yerel `<modules>` *Web. config* dosyasına `<remove>` modülünü uygulamadan kaldırmak için bir bölüm ekleyin. Birden çok `<remove>` modülü kaldırmak için birden çok öğe ekleyin. Sunucuda *Web. config* değişiklikleri yapılırsa, projenin *Web. config* dosyasında bu değişiklikleri hemen yerel olarak yapın. Bu yaklaşımı kullanarak bir modülün kaldırılması, modülün sunucu üzerindeki diğer uygulamalarla kullanımını etkilemez.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

*Web. config*kullanarak IIS Express modül eklemek veya kaldırmak Için, *ApplicationHost. config* ' i değiştirerek `<modules>` bölümün kilidini açın:

1. *{APPLICATION root}\\. vs\config\applicationhost,config*dosyasını açın.

1. IIS modülleri `<section>` için öğesini bulun ve ' den `overrideModeDefault` `Deny` ' ye `Allow`geçin:

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. `<location path="" overrideMode="Allow"><system.webServer><modules>` bölümünü bulun. Kaldırmak istediğiniz modüller için, ' dan `lockItem` `true` ' a ayarlayın. `false` Aşağıdaki örnekte, CGI modülünün kilidi açıldı:

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. `<modules>` Bölüm ve bağımsız modüllerin kilidi açıldıktan sonra, uygulamayı IIS Express üzerinde çalıştırmak için uygulamanın *Web. CONFIG* dosyasını kullanarak IIS modüllerini ekleme veya kaldırma ücretsizdir.

Ayrıca, *Appcmd. exe*Ile bir IIS modülü de kaldırılabilir. Komutuna `MODULE_NAME` ve `APPLICATION_NAME` komutuna şunu girin:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Örneğin, varsayılan Web sitesinden `DynamicCompressionModule` öğesini kaldırın:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>En düşük modül yapılandırması

ASP.NET Core bir uygulamayı çalıştırmak için gereken tek modüller, anonim kimlik doğrulama modülüdür ve ASP.NET Core modülüdür.

URI önbelleğe alma modülü (`UriCacheModule`), IIS 'nin URL düzeyinde Web sitesi yapılandırmasını önbelleğe almasına izin verir. Bu modül olmadan, aynı URL sürekli olarak istendiği zaman bile IIS her istekte yapılandırmayı okumalı ve ayrıştıramalıdır. Yapılandırma ayrıştırılırken her istek önemli bir performans cezasına neden olur. *Barındırılan bir ASP.NET Core uygulamasının çalışması için URI önbelleğe alma modülü kesinlikle gerekli olmasa da, tüm ASP.NET Core dağıtımları için URI önbelleğe alma modülünün etkinleştirilmesini öneririz.*

HTTP önbelleğe alma modülü (`HttpCacheModule`), IIS çıkış önbelleğini ve ayrıca http. sys önbelleğinde öğeleri önbelleğe alma mantığını uygular. Bu modül olmadan içerik artık çekirdek modunda önbelleğe alınmaz ve önbellek profilleri yok sayılır. HTTP önbelleğe alma modülünün kaldırılması genellikle performans ve kaynak kullanımındaki olumsuz etkileri vardır. *Barındırılan bir ASP.NET Core uygulamasının çalışması için HTTP önbelleğe alma modülü kesinlikle gerekli olmasa da, tüm ASP.NET Core dağıtımları için HTTP önbelleğe alma modülünün etkinleştirilmesini öneririz.*

## <a name="additional-resources"></a>Ek kaynaklar

* [IIS mimarilerine giriş: IIS 'deki modüller](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [IIS modüllerine genel bakış](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [IIS 7,0 rollerini ve modüllerini özelleştirme](https://technet.microsoft.com/library/cc627313.aspx)
* [IIS \<System. webserver>](/iis/configuration/system.webServer/)
