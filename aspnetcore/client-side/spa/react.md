---
title: ASP.NET Core ile React proje şablonuna sahip
author: SteveSandersonMS
description: React ve create-react-app için ASP.NET Core Single Page Application (SPA) proje şablonu ile nasıl başlayın öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: spa/react
ms.openlocfilehash: 9703a62eb7f779974382fe0fb01702d9fcd37d64
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664964"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>ASP.NET Core ile React proje şablonuna sahip

Güncelleştirilmiş React proje şablonu, zengin, istemci tarafındaki kullanıcı arabirimi (UI) uygulamak için React ve [create-react-app](https://github.com/facebookincubator/create-react-app) (MKK) kurallarını kullanarak ASP.NET Core uygulamaları için kullanışlı bir başlangıç noktası sağlar.

Şablon, hem bir API arka uç olarak hareket etmek için bir ASP.NET Core proje oluşturma eşdeğerdir, ve standart bir Cra React proje bir UI olarak hareket etmek, ancak inşa edilebilir ve tek bir birim olarak yayınlanan tek bir uygulama projesi hem barındırma kolaylığı ile.

React proje şablonu sunucu tarafı oluşturma (SSR) için değildir. React ve Node.js ile SSR için [Next.js](https://github.com/zeit/next.js/) veya [Razzle'ı](https://github.com/jaredpalmer/razzle)düşünün.

## <a name="create-a-new-app"></a>Yeni bir uygulama oluşturma

Core 2.1 yüklü ASP.NET varsa, React project şablonunu yüklemenize gerek yoktur.

Boş bir dizindeki komutu `dotnet new react` kullanarak komut isteminden yeni bir proje oluşturun. Örneğin, aşağıdaki komutlar uygulamayı yeni *uygulama dizinimde* oluşturur ve bu dizine geçer:

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

Uygulamayı Visual Studio veya .NET Core CLI'dan çalıştırın:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Oluşturulan *.csproj* dosyasını açın ve uygulamayı buradan normal şekilde çalıştırın.

Yapı işlemi, birkaç dakika sürebilen ilk çalıştırmadaki npm bağımlılıklarını geri yükler. Sonraki yapılar çok daha hızlıdır.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Değeri ile adlandırılan `ASPNETCORE_Environment` bir ortam `Development`değişkenine sahip olduğundan emin olun. Windows'da (PowerShell olmayan istemlerde), çalıştırın. `SET ASPNETCORE_Environment=Development` Linux veya macOS'ta çalıştırın. `export ASPNETCORE_Environment=Development`

Uygulamanızın doğru yapılışlarını doğrulamak için [dotnet yapısını](/dotnet/core/tools/dotnet-build) çalıştırın. İlk çalıştırmada, yapı işlemi birkaç dakika sürebilir npm bağımlılıkları, geri yüklenir. Sonraki yapılar çok daha hızlıdır.

Uygulamayı başlatmak için [dotnet çalıştırın.](/dotnet/core/tools/dotnet-run)

---

Proje şablonu bir ASP.NET Core uygulaması ve bir React uygulaması oluşturur. ASP.NET Core uygulaması, veri erişimi, yetkilendirme ve diğer sunucu tarafı sorunları için kullanılmak üzere tasarlanmıştır. *ClientApp* alt dizininde yer alan React uygulamasının tüm UI endişeleri için kullanılması amaçlanmıştır.

## <a name="add-pages-images-styles-modules-etc"></a>Sayfalar, resimler, stiller, modüller vb. ekleyin.

*ClientApp* dizini standart bir MKK React uygulamasıdır. Daha fazla bilgi için resmi [MKK belgelerine](https://create-react-app.dev/docs/getting-started/) bakın.

Bu şablon tarafından oluşturulan React uygulaması ile MKK'nın kendisi tarafından oluşturulan uygulama arasında küçük farklar vardır; ancak, uygulamanın yetenekleri değişmez. Şablon tarafından oluşturulan uygulama, [Bootstrap](https://getbootstrap.com/)tabanlı bir düzen ve temel bir yönlendirme örneği içerir.

## <a name="install-npm-packages"></a>npm paketleri yükleyin

Üçüncü taraf npm paketleri yüklemek için *ClientApp* alt dizininde bir komut istemi kullanın. Örneğin:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Yayımla ve dağıt

Geliştirme aşamasında, uygulama geliştirici kolaylığı için optimize edilmiş bir modda çalışır. Örneğin, JavaScript paketleri kaynak haritaları içerir (böylece hata ayıklama yaparken orijinal kaynak kodunuzu görebilirsiniz). Uygulama, Diskteki JavaScript, HTML ve CSS dosya değişikliklerini izler ve bu dosyaların değiştiğini gördüğünde otomatik olarak yeniden derleyip yeniden yükler.

Üretimde, uygulamanızın performans için optimize edilmiş bir sürümüne hizmet edin. Bu otomatik olarak gerçekleşmesi için yapılandırılır. Yayımladığınızda, yapı yapılandırması istemci tarafı kodunuzu minified, transpiled bir yapı yayan. Geliştirme yapısının aksine, üretim yapısı Sunucuya Node.js'nin yüklenmesini gerektirmez.

Standart ASP.NET [Core barındırma ve dağıtım yöntemlerini](xref:host-and-deploy/index)kullanabilirsiniz.

## <a name="run-the-cra-server-independently"></a>MKK sunucusunu bağımsız olarak çalıştırın

Proje, ASP.NET Core uygulaması geliştirme modunda başladığında arka planda MKK geliştirme sunucusunun kendi örneğini başlatmak üzere yapılandırılmıştır. Bu, ayrı bir sunucuyu el ile çalıştırmak zorunda olmadığınız anlamına geldiği için kullanışlıdır.

Bu varsayılan kurulumun bir dezavantajı vardır. C# kodunuzu her değiştirişinizde ve ASP.NET Core uygulamanızın yeniden başlatılması gerektiğinde, MKK sunucusu yeniden başlatılır. Yeniden başlatmak için birkaç saniye gerekir. Sık sık C# kodu düzenlemeyapıyorsanız ve MKK sunucusunun yeniden başlatılmasını beklemek istemiyorsanız, MKK sunucusunu ASP.NET Core işleminden bağımsız olarak harici olarak çalıştırın. Bunu yapmak için:

1. *ClientApp* alt dizinine aşağıdaki ayarı içeren bir *.env* dosyası ekleyin:

    ```
    BROWSER=none
    ```

    Bu, MKK sunucusunu dışarıdan başlatırken web tarayıcınızın açılmasını engeller.

2. Komut istemiyle *ClientApp* alt dizinine geçin ve MKK geliştirme sunucusunu başlatın:

    ```console
    cd ClientApp
    npm start
    ```

3. ASP.NET Core uygulamanızı, kendi uygulamalarından birini başlatmak yerine harici MKK sunucu örneğini kullanacak şekilde değiştirin. *Başlangıç* sınıfınızda, çağırmayı aşağıdakilerle değiştirin: `spa.UseReactDevelopmentServer`

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

ASP.NET Core uygulamanızı başlattığınızda, bir MKK sunucusu başlatmaz. Bunun yerine el ile başlattığınız örnek kullanılır. Bu, daha hızlı başlatılmasını ve yeniden başlatılmasını sağlar. Artık React uygulamanızın her seferinde yeniden oluşturulmasını beklemiyor.

> [!IMPORTANT]
> "Sunucu tarafı oluşturma" bu şablonun desteklenen bir özelliği değildir. Bu şablonile amacımız "create-react-app" ile parite karşılamaktır. Bu nedenle, "create-react-app" projesinde (SSR gibi) yer almayan senaryolar ve özellikler desteklenmez ve kullanıcı için bir alıştırma olarak bırakılır.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authentication/identity/spa>
