---
title: ASP.NET Core SignalR Java istemcisi
author: mikaelm12
description: ASP.NET Core Java istemcisinin nasıl kullanılacağını öğrenin SignalR .
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- appsettings.json
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
uid: signalr/java-client
ms.openlocfilehash: 92941d21820de90eb2ae8fb76c21c588ed9f1ffb
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024762"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a>ASP.NET Core SignalR Java istemcisi

X [MIKAEL Mengistu](https://twitter.com/MikaelM_12) tarafından

Java istemcisi, SignalR Android uygulamaları dahil olmak üzere Java kodundan bir ASP.NET Core sunucusuna bağlanmasını sağlar. [JavaScript istemcisi](xref:signalr/javascript-client) ve [.NET istemcisi](xref:signalr/dotnet-client)gibi Java istemcisi, bir hub 'a gerçek zamanlı iletiler alıp göndermenizi sağlar. Java istemcisi ASP.NET Core 2,2 ve üzeri sürümlerde kullanılabilir.

Bu makalede başvurulan örnek Java konsol uygulaması SignalR Java istemcisini kullanır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="install-the-no-locsignalr-java-client-package"></a>SignalRJava istemci paketini yükler

*SignalR-1.0.0* jar dosyası istemcilerin hub 'lara bağlanmasına izin verir SignalR . En son JAR dosyası sürüm numarasını bulmak için bkz. [Maven arama sonuçları](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Gradle kullanıyorsanız, `dependencies` *Build. Gradle* dosyanızın bölümüne aşağıdaki satırı ekleyin:

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Maven kullanıyorsanız, `<dependencies>` *pom.xml* dosyanızın öğesinin içine aşağıdaki satırları ekleyin:

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Bir hub 'a bağlanma

Oluşturmak için kullanılmalıdır `HubConnection` `HubConnectionBuilder` . Hub URL 'SI ve günlük düzeyi bir bağlantı derlenirken yapılandırılabilir. Daha önce herhangi bir yöntemi çağırarak gerekli seçenekleri yapılandırın `HubConnectionBuilder` `build` . Bağlantısını ile başlatın `start` .

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>İstemciden çağrı merkezi yöntemleri

Bir `send` hub metodunu çağırma çağrısı. Hub yöntemi adını ve hub metodunda tanımlanan tüm bağımsız değişkenleri öğesine geçirin `send` .

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Hub yöntemlerinin bir istemciden çağrılması yalnızca Azure SignalR hizmeti *varsayılan* modda kullanılırken desteklenir. Daha fazla bilgi için bkz. [sık sorulan sorular (Azure-SignalR GitHub deposu)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="call-client-methods-from-hub"></a>Hub 'dan istemci yöntemlerini çağır

`hubConnection.on`İstemci üzerinde hub 'ın çağırakullanabileceği yöntemleri tanımlamak için kullanın. Bağlantıyı başlatmadan önce, oluşturma işleminden sonra yöntemleri tanımlayın.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Günlüğe kaydetme işlevi ekleme

SignalRJava istemcisi, günlük kaydı Için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır. Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir. Aşağıdaki kod parçacığı, Java istemcisiyle nasıl kullanılacağını göstermektedir `java.util.logging` SignalR .

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

İstemci özelliklerine Android SDK uyumlulukla ilgili olarak SignalR , hedef Android SDK sürümünüzü belirtirken aşağıdaki öğeleri göz önünde bulundurun:

* SignalRJava istemcisi, ANDROID API düzeyi 16 ve sonrasında çalışır.
* Azure hizmeti SignalR TLS 1,2 gerektirdiğinden ve SHA-1 tabanlı şifre paketlerini desteklemediğinden Azure hizmeti [üzerinden SignalR ](/azure/azure-signalr/signalr-overview) bağlanmak için Android API düzeyi 20 ve üzeri bir sürüm gerekir. Android, API düzeyi 20 ' de [SHA-256 (ve üzeri) şifre paketleri için destek ekledi](https://developer.android.com/reference/javax/net/ssl/SSLSocket) .

## <a name="configure-bearer-token-authentication"></a>Taşıyıcı belirteç kimlik doğrulamasını yapılandırma

SignalRJava istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)'a "erişim belirteci fabrikası" sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz. Bir [Rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın. [Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

::: moniker range=">= aspnetcore-5.0"

### <a name="passing-class-information-in-java"></a>Java 'da sınıf bilgilerini geçirme

`on` `invoke` Java istemcisindeki, veya yöntemlerini çağırırken, `stream` `HubConnection` Kullanıcılar `Type` `Class<?>` yöntemine geçirilen herhangi bir genel açıklama için bir nesne yerine bir nesne iletmelidir `Object` . Bir bir, `Type` belirtilen sınıf kullanılarak elde edilebilir `TypeReference` . Örneğin, adlı özel bir genel sınıf kullanarak `Foo<T>` Aşağıdaki kod şunu alır `Type` :

```java
Type fooType = new TypeReference<Foo<String>>() { }).getType();
```

Gibi temel olmayan türler veya gibi parametreli olmayan türler için `String` , yerleşik olarak kullanmanız yeterlidir `.class` .

Bir veya daha fazla nesne türüyle bu yöntemlerden birini çağırırken, yöntemi çağırırken genel türler sözdizimini kullanın. Örneğin, `on` `func` bir dize ve bir nesne olarak bağımsız değişken olarak alan adlı bir yöntem için bir işleyici kaydedilirken `Foo<String>` , bağımsız değişkenleri yazdırmak üzere bir eylem ayarlamak için aşağıdaki kodu kullanın:

```java
hubConnection.<String, Foo<String>>on("func", (param1, param2) ->{
    System.out.println(param1);
    System.out.println(param2);
}, String.class, fooType);
```

`Object.getClass`Java 'da silinme türü nedeniyle yöntemi ile karmaşık türlerle ilgili tüm bilgileri alamadığımız için bu kural gereklidir. Örneğin, `getClass` bir üzerinde çağırmak `ArrayList<String>` döndürmez `Class<ArrayList<String>>` , ancak `Class<ArrayList>` seri hale getirici tarafından gelen bir iletiyi doğru bir şekilde seri durumdan çıkarmak için yeterli bilgi vermemez. Aynı, özel nesneler için de geçerlidir.

::: moniker-end

## <a name="known-limitations"></a>Bilinen sınırlamalar

::: moniker range=">= aspnetcore-5.0"

* Taşıma geri dönüşü ve sunucu gönderme olayları aktarımı desteklenmez.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* Taşıma geri dönüşü ve sunucu gönderme olayları aktarımı desteklenmez.
* Yalnızca JSON Protokolü destekleniyor.

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
