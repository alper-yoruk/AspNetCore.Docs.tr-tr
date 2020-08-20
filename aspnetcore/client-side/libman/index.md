---
title: LibMan ile ASP.NET Core istemci tarafı kitaplık alma
author: scottaddie
description: Kitaplık Yöneticisi 'Ni (LibMan) kullanarak bir ASP.NET Core projesindeki istemci tarafı kitaplık varlıklarını yüklemeyi öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/index
ms.openlocfilehash: 8458f2fe1cdc139a3f22c7d73675301d89ad368e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625641"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>LibMan ile ASP.NET Core istemci tarafı kitaplık alma

[Scott Ade](https://twitter.com/Scott_Addie) tarafından

Kitaplık Yöneticisi (LibMan), basit, istemci tarafı bir kitaplık alma aracıdır. LibMan, dosya sisteminden veya bir [içerik teslim ağından (CDN)](https://wikipedia.org/wiki/Content_delivery_network)popüler kitaplıkları ve çerçeveleri indirir. Desteklenen CDNs, [Cdnjs](https://cdnjs.com/), [jsdelivr](https://www.jsdelivr.com/)ve [unpkg](https://unpkg.com/#/)'yi içerir. Seçilen kitaplık dosyaları getirildi ve ASP.NET Core projesi içindeki uygun konuma yerleştirildi.

## <a name="libman-use-cases"></a>LibMan kullanım örnekleri

LibMan aşağıdaki avantajları sunar:

* Yalnızca ihtiyacınız olan kitaplık dosyaları indirilir.
* [Node.js](https://nodejs.org), [NPM](https://www.npmjs.com)ve [WebPack](https://webpack.js.org)gibi ek araçlar, bir kitaplıktaki dosyaların bir alt kümesini almak için gerekli değildir.
* Dosyalar, derleme görevleri veya el ile dosya kopyalama işlemleri yapılmadan belirli bir konuma yerleştirilebilir.

LibMan 'ın avantajları hakkında daha fazla bilgi için [Visual Studio 2017: Libman segmentinde modern ön uç Web geliştirmesini](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s)izleyin.

LibMan bir paket yönetim sistemi değildir. Zaten NPM veya [Yarn](https://yarnpkg.com)gibi bir paket Yöneticisi kullanıyorsanız, bunu yapmaya devam edin. LibMan, bu araçların yerini alacak şekilde geliştirilmedi.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [LibMan GitHub deposu](https://github.com/aspnet/LibraryManager)
