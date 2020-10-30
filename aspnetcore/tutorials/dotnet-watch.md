---
title: Dosya İzleyicisi kullanarak ASP.NET Core uygulamalar geliştirme
author: rick-anderson
description: Bu öğreticide, .NET Core CLI dosya İzleyicisi (DotNet Watch) aracının bir ASP.NET Core uygulamasında nasıl yükleneceği ve kullanılacağı gösterilmektedir.
ms.author: riande
ms.date: 05/31/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/dotnet-watch
ms.openlocfilehash: 27420fe00ba6375e15b67fb359be06df055eff1f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060046"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="4ed75-103">Dosya İzleyicisi kullanarak ASP.NET Core uygulamalar geliştirme</span><span class="sxs-lookup"><span data-stu-id="4ed75-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="4ed75-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="4ed75-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="4ed75-105">`dotnet watch` , kaynak dosyalar değiştiğinde [.NET Core CLI](/dotnet/core/tools) komutu çalıştıran bir araçtır.</span><span class="sxs-lookup"><span data-stu-id="4ed75-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="4ed75-106">Örneğin, bir dosya değişikliği derleme, test yürütmesi veya dağıtımı tetikleyebilir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="4ed75-107">Bu öğretici, iki uç nokta ile mevcut bir Web API 'SI kullanır: bir toplamı ve bir ürünü döndüren bir tane döndürür.</span><span class="sxs-lookup"><span data-stu-id="4ed75-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="4ed75-108">Ürün yönteminde, bu öğreticide düzeltilen bir hata vardır.</span><span class="sxs-lookup"><span data-stu-id="4ed75-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="4ed75-109">[Örnek uygulamayı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)indirin.</span><span class="sxs-lookup"><span data-stu-id="4ed75-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="4ed75-110">İki projeden oluşur: *WebApp* (bir ASP.NET Core Web API 'si) ve *webapptests* (Web API 'si için birim testleri).</span><span class="sxs-lookup"><span data-stu-id="4ed75-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="4ed75-111">Bir komut kabuğu 'nda *WebApp* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="4ed75-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="4ed75-112">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ed75-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="4ed75-113">`dotnet run --project <PROJECT>`Çalıştırmak için bir proje belirtmek üzere ' i kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4ed75-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="4ed75-114">Örneğin, `dotnet run --project WebApp` örnek uygulamanın kökünden çalıştırıldığında *WebApp* projesi de çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="4ed75-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="4ed75-115">Konsol çıktısı aşağıdakine benzer iletileri gösterir (uygulamanın çalıştığını ve istekleri beklediğini gösterir):</span><span class="sxs-lookup"><span data-stu-id="4ed75-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="4ed75-116">Bir web tarayıcısında `http://localhost:<port number>/api/math/sum?a=4&b=5` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="4ed75-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="4ed75-117">Sonucunu görmeniz gerekir `9` .</span><span class="sxs-lookup"><span data-stu-id="4ed75-117">You should see the result of `9`.</span></span>

<span data-ttu-id="4ed75-118">Ürün API 'sine () gidin `http://localhost:<port number>/api/math/product?a=4&b=5` .</span><span class="sxs-lookup"><span data-stu-id="4ed75-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="4ed75-119">`9`Beklenmez değil, döndürür `20` .</span><span class="sxs-lookup"><span data-stu-id="4ed75-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="4ed75-120">Bu sorun öğreticide daha sonra düzeltildi.</span><span class="sxs-lookup"><span data-stu-id="4ed75-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="4ed75-121">`dotnet watch`Projeye ekleme</span><span class="sxs-lookup"><span data-stu-id="4ed75-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="4ed75-122">`dotnet watch`Dosya İzleyici Aracı, .NET Core SDK sürüm 2.1.300 eklenir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="4ed75-123">.NET Core SDK önceki bir sürümü kullanılırken aşağıdaki adımlar gereklidir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="4ed75-124">`Microsoft.DotNet.Watcher.Tools` *. Csproj* dosyasına bir paket başvurusu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="4ed75-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="4ed75-125">`Microsoft.DotNet.Watcher.Tools`Aşağıdaki komutu çalıştırarak paketi yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="4ed75-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="4ed75-126">Kullanarak .NET Core CLI komutları çalıştırma `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="4ed75-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="4ed75-127">Tüm [.NET Core CLI komutları](/dotnet/core/tools#cli-commands) ile çalıştırılabilir `dotnet watch` .</span><span class="sxs-lookup"><span data-stu-id="4ed75-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="4ed75-128">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ed75-128">For example:</span></span>

| <span data-ttu-id="4ed75-129">Komut</span><span class="sxs-lookup"><span data-stu-id="4ed75-129">Command</span></span> | <span data-ttu-id="4ed75-130">İzle komutu</span><span class="sxs-lookup"><span data-stu-id="4ed75-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="4ed75-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="4ed75-131">dotnet run</span></span> | <span data-ttu-id="4ed75-132">DotNet izleme çalıştırması</span><span class="sxs-lookup"><span data-stu-id="4ed75-132">dotnet watch run</span></span> |
| <span data-ttu-id="4ed75-133">DotNet Run-f netcoreapp 3.1</span><span class="sxs-lookup"><span data-stu-id="4ed75-133">dotnet run -f netcoreapp3.1</span></span> | <span data-ttu-id="4ed75-134">DotNet Watch çalıştırması-f netcoreapp 3.1</span><span class="sxs-lookup"><span data-stu-id="4ed75-134">dotnet watch run -f netcoreapp3.1</span></span> |
| <span data-ttu-id="4ed75-135">DotNet Run-f netcoreapp 3.1----arg1</span><span class="sxs-lookup"><span data-stu-id="4ed75-135">dotnet run -f netcoreapp3.1 -- --arg1</span></span> | <span data-ttu-id="4ed75-136">DotNet Watch-f netcoreapp 3.1----arg1</span><span class="sxs-lookup"><span data-stu-id="4ed75-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span></span> |
| <span data-ttu-id="4ed75-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="4ed75-137">dotnet test</span></span> | <span data-ttu-id="4ed75-138">DotNet izleme testi</span><span class="sxs-lookup"><span data-stu-id="4ed75-138">dotnet watch test</span></span> |

<span data-ttu-id="4ed75-139">`dotnet watch run` *WebApp* klasöründe çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4ed75-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="4ed75-140">Konsol çıktısı başladığını gösterir `watch` .</span><span class="sxs-lookup"><span data-stu-id="4ed75-140">The console output indicates `watch` has started.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="4ed75-141">`dotnet watch run`Bir Web uygulamasında çalıştırıldığında UYGULAMANıN URL 'sine, bir kez daha sonra giden bir tarayıcı başlatılır.</span><span class="sxs-lookup"><span data-stu-id="4ed75-141">Running `dotnet watch run` on a web app launches a browser that navigates to the app's URL once ready.</span></span> <span data-ttu-id="4ed75-142">`dotnet watch` Bunu, uygulamanın konsol çıkışını okuyarak ve tarafından görüntülenmeye yönelik bir ileti için bekleyen yapar <xref:Microsoft.AspNetCore.WebHost> .</span><span class="sxs-lookup"><span data-stu-id="4ed75-142">`dotnet watch` does this by reading the app's console output and waiting for the the ready message displayed by <xref:Microsoft.AspNetCore.WebHost>.</span></span>

<span data-ttu-id="4ed75-143">`dotnet watch` izlenen dosyalardaki değişiklikleri algıladığında tarayıcıyı yeniler.</span><span class="sxs-lookup"><span data-stu-id="4ed75-143">`dotnet watch` refreshes the browser when it detects changes to watched files.</span></span> <span data-ttu-id="4ed75-144">Bunu yapmak için, izle komutu, uygulama tarafından oluşturulan HTML yanıtlarını değiştiren bir ara yazılımı uygulamaya yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-144">To do this, the watch command injects a middleware to the app that modifies HTML responses created by the app.</span></span> <span data-ttu-id="4ed75-145">Ara yazılım, `dotnet watch` tarayıcıya, tarayıcıyı yenilemeye izin veren bir JavaScript betik bloğu ekler.</span><span class="sxs-lookup"><span data-stu-id="4ed75-145">The middleware adds a JavaScript script block to the page that allows `dotnet watch` to instruct the browser to refresh.</span></span> <span data-ttu-id="4ed75-146">Şu anda, *. html* ve *. css* dosyaları gibi statik içerik de dahil olmak üzere tüm izlenen dosyalardaki değişiklikler uygulamanın yeniden oluşturulmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="4ed75-146">Currently, changes to all watched files, including static content such as *.html* and *.css* files cause the app to be rebuilt.</span></span>

<span data-ttu-id="4ed75-147">`dotnet watch`:</span><span class="sxs-lookup"><span data-stu-id="4ed75-147">`dotnet watch`:</span></span>

* <span data-ttu-id="4ed75-148">Yalnızca varsayılan olarak derlemeleri etkileyen dosyaları izler.</span><span class="sxs-lookup"><span data-stu-id="4ed75-148">Only watches files that impact builds by default.</span></span>
* <span data-ttu-id="4ed75-149">Diğer izlenen dosyalar (yapılandırma aracılığıyla), hala derleme gerçekleşirken sonuç olarak oluşur.</span><span class="sxs-lookup"><span data-stu-id="4ed75-149">Any additionally watched files (via configuration) still results in a build taking place.</span></span>

<span data-ttu-id="4ed75-150">Yapılandırma hakkında daha fazla bilgi için bu belgede [DotNet-Watch yapılandırması](#dotnet-watch-configuration) bölümüne bakın</span><span class="sxs-lookup"><span data-stu-id="4ed75-150">For more information on configuration, see [dotnet-watch configuration](#dotnet-watch-configuration) in this document</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="4ed75-151">`dotnet watch --project <PROJECT>`İzlenecek projeyi belirtmek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4ed75-151">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="4ed75-152">Örneğin, `dotnet watch --project WebApp run` örnek uygulamanın kökünden çalıştırılması Ayrıca *WebApp* projesini de çalıştırır ve bu uygulamayı izleyebilir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-152">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="4ed75-153">İle değişiklik yap `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="4ed75-153">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="4ed75-154">Çalıştığından emin olun `dotnet watch` .</span><span class="sxs-lookup"><span data-stu-id="4ed75-154">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="4ed75-155">`Product` *MathController.cs* yöntemindeki hatayı düzelttikten sonra, ürünü döndürür ve toplamı değil:</span><span class="sxs-lookup"><span data-stu-id="4ed75-155">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="4ed75-156">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="4ed75-156">Save the file.</span></span> <span data-ttu-id="4ed75-157">Konsol çıktısı `dotnet watch` bir dosya değişikliği olduğunu algıladı ve uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="4ed75-157">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="4ed75-158">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` doğru sonucu döndürür.</span><span class="sxs-lookup"><span data-stu-id="4ed75-158">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="4ed75-159">Kullanarak testleri çalıştırma `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="4ed75-159">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="4ed75-160">`Product`Toplamı döndürmek için *MathController.cs* yöntemini geri değiştirin.</span><span class="sxs-lookup"><span data-stu-id="4ed75-160">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="4ed75-161">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="4ed75-161">Save the file.</span></span>
1. <span data-ttu-id="4ed75-162">Bir komut kabuğunda *Webapptests* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="4ed75-162">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="4ed75-163">[DotNet restore](/dotnet/core/tools/dotnet-restore)çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4ed75-163">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="4ed75-164">Şu komutu çalıştırın: `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="4ed75-164">Run `dotnet watch test`.</span></span> <span data-ttu-id="4ed75-165">Çıktısı bir testin başarısız olduğunu ve izleyicinin dosya değişikliklerini beklediğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="4ed75-165">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="4ed75-166">`Product`Yöntem kodunu, ürünü geri döndürdüğünden düzeltir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-166">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="4ed75-167">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="4ed75-167">Save the file.</span></span>

<span data-ttu-id="4ed75-168">`dotnet watch` dosya değişikliğini algılar ve testleri yeniden çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="4ed75-168">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="4ed75-169">Konsol çıktısı, geçirilen testleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-169">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="4ed75-170">Dosya listesini izlemek için Özelleştir</span><span class="sxs-lookup"><span data-stu-id="4ed75-170">Customize files list to watch</span></span>

<span data-ttu-id="4ed75-171">Varsayılan olarak, `dotnet-watch` aşağıdaki glob desenleriyle eşleşen tüm dosyaları izler:</span><span class="sxs-lookup"><span data-stu-id="4ed75-171">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="4ed75-172">*. Csproj* dosyası düzenlenerek, izleme listesine daha fazla öğe eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-172">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="4ed75-173">Öğeler tek tek veya glob desenleri kullanılarak belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-173">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="4ed75-174">İzlenen dosyaları kabul etme</span><span class="sxs-lookup"><span data-stu-id="4ed75-174">Opt-out of files to be watched</span></span>

<span data-ttu-id="4ed75-175">`dotnet-watch` varsayılan ayarlarını yok sayılabilecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-175">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="4ed75-176">Belirli dosyaları yoksaymak için `Watch="false"` özniteliği *. csproj* dosyasındaki bir öğenin tanımına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="4ed75-176">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="4ed75-177">Özel izleme projeleri</span><span class="sxs-lookup"><span data-stu-id="4ed75-177">Custom watch projects</span></span>

<span data-ttu-id="4ed75-178">`dotnet-watch` C# projeleriyle sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-178">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="4ed75-179">Özel izleme projeleri, farklı senaryoları işlemek için oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-179">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="4ed75-180">Aşağıdaki proje mizanpajını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="4ed75-180">Consider the following project layout:</span></span>

* <span data-ttu-id="4ed75-181">**sınamanız**</span><span class="sxs-lookup"><span data-stu-id="4ed75-181">**test/**</span></span>
  * <span data-ttu-id="4ed75-182">*UnitTests/UnitTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="4ed75-182">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="4ed75-183">*Integrationtests/ıntegrationtests. csproj*</span><span class="sxs-lookup"><span data-stu-id="4ed75-183">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="4ed75-184">Hedef her iki projeyi de seyretmek istiyorsanız, her iki projeyi de izlemek için yapılandırılmış özel bir proje dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="4ed75-184">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="4ed75-185">Her iki projede de dosya izlemeye başlamak için, *Test* klasörüne geçin.</span><span class="sxs-lookup"><span data-stu-id="4ed75-185">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="4ed75-186">Şu kodu yürütün:</span><span class="sxs-lookup"><span data-stu-id="4ed75-186">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="4ed75-187">VSTest, her iki test projesinde herhangi bir dosya değiştiğinde yürütülür.</span><span class="sxs-lookup"><span data-stu-id="4ed75-187">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-configuration"></a><span data-ttu-id="4ed75-188">DotNet-yapılandırmayı izle</span><span class="sxs-lookup"><span data-stu-id="4ed75-188">dotnet-watch configuration</span></span>

<span data-ttu-id="4ed75-189">Bazı yapılandırma seçenekleri, `dotnet watch` ortam değişkenleri aracılığıyla geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-189">Some configuration options can be passed to `dotnet watch` through environment variables.</span></span> <span data-ttu-id="4ed75-190">Kullanılabilir değişkenler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="4ed75-190">The available variables are:</span></span>

| <span data-ttu-id="4ed75-191">Ayar</span><span class="sxs-lookup"><span data-stu-id="4ed75-191">Setting</span></span>  | <span data-ttu-id="4ed75-192">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4ed75-192">Description</span></span> |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | <span data-ttu-id="4ed75-193">"1" veya "true" olarak ayarlandıysa `dotnet watch` CoreFx yerine bir yoklama Dosya İzleyicisi kullanır `FileSystemWatcher` .</span><span class="sxs-lookup"><span data-stu-id="4ed75-193">If set to "1" or "true", `dotnet watch` uses a polling file watcher instead of CoreFx's `FileSystemWatcher`.</span></span> <span data-ttu-id="4ed75-194">Ağ paylaşımlarında veya Docker takılmış birimlerde dosya izlerken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4ed75-194">Used when watching files on network shares or Docker mounted volumes.</span></span>                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | <span data-ttu-id="4ed75-195">Varsayılan olarak, `dotnet watch` her dosya değişikliğine geri yükleme çalıştırma veya izlenen dosyalar kümesini yeniden değerlendirme gibi belirli işlemleri önleyerek derlemeyi iyileştirir.</span><span class="sxs-lookup"><span data-stu-id="4ed75-195">By default, `dotnet watch` optimizes the build by avoiding certain operations such as running restore or re-evaluating the set of watched files on every file change.</span></span> <span data-ttu-id="4ed75-196">"1" veya "true" olarak ayarlanırsa, bu iyileştirmeler devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="4ed75-196">If set to "1" or "true",  these optimizations are disabled.</span></span> |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | <span data-ttu-id="4ed75-197">`dotnet watch run``launchBrowser` *launchSettings.jsüzerinde* yapılandırılmış Web uygulamaları için tarayıcıları başlatmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="4ed75-197">`dotnet watch run` attempts to launch browsers for web apps with `launchBrowser` configured in *launchSettings.json* .</span></span> <span data-ttu-id="4ed75-198">"1" veya "true" olarak ayarlanırsa, bu davranış bastırılır.</span><span class="sxs-lookup"><span data-stu-id="4ed75-198">If set to "1" or "true", this behavior is suppressed.</span></span> |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | <span data-ttu-id="4ed75-199">`dotnet watch run` dosya değişikliklerini algıladığında tarayıcıları yenilemeyi dener.</span><span class="sxs-lookup"><span data-stu-id="4ed75-199">`dotnet watch run` attempts to refresh browsers when it detects file changes.</span></span> <span data-ttu-id="4ed75-200">"1" veya "true" olarak ayarlanırsa, bu davranış bastırılır.</span><span class="sxs-lookup"><span data-stu-id="4ed75-200">If set to "1" or "true", this behavior is suppressed.</span></span> <span data-ttu-id="4ed75-201">Ayarlanırsa, bu davranış da bastırılır `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` .</span><span class="sxs-lookup"><span data-stu-id="4ed75-201">This behavior is also suppressed if `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` is set.</span></span> |

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="4ed75-202">`dotnet-watch` GitHub 'da</span><span class="sxs-lookup"><span data-stu-id="4ed75-202">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="4ed75-203">`dotnet-watch` GitHub [DotNet/AspNetCore deposunun](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="4ed75-203">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
