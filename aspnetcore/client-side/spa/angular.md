---
title: Angular proje şablonunu ASP.NET Core ile kullanma
author: SteveSandersonMS
description: Angular ve angular CLı için ASP.NET Core tek sayfalı uygulama (SPA) proje şablonunu kullanmaya nasıl başlaleyeceğinizi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: spa/angular
ms.openlocfilehash: 1c91f1adadca8b339e4daf33207ca60008679810
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401785"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a>Angular proje şablonunu ASP.NET Core ile kullanma

Güncelleştirilmiş angular proje şablonu, zengin, istemci tarafı Kullanıcı arabirimi (UI) uygulamak için angular ve angular CLı kullanan ASP.NET Core uygulamalar için uygun bir başlangıç noktası sağlar.

Şablon, bir API arka ucu görevi gören bir ASP.NET Core projesi ve Kullanıcı arabirimi olarak davranacak angular CLı projesi oluşturma ile eşdeğerdir. Şablon, her iki proje türünü tek bir uygulama projesinde barındırmanın kolaylığını sunar. Sonuç olarak, uygulama projesi tek bir birim olarak oluşturulup yayımlanabilir.

## <a name="create-a-new-app"></a>Yeni uygulama oluşturma

ASP.NET Core 2,1 yüklüyse, angular proje şablonunu yüklemeye gerek yoktur.

Boş bir dizinde komutunu kullanarak komut isteminden yeni bir proje oluşturun `dotnet new angular` . Örneğin, aşağıdaki komutlar uygulamayı *Yeni bir uygulama* dizininde oluşturur ve bu dizine geçer:

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

Uygulamayı Visual Studio 'dan veya .NET Core CLI çalıştırın:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

Oluşturulan *. csproj* dosyasını açın ve uygulamayı buradan normal olarak çalıştırın.

Yapı işlemi ilk çalıştırmada NPM bağımlılıklarını geri yükler ve bu işlem birkaç dakika sürebilir. Sonraki derlemeler çok daha hızlıdır.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Bir ortam değişkenine sahip olduğunuzdan emin olun `ASPNETCORE_Environment` `Development` . Windows 'ta (PowerShell olmayan istemler ' de), öğesini çalıştırın `SET ASPNETCORE_Environment=Development` . Linux veya macOS üzerinde öğesini çalıştırın `export ASPNETCORE_Environment=Development` .

Uygulamanın doğru bir şekilde derlemelerin doğrulanması için [DotNet derlemesini](/dotnet/core/tools/dotnet-build) çalıştırın. İlk çalıştırmada, derleme işlemi NPM bağımlılıklarını geri yükler ve bu işlem birkaç dakika sürebilir. Sonraki derlemeler çok daha hızlıdır.

Uygulamayı başlatmak için [DotNet çalıştırmasını](/dotnet/core/tools/dotnet-run) çalıştırın. Aşağıdakine benzer bir ileti günlüğe kaydedilir:

```console
Now listening on: http://localhost:<port>
```

Bu URL 'yi bir tarayıcıda gezin.

> [!WARNING]
> Uygulama arka planda angular CLı sunucusunun bir örneğini başlatır. Aşağıdakine benzer bir ileti günlüğe kaydedilir: *ng Live Development Server, localhost: &lt; otherport &gt; , için bir tarayıcı açın, http://localhost:&lt diğer bir deyişle, diğer bağlantı &gt; / noktası*. Bu iletiyi yoksay &mdash; birleşik ASP.NET Core ve angular CLI uygulamasının URL 'si **değildir** .

---

Proje şablonu, bir ASP.NET Core uygulaması ve angular uygulaması oluşturur. ASP.NET Core uygulaması veri erişimi, yetkilendirme ve diğer sunucu tarafı sorunları için kullanılmak üzere tasarlanmıştır. *Clientapp* alt dizininde bulunan angular uygulamasının tüm Kullanıcı arabirimi sorunları için kullanılması amaçlanmıştır.

## <a name="add-pages-images-styles-modules-etc"></a>Sayfa, resim, stil, modül vb. ekleyin

*Clientapp* dizini standart ANGULAR CLI uygulaması içerir. Daha fazla bilgi için bkz. resmi [angular belgeleri](https://angular.io) .

Bu şablon tarafından oluşturulan angular uygulaması ve angular CLı 'nın kendisi (aracılığıyla) tarafından oluşturulan küçük farklılıklar vardır `ng new` ; ancak, uygulamanın özellikleri değiştirilmez. Şablon tarafından oluşturulan uygulama, [önyükleme](https://getbootstrap.com/)tabanlı bir düzen ve temel bir yönlendirme örneği içerir.

## <a name="run-ng-commands"></a>Çalıştır komutları

Bir komut isteminde *clientapp* alt dizinine geçin:

```console
cd ClientApp
```

`ng`Araç genel olarak yüklüyse, komutlarından birini çalıştırabilirsiniz. Örneğin, `ng lint` `ng test` diğer [angular CLI komutlarından](https://angular.io/cli)birini veya birini çalıştırabilirsiniz. `ng serve`ASP.NET Core uygulamanız uygulamanızın sunucu tarafı ve istemci tarafı bölümlerine sunma konusunda anlaştığından, ancak bu çalışmaya gerek yoktur. Dahili olarak, `ng serve` geliştirme sürecinde kullanır.

`ng`Yüklü bir araç yoksa, `npm run ng` bunun yerine çalıştırın. Örneğin, `npm run ng lint` veya çalıştırabilirsiniz `npm run ng test` .

## <a name="install-npm-packages"></a>NPM paketlerini yükler

Üçüncü taraf NPM paketlerini yüklemek için *clientapp* alt dizininde bir komut istemi kullanın. Örneğin:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Yayımla ve dağıt

Geliştirme aşamasında uygulama, geliştirici kolaylığı için iyileştirilmiş bir modda çalışır. Örneğin, JavaScript demeti kaynak eşlemeleri içerir (hata ayıklarken, özgün TypeScript kodunuzu görebilirsiniz). Uygulama TypeScript, HTML ve CSS dosya değişikliklerini diskte izler ve bu dosya değişikliğini gördüğünde otomatik olarak yeniden derlenir ve yeniden yükler.

Üretimde, uygulamanızın performans için iyileştirilmiş bir sürümünü sunar. Bu otomatik olarak gerçekleşecek şekilde yapılandırılmıştır. Yayımladığınızda, derleme yapılandırması, istemci tarafı kodunuzun küçültülmüş, önceden derlenmiş bir derlemesini yayar. Geliştirme derlemesinin aksine, üretim derlemesi sunucuya Node.js yüklenmesini gerektirmez (sunucu tarafı işlemesini (SSR) etkinleştirmediğiniz müddetçe).

Standart [ASP.NET Core barındırma ve dağıtım yöntemleri](xref:host-and-deploy/index)kullanabilirsiniz.

## <a name="run-ng-serve-independently"></a>Bağımsız olarak "ng hizmeti" Çalıştır

Proje, ASP.NET Core uygulaması geliştirme modunda başladığında arka planda kendi angular CLı sunucusu örneğini başlatacak şekilde yapılandırılmıştır. Ayrı bir sunucuyu el ile çalıştırmak zorunda olmadığınızdan bu kullanışlıdır.

Bu varsayılan Kurulumun bir dezavantajı vardır. C# kodunuzu her değiştirişinizde ve ASP.NET Core uygulamanızın yeniden başlatılması gerektiğinde, angular CLı sunucusu yeniden başlatılır. Yeniden başlatmak için yaklaşık 10 saniye gereklidir. Sık kullanılan C# kod düzenlemeleri oluşturuyorsanız ve angular CLı 'nın yeniden başlatılmasını beklemek istemiyorsanız, angular CLı sunucusunu ASP.NET Core işleminden bağımsız olarak dışarıdan çalıştırın. Bunu yapmak için:

1. Bir komut isteminde *clientapp* alt dizinine geçin ve ANGULAR CLI geliştirme sunucusunu başlatın:

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > `npm start` `ng serve` *package.jsüzerindeki* yapılandırmanın kullanılması IÇIN, angular CLI geliştirme sunucusunu başlatmak için kullanın. Angular CLı sunucusuna ek parametreler geçirmek için, bunları `scripts` dosyadaki *package.js* ilgili satıra ekleyin.

2. ASP.NET Core uygulamanızı, kendi kendine birini başlatmak yerine dış angular CLı örneğini kullanacak şekilde değiştirin. *Başlangıç* sınıfınıza, `spa.UseAngularCliServer` çağrıyı aşağıdaki ile değiştirin:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

ASP.NET Core uygulamanızı başlattığınızda, angular CLı sunucusunu başlatmayacaktır. Bunun yerine el ile başlattığınız örnek kullanılır. Bu, daha hızlı başlamasını ve yeniden başlatılmasını sağlar. Artık, istemci uygulamanızı her seferinde yeniden oluşturmak için angular CLı bekliyor.

### <a name="pass-data-from-net-code-into-typescript-code"></a>.NET kodundan verileri TypeScript koduna geçirme

SSR sırasında, ASP.NET Core uygulamanızdaki istek başına verileri angular uygulamanıza geçirmek isteyebilirsiniz. Örneğin, tanımlama bilgisi bilgilerini veya bir veritabanından okunan bir şeyi geçirebilirsiniz. Bunu yapmak için *Başlangıç* sınıfınızı düzenleyin. İçin geri çağırmada `UseSpaPrerendering` , aşağıdaki gibi bir değer ayarlayın `options.SupplyData` :

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

`SupplyData`Geri arama, isteğe bağlı olarak, JSON ile seri hale getirilebilir verileri (örneğin, dizeler, Boole değerleri veya sayılar) geçirmenize olanak sağlar. *Main. Server. TS* kodunuz bunu olarak alır `params.data` . Örneğin, yukarıdaki kod örneği `params.data.isHttpsRequest` geri çağırmada olduğu gibi bir Boole değeri geçirir `createServerRenderer` . Bunu uygulamanızın diğer bölümlerine, angular tarafından desteklenen herhangi bir yolla geçirebilirsiniz. Örneğin, bkz. *Main. Server. TS* `BASE_URL` değeri, Oluşturucusu onu almak için bildirildiği herhangi bir bileşene geçirir.

### <a name="drawbacks-of-ssr"></a>SSR 'nin dezavantajları

Tüm uygulamalar SSR 'den faydalanır. Birincil avantaj, performans algılanır. Yavaş bir ağ bağlantısı veya yavaş mobil cihazlarda uygulamanıza ulaşan ziyaretçiler, JavaScript paketlerini getirme veya ayrıştırma sırasında bile ilk Kullanıcı arabirimine hızlı bir şekilde bakın. Ancak, çoğu maça genellikle uygulamanın neredeyse anında göründüğü hızlı bilgisayarlarda hızlı, dahili şirket ağları üzerinde kullanılır.

Aynı zamanda SSR 'yi etkinleştirmenin önemli sakıncaları vardır. Geliştirme sürecinizi karmaşıklık altına ekler. Kodunuzun iki farklı ortamda çalışması gerekir: istemci tarafı ve sunucu tarafı (ASP.NET Core tarafından çağrılan Node.js bir ortamda). Göz önünde bulundurmanız gereken bazı şeyler aşağıda verilmiştir:

* SSR, üretim sunucularınızda Node.js yüklemesi gerektirir. Azure uygulama hizmetleri gibi bazı dağıtım senaryolarında bu durum otomatik olarak yapılır, ancak Azure Service Fabric gibi diğerleri için de değildir.
* `BuildServerSideRenderer`Yapı bayrağını etkinleştirmek, *node_modules* dizininizin yayımlamasına neden olur. Bu klasör, dağıtım süresini artıran 20000 + dosyalarını içerir.
* Kodunuzu Node.js bir ortamda çalıştırmak için, veya gibi tarayıcıya özgü JavaScript API 'Lerinin varlığına bağlı olamaz `window` `localStorage` . Kodunuz (veya başvuru yaptığınız bazı üçüncü taraf kitaplığı) Bu API 'Leri kullanmayı denediğinde SSR sırasında bir hata alırsınız. Örneğin, birçok yerde tarayıcıya özgü API 'Lere başvurduğundan jQuery kullanmayın. Hataları önlemek için SSR 'yi kullanmaktan veya tarayıcıya özgü API 'lerden ya da kitaplıklardan kaçınmalısınız. Bu tür API 'lere yapılan çağrıları SSR sırasında çağrıdıklarından emin olmak için, denetimleri içinde kaydırabilirsiniz. Örneğin, JavaScript veya TypeScript kodunda aşağıdakiler gibi bir denetim kullanın:

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authentication/identity/spa>
