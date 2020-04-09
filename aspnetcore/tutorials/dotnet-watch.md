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
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Dosya izleyicisi kullanarak ASP.NET Core uygulamaları geliştirme

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

[dotnet watch,](https://www.nuget.org/packages/dotnet-watch) kaynak dosyaları değiştiğinde [.NET Core CLI](/dotnet/core/tools) komutunu çalıştıran bir araçtır. Örneğin, bir dosya değişikliği derlemeyi, test yürütmeyi veya dağıtımı tetikleyebilir.

Bu öğretici, iki uç noktaile varolan bir web API kullanır: biri bir toplamı döndürür ve bir ürün döndürür. Ürün yöntemi, bu öğreticide sabit bir hata vardır.

Örnek [uygulamayı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)indirin. İki projeden oluşur: *WebApp* (ASP.NET Core web API) ve *WebAppTests* (web API için birim testleri).

Komut kabuğunda *WebApp* klasörüne gidin. Şu komutu çalıştırın:

```dotnetcli
dotnet run
```

> [!NOTE]
> Çalışacak bir `dotnet run --project <PROJECT>` proje belirtmek için kullanabilirsiniz. Örneğin, örnek `dotnet run --project WebApp` uygulamanın kökünden çalıştırmak da *WebApp* projesini çalıştıracaktır.

Konsol çıkışı aşağıdakine benzer iletileri gösterir (uygulamanın çalıştığını ve istekleri beklediğini gösterir):

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Bir web tarayıcısında `http://localhost:<port number>/api/math/sum?a=4&b=5` sayfasına gidin. Sonucu `9`görmelisiniz.

Ürün API'sine`http://localhost:<port number>/api/math/product?a=4&b=5`gidin ( ). Beklediğiniz `9`gibi `20` değil, geri dönüyor. Bu sorun daha sonra öğretici de giderilir.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Projeye `dotnet watch` ekleme

Dosya `dotnet watch` izleme aracı .NET Core SDK'nın 2.1.300 sürümüyle birlikte verilir. .NET Core SDK'nın önceki bir sürümünü kullanırken aşağıdaki adımlar gereklidir.

1. `Microsoft.DotNet.Watcher.Tools` *.csproj* dosyasına bir paket başvurusu ekleyin:

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. Aşağıdaki `Microsoft.DotNet.Watcher.Tools` komutu çalıştırarak paketi yükleyin:

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Kullanarak .NET Core CLI komutlarını çalıştırın`dotnet watch`

Herhangi bir [.NET Core CLI komutu](/dotnet/core/tools#cli-commands) ile `dotnet watch`çalıştırılabilir. Örneğin:

| Komut | Saat ile komut |
| ---- | ----- |
| dotnet run | dotnet izle koş |
| dotnet çalıştır -f netcoreapp2.0 | dotnet izle run -f netcoreapp2.0 |
| dotnet koşmak -f netcoreapp2.0 -- --arg1 | dotnet izle run -f netcoreapp2.0 -- --arg1 |
| dotnet test | dotnet izle testi |

`dotnet watch run` *WebApp* klasöründe çalıştırın. Konsol çıkışı `watch` nın başladığını gösterir.

> [!NOTE]
> İzlenecek `dotnet watch --project <PROJECT>` bir proje belirtmek için kullanabilirsiniz. Örneğin, örnek `dotnet watch --project WebApp run` uygulamanın kökünden çalışan da çalışacak ve *WebApp* projesini izlemek.

## <a name="make-changes-with-dotnet-watch"></a>Değişiklik yapın`dotnet watch`

Koştuğundan emin `dotnet watch` ol.

Hatayı `Product` *MathController.cs* yönteminde düzeltin, böylece toplamı değil, ürünü döndürür:

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Dosyayı kaydedin. Konsol çıktısı, `dotnet watch` bir dosya değişikliği algıladığını ve uygulamayı yeniden başlattığını gösterir.

Doğru `http://localhost:<port number>/api/math/product?a=4&b=5` sonucu döndürür.

## <a name="run-tests-using-dotnet-watch"></a>Testleri kullanarak çalıştırma`dotnet watch`

1. `Product` *MathController.cs* yöntemini toplamı döndürmeye değiştirin. Dosyayı kaydedin.
1. Komut *kabuğunda, WebAppTests* klasörüne gidin.
1. [Dotnet geri yükleme](/dotnet/core/tools/dotnet-restore)çalıştırın.
1. `dotnet watch test` öğesini çalıştırın. Çıktısı, bir testin başarısız olduğunu ve izleyenin dosya değişikliklerini beklediğini gösterir:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. `Product` Yöntemi döndürecek şekilde yöntem kodunu düzeltin. Dosyayı kaydedin.

`dotnet watch`dosya değişikliğini algılar ve testleri yeniden çalıştırın. Konsol çıkışı, geçen testleri gösterir.

## <a name="customize-files-list-to-watch"></a>İzlenecek dosya listesini özelleştirme

Varsayılan olarak, `dotnet-watch` aşağıdaki glob desenleri eşleşen tüm dosyaları izler:

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

*.csproj* dosyasını düzenleyerek izleme listesine daha fazla öğe eklenebilir. Öğeler tek tek veya glob desenleri kullanılarak belirtilebilir.

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>İzlenecek dosyaları devre dışı bırakma

`dotnet-watch`varsayılan ayarlarını yoksayacak şekilde yapılandırılabilir. Belirli dosyaları yoksaymak `Watch="false"` için, *.csproj* dosyasında bir öğenin tanımına özniteliği ekleyin:

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

## <a name="custom-watch-projects"></a>Özel saat projeleri

`dotnet-watch`C# projeleri ile sınırlı değildir. Farklı senaryoları işlemek için özel izleme projeleri oluşturulabilir. Aşağıdaki proje düzenini göz önünde bulundurun:

* **test/**
  * *UnitTests/UnitTests.csproj*
  * *EntegrasyonTestleri/EntegrasyonTestleri.csproj*

Amaç her iki projeyi de izlemekse, her iki projeyi de izlemek üzere yapılandırılan özel bir proje dosyası oluşturun:

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

Her iki projede de dosya izlemeyi başlatmak için *test* klasörüne değiştirin. Aşağıdaki komutu yürütün:

```dotnetcli
dotnet watch msbuild /t:Test
```

VSTest, her iki test projesinde de herhangi bir dosya değiştiğinde yürütülür.

## <a name="dotnet-watch-in-github"></a>`dotnet-watch`in GitHub

`dotnet-watch`GitHub [dotnet/AspNetCore deposunun bir](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)parçasıdır.
