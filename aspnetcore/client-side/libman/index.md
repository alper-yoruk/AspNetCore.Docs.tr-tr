---
title: LibMan ile ASP.NET Core'da istemci tarafı kitaplık edinimi
author: scottaddie
description: Kitaplık Yöneticisi 'ni (LibMan) kullanarak istemci tarafındaki kitaplık varlıklarını ASP.NET Core projesine nasıl yükleyebilirsiniz öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: 87987446b7f2c625da90951510e697e06569ba36
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664670"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>LibMan ile ASP.NET Core'da istemci tarafı kitaplık edinimi

Yazar: [Scott Addie](https://twitter.com/Scott_Addie)

Kitaplık Yöneticisi (LibMan) hafif, istemci tarafı kitaplık edinme aracıdır. LibMan, popüler kitaplıkları ve çerçeveleri dosya sisteminden veya [içerik dağıtım ağından (CDN)](https://wikipedia.org/wiki/Content_delivery_network)indirir. Desteklenen [CDNs CDNJS](https://cdnjs.com/)dahil , [jsDelivr](https://www.jsdelivr.com/), ve [unpkg](https://unpkg.com/#/). Seçili kitaplık dosyaları getirilir ve ASP.NET Çekirdek projesi içinde uygun konuma yerleştirilir.

## <a name="libman-use-cases"></a>LibMan kullanım örnekleri

LibMan aşağıdaki avantajları sunar:

* Yalnızca gereksinim duyduğunuz kitaplık dosyaları indirilir.
* [Not.js,](https://nodejs.org) [npm](https://www.npmjs.com)ve [WebPack](https://webpack.js.org)gibi ek araç, kitaplıktaki dosyaların bir alt kümesini elde etmek için gerekli değildir.
* Dosyalar, görevler oluşturma veya el ile dosya kopyalamaya başvurmadan belirli bir konuma yerleştirilebilir.

LibMan'ın avantajları hakkında daha fazla bilgi için [Visual Studio 2017: LibMan segmentinde Modern ön uç web geliştirmesini](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s)izleyin.

LibMan bir paket yönetim sistemi değil. Zaten npm veya [iplik](https://yarnpkg.com)gibi bir paket yöneticisi kullanıyorsanız, bunu yapmaya devam edin. LibMan bu aletleri değiştirmek için geliştirilmemiş.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [LibMan GitHub deposu](https://github.com/aspnet/LibraryManager)
