---
title: Dosya İzleyicisi kullanarak ASP.NET Core uygulamalar geliştirme
author: rick-anderson
description: Bu öğreticide, .NET Core CLI dosya İzleyicisi (DotNet Watch) aracının bir ASP.NET Core uygulamasında nasıl yükleneceği ve kullanılacağı gösterilmektedir.
ms.author: riande
ms.date: 05/31/2018
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: 27420fe00ba6375e15b67fb359be06df055eff1f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060046"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Dosya İzleyicisi kullanarak ASP.NET Core uygulamalar geliştirme

By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch` , kaynak dosyalar değiştiğinde [.NET Core CLI](/dotnet/core/tools) komutu çalıştıran bir araçtır. Örneğin, bir dosya değişikliği derleme, test yürütmesi veya dağıtımı tetikleyebilir.

Bu öğretici, iki uç nokta ile mevcut bir Web API 'SI kullanır: bir toplamı ve bir ürünü döndüren bir tane döndürür. Ürün yönteminde, bu öğreticide düzeltilen bir hata vardır.

[Örnek uygulamayı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)indirin. İki projeden oluşur: *WebApp* (bir ASP.NET Core Web API 'si) ve *webapptests* (Web API 'si için birim testleri).

Bir komut kabuğu 'nda *WebApp* klasörüne gidin. Şu komutu çalıştırın:

```dotnetcli
dotnet run
```

> [!NOTE]
> `dotnet run --project <PROJECT>`Çalıştırmak için bir proje belirtmek üzere ' i kullanabilirsiniz. Örneğin, `dotnet run --project WebApp` örnek uygulamanın kökünden çalıştırıldığında *WebApp* projesi de çalıştırılır.

Konsol çıktısı aşağıdakine benzer iletileri gösterir (uygulamanın çalıştığını ve istekleri beklediğini gösterir):

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Bir web tarayıcısında `http://localhost:<port number>/api/math/sum?a=4&b=5` sayfasına gidin. Sonucunu görmeniz gerekir `9` .

Ürün API 'sine () gidin `http://localhost:<port number>/api/math/product?a=4&b=5` . `9`Beklenmez değil, döndürür `20` . Bu sorun öğreticide daha sonra düzeltildi.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>`dotnet watch`Projeye ekleme

`dotnet watch`Dosya İzleyici Aracı, .NET Core SDK sürüm 2.1.300 eklenir. .NET Core SDK önceki bir sürümü kullanılırken aşağıdaki adımlar gereklidir.

1. `Microsoft.DotNet.Watcher.Tools` *. Csproj* dosyasına bir paket başvurusu ekleyin:

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. `Microsoft.DotNet.Watcher.Tools`Aşağıdaki komutu çalıştırarak paketi yüklemelisiniz:

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Kullanarak .NET Core CLI komutları çalıştırma `dotnet watch`

Tüm [.NET Core CLI komutları](/dotnet/core/tools#cli-commands) ile çalıştırılabilir `dotnet watch` . Örneğin:

| Komut | İzle komutu |
| ---- | ----- |
| dotnet run | DotNet izleme çalıştırması |
| DotNet Run-f netcoreapp 3.1 | DotNet Watch çalıştırması-f netcoreapp 3.1 |
| DotNet Run-f netcoreapp 3.1----arg1 | DotNet Watch-f netcoreapp 3.1----arg1 |
| dotnet test | DotNet izleme testi |

`dotnet watch run` *WebApp* klasöründe çalıştırın. Konsol çıktısı başladığını gösterir `watch` .

::: moniker range=">= aspnetcore-5.0"
`dotnet watch run`Bir Web uygulamasında çalıştırıldığında UYGULAMANıN URL 'sine, bir kez daha sonra giden bir tarayıcı başlatılır. `dotnet watch` Bunu, uygulamanın konsol çıkışını okuyarak ve tarafından görüntülenmeye yönelik bir ileti için bekleyen yapar <xref:Microsoft.AspNetCore.WebHost> .

`dotnet watch` izlenen dosyalardaki değişiklikleri algıladığında tarayıcıyı yeniler. Bunu yapmak için, izle komutu, uygulama tarafından oluşturulan HTML yanıtlarını değiştiren bir ara yazılımı uygulamaya yerleştirir. Ara yazılım, `dotnet watch` tarayıcıya, tarayıcıyı yenilemeye izin veren bir JavaScript betik bloğu ekler. Şu anda, *. html* ve *. css* dosyaları gibi statik içerik de dahil olmak üzere tüm izlenen dosyalardaki değişiklikler uygulamanın yeniden oluşturulmasına neden olur.

`dotnet watch`:

* Yalnızca varsayılan olarak derlemeleri etkileyen dosyaları izler.
* Diğer izlenen dosyalar (yapılandırma aracılığıyla), hala derleme gerçekleşirken sonuç olarak oluşur.

Yapılandırma hakkında daha fazla bilgi için bu belgede [DotNet-Watch yapılandırması](#dotnet-watch-configuration) bölümüne bakın

::: moniker-end

> [!NOTE]
> `dotnet watch --project <PROJECT>`İzlenecek projeyi belirtmek için kullanabilirsiniz. Örneğin, `dotnet watch --project WebApp run` örnek uygulamanın kökünden çalıştırılması Ayrıca *WebApp* projesini de çalıştırır ve bu uygulamayı izleyebilir.

## <a name="make-changes-with-dotnet-watch"></a>İle değişiklik yap `dotnet watch`

Çalıştığından emin olun `dotnet watch` .

`Product` *MathController.cs* yöntemindeki hatayı düzelttikten sonra, ürünü döndürür ve toplamı değil:

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Dosyayı kaydedin. Konsol çıktısı `dotnet watch` bir dosya değişikliği olduğunu algıladı ve uygulamayı yeniden başlatın.

Verify `http://localhost:<port number>/api/math/product?a=4&b=5` doğru sonucu döndürür.

## <a name="run-tests-using-dotnet-watch"></a>Kullanarak testleri çalıştırma `dotnet watch`

1. `Product`Toplamı döndürmek için *MathController.cs* yöntemini geri değiştirin. Dosyayı kaydedin.
1. Bir komut kabuğunda *Webapptests* klasörüne gidin.
1. [DotNet restore](/dotnet/core/tools/dotnet-restore)çalıştırın.
1. Şu komutu çalıştırın: `dotnet watch test`. Çıktısı bir testin başarısız olduğunu ve izleyicinin dosya değişikliklerini beklediğini gösterir:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. `Product`Yöntem kodunu, ürünü geri döndürdüğünden düzeltir. Dosyayı kaydedin.

`dotnet watch` dosya değişikliğini algılar ve testleri yeniden çalıştırır. Konsol çıktısı, geçirilen testleri belirtir.

## <a name="customize-files-list-to-watch"></a>Dosya listesini izlemek için Özelleştir

Varsayılan olarak, `dotnet-watch` aşağıdaki glob desenleriyle eşleşen tüm dosyaları izler:

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

*. Csproj* dosyası düzenlenerek, izleme listesine daha fazla öğe eklenebilir. Öğeler tek tek veya glob desenleri kullanılarak belirtilebilir.

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>İzlenen dosyaları kabul etme

`dotnet-watch` varsayılan ayarlarını yok sayılabilecek şekilde yapılandırılabilir. Belirli dosyaları yoksaymak için `Watch="false"` özniteliği *. csproj* dosyasındaki bir öğenin tanımına ekleyin:

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

## <a name="custom-watch-projects"></a>Özel izleme projeleri

`dotnet-watch` C# projeleriyle sınırlı değildir. Özel izleme projeleri, farklı senaryoları işlemek için oluşturulabilir. Aşağıdaki proje mizanpajını göz önünde bulundurun:

* **sınamanız**
  * *UnitTests/UnitTests. csproj*
  * *Integrationtests/ıntegrationtests. csproj*

Hedef her iki projeyi de seyretmek istiyorsanız, her iki projeyi de izlemek için yapılandırılmış özel bir proje dosyası oluşturun:

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

Her iki projede de dosya izlemeye başlamak için, *Test* klasörüne geçin. Şu kodu yürütün:

```dotnetcli
dotnet watch msbuild /t:Test
```

VSTest, her iki test projesinde herhangi bir dosya değiştiğinde yürütülür.

## <a name="dotnet-watch-configuration"></a>DotNet-yapılandırmayı izle

Bazı yapılandırma seçenekleri, `dotnet watch` ortam değişkenleri aracılığıyla geçirilebilir. Kullanılabilir değişkenler şunlardır:

| Ayar  | Açıklama |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | "1" veya "true" olarak ayarlandıysa `dotnet watch` CoreFx yerine bir yoklama Dosya İzleyicisi kullanır `FileSystemWatcher` . Ağ paylaşımlarında veya Docker takılmış birimlerde dosya izlerken kullanılır.                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | Varsayılan olarak, `dotnet watch` her dosya değişikliğine geri yükleme çalıştırma veya izlenen dosyalar kümesini yeniden değerlendirme gibi belirli işlemleri önleyerek derlemeyi iyileştirir. "1" veya "true" olarak ayarlanırsa, bu iyileştirmeler devre dışı bırakılır. |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | `dotnet watch run``launchBrowser` *launchSettings.jsüzerinde* yapılandırılmış Web uygulamaları için tarayıcıları başlatmaya çalışır. "1" veya "true" olarak ayarlanırsa, bu davranış bastırılır. |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | `dotnet watch run` dosya değişikliklerini algıladığında tarayıcıları yenilemeyi dener. "1" veya "true" olarak ayarlanırsa, bu davranış bastırılır. Ayarlanırsa, bu davranış da bastırılır `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` . |

## <a name="dotnet-watch-in-github"></a>`dotnet-watch` GitHub 'da

`dotnet-watch` GitHub [DotNet/AspNetCore deposunun](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)bir parçasıdır.
