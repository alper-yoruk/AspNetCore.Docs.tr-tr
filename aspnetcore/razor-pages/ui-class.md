---
title: ASP.NET Core ile sınıf kütüphanelerinde yeniden kullanılabilir Razor UI
author: Rick-Anderson
description: ASP.NET Core'daki bir sınıf kitaplığında kısmi görünümler kullanarak yeniden kullanılabilir Razor UI'nin nasıl oluşturulabildiğini açıklar.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667568"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>ASP.NET Core'daki Razor sınıfı kitaplık projesini kullanarak yeniden kullanılabilir ui oluşturma

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Jilet görünümleri, sayfalar, denetleyiciler, sayfa modelleri, [Razor bileşenleri,](xref:blazor/class-libraries) [Bileşenleri görüntüle](xref:mvc/views/view-components)ve veri modelleri Bir Razor sınıfı kitaplık (RCL) içine inşa edilebilir. RCL paketlenebilir ve yeniden kullanılabilir. Uygulamalar RCL'yi içerebilir ve içerdiği görünümleri ve sayfaları geçersiz kılabilir. Hem web uygulamasında hem de RCL'de bir görünüm, kısmi görünüm veya Razor Page bulunduğunda, web uygulamasındaki Razor biçimlendirmesi *(.cshtml* dosyası) önceliklidir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Razor UI içeren bir sınıf kitaplığı oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio'dan **yeni bir proje oluştur'u**seçin.
* **Sonraki** **Razor Class Kitaplığı'nı** > seçin.
* Kitaplığı adlandırın (örneğin, "RazorClassLib"), > **Oluştur.** Oluşturulan görünüm kitaplığıyla dosya adı çakışmasını önlemek için kitaplık adının `.Views`'de bitmediğinden emin olun
* Görünümleri desteklemeniz gerekiyorsa **Destek sayfalarını ve görünümlerini** seçin. Varsayılan olarak, yalnızca Razor Pages desteklenir. **Oluştur'u**seçin.

Razor sınıf kitaplığı (RCL) şablonu varsayılan olarak Razor bileşen geliştirme için varsayılan dır. **Destek sayfaları ve görünümleri** seçeneği sayfaları ve görünümleri destekler.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Komut satırından çalıştırın. `dotnet new razorclasslib` Örneğin:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Razor sınıf kitaplığı (RCL) şablonu varsayılan olarak Razor bileşen geliştirme için varsayılan dır. Sayfalar `--support-pages-and-views` ve`dotnet new razorclasslib --support-pages-and-views`görünümler için destek sağlamak için seçeneği ni geçirin .

Daha fazla bilgi için [dotnet yeni.](/dotnet/core/tools/dotnet-new) Oluşturulan görünüm kitaplığıyla dosya adı çakışmasını önlemek için kitaplık adının `.Views`'de bitmediğinden emin olun

---

RCL'ye Razor dosyaları ekleyin.

ASP.NET Core şablonları RCL içeriğinin *Alanlar* klasöründe olduğunu varsayar. İçeriği `~/Pages` yerine yerine ortaya çıkaran bir RCL oluşturmak `~/Areas/Pages`için [RCL Sayfaları düzenine](#rcl-pages-layout) bakın.

## <a name="reference-rcl-content"></a>Referans RCL içeriği

RCL tarafından başvurulabilir:

* NuGet paketi. Bkz. [NuGet paketleri oluşturma](/nuget/create-packages/creating-a-package) ve [dotnet paketi ekleyin](/dotnet/core/tools/dotnet-add-package) ve bir [NuGet paketi oluşturun ve yayımlayın.](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)
* *{ProjectName}.csproj*. [Bkz. dotnet-add başvurusu.](/dotnet/core/tools/dotnet-add-reference)

## <a name="override-views-partial-views-and-pages"></a>Görünümleri, kısmi görünümleri ve sayfaları geçersiz kılma

Hem web uygulamasında hem de RCL'de bir görünüm, kısmi görünüm veya Razor Page bulunduğunda, web uygulamasındaki Razor biçimlendirmesi *(.cshtml* dosyası) önceliklidir. Örneğin, *WebApp1'e WebApp1/Areas/MyFeature/Pages/Page1.cshtml* ekleyin ve WebApp1'deki Page1, RCL'deki Page1'den önce gelir.

Örnek indirmede, önceliği test etmek için *WebApp1/Areas/MyFeature2'yi* *WebApp1/Areas/MyFeature* olarak yeniden adlandırın.

*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* kısmi görünümü *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*adresine kopyalayın. Yeni konumu belirtmek için biçimlendirmeyi güncelleştirin. Uygulamanın kısmi sürümünün kullanıldığını doğrulamak için uygulamayı oluşturun ve çalıştırın.

### <a name="rcl-pages-layout"></a>RCL Sayfaları düzeni

RCL içeriğine web uygulamasının *Sayfalar* klasörünün bir parçasıymış gibi başvurmak için, aşağıdaki dosya yapısına sahip RCL projesini oluşturun:

* *RazoruiClassLib/Sayfalar*
* *RazoruiClassLib/Sayfalar/Paylaşılan*

*RazorUIClassLib/Pages/Shared'in* iki kısmi dosya içerdiğini varsayalım: *_Header.cshtml* ve *_Footer.cshtml*. Etiketler `<partial>` *_Layout.cshtml* dosyasına eklenebilir:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>Statik varlıklara sahip bir RCL oluşturma

Bir RCL, RCL veya RCL'nin tüketen uygulaması tarafından başvurulan eşlik eden statik varlıklar gerektirebilir. ASP.NET Core, tüketen bir uygulamanın kullanabileceği statik varlıkları içeren RCL'ler oluşturmaya olanak tanır.

Bir RCL'nin parçası olarak eşlik eden varlıkları eklemek için, sınıf kitaplığında bir *wwwroot* klasörü oluşturun ve bu klasöre gerekli dosyaları ekleyin.

Bir RCL paketlenirken, *wwwroot* klasöründeki tüm tamamlayıcı varlıklar otomatik olarak pakete dahil edilir.

### <a name="exclude-static-assets"></a>Statik varlıkları hariç tutma

Statik kıymetleri hariç tutmak için, `$(DefaultItemExcludes)` proje dosyasındaki özellik grubuna istenen dışlama yolunu ekleyin. Bir semicolon ile`;`ayrı girişleri ( ).

Aşağıdaki örnekte, *wwwroot* klasöründeki *lib.css* stylesheet statik bir varlık olarak kabul etmez ve yayınlanan RCL'ye dahil değildir:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Typescript tümleştirmesi

TypeScript dosyalarını Bir RCL'ye eklemek için:

1. TypeScript dosyalarını (*.ts)* *wwwroot* klasörünün dışına yerleştirin. Örneğin, dosyaları *Istemci* klasörüne yerleştirin.

1. *Wwwroot* klasörü için TypeScript yapı çıktısını yapılandırın. Proje `TypescriptOutDir` dosyasındaki bir `PropertyGroup` özelliğin içindeki özelliği ayarlama:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Project dosyasına aşağıdaki hedefi ekleyerek, `ResolveCurrentProjectStaticWebAssets` hedefin bağımlılığı olarak TypeScript hedefini `PropertyGroup` ekleyin:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Başvurulan bir RCL'den içerik tüketin

RCL'nin *wwwroot* klasöründe yer alan dosyalar, önek `_content/{LIBRARY NAME}/`altında RCL'ye veya tüketen uygulamaya maruz kalır. Örneğin, *Razor.Class.Lib* adlı bir kitaplık, 'de `_content/Razor.Class.Lib/`statik içeriğe giden bir yol ile sonuçlanır. NuGet paketi üretirken ve montaj adı paket kimliğiyle aynı değildir, paket `{LIBRARY NAME}`kimliğini kullanın.

Tüketen uygulama, kitaplık tarafından sağlanan `<script>` `<style>`statik `<img>`varlıklara , , ve diğer HTML etiketleri ile başvurur. Tüketen uygulamanın [aşağıdaki](xref:fundamentals/static-files) durumlarda `Startup.Configure`statik dosya desteği etkinleştirilmiş olması gerekir:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Alıcı uygulamayı yapı çıktısından çalıştırırken (`dotnet run`), statik web varlıkları Geliştirme ortamında varsayılan olarak etkinleştirilir. Yapı çıktısından kaçarken diğer ortamlardaki `UseStaticWebAssets` varlıkları desteklemek *için, Program.cs*ev sahibi oluşturucuyu arayın:

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

Yayınlanmış `UseStaticWebAssets` çıktıdan bir uygulamayı çalıştırırken arama`dotnet publish`yapılması gerekmez ( ).

### <a name="multi-project-development-flow"></a>Çok projeli geliştirme akışı

Tüketen uygulama çalıştığında:

* RCL'deki varlıklar özgün klasörlerinde kalır. Varlıklar tüketen uygulamaya taşınmaz.
* RCL'nin *wwwroot* klasöründeki herhangi bir değişiklik, RCL yeniden inşa edildikten ve tüketen uygulamayı yeniden oluşturmadan tüketen uygulamaya yansıtılır.

RCL oluşturulduğunda, statik web varlık konumlarını açıklayan bir bildirim oluşturulur. Tüketen uygulama, başvurulan projeler den ve paketlerden varlıkları tüketmek için çalışma zamanında manifestoyu okur. Bir RCL'ye yeni bir varlık eklendiğinde, tüketen bir uygulamanın yeni varlığa erişemeden önce bildirimini güncelleştirmek için RCL'nin yeniden inşa edilmesi gerekir.

### <a name="publish"></a>Yayımlama

Uygulama yayımlandığında, başvurulan tüm proje ve paketlerdeki yardımcı varlıklar, altında `_content/{LIBRARY NAME}/`yayınlanan uygulamanın *wwwroot* klasörüne kopyalanır.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Jilet görünümleri, sayfalar, denetleyiciler, sayfa modelleri, [Razor bileşenleri,](xref:blazor/class-libraries) [Bileşenleri görüntüle](xref:mvc/views/view-components)ve veri modelleri Bir Razor sınıfı kitaplık (RCL) içine inşa edilebilir. RCL paketlenebilir ve yeniden kullanılabilir. Uygulamalar RCL'yi içerebilir ve içerdiği görünümleri ve sayfaları geçersiz kılabilir. Hem web uygulamasında hem de RCL'de bir görünüm, kısmi görünüm veya Razor Page bulunduğunda, web uygulamasındaki Razor biçimlendirmesi *(.cshtml* dosyası) önceliklidir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Razor UI içeren bir sınıf kitaplığı oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.
* **Core Web Uygulaması ASP.NET**seçin.
* Kitaplığı adlandırın (örneğin, "RazorClassLib") **> Tamam.** Oluşturulan görünüm kitaplığıyla dosya adı çakışmasını önlemek için kitaplık adının `.Views`'de bitmediğinden emin olun
* **Core 2.1** veya daha sonra ASP.NET doğrulayın.
* **Razor Class Kitaplığı** > **Tamam'ı**seçin.

Bir RCL aşağıdaki proje dosyasına sahiptir:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Komut satırından çalıştırın. `dotnet new razorclasslib` Örneğin:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Daha fazla bilgi için [dotnet yeni.](/dotnet/core/tools/dotnet-new) Oluşturulan görünüm kitaplığıyla dosya adı çakışmasını önlemek için kitaplık adının `.Views`'de bitmediğinden emin olun

---

RCL'ye Razor dosyaları ekleyin.

ASP.NET Core şablonları RCL içeriğinin *Alanlar* klasöründe olduğunu varsayar. İçeriği `~/Pages` yerine yerine ortaya çıkaran bir RCL oluşturmak `~/Areas/Pages`için [RCL Sayfaları düzenine](#rcl-pages-layout) bakın.

## <a name="reference-rcl-content"></a>Referans RCL içeriği

RCL tarafından başvurulabilir:

* NuGet paketi. Bkz. [NuGet paketleri oluşturma](/nuget/create-packages/creating-a-package) ve [dotnet paketi ekleyin](/dotnet/core/tools/dotnet-add-package) ve bir [NuGet paketi oluşturun ve yayımlayın.](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)
* *{ProjectName}.csproj*. [Bkz. dotnet-add başvurusu.](/dotnet/core/tools/dotnet-add-reference)

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Walkthrough: Bir RCL projesi oluşturun ve Bir Razor Pages projesinden kullanın

[Projenin tamamını](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) indirebilir ve oluşturmak yerine test edebilirsiniz. Örnek karşıdan yükleme, projeyi test etmeyi kolaylaştıran ek kod lar ve bağlantılar içerir. [Bu GitHub sorununda,](https://github.com/dotnet/AspNetCore.Docs/issues/6098) indirme örnekleri hakkındaki yorumlarınızla ve adım adım talimatlara karşı geri bildirim bırakabilirsiniz.

### <a name="test-the-download-app"></a>İndirme uygulamasını test edin

Tamamlanan uygulamayı indirmediyseniz ve gözden geçirme projesini oluşturmak yerine bir [sonraki bölüme](#create-an-rcl)atlayın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*.sln* dosyasını Visual Studio'da açın. Uygulamayı çalıştırın.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

*Cli* dizinindeki bir komut isteminden, RCL ve web uygulamasını oluşturun.

```dotnetcli
dotnet build
```

*WebApp1* dizinine geçin ve uygulamayı çalıştırın:

```dotnetcli
dotnet run
```

---

[Test WebApp1'deki](#test-webapp1) talimatları izleyin

## <a name="create-an-rcl"></a>RCL oluşturma

Bu bölümde bir RCL oluşturulur. Ustura dosyaları RCL eklenir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

RCL projesini oluşturun:

* Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.
* **Core Web Uygulaması ASP.NET**seçin.
* Uygulamayı **RazorUIClassLib** > **Tamam**adlandırın.
* **Core 2.1** veya daha sonra ASP.NET doğrulayın.
* **Razor Class Kitaplığı** > **Tamam'ı**seçin.
* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*adlı bir Razor kısmi görünüm dosyası ekleyin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Komut satırından aşağıdakileri çalıştırın:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Önceki komutlar:

* RCL `RazorUIClassLib` oluşturur.
* Bir Jilet _Message sayfası oluşturur ve RCL'ye ekler. Parametre `-np` olmadan sayfa oluşturur `PageModel`.
* [bir _ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) dosyası oluşturur ve RCL'ye ekler.

*_ViewStart.cshtml* dosyasının Razor Pages projesinin düzenini kullanması gerekmektedir (bir sonraki bölümde eklenir).

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Projeye Razor dosya ve klasörleri ekleme

* *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml'deki* biçimlendirmeyi aşağıdaki kodla değiştirin:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml'deki* biçimlendirmeyi aşağıdaki kodla değiştirin:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`kısmi görünümü kullanmak için`<partial name="_Message" />`gereklidir ( ). Yönergeyi `@addTagHelper` eklemek yerine, bir *_ViewImports.cshtml* dosyası ekleyebilirsiniz. Örneğin:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  *_ViewImports.cshtml*hakkında daha fazla bilgi için [bkz.](xref:mvc/views/layout#importing-shared-directives)

* Derleyici hatası olmadığını doğrulamak için sınıf kitaplığını oluşturun:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

Yapı *çıktısı RazorUIClassLib.dll* ve *RazorUIClassLib.Views.dll*içerir. *RazorUIClassLib.Views.dll* derlenmiş Razor içeriğini içerir.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Razor Pages projesindeki Razor UI kitaplığını kullanma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor Pages web uygulamasını oluşturun:

* **Solution Explorer'dan,** Yeni **Proje** **Ekle** >> çözüme sağ tıklayın.  
* **Core Web Uygulaması ASP.NET**seçin.
* Uygulamanın adını **WebApp1.**
* **Core 2.1** veya daha sonra ASP.NET doğrulayın.
* **Web Uygulaması Tamam'ı** > **OK**seçin.

* **Solution Explorer'dan** **WebApp1'e** sağ tıklayın ve **StartUp Project olarak ayarla'yı**seçin.
* **Solution Explorer'dan** **WebApp1'e** sağ tıklayın ve **Bağımlılıkoluştur** > **Projesi Bağımlılıkları'nı**seçin.
* **WebApp1'e**bağımlılık olarak **RazorUIClassLib'i** kontrol edin.
* **Solution Explorer'dan** **WebApp1'e** sağ tıklayın ve **Referans** **Ekle'yi** > seçin.
* Başvuru **Yöneticisi** iletişim kutusunda, **RazorUIClassLib** > **Tamam'ı**işaretleyin.

Uygulamayı çalıştırın.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Razor Pages web uygulaması ve Razor Pages uygulamasını ve RCL'yi içeren bir çözüm dosyası oluşturun:

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Web uygulamasını oluşturun ve çalıştırın:

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>Test WebApp1

Razor UI sınıf kitaplığının kullanımda olduğunu doğrulamak için `/MyFeature/Page1` göz atın.

## <a name="override-views-partial-views-and-pages"></a>Görünümleri, kısmi görünümleri ve sayfaları geçersiz kılma

Hem web uygulamasında hem de RCL'de bir görünüm, kısmi görünüm veya Razor Page bulunduğunda, web uygulamasındaki Razor biçimlendirmesi *(.cshtml* dosyası) önceliklidir. Örneğin, *WebApp1'e WebApp1/Areas/MyFeature/Pages/Page1.cshtml* ekleyin ve WebApp1'deki Page1, RCL'deki Page1'den önce gelir.

Örnek indirmede, önceliği test etmek için *WebApp1/Areas/MyFeature2'yi* *WebApp1/Areas/MyFeature* olarak yeniden adlandırın.

*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* kısmi görünümü *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*adresine kopyalayın. Yeni konumu belirtmek için biçimlendirmeyi güncelleştirin. Uygulamanın kısmi sürümünün kullanıldığını doğrulamak için uygulamayı oluşturun ve çalıştırın.

### <a name="rcl-pages-layout"></a>RCL Sayfaları düzeni

RCL içeriğine web uygulamasının *Sayfalar* klasörünün bir parçasıymış gibi başvurmak için, aşağıdaki dosya yapısına sahip RCL projesini oluşturun:

* *RazoruiClassLib/Sayfalar*
* *RazoruiClassLib/Sayfalar/Paylaşılan*

*RazorUIClassLib/Pages/Shared'in* iki kısmi dosya içerdiğini varsayalım: *_Header.cshtml* ve *_Footer.cshtml*. Etiketler `<partial>` *_Layout.cshtml* dosyasına eklenebilir:

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
