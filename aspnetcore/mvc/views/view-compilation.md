---
title: ASP.NET Core'da jilet dosya derlemesi
author: rick-anderson
description: Razor dosyalarının derlemesinin ASP.NET Core uygulamasında nasıl oluştuğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 0afd39fdb5a6f570e0e78ad54f6c436460bad3a6
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223965"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>ASP.NET Core'da jilet dosya derlemesi

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

*.cshtml* uzantılı razor [dosyaları, Razor SDK](xref:razor-pages/sdk)kullanılarak hem oluşturma hem de yayımlama zamanında derlenir. Çalışma süresi derlemesi, uygulamanızı yapılandırarak isteğe bağlı olarak etkinleştirilebilir.

## <a name="razor-compilation"></a>Jilet derlemesi

Razor dosyalarının oluşturma ve yayımlama zamanı derlemesi varsayılan olarak Razor SDK tarafından etkinleştirilir. Etkinleştirildiğinde, runtime derlemesi yapı zamanı derlemesini tamamlayarak Razor dosyalarının düzenlenirse güncelleştirilmesini sağlar.

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

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

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
