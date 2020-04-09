---
title: ASP.NET Core'daki Dosya Sağlayıcıları
author: rick-anderson
description: ASP.NET Core özetleri dosya sistemine Dosya Sağlayıcıları'nın kullanımı yoluyla nasıl erişebildiğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
uid: fundamentals/file-providers
ms.openlocfilehash: 25607bd534cae05a6c6b11fa6d8902faa3c0684c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751095"
---
# <a name="file-providers-in-aspnet-core"></a>ASP.NET Core'daki Dosya Sağlayıcıları

Yazar: [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core özetleri dosya sistemi erişim Dosya Sağlayıcıları kullanımı yoluyla. Dosya Sağlayıcıları ASP.NET Core çerçevesi boyunca kullanılır. Örneğin:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>uygulamanın [içerik kökünü](xref:fundamentals/index#content-root) ve web `IFileProvider` [kökünü](xref:fundamentals/index#web-root) tür olarak ortaya çıkarır.
* [Statik Dosya Middleware](xref:fundamentals/static-files) statik dosyaları bulmak için Dosya Sağlayıcıları kullanır.
* [Razor,](xref:mvc/views/razor) sayfaları ve görünümleri bulmak için Dosya Sağlayıcıları'nı kullanır.
* .NET Core araçlama, hangi dosyaların yayımlanması gerektiğini belirtmek için Dosya Sağlayıcıları ve glob desenlerini kullanır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Dosya Sağlayıcı arabirimleri

Birincil <xref:Microsoft.Extensions.FileProviders.IFileProvider>arabirim. `IFileProvider`yöntemleri ortaya çıkarır:

* Dosya bilgilerini<xref:Microsoft.Extensions.FileProviders.IFileInfo>alma ( ).
* Dizin bilgileri<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>edinin ( ).
* Değişiklik bildirimlerini ayarlama (bir <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo`dosyalarla çalışmak için yöntem ve özellikler sağlar:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(bayt olarak)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Tarih

<xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> Yöntemi kullanarak dosyadan okuyabilirsiniz.

*FileProviderSample* örnek [uygulaması, bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)yoluyla `Startup.ConfigureServices` uygulama boyunca kullanılmak üzere bir Dosya Sağlayıcısının nasıl yapılandırılabildiğini gösterir.

## <a name="file-provider-implementations"></a>Dosya Sağlayıcı uygulamaları

Aşağıdaki tabloda `IFileProvider`.

| Uygulama | Açıklama |
| -------------- | ----------- |
| [KompozitDosya Sağlayıcısı](#compositefileprovider) | Bir veya daha fazla sağlayıcıdan gelen dosyalara ve dizinlere birleşik erişim sağlamak için kullanılır. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Derlemelere katışmış dosyalara erişmek için kullanılır. |
| [PhysicalFileProvider](#physicalfileprovider) | Sistemin fiziksel dosyalarına erişmek için kullanılır. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

Fiziksel <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> dosya sistemine erişim sağlar. `PhysicalFileProvider`<xref:System.IO.File?displayProperty=fullName> türünü (fiziksel sağlayıcı için) kullanır ve bir dizin ve çocuklarına giden tüm yolları scopelar. Bu kapsam, belirtilen dizin ve altlarının dışında dosya sistemine erişimi engeller. Oluşturmak ve kullanmak `PhysicalFileProvider` için en yaygın senaryo `IFileProvider` [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)yoluyla bir yapıcı bir istek.

Bu sağlayıcıyı doğrudan anında kullanırken, mutlak bir dizin yolu gereklidir ve sağlayıcı kullanılarak yapılan tüm istekler için temel yol olarak hizmet vermektedir. Glob desenleri dizin yolunda desteklenmez.

Aşağıdaki kod, dizin `PhysicalFileProvider` içeriği ve dosya bilgilerini elde etmek için nasıl kullanılacağını gösterir:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

Önceki örnekteki türler:

* `provider`bir `IFileProvider`.
* `contents`bir `IDirectoryContents`.
* `fileInfo`bir `IFileInfo`.

Dosya Sağlayıcı, tarafından `applicationRoot` belirtilen dizini doğrulamak veya dosya `GetFileInfo` bilgilerini almak için aramak için kullanılabilir. Glob desenleri `GetFileInfo` yönteme geçirilemez. Dosya Sağlayıcısı'nın `applicationRoot` dizin dışında erişimi yoktur.

*FileProviderSample* örnek uygulaması kullanarak `Startup.ConfigureServices` <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>yöntem sağlayıcı oluşturur:

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

Derlemeler <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> içinde katıştırılmış dosyalara erişmek için kullanılır. Katıştılı `ManifestEmbeddedFileProvider` dosyaların özgün yollarını yeniden oluşturmak için derlemede derlenmiş bir bildirim kullanır.

Katıştılmış dosyaların bir bildirimini oluşturmak için:

1. [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet paketini projenize ekleyin.
1. Özelliği `<GenerateEmbeddedFilesManifest>` ' `true`ye ayarlayın. [ \<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)ile katıştırmak için dosyaları belirtin:

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

Derlemeye katıştırmak için bir veya daha fazla dosya belirtmek için [glob desenleri](#glob-patterns) kullanın.

*FileProviderSample* örnek uygulaması bir `ManifestEmbeddedFileProvider` oluşturma zaif ve şu anda yürütülen montaj onun oluşturucu ya da geçer.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Ek aşırı yüklemeler şunları sağlar:

* Göreli bir dosya yolu belirtin.
* Dosyaları son değiştirilmiş tarihe kadar kapsam.
* Katıştırılmış dosya bildirimini içeren katıştırılmış kaynağı adlandırın.

| Aşırı | Açıklama |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | İsteğe bağlı `root` göreli yol parametresini kabul eder. `root` Sağlanan yol altında <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> bu kaynaklara yapılan kapsam çağrılarını belirtin. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | İsteğe bağlı `root` göreli yol `lastModified` parametresi ve tarih (<xref:System.DateTimeOffset>) parametresini kabul eder. Tarih, `lastModified` <xref:Microsoft.Extensions.FileProviders.IFileInfo> <xref:Microsoft.Extensions.FileProviders.IFileProvider>'nin döndürülen örnekleri için son değişiklik tarihini kapsama |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | İsteğe bağlı `root` göreli `lastModified` yolu, `manifestName` tarihi ve parametreleri kabul eder. Bildirimiçeren `manifestName` katıştırılmış kaynağın adını temsil eder. |

### <a name="compositefileprovider"></a>KompozitDosya Sağlayıcısı

Örnekleri <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> birleştirerek, `IFileProvider` birden çok sağlayıcıdan gelen dosyalarla çalışmak için tek bir arabirimi açığa çıkarır. `CompositeFileProvider`Oluştururken, bir veya `IFileProvider` daha fazla örneği oluşturucuya geçirin.

*FileProviderSample* örnek uygulamasında, `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` ve a, `CompositeFileProvider` uygulamanın hizmet kapsayıcısında kayıtlı bir dosya sağlar. Projenin `Startup.ConfigureServices` yönteminde aşağıdaki kod bulunur:

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Değişikliklere dikkat edin

Yöntem, <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> değişiklikler için bir veya daha fazla dosya veya dizin izlemek için bir senaryo sağlar. Yöntem: `Watch`

* Birden çok dosya belirtmek için [glob desenleri](#glob-patterns) kullanabilen bir dosya yolu dizesini kabul eder.
* Bir <xref:Microsoft.Extensions.Primitives.IChangeToken>.

Ortaya çıkan değişiklik belirteci şunları ortaya çıkarır:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;Bir değişikliğin meydana geldiğini belirlemek için denetlenebilecek bir özellik.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Belirtilen yol dizesine değişiklikler algılandığında çağrılır. Her değişiklik belirteci yalnızca tek bir değişikliğe yanıt olarak ilişkili geri arama çağırır. Sürekli izleme sağlamak için, <xref:System.Threading.Tasks.TaskCompletionSource`1> bir (aşağıda gösterilmiştir) kullanın veya değişikliklere yanıt olarak örnekleri yeniden oluşturun. `IChangeToken`

*WatchConsole* örnek *uygulaması, TextFiles* dizinindeki *bir .txt* dosyası değiştirildiğinde bir ileti yazar:

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

Docker kapsayıcıları ve ağ paylaşımları gibi bazı dosya sistemleri, güvenilir bir şekilde değişiklik bildirimleri göndermeyebilir. Ortam `DOTNET_USE_POLLING_FILE_WATCHER` değişkenini `1` `true` her dört saniyede bir (yapılandırılamaz) değişiklikler için dosya sistemini yoklamak için ayarlayın.

### <a name="glob-patterns"></a>Glob desenleri

Dosya sistemi yolları *glob (veya globbing) desenleri*olarak adlandırılan joker karakter desenleri kullanır. Bu desenlere sahip dosya gruplarını belirtin. İki joker karakter ve `*` `**`şunlardır:

**`*`**  
Geçerli klasör düzeyindeki herhangi bir şeyle, dosya adıyla veya dosya uzantısını eşleşir. Eşleşmeler tarafından `/` `.` sonlandırılır ve dosya yolundaki karakterler.

**`**`**  
Birden çok dizin düzeyindeki her şeyle eşleşir. Dizin hiyerarşisi içindeki birçok dosyayı özyinelemeli olarak eşleştirmek için kullanılabilir.

Aşağıdaki tabloglob desenleri ortak örnekler sağlar.

|Desen  |Açıklama  |
|---------|---------|
|`directory/file.txt`|Belirli bir dizindeki belirli bir dosyayla eşleşir.|
|`directory/*.txt`|Tüm dosyaları *.txt* uzantısı ile belirli bir dizinde eşler.|
|`directory/*/appsettings.json`|Dizinlerdeki tüm *appsettings.json* dosyalarını *dizin* klasöründen tam olarak bir düzey aşağı eşleştirin.|
|`directory/**/*.txt`|Dizin klasörü altında bulunan *bir .txt* uzantısı ile tüm dosyaları eşleşir. *directory*|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core özetleri dosya sistemi erişim Dosya Sağlayıcıları kullanımı yoluyla. Dosya Sağlayıcıları ASP.NET Çekirdek çerçevesi boyunca kullanılır:

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>uygulamanın [içerik kökünü](xref:fundamentals/index#content-root) ve web `IFileProvider` [kökünü](xref:fundamentals/index#web-root) tür olarak ortaya çıkarır.
* [Statik Dosya Middleware](xref:fundamentals/static-files) statik dosyaları bulmak için Dosya Sağlayıcıları kullanır.
* [Razor,](xref:mvc/views/razor) sayfaları ve görünümleri bulmak için Dosya Sağlayıcıları'nı kullanır.
* .NET Core araçlama, hangi dosyaların yayımlanması gerektiğini belirtmek için Dosya Sağlayıcıları ve glob desenlerini kullanır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Dosya Sağlayıcı arabirimleri

Birincil <xref:Microsoft.Extensions.FileProviders.IFileProvider>arabirim. `IFileProvider`yöntemleri ortaya çıkarır:

* Dosya bilgilerini<xref:Microsoft.Extensions.FileProviders.IFileInfo>alma ( ).
* Dizin bilgileri<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>edinin ( ).
* Değişiklik bildirimlerini ayarlama (bir <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo`dosyalarla çalışmak için yöntem ve özellikler sağlar:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(bayt olarak)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Tarih

[Dosyadan IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) yöntemini kullanarak okuyabilirsiniz.

Örnek uygulama, [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)yoluyla uygulama `Startup.ConfigureServices` boyunca kullanılmak üzere bir Dosya Sağlayıcısının nasıl yapılandırılabildiğini gösterir.

## <a name="file-provider-implementations"></a>Dosya Sağlayıcı uygulamaları

Üç uygulama `IFileProvider` mevcuttur.

| Uygulama | Açıklama |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Fiziksel sağlayıcı, sistemin fiziksel dosyalarına erişmek için kullanılır. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Manifest katıştırılmış sağlayıcı derlemeler gömülü dosyalara erişmek için kullanılır. |
| [KompozitDosya Sağlayıcısı](#compositefileprovider) | Bileşik sağlayıcı, bir veya daha fazla sağlayıcıdan gelen dosyalara ve dizinlere birleşik erişim sağlamak için kullanılır. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

Fiziksel <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> dosya sistemine erişim sağlar. `PhysicalFileProvider`<xref:System.IO.File?displayProperty=fullName> türünü (fiziksel sağlayıcı için) kullanır ve bir dizin ve çocuklarına giden tüm yolları scopelar. Bu kapsam, belirtilen dizin ve altlarının dışında dosya sistemine erişimi engeller. Oluşturmak ve kullanmak `PhysicalFileProvider` için en yaygın senaryo `IFileProvider` [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)yoluyla bir yapıcı bir istek.

Bu sağlayıcıyı doğrudan anında kullanırken, bir dizin yolu gereklidir ve sağlayıcı kullanılarak yapılan tüm istekler için temel yol olarak hizmet vermektedir.

Aşağıdaki kod, dizin `PhysicalFileProvider` içeriği ve dosya bilgilerini elde etmek için nasıl oluşturulup kullanılacağını gösterir:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Önceki örnekteki türler:

* `provider`bir `IFileProvider`.
* `contents`bir `IDirectoryContents`.
* `fileInfo`bir `IFileInfo`.

Dosya Sağlayıcı, tarafından `applicationRoot` belirtilen dizini doğrulamak veya dosya `GetFileInfo` bilgilerini almak için aramak için kullanılabilir. Dosya Sağlayıcısı'nın `applicationRoot` dizin dışında erişimi yoktur.

Örnek uygulama `Startup.ConfigureServices` [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider)kullanarak uygulamanın sınıfında sağlayıcı oluşturur:

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

Derlemeler <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> içinde katıştırılmış dosyalara erişmek için kullanılır. Katıştılı `ManifestEmbeddedFileProvider` dosyaların özgün yollarını yeniden oluşturmak için derlemede derlenmiş bir bildirim kullanır.

Katıştılmış dosyaların bir bildirimini `<GenerateEmbeddedFilesManifest>` oluşturmak `true`için özelliği ' ye göre ayarlayın. [ &lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)ile katıştırmak için dosyaları belirtin:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Derlemeye katıştırmak için bir veya daha fazla dosya belirtmek için [glob desenleri](#glob-patterns) kullanın.

Örnek uygulama bir `ManifestEmbeddedFileProvider` oluşturmaz ve şu anda çalıştırılamakta olan derlemeyi oluşturucuya geçirir.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Ek aşırı yüklemeler şunları sağlar:

* Göreli bir dosya yolu belirtin.
* Dosyaları son değiştirilmiş tarihe kadar kapsam.
* Katıştırılmış dosya bildirimini içeren katıştırılmış kaynağı adlandırın.

| Aşırı | Açıklama |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | İsteğe bağlı `root` göreli yol parametresini kabul eder. `root` Sağlanan yol altında <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> bu kaynaklara yapılan kapsam çağrılarını belirtin. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | İsteğe bağlı `root` göreli yol `lastModified` parametresi ve tarih (<xref:System.DateTimeOffset>) parametresini kabul eder. Tarih, `lastModified` <xref:Microsoft.Extensions.FileProviders.IFileInfo> <xref:Microsoft.Extensions.FileProviders.IFileProvider>'nin döndürülen örnekleri için son değişiklik tarihini kapsama |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | İsteğe bağlı `root` göreli `lastModified` yolu, `manifestName` tarihi ve parametreleri kabul eder. Bildirimiçeren `manifestName` katıştırılmış kaynağın adını temsil eder. |

### <a name="compositefileprovider"></a>KompozitDosya Sağlayıcısı

Örnekleri <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> birleştirerek, `IFileProvider` birden çok sağlayıcıdan gelen dosyalarla çalışmak için tek bir arabirimi açığa çıkarır. `CompositeFileProvider`Oluştururken, bir veya `IFileProvider` daha fazla örneği oluşturucuya geçirin.

Örnek uygulamada, uygulamanın `PhysicalFileProvider` `ManifestEmbeddedFileProvider` hizmet kapsayıcısında `CompositeFileProvider` kayıtlı bir dosya sağlar:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Değişikliklere dikkat edin

[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) yöntemi, değişiklikler için bir veya daha fazla dosya veya dizin izlemek için bir senaryo sağlar. `Watch`birden çok dosya belirtmek için [glob desenleri](#glob-patterns) kullanabilirsiniz bir yol dize, kabul eder. `Watch`bir <xref:Microsoft.Extensions.Primitives.IChangeToken>' yi döndürür. Değişiklik belirteci şunları ortaya çıkarır:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Bir değişikliğin meydana geldiğini belirlemek için denetlenebilecek bir özellik.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Belirtilen yol dizesine değişiklikler algılandığında çağrılır. Her değişiklik belirteci yalnızca tek bir değişikliğe yanıt olarak ilişkili geri arama çağırır. Sürekli izleme sağlamak için, <xref:System.Threading.Tasks.TaskCompletionSource`1> bir (aşağıda gösterilmiştir) kullanın veya değişikliklere yanıt olarak örnekleri yeniden oluşturun. `IChangeToken`

Örnek uygulamada, *WatchConsole* konsol uygulaması, bir metin dosyası değiştirildiğinde bir iletiyi görüntülemek üzere yapılandırılır:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Docker kapsayıcıları ve ağ paylaşımları gibi bazı dosya sistemleri, güvenilir bir şekilde değişiklik bildirimleri göndermeyebilir. Ortam `DOTNET_USE_POLLING_FILE_WATCHER` değişkenini `1` `true` her dört saniyede bir (yapılandırılamaz) değişiklikler için dosya sistemini yoklamak için ayarlayın.

## <a name="glob-patterns"></a>Glob desenleri

Dosya sistemi yolları *glob (veya globbing) desenleri*olarak adlandırılan joker karakter desenleri kullanır. Bu desenlere sahip dosya gruplarını belirtin. İki joker karakter ve `*` `**`şunlardır:

**`*`**  
Geçerli klasör düzeyindeki herhangi bir şeyle, dosya adıyla veya dosya uzantısını eşleşir. Eşleşmeler tarafından `/` `.` sonlandırılır ve dosya yolundaki karakterler.

**`**`**  
Birden çok dizin düzeyindeki her şeyle eşleşir. Dizin hiyerarşisi içindeki birçok dosyayı özyinelemeli olarak eşleştirmek için kullanılabilir.

**Glob desen örnekleri**

**`directory/file.txt`**  
Belirli bir dizindeki belirli bir dosyayla eşleşir.

**`directory/*.txt`**  
Tüm dosyaları *.txt* uzantısı ile belirli bir dizinde eşler.

**`directory/*/appsettings.json`**  
Dizinlerdeki tüm `appsettings.json` dosyaları *dizin* klasöründen tam olarak bir düzey aşağı eşleştirin.

**`directory/**/*.txt`**  
*Dizin* klasörü altında bulunan *.txt* uzantısı ile tüm dosyaları eşleşir.

::: moniker-end
