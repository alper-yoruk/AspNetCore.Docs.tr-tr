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
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="b65c9-103">ASP.NET Core ile React proje şablonuna sahip</span><span class="sxs-lookup"><span data-stu-id="b65c9-103">Use the React project template with ASP.NET Core</span></span>

<span data-ttu-id="b65c9-104">Güncelleştirilmiş React proje şablonu, zengin, istemci tarafındaki kullanıcı arabirimi (UI) uygulamak için React ve [create-react-app](https://github.com/facebookincubator/create-react-app) (MKK) kurallarını kullanarak ASP.NET Core uygulamaları için kullanışlı bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="b65c9-104">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="b65c9-105">Şablon, hem bir API arka uç olarak hareket etmek için bir ASP.NET Core proje oluşturma eşdeğerdir, ve standart bir Cra React proje bir UI olarak hareket etmek, ancak inşa edilebilir ve tek bir birim olarak yayınlanan tek bir uygulama projesi hem barındırma kolaylığı ile.</span><span class="sxs-lookup"><span data-stu-id="b65c9-105">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

<span data-ttu-id="b65c9-106">React proje şablonu sunucu tarafı oluşturma (SSR) için değildir.</span><span class="sxs-lookup"><span data-stu-id="b65c9-106">The React project template isn't meant for server-side rendering (SSR).</span></span> <span data-ttu-id="b65c9-107">React ve Node.js ile SSR için [Next.js](https://github.com/zeit/next.js/) veya [Razzle'ı](https://github.com/jaredpalmer/razzle)düşünün.</span><span class="sxs-lookup"><span data-stu-id="b65c9-107">For SSR with React and Node.js, consider [Next.js](https://github.com/zeit/next.js/) or [Razzle](https://github.com/jaredpalmer/razzle).</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="b65c9-108">Yeni bir uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="b65c9-108">Create a new app</span></span>

<span data-ttu-id="b65c9-109">Core 2.1 yüklü ASP.NET varsa, React project şablonunu yüklemenize gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="b65c9-109">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

<span data-ttu-id="b65c9-110">Boş bir dizindeki komutu `dotnet new react` kullanarak komut isteminden yeni bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b65c9-110">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="b65c9-111">Örneğin, aşağıdaki komutlar uygulamayı yeni *uygulama dizinimde* oluşturur ve bu dizine geçer:</span><span class="sxs-lookup"><span data-stu-id="b65c9-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="b65c9-112">Uygulamayı Visual Studio veya .NET Core CLI'dan çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b65c9-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b65c9-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b65c9-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b65c9-114">Oluşturulan *.csproj* dosyasını açın ve uygulamayı buradan normal şekilde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b65c9-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="b65c9-115">Yapı işlemi, birkaç dakika sürebilen ilk çalıştırmadaki npm bağımlılıklarını geri yükler.</span><span class="sxs-lookup"><span data-stu-id="b65c9-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="b65c9-116">Sonraki yapılar çok daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b65c9-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b65c9-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b65c9-118">Değeri ile adlandırılan `ASPNETCORE_Environment` bir ortam `Development`değişkenine sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="b65c9-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="b65c9-119">Windows'da (PowerShell olmayan istemlerde), çalıştırın. `SET ASPNETCORE_Environment=Development`</span><span class="sxs-lookup"><span data-stu-id="b65c9-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="b65c9-120">Linux veya macOS'ta çalıştırın. `export ASPNETCORE_Environment=Development`</span><span class="sxs-lookup"><span data-stu-id="b65c9-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="b65c9-121">Uygulamanızın doğru yapılışlarını doğrulamak için [dotnet yapısını](/dotnet/core/tools/dotnet-build) çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b65c9-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="b65c9-122">İlk çalıştırmada, yapı işlemi birkaç dakika sürebilir npm bağımlılıkları, geri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="b65c9-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="b65c9-123">Sonraki yapılar çok daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="b65c9-124">Uygulamayı başlatmak için [dotnet çalıştırın.](/dotnet/core/tools/dotnet-run)</span><span class="sxs-lookup"><span data-stu-id="b65c9-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="b65c9-125">Proje şablonu bir ASP.NET Core uygulaması ve bir React uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b65c9-125">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="b65c9-126">ASP.NET Core uygulaması, veri erişimi, yetkilendirme ve diğer sunucu tarafı sorunları için kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-126">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="b65c9-127">*ClientApp* alt dizininde yer alan React uygulamasının tüm UI endişeleri için kullanılması amaçlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-127">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="b65c9-128">Sayfalar, resimler, stiller, modüller vb. ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b65c9-128">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="b65c9-129">*ClientApp* dizini standart bir MKK React uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-129">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="b65c9-130">Daha fazla bilgi için resmi [MKK belgelerine](https://create-react-app.dev/docs/getting-started/) bakın.</span><span class="sxs-lookup"><span data-stu-id="b65c9-130">See the official [CRA documentation](https://create-react-app.dev/docs/getting-started/) for more information.</span></span>

<span data-ttu-id="b65c9-131">Bu şablon tarafından oluşturulan React uygulaması ile MKK'nın kendisi tarafından oluşturulan uygulama arasında küçük farklar vardır; ancak, uygulamanın yetenekleri değişmez.</span><span class="sxs-lookup"><span data-stu-id="b65c9-131">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="b65c9-132">Şablon tarafından oluşturulan uygulama, [Bootstrap](https://getbootstrap.com/)tabanlı bir düzen ve temel bir yönlendirme örneği içerir.</span><span class="sxs-lookup"><span data-stu-id="b65c9-132">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="b65c9-133">npm paketleri yükleyin</span><span class="sxs-lookup"><span data-stu-id="b65c9-133">Install npm packages</span></span>

<span data-ttu-id="b65c9-134">Üçüncü taraf npm paketleri yüklemek için *ClientApp* alt dizininde bir komut istemi kullanın.</span><span class="sxs-lookup"><span data-stu-id="b65c9-134">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="b65c9-135">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b65c9-135">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="b65c9-136">Yayımla ve dağıt</span><span class="sxs-lookup"><span data-stu-id="b65c9-136">Publish and deploy</span></span>

<span data-ttu-id="b65c9-137">Geliştirme aşamasında, uygulama geliştirici kolaylığı için optimize edilmiş bir modda çalışır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-137">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="b65c9-138">Örneğin, JavaScript paketleri kaynak haritaları içerir (böylece hata ayıklama yaparken orijinal kaynak kodunuzu görebilirsiniz).</span><span class="sxs-lookup"><span data-stu-id="b65c9-138">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="b65c9-139">Uygulama, Diskteki JavaScript, HTML ve CSS dosya değişikliklerini izler ve bu dosyaların değiştiğini gördüğünde otomatik olarak yeniden derleyip yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="b65c9-139">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="b65c9-140">Üretimde, uygulamanızın performans için optimize edilmiş bir sürümüne hizmet edin.</span><span class="sxs-lookup"><span data-stu-id="b65c9-140">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="b65c9-141">Bu otomatik olarak gerçekleşmesi için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-141">This is configured to happen automatically.</span></span> <span data-ttu-id="b65c9-142">Yayımladığınızda, yapı yapılandırması istemci tarafı kodunuzu minified, transpiled bir yapı yayan.</span><span class="sxs-lookup"><span data-stu-id="b65c9-142">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="b65c9-143">Geliştirme yapısının aksine, üretim yapısı Sunucuya Node.js'nin yüklenmesini gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="b65c9-143">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="b65c9-144">Standart ASP.NET [Core barındırma ve dağıtım yöntemlerini](xref:host-and-deploy/index)kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b65c9-144">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="b65c9-145">MKK sunucusunu bağımsız olarak çalıştırın</span><span class="sxs-lookup"><span data-stu-id="b65c9-145">Run the CRA server independently</span></span>

<span data-ttu-id="b65c9-146">Proje, ASP.NET Core uygulaması geliştirme modunda başladığında arka planda MKK geliştirme sunucusunun kendi örneğini başlatmak üzere yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-146">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="b65c9-147">Bu, ayrı bir sunucuyu el ile çalıştırmak zorunda olmadığınız anlamına geldiği için kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-147">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="b65c9-148">Bu varsayılan kurulumun bir dezavantajı vardır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-148">There's a drawback to this default setup.</span></span> <span data-ttu-id="b65c9-149">C# kodunuzu her değiştirişinizde ve ASP.NET Core uygulamanızın yeniden başlatılması gerektiğinde, MKK sunucusu yeniden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-149">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="b65c9-150">Yeniden başlatmak için birkaç saniye gerekir.</span><span class="sxs-lookup"><span data-stu-id="b65c9-150">A few seconds are required to start back up.</span></span> <span data-ttu-id="b65c9-151">Sık sık C# kodu düzenlemeyapıyorsanız ve MKK sunucusunun yeniden başlatılmasını beklemek istemiyorsanız, MKK sunucusunu ASP.NET Core işleminden bağımsız olarak harici olarak çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b65c9-151">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="b65c9-152">Bunu yapmak için:</span><span class="sxs-lookup"><span data-stu-id="b65c9-152">To do so:</span></span>

1. <span data-ttu-id="b65c9-153">*ClientApp* alt dizinine aşağıdaki ayarı içeren bir *.env* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b65c9-153">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```

    <span data-ttu-id="b65c9-154">Bu, MKK sunucusunu dışarıdan başlatırken web tarayıcınızın açılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="b65c9-154">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="b65c9-155">Komut istemiyle *ClientApp* alt dizinine geçin ve MKK geliştirme sunucusunu başlatın:</span><span class="sxs-lookup"><span data-stu-id="b65c9-155">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="b65c9-156">ASP.NET Core uygulamanızı, kendi uygulamalarından birini başlatmak yerine harici MKK sunucu örneğini kullanacak şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="b65c9-156">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="b65c9-157">*Başlangıç* sınıfınızda, çağırmayı aşağıdakilerle değiştirin: `spa.UseReactDevelopmentServer`</span><span class="sxs-lookup"><span data-stu-id="b65c9-157">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="b65c9-158">ASP.NET Core uygulamanızı başlattığınızda, bir MKK sunucusu başlatmaz.</span><span class="sxs-lookup"><span data-stu-id="b65c9-158">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="b65c9-159">Bunun yerine el ile başlattığınız örnek kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-159">The instance you started manually is used instead.</span></span> <span data-ttu-id="b65c9-160">Bu, daha hızlı başlatılmasını ve yeniden başlatılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="b65c9-160">This enables it to start and restart faster.</span></span> <span data-ttu-id="b65c9-161">Artık React uygulamanızın her seferinde yeniden oluşturulmasını beklemiyor.</span><span class="sxs-lookup"><span data-stu-id="b65c9-161">It's no longer waiting for your React app to rebuild each time.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b65c9-162">"Sunucu tarafı oluşturma" bu şablonun desteklenen bir özelliği değildir.</span><span class="sxs-lookup"><span data-stu-id="b65c9-162">"Server-side rendering" is not a supported feature of this template.</span></span> <span data-ttu-id="b65c9-163">Bu şablonile amacımız "create-react-app" ile parite karşılamaktır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-163">Our goal with this template is to meet parity with "create-react-app".</span></span> <span data-ttu-id="b65c9-164">Bu nedenle, "create-react-app" projesinde (SSR gibi) yer almayan senaryolar ve özellikler desteklenmez ve kullanıcı için bir alıştırma olarak bırakılır.</span><span class="sxs-lookup"><span data-stu-id="b65c9-164">As such, scenarios and features not included in a "create-react-app" project (such as SSR) are not supported and are left as an exercise for the user.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b65c9-165">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b65c9-165">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
