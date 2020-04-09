---
title: Dosya izleyicisi kullanarak ASP.NET Core uygulamaları geliştirme
author: rick-anderson
description: Bu öğretici, ASP.NET Core uygulamasında .NET Core CLI'nin dosya izleyicisi (dotnet watch) aracının nasıl yüklenilip kullanılacağını gösterir.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: bedb3e6a65839db915ca7bc821a267a14d34bf30
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667414"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="8202d-103">Dosya izleyicisi kullanarak ASP.NET Core uygulamaları geliştirme</span><span class="sxs-lookup"><span data-stu-id="8202d-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="8202d-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="8202d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="8202d-105">[dotnet watch,](https://www.nuget.org/packages/dotnet-watch) kaynak dosyaları değiştiğinde [.NET Core CLI](/dotnet/core/tools) komutunu çalıştıran bir araçtır.</span><span class="sxs-lookup"><span data-stu-id="8202d-105">[dotnet watch](https://www.nuget.org/packages/dotnet-watch) is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="8202d-106">Örneğin, bir dosya değişikliği derlemeyi, test yürütmeyi veya dağıtımı tetikleyebilir.</span><span class="sxs-lookup"><span data-stu-id="8202d-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="8202d-107">Bu öğretici, iki uç noktaile varolan bir web API kullanır: biri bir toplamı döndürür ve bir ürün döndürür.</span><span class="sxs-lookup"><span data-stu-id="8202d-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="8202d-108">Ürün yöntemi, bu öğreticide sabit bir hata vardır.</span><span class="sxs-lookup"><span data-stu-id="8202d-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="8202d-109">Örnek [uygulamayı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)indirin.</span><span class="sxs-lookup"><span data-stu-id="8202d-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="8202d-110">İki projeden oluşur: *WebApp* (ASP.NET Core web API) ve *WebAppTests* (web API için birim testleri).</span><span class="sxs-lookup"><span data-stu-id="8202d-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="8202d-111">Komut kabuğunda *WebApp* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="8202d-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="8202d-112">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8202d-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="8202d-113">Çalışacak bir `dotnet run --project <PROJECT>` proje belirtmek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8202d-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="8202d-114">Örneğin, örnek `dotnet run --project WebApp` uygulamanın kökünden çalıştırmak da *WebApp* projesini çalıştıracaktır.</span><span class="sxs-lookup"><span data-stu-id="8202d-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="8202d-115">Konsol çıkışı aşağıdakine benzer iletileri gösterir (uygulamanın çalıştığını ve istekleri beklediğini gösterir):</span><span class="sxs-lookup"><span data-stu-id="8202d-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="8202d-116">Bir web tarayıcısında `http://localhost:<port number>/api/math/sum?a=4&b=5` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="8202d-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="8202d-117">Sonucu `9`görmelisiniz.</span><span class="sxs-lookup"><span data-stu-id="8202d-117">You should see the result of `9`.</span></span>

<span data-ttu-id="8202d-118">Ürün API'sine`http://localhost:<port number>/api/math/product?a=4&b=5`gidin ( ).</span><span class="sxs-lookup"><span data-stu-id="8202d-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="8202d-119">Beklediğiniz `9`gibi `20` değil, geri dönüyor.</span><span class="sxs-lookup"><span data-stu-id="8202d-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="8202d-120">Bu sorun daha sonra öğretici de giderilir.</span><span class="sxs-lookup"><span data-stu-id="8202d-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="8202d-121">Projeye `dotnet watch` ekleme</span><span class="sxs-lookup"><span data-stu-id="8202d-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="8202d-122">Dosya `dotnet watch` izleme aracı .NET Core SDK'nın 2.1.300 sürümüyle birlikte verilir.</span><span class="sxs-lookup"><span data-stu-id="8202d-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="8202d-123">.NET Core SDK'nın önceki bir sürümünü kullanırken aşağıdaki adımlar gereklidir.</span><span class="sxs-lookup"><span data-stu-id="8202d-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="8202d-124">`Microsoft.DotNet.Watcher.Tools` *.csproj* dosyasına bir paket başvurusu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8202d-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="8202d-125">Aşağıdaki `Microsoft.DotNet.Watcher.Tools` komutu çalıştırarak paketi yükleyin:</span><span class="sxs-lookup"><span data-stu-id="8202d-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="8202d-126">Kullanarak .NET Core CLI komutlarını çalıştırın`dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="8202d-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="8202d-127">Herhangi bir [.NET Core CLI komutu](/dotnet/core/tools#cli-commands) ile `dotnet watch`çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="8202d-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="8202d-128">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="8202d-128">For example:</span></span>

| <span data-ttu-id="8202d-129">Komut</span><span class="sxs-lookup"><span data-stu-id="8202d-129">Command</span></span> | <span data-ttu-id="8202d-130">Saat ile komut</span><span class="sxs-lookup"><span data-stu-id="8202d-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="8202d-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="8202d-131">dotnet run</span></span> | <span data-ttu-id="8202d-132">dotnet izle koş</span><span class="sxs-lookup"><span data-stu-id="8202d-132">dotnet watch run</span></span> |
| <span data-ttu-id="8202d-133">dotnet çalıştır -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="8202d-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="8202d-134">dotnet izle run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="8202d-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="8202d-135">dotnet koşmak -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="8202d-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="8202d-136">dotnet izle run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="8202d-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="8202d-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="8202d-137">dotnet test</span></span> | <span data-ttu-id="8202d-138">dotnet izle testi</span><span class="sxs-lookup"><span data-stu-id="8202d-138">dotnet watch test</span></span> |

<span data-ttu-id="8202d-139">`dotnet watch run` *WebApp* klasöründe çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="8202d-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="8202d-140">Konsol çıkışı `watch` nın başladığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="8202d-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="8202d-141">İzlenecek `dotnet watch --project <PROJECT>` bir proje belirtmek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8202d-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="8202d-142">Örneğin, örnek `dotnet watch --project WebApp run` uygulamanın kökünden çalışan da çalışacak ve *WebApp* projesini izlemek.</span><span class="sxs-lookup"><span data-stu-id="8202d-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="8202d-143">Değişiklik yapın`dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="8202d-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="8202d-144">Koştuğundan emin `dotnet watch` ol.</span><span class="sxs-lookup"><span data-stu-id="8202d-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="8202d-145">Hatayı `Product` *MathController.cs* yönteminde düzeltin, böylece toplamı değil, ürünü döndürür:</span><span class="sxs-lookup"><span data-stu-id="8202d-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="8202d-146">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="8202d-146">Save the file.</span></span> <span data-ttu-id="8202d-147">Konsol çıktısı, `dotnet watch` bir dosya değişikliği algıladığını ve uygulamayı yeniden başlattığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="8202d-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="8202d-148">Doğru `http://localhost:<port number>/api/math/product?a=4&b=5` sonucu döndürür.</span><span class="sxs-lookup"><span data-stu-id="8202d-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="8202d-149">Testleri kullanarak çalıştırma`dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="8202d-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="8202d-150">`Product` *MathController.cs* yöntemini toplamı döndürmeye değiştirin.</span><span class="sxs-lookup"><span data-stu-id="8202d-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="8202d-151">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="8202d-151">Save the file.</span></span>
1. <span data-ttu-id="8202d-152">Komut *kabuğunda, WebAppTests* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="8202d-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="8202d-153">[Dotnet geri yükleme](/dotnet/core/tools/dotnet-restore)çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="8202d-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="8202d-154">`dotnet watch test` öğesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="8202d-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="8202d-155">Çıktısı, bir testin başarısız olduğunu ve izleyenin dosya değişikliklerini beklediğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="8202d-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="8202d-156">`Product` Yöntemi döndürecek şekilde yöntem kodunu düzeltin.</span><span class="sxs-lookup"><span data-stu-id="8202d-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="8202d-157">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="8202d-157">Save the file.</span></span>

<span data-ttu-id="8202d-158">`dotnet watch`dosya değişikliğini algılar ve testleri yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="8202d-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="8202d-159">Konsol çıkışı, geçen testleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="8202d-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="8202d-160">İzlenecek dosya listesini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="8202d-160">Customize files list to watch</span></span>

<span data-ttu-id="8202d-161">Varsayılan olarak, `dotnet-watch` aşağıdaki glob desenleri eşleşen tüm dosyaları izler:</span><span class="sxs-lookup"><span data-stu-id="8202d-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="8202d-162">*.csproj* dosyasını düzenleyerek izleme listesine daha fazla öğe eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="8202d-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="8202d-163">Öğeler tek tek veya glob desenleri kullanılarak belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="8202d-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="8202d-164">İzlenecek dosyaları devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="8202d-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="8202d-165">`dotnet-watch`varsayılan ayarlarını yoksayacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="8202d-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="8202d-166">Belirli dosyaları yoksaymak `Watch="false"` için, *.csproj* dosyasında bir öğenin tanımına özniteliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8202d-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

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

## <a name="custom-watch-projects"></a><span data-ttu-id="8202d-167">Özel saat projeleri</span><span class="sxs-lookup"><span data-stu-id="8202d-167">Custom watch projects</span></span>

<span data-ttu-id="8202d-168">`dotnet-watch`C# projeleri ile sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="8202d-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="8202d-169">Farklı senaryoları işlemek için özel izleme projeleri oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="8202d-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="8202d-170">Aşağıdaki proje düzenini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="8202d-170">Consider the following project layout:</span></span>

* <span data-ttu-id="8202d-171">**test/**</span><span class="sxs-lookup"><span data-stu-id="8202d-171">**test/**</span></span>
  * <span data-ttu-id="8202d-172">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="8202d-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="8202d-173">*EntegrasyonTestleri/EntegrasyonTestleri.csproj*</span><span class="sxs-lookup"><span data-stu-id="8202d-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="8202d-174">Amaç her iki projeyi de izlemekse, her iki projeyi de izlemek üzere yapılandırılan özel bir proje dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="8202d-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

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

<span data-ttu-id="8202d-175">Her iki projede de dosya izlemeyi başlatmak için *test* klasörüne değiştirin.</span><span class="sxs-lookup"><span data-stu-id="8202d-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="8202d-176">Aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="8202d-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="8202d-177">VSTest, her iki test projesinde de herhangi bir dosya değiştiğinde yürütülür.</span><span class="sxs-lookup"><span data-stu-id="8202d-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="8202d-178">`dotnet-watch`in GitHub</span><span class="sxs-lookup"><span data-stu-id="8202d-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="8202d-179">`dotnet-watch`GitHub [dotnet/AspNetCore deposunun bir](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="8202d-179">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
