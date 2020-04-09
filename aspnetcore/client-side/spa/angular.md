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
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="1e8e1-103">ASP.NET Core ile Açısal proje şablonuna sahip</span><span class="sxs-lookup"><span data-stu-id="1e8e1-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="1e8e1-104">Güncelleştirilmiş Açısal proje şablonu, zengin, istemci tarafı kullanıcı arabirimi (UI) uygulamak için Açısal ve Açısal CLI'yi kullanan ASP.NET Core uygulamaları için kullanışlı bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="1e8e1-105">Şablon, bir API arka uç ve bir Arama CLI projesi olarak hareket etmek için bir ASP.NET Core proje oluşturmaya eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="1e8e1-106">Şablon, her iki proje türünü tek bir uygulama projesinde barındırma kolaylığı sunar.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="1e8e1-107">Sonuç olarak, uygulama projesi tek bir birim olarak oluşturulabilir ve yayınlanabilir.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="1e8e1-108">Yeni bir uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="1e8e1-108">Create a new app</span></span>

<span data-ttu-id="1e8e1-109">Core 2.1 yüklü ASP.NET varsa, Açısal proje şablonu yüklemenize gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="1e8e1-110">Boş bir dizindeki komutu `dotnet new angular` kullanarak komut isteminden yeni bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="1e8e1-111">Örneğin, aşağıdaki komutlar uygulamayı yeni *uygulama dizinimde* oluşturur ve bu dizine geçer:</span><span class="sxs-lookup"><span data-stu-id="1e8e1-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="1e8e1-112">Uygulamayı Visual Studio veya .NET Core CLI'dan çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1e8e1-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e8e1-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e8e1-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="1e8e1-114">Oluşturulan *.csproj* dosyasını açın ve uygulamayı buradan normal şekilde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="1e8e1-115">Yapı işlemi, birkaç dakika sürebilen ilk çalıştırmadaki npm bağımlılıklarını geri yükler.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="1e8e1-116">Sonraki yapılar çok daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1e8e1-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1e8e1-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1e8e1-118">'nin değeri ile `ASPNETCORE_Environment` adlandırılan bir `Development`ortam değişkenine sahip olduğundan emin olun</span><span class="sxs-lookup"><span data-stu-id="1e8e1-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="1e8e1-119">Windows'da (PowerShell olmayan istemlerde), çalıştırın. `SET ASPNETCORE_Environment=Development`</span><span class="sxs-lookup"><span data-stu-id="1e8e1-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="1e8e1-120">Linux veya macOS'ta çalıştırın. `export ASPNETCORE_Environment=Development`</span><span class="sxs-lookup"><span data-stu-id="1e8e1-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="1e8e1-121">Uygulamanın doğru şekilde oluşturduğunu doğrulamak için [dotnet yapısını](/dotnet/core/tools/dotnet-build) çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="1e8e1-122">İlk çalıştırmada, yapı işlemi birkaç dakika sürebilir npm bağımlılıkları, geri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="1e8e1-123">Sonraki yapılar çok daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="1e8e1-124">Uygulamayı başlatmak için [dotnet çalıştırın.](/dotnet/core/tools/dotnet-run)</span><span class="sxs-lookup"><span data-stu-id="1e8e1-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="1e8e1-125">Aşağıdakilere benzer bir ileti günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="1e8e1-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="1e8e1-126">Tarayıcıda bu URL'ye gidin.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-126">Navigate to this URL in a browser.</span></span>

> [!WARNING]
> <span data-ttu-id="1e8e1-127">Uygulama arka planda Açısal CLI sunucusunun bir örneğini başlatır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="1e8e1-128">Aşağıdakine benzer bir ileti günlüğe kaydedilir: *NG Live Development&lt;Server&gt;localhost'u http://localhost:&ltdinliyor:&gt;otherport , bir tarayıcı aç ;otherport*.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open a browser to http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="1e8e1-129">Core ve&mdash;Açısal CLI uygulamasının ASP.NET birleşimi için url **değildir** bu iletiyi yoksayın.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="1e8e1-130">Proje şablonu bir ASP.NET Core uygulaması ve Açısal bir uygulama oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="1e8e1-131">ASP.NET Core uygulaması, veri erişimi, yetkilendirme ve diğer sunucu tarafı sorunları için kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="1e8e1-132">*ClientApp* alt dizininde yer alan Açısal uygulamanın tüm UI endişeleri için kullanılması amaçlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="1e8e1-133">Sayfalar, resimler, stiller, modüller vb. ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="1e8e1-134">*ClientApp* dizini standart bir Açısal CLI uygulaması içerir.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="1e8e1-135">Daha fazla bilgi için resmi [Açısal belgelere](https://angular.io) bakın.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-135">See the official [Angular documentation](https://angular.io) for more information.</span></span>

<span data-ttu-id="1e8e1-136">Bu şablon tarafından oluşturulan Açısal uygulama ile Açısal CLI'nin kendisi tarafından oluşturulan `ng new`uygulama (üzerinden) arasında küçük farklar vardır; ancak, uygulamanın yetenekleri değişmez.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="1e8e1-137">Şablon tarafından oluşturulan uygulama, [Bootstrap](https://getbootstrap.com/)tabanlı bir düzen ve temel bir yönlendirme örneği içerir.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="1e8e1-138">Ng komutlarını çalıştırın</span><span class="sxs-lookup"><span data-stu-id="1e8e1-138">Run ng commands</span></span>

<span data-ttu-id="1e8e1-139">Komut isteminde *ClientApp* alt dizinine geçin:</span><span class="sxs-lookup"><span data-stu-id="1e8e1-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="1e8e1-140">`ng` Aracı genel olarak yüklüyorsanız, komutlarından herhangi birini çalıştırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="1e8e1-141">Örneğin, , , `ng lint` `ng test`veya diğer [Açısal CLI komutlarından](https://angular.io/cli)herhangi birini çalıştırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://angular.io/cli).</span></span> <span data-ttu-id="1e8e1-142">Ancak çalıştırmaya `ng serve` gerek yoktur, çünkü ASP.NET Core uygulamanız uygulamanızın hem sunucu hem de istemci tarafı bölümlerine hizmet vermekle ilgilenir.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="1e8e1-143">Dahili olarak, `ng serve` geliştirme de kullanır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="1e8e1-144">`ng` Aracı yüklemediyseniz, bunun yerine çalıştırın. `npm run ng`</span><span class="sxs-lookup"><span data-stu-id="1e8e1-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="1e8e1-145">Örneğin, çalıştırabilirsiniz `npm run ng lint` veya `npm run ng test`.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="1e8e1-146">npm paketleri yükleyin</span><span class="sxs-lookup"><span data-stu-id="1e8e1-146">Install npm packages</span></span>

<span data-ttu-id="1e8e1-147">Üçüncü taraf npm paketleri yüklemek için *ClientApp* alt dizininde bir komut istemi kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="1e8e1-148">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="1e8e1-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="1e8e1-149">Yayımla ve dağıt</span><span class="sxs-lookup"><span data-stu-id="1e8e1-149">Publish and deploy</span></span>

<span data-ttu-id="1e8e1-150">Geliştirme aşamasında, uygulama geliştirici kolaylığı için optimize edilmiş bir modda çalışır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="1e8e1-151">Örneğin, JavaScript paketleri kaynak haritaları içerir (böylece hata ayıklama yaparken orijinal TypeScript kodunuzu görebilirsiniz).</span><span class="sxs-lookup"><span data-stu-id="1e8e1-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="1e8e1-152">Uygulama Diskteki TypeScript, HTML ve CSS dosya değişikliklerini izler ve bu dosyaların değiştiğini gördüğünde otomatik olarak yeniden derleyip yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="1e8e1-153">Üretimde, uygulamanızın performans için optimize edilmiş bir sürümüne hizmet edin.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="1e8e1-154">Bu otomatik olarak gerçekleşmesi için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-154">This is configured to happen automatically.</span></span> <span data-ttu-id="1e8e1-155">Yayımladığınızda, yapı yapılandırması, istemci tarafı kodunuzu derleyen minified, ahead-of-time (AoT) bir yapı yayır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="1e8e1-156">Geliştirme yapısının aksine, üretim yapısı Sunucuya Node.js'nin yüklenmesini gerektirmez (sunucu tarafı oluşturmayı (SSR) etkinleştirmediyseniz).</span><span class="sxs-lookup"><span data-stu-id="1e8e1-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="1e8e1-157">Standart ASP.NET [Core barındırma ve dağıtım yöntemlerini](xref:host-and-deploy/index)kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="1e8e1-158">"ng serve"i bağımsız olarak çalıştırın</span><span class="sxs-lookup"><span data-stu-id="1e8e1-158">Run "ng serve" independently</span></span>

<span data-ttu-id="1e8e1-159">Proje, ASP.NET Core uygulaması geliştirme modunda başladığında arka planda açısal CLI sunucusunun kendi örneğini başlatmak üzere yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="1e8e1-160">Ayrı bir sunucuydan el ile çalıştırmanız gerekmedığından bu kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="1e8e1-161">Bu varsayılan kurulumun bir dezavantajı vardır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="1e8e1-162">C# kodunuzu her değiştirip ASP.NET Core uygulamanızın yeniden başlatılması gerektiğinde, Açısal CLI sunucusu yeniden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="1e8e1-163">Yeniden başlatmak için yaklaşık 10 saniye gerekir.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="1e8e1-164">Sık sık C# kodu düzenlemeyapıyorsanız ve Açısal CLI'nin yeniden başlatılmasını beklemek istemiyorsanız, Açısal CLI sunucusunu ASP.NET Core işleminden bağımsız olarak harici olarak çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="1e8e1-165">Bunu yapmak için:</span><span class="sxs-lookup"><span data-stu-id="1e8e1-165">To do so:</span></span>

1. <span data-ttu-id="1e8e1-166">Komut istemiyle *ClientApp* alt dizinine geçin ve Açısal CLI geliştirme sunucusunu başlatın:</span><span class="sxs-lookup"><span data-stu-id="1e8e1-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="1e8e1-167">Açısal CLI geliştirme sunucusubaşlatmak için `npm start` `ng serve`kullanın, değil , böylece *package.json* yapılandırma saygı duyulur.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="1e8e1-168">Açısal CLI sunucusuna ek parametreler ilerler sayılsın, bunları `scripts` *package.json* dosyanızdaki ilgili satıra ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="1e8e1-169">kendi uygulamalarından birini başlatmak yerine harici Açısal CLI örneğini kullanacak şekilde ASP.NET Core uygulamanızı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="1e8e1-170">*Başlangıç* sınıfınızda, çağırmayı aşağıdakilerle değiştirin: `spa.UseAngularCliServer`</span><span class="sxs-lookup"><span data-stu-id="1e8e1-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="1e8e1-171">ASP.NET Core uygulamanızı başlattığınızda, bir Açısal CLI sunucusu başlatmaz.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="1e8e1-172">Bunun yerine el ile başlattığınız örnek kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="1e8e1-173">Bu, daha hızlı başlatılmasını ve yeniden başlatılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="1e8e1-174">Artık Açısal CLI'nin her seferinde istemci uygulamanızı yeniden oluşturmasını beklemiyor.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="1e8e1-175">.NET kodundan typeScript koduna veri aktarma</span><span class="sxs-lookup"><span data-stu-id="1e8e1-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="1e8e1-176">SSR sırasında, istek başına verileri ASP.NET Core uygulamanızdan Açısal uygulamanıza aktarmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="1e8e1-177">Örneğin, çerez bilgilerini veya veritabanından okunan bir şeyi aktarabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-177">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="1e8e1-178">Bunu yapmak için *Başlangıç* sınıfınızı edin.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="1e8e1-179">Geri `UseSpaPrerendering`aramada, `options.SupplyData` aşağıdakiler gibi bir değer ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="1e8e1-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="1e8e1-180">Geri `SupplyData` arama, isteğe göre rasgele, JSON serileştirilebilir verileri (örneğin, dizeleri, booleans veya sayılar) geçirmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="1e8e1-181">*Main.server.ts* kodunuz bunu `params.data`.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="1e8e1-182">Örneğin, önceki kod örneği `params.data.isHttpsRequest` `createServerRenderer` geri arama olarak bir boolean değeri geçer.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="1e8e1-183">Bunu, Açısal tarafından desteklenen herhangi bir şekilde uygulamanızın diğer bölümlerine iletebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="1e8e1-184">Örneğin, *main.server.ts'nin* `BASE_URL` değeri, oluşturucusu onu almak üzere bildirilen herhangi bir bileşene nasıl geçtiğini görün.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="1e8e1-185">SSR'nin Sakıncaları</span><span class="sxs-lookup"><span data-stu-id="1e8e1-185">Drawbacks of SSR</span></span>

<span data-ttu-id="1e8e1-186">Tüm uygulamalar SSR'den yararlanamaz.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="1e8e1-187">Birincil yararı algılanan performanstır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="1e8e1-188">Yavaş bir ağ bağlantısı üzerinden veya yavaş mobil cihazlarda uygulamanıza ulaşan ziyaretçiler, JavaScript paketlerini almak veya ayrışdırmak biraz zaman alsa bile ilk UI'yi hızlı bir şekilde görür.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="1e8e1-189">Ancak, birçok SCA esas olarak hızlı, uygulama neredeyse anında görünür hızlı bilgisayarlarda dahili şirket ağları üzerinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="1e8e1-190">Aynı zamanda, SSR'yi etkinleştirmek için önemli dezavantajları da vardır.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="1e8e1-191">Geliştirme sürecinize karmaşıklık katar.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-191">It adds complexity to your development process.</span></span> <span data-ttu-id="1e8e1-192">Kodunuz iki farklı ortamda çalışmalıdır: istemci tarafı ve sunucu tarafı (ASP.NET Core'dan çağrılan bir Düğüm.js ortamında).</span><span class="sxs-lookup"><span data-stu-id="1e8e1-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="1e8e1-193">Akılda tutulması gereken bazı şeyler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="1e8e1-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="1e8e1-194">SSR, üretim sunucularınızda bir Düğüm.js yüklemesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="1e8e1-195">Bu, Azure Uygulama Hizmetleri gibi bazı dağıtım senaryoları için otomatik olarak geçerlidir, ancak Azure Hizmet Kumaşı gibi diğerleri için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="1e8e1-196">`BuildServerSideRenderer` Yapı bayrağını etkinleştirmek *node_modules* dizininizin yayımlanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="1e8e1-197">Bu klasör, dağıtım süresini artıran 20.000'den fazla dosya içerir.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="1e8e1-198">Kodunuzu Düğüm.js ortamında çalıştırmak için, tarayıcıya özgü JavaScript API'lerinin varlığına `window` güvenemez. `localStorage`</span><span class="sxs-lookup"><span data-stu-id="1e8e1-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="1e8e1-199">Kodunuz (veya başvuruda bulunacağınız bazı üçüncü taraf kitaplık) bu API'leri kullanmaya çalışırsa, SSR sırasında bir hata alırsınız.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="1e8e1-200">Örneğin, birçok yerde tarayıcıya özgü API'lere atıfta bulunulduğu için jQuery'yi kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="1e8e1-201">Hataları önlemek için SSR'den kaçınmanız veya tarayıcıya özgü API'lerden veya kitaplıklardan kaçınmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="1e8e1-202">SSR sırasında çağrılmadığından emin olmak için bu TÜR API'lere yapılan çağrıları denetimlerde sararsınız.</span><span class="sxs-lookup"><span data-stu-id="1e8e1-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="1e8e1-203">Örneğin, JavaScript veya TypeScript kodunda aşağıdaki gibi bir denetim kullanın:</span><span class="sxs-lookup"><span data-stu-id="1e8e1-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="1e8e1-204">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1e8e1-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
