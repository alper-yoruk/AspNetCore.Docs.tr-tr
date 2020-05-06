---
title: ASP.NET Core Razor ile sınıf kitaplıklarında yeniden kullanılabilir kullanıcı arabirimi
author: Rick-Anderson
description: ASP.NET Core bir sınıf kitaplığında kısmi Razor görünümler kullanarak yeniden kullanılabilir kullanıcı arabirimi oluşturmayı açıklar.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/ui-class
ms.openlocfilehash: 2c2a2c1e13b2d511ecf8c1c02c235192861fd486
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774281"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>ASP.NET Core 'de Razor Sınıf Kitaplığı projesini kullanarak yeniden kullanılabilir kullanıcı arabirimi oluşturma

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Razor görünümleri, sayfalar, denetleyiciler, sayfa modelleri, [Razor bileşenleri](xref:blazor/class-libraries), [Görünüm bileşenleri](xref:mvc/views/view-components)ve veri modelleri Razor sınıf kitaplığı 'nda (RCL) yerleşik olarak bulunabilir. RCL paketlenebilir ve yeniden kullanılabilir. Uygulamalar RCL 'yi içerebilir ve içerdiği görünümleri ve sayfaları geçersiz kılabilir. Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya Razor sayfası bulunduğunda, Web uygulamasındaki Razor biçimlendirmesi (*. cshtml* dosyası) önceliklidir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Razor Kullanıcı arabirimi içeren bir sınıf kitaplığı oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio 'dan **Yeni bir proje oluştur**' u seçin.
* **Razor sınıfı kitaplığı** > **İleri ' yi**seçin.
* Kitaplığı adlandırın (örneğin, "RazorClassLib"), > **Oluştur**. Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views`.
* Görünümleri desteketmeniz gerekiyorsa **destek sayfaları ve görünümleri '** ni seçin. Varsayılan olarak yalnızca Razor Pages desteklenir. **Oluştur**’u seçin.

Razor sınıf kitaplığı (RCL) şablonu varsayılan olarak Razor bileşen geliştirmeyi varsayılan olarak belirler. **Destek sayfaları ve görünümleri** seçeneği sayfaları ve görünümleri destekler.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Komut satırından komutunu çalıştırın `dotnet new razorclasslib`. Örneğin:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Razor sınıf kitaplığı (RCL) şablonu varsayılan olarak Razor bileşen geliştirmeyi varsayılan olarak belirler. Sayfalar ve `--support-pages-and-views` görünümler için`dotnet new razorclasslib --support-pages-and-views`destek sağlamak üzere seçeneğini () geçirin.

Daha fazla bilgi için bkz. [DotNet New](/dotnet/core/tools/dotnet-new). Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views`.

---

RCL 'ye Razor dosyaları ekleyin.

ASP.NET Core şablonları RCL içeriğinin *Areas* klasöründe olduğunu varsayar. ' De `~/Pages` içeriğini kullanıma sunan bir RCL oluşturmak Için [RCL Pages düzenine](#rcl-pages-layout) bakın. `~/Areas/Pages`

## <a name="reference-rcl-content"></a>RCL içeriğine başvur

RCL 'ye şu şekilde başvurulabilir:

* NuGet paketi. Bkz. [NuGet paketleri oluşturma](/nuget/create-packages/creating-a-package) ve [DotNet paket ekleme](/dotnet/core/tools/dotnet-add-package) ve [bir NuGet paketi oluşturma ve yayımlama](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}. csproj*. Bkz. [DotNet-başvuru Ekle](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Görünümleri, kısmi görünümleri ve sayfaları geçersiz kıl

Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya Razor sayfası bulunduğunda, Web uygulamasındaki Razor biçimlendirmesi (*. cshtml* dosyası) önceliklidir. Örneğin, *WebApp1/Areas/MyFeature/Pages/Sayfa1. cshtml* öğesini WebApp1 öğesine ekleyin ve WebApp1 içindeki Sayfa1, RCL 'deki Sayfa1 'e göre öncelikli olur.

Örnek indirme içinde *WebApp1/Areas/MyFeature2* öğesini *WebApp1/Areas/myfeature* olarak yeniden adlandırın ve test önceliğini belirtin.

*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message. cshtml* kısmi görünümünü *WebApp1/Areas/Myfeature/Pages/Shared/_Message. cshtml*'ye kopyalayın. Biçimlendirmeyi yeni konumu belirtecek şekilde güncelleştirin. Uygulamanın kısmi sürümünün kullanılmakta olduğunu doğrulamak için uygulamayı derleyin ve çalıştırın.

### <a name="rcl-pages-layout"></a>RCL sayfaları düzeni

RCL içeriğine, Web uygulamasının *Sayfalar* klasörünün bir parçası olmasına rağmen başvurmak için, aşağıdaki dosya yapısıyla RCL projesini oluşturun:

* *RazorUIClassLib/sayfalar*
* *RazorUIClassLib/sayfalar/paylaşılan*

*RazorUIClassLib/Pages/Shared* iki kısmi dosya içerir: *_Header. cshtml* ve *_Footer. cshtml*. `<partial>` Etiketler *_Layout. cshtml* dosyasına eklenebilir:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>Statik varlıklar içeren bir RCL oluşturma

RCL, RCL 'nin RCL veya tüketen uygulaması tarafından başvurulabilen, yardımcı statik varlıklar gerektirebilir. ASP.NET Core, tüketen bir uygulama tarafından kullanılabilen statik varlıkları içeren RCLs oluşturulmasına izin verir.

Yardımcı varlıkları RCL 'nin bir parçası olarak dahil etmek için, sınıf kitaplığında bir *Wwwroot* klasörü oluşturun ve gerekli dosyaları bu klasöre ekleyin.

RCL 'yi paketleyerek, *Wwwroot* klasöründeki tüm yardımcı varlıklar pakete otomatik olarak eklenir.

### <a name="exclude-static-assets"></a>Statik varlıkları hariç tut

Statik varlıkları dışlamak için, istenen dışlama yolunu proje dosyasındaki `$(DefaultItemExcludes)` özellik grubuna ekleyin. Girişleri noktalı virgül (`;`) ile ayırın.

Aşağıdaki örnekte, *Wwwroot* klasöründeki *lib. css* stil sayfası statik bir varlık olarak değerlendirilmez ve yayımlanan RCL 'ye dahil değildir:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>TypeScript tümleştirmesi

TypeScript dosyalarını RCL 'ye eklemek için:

1. TypeScript dosyalarını (*. TS*) *Wwwroot* klasörünün dışına yerleştirin. Örneğin, dosyaları bir *istemci* klasörüne yerleştirin.

1. *Wwwroot* klasörü için TypeScript derleme çıkışını yapılandırın. Proje dosyasındaki `TypescriptOutDir` öğesinin `PropertyGroup` içindeki özelliğini ayarlayın:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Proje dosyasında bir `PropertyGroup` öğesinin içine aşağıdaki hedefi ekleyerek TypeScript `ResolveCurrentProjectStaticWebAssets` hedefini hedefin bağımlılığı olarak ekleyin:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Başvurulan bir RCL 'den içerik tüketme

RCL 'nin *Wwwroot* klasörüne eklenen dosyalar, ön ek `_content/{LIBRARY NAME}/`altında RCL veya tüketen uygulamaya sunulur. Örneğin, *Razor. Class. lib* adlı bir kitaplık, konumundaki `_content/Razor.Class.Lib/`statik içeriğe bir yol ile sonuçlanır. Bir NuGet paketi üretilirken ve derleme adı paket KIMLIĞIYLE aynı değilse, için `{LIBRARY NAME}`paket kimliğini kullanın.

Tüketen uygulama `<script>`, `<style>` `<img>`,, ve diğer HTML etiketleriyle kitaplık tarafından sunulan statik varlıklara başvurur. Kullanan uygulamada [statik dosya desteğinin](xref:fundamentals/static-files) etkinleştirilmesi gerekir `Startup.Configure`:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Yapı çıktısından (`dotnet run`) kullanan uygulamayı çalıştırırken, varsayılan olarak, statik Web varlıkları geliştirme ortamında etkinleştirilmiştir. Derleme çıktılarından çalışırken diğer ortamlardaki varlıkları desteklemek için, *program.cs*içindeki konak `UseStaticWebAssets` Oluşturucu 'da çağırın:

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

Yayımlanan `UseStaticWebAssets` çıktısından (`dotnet publish`) bir uygulama çalıştırılırken çağırma gerekmez.

### <a name="multi-project-development-flow"></a>Çoklu projeli geliştirme akışı

Kullanan uygulama şu şekilde çalışır:

* RCL içindeki varlıklar özgün klasörlerinde kalır. Varlıklar, tüketim uygulamasına taşınmaz.
* RCL 'nin *Wwwroot* klasörü içindeki tüm değişiklikler, RCL yeniden oluşturulduktan ve tüketen uygulamayı yeniden oluşturmadan önce tüketen uygulamaya yansıtılır.

RCL yapılandırıldığında, statik Web varlık konumlarını açıklayan bir bildirim oluşturulur. Tüketen uygulama, başvurulan proje ve paketlerden varlıkları kullanmak için çalışma zamanında bildirimi okur. Bir RCL 'ye yeni bir varlık eklendiğinde, bir uygulamanın yeni varlığa erişebilmesi için bildirim güncellemek üzere RCL 'nin yeniden oluşturulması gerekir.

### <a name="publish"></a>Yayımlama

Uygulama yayımlandığında, tüm başvurulan projeler ve paketlerin yardımcı varlıkları altında `_content/{LIBRARY NAME}/`yayımlanan uygulamanın *Wwwroot* klasörüne kopyalanır.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor görünümleri, sayfalar, denetleyiciler, sayfa modelleri, [Razor bileşenleri](xref:blazor/class-libraries), [Görünüm bileşenleri](xref:mvc/views/view-components)ve veri modelleri Razor sınıf kitaplığı 'nda (RCL) yerleşik olarak bulunabilir. RCL paketlenebilir ve yeniden kullanılabilir. Uygulamalar RCL 'yi içerebilir ve içerdiği görünümleri ve sayfaları geçersiz kılabilir. Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya Razor sayfası bulunduğunda, Web uygulamasındaki Razor biçimlendirmesi (*. cshtml* dosyası) önceliklidir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Razor Kullanıcı arabirimi içeren bir sınıf kitaplığı oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.
* **ASP.NET Core Web uygulaması**' nı seçin.
* Kitaplığı adlandırın (örneğin, "RazorClassLib") > **Tamam**. Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views`.
* **ASP.NET Core 2,1** veya sonraki bir sürümü seçildiğini doğrulayın.
* **Razor sınıfı kitaplığı** > **Tamam ' ı**seçin.

RCL aşağıdaki proje dosyasına sahiptir:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Komut satırından komutunu çalıştırın `dotnet new razorclasslib`. Örneğin:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Daha fazla bilgi için bkz. [DotNet New](/dotnet/core/tools/dotnet-new). Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views`.

---

RCL 'ye Razor dosyaları ekleyin.

ASP.NET Core şablonları RCL içeriğinin *Areas* klasöründe olduğunu varsayar. ' De `~/Pages` içeriğini kullanıma sunan bir RCL oluşturmak Için [RCL Pages düzenine](#rcl-pages-layout) bakın. `~/Areas/Pages`

## <a name="reference-rcl-content"></a>RCL içeriğine başvur

RCL 'ye şu şekilde başvurulabilir:

* NuGet paketi. Bkz. [NuGet paketleri oluşturma](/nuget/create-packages/creating-a-package) ve [DotNet paket ekleme](/dotnet/core/tools/dotnet-add-package) ve [bir NuGet paketi oluşturma ve yayımlama](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}. csproj*. Bkz. [DotNet-başvuru Ekle](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>İzlenecek yol: bir Razor Pages projesinden bir RCL projesi oluşturma ve kullanma

[Tüm projeyi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) indirebilir ve oluşturmak yerine test edebilirsiniz. Örnek indirme, projenin test olmasını kolaylaştıran ek kod ve bağlantılar içerir. Yükleme örnekleri ve adım adım yönergeler hakkındaki açıklamalarınızla [Bu GitHub sorunuyla](https://github.com/dotnet/AspNetCore.Docs/issues/6098) ilgili geri bildirimde bulunun.

### <a name="test-the-download-app"></a>İndirme uygulamasını test etme

Tamamlanmış uygulamayı indirmediyseniz ve gözden geçirme projesi oluşturmak istiyorsanız, [sonraki bölüme](#create-an-rcl)atlayın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio 'da *. sln* dosyasını açın. Uygulamayı çalıştırın.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

*CLI* dizinindeki bir komut isteminden RCL ve Web uygulamasını oluşturun.

```dotnetcli
dotnet build
```

*WebApp1* dizinine gidin ve uygulamayı çalıştırın:

```dotnetcli
dotnet run
```

---

[Test WebApp1](#test-webapp1) içindeki yönergeleri izleyin

## <a name="create-an-rcl"></a>RCL oluşturma

Bu bölümde bir RCL oluşturulur. Razor dosyaları RCL 'ye eklenir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

RCL projesini oluşturun:

* Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.
* **ASP.NET Core Web uygulaması**' nı seçin.
* Uygulamayı **RazorUIClassLib** > **Tamam**olarak adlandırın.
* **ASP.NET Core 2,1** veya sonraki bir sürümü seçildiğini doğrulayın.
* **Razor sınıfı kitaplığı** > **Tamam ' ı**seçin.
* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message. cshtml*adlı bir Razor kısmi görünüm dosyası ekleyin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Komut satırından aşağıdakileri çalıştırın:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Önceki komutlar:

* `RazorUIClassLib` RCL 'yi oluşturur.
* Razor _Message sayfası oluşturur ve RCL 'ye ekler. `-np` Parametresi, sayfasını bir `PageModel`olmadan oluşturur.
* Bir [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) dosyası oluşturur ve RCL 'ye ekler.

*_ViewStart. cshtml* dosyası, Razor Pages projenin (bir sonraki bölüme eklenen) düzeninin yerleşimini kullanmak için gereklidir.

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Projeye Razor dosyaları ve klasörleri ekleme

* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message. cshtml* içindeki biçimlendirmeyi aşağıdaki kodla değiştirin:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* *RazorUIClassLib/Areas/MyFeature/Pages/Sayfa1. cshtml* içindeki biçimlendirmeyi aşağıdaki kodla değiştirin:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`kısmi görünümü kullanmak için gerekir (`<partial name="_Message" />`). `@addTagHelper` Yönergesini eklemek yerine bir *_ViewImports. cshtml* dosyası ekleyebilirsiniz. Örneğin:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  *_ViewImports. cshtml*hakkında daha fazla bilgi için bkz. [paylaşılan yönergeleri içeri aktarma](xref:mvc/views/layout#importing-shared-directives)

* Derleyici hatası olmadığını doğrulamak için sınıf kitaplığı oluşturun:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

Derleme çıkışı *RazorUIClassLib. dll* ve *RazorUIClassLib. views. dll*içerir. *RazorUIClassLib. views. dll* derlenen Razor içeriğini içerir.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Razor Pages projesinden Razor Kullanıcı arabirimi kitaplığını kullanma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor Pages Web uygulaması oluşturun:

* **Çözüm Gezgini**, **Yeni proje** **Ekle** >> çözüme sağ tıklayın.  
* **ASP.NET Core Web uygulaması**' nı seçin.
* Uygulamayı **WebApp1**olarak adlandırın.
* **ASP.NET Core 2,1** veya sonraki bir sürümü seçildiğini doğrulayın.
* **Web uygulaması** > **Tamam ' ı**seçin.

* **Çözüm Gezgini**, **WebApp1** ' ye sağ tıklayın ve **Başlangıç projesi olarak ayarla**' yı seçin.
* **Çözüm Gezgini**, **WebApp1** ' ye sağ tıklayın ve **yapı bağımlılıkları** > **Proje bağımlılıkları**' nı seçin.
* **WebApp1**'in bağımlılığı olarak **RazorUIClassLib** denetleyin.
* **Çözüm Gezgini**, **WebApp1** ' a sağ tıklayın ve **başvuru** **Ekle** > ' yi seçin.
* **Başvuru Yöneticisi** iletişim kutusunda **RazorUIClassLib** > **Tamam**' ı işaretleyin.

Uygulamayı çalıştırın.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Razor Pages uygulamasını ve RCL 'yi içeren bir Razor Pages Web uygulaması ve çözüm dosyası oluşturun:

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Web uygulamasını derleyin ve çalıştırın:

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>Test WebApp1

UI sınıf `/MyFeature/Page1` kitaplığının kullanımda olduğunu doğrulamak için öğesine gidin. Razor

## <a name="override-views-partial-views-and-pages"></a>Görünümleri, kısmi görünümleri ve sayfaları geçersiz kıl

Hem Web uygulamasında hem de RCL 'de Razor bir görünüm, kısmi görünüm veya sayfa bulunduğunda, Web uygulamasındaki Razor biçimlendirme (*. cshtml* dosyası) öncelik kazanır. Örneğin, *WebApp1/Areas/MyFeature/Pages/Sayfa1. cshtml* öğesini WebApp1 öğesine ekleyin ve WebApp1 içindeki Sayfa1, RCL 'deki Sayfa1 'e göre öncelikli olur.

Örnek indirme içinde *WebApp1/Areas/MyFeature2* öğesini *WebApp1/Areas/myfeature* olarak yeniden adlandırın ve test önceliğini belirtin.

*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message. cshtml* kısmi görünümünü *WebApp1/Areas/Myfeature/Pages/Shared/_Message. cshtml*'ye kopyalayın. Biçimlendirmeyi yeni konumu belirtecek şekilde güncelleştirin. Uygulamanın kısmi sürümünün kullanılmakta olduğunu doğrulamak için uygulamayı derleyin ve çalıştırın.

### <a name="rcl-pages-layout"></a>RCL sayfaları düzeni

RCL içeriğine, Web uygulamasının *Sayfalar* klasörünün bir parçası olmasına rağmen başvurmak için, aşağıdaki dosya yapısıyla RCL projesini oluşturun:

* *RazorUIClassLib/sayfalar*
* *RazorUIClassLib/sayfalar/paylaşılan*

*RazorUIClassLib/Pages/Shared* iki kısmi dosya içerir: *_Header. cshtml* ve *_Footer. cshtml*. `<partial>` Etiketler *_Layout. cshtml* dosyasına eklenebilir:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/class-libraries>
