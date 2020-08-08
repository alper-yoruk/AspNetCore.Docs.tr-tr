---
title: RazorASP.NET Core ile sınıf kitaplıklarında yeniden kullanılabilir kullanıcı arabirimi
author: Rick-Anderson
description: RazorASP.NET Core bir sınıf kitaplığında kısmi görünümler kullanarak yeniden kullanılabilir kullanıcı arabirimi oluşturmayı açıklar.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/ui-class
ms.openlocfilehash: 042ef8dcc7135e0c60baec972d405380d85a6a10
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019228"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a>RazorASP.NET Core içindeki sınıf kitaplığı projesini kullanarak yeniden kullanılabilir kullanıcı arabirimi oluşturma

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Razorgörünümler, sayfalar, denetleyiciler, sayfa modelleri, [ Razor Bileşenler](xref:blazor/components/class-libraries), [Görünüm bileşenleri](xref:mvc/views/view-components)ve veri modelleri bir Razor sınıf kitaplığı (RCL) halinde oluşturulabilir. RCL paketlenebilir ve yeniden kullanılabilir. Uygulamalar RCL 'yi içerebilir ve içerdiği görünümleri ve sayfaları geçersiz kılabilir. Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya Razor sayfa bulunduğunda, Razor Web uygulamasındaki biçimlendirme (*. cshtml* dosyası) öncelik kazanır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>UI içeren bir sınıf kitaplığı oluşturma Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio 'dan **Yeni bir proje oluştur**' u seçin.
* ** Razor Sınıf kitaplığı** > **İleri ' yi**seçin.
* Kitaplığı adlandırın (örneğin, " Razor ClassLib"), > **Oluştur**. Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views` .
* Görünümleri desteketmeniz gerekiyorsa **destek sayfaları ve görünümleri '** ni seçin. Varsayılan olarak, yalnızca Razor Sayfalar desteklenir. **Oluştur**’u seçin.

RazorSınıf kitaplığı (RCL) şablonu Razor Varsayılan olarak varsayılan olarak bileşen geliştirme olur. **Destek sayfaları ve görünümleri** seçeneği sayfaları ve görünümleri destekler.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Komut satırından komutunu çalıştırın `dotnet new razorclasslib` . Örnek:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

RazorSınıf kitaplığı (RCL) şablonu Razor Varsayılan olarak varsayılan olarak bileşen geliştirme olur. `--support-pages-and-views` `dotnet new razorclasslib --support-pages-and-views` Sayfalar ve görünümler için destek sağlamak üzere seçeneğini () geçirin.

Daha fazla bilgi için bkz. [DotNet New](/dotnet/core/tools/dotnet-new). Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views` .

---

RazorRCL 'ye dosya ekleyin.

ASP.NET Core şablonları RCL içeriğinin *Areas* klasöründe olduğunu varsayar. ' De içeriğini kullanıma sunan bir RCL oluşturmak için [RCL Pages düzenine](#rcl-pages-layout) bakın `~/Pages` `~/Areas/Pages` .

## <a name="reference-rcl-content"></a>RCL içeriğine başvur

RCL 'ye şu şekilde başvurulabilir:

* NuGet paketi. Bkz. [NuGet paketleri oluşturma](/nuget/create-packages/creating-a-package) ve [DotNet paket ekleme](/dotnet/core/tools/dotnet-add-package) ve [bir NuGet paketi oluşturma ve yayımlama](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}. csproj*. Bkz. [DotNet-başvuru Ekle](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Görünümleri, kısmi görünümleri ve sayfaları geçersiz kıl

Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya Razor sayfa bulunduğunda, Razor Web uygulamasındaki biçimlendirme (*. cshtml* dosyası) öncelik kazanır. Örneğin, *WebApp1/Areas/MyFeature/Pages/Sayfa1. cshtml* öğesini WebApp1 öğesine ekleyin ve WebApp1 içindeki Sayfa1, RCL 'deki Sayfa1 'e göre öncelikli olur.

Örnek indirme içinde *WebApp1/Areas/MyFeature2* öğesini *WebApp1/Areas/myfeature* olarak yeniden adlandırın ve test önceliğini belirtin.

* Razor Uıısslib/Areas/Myfeature/Pages/shared/_Message. cshtml* kısmi görünümünü *WebApp1/Areas/Myfeature/pages/Shared/_Message. cshtml*'ye kopyalayın. Biçimlendirmeyi yeni konumu belirtecek şekilde güncelleştirin. Uygulamanın kısmi sürümünün kullanılmakta olduğunu doğrulamak için uygulamayı derleyin ve çalıştırın.

### <a name="rcl-pages-layout"></a>RCL sayfaları düzeni

RCL içeriğine, Web uygulamasının *Sayfalar* klasörünün bir parçası olmasına rağmen başvurmak için, aşağıdaki dosya yapısıyla RCL projesini oluşturun:

* *RazorUıısslib/sayfalar*
* *RazorUıısslib/sayfalar/paylaşılan*

* Razor Uıısslib/Pages/Shared* iki kısmi dosya içerir: *_Header. cshtml* ve *_Footer. cshtml*. `<partial>`Etiketler *_Layout. cshtml* dosyasına eklenebilir:

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

`dotnet pack`NuGet.exe sürümü yerine komutunu kullanın `nuget pack` .

### <a name="exclude-static-assets"></a>Statik varlıkları hariç tut

Statik varlıkları dışlamak için, istenen dışlama yolunu `$(DefaultItemExcludes)` Proje dosyasındaki özellik grubuna ekleyin. Girişleri noktalı virgül () ile ayırın `;` .

Aşağıdaki örnekte, *Wwwroot* klasöründeki *lib. css* stil sayfası statik bir varlık olarak değerlendirilmez ve yayımlanan RCL 'ye dahil değildir:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>TypeScript tümleştirmesi

TypeScript dosyalarını RCL 'ye eklemek için:

1. TypeScript dosyalarını (*. TS*) *Wwwroot* klasörünün dışına yerleştirin. Örneğin, dosyaları bir *istemci* klasörüne yerleştirin.

1. *Wwwroot* klasörü için TypeScript derleme çıkışını yapılandırın. `TypescriptOutDir`Proje dosyasındaki öğesinin içindeki özelliğini ayarlayın `PropertyGroup` :

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. `ResolveCurrentProjectStaticWebAssets`Proje dosyasında bir öğesinin içine aşağıdaki hedefi ekleyerek TypeScript hedefini hedefin bağımlılığı olarak ekleyin `PropertyGroup` :

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Başvurulan bir RCL 'den içerik tüketme

RCL 'nin *Wwwroot* klasörüne eklenen dosyalar, ön ek altında RCL veya tüketen uygulamaya sunulur `_content/{LIBRARY NAME}/` . Örneğin, adlı bir kitaplık * Razor . Class. lib* , konumundaki statik içeriğe yol açmaya neden olur `_content/Razor.Class.Lib/` . Bir NuGet paketi üretilirken ve derleme adı paket KIMLIĞIYLE aynı değilse, için paket KIMLIĞINI kullanın `{LIBRARY NAME}` .

Tüketen uygulama,,, `<script>` `<style>` `<img>` ve diğer HTML etiketleriyle kitaplık tarafından sunulan statik varlıklara başvurur. Kullanan uygulamada [statik dosya desteğinin](xref:fundamentals/static-files) etkinleştirilmesi gerekir `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Yapı çıktısından () kullanan uygulamayı çalıştırırken `dotnet run` , varsayılan olarak, statik Web varlıkları geliştirme ortamında etkinleştirilmiştir. Derleme çıktılarından çalışırken diğer ortamlardaki varlıkları desteklemek için, `UseStaticWebAssets` *program.cs*içindeki konak Oluşturucu 'da çağırın:

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

`UseStaticWebAssets`Yayımlanan çıktısından () bir uygulama çalıştırılırken çağırma gerekmez `dotnet publish` .

### <a name="multi-project-development-flow"></a>Çoklu projeli geliştirme akışı

Kullanan uygulama şu şekilde çalışır:

* RCL içindeki varlıklar özgün klasörlerinde kalır. Varlıklar, tüketim uygulamasına taşınmaz.
* RCL 'nin *Wwwroot* klasörü içindeki tüm değişiklikler, RCL yeniden oluşturulduktan ve tüketen uygulamayı yeniden oluşturmadan önce tüketen uygulamaya yansıtılır.

RCL yapılandırıldığında, statik Web varlık konumlarını açıklayan bir bildirim oluşturulur. Tüketen uygulama, başvurulan proje ve paketlerden varlıkları kullanmak için çalışma zamanında bildirimi okur. Bir RCL 'ye yeni bir varlık eklendiğinde, bir uygulamanın yeni varlığa erişebilmesi için bildirim güncellemek üzere RCL 'nin yeniden oluşturulması gerekir.

### <a name="publish"></a>Yayımla

Uygulama yayımlandığında, tüm başvurulan projeler ve paketlerin yardımcı varlıkları altında yayımlanan uygulamanın *Wwwroot* klasörüne kopyalanır `_content/{LIBRARY NAME}/` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razorgörünümler, sayfalar, denetleyiciler, sayfa modelleri, [ Razor Bileşenler](xref:blazor/components/class-libraries), [Görünüm bileşenleri](xref:mvc/views/view-components)ve veri modelleri bir Razor sınıf kitaplığı (RCL) halinde oluşturulabilir. RCL paketlenebilir ve yeniden kullanılabilir. Uygulamalar RCL 'yi içerebilir ve içerdiği görünümleri ve sayfaları geçersiz kılabilir. Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya Razor sayfa bulunduğunda, Razor Web uygulamasındaki biçimlendirme (*. cshtml* dosyası) öncelik kazanır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-no-locrazor-ui"></a>UI içeren bir sınıf kitaplığı oluşturma Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.
* **ASP.NET Core Web uygulaması**' nı seçin.
* Kitaplığı adlandırın (örneğin, " Razor ClassLib") > **Tamam**. Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views` .
* **ASP.NET Core 2,1** veya sonraki bir sürümü seçildiğini doğrulayın.
* ** Razor Sınıf kitaplığı** > **Tamam ' ı**seçin.

RCL aşağıdaki proje dosyasına sahiptir:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Komut satırından komutunu çalıştırın `dotnet new razorclasslib` . Örnek:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Daha fazla bilgi için bkz. [DotNet New](/dotnet/core/tools/dotnet-new). Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views` .

---

RazorRCL 'ye dosya ekleyin.

ASP.NET Core şablonları RCL içeriğinin *Areas* klasöründe olduğunu varsayar. ' De içeriğini kullanıma sunan bir RCL oluşturmak için [RCL Pages düzenine](#rcl-pages-layout) bakın `~/Pages` `~/Areas/Pages` .

## <a name="reference-rcl-content"></a>RCL içeriğine başvur

RCL 'ye şu şekilde başvurulabilir:

* NuGet paketi. Bkz. [NuGet paketleri oluşturma](/nuget/create-packages/creating-a-package) ve [DotNet paket ekleme](/dotnet/core/tools/dotnet-add-package) ve [bir NuGet paketi oluşturma ve yayımlama](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}. csproj*. Bkz. [DotNet-başvuru Ekle](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a>İzlenecek yol: bir RCL projesi oluşturma ve bir Razor Sayfalar projesinden kullanma

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

Bu bölümde bir RCL oluşturulur. Razordosyalar RCL 'ye eklenir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

RCL projesini oluşturun:

* Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.
* **ASP.NET Core Web uygulaması**' nı seçin.
* Uygulamayı ** Razor uıısslib** olarak adlandırın > **OK**.
* **ASP.NET Core 2,1** veya sonraki bir sürümü seçildiğini doğrulayın.
* ** Razor Sınıf kitaplığı** > **Tamam ' ı**seçin.
* Razor * Razor Uıısslib/Areas/Myfeature/Pages/Shared/_Message. cshtml*adlı kısmi bir görünüm dosyası ekleyin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Komut satırından aşağıdakileri çalıştırın:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Önceki komutlar:

* `RazorUIClassLib`RCL 'yi oluşturur.
* Bir Razor _Message sayfası oluşturur ve RCL 'ye ekler. `-np`Parametresi, sayfasını bir olmadan oluşturur `PageModel` .
* Bir [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) dosyası oluşturur ve RCL 'ye ekler.

*_ViewStart. cshtml* dosyası, Razor Sayfalar projesinin yerleşimini (bir sonraki bölüme eklenen) kullanmak için gereklidir.

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a>RazorProjeye dosya ve klasör ekleme

* * Razor Uıısslib/Areas/Myfeature/Pages/Shared/_Message. cshtml* içindeki biçimlendirmeyi aşağıdaki kodla değiştirin:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* * Razor Uıısslib/Areas/Myfeature/Pages/Sayfa1. cshtml* içindeki biçimlendirmeyi aşağıdaki kodla değiştirin:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`kısmi görünümü kullanmak için gerekir ( `<partial name="_Message" />` ). `@addTagHelper`Yönergesini eklemek yerine bir *_ViewImports. cshtml* dosyası ekleyebilirsiniz. Örnek:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  *_ViewImports. cshtml*hakkında daha fazla bilgi için bkz. [paylaşılan yönergeleri içeri aktarma](xref:mvc/views/layout#importing-shared-directives)

* Derleyici hatası olmadığını doğrulamak için sınıf kitaplığı oluşturun:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

Derleme çıktısı * RazorUIClassLib.dll* ve * RazorUIClassLib.Views.dll*içerir. * RazorUIClassLib.Views.dll* derlenen içeriği içerir Razor .

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a>RazorBir sayfalar projesinden UI kitaplığını kullanma Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

RazorSayfalar Web uygulaması oluşturma:

* **Çözüm Gezgini**, **Add** > **Yeni proje**Ekle > çözüme sağ tıklayın.  
* **ASP.NET Core Web uygulaması**' nı seçin.
* Uygulamayı **WebApp1**olarak adlandırın.
* **ASP.NET Core 2,1** veya sonraki bir sürümü seçildiğini doğrulayın.
* **Web uygulaması** > **Tamam ' ı**seçin.

* **Çözüm Gezgini**, **WebApp1** ' ye sağ tıklayın ve **Başlangıç projesi olarak ayarla**' yı seçin.
* **Çözüm Gezgini**, **WebApp1** ' ye sağ tıklayın ve **yapı bağımlılıkları** > **Proje bağımlılıkları**' nı seçin.
* ** Razor Uıısslib** öğesini **WebApp1**bağımlılığı olarak denetleyin.
* **Çözüm Gezgini**, **WebApp1** ' a sağ tıklayın ve başvuru **Ekle** ' yi seçin > **Reference**.
* **Başvuru Yöneticisi** iletişim kutusunda ** Razor uıısslib** Tamam ' ı işaretleyin > **OK**.

Uygulamayı çalıştırın.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Sayfalar Razor Razor uygulaması ve RCL içeren bir sayfa Web uygulaması ve bir çözüm dosyası oluşturun:

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

`/MyFeature/Page1`UI sınıf kitaplığının kullanımda olduğunu doğrulamak için öğesine gidin Razor .

## <a name="override-views-partial-views-and-pages"></a>Görünümleri, kısmi görünümleri ve sayfaları geçersiz kıl

Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya Razor sayfa bulunduğunda, Razor Web uygulamasındaki biçimlendirme (*. cshtml* dosyası) öncelik kazanır. Örneğin, *WebApp1/Areas/MyFeature/Pages/Sayfa1. cshtml* öğesini WebApp1 öğesine ekleyin ve WebApp1 içindeki Sayfa1, RCL 'deki Sayfa1 'e göre öncelikli olur.

Örnek indirme içinde *WebApp1/Areas/MyFeature2* öğesini *WebApp1/Areas/myfeature* olarak yeniden adlandırın ve test önceliğini belirtin.

* Razor Uıısslib/Areas/Myfeature/Pages/shared/_Message. cshtml* kısmi görünümünü *WebApp1/Areas/Myfeature/pages/Shared/_Message. cshtml*'ye kopyalayın. Biçimlendirmeyi yeni konumu belirtecek şekilde güncelleştirin. Uygulamanın kısmi sürümünün kullanılmakta olduğunu doğrulamak için uygulamayı derleyin ve çalıştırın.

### <a name="rcl-pages-layout"></a>RCL sayfaları düzeni

RCL içeriğine, Web uygulamasının *Sayfalar* klasörünün bir parçası olmasına rağmen başvurmak için, aşağıdaki dosya yapısıyla RCL projesini oluşturun:

* *RazorUıısslib/sayfalar*
* *RazorUıısslib/sayfalar/paylaşılan*

* Razor Uıısslib/Pages/Shared* iki kısmi dosya içerir: *_Header. cshtml* ve *_Footer. cshtml*. `<partial>`Etiketler *_Layout. cshtml* dosyasına eklenebilir:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/components/class-libraries>
