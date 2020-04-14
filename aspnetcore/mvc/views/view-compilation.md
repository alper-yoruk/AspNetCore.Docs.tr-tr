---
title: ASP.NET Core'da jilet dosya derlemesi
author: rick-anderson
description: Razor dosyalarının derlemesinin ASP.NET Core uygulamasında nasıl oluştuğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277281"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>ASP.NET Core'da jilet dosya derlemesi

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

*.cshtml* uzantılı razor [dosyaları, Razor SDK](xref:razor-pages/sdk)kullanılarak hem oluşturma hem de yayımlama zamanında derlenir. Runtime derlemesi, projenizi yapılandırarak isteğe bağlı olarak etkinleştirilebilir.

## <a name="razor-compilation"></a>Jilet derlemesi

Razor dosyalarının oluşturma zamanı ve yayımlama zamanı derlemesi varsayılan olarak Razor SDK tarafından etkinleştirilir. Etkinleştirildiğinde, runtime derlemesi yapı zamanı derlemesini tamamlayarak Razor dosyalarının düzenlenirse güncelleştirilmesini sağlar.

## <a name="enable-runtime-compilation-at-project-creation"></a>Proje oluşturmada çalışma zamanı derlemesini etkinleştirme

Razor Pages ve MVC proje şablonları, proje oluşturulduğunda çalışma zamanı derlemesini etkinleştirme seçeneğini içerir. Bu seçenek Core 3.1 ve sonraki ASP.NET desteklenir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Yeni **bir ASP.NET Core web uygulaması** oluştur iletişim kutusunda:

1. **Web Uygulaması** veya Web **Uygulaması (Model-View-Controller)** proje şablonu seçin.
1. Etkinleştir **Jilet çalışma zamanı derleme** onay kutusunu seçin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

`-rrc` Veya `--razor-runtime-compilation` şablon seçeneğini kullanın. Örneğin, aşağıdaki komut, çalışma zamanı derlemesi etkin leştirilmiş yeni bir Razor Pages projesi oluşturur:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Varolan bir projede çalışma zamanı derlemesini etkinleştirme

Varolan bir projedeki tüm ortamlar için çalışma zamanı derlemesini etkinleştirmek için:

1. [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketini yükleyin.
1. Bir çağrı içerecek `Startup.ConfigureServices` şekilde projenin yöntemini <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>güncelleştirin. Örneğin:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Varolan bir projede koşullu olarak çalışma zamanı derlemesini etkinleştirin

Çalışma zamanı derlemesi, yalnızca yerel geliştirme için kullanılabilecek şekilde etkinleştirilebilir. Bu şekilde koşullu olarak etkinleştirmek, yayımlanmış çıktının aşağıdakileri sağlamasını sağlar:

* Derlenmiş görünümleri kullanır.
* Üretimde dosya izleyicilerine izin vermez.

Yalnızca Geliştirme ortamında çalışma zamanı derlemesini etkinleştirmek için:

1. [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketini yükleyin.
1. `environmentVariables` *launchSettings.json'daki başlatma*profili bölümünü değiştirin:
    * Doğrula' `ASPNETCORE_ENVIRONMENT` `"Development"`ya ' olarak ayarlanır.
    * `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`Ayarlayın. `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`

Aşağıdaki örnekte, çalışma zamanı derlemesi geliştirme ortamında `IIS Express` `RazorPagesApp` ve başlatma profilleri için etkinleştirilir:

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

Projenin `Startup` sınıfında kod değişikliği gerekmez. Çalışma zamanında, ASP.NET Core' da derleme düzeyinde bir `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` [HostingStartup özniteliği](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) arar. Öznitelik, `HostingStartup` yürütülecek uygulama başlangıç kodunu belirtir. Bu başlangıç kodu çalışma zamanı derlemesini sağlar.

## <a name="additional-resources"></a>Ek kaynaklar

* [RazorCompileOnBuild ve RazorCompileOnPublish](xref:razor-pages/sdk#properties) özellikleri.
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Projeler arasında çalışma zamanı derleme çalışması yaptığını gösteren bir örnek için [GitHub'daki çalışma zamanı derleme örneğine](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) bakın.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

*.cshtml* uzantılı razor [dosyaları, Razor SDK](xref:razor-pages/sdk)kullanılarak hem oluşturma hem de yayımlama zamanında derlenir. Çalışma süresi derlemesi, uygulamanızı yapılandırarak isteğe bağlı olarak etkinleştirilebilir.

## <a name="razor-compilation"></a>Jilet derlemesi

Razor dosyalarının oluşturma zamanı ve yayımlama zamanı derlemesi varsayılan olarak Razor SDK tarafından etkinleştirilir. Etkinleştirildiğinde, runtime derlemesi yapı zamanı derlemesini tamamlayarak Razor dosyalarının düzenlenirse güncelleştirilmesini sağlar.

## <a name="runtime-compilation"></a>Çalışma zamanı derlemesi

Tüm ortamlar ve yapılandırma modları için çalışma zamanı derlemesini etkinleştirmek için:

1. [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet paketini yükleyin.

1. Bir çağrı içerecek `Startup.ConfigureServices` şekilde projenin yöntemini <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>güncelleştirin. Örneğin:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Koşullu etkinleştirme çalışma zamanı derlemesi

Çalışma zamanı derlemesi, yalnızca yerel geliştirme için kullanılabilecek şekilde etkinleştirilebilir. Bu şekilde koşullu olarak etkinleştirmek, yayımlanmış çıktının aşağıdakileri sağlamasını sağlar:

* Derlenmiş görünümleri kullanır.
* Boyutu daha küçüktür.
* Üretimde dosya izleyicilerine izin vermez.

Ortam ve yapılandırma moduna göre çalışma zamanı derlemesini etkinleştirmek için:

1. Etkin `Configuration` değere dayalı [olarak Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) paketine koşullu olarak başvurun:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Bir çağrı içerecek `Startup.ConfigureServices` şekilde projenin yöntemini `AddRazorRuntimeCompilation`güncelleştirin. Koşullu `AddRazorRuntimeCompilation` olarak, `ASPNETCORE_ENVIRONMENT` yalnızca değişken ayarlandığında Hata Ayıklama `Development`modunda çalışır:

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Ek kaynaklar

* [RazorCompileOnBuild ve RazorCompileOnPublish](xref:razor-pages/sdk#properties) özellikleri.
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Projeler arasında çalışma zamanı derleme çalışması yaptığını gösteren bir örnek için [GitHub'daki çalışma zamanı derleme örneğine](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) bakın.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

İlgili Razor Page veya MVC görünümü çağrıldığı çalışma zamanında bir Razor dosyası derlenir. Razor [dosyaları, Razor SDK](xref:razor-pages/sdk)kullanılarak hem oluşturma hem de yayımlama zamanında derlenir.

## <a name="razor-compilation"></a>Jilet derlemesi

Razor dosyalarının oluşturma ve yayımlama zamanı derlemesi varsayılan olarak Razor SDK tarafından etkinleştirilir. Razor dosyalarının güncelleştirildikten sonra düzenlenmesi yapı zamanında desteklenir. Varsayılan olarak, yalnızca derlenmiş *Views.dll* ve razor dosyalarını derlemek için gereken *.cshtml* dosyaları veya referans derlemeleri uygulamanızla birlikte dağıtılır.

> [!IMPORTANT]
> Precompilation aracı amortismana kaldırılmıştır ve Core 3.0 ASP.NET kaldırılacaktır. [Razor Sdk'ya](xref:razor-pages/sdk)göç etmenizi öneririz.
>
> Razor SDK, yalnızca proje dosyasında precompilation'a özgü özellikler ayarlanmadığında etkilidir. Örneğin, *.csproj* dosyasının `MvcRazorCompileOnPublish` özelliğini `true` Razor SDK'yı devre dışı kılabilir şekilde ayarlamak.

## <a name="runtime-compilation"></a>Çalışma zamanı derlemesi

Oluşturma zamanı derlemesi, Razor dosyalarının çalışma zamanı derlemesi ile desteklenir. ASP.NET Core MVC, *.cshtml* dosyasının içeriği değiştiğinde Razor dosyalarını yeniden derler.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
