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
ms.openlocfilehash: ca49564136e030fdaf86eefac56146fcb79f7bad
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673958"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="fdca1-103">BlazorKarşıya dosya yükleme ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fdca1-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="fdca1-104">[Daniel Roth](https://github.com/danroth27) ve [Pranav Krishnamoorthy](https://github.com/pranavkm) tarafından</span><span class="sxs-lookup"><span data-stu-id="fdca1-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="fdca1-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fdca1-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fdca1-106">`InputFile`Dosya karşıya yükleme dahil olmak üzere, tarayıcı dosyası verilerini .net Code 'a okumak için bileşeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="fdca1-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="fdca1-107">Dosya yükleme en iyi yöntemlerini her zaman izleyin.</span><span class="sxs-lookup"><span data-stu-id="fdca1-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="fdca1-108">Daha fazla bilgi için bkz. <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="fdca1-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="fdca1-109">`InputFile` bileşeni</span><span class="sxs-lookup"><span data-stu-id="fdca1-109">`InputFile` component</span></span>

<span data-ttu-id="fdca1-110">`InputFile`Bileşen türünde BIR HTML girişi olarak işler `file` .</span><span class="sxs-lookup"><span data-stu-id="fdca1-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="fdca1-111">Varsayılan olarak, Kullanıcı tek dosya seçer.</span><span class="sxs-lookup"><span data-stu-id="fdca1-111">By default, the user selects single files.</span></span> <span data-ttu-id="fdca1-112">`multiple`Kullanıcının aynı anda birden çok dosya yüklemesine izin vermek için özniteliğini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fdca1-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="fdca1-113">Kullanıcı tarafından bir veya daha fazla dosya seçildiğinde, `InputFile` bileşen bir `OnChange` olayı harekete geçirir ve `InputFileChangeEventArgs` Seçili dosya listesine ve her dosya hakkındaki ayrıntılara erişim sağlayan bir ' de geçirir.</span><span class="sxs-lookup"><span data-stu-id="fdca1-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="fdca1-114">Kullanıcı tarafından seçilen dosyadan veri okumak için:</span><span class="sxs-lookup"><span data-stu-id="fdca1-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="fdca1-115">Dosyada çağrı yapın `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` ve döndürülen akıştan okuyun.</span><span class="sxs-lookup"><span data-stu-id="fdca1-115">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="fdca1-116">Daha fazla bilgi için [dosya akışları](#file-streams) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="fdca1-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="fdca1-117"><xref:System.IO.Stream>Tarafından döndürülen, `OpenReadStream` okunan bayt cinsinden en büyük boyutu zorlar `Stream` .</span><span class="sxs-lookup"><span data-stu-id="fdca1-117">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="fdca1-118">Varsayılan olarak, boyutu 512.000 bayttan (500 KB) daha büyük olmayan dosyalar, daha fazla okuma yapılmadan önce okunarak bir özel duruma neden olur.</span><span class="sxs-lookup"><span data-stu-id="fdca1-118">By default, files no larger than 512,000 bytes (500 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="fdca1-119">Bu sınır, geliştiricilerin yanlışlıkla bellekteki büyük dosyaları yanlışlıkla okumasını engellemek için vardır.</span><span class="sxs-lookup"><span data-stu-id="fdca1-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="fdca1-120">`maxAllowedSize`Üzerinde parametresi, `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` gerekirse daha büyük bir boyut belirtmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="fdca1-120">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="fdca1-121">Gelen dosya akışını doğrudan belleğe okumaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="fdca1-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="fdca1-122">Örneğin, dosya baytlarını bir <xref:System.IO.MemoryStream> bayt dizisi olarak bir veya Read öğesine kopyalamayın.</span><span class="sxs-lookup"><span data-stu-id="fdca1-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="fdca1-123">Bu yaklaşımlar, özellikle içinde, performans ve güvenlik sorunlarına neden olabilir Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="fdca1-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="fdca1-124">Bunun yerine, dosya baytlarını bir blob veya diskteki bir dosya gibi bir dış depoya kopyalamayı göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="fdca1-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="fdca1-125">Görüntü dosyası alan bir bileşen, `RequestImageFileAsync` görüntünün uygulamaya akışı tamamlanmadan önce tarayıcının JavaScript çalışma zamanı içindeki görüntü verilerini yeniden boyutlandırmak için dosyanın kullanışlı yöntemini çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="fdca1-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="fdca1-126">Aşağıdaki örnek, bir bileşende birden çok resim dosyasını karşıya yüklemeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="fdca1-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="fdca1-127">`InputFileChangeEventArgs.GetMultipleFiles` birden çok dosyanın okunmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="fdca1-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="fdca1-128">Kötü amaçlı bir kullanıcının uygulamanın beklediği kadar çok sayıda dosyayı karşıya yüklemesini engellemek için, okumak istediğiniz en fazla dosya sayısını belirtin.</span><span class="sxs-lookup"><span data-stu-id="fdca1-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="fdca1-129">`InputFileChangeEventArgs.File` dosya karşıya yükleme birden çok dosyayı desteklemiyorsa, ilk ve yalnızca dosyanın okunmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="fdca1-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

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

<span data-ttu-id="fdca1-130">`IBrowserFile`[tarayıcı tarafından sunulan](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) meta verileri özellikler olarak döndürür.</span><span class="sxs-lookup"><span data-stu-id="fdca1-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="fdca1-131">Bu meta veriler, ön doğrulama için yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="fdca1-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="fdca1-132">Örneğin, [ `FileUpload.razor` ve `FilePreview.razor` örnek bileşenlerine](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)bakın.</span><span class="sxs-lookup"><span data-stu-id="fdca1-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="fdca1-133">Dosya akışları</span><span class="sxs-lookup"><span data-stu-id="fdca1-133">File streams</span></span>

<span data-ttu-id="fdca1-134">Bir Blazor WebAssembly uygulamada, veriler doğrudan tarayıcı içindeki .net koduna akışla kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="fdca1-134">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="fdca1-135">Bir Blazor Server uygulamada, dosya SignalR akışından okunan dosya verileri sunucuda .net koduna bağlantı üzerinden akışa kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="fdca1-135">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="fdca1-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) için dosya yükleme özelliklerinin yapılandırılmasını sağlar Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="fdca1-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fdca1-137">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fdca1-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
