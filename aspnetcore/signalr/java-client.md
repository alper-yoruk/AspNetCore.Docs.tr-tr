---
title: ASP.NET Core SignalR Java istemcisi
author: mikaelm12
description: ASP.NET Core SignalR Java istemcisinin nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/java-client
ms.openlocfilehash: 33c1e3b9b2b8990c811f3b49a978cbc630294c81
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777260"
---
# <a name="aspnet-core-signalr-java-client"></a>ASP.NET Core SignalR Java istemcisi

X [MIKAEL Mengistu](https://twitter.com/MikaelM_12) tarafından

Java istemcisi, Android uygulamaları dahil olmak üzere SignalR Java kodundan bir ASP.NET Core sunucusuna bağlanmasını sağlar. [JavaScript istemcisi](xref:signalr/javascript-client) ve [.NET istemcisi](xref:signalr/dotnet-client)gibi Java istemcisi, bir hub 'a gerçek zamanlı iletiler alıp göndermenizi sağlar. Java istemcisi ASP.NET Core 2,2 ve üzeri sürümlerde kullanılabilir.

Bu makalede başvurulan örnek Java konsol uygulaması SignalR Java istemcisini kullanır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-java-client-package"></a>SignalR Java istemci paketini yükler

*SignalR-1.0.0* jar dosyası istemcilerin SignalR hub 'lara bağlanmasına izin verir. En son JAR dosyası sürüm numarasını bulmak için bkz. [Maven arama sonuçları](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Gradle kullanıyorsanız, `dependencies` *Build. Gradle* dosyanızın bölümüne aşağıdaki satırı ekleyin:

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Maven kullanıyorsanız, `<dependencies>` *Pok. xml* dosyanızın öğesinin içine aşağıdaki satırları ekleyin:

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Bir hub 'a bağlanma

Oluşturmak `HubConnection` `HubConnectionBuilder` için kullanılmalıdır. Hub URL 'SI ve günlük düzeyi bir bağlantı derlenirken yapılandırılabilir. Daha önce `HubConnectionBuilder` `build`herhangi bir yöntemi çağırarak gerekli seçenekleri yapılandırın. Bağlantısını ile `start`başlatın.

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>İstemciden çağrı merkezi yöntemleri

Bir hub metodunu `send` çağırma çağrısı. Hub yöntemi adını ve hub metodunda tanımlanan tüm bağımsız değişkenleri öğesine `send`geçirin.

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Azure SignalR hizmeti 'Ni *sunucusuz modda*kullanıyorsanız, bir istemciden hub yöntemleri çağrılamaz. Daha fazla bilgi için bkz. [ SignalR hizmet belgeleri](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Hub 'dan istemci yöntemlerini çağır

İstemci `hubConnection.on` üzerinde hub 'ın çağırakullanabileceği yöntemleri tanımlamak için kullanın. Bağlantıyı başlatmadan önce, oluşturma işleminden sonra yöntemleri tanımlayın.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Günlüğe kaydetme işlevi ekleme

Java istemcisi, günlük kaydı için dolayısıyla slf4j kitaplığını kullanır. [SLF4J](https://www.slf4j.org/) SignalR Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir. Aşağıdaki kod parçacığı, `java.util.logging` SignalR Java istemcisiyle nasıl kullanılacağını göstermektedir.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Bu, güvenle yoksayılabilir.

## <a name="android-development-notes"></a>Android geliştirme notları

SignalR İstemci özelliklerine Android SDK uyumlulukla ilgili olarak, hedef Android SDK sürümünüzü belirtirken aşağıdaki öğeleri göz önünde bulundurun:

* Java SignalR Istemcisi, Android API düzeyi 16 ve sonrasında çalışır.
* Azure [HIZMETI TLS 1,2 gerektirdiğinden SignalR ve](/azure/azure-signalr/signalr-overview) SHA-1 tabanlı şifre paketlerini desteklemediğinden Azure SignalR hizmeti üzerinden bağlanmak için Android API düzeyi 20 ve üzeri bir sürüm gerekir. Android, API düzeyi 20 ' de [SHA-256 (ve üzeri) şifre paketleri için destek ekledi](https://developer.android.com/reference/javax/net/ssl/SSLSocket) .

## <a name="configure-bearer-token-authentication"></a>Taşıyıcı belirteç kimlik doğrulamasını yapılandırma

SignalR Java Istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a "erişim belirteci fabrikası" sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz. [Rxjava](https://github.com/ReactiveX/RxJava) [tek\<dize>](https://reactivex.io/documentation/single.html)sağlamak için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın. [Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>Bilinen sınırlamalar

::: moniker range=">= aspnetcore-3.0"

* Yalnızca JSON Protokolü destekleniyor.
* Taşıma geri dönüşü ve sunucu gönderme olayları aktarımı desteklenmez.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Yalnızca JSON Protokolü destekleniyor.
* Yalnızca WebSockets taşıması desteklenir.
* Akış henüz desteklenmiyor.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [Java API'si başvurusu](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Azure SignalR hizmeti sunucusuz belgeler](/azure/azure-signalr/signalr-concept-serverless-development-config)
