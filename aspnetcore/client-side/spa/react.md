---
title: ASP.NET Core ile tepki verme proje şablonunu kullanın
author: SteveSandersonMS
description: Tepki verme ve oluşturma-yanıt verme için ASP.NET Core tek sayfalı uygulama (SPA) proje şablonunu kullanmaya nasıl başlacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
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
uid: spa/react
ms.openlocfilehash: 6c3539d96329489189f49a3af0c718791824be6b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054456"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>ASP.NET Core ile tepki verme proje şablonunu kullanın

Güncelleştirilmiş tepki verme projesi şablonu, zengin, istemci tarafı Kullanıcı arabirimi (UI) uygulamak için tepki verme ve [oluşturma-](https://github.com/facebookincubator/create-react-app) yanıt verme (CRA) kurallarını kullanan ASP.NET Core uygulamalar için uygun bir başlangıç noktası sağlar.

Şablon, API arka ucu olarak davranacak bir ASP.NET Core projesi ve bir kullanıcı arabirimi olarak görev yapacak standart CRA tepki veren bir proje oluşturmaya eşdeğerdir, ancak her ikisini de tek bir birim olarak oluşturulup yayımlanabilen tek bir uygulama projesinde barındırmanın rahatlığını sağlar.

Tepki verme projesi şablonu, sunucu tarafı işleme (SSR) için tasarlanmamıştır. Yanıt verme ve Node.js ile SSR için, [Next.js](https://github.com/zeit/next.js/) veya [kzzle](https://github.com/jaredpalmer/razzle)göz önünde bulundurun.

## <a name="create-a-new-app"></a>Yeni uygulama oluşturma

ASP.NET Core 2,1 yüklüyse, tepki verme projesi şablonunu yüklemeniz gerekmez.

Boş bir dizinde komutunu kullanarak komut isteminden yeni bir proje oluşturun `dotnet new react` . Örneğin, aşağıdaki komutlar uygulamayı *Yeni bir uygulama* dizininde oluşturur ve bu dizine geçer:

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

Uygulamayı Visual Studio 'dan veya .NET Core CLI çalıştırın:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Oluşturulan *. csproj* dosyasını açın ve uygulamayı buradan normal olarak çalıştırın.

Yapı işlemi ilk çalıştırmada NPM bağımlılıklarını geri yükler ve bu işlem birkaç dakika sürebilir. Sonraki derlemeler çok daha hızlıdır.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Değeri ile çağrılan bir ortam değişkenine sahip olduğunuzdan emin olun `ASPNETCORE_Environment` `Development` . Windows 'ta (PowerShell olmayan istemler ' de), öğesini çalıştırın `SET ASPNETCORE_Environment=Development` . Linux veya macOS üzerinde öğesini çalıştırın `export ASPNETCORE_Environment=Development` .

Uygulamanızın doğru derlemelerinizi doğrulamak için [DotNet derlemesini](/dotnet/core/tools/dotnet-build) çalıştırın. İlk çalıştırmada, derleme işlemi NPM bağımlılıklarını geri yükler ve bu işlem birkaç dakika sürebilir. Sonraki derlemeler çok daha hızlıdır.

Uygulamayı başlatmak için [DotNet çalıştırmasını](/dotnet/core/tools/dotnet-run) çalıştırın.

---

Proje şablonu, bir ASP.NET Core uygulaması ve bir tepki verme uygulaması oluşturur. ASP.NET Core uygulaması veri erişimi, yetkilendirme ve diğer sunucu tarafı sorunları için kullanılmak üzere tasarlanmıştır. *Clientapp* alt dizininde bulunan tepki verme uygulaması, tüm Kullanıcı arabirimi endişeleri için kullanılmak üzere tasarlanmıştır.

## <a name="add-pages-images-styles-modules-etc"></a>Sayfa, resim, stil, modül vb. ekleyin

*Clientapp* dizini standart bir CRA tepki uygulamadır. Daha fazla bilgi için resmi [CRA belgelerine](https://create-react-app.dev/docs/getting-started/) bakın.

Bu şablon tarafından oluşturulan ve CRA tarafından oluşturulan tepki verme uygulaması arasında hafif farklar vardır; Ancak, uygulamanın özellikleri değiştirilmez. Şablon tarafından oluşturulan uygulama, [önyükleme](https://getbootstrap.com/)tabanlı bir düzen ve temel bir yönlendirme örneği içerir.

## <a name="install-npm-packages"></a>NPM paketlerini yükler

Üçüncü taraf NPM paketlerini yüklemek için *clientapp* alt dizininde bir komut istemi kullanın. Örneğin:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Yayımla ve dağıt

Geliştirme aşamasında uygulama, geliştirici kolaylığı için iyileştirilmiş bir modda çalışır. Örneğin, JavaScript demeti kaynak eşlemeleri içerir (hata ayıklarken, özgün kaynak kodunuzu görebilirsiniz). Uygulama JavaScript, HTML ve CSS dosya değişikliklerini diskte izler ve bu dosya değişikliğini gördüğünde otomatik olarak yeniden derlenir ve yeniden yükler.

Üretimde, uygulamanızın performans için iyileştirilmiş bir sürümünü sunar. Bu otomatik olarak gerçekleşecek şekilde yapılandırılmıştır. Yayımladığınızda, derleme yapılandırması, istemci tarafı kodunuzun küçültülmüş, transpiled derlemesini yayar. Geliştirme derlemesinin aksine, üretim derlemesi sunucuya Node.js yüklenmesini gerektirmez.

Standart [ASP.NET Core barındırma ve dağıtım yöntemleri](xref:host-and-deploy/index)kullanabilirsiniz.

## <a name="run-the-cra-server-independently"></a>CRA sunucusunu bağımsız olarak çalıştırma

Proje, ASP.NET Core uygulama geliştirme modunda başladığında, CRA geliştirme sunucusunun kendi örneğini arka planda başlatacak şekilde yapılandırılmıştır. Bu kullanışlı bir yöntemdir çünkü bu, ayrı bir sunucuyu el ile çalıştırmak zorunda olmadığınız anlamına gelir.

Bu varsayılan Kurulumun bir dezavantajı vardır. C# kodunuzu her değiştirişinizde ve ASP.NET Core uygulamanızın yeniden başlatılması gerektiğinde CRA sunucusu yeniden başlatılır. Yeniden başlamak için birkaç saniye gerekir. Sık kullanılan C# kod düzenlemeleri yapıyorsanız ve CRA sunucusunun yeniden başlamasını beklemek istemiyorsanız, CRA sunucusunu ASP.NET Core işleminden bağımsız olarak dışarıdan çalıştırın. Bunun için:

1. *Clientapp* alt dizinine aşağıdaki ayarla bir *. env* dosyası ekleyin:

    ```
    BROWSER=none
    ```

    Bu, CRA sunucusunu dışarıdan başlatırken Web tarayıcınızın açılmasını engeller.

2. Bir komut isteminde *clientapp* alt dizinine geçin ve CRA geliştirme sunucusunu başlatın:

    ```console
    cd ClientApp
    npm start
    ```

3. ASP.NET Core uygulamanızı, kendi kendine birini başlatmak yerine dış CRA sunucu örneğini kullanacak şekilde değiştirin. *Başlangıç* sınıfınıza, `spa.UseReactDevelopmentServer` çağrıyı aşağıdaki ile değiştirin:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

ASP.NET Core uygulamanızı başlattığınızda, bir CRA sunucusu başlatılmaz. Bunun yerine el ile başlattığınız örnek kullanılır. Bu, daha hızlı başlamasını ve yeniden başlatılmasını sağlar. Artık, yanıt verme uygulamanızın her seferinde yeniden derlenmesini beklemiyordu.

> [!IMPORTANT]
> "Sunucu tarafı işleme", bu şablonun desteklenen bir özelliği değildir. Bu şablonla olan amamız "oluşturma-tepki-uygulama" ile eşlik sağlamaktır. Bu nedenle, "oluşturma-yanıt verme uygulaması" projesine (SSR gibi) dahil olmayan senaryolar ve özellikler desteklenmez ve Kullanıcı için bir alıştırma olarak kalır.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/authentication/identity/spa>
