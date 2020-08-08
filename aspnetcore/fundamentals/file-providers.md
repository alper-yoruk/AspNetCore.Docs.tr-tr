---
title: ASP.NET Core dosya sağlayıcıları
author: rick-anderson
description: Dosya sağlayıcılarının kullanımı aracılığıyla dosya sistemi erişimini ASP.NET Core nasıl soyutleyeceğinizi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
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
uid: fundamentals/file-providers
ms.openlocfilehash: e2441a56fc8b05ba16dd8e368382501ac6b268a3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017447"
---
# <a name="file-providers-in-aspnet-core"></a>ASP.NET Core dosya sağlayıcıları

[Steve Smith](https://ardalis.com/) tarafından

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core dosya sistemi erişimini dosya sağlayıcılarının kullanımı üzerinden soyutlar. Dosya sağlayıcıları ASP.NET Core Framework boyunca kullanılır. Örnek:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>uygulamanın [içerik kökünü](xref:fundamentals/index#content-root) ve [Web kökünü](xref:fundamentals/index#web-root) türler olarak gösterir `IFileProvider` .
* [Statik dosya ara yazılımı](xref:fundamentals/static-files) , statik dosyaları bulmak Için dosya sağlayıcılarını kullanır.
* [Razor](xref:mvc/views/razor)sayfa ve görünümleri bulmak için dosya sağlayıcılarını kullanır.
* .NET Core araçları, hangi dosyaların yayımlanacak olduğunu belirlemek için dosya sağlayıcılarını ve glob düzenlerini kullanır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Dosya sağlayıcısı arabirimleri

Birincil arabirim <xref:Microsoft.Extensions.FileProviders.IFileProvider> . `IFileProvider`şunları yapmak için yöntemler sunar:

* Dosya bilgilerini edinin ( <xref:Microsoft.Extensions.FileProviders.IFileInfo> ).
* Dizin bilgilerini ( <xref:Microsoft.Extensions.FileProviders.IDirectoryContents> ) alın.
* Değişiklik bildirimlerini ayarlayın (bir kullanarak <xref:Microsoft.Extensions.Primitives.IChangeToken> ).

`IFileInfo`dosyalarla çalışma için yöntemler ve özellikler sağlar:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(bayt cinsinden)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>güncel

Yöntemini kullanarak dosyasından okuma yapabilirsiniz <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> .

*Fileprovidersample* örnek uygulaması, uygulamasında `Startup.ConfigureServices` [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama genelinde kullanılmak üzere bir dosya sağlayıcısının nasıl yapılandırılacağını gösterir.

## <a name="file-provider-implementations"></a>Dosya sağlayıcısı uygulamaları

Aşağıdaki tablo uygulamasının uygulamalarını listelemektedir `IFileProvider` .

| Uygulama | Açıklama |
| -------------- | ----------- |
| [CompositeFileProvider](#compositefileprovider) | Bir veya daha fazla sağlayıcıdan dosya ve dizinlere Birleşik erişim sağlamak için kullanılır. |
| [Bildirimli Estembeddedfileprovider](#manifestembeddedfileprovider) | Derlemelerde yerleşik dosyalara erişmek için kullanılır. |
| [PhysicalFileProvider](#physicalfileprovider) | Sistemin fiziksel dosyalarına erişmek için kullanılır. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>Fiziksel dosya sistemine erişim sağlar. `PhysicalFileProvider`, <xref:System.IO.File?displayProperty=fullName> türü (fiziksel sağlayıcı için) ve tüm yolları bir dizine ve alt öğelerine kullanır. Bu kapsam, belirtilen dizin ve alt öğeleri dışındaki dosya sistemine erişimi engeller. Oluşturma ve kullanma için en yaygın senaryo, `PhysicalFileProvider` `IFileProvider` [bağımlılık ekleme](xref:fundamentals/dependency-injection)aracılığıyla bir Oluşturucu içinde istekte bulunur.

Bu sağlayıcıyı doğrudan örnekledikten sonra, mutlak bir dizin yolu gereklidir ve sağlayıcı kullanılarak yapılan tüm isteklerin temel yolu olarak görev yapar. Glob desenleri dizin yolunda desteklenmez.

Aşağıdaki kod, `PhysicalFileProvider` dizin içeriğini ve dosya bilgilerini elde etmek için nasıl kullanılacağını gösterir:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

Önceki örnekteki türler:

* `provider`bir `IFileProvider` .
* `contents`bir `IDirectoryContents` .
* `fileInfo`bir `IFileInfo` .

Dosya sağlayıcısı, tarafından belirtilen dizin üzerinden yinelemek `applicationRoot` veya `GetFileInfo` bir dosyanın bilgilerini almak için çağırmak üzere kullanılabilir. Glob desenleri yönteme geçirilememelidir `GetFileInfo` . Dosya sağlayıcısı, dizin dışında bir erişime sahip değil `applicationRoot` .

*Fileprovidersample* örnek uygulaması, şunu kullanarak sağlayıcıyı oluşturur `Startup.ConfigureServices` <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> :

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>Bildirimli Estembeddedfileprovider

, <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> Derlemeler içine katıştırılmış dosyalara erişmek için kullanılır. , `ManifestEmbeddedFileProvider` Gömülü dosyaların özgün yollarını yeniden oluşturmak için derlemeye derlenen bir bildirim kullanır.

Katıştırılmış dosyaların bir bildirimini oluşturmak için:

1. Projenize [Microsoft. Extensions. FileProviders. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet paketini ekleyin.
1. `<GenerateEmbeddedFilesManifest>`Özelliğini olarak ayarlayın `true` . Eklenecek dosyaları belirtin [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) :

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

Derlemeye eklemek üzere bir veya daha fazla dosya belirtmek için [Glob desenlerini](#glob-patterns) kullanın.

*Fileprovidersample* örnek uygulaması bir oluşturur `ManifestEmbeddedFileProvider` ve şu anda yürütülmekte olan derlemeyi oluşturucusuna geçirir.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Ek aşırı yüklemeler şunları yapmanıza olanak sağlar:

* Göreli bir dosya yolu belirtin.
* Dosya kapsamını son değiştirilme tarihine kadar.
* Katıştırılmış dosya bildirimini içeren gömülü kaynağı adlandırın.

| Yüklemek | Açıklama |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | İsteğe bağlı bir `root` göreli yol parametresini kabul eder. `root` <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> Belirtilen yol altında bu kaynaklara yönelik olarak yapılan çağrıları belirtin. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | İsteğe bağlı `root` göreli yol parametresini ve bir `lastModified` date ( <xref:System.DateTimeOffset> ) parametresini kabul eder. `lastModified`Tarih kapsamları <xref:Microsoft.Extensions.FileProviders.IFileInfo> tarafından döndürülen örneklerin son değiştirilme tarihidir <xref:Microsoft.Extensions.FileProviders.IFileProvider> . |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | İsteğe bağlı bir `root` göreli yolu, `lastModified` tarihi ve parametreleri kabul eder `manifestName` . , `manifestName` Bildirimi içeren gömülü kaynağın adını temsil eder. |

### <a name="compositefileprovider"></a>CompositeFileProvider

, <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> `IFileProvider` Birden çok sağlayıcıdan dosyalarla çalışmak için tek bir arabirim ortaya çıkaran örnekleri birleştirir. Oluştururken `CompositeFileProvider` bir veya daha fazla `IFileProvider` örneği oluşturucuya geçirin.

*Fileprovidersample* örnek uygulamasında bir `PhysicalFileProvider` ve, `ManifestEmbeddedFileProvider` `CompositeFileProvider` uygulamanın hizmet kapsayıcısında kayıtlı bir dosya sağlayın. Aşağıdaki kod proje `Startup.ConfigureServices` yönteminde bulunur:

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Değişiklikleri izle

<xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType>Yöntemi, değişiklikler için bir veya daha fazla dosya veya dizini izlemek üzere bir senaryo sağlar. `Watch`Yöntemi:

* Birden çok dosya belirtmek için [Glob desenlerini](#glob-patterns) içerebilen bir dosya yolu dizesi kabul eder.
* Döndürür <xref:Microsoft.Extensions.Primitives.IChangeToken> .

Ortaya çıkan değişiklik belirteci şunları gösterir:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Bir değişikliğin oluşup oluşmadığını tespit etmek için incelenebilir bir özellik.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Belirtilen yol dizesinde değişiklikler algılandığında çağırılır. Her değişiklik belirteci yalnızca, ilişkili geri çağırma işlemini tek bir değişikliğe yanıt olarak çağırır. Sabit izlemeyi etkinleştirmek için bir <xref:System.Threading.Tasks.TaskCompletionSource`1> (aşağıda gösterilmiştir) kullanın veya `IChangeToken` değişiklikleri yanıt olarak yeniden oluşturun.

*WatchConsole* örnek uygulaması, *TextFiles* dizinindeki bir *. txt* dosyası her değiştirildiğinde bir ileti yazar:

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

Docker kapsayıcıları ve ağ paylaşımları gibi bazı dosya sistemleri, değişiklik bildirimlerini güvenilir bir şekilde gönderemeyebilir. `DOTNET_USE_POLLING_FILE_WATCHER`Ortam değişkenini, `1` `true` her dört saniyede bir değişiklikler için dosya sistemini yoklamak üzere veya olarak ayarlayın (yapılandırılamaz).

### <a name="glob-patterns"></a>Glob desenleri

Dosya sistemi yolları, *Glob (veya glob) desenleri*adlı joker karakter desenleri kullanır. Bu desenlerle dosya gruplarını belirtin. İki joker karakter şunlardır `*` `**` :

**`*`**  
Geçerli klasör düzeyindeki her şeyi, dosya adını veya herhangi bir dosya uzantısını eşleştirir. Eşleşmeler, `/` `.` dosya yolundaki karakterler ile sonlandırılır.

**`**`**  
Birden çok dizin düzeyindeki tüm öğeleri eşleştirir. , Bir Dizin hiyerarşisinde birçok dosya yinelemeli olarak eşleşmek için kullanılabilir.

Aşağıdaki tabloda, glob desenlerinin yaygın örnekleri verilmiştir.

|Desen  |Açıklama  |
|---------|---------|
|`directory/file.txt`|Belirli bir dizindeki belirli bir dosyayla eşleşir.|
|`directory/*.txt`|Belirli bir dizinde *. txt* uzantılı tüm dosyaları eşleştirir.|
|`directory/*/appsettings.json`|Dizinler içindeki dosyalardaki tüm *appsettings.js* , *Dizin* klasörünün altında tam olarak bir düzey ile eşleşir.|
|`directory/**/*.txt`|*. Txt* uzantılı tüm dosyaları, *Dizin* klasörünün altında herhangi bir yerde buldu.|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core dosya sistemi erişimini dosya sağlayıcılarının kullanımı üzerinden soyutlar. Dosya sağlayıcıları ASP.NET Core Framework boyunca kullanılır:

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>uygulamanın [içerik kökünü](xref:fundamentals/index#content-root) ve [Web kökünü](xref:fundamentals/index#web-root) türler olarak gösterir `IFileProvider` .
* [Statik dosya ara yazılımı](xref:fundamentals/static-files) , statik dosyaları bulmak Için dosya sağlayıcılarını kullanır.
* [Razor](xref:mvc/views/razor)sayfa ve görünümleri bulmak için dosya sağlayıcılarını kullanır.
* .NET Core araçları, hangi dosyaların yayımlanacak olduğunu belirlemek için dosya sağlayıcılarını ve glob düzenlerini kullanır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Dosya sağlayıcısı arabirimleri

Birincil arabirim <xref:Microsoft.Extensions.FileProviders.IFileProvider> . `IFileProvider`şunları yapmak için yöntemler sunar:

* Dosya bilgilerini edinin ( <xref:Microsoft.Extensions.FileProviders.IFileInfo> ).
* Dizin bilgilerini ( <xref:Microsoft.Extensions.FileProviders.IDirectoryContents> ) alın.
* Değişiklik bildirimlerini ayarlayın (bir kullanarak <xref:Microsoft.Extensions.Primitives.IChangeToken> ).

`IFileInfo`dosyalarla çalışma için yöntemler ve özellikler sağlar:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(bayt cinsinden)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>güncel

[Ifileınfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) yöntemini kullanarak dosyadan okuma yapabilirsiniz.

Örnek uygulama, uygulamasında `Startup.ConfigureServices` [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama genelinde kullanılmak üzere bir dosya sağlayıcısının nasıl yapılandırılacağını gösterir.

## <a name="file-provider-implementations"></a>Dosya sağlayıcısı uygulamaları

Üç uygulaması `IFileProvider` mevcuttur.

| Uygulama | Açıklama |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Fiziksel sağlayıcı, sistemin fiziksel dosyalarına erişmek için kullanılır. |
| [Bildirimli Estembeddedfileprovider](#manifestembeddedfileprovider) | Bildirimde yerleşik olarak bulunan dosyalara erişmek için bildirim eklenmiş sağlayıcı kullanılır. |
| [CompositeFileProvider](#compositefileprovider) | Bileşik sağlayıcı, bir veya daha fazla sağlayıcıdan gelen dosyalara ve dizinlere Birleşik erişim sağlamak için kullanılır. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>Fiziksel dosya sistemine erişim sağlar. `PhysicalFileProvider`, <xref:System.IO.File?displayProperty=fullName> türü (fiziksel sağlayıcı için) ve tüm yolları bir dizine ve alt öğelerine kullanır. Bu kapsam, belirtilen dizin ve alt öğeleri dışındaki dosya sistemine erişimi engeller. Oluşturma ve kullanma için en yaygın senaryo, `PhysicalFileProvider` `IFileProvider` [bağımlılık ekleme](xref:fundamentals/dependency-injection)aracılığıyla bir Oluşturucu içinde istekte bulunur.

Bu sağlayıcıyı doğrudan örnekleyen bir dizin yolu gereklidir ve sağlayıcı kullanılarak yapılan tüm isteklerin temel yolu olarak görev yapar.

Aşağıdaki kod, nasıl oluşturulacağını `PhysicalFileProvider` ve dizin içeriğini ve dosya bilgilerini almak için nasıl kullanılacağını gösterir:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Önceki örnekteki türler:

* `provider`bir `IFileProvider` .
* `contents`bir `IDirectoryContents` .
* `fileInfo`bir `IFileInfo` .

Dosya sağlayıcısı, tarafından belirtilen dizin üzerinden yinelemek `applicationRoot` veya `GetFileInfo` bir dosyanın bilgilerini almak için çağırmak üzere kullanılabilir. Dosya sağlayıcısı, dizin dışında bir erişime sahip değil `applicationRoot` .

Örnek uygulama, sağlayıcıyı `Startup.ConfigureServices` [ıhostingenvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider)kullanarak uygulamanın sınıfında oluşturur:

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>Bildirimli Estembeddedfileprovider

, <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> Derlemeler içine katıştırılmış dosyalara erişmek için kullanılır. , `ManifestEmbeddedFileProvider` Gömülü dosyaların özgün yollarını yeniden oluşturmak için derlemeye derlenen bir bildirim kullanır.

Gömülü dosyaların bir bildirimini oluşturmak için `<GenerateEmbeddedFilesManifest>` özelliğini olarak ayarlayın `true` . [ &lt; EmbeddedResource &gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)ile eklenecek dosyaları belirtin:

[!code-xml[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Derlemeye eklemek üzere bir veya daha fazla dosya belirtmek için [Glob desenlerini](#glob-patterns) kullanın.

Örnek uygulama bir oluşturur `ManifestEmbeddedFileProvider` ve şu anda yürütülmekte olan derlemeyi oluşturucusuna geçirir.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Ek aşırı yüklemeler şunları yapmanıza olanak sağlar:

* Göreli bir dosya yolu belirtin.
* Dosya kapsamını son değiştirilme tarihine kadar.
* Katıştırılmış dosya bildirimini içeren gömülü kaynağı adlandırın.

| Yüklemek | Açıklama |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | İsteğe bağlı bir `root` göreli yol parametresini kabul eder. `root` <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> Belirtilen yol altında bu kaynaklara yönelik olarak yapılan çağrıları belirtin. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | İsteğe bağlı `root` göreli yol parametresini ve bir `lastModified` date ( <xref:System.DateTimeOffset> ) parametresini kabul eder. `lastModified`Tarih kapsamları <xref:Microsoft.Extensions.FileProviders.IFileInfo> tarafından döndürülen örneklerin son değiştirilme tarihidir <xref:Microsoft.Extensions.FileProviders.IFileProvider> . |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | İsteğe bağlı bir `root` göreli yolu, `lastModified` tarihi ve parametreleri kabul eder `manifestName` . , `manifestName` Bildirimi içeren gömülü kaynağın adını temsil eder. |

### <a name="compositefileprovider"></a>CompositeFileProvider

, <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> `IFileProvider` Birden çok sağlayıcıdan dosyalarla çalışmak için tek bir arabirim ortaya çıkaran örnekleri birleştirir. Oluştururken `CompositeFileProvider` bir veya daha fazla `IFileProvider` örneği oluşturucuya geçirin.

Örnek uygulamada, `PhysicalFileProvider` ve, `ManifestEmbeddedFileProvider` `CompositeFileProvider` uygulamanın hizmet kapsayıcısında kayıtlı bir dosya sağlar:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Değişiklikleri izle

[IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) yöntemi, değişiklikler için bir veya daha fazla dosya ya da dizin izlemek üzere bir senaryo sağlar. `Watch`birden çok dosya belirtmek için [Glob desenlerini](#glob-patterns) içerebilen bir yol dizesi kabul eder. `Watch`döndürür <xref:Microsoft.Extensions.Primitives.IChangeToken> . Değişiklik belirteci şunları gösterir:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Bir değişikliğin oluşup oluşmadığını tespit etmek için incelenebilir bir özellik.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Belirtilen yol dizesinde değişiklikler algılandığında çağırılır. Her değişiklik belirteci yalnızca, ilişkili geri çağırma işlemini tek bir değişikliğe yanıt olarak çağırır. Sabit izlemeyi etkinleştirmek için bir <xref:System.Threading.Tasks.TaskCompletionSource`1> (aşağıda gösterilmiştir) kullanın veya `IChangeToken` değişiklikleri yanıt olarak yeniden oluşturun.

Örnek uygulamada, *WatchConsole* konsol uygulaması bir metin dosyası her değiştirildiğinde bir ileti görüntüleyecek şekilde yapılandırılmıştır:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Docker kapsayıcıları ve ağ paylaşımları gibi bazı dosya sistemleri, değişiklik bildirimlerini güvenilir bir şekilde gönderemeyebilir. `DOTNET_USE_POLLING_FILE_WATCHER`Ortam değişkenini, `1` `true` her dört saniyede bir değişiklikler için dosya sistemini yoklamak üzere veya olarak ayarlayın (yapılandırılamaz).

## <a name="glob-patterns"></a>Glob desenleri

Dosya sistemi yolları, *Glob (veya glob) desenleri*adlı joker karakter desenleri kullanır. Bu desenlerle dosya gruplarını belirtin. İki joker karakter şunlardır `*` `**` :

**`*`**  
Geçerli klasör düzeyindeki her şeyi, dosya adını veya herhangi bir dosya uzantısını eşleştirir. Eşleşmeler, `/` `.` dosya yolundaki karakterler ile sonlandırılır.

**`**`**  
Birden çok dizin düzeyindeki tüm öğeleri eşleştirir. , Bir Dizin hiyerarşisinde birçok dosya yinelemeli olarak eşleşmek için kullanılabilir.

**Glob deseninin örnekleri**

**`directory/file.txt`**  
Belirli bir dizindeki belirli bir dosyayla eşleşir.

**`directory/*.txt`**  
Belirli bir dizinde *. txt* uzantılı tüm dosyaları eşleştirir.

**`directory/*/appsettings.json`**  
`appsettings.json`Dizinteki tüm dosyaları, *Dizin* klasörünün altında tam olarak bir düzey eşler.

**`directory/**/*.txt`**  
*. Txt* uzantılı tüm dosyaları, *Dizin* klasörünün altında herhangi bir yerde buldu.

::: moniker-end
