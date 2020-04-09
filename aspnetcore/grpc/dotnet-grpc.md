---
title: dotnet-grpc ile Protobuf başvurularını yönetme
author: juntaoluo
description: Dotnet-grpc global aracıyla Protobuf referanslarını ekleme, güncelleme, kaldırma ve listeleme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667337"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>dotnet-grpc ile Protobuf başvurularını yönetme

Yazar: [John Luo](https://github.com/juntaoluo)

`dotnet-grpc`bir .NET gRPC projesi içinde [Protobuf (*.proto*)](xref:grpc/basics#proto-file) referanslarını yönetmek için bir .NET Core Global Aracıdır. Araç, Protobuf başvuruları eklemek, yenilemek, kaldırmak ve listelemek için kullanılabilir.

## <a name="installation"></a>Yükleme

`dotnet-grpc` [.NET Core Global Aracı'nı](/dotnet/core/tools/global-tools)yüklemek için aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Referans ekleme

`dotnet-grpc`*.csproj* dosyasına öğe `<Protobuf />` olarak Protobuf başvuruları eklemek için kullanılabilir:

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Protobuf başvuruları C# istemcisi ve/veya sunucu varlıklarını oluşturmak için kullanılır. `dotnet-grpc` Araç şunları yapabilir:

* Diskteki yerel dosyalardan bir Protobuf başvurusu oluşturun.
* URL tarafından belirtilen uzak bir dosyadan bir Protobuf başvurusu oluşturun.
* Doğru gRPC paket bağımlılıklarının projeye eklenmediğinden emin olun.

Örneğin, `Grpc.AspNetCore` paket bir web uygulamasına eklenir. `Grpc.AspNetCore`gRPC sunucusu ve istemci kitaplıkları ve araç desteği içerir. Alternatif olarak, `Grpc.Net.Client` `Grpc.Tools` yalnızca `Google.Protobuf` gRPC istemci kitaplıkları ve araç desteği içeren , ve paketler konsol uygulamasına eklenir.

### <a name="add-file"></a>Dosya ekle

Komut, `add-file` Protobuf başvuruları olarak diske yerel dosyaları eklemek için kullanılır. Sağlanan dosya yolları:

* Geçerli dizin veya mutlak yollargöreli olabilir.
* Desen tabanlı dosya [parlaması](https://wikipedia.org/wiki/Glob_(programming))için joker karakterler içerebilir.

Proje dizininin dışında herhangi bir `Link` dosya varsa, dosyayı Visual `Protos` Studio'daki klasörün altında görüntülemek için bir öğe eklenir.

### <a name="usage"></a>Kullanım

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Bağımsız Değişkenler

| Bağımsız Değişken | Açıklama |
|-|-|
| files | Protobuf dosyası referansları. Bu yerel protobuf dosyaları için glob bir yol olabilir. |

#### <a name="options"></a>Seçenekler

| Kısa seçenek | Uzun seçenek | Açıklama |
|-|-|-|
| -p | --proje | Çalışacak proje dosyasına giden yol. Bir dosya belirtilmemişse, komut geçerli dizini arar.
| -s | --hizmetler | Oluşturulması gereken gRPC hizmetlerinin türü. `Default` Belirtilirse, `Both` Web projeleri için `Client` kullanılır ve Web olmayan projeler için kullanılır. Kabul edilen `Both`değerler `Client` `Default`, `None` `Server`, , , .
| -i | --ek-alma-dirs | Protobuf dosyaları için içeri aktarımları çözerken kullanılacak ek dizinler. Bu, yarım nokta nokta lı ayrılmış bir yol listesidir.
| | --erişim | Oluşturulan C# sınıfları için kullanılacak erişim değiştirici. Varsayılan değer: `Public`. Kabul edilen `Internal` değerler `Public`ve .

### <a name="add-url"></a>URL ekle

Komut, `add-url` kaynak URL tarafından Protobuf başvurusu olarak belirtilen uzak bir dosya eklemek için kullanılır. Uzak dosyanın nerede indirilir belirtilmesi için bir dosya yolu sağlanmalıdır. Dosya yolu geçerli dizine veya mutlak bir yola göreolabilir. Dosya yolu proje dizininin dışındaysa, `Link` dosyayı Visual Studio'daki sanal `Protos` klasörün altında görüntülemek için bir öğe eklenir.

### <a name="usage"></a>Kullanım

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Bağımsız Değişkenler

| Bağımsız Değişken | Açıklama |
|-|-|
| url | Uzak bir protobuf dosyasının URL'si. |

#### <a name="options"></a>Seçenekler

| Kısa seçenek | Uzun seçenek | Açıklama |
|-|-|-|
| -o | --output | Uzaktan protobuf dosyasının indirme yolunu belirtir. Bu gerekli bir seçenektir.
| -p | --proje | Çalışacak proje dosyasına giden yol. Bir dosya belirtilmemişse, komut geçerli dizini arar.
| -s | --hizmetler | Oluşturulması gereken gRPC hizmetlerinin türü. `Default` Belirtilirse, `Both` Web projeleri için `Client` kullanılır ve Web olmayan projeler için kullanılır. Kabul edilen `Both`değerler `Client` `Default`, `None` `Server`, , , .
| -i | --ek-alma-dirs | Protobuf dosyaları için içeri aktarımları çözerken kullanılacak ek dizinler. Bu, yarım nokta nokta lı ayrılmış bir yol listesidir.
| | --erişim | Oluşturulan C# sınıfları için kullanılacak erişim değiştirici. Varsayılan değer. `Public` Kabul edilen `Internal` değerler `Public`ve .

## <a name="remove"></a>Kaldır

Komut, `remove` *.csproj* dosyasından Protobuf başvurularını kaldırmak için kullanılır. Komut, yol bağımsız değişkenlerini ve kaynak URL'lerini bağımsız değişken olarak kabul eder. Araç:

* Yalnızca Protobuf referansını kaldırır.
* Uzak bir URL'den ilk olarak indirilmiş olsa bile *.proto* dosyasını silmez.

### <a name="usage"></a>Kullanım

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Bağımsız Değişkenler

| Bağımsız Değişken | Açıklama |
|-|-|
| başvurular | Kaldırılacak protobuf başvurularının URL'leri veya dosya yolları. |

### <a name="options"></a>Seçenekler

| Kısa seçenek | Uzun seçenek | Açıklama |
|-|-|-|
| -p | --proje | Çalışacak proje dosyasına giden yol. Bir dosya belirtilmemişse, komut geçerli dizini arar.

## <a name="refresh"></a>Yenile

Komut, kaynak URL'deki `refresh` en son içerikle uzak bir başvuruyu güncelleştirmek için kullanılır. Hem karşıdan yükleme dosyası yolu hem de kaynak URL güncellenecek başvuruyu belirtmek için kullanılabilir. Not:

* Yerel dosyanın güncelleştirilip güncelleştirilmeyeceğini belirlemek için dosya içeriğinin işlenme leri karşılaştırılır.
* Hiçbir zaman damgası bilgisi karşılaştırılmada.

Bir güncelleştirme gerekiyorsa araç her zaman uzak dosya ile yerel dosya değiştirir.

### <a name="usage"></a>Kullanım

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Bağımsız Değişkenler

| Bağımsız Değişken | Açıklama |
|-|-|
| başvurular | URL'ler veya dosya yolları güncelleştirilmelidir uzak protobuf başvuruları için. Tüm uzak başvuruları yenilemek için bu bağımsız değişkeni boş bırakın. |

### <a name="options"></a>Seçenekler

| Kısa seçenek | Uzun seçenek | Açıklama |
|-|-|-|
| -p | --proje | Çalışacak proje dosyasına giden yol. Bir dosya belirtilmemişse, komut geçerli dizini arar.
| | --kuru-çalıştır | Yeni içerik indirilmeden güncellenecek dosyaların listesini çıktırıyor.

## <a name="list"></a>Liste

Komut, `list` proje dosyasındaki tüm Protobuf başvurularını görüntülemek için kullanılır. Bir sütunun tüm değerleri varsayılan değerlerse, sütun atlanabilir.

### <a name="usage"></a>Kullanım

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Seçenekler

| Kısa seçenek | Uzun seçenek | Açıklama |
|-|-|-|
| -p | --proje | Çalışacak proje dosyasına giden yol. Bir dosya belirtilmemişse, komut geçerli dizini arar.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
