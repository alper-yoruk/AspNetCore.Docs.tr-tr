---
title: dotnet aspnet-codegenerator komutu
author: rick-anderson
description: Dotnet aspnet-codegenerator komut iskeleleri ASP.NET Core projeleri.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 1043a578f66d5bb57f4a81e9fe21afa5e3c37cb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665188"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet aspnet-codegenerator

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator`- ASP.NET Core iskele motoru çalışır. `dotnet aspnet-codegenerator`sadece komut satırından iskele için gerekli olan, Visual Studio ile iskele kullanmak için gerekli değildir.

Bu makale [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) ve sonrası için geçerlidir.

## <a name="installing-aspnet-codegenerator"></a>Aspnet kodlayıcı yükleme

`dotnet-aspnet-codegenerator`yüklenmesi gereken genel bir [araçtır.](/dotnet/core/tools/global-tools) Aşağıdaki komut `dotnet-aspnet-codegenerator` aracın en son kararlı sürümünü yükler:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Yüklenen .NET `dotnet-aspnet-codegenerator` Core SDK'lardan edinilebilen en son kararlı sürüme aşağıdaki komut güncelleştirmeleri:

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Özet

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Açıklama

Küresel `dotnet aspnet-codegenerator` komut ASP.NET Core kod jeneratörve iskele motoru çalışır.

## <a name="arguments"></a>Bağımsız Değişkenler

`generator`

Çalışacak kod jeneratörü. Aşağıdaki jeneratörler mevcuttur:

| Oluşturucu | İşlem |
| ----------------- | ------------ | 
| alan      | [İskeleler Bir Alan](/aspnet/core/mvc/controllers/areas) |
  denetleyici| [İskele bir denetleyici](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  identity  | [İskele kimliği](/aspnet/core/security/authentication/scaffold-identity) |
  jilet sayfası | [İskeleler Jilet Sayfaları](/aspnet/core/tutorials/razor-pages/model) |
  görüntüle      | [İskeleler bir görünüm](/aspnet/core/mvc/views/overview) |

## <a name="options"></a>Seçenekler

`-n|--nuget-package-dir`

NuGet paket dizinini belirtir.

`-c|--configuration {Debug|Release}`

Yapı yapılandırmasını tanımlar. Varsayılan değer: `Debug`.

`-tfm|--target-framework`

Kullanılacak [Hedef Çerçeve.](/dotnet/standard/frameworks) Örneğin, `net46`.

`-b|--build-base-path`

Yapı tabanı yolu.

`-h|--help`

Komut için kısa bir yardım yazdırır.

`--no-build`

Çalıştırmadan önce projeyi oluşturmaz. Ayrıca bayrağı da `--no-restore` örtülü olarak ayarlar.

`-p|--project <PATH>`

Proje dosyasının çalışma yolunu (klasör adı veya tam yol) belirtir. Belirtilmemişse, varsayılan olarak geçerli dizine göre olur.

## <a name="generator-options"></a>Jeneratör seçenekleri

Aşağıdaki bölümler, desteklenen jeneratörler için mevcut seçenekleri ayrıntılı olarak açıklanır:

* Alan
* Denetleyicisi
* Kimlik  
* Razorpage
* Görüntüle

<a name="area"></a>

### <a name="area-options"></a>Alan seçenekleri

Bu araç, denetleyicileri ve görünümleri ile core web projeleri ASP.NET için tasarlanmıştır. Razor Pages uygulamaları için tasarlanmamıştır.

Kullanım: `dotnet aspnet-codegenerator area AreaNameToGenerate`

Önceki komut aşağıdaki klasörleri oluşturur:

* *Alanlar*
  * *AlanNameToGenerate*
    * *Denetleyiciler*
    * *Veri*
    * *Modeller*
    * *Görünümler*

<a name="ctl"></a>

### <a name="controller-options"></a>Denetleyici seçenekleri

Aşağıdaki tablo da `aspnet-codegenerator` `controller` seçenekler `razorpage`ve:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Aşağıdaki tabloda benzersiz `aspnet-codegenerator controller`seçenekler listeledir:

| Seçenek               | Açıklama|
| ----------------- | ------------ |
| --controllerName veya -name | Denetleyicinin adı. |
| --useAsyncActions veya -async | Async denetleyici eylemleri oluşturun. |
| --noViews veya -nv | Görünüm **oluşturmayın.** |
| --restWithNoViews veya -api  | REST stili API'li bir Denetleyici oluşturun. `noViews`varsayılabilir ve görünümle ilgili seçenekler yoksayılır. |
| --readWriteActions veya -actions | Model olmadan okuma/yazma eylemleri içeren denetleyici oluşturun. |

`aspnet-codegenerator controller` Komut üzerinde `-h` yardım için anahtarı kullanın:

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Örnek olarak [film modelini İskele'ye](/aspnet/core/tutorials/razor-pages/model) `dotnet aspnet-codegenerator controller`bakın.

### <a name="razorpage"></a>Razorpage

<a name="rp"></a>

Jilet Sayfaları, yeni sayfanın adını ve kullanılacak şablonu belirterek ayrı ayrı iskeleye eklenebilir. Desteklenen şablonlar şunlardır:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Örneğin, aşağıdaki komut *MyEdit.cshtml* oluşturmak için Edit şablonunu kullanır ve *MyEdit.cshtml.cs:*

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Genellikle, şablon ve oluşturulan dosya adı belirtilmemiş ve aşağıdaki şablonlar oluşturulur:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Aşağıdaki tablo da `aspnet-codegenerator` `razorpage` seçenekler `controller`ve:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Aşağıdaki tabloda benzersiz `aspnet-codegenerator razorpage`seçenekler listeledir:

| Seçenek               | Açıklama|
| ----------------- | ------------ |
|   --namespaceName veya -namespace | Oluşturulan PageModel için kullanılacak ad alanının adı |
| --partialView veya -kısmi | Kısmi bir görünüm oluşturun. Bu belirtilirse düzen seçenekleri -l ve -udl yoksayılır. |
| --noPageModel veya -npm | Boş şablon için PageModel sınıfı oluşturmamak için geçiş yap |

`aspnet-codegenerator razorpage` Komut üzerinde `-h` yardım için anahtarı kullanın:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Örnek olarak [film modelini İskele'ye](/aspnet/core/tutorials/razor-pages/model) `dotnet aspnet-codegenerator razorpage`bakın.

### <a name="identity"></a>Kimlik

Bkz. [İskele Kimliği](/aspnet/core/security/authentication/scaffold-identity)
