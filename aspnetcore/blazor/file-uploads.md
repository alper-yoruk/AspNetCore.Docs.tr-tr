---
title: BlazorKarşıya dosya yükleme ASP.NET Core
author: guardrex
description: "' De dosyaları Blazor , InputFile bileşeniyle karşıya yüklemeyi öğrenin."
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/file-uploads
ms.openlocfilehash: de4654f2efc401143e066628b096052efa65d7a0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722997"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a>BlazorKarşıya dosya yükleme ASP.NET Core

[Daniel Roth](https://github.com/danroth27) tarafından

`InputFile`Dosya karşıya yükleme dahil olmak üzere, tarayıcı dosyası verilerini .net Code 'a okumak için bileşeni kullanın. `InputFile`Bileşen türünde BIR HTML girişi olarak işler `file` .

Varsayılan olarak, Kullanıcı tek dosya seçer. `multiple`Kullanıcının aynı anda birden çok dosya yüklemesine izin vermek için özniteliğini ekleyin. Kullanıcı tarafından bir veya daha fazla dosya seçildiğinde, `InputFile` bileşen bir `OnChange` olayı harekete geçirir ve `InputFileChangeEventArgs` Seçili dosya listesine ve her dosya hakkındaki ayrıntılara erişim sağlayan bir ' de geçirir.

Görüntü dosyası alan bir bileşen, `RequestImageFileAsync` görüntünün uygulamaya akışı tamamlanmadan önce tarayıcının JavaScript çalışma zamanı içindeki görüntü verilerini yeniden boyutlandırmak için dosyanın kullanışlı yöntemini çağırabilir.

Aşağıdaki örnek, bir bileşende birden çok resim dosyasını karşıya yüklemeyi gösterir:

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h3>Images</h3>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var imageFiles = e.GetMultipleFiles();
        var format = "image/png";

        foreach (var imageFile in imageFiles)
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

Kullanıcı tarafından seçilen bir dosyadan veri okumak için, dosyayı çağırın `OpenReadStream` ve döndürülen akıştan okuyun. Bir Blazor WebAssembly uygulamada, veriler doğrudan tarayıcı içindeki .net koduna akışla kaydedilir. Bir Blazor Server uygulamada, dosya akıştan okunana kadar dosya verileri sunucuda .net koduna akışla kaydedilir. 
