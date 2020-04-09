---
title: ASP.NET Core ile Açısal proje şablonuna sahip
author: SteveSandersonMS
description: Açısal ve Açısal CLI için ASP.NET Çekirdek Tek Sayfa Uygulaması (SPA) proje şablonu ile nasıl başlayın öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
uid: spa/angular
ms.openlocfilehash: fee872ff237e14cbe491efed9b320809df4c5654
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657635"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a>ASP.NET Core ile Açısal proje şablonuna sahip

Güncelleştirilmiş Açısal proje şablonu, zengin, istemci tarafı kullanıcı arabirimi (UI) uygulamak için Açısal ve Açısal CLI'yi kullanan ASP.NET Core uygulamaları için kullanışlı bir başlangıç noktası sağlar.

Şablon, bir API arka uç ve bir Arama CLI projesi olarak hareket etmek için bir ASP.NET Core proje oluşturmaya eşdeğerdir. Şablon, her iki proje türünü tek bir uygulama projesinde barındırma kolaylığı sunar. Sonuç olarak, uygulama projesi tek bir birim olarak oluşturulabilir ve yayınlanabilir.

## <a name="create-a-new-app"></a>Yeni bir uygulama oluşturma

Core 2.1 yüklü ASP.NET varsa, Açısal proje şablonu yüklemenize gerek yoktur.

Boş bir dizindeki komutu `dotnet new angular` kullanarak komut isteminden yeni bir proje oluşturun. Örneğin, aşağıdaki komutlar uygulamayı yeni *uygulama dizinimde* oluşturur ve bu dizine geçer:

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

Uygulamayı Visual Studio veya .NET Core CLI'dan çalıştırın:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

Oluşturulan *.csproj* dosyasını açın ve uygulamayı buradan normal şekilde çalıştırın.

Yapı işlemi, birkaç dakika sürebilen ilk çalıştırmadaki npm bağımlılıklarını geri yükler. Sonraki yapılar çok daha hızlıdır.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

'nin değeri ile `ASPNETCORE_Environment` adlandırılan bir `Development`ortam değişkenine sahip olduğundan emin olun Windows'da (PowerShell olmayan istemlerde), çalıştırın. `SET ASPNETCORE_Environment=Development` Linux veya macOS'ta çalıştırın. `export ASPNETCORE_Environment=Development`

Uygulamanın doğru şekilde oluşturduğunu doğrulamak için [dotnet yapısını](/dotnet/core/tools/dotnet-build) çalıştırın. İlk çalıştırmada, yapı işlemi birkaç dakika sürebilir npm bağımlılıkları, geri yüklenir. Sonraki yapılar çok daha hızlıdır.

Uygulamayı başlatmak için [dotnet çalıştırın.](/dotnet/core/tools/dotnet-run) Aşağıdakilere benzer bir ileti günlüğe kaydedilir:

```console
Now listening on: http://localhost:<port>
```

Tarayıcıda bu URL'ye gidin.

> [!WARNING]
> Uygulama arka planda Açısal CLI sunucusunun bir örneğini başlatır. Aşağıdakine benzer bir ileti günlüğe kaydedilir: *NG Live Development&lt;Server&gt;localhost'u http://localhost:&ltdinliyor:&gt;otherport , bir tarayıcı aç ;otherport*. Core ve&mdash;Açısal CLI uygulamasının ASP.NET birleşimi için url **değildir** bu iletiyi yoksayın.

---

Proje şablonu bir ASP.NET Core uygulaması ve Açısal bir uygulama oluşturur. ASP.NET Core uygulaması, veri erişimi, yetkilendirme ve diğer sunucu tarafı sorunları için kullanılmak üzere tasarlanmıştır. *ClientApp* alt dizininde yer alan Açısal uygulamanın tüm UI endişeleri için kullanılması amaçlanmıştır.

## <a name="add-pages-images-styles-modules-etc"></a>Sayfalar, resimler, stiller, modüller vb. ekleyin.

*ClientApp* dizini standart bir Açısal CLI uygulaması içerir. Daha fazla bilgi için resmi [Açısal belgelere](https://angular.io) bakın.

Bu şablon tarafından oluşturulan Açısal uygulama ile Açısal CLI'nin kendisi tarafından oluşturulan `ng new`uygulama (üzerinden) arasında küçük farklar vardır; ancak, uygulamanın yetenekleri değişmez. Şablon tarafından oluşturulan uygulama, [Bootstrap](https://getbootstrap.com/)tabanlı bir düzen ve temel bir yönlendirme örneği içerir.

## <a name="run-ng-commands"></a>Ng komutlarını çalıştırın

Komut isteminde *ClientApp* alt dizinine geçin:

```console
cd ClientApp
```

`ng` Aracı genel olarak yüklüyorsanız, komutlarından herhangi birini çalıştırabilirsiniz. Örneğin, , , `ng lint` `ng test`veya diğer [Açısal CLI komutlarından](https://angular.io/cli)herhangi birini çalıştırabilirsiniz. Ancak çalıştırmaya `ng serve` gerek yoktur, çünkü ASP.NET Core uygulamanız uygulamanızın hem sunucu hem de istemci tarafı bölümlerine hizmet vermekle ilgilenir. Dahili olarak, `ng serve` geliştirme de kullanır.

`ng` Aracı yüklemediyseniz, bunun yerine çalıştırın. `npm run ng` Örneğin, çalıştırabilirsiniz `npm run ng lint` veya `npm run ng test`.

## <a name="install-npm-packages"></a>npm paketleri yükleyin

Üçüncü taraf npm paketleri yüklemek için *ClientApp* alt dizininde bir komut istemi kullanın. Örneğin:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Yayımla ve dağıt

Geliştirme aşamasında, uygulama geliştirici kolaylığı için optimize edilmiş bir modda çalışır. Örneğin, JavaScript paketleri kaynak haritaları içerir (böylece hata ayıklama yaparken orijinal TypeScript kodunuzu görebilirsiniz). Uygulama Diskteki TypeScript, HTML ve CSS dosya değişikliklerini izler ve bu dosyaların değiştiğini gördüğünde otomatik olarak yeniden derleyip yeniden yükler.

Üretimde, uygulamanızın performans için optimize edilmiş bir sürümüne hizmet edin. Bu otomatik olarak gerçekleşmesi için yapılandırılır. Yayımladığınızda, yapı yapılandırması, istemci tarafı kodunuzu derleyen minified, ahead-of-time (AoT) bir yapı yayır. Geliştirme yapısının aksine, üretim yapısı Sunucuya Node.js'nin yüklenmesini gerektirmez (sunucu tarafı oluşturmayı (SSR) etkinleştirmediyseniz).

Standart ASP.NET [Core barındırma ve dağıtım yöntemlerini](xref:host-and-deploy/index)kullanabilirsiniz.

## <a name="run-ng-serve-independently"></a>"ng serve"i bağımsız olarak çalıştırın

Proje, ASP.NET Core uygulaması geliştirme modunda başladığında arka planda açısal CLI sunucusunun kendi örneğini başlatmak üzere yapılandırılmıştır. Ayrı bir sunucuydan el ile çalıştırmanız gerekmedığından bu kullanışlıdır.

Bu varsayılan kurulumun bir dezavantajı vardır. C# kodunuzu her değiştirip ASP.NET Core uygulamanızın yeniden başlatılması gerektiğinde, Açısal CLI sunucusu yeniden başlatılır. Yeniden başlatmak için yaklaşık 10 saniye gerekir. Sık sık C# kodu düzenlemeyapıyorsanız ve Açısal CLI'nin yeniden başlatılmasını beklemek istemiyorsanız, Açısal CLI sunucusunu ASP.NET Core işleminden bağımsız olarak harici olarak çalıştırın. Bunu yapmak için:

1. Komut istemiyle *ClientApp* alt dizinine geçin ve Açısal CLI geliştirme sunucusunu başlatın:

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > Açısal CLI geliştirme sunucusubaşlatmak için `npm start` `ng serve`kullanın, değil , böylece *package.json* yapılandırma saygı duyulur. Açısal CLI sunucusuna ek parametreler ilerler sayılsın, bunları `scripts` *package.json* dosyanızdaki ilgili satıra ekleyin.

2. kendi uygulamalarından birini başlatmak yerine harici Açısal CLI örneğini kullanacak şekilde ASP.NET Core uygulamanızı değiştirin. *Başlangıç* sınıfınızda, çağırmayı aşağıdakilerle değiştirin: `spa.UseAngularCliServer`

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

ASP.NET Core uygulamanızı başlattığınızda, bir Açısal CLI sunucusu başlatmaz. Bunun yerine el ile başlattığınız örnek kullanılır. Bu, daha hızlı başlatılmasını ve yeniden başlatılmasını sağlar. Artık Açısal CLI'nin her seferinde istemci uygulamanızı yeniden oluşturmasını beklemiyor.

### <a name="pass-data-from-net-code-into-typescript-code"></a>.NET kodundan typeScript koduna veri aktarma

SSR sırasında, istek başına verileri ASP.NET Core uygulamanızdan Açısal uygulamanıza aktarmak isteyebilirsiniz. Örneğin, çerez bilgilerini veya veritabanından okunan bir şeyi aktarabilirsiniz. Bunu yapmak için *Başlangıç* sınıfınızı edin. Geri `UseSpaPrerendering`aramada, `options.SupplyData` aşağıdakiler gibi bir değer ayarlayın:

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

Geri `SupplyData` arama, isteğe göre rasgele, JSON serileştirilebilir verileri (örneğin, dizeleri, booleans veya sayılar) geçirmenizi sağlar. *Main.server.ts* kodunuz bunu `params.data`. Örneğin, önceki kod örneği `params.data.isHttpsRequest` `createServerRenderer` geri arama olarak bir boolean değeri geçer. Bunu, Açısal tarafından desteklenen herhangi bir şekilde uygulamanızın diğer bölümlerine iletebilirsiniz. Örneğin, *main.server.ts'nin* `BASE_URL` değeri, oluşturucusu onu almak üzere bildirilen herhangi bir bileşene nasıl geçtiğini görün.

### <a name="drawbacks-of-ssr"></a>SSR'nin Sakıncaları

Tüm uygulamalar SSR'den yararlanamaz. Birincil yararı algılanan performanstır. Yavaş bir ağ bağlantısı üzerinden veya yavaş mobil cihazlarda uygulamanıza ulaşan ziyaretçiler, JavaScript paketlerini almak veya ayrışdırmak biraz zaman alsa bile ilk UI'yi hızlı bir şekilde görür. Ancak, birçok SCA esas olarak hızlı, uygulama neredeyse anında görünür hızlı bilgisayarlarda dahili şirket ağları üzerinde kullanılır.

Aynı zamanda, SSR'yi etkinleştirmek için önemli dezavantajları da vardır. Geliştirme sürecinize karmaşıklık katar. Kodunuz iki farklı ortamda çalışmalıdır: istemci tarafı ve sunucu tarafı (ASP.NET Core'dan çağrılan bir Düğüm.js ortamında). Akılda tutulması gereken bazı şeyler şunlardır:

* SSR, üretim sunucularınızda bir Düğüm.js yüklemesi gerektirir. Bu, Azure Uygulama Hizmetleri gibi bazı dağıtım senaryoları için otomatik olarak geçerlidir, ancak Azure Hizmet Kumaşı gibi diğerleri için geçerli değildir.
* `BuildServerSideRenderer` Yapı bayrağını etkinleştirmek *node_modules* dizininizin yayımlanmasını sağlar. Bu klasör, dağıtım süresini artıran 20.000'den fazla dosya içerir.
* Kodunuzu Düğüm.js ortamında çalıştırmak için, tarayıcıya özgü JavaScript API'lerinin varlığına `window` güvenemez. `localStorage` Kodunuz (veya başvuruda bulunacağınız bazı üçüncü taraf kitaplık) bu API'leri kullanmaya çalışırsa, SSR sırasında bir hata alırsınız. Örneğin, birçok yerde tarayıcıya özgü API'lere atıfta bulunulduğu için jQuery'yi kullanmayın. Hataları önlemek için SSR'den kaçınmanız veya tarayıcıya özgü API'lerden veya kitaplıklardan kaçınmanız gerekir. SSR sırasında çağrılmadığından emin olmak için bu TÜR API'lere yapılan çağrıları denetimlerde sararsınız. Örneğin, JavaScript veya TypeScript kodunda aşağıdaki gibi bir denetim kullanın:

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authentication/identity/spa>
