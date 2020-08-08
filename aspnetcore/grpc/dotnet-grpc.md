---
title: dotnet-grpc ile Protobuf başvurularını yönetme
author: juntaoluo
description: DotNet-GRPC küresel aracıyla prototip başvuruları ekleme, güncelleştirme, kaldırma ve listeleme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: d41958d586f54d5944af187933f2b0248f763171
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016135"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>dotnet-grpc ile Protobuf başvurularını yönetme

[John Luo](https://github.com/juntaoluo) tarafından

`dotnet-grpc`, bir .NET gRPC projesindeki [prototip (*. proto*)](xref:grpc/basics#proto-file) başvurularını yönetmeye yönelik .NET Core küresel bir araçtır. Araç, prototipleme başvurularını eklemek, yenilemek, kaldırmak ve listelemek için kullanılabilir.

## <a name="installation"></a>Yükleme

`dotnet-grpc` [.NET Core küresel aracını](/dotnet/core/tools/global-tools)yüklemek için şu komutu çalıştırın:

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Başvuru Ekle

`dotnet-grpc`, `<Protobuf />` *. csproj* dosyasına öğe olarak prototip başvuruları eklemek için kullanılabilir:

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Prototip başvuruları, C# istemcisi ve/veya sunucu varlıklarını oluşturmak için kullanılır. `dotnet-grpc`Araç şunları yapabilir:

* Diskteki yerel dosyalardan Prototipsiz başvuru oluştur.
* Bir URL ile belirtilen uzak dosyadan Protobir başvuru oluştur.
* Projeye doğru gRPC paket bağımlılıklarının eklendiğinden emin olun.

Örneğin, `Grpc.AspNetCore` paket bir Web uygulamasına eklenir. `Grpc.AspNetCore`gRPC sunucusu ve istemci kitaplıklarını ve araç desteğini içerir. Alternatif olarak, `Grpc.Net.Client` `Grpc.Tools` ve `Google.Protobuf` yalnızca GRPC istemci kitaplıklarını ve araç desteğini içeren paketleri konsol uygulamasına eklenir.

### <a name="add-file"></a>Dosya ekle

`add-file`Komut, disk üzerindeki yerel dosyaları prototip başvuruları olarak eklemek için kullanılır. Belirtilen dosya yolları:

* Geçerli dizin veya mutlak yollarla göreli olabilir.
* , [Glob](https://wikipedia.org/wiki/Glob_(programming))model tabanlı dosya için joker karakterler içerebilir.

Herhangi bir dosya proje dizininin dışındaysa, `Link` dosyayı `Protos` Visual Studio 'daki klasörü altında göstermek için bir öğe eklenir.

### <a name="usage"></a>Kullanım

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Arguments

| Bağımsız Değişken | Açıklama |
|-|-|
| files | Prototip dosyası başvuruları. Bunlar yerel prototipli dosyalar için glob 'nin bir yolu olabilir. |

#### <a name="options"></a>Seçenekler

| Short seçeneği | Long seçeneği | Açıklama |
|-|-|-|
| -p | --Proje | Üzerinde çalışılacak proje dosyasının yolu. Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.
| -s | --Hizmetler | Oluşturulması gereken gRPC Hizmetleri türü. `Default`Belirtilmişse, `Both` Web projeleri için kullanılır ve `Client` Web dışı projeler için kullanılır. Kabul edilen değerler,,,, `Both` `Client` `Default` `None` `Server` .
| -i | --ek-içeri aktarma-Dizin | Prototip dosyaları için içeri aktarmalar çözümlenirken kullanılacak ek dizinler. Bu, yolların noktalı virgülle ayrılmış listesidir.
| | --erişim | Oluşturulan C# sınıfları için kullanılacak erişim değiştiricisi. Varsayılan değer: `Public`. Kabul edilen değerler `Internal` şunlardır `Public` .

### <a name="add-url"></a>URL Ekle

`add-url`Komut, kaynak URL tarafından belirtilen bir uzak dosyayı prototipde başvuru olarak eklemek için kullanılır. Uzak dosyanın nereye indirileceği belirtmek için bir dosya yolu belirtilmelidir. Dosya yolu, geçerli dizin veya mutlak bir yol ile ilişkili olabilir. Dosya yolu proje dizininin dışındaysa, `Link` dosyayı `Protos` Visual Studio 'daki sanal klasör altında göstermek için bir öğe eklenir.

### <a name="usage"></a>Kullanım

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Arguments

| Bağımsız Değişken | Açıklama |
|-|-|
| url | Uzak protoarabellek dosyasının URL 'SI. |

#### <a name="options"></a>Seçenekler

| Short seçeneği | Long seçeneği | Açıklama |
|-|-|-|
| -o | --output | Uzak protoarabellek dosyası için indirme yolunu belirtir. Bu gerekli bir seçenektir.
| -p | --Proje | Üzerinde çalışılacak proje dosyasının yolu. Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.
| -s | --Hizmetler | Oluşturulması gereken gRPC Hizmetleri türü. `Default`Belirtilmişse, `Both` Web projeleri için kullanılır ve `Client` Web dışı projeler için kullanılır. Kabul edilen değerler,,,, `Both` `Client` `Default` `None` `Server` .
| -i | --ek-içeri aktarma-Dizin | Prototip dosyaları için içeri aktarmalar çözümlenirken kullanılacak ek dizinler. Bu, yolların noktalı virgülle ayrılmış listesidir.
| | --erişim | Oluşturulan C# sınıfları için kullanılacak erişim değiştiricisi. Varsayılan değer `Public` . Kabul edilen değerler `Internal` şunlardır `Public` .

## <a name="remove"></a>Kaldır

`remove`Komut, *. csproj* dosyasından prototipsiz başvuruları kaldırmak için kullanılır. Komut bağımsız değişken olarak yol bağımsız değişkenlerini ve kaynak URL 'Lerini kabul eder. Araç:

* Yalnızca Prototipsiz başvuruyu kaldırır.
* , İlk olarak uzak bir URL 'den indirilse bile, *. proto* dosyasını silmez.

### <a name="usage"></a>Kullanım

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Arguments

| Bağımsız Değişken | Açıklama |
|-|-|
| başvurular | Kaldırılacak prototip başvurularının URL 'Leri veya dosya yolları. |

### <a name="options"></a>Seçenekler

| Short seçeneği | Long seçeneği | Açıklama |
|-|-|-|
| -p | --Proje | Üzerinde çalışılacak proje dosyasının yolu. Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.

## <a name="refresh"></a>Yenile

`refresh`Komut, kaynak URL 'den en son içerikle uzak bir başvuruyu güncelleştirmek için kullanılır. Yalnızca indirme dosyası yolu ve kaynak URL 'SI, güncellenmek üzere olan başvuruyu belirtmek için kullanılabilir. Not:

* Dosya içeriğinin karmaları yerel dosyanın güncelleştirilip güncelleştirilmediğini belirleme ile karşılaştırılır.
* Zaman damgası bilgisi karşılaştırılmaz.

Bir güncelleştirme gerekiyorsa araç her zaman yerel dosyayı uzak dosya ile değiştirir.

### <a name="usage"></a>Kullanım

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Arguments

| Bağımsız Değişken | Açıklama |
|-|-|
| başvurular | Güncellenmesi gereken uzak prototip başvurularına yönelik URL veya dosya yolları. Tüm Uzak başvuruları yenilemek için bu bağımsız değişkeni boş bırakın. |

### <a name="options"></a>Seçenekler

| Short seçeneği | Long seçeneği | Açıklama |
|-|-|-|
| -p | --Proje | Üzerinde çalışılacak proje dosyasının yolu. Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.
| | --Kuru-Çalıştır | Herhangi bir yeni içerik indirilmeden güncelleştirilenecek dosyaların listesini verir.

## <a name="list"></a>Liste

`list`Komut, proje dosyasındaki tüm prototipsiz başvuruları göstermek için kullanılır. Bir sütunun tüm değerleri varsayılan değerler ise, sütun atlanabilir.

### <a name="usage"></a>Kullanım

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Seçenekler

| Short seçeneği | Long seçeneği | Açıklama |
|-|-|-|
| -p | --Proje | Üzerinde çalışılacak proje dosyasının yolu. Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
