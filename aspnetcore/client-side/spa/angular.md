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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: spa/angular
ms.openlocfilehash: d6e52a7e2c8e9c2e440b187312eeee9fc06699ae
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773748"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="1bb32-103">Angular proje şablonunu ASP.NET Core ile kullanma</span><span class="sxs-lookup"><span data-stu-id="1bb32-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="1bb32-104">Güncelleştirilmiş angular proje şablonu, zengin, istemci tarafı Kullanıcı arabirimi (UI) uygulamak için angular ve angular CLı kullanan ASP.NET Core uygulamalar için uygun bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="1bb32-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="1bb32-105">Şablon, bir API arka ucu görevi gören bir ASP.NET Core projesi ve Kullanıcı arabirimi olarak davranacak angular CLı projesi oluşturma ile eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="1bb32-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="1bb32-106">Şablon, her iki proje türünü tek bir uygulama projesinde barındırmanın kolaylığını sunar.</span><span class="sxs-lookup"><span data-stu-id="1bb32-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="1bb32-107">Sonuç olarak, uygulama projesi tek bir birim olarak oluşturulup yayımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="1bb32-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="1bb32-108">Yeni bir uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="1bb32-108">Create a new app</span></span>

<span data-ttu-id="1bb32-109">ASP.NET Core 2,1 yüklüyse, angular proje şablonunu yüklemeye gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="1bb32-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="1bb32-110">Boş bir dizinde komutunu `dotnet new angular` kullanarak komut isteminden yeni bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bb32-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="1bb32-111">Örneğin, aşağıdaki komutlar uygulamayı *Yeni bir uygulama* dizininde oluşturur ve bu dizine geçer:</span><span class="sxs-lookup"><span data-stu-id="1bb32-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="1bb32-112">Uygulamayı Visual Studio 'dan veya .NET Core CLI çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1bb32-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bb32-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bb32-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="1bb32-114">Oluşturulan *. csproj* dosyasını açın ve uygulamayı buradan normal olarak çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bb32-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="1bb32-115">Yapı işlemi ilk çalıştırmada NPM bağımlılıklarını geri yükler ve bu işlem birkaç dakika sürebilir.</span><span class="sxs-lookup"><span data-stu-id="1bb32-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="1bb32-116">Sonraki derlemeler çok daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1bb32-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1bb32-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1bb32-118">Bir ortam değişkenine `ASPNETCORE_Environment` sahip olduğunuzdan emin olun `Development`.</span><span class="sxs-lookup"><span data-stu-id="1bb32-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="1bb32-119">Windows 'ta (PowerShell olmayan istemler ' de), `SET ASPNETCORE_Environment=Development`öğesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bb32-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="1bb32-120">Linux veya macOS üzerinde öğesini çalıştırın `export ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="1bb32-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="1bb32-121">Uygulamanın doğru bir şekilde derlemelerin doğrulanması için [DotNet derlemesini](/dotnet/core/tools/dotnet-build) çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bb32-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="1bb32-122">İlk çalıştırmada, derleme işlemi NPM bağımlılıklarını geri yükler ve bu işlem birkaç dakika sürebilir.</span><span class="sxs-lookup"><span data-stu-id="1bb32-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="1bb32-123">Sonraki derlemeler çok daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="1bb32-124">Uygulamayı başlatmak için [DotNet çalıştırmasını](/dotnet/core/tools/dotnet-run) çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bb32-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="1bb32-125">Aşağıdakine benzer bir ileti günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="1bb32-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="1bb32-126">Bu URL 'yi bir tarayıcıda gezin.</span><span class="sxs-lookup"><span data-stu-id="1bb32-126">Navigate to this URL in a browser.</span></span>

> [!WARNING]
> <span data-ttu-id="1bb32-127">Uygulama arka planda angular CLı sunucusunun bir örneğini başlatır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="1bb32-128">Aşağıdakine benzer bir ileti günlüğe kaydedilir: *ng Live Development Server, localhost&lt;: otherport&gt;, için http://localhost:&ltbir tarayıcı açın, diğer bir deyişle, diğer bağlantı&gt;noktası*.</span><span class="sxs-lookup"><span data-stu-id="1bb32-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open a browser to http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="1bb32-129">Bu iletiyi&mdash;yoksay Birleşik ASP.NET Core ve ANGULAR CLI uygulamasının URL 'si **değildir** .</span><span class="sxs-lookup"><span data-stu-id="1bb32-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="1bb32-130">Proje şablonu, bir ASP.NET Core uygulaması ve angular uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bb32-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="1bb32-131">ASP.NET Core uygulaması veri erişimi, yetkilendirme ve diğer sunucu tarafı sorunları için kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="1bb32-132">*Clientapp* alt dizininde bulunan angular uygulamasının tüm Kullanıcı arabirimi sorunları için kullanılması amaçlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="1bb32-133">Sayfa, resim, stil, modül vb. ekleyin</span><span class="sxs-lookup"><span data-stu-id="1bb32-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="1bb32-134">*Clientapp* dizini standart ANGULAR CLI uygulaması içerir.</span><span class="sxs-lookup"><span data-stu-id="1bb32-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="1bb32-135">Daha fazla bilgi için bkz. resmi [angular belgeleri](https://angular.io) .</span><span class="sxs-lookup"><span data-stu-id="1bb32-135">See the official [Angular documentation](https://angular.io) for more information.</span></span>

<span data-ttu-id="1bb32-136">Bu şablon tarafından oluşturulan angular uygulaması ve angular CLı 'nın kendisi (aracılığıyla `ng new`) tarafından oluşturulan hafif farklar vardır; Ancak, uygulamanın özellikleri değiştirilmez.</span><span class="sxs-lookup"><span data-stu-id="1bb32-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="1bb32-137">Şablon tarafından oluşturulan uygulama, [önyükleme](https://getbootstrap.com/)tabanlı bir düzen ve temel bir yönlendirme örneği içerir.</span><span class="sxs-lookup"><span data-stu-id="1bb32-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="1bb32-138">Çalıştır komutları</span><span class="sxs-lookup"><span data-stu-id="1bb32-138">Run ng commands</span></span>

<span data-ttu-id="1bb32-139">Bir komut isteminde *clientapp* alt dizinine geçin:</span><span class="sxs-lookup"><span data-stu-id="1bb32-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="1bb32-140">`ng` Araç genel olarak yüklüyse, komutlarından birini çalıştırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1bb32-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="1bb32-141">Örneğin, diğer `ng lint` [angular CLI komutlarından](https://angular.io/cli)birini veya birini çalıştırabilirsiniz `ng test`.</span><span class="sxs-lookup"><span data-stu-id="1bb32-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://angular.io/cli).</span></span> <span data-ttu-id="1bb32-142">ASP.NET Core uygulamanız uygulamanızın sunucu tarafı ve `ng serve` istemci tarafı bölümlerine sunma konusunda anlaştığından, ancak bu çalışmaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="1bb32-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="1bb32-143">Dahili olarak, geliştirme `ng serve` sürecinde kullanır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="1bb32-144">Yüklü bir `ng` araç yoksa, bunun yerine çalıştırın `npm run ng` .</span><span class="sxs-lookup"><span data-stu-id="1bb32-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="1bb32-145">Örneğin, veya `npm run ng lint` `npm run ng test`çalıştırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1bb32-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="1bb32-146">NPM paketlerini yükler</span><span class="sxs-lookup"><span data-stu-id="1bb32-146">Install npm packages</span></span>

<span data-ttu-id="1bb32-147">Üçüncü taraf NPM paketlerini yüklemek için *clientapp* alt dizininde bir komut istemi kullanın.</span><span class="sxs-lookup"><span data-stu-id="1bb32-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="1bb32-148">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="1bb32-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="1bb32-149">Yayımla ve dağıt</span><span class="sxs-lookup"><span data-stu-id="1bb32-149">Publish and deploy</span></span>

<span data-ttu-id="1bb32-150">Geliştirme aşamasında uygulama, geliştirici kolaylığı için iyileştirilmiş bir modda çalışır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="1bb32-151">Örneğin, JavaScript demeti kaynak eşlemeleri içerir (hata ayıklarken, özgün TypeScript kodunuzu görebilirsiniz).</span><span class="sxs-lookup"><span data-stu-id="1bb32-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="1bb32-152">Uygulama TypeScript, HTML ve CSS dosya değişikliklerini diskte izler ve bu dosya değişikliğini gördüğünde otomatik olarak yeniden derlenir ve yeniden yükler.</span><span class="sxs-lookup"><span data-stu-id="1bb32-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="1bb32-153">Üretimde, uygulamanızın performans için iyileştirilmiş bir sürümünü sunar.</span><span class="sxs-lookup"><span data-stu-id="1bb32-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="1bb32-154">Bu otomatik olarak gerçekleşecek şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-154">This is configured to happen automatically.</span></span> <span data-ttu-id="1bb32-155">Yayımladığınızda, derleme yapılandırması, istemci tarafı kodunuzun küçültülmüş, önceden derlenmiş bir derlemesini yayar.</span><span class="sxs-lookup"><span data-stu-id="1bb32-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="1bb32-156">Geliştirme derlemesinin aksine, üretim derlemesi, Node. js ' nin sunucuya yüklenmesini gerektirmez (sunucu tarafı işlemesini (SSR) etkinleştirmediğiniz müddetçe).</span><span class="sxs-lookup"><span data-stu-id="1bb32-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="1bb32-157">Standart [ASP.NET Core barındırma ve dağıtım yöntemleri](xref:host-and-deploy/index)kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1bb32-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="1bb32-158">Bağımsız olarak "ng hizmeti" Çalıştır</span><span class="sxs-lookup"><span data-stu-id="1bb32-158">Run "ng serve" independently</span></span>

<span data-ttu-id="1bb32-159">Proje, ASP.NET Core uygulaması geliştirme modunda başladığında arka planda kendi angular CLı sunucusu örneğini başlatacak şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="1bb32-160">Ayrı bir sunucuyu el ile çalıştırmak zorunda olmadığınızdan bu kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="1bb32-161">Bu varsayılan Kurulumun bir dezavantajı vardır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="1bb32-162">C# kodunuzu her değiştirişinizde ve ASP.NET Core uygulamanızın yeniden başlatılması gerektiğinde, angular CLı sunucusu yeniden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="1bb32-163">Yeniden başlatmak için yaklaşık 10 saniye gereklidir.</span><span class="sxs-lookup"><span data-stu-id="1bb32-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="1bb32-164">Sık kullanılan C# kod düzenlemeleri oluşturuyorsanız ve angular CLı 'nın yeniden başlatılmasını beklemek istemiyorsanız, angular CLı sunucusunu ASP.NET Core işleminden bağımsız olarak dışarıdan çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bb32-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="1bb32-165">Bunu yapmak için:</span><span class="sxs-lookup"><span data-stu-id="1bb32-165">To do so:</span></span>

1. <span data-ttu-id="1bb32-166">Bir komut isteminde *clientapp* alt dizinine geçin ve ANGULAR CLI geliştirme sunucusunu başlatın:</span><span class="sxs-lookup"><span data-stu-id="1bb32-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="1bb32-167">*Package. JSON* içindeki yapılandırmanın kullanılması için değil `npm start` `ng serve`, angular CLI geliştirme sunucusunu başlatmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1bb32-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="1bb32-168">Angular CLı sunucusuna ek parametreler geçirmek için bunları `scripts` *Package. JSON* dosyanızdaki ilgili satıra ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1bb32-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="1bb32-169">ASP.NET Core uygulamanızı, kendi kendine birini başlatmak yerine dış angular CLı örneğini kullanacak şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1bb32-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="1bb32-170">*Başlangıç* sınıfınıza, `spa.UseAngularCliServer` çağrıyı aşağıdaki ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="1bb32-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="1bb32-171">ASP.NET Core uygulamanızı başlattığınızda, angular CLı sunucusunu başlatmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="1bb32-172">Bunun yerine el ile başlattığınız örnek kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="1bb32-173">Bu, daha hızlı başlamasını ve yeniden başlatılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="1bb32-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="1bb32-174">Artık, istemci uygulamanızı her seferinde yeniden oluşturmak için angular CLı bekliyor.</span><span class="sxs-lookup"><span data-stu-id="1bb32-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="1bb32-175">.NET kodundan verileri TypeScript koduna geçirme</span><span class="sxs-lookup"><span data-stu-id="1bb32-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="1bb32-176">SSR sırasında, ASP.NET Core uygulamanızdaki istek başına verileri angular uygulamanıza geçirmek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1bb32-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="1bb32-177">Örneğin, tanımlama bilgisi bilgilerini veya bir veritabanından okunan bir şeyi geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1bb32-177">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="1bb32-178">Bunu yapmak için *Başlangıç* sınıfınızı düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="1bb32-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="1bb32-179">İçin `UseSpaPrerendering`geri çağırmada, aşağıdaki gibi bir değer `options.SupplyData` ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="1bb32-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="1bb32-180">`SupplyData` Geri arama, isteğe bağlı olarak, JSON ile seri hale getirilebilir verileri (örneğin, dizeler, Boole değerleri veya sayılar) geçirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="1bb32-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="1bb32-181">*Main. Server. TS* kodunuz bunu olarak `params.data`alır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="1bb32-182">Örneğin, yukarıdaki kod örneği `params.data.isHttpsRequest` `createServerRenderer` geri çağırmada olduğu gibi bir Boole değeri geçirir.</span><span class="sxs-lookup"><span data-stu-id="1bb32-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="1bb32-183">Bunu uygulamanızın diğer bölümlerine, angular tarafından desteklenen herhangi bir yolla geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1bb32-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="1bb32-184">Örneğin, bkz. *Main. Server. TS* `BASE_URL` değeri, Oluşturucusu onu almak için bildirildiği herhangi bir bileşene geçirir.</span><span class="sxs-lookup"><span data-stu-id="1bb32-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="1bb32-185">SSR 'nin dezavantajları</span><span class="sxs-lookup"><span data-stu-id="1bb32-185">Drawbacks of SSR</span></span>

<span data-ttu-id="1bb32-186">Tüm uygulamalar SSR 'den faydalanır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="1bb32-187">Birincil avantaj, performans algılanır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="1bb32-188">Yavaş bir ağ bağlantısı veya yavaş mobil cihazlarda uygulamanıza ulaşan ziyaretçiler, JavaScript paketlerini getirme veya ayrıştırma sırasında bile ilk Kullanıcı arabirimine hızlı bir şekilde bakın.</span><span class="sxs-lookup"><span data-stu-id="1bb32-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="1bb32-189">Ancak, çoğu maça genellikle uygulamanın neredeyse anında göründüğü hızlı bilgisayarlarda hızlı, dahili şirket ağları üzerinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="1bb32-190">Aynı zamanda SSR 'yi etkinleştirmenin önemli sakıncaları vardır.</span><span class="sxs-lookup"><span data-stu-id="1bb32-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="1bb32-191">Geliştirme sürecinizi karmaşıklık altına ekler.</span><span class="sxs-lookup"><span data-stu-id="1bb32-191">It adds complexity to your development process.</span></span> <span data-ttu-id="1bb32-192">Kodunuzun iki farklı ortamda çalışması gerekir: istemci tarafı ve sunucu tarafı (ASP.NET Core bir Node. js ortamında çağrılır).</span><span class="sxs-lookup"><span data-stu-id="1bb32-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="1bb32-193">Göz önünde bulundurmanız gereken bazı şeyler aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="1bb32-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="1bb32-194">SSR, üretim sunucularınızda bir Node. js yüklemesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1bb32-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="1bb32-195">Azure uygulama hizmetleri gibi bazı dağıtım senaryolarında bu durum otomatik olarak yapılır, ancak Azure Service Fabric gibi diğerleri için de değildir.</span><span class="sxs-lookup"><span data-stu-id="1bb32-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="1bb32-196">Yapı bayrağını `BuildServerSideRenderer` etkinleştirmek, *node_modules* dizininizin yayımlamasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="1bb32-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="1bb32-197">Bu klasör, dağıtım süresini artıran 20000 + dosyalarını içerir.</span><span class="sxs-lookup"><span data-stu-id="1bb32-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="1bb32-198">Kodunuzu bir Node. js ortamında çalıştırmak için, `window` veya `localStorage`gibi tarayıcıya özgü JavaScript API 'lerinin varlığına bağlı olamaz.</span><span class="sxs-lookup"><span data-stu-id="1bb32-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="1bb32-199">Kodunuz (veya başvuru yaptığınız bazı üçüncü taraf kitaplığı) Bu API 'Leri kullanmayı denediğinde SSR sırasında bir hata alırsınız.</span><span class="sxs-lookup"><span data-stu-id="1bb32-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="1bb32-200">Örneğin, birçok yerde tarayıcıya özgü API 'Lere başvurduğundan jQuery kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="1bb32-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="1bb32-201">Hataları önlemek için SSR 'yi kullanmaktan veya tarayıcıya özgü API 'lerden ya da kitaplıklardan kaçınmalısınız.</span><span class="sxs-lookup"><span data-stu-id="1bb32-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="1bb32-202">Bu tür API 'lere yapılan çağrıları SSR sırasında çağrıdıklarından emin olmak için, denetimleri içinde kaydırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1bb32-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="1bb32-203">Örneğin, JavaScript veya TypeScript kodunda aşağıdakiler gibi bir denetim kullanın:</span><span class="sxs-lookup"><span data-stu-id="1bb32-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="1bb32-204">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1bb32-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
