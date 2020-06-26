---
title: RazorASP.NET Core 'de dosya derleme
author: rick-anderson
description: ASP.NET Core uygulamasında dosya derlemesinin nasıl Razor oluştuğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/view-compilation
ms.openlocfilehash: 71487ff2d5d7d7cf96835778f386e5f30fa32254
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405451"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>RazorASP.NET Core 'de dosya derleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Razor*. cshtml* uzantılı dosyalar, [ Razor SDK](xref:razor-pages/sdk)kullanılarak hem derleme hem de yayımlama zamanında derlenir. Çalışma zamanı derlemesi, projeniz yapılandırılarak isteğe bağlı olarak etkinleştirilebilir.

## <a name="razor-compilation"></a>Razorderleme

Dosyaların derleme zamanı ve yayımlama zamanı derlemesi, Razor SDK tarafından varsayılan olarak etkindir Razor . Etkinleştirildiğinde, çalışma zamanı derlemesi derleme zamanı derlemesini tamamlar ve bu Razor dosyalar düzenlendiklerinde güncelleştirilmesini sağlar.

## <a name="enable-runtime-compilation-at-project-creation"></a>Proje oluşturulurken çalışma zamanı derlemesini etkinleştir

RazorSayfalar ve MVC proje şablonları, proje oluşturulduğunda çalışma zamanı derlemesini etkinleştirme seçeneğini içerir. Bu seçenek ASP.NET Core 3,1 ve üzeri sürümlerde desteklenir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Yeni ASP.NET Core Web uygulaması oluştur** iletişim kutusunda:

1. **Web uygulaması** veya **Web uygulaması (Model-View-Controller)** proje şablonunu seçin.
1. ** Razor Çalışma zamanı derlemesini etkinleştir** onay kutusunu seçin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

`-rrc`Veya `--razor-runtime-compilation` şablon seçeneğini kullanın. Örneğin, aşağıdaki komut Razor çalışma zamanı derlemesi etkin olan yeni bir sayfa projesi oluşturur:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Mevcut bir projede çalışma zamanı derlemesini etkinleştir

Mevcut bir projedeki tüm ortamlarda çalışma zamanı derlemesini etkinleştirmek için:

1. [Microsoft. AspNetCore. Mvc 'yi yükler. Razor RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketi.
1. `Startup.ConfigureServices`İçin bir çağrı içerecek şekilde projenin metodunu güncelleştirin <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> . Örneğin:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Mevcut bir projede çalışma zamanı derlemesini koşullu olarak etkinleştir

Çalışma zamanı derlemesi, yalnızca yerel geliştirme için kullanılabilir olacak şekilde etkinleştirilebilir. Bu şekilde koşullu etkinleştirme, yayımlanan çıktının olmasını sağlar:

* Derlenmiş görünümleri kullanır.
* , İzleyicileri dosyasını üretimde etkinleştirmez.

Çalışma zamanı derlemesini yalnızca geliştirme ortamında etkinleştirmek için:

1. [Microsoft. AspNetCore. Mvc 'yi yükler. Razor RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketi.
1. `environmentVariables` *ÜzerindelaunchSettings.js*başlatma profili bölümünü değiştirin:
    * Verify `ASPNETCORE_ENVIRONMENT` olarak ayarlanır `"Development"` .
    * `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`Olarak ayarlayın `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` .

Aşağıdaki örnekte, `IIS Express` ve başlatma profillerinin geliştirme ortamında çalışma zamanı derlemesi etkinleştirilmiştir `RazorPagesApp` :

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

Projenin sınıfında kod değişikliği yapılması gerekmez `Startup` . Çalışma zamanında, içinde [derleme düzeyi HostingStartup özniteliğini](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) arar ASP.NET Core `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` . `HostingStartup`Öznitelik, çalıştırılacak uygulama başlangıç kodunu belirtir. Bu başlangıç kodu, çalışma zamanı derlemesini sunar.

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a>Sınıf kitaplığı için çalışma zamanı derlemesini Etkinleştir Razor

Bir Razor sayfa projesinin *myclasslib*adlı bir [ Razor sınıf kitaplığına (RCL)](xref:razor-pages/ui-class) başvurduğu bir senaryo düşünün. RCL, tüm takımınızın MVC ve sayfa projelerinin tükettiği bir *_Layout. cshtml* dosyası içerir Razor . Bu RCL 'de *_Layout. cshtml* dosyası için çalışma zamanı derlemesini etkinleştirmek istiyorsunuz. Sayfalar projesinde aşağıdaki değişiklikleri yapın Razor :

1. [Mevcut bir projede çalışma zamanı derlemesini koşullu olarak etkinleştirme](#conditionally-enable-runtime-compilation-in-an-existing-project)yönergelerini kullanarak çalışma zamanı derlemesini etkinleştirin.
1. Çalışma zamanı derleme seçeneklerini şu şekilde yapılandırın `Startup.ConfigureServices` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    Yukarıdaki kodda, *Myclasslib* RCL için mutlak bir yol oluşturulur. Bu mutlak yoldaki dizinleri ve dosyaları bulmak için [Physicalfileprovider API 'si](xref:fundamentals/file-providers#physicalfileprovider) kullanılır. Son olarak, `PhysicalFileProvider` örnek, RCL 'nin *. cshtml* dosyalarına erişime izin veren bir dosya sağlayıcıları koleksiyonuna eklenir.

## <a name="additional-resources"></a>Ek kaynaklar

* [RazorCompileOnBuild ve RazorCompileOnPublish](xref:razor-pages/sdk#properties) özellikleri.
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razor*. cshtml* uzantılı dosyalar, [ Razor SDK](xref:razor-pages/sdk)kullanılarak hem derleme hem de yayımlama zamanında derlenir. Çalışma zamanı derlemesi, uygulamanız yapılandırılarak isteğe bağlı olarak etkinleştirilebilir.

## <a name="razor-compilation"></a>Razorderleme

Dosyaların derleme zamanı ve yayımlama zamanı derlemesi, Razor SDK tarafından varsayılan olarak etkindir Razor . Etkinleştirildiğinde, çalışma zamanı derlemesi derleme zamanı derlemesini tamamlar ve bu Razor dosyalar düzenlendiklerinde güncelleştirilmesini sağlar.

## <a name="runtime-compilation"></a>Çalışma zamanı derlemesi

Tüm ortamlar ve yapılandırma modları için çalışma zamanı derlemesini etkinleştirmek için:

1. [Microsoft. AspNetCore. Mvc 'yi yükler. Razor RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketi.

1. `Startup.ConfigureServices`İçin bir çağrı içerecek şekilde projenin metodunu güncelleştirin <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> . Örneğin:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Çalışma zamanı derlemesini koşullu olarak etkinleştir

Çalışma zamanı derlemesi, yalnızca yerel geliştirme için kullanılabilir olacak şekilde etkinleştirilebilir. Bu şekilde koşullu etkinleştirme, yayımlanan çıktının olmasını sağlar:

* Derlenmiş görünümleri kullanır.
* Boyutu küçüktür.
* , İzleyicileri dosyasını üretimde etkinleştirmez.

Çalışma zamanı derlemesini ortam ve yapılandırma moduna göre etkinleştirmek için:

1. Koşullu olarak [Microsoft. AspNetCore. Mvc öğesine başvurun. Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Etkin değere göre RuntimeCompilation paketi `Configuration` :

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. `Startup.ConfigureServices`İçin bir çağrı içerecek şekilde projenin metodunu güncelleştirin `AddRazorRuntimeCompilation` . Koşullu olarak Execute `AddRazorRuntimeCompilation` yalnızca `ASPNETCORE_ENVIRONMENT` değişken olarak ayarlandığında hata ayıklama modunda çalışır `Development` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Ek kaynaklar

* [RazorCompileOnBuild ve RazorCompileOnPublish](xref:razor-pages/sdk#properties) özellikleri.
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Çalışma zamanı derleme işini projeler arasında yapmayı gösteren bir örnek için bkz. [GitHub üzerinde çalışma zamanı derleme örneği](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

RazorÇalışma zamanında, ilişkili Razor sayfa veya MVC görünümü çağrıldığında bir dosya derlenir. Razordosyalar, [ Razor SDK](xref:razor-pages/sdk)kullanılarak hem derlemede hem de yayımlama zamanında derlenir.

## <a name="razor-compilation"></a>Razorderleme

Dosyaların derleme ve yayımlama zamanı derlemesi, Razor SDK tarafından varsayılan olarak etkindir Razor . RazorDosyalar güncelleştirildikten sonra düzenlendikten sonra derleme sırasında desteklenir. Varsayılan olarak, yalnızca derlenmiş *Views.dll* ve No *. cshtml* dosyası ya da dosyaları derlemek için gerekli derlemeler Razor uygulamanız ile dağıtılır.

> [!IMPORTANT]
> Ön derleme aracı kullanım dışı bırakılmıştır ve ASP.NET Core 3,0 ' de kaldırılacak. [ Razor SDK](xref:razor-pages/sdk)'ya geçiş yapmanızı öneririz.
>
> RazorSDK yalnızca proje dosyasında ön derleme özgü hiçbir özellik ayarlanmamışsa etkilidir. Örneğin, *. csproj* dosyasının `MvcRazorCompileOnPublish` özelliğini, `true` SDK 'yı devre dışı bırakmak için ayarlama Razor .

## <a name="runtime-compilation"></a>Çalışma zamanı derlemesi

Derleme zamanı derlemesi, dosyaların çalışma zamanı derlemesi tarafından takıma alınmıştır Razor . ASP.NET Core MVC Razor , bir *. cshtml* dosyasının içeriği değiştiğinde dosyaları yeniden derleyerek.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
