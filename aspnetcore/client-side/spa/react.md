---
title: ASP.NET Core ile tepki verme proje şablonunu kullanın
author: SteveSandersonMS
description: Tepki verme ve oluşturma-yanıt verme için ASP.NET Core tek sayfalı uygulama (SPA) proje şablonunu kullanmaya nasıl başlacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: spa/react
ms.openlocfilehash: d5185a80895423bdd60309509ab4d1dad4dad12f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401733"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="2f60b-103">ASP.NET Core ile tepki verme proje şablonunu kullanın</span><span class="sxs-lookup"><span data-stu-id="2f60b-103">Use the React project template with ASP.NET Core</span></span>

<span data-ttu-id="2f60b-104">Güncelleştirilmiş tepki verme projesi şablonu, zengin, istemci tarafı Kullanıcı arabirimi (UI) uygulamak için tepki verme ve [oluşturma-](https://github.com/facebookincubator/create-react-app) yanıt verme (CRA) kurallarını kullanan ASP.NET Core uygulamalar için uygun bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="2f60b-104">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="2f60b-105">Şablon, API arka ucu olarak davranacak bir ASP.NET Core projesi ve bir kullanıcı arabirimi olarak görev yapacak standart CRA tepki veren bir proje oluşturmaya eşdeğerdir, ancak her ikisini de tek bir birim olarak oluşturulup yayımlanabilen tek bir uygulama projesinde barındırmanın rahatlığını sağlar.</span><span class="sxs-lookup"><span data-stu-id="2f60b-105">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

<span data-ttu-id="2f60b-106">Tepki verme projesi şablonu, sunucu tarafı işleme (SSR) için tasarlanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-106">The React project template isn't meant for server-side rendering (SSR).</span></span> <span data-ttu-id="2f60b-107">Yanıt verme ve Node.js ile SSR için, [Next.js](https://github.com/zeit/next.js/) veya [kzzle](https://github.com/jaredpalmer/razzle)göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="2f60b-107">For SSR with React and Node.js, consider [Next.js](https://github.com/zeit/next.js/) or [Razzle](https://github.com/jaredpalmer/razzle).</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="2f60b-108">Yeni uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="2f60b-108">Create a new app</span></span>

<span data-ttu-id="2f60b-109">ASP.NET Core 2,1 yüklüyse, tepki verme projesi şablonunu yüklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="2f60b-109">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

<span data-ttu-id="2f60b-110">Boş bir dizinde komutunu kullanarak komut isteminden yeni bir proje oluşturun `dotnet new react` .</span><span class="sxs-lookup"><span data-stu-id="2f60b-110">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="2f60b-111">Örneğin, aşağıdaki komutlar uygulamayı *Yeni bir uygulama* dizininde oluşturur ve bu dizine geçer:</span><span class="sxs-lookup"><span data-stu-id="2f60b-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="2f60b-112">Uygulamayı Visual Studio 'dan veya .NET Core CLI çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2f60b-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f60b-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f60b-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f60b-114">Oluşturulan *. csproj* dosyasını açın ve uygulamayı buradan normal olarak çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2f60b-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="2f60b-115">Yapı işlemi ilk çalıştırmada NPM bağımlılıklarını geri yükler ve bu işlem birkaç dakika sürebilir.</span><span class="sxs-lookup"><span data-stu-id="2f60b-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="2f60b-116">Sonraki derlemeler çok daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2f60b-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2f60b-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2f60b-118">Değeri ile çağrılan bir ortam değişkenine sahip olduğunuzdan emin olun `ASPNETCORE_Environment` `Development` .</span><span class="sxs-lookup"><span data-stu-id="2f60b-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="2f60b-119">Windows 'ta (PowerShell olmayan istemler ' de), öğesini çalıştırın `SET ASPNETCORE_Environment=Development` .</span><span class="sxs-lookup"><span data-stu-id="2f60b-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="2f60b-120">Linux veya macOS üzerinde öğesini çalıştırın `export ASPNETCORE_Environment=Development` .</span><span class="sxs-lookup"><span data-stu-id="2f60b-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="2f60b-121">Uygulamanızın doğru derlemelerinizi doğrulamak için [DotNet derlemesini](/dotnet/core/tools/dotnet-build) çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2f60b-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="2f60b-122">İlk çalıştırmada, derleme işlemi NPM bağımlılıklarını geri yükler ve bu işlem birkaç dakika sürebilir.</span><span class="sxs-lookup"><span data-stu-id="2f60b-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="2f60b-123">Sonraki derlemeler çok daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="2f60b-124">Uygulamayı başlatmak için [DotNet çalıştırmasını](/dotnet/core/tools/dotnet-run) çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2f60b-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="2f60b-125">Proje şablonu, bir ASP.NET Core uygulaması ve bir tepki verme uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2f60b-125">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="2f60b-126">ASP.NET Core uygulaması veri erişimi, yetkilendirme ve diğer sunucu tarafı sorunları için kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-126">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="2f60b-127">*Clientapp* alt dizininde bulunan tepki verme uygulaması, tüm Kullanıcı arabirimi endişeleri için kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-127">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="2f60b-128">Sayfa, resim, stil, modül vb. ekleyin</span><span class="sxs-lookup"><span data-stu-id="2f60b-128">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="2f60b-129">*Clientapp* dizini standart bir CRA tepki uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-129">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="2f60b-130">Daha fazla bilgi için resmi [CRA belgelerine](https://create-react-app.dev/docs/getting-started/) bakın.</span><span class="sxs-lookup"><span data-stu-id="2f60b-130">See the official [CRA documentation](https://create-react-app.dev/docs/getting-started/) for more information.</span></span>

<span data-ttu-id="2f60b-131">Bu şablon tarafından oluşturulan ve CRA tarafından oluşturulan tepki verme uygulaması arasında hafif farklar vardır; Ancak, uygulamanın özellikleri değiştirilmez.</span><span class="sxs-lookup"><span data-stu-id="2f60b-131">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="2f60b-132">Şablon tarafından oluşturulan uygulama, [önyükleme](https://getbootstrap.com/)tabanlı bir düzen ve temel bir yönlendirme örneği içerir.</span><span class="sxs-lookup"><span data-stu-id="2f60b-132">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="2f60b-133">NPM paketlerini yükler</span><span class="sxs-lookup"><span data-stu-id="2f60b-133">Install npm packages</span></span>

<span data-ttu-id="2f60b-134">Üçüncü taraf NPM paketlerini yüklemek için *clientapp* alt dizininde bir komut istemi kullanın.</span><span class="sxs-lookup"><span data-stu-id="2f60b-134">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="2f60b-135">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2f60b-135">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="2f60b-136">Yayımla ve dağıt</span><span class="sxs-lookup"><span data-stu-id="2f60b-136">Publish and deploy</span></span>

<span data-ttu-id="2f60b-137">Geliştirme aşamasında uygulama, geliştirici kolaylığı için iyileştirilmiş bir modda çalışır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-137">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="2f60b-138">Örneğin, JavaScript demeti kaynak eşlemeleri içerir (hata ayıklarken, özgün kaynak kodunuzu görebilirsiniz).</span><span class="sxs-lookup"><span data-stu-id="2f60b-138">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="2f60b-139">Uygulama JavaScript, HTML ve CSS dosya değişikliklerini diskte izler ve bu dosya değişikliğini gördüğünde otomatik olarak yeniden derlenir ve yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="2f60b-139">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="2f60b-140">Üretimde, uygulamanızın performans için iyileştirilmiş bir sürümünü sunar.</span><span class="sxs-lookup"><span data-stu-id="2f60b-140">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="2f60b-141">Bu otomatik olarak gerçekleşecek şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-141">This is configured to happen automatically.</span></span> <span data-ttu-id="2f60b-142">Yayımladığınızda, derleme yapılandırması, istemci tarafı kodunuzun küçültülmüş, transpiled derlemesini yayar.</span><span class="sxs-lookup"><span data-stu-id="2f60b-142">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="2f60b-143">Geliştirme derlemesinin aksine, üretim derlemesi sunucuya Node.js yüklenmesini gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="2f60b-143">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="2f60b-144">Standart [ASP.NET Core barındırma ve dağıtım yöntemleri](xref:host-and-deploy/index)kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2f60b-144">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="2f60b-145">CRA sunucusunu bağımsız olarak çalıştırma</span><span class="sxs-lookup"><span data-stu-id="2f60b-145">Run the CRA server independently</span></span>

<span data-ttu-id="2f60b-146">Proje, ASP.NET Core uygulama geliştirme modunda başladığında, CRA geliştirme sunucusunun kendi örneğini arka planda başlatacak şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-146">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="2f60b-147">Bu kullanışlı bir yöntemdir çünkü bu, ayrı bir sunucuyu el ile çalıştırmak zorunda olmadığınız anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="2f60b-147">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="2f60b-148">Bu varsayılan Kurulumun bir dezavantajı vardır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-148">There's a drawback to this default setup.</span></span> <span data-ttu-id="2f60b-149">C# kodunuzu her değiştirişinizde ve ASP.NET Core uygulamanızın yeniden başlatılması gerektiğinde CRA sunucusu yeniden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-149">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="2f60b-150">Yeniden başlamak için birkaç saniye gerekir.</span><span class="sxs-lookup"><span data-stu-id="2f60b-150">A few seconds are required to start back up.</span></span> <span data-ttu-id="2f60b-151">Sık kullanılan C# kod düzenlemeleri yapıyorsanız ve CRA sunucusunun yeniden başlamasını beklemek istemiyorsanız, CRA sunucusunu ASP.NET Core işleminden bağımsız olarak dışarıdan çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2f60b-151">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="2f60b-152">Bunu yapmak için:</span><span class="sxs-lookup"><span data-stu-id="2f60b-152">To do so:</span></span>

1. <span data-ttu-id="2f60b-153">*Clientapp* alt dizinine aşağıdaki ayarla bir *. env* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2f60b-153">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```

    <span data-ttu-id="2f60b-154">Bu, CRA sunucusunu dışarıdan başlatırken Web tarayıcınızın açılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="2f60b-154">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="2f60b-155">Bir komut isteminde *clientapp* alt dizinine geçin ve CRA geliştirme sunucusunu başlatın:</span><span class="sxs-lookup"><span data-stu-id="2f60b-155">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="2f60b-156">ASP.NET Core uygulamanızı, kendi kendine birini başlatmak yerine dış CRA sunucu örneğini kullanacak şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2f60b-156">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="2f60b-157">*Başlangıç* sınıfınıza, `spa.UseReactDevelopmentServer` çağrıyı aşağıdaki ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2f60b-157">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="2f60b-158">ASP.NET Core uygulamanızı başlattığınızda, bir CRA sunucusu başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="2f60b-158">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="2f60b-159">Bunun yerine el ile başlattığınız örnek kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-159">The instance you started manually is used instead.</span></span> <span data-ttu-id="2f60b-160">Bu, daha hızlı başlamasını ve yeniden başlatılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="2f60b-160">This enables it to start and restart faster.</span></span> <span data-ttu-id="2f60b-161">Artık, yanıt verme uygulamanızın her seferinde yeniden derlenmesini beklemiyordu.</span><span class="sxs-lookup"><span data-stu-id="2f60b-161">It's no longer waiting for your React app to rebuild each time.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2f60b-162">"Sunucu tarafı işleme", bu şablonun desteklenen bir özelliği değildir.</span><span class="sxs-lookup"><span data-stu-id="2f60b-162">"Server-side rendering" is not a supported feature of this template.</span></span> <span data-ttu-id="2f60b-163">Bu şablonla olan amamız "oluşturma-tepki-uygulama" ile eşlik sağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-163">Our goal with this template is to meet parity with "create-react-app".</span></span> <span data-ttu-id="2f60b-164">Bu nedenle, "oluşturma-yanıt verme uygulaması" projesine (SSR gibi) dahil olmayan senaryolar ve özellikler desteklenmez ve Kullanıcı için bir alıştırma olarak kalır.</span><span class="sxs-lookup"><span data-stu-id="2f60b-164">As such, scenarios and features not included in a "create-react-app" project (such as SSR) are not supported and are left as an exercise for the user.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2f60b-165">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2f60b-165">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
