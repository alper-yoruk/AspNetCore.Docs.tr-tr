---
title: BlazorKarşıya dosya yükleme ASP.NET Core
author: guardrex
description: "' De dosyaları Blazor , InputFile bileşeniyle karşıya yüklemeyi öğrenin."
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
no-loc:
- appsettings.json
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
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: c0806c3a68a4d9e698925f6ec955dd2f53d7818f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056133"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a>BlazorKarşıya dosya yükleme ASP.NET Core

[Daniel Roth](https://github.com/danroth27) ve [Pranav Krishnamoorthy](https://github.com/pranavkm) tarafından

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

`InputFile`Dosya karşıya yükleme dahil olmak üzere, tarayıcı dosyası verilerini .net Code 'a okumak için bileşeni kullanın.

> [!WARNING]
> Dosya yükleme en iyi yöntemlerini her zaman izleyin. Daha fazla bilgi için bkz. <xref:mvc/models/file-uploads#security-considerations>.

## <a name="inputfile-component"></a>`InputFile` bileşeni

`InputFile`Bileşen türünde BIR HTML girişi olarak işler `file` .

Varsayılan olarak, Kullanıcı tek dosya seçer. `multiple`Kullanıcının aynı anda birden çok dosya yüklemesine izin vermek için özniteliğini ekleyin. Kullanıcı tarafından bir veya daha fazla dosya seçildiğinde, `InputFile` bileşen bir `OnChange` olayı harekete geçirir ve `InputFileChangeEventArgs` Seçili dosya listesine ve her dosya hakkındaki ayrıntılara erişim sağlayan bir ' de geçirir.

Kullanıcı tarafından seçilen dosyadan veri okumak için:

* Dosyada çağrı yapın `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` ve döndürülen akıştan okuyun. Daha fazla bilgi için [dosya akışları](#file-streams) bölümüne bakın.
* <xref:System.IO.Stream>Tarafından döndürülen, `OpenReadStream` okunan bayt cinsinden en büyük boyutu zorlar `Stream` . Varsayılan olarak, yalnızca boyut olarak 524.288 KB 'tan (512 KB) daha küçük dosyalara daha fazla okuma için izin verilir. Bu sınır, geliştiricilerin yanlışlıkla bellekteki büyük dosyaları yanlışlıkla okumasını engellemek için vardır. `maxAllowedSize`Üzerinde parametresi, `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` gerekirse daha büyük bir boyut belirtmek için kullanılabilir.
* Gelen dosya akışını doğrudan belleğe okumaktan kaçının. Örneğin, dosya baytlarını bir <xref:System.IO.MemoryStream> bayt dizisi olarak bir veya Read öğesine kopyalamayın. Bu yaklaşımlar, özellikle içinde, performans ve güvenlik sorunlarına neden olabilir Blazor Server . Bunun yerine, dosya baytlarını bir blob veya diskteki bir dosya gibi bir dış depoya kopyalamayı göz önünde bulundurun.

Görüntü dosyası alan bir bileşen, `RequestImageFileAsync` görüntünün uygulamaya akışı tamamlanmadan önce tarayıcının JavaScript çalışma zamanı içindeki görüntü verilerini yeniden boyutlandırmak için dosyanın kullanışlı yöntemini çağırabilir.

Aşağıdaki örnek, bir bileşende birden çok resim dosyasını karşıya yüklemeyi gösterir. `InputFileChangeEventArgs.GetMultipleFiles` birden çok dosyanın okunmasına izin verir. Kötü amaçlı bir kullanıcının uygulamanın beklediği kadar çok sayıda dosyayı karşıya yüklemesini engellemek için, okumak istediğiniz en fazla dosya sayısını belirtin. `InputFileChangeEventArgs.File` dosya karşıya yükleme birden çok dosyayı desteklemiyorsa, ilk ve yalnızca dosyanın okunmasına izin verir.

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

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
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
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

`IBrowserFile`[tarayıcı tarafından sunulan](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) meta verileri özellikler olarak döndürür. Bu meta veriler, ön doğrulama için yararlı olabilir. Örneğin, [ `FileUpload.razor` ve `FilePreview.razor` örnek bileşenlerine](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)bakın.

## <a name="file-streams"></a>Dosya akışları

Bir Blazor WebAssembly uygulamada, veriler doğrudan tarayıcı içindeki .net koduna akışla kaydedilir.

Bir Blazor Server uygulamada, dosya SignalR akışından okunan dosya verileri sunucuda .net koduna bağlantı üzerinden akışa kaydedilir. [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) için dosya yükleme özelliklerinin yapılandırılmasını sağlar Blazor Server .

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
