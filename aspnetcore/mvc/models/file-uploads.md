---
title: ASP.NET Core dosyaları karşıya yükleme
author: rick-anderson
description: ASP.NET Core MVC 'de dosyaları karşıya yüklemek için model bağlama ve akışı kullanma.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/03/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/file-uploads
ms.openlocfilehash: b613ccd8df65e41b86793466a0ed5dc7bf7e8772
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976759"
---
# <a name="upload-files-in-aspnet-core"></a>ASP.NET Core dosyaları karşıya yükleme

[Steve Smith](https://ardalis.com/) ve [Rutger fırtınası](https://github.com/rutix) tarafından

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core, daha küçük dosyalar için arabellekli model bağlama ve daha büyük dosyalar için arabelleğe alınmamış akış kullanarak bir veya daha fazla dosyanın yüklenmesini destekler

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Güvenlik konuları

Kullanıcılara bir sunucuya dosya yükleme yeteneği sağlarken dikkatli olun. Saldırganlar şunları deneyebilir:

* [Hizmet reddi](/windows-hardware/drivers/ifs/denial-of-service) saldırıları yürütün.
* Virüsleri veya kötü amaçlı yazılımları karşıya yükleyin.
* Ağları ve sunucuları diğer yollarla tehlikeye atabilir.

Başarılı bir saldırının olasılığını azaltan güvenlik adımları şunlardır:

* Dosyaları, tercihen sistem dışı bir sürücüye, özel bir dosya yükleme alanına yükleyin. Ayrılmış bir konum, karşıya yüklenen dosyalar üzerinde güvenlik kısıtlamaları yapmayı kolaylaştırır. Dosya yükleme konumunda yürütme izinlerini devre dışı bırakın.&dagger;
* Karşıya yüklenen dosyaları uygulamayla aynı dizin **ağacında kalıcı hale** getirme.&dagger;
* Uygulama tarafından belirlenen bir güvenli dosya adı kullanın. Kullanıcı tarafından belirtilen bir dosya adı veya karşıya yüklenen dosyanın güvenilmeyen dosya adı kullanmayın. &dagger; HTML, görüntüleme sırasında güvenilmeyen dosya adını kodlayamıyor. Örneğin, dosya adını günlüğe kaydetme veya Kullanıcı arabiriminde görüntüleme (Razor OTOMATIK olarak HTML kodlama çıktısı).
* Uygulamanın tasarım belirtimi için yalnızca onaylanan dosya uzantılarına izin verin.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Sunucuda istemci tarafı denetimlerinin gerçekleştirildiğinden emin olun. &dagger; İstemci tarafı denetimleri kolayca atmayı kolaylaştırır.
* Karşıya yüklenen dosyanın boyutunu denetleyin. Büyük karşıya yüklemeleri engellemek için en büyük boyut sınırını ayarlayın.&dagger;
* Aynı ada sahip karşıya yüklenen bir dosya tarafından dosyaların üzerine yazılmaması gerektiğinde, dosyayı karşıya yüklemeden önce dosya adını veritabanına veya fiziksel depolamaya göre denetleyin.
* **Dosya depolanmadan önce karşıya yüklenen içerik üzerinde bir virüs/kötü amaçlı yazılım tarayıcısı çalıştırın.**

&dagger;Örnek uygulama, ölçütlere uyan bir yaklaşımı gösterir.

> [!WARNING]
> Kötü amaçlı kodun bir sisteme yüklenmesi genellikle şu şekilde kod yürütmenin ilk adımıdır:
>
> * Sistemin denetimini tamamen elde edin.
> * Sistemin kilitlenme sonucuyla bir sistemi aşırı yükleme.
> * Kullanıcı veya Sistem verilerinin güvenliğini tehlikeye atabilir.
> * Genel Kullanıcı arabirimine Graffiti uygulayın.
>
> Kullanıcılardan dosya kabul edilirken saldırı yüzeyi alanını azaltma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:
>
> * [Kısıtlanmamış dosya yükleme](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Azure güvenliği: kullanıcılardan dosya kabul edilirken uygun denetimlerin yerinde olduğundan emin olun](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Örnek uygulamadaki örnekler de dahil olmak üzere güvenlik önlemlerini uygulama hakkında daha fazla bilgi için [doğrulama](#validation) bölümüne bakın.

## <a name="storage-scenarios"></a>Depolama senaryoları

Dosyalar için ortak depolama seçenekleri şunlardır:

* Veritabanı

  * Küçük dosya yüklemeleri için, bir veritabanı genellikle fiziksel depolama (dosya sistemi veya ağ paylaşımından) seçeneklerinden daha hızlıdır.
  * Kullanıcı verileri için bir veritabanı kaydının alınması eşzamanlı olarak dosya içeriğini (örneğin, avatar görüntüsü) sağlayabildiğinden, veritabanı genellikle fiziksel depolama seçeneklerine göre daha uygundur.
  * Bir veritabanı, veri depolama hizmeti kullanmaktan daha ucuz olabilir.

* Fiziksel depolama (dosya sistemi veya ağ paylaşma)

  * Büyük dosya yüklemeleri için:
    * Veritabanı limitleri karşıya yükleme boyutunu kısıtlayabilir.
    * Fiziksel depolama genellikle bir veritabanındaki depolamadan daha az ekonomik olur.
  * Fiziksel depolama, veri depolama hizmeti kullanmaktan daha ucuz olabilir.
  * Uygulamanın işlemi, depolama konumu için okuma ve yazma izinlerine sahip olmalıdır. **Hiçbir zaman yürütme izni vermeyin.**

* Veri depolama hizmeti (örneğin, [Azure Blob depolama](https://azure.microsoft.com/services/storage/blobs/))

  * Hizmetler genellikle tek hata noktalarına tabi olan şirket içi çözümler üzerinde geliştirilmiş ölçeklenebilirlik ve esneklik sunar.
  * Hizmetler büyük depolama altyapısı senaryolarında düşük maliyetlidir.

  Daha fazla bilgi için bkz. [hızlı başlangıç: nesne depolamada blob oluşturmak için .NET kullanma](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Karşıya dosya yükleme senaryoları

Dosyaları karşıya yüklemek için iki genel yaklaşım arabelleğe alınır ve akışlardır.

**Ara**

Dosyanın tamamı <xref:Microsoft.AspNetCore.Http.IFormFile>, dosyayı işlemek veya kaydetmek için kullanılan dosyanın C# temsili olan öğesine okunurdur.

Dosya karşıya yüklemeleri tarafından kullanılan kaynaklar (disk, bellek), eşzamanlı dosya karşıya yüklemelerinin sayısına ve boyutuna bağlıdır. Bir uygulama çok fazla karşıya yükleme arabelleğini denerse, bellek veya disk alanı tükenirse site çöker. Karşıya dosya yükleme boyutu veya sıklığı uygulama kaynaklarını tüketme ise, akış ' ı kullanın.

> [!NOTE]
> 64 KB geçen tek bir arabelleğe alınmış dosya, bellekten diskte geçici bir dosyaya taşınır.

Dosya arabelleğe alma, bu konunun aşağıdaki bölümlerinde ele alınmıştır:

* [Fiziksel depolama alanı](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Veritabanınızı](#upload-small-files-with-buffered-model-binding-to-a-database)

**Akış**

Dosya çok parçalı bir istekten alınır ve doğrudan uygulama tarafından işlenir veya kaydedilir. Akış performansı önemli ölçüde iyileştirmez. Akış, dosya karşıya yüklenirken bellek veya disk alanı taleplerini azaltır.

Akış büyük dosyaları [akış ile büyük dosyaları karşıya yükle](#upload-large-files-with-streaming) bölümünde ele alınmıştır.

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Fiziksel depolamaya arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme

Küçük dosyaları karşıya yüklemek için, çok parçalı bir form kullanın veya JavaScript kullanarak bir POST isteği oluşturun.

Aşağıdaki örnek, tek bir dosyayı karşıya yüklemek Razor için sayfalar formunun kullanımını gösterir (örnek uygulamada*Pages/Bufferedsinglefileuploadfiziksel. cshtml* ):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

Aşağıdaki örnek, önceki örneğe benzerdir, ancak şunları hariç:

* Form verilerini göndermek için JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) kullanılır.
* Doğrulama yok.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

[Fetch API 'sini desteklemeyen](https://caniuse.com/#feat=fetch)istemcilerde form gönderisini JavaScript 'te gerçekleştirmek için aşağıdaki yaklaşımlardan birini kullanın:

* Fetch Polyfill kullanın (örneğin, [Window. Fetch (GitHub/fetch)](https://github.com/github/fetch)).
* `XMLHttpRequest` adresini kullanın. Örneğin:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Dosya yüklemelerini desteklemek için, HTML formları bir kodlama türü (`enctype`) belirtmelidir. `multipart/form-data`

Birden çok `files` dosyayı karşıya yüklemeyi destekleyen bir giriş öğesi için, `multiple` `<input>` öğesine özniteliği sağlar:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Sunucuya yüklenen tek dosyalara, kullanılarak <xref:Microsoft.AspNetCore.Http.IFormFile> [model bağlama](xref:mvc/models/model-binding) yoluyla erişilebilir. Örnek uygulama, veritabanı ve fiziksel depolama senaryoları için birden çok arabellekli dosya yüklemeyi gösterir.

<a name="filename"></a>

> [!WARNING]
> Görüntüleme ve günlüğe kaydetme `FileName` için dışındaki <xref:Microsoft.AspNetCore.Http.IFormFile> **özelliğini kullanmayın.** Görüntüleme veya günlüğe kaydetme sırasında, HTML dosya adını kodlayın. Saldırgan, tam yollar veya göreli yollar dahil olmak üzere kötü amaçlı bir dosya adı sağlayabilir. Uygulamalar:
>
> * Kullanıcı tarafından sağlanan dosya adının yolunu kaldırın.
> * Kullanıcı arabirimi veya günlüğe kaydetme için HTML kodlu, yol tarafından kaldırılan dosya adını kaydedin.
> * Depolama için yeni bir rastgele dosya adı oluşturun.
>
> Aşağıdaki kod, dosya adından yolu kaldırır:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Bu nedenle, şu ana kadar dikkate alınması gereken örnekler aşağıda verilmiştir. Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:
>
> * [Güvenlik konuları](#security-considerations)
> * [Doğrulama](#validation)

Model bağlama kullanarak dosyaları karşıya yüklerken <xref:Microsoft.AspNetCore.Http.IFormFile>, eylem yöntemi kabul edebilir:

* Tek <xref:Microsoft.AspNetCore.Http.IFormFile>bir.
* Birkaç dosyayı temsil eden aşağıdaki koleksiyonlardan herhangi biri:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Listele](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Bağlama, form dosyaları adına göre eşleşir. Örneğin, içindeki `name` `<input type="file" name="formFile">` HTML değeri C# parametresi/özelliği ile (`FormFile`) eşleşmelidir. Daha fazla bilgi için, [ad öznitelik DEĞERINI Post yönteminin parametre adına eşleştirin](#match-name-attribute-value-to-parameter-name-of-post-method) bölümüne bakın.

Aşağıdaki örnek:

* Karşıya yüklenen bir veya daha fazla dosya üzerinden döngü.
* Dosya adı da dahil olmak üzere bir dosyanın tam yolunu döndürmek için [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanır. 
* Dosyalar, uygulama tarafından oluşturulan bir dosya adı kullanılarak yerel dosya sistemine kaydedilir.
* Karşıya yüklenen dosyaların toplam sayısını ve boyutunu döndürür.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Yol `Path.GetRandomFileName` olmadan bir dosya adı oluşturmak için kullanın. Aşağıdaki örnekte, yol yapılandırmadan alınır:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Öğesine <xref:System.IO.FileStream> geçirilen *yol, dosya adını içermelidir.* Dosya adı sağlanmazsa, çalışma zamanında bir <xref:System.UnauthorizedAccessException> oluşturulur.

Tekniği kullanılarak yüklenen dosyalar <xref:Microsoft.AspNetCore.Http.IFormFile> , işlemeden önce sunucuda veya diskte bellek halinde arabelleğe alınır. Eylem yönteminde, <xref:Microsoft.AspNetCore.Http.IFormFile> içeriğe bir <xref:System.IO.Stream>olarak erişilebilir. Yerel dosya sistemine ek olarak, dosyalar bir ağ paylaşımında veya [Azure Blob depolama](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)gibi bir dosya depolama hizmetine kaydedilebilir.

Karşıya yüklemek için birden çok dosya üzerinde döngü yapan ve güvenli dosya adları kullanan başka bir örnek için, örnek uygulamadaki *Pages/Bufferedmultiplefileuploadfiziksel. cshtml. cs* dosyasına bakın.

> [!WARNING]
> [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , önceki <xref:System.IO.IOException> geçici dosyaları silmeden 65.535 ' den fazla dosya oluşturulduysa bir oluşturur. 65.535 dosya sınırının sunucu başına sınırı vardır. Windows işletim sistemi için bu sınır hakkında daha fazla bilgi için aşağıdaki konulara bakın:
>
> * [GetTempFileNameA işlevi](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Bir veritabanına arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme

İkili dosya verilerini [Entity Framework](/ef/core/index)kullanarak bir veritabanında depolamak için, varlıkta bir <xref:System.Byte> dizi özelliği tanımlayın:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Sınıf için şunu içeren bir sayfa modeli özelliği belirtin <xref:Microsoft.AspNetCore.Http.IFormFile>:

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile>doğrudan bir eylem yöntemi parametresi veya bir bağlantılı model özelliği olarak kullanılabilir. Önceki örnekte, bir bağlantılı model özelliği kullanılmaktadır.

, `FileUpload` Razor Sayfalar formunda kullanılır:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Form sunucuya gönderildiğinde, öğesini bir akışa kopyalayın <xref:Microsoft.AspNetCore.Http.IFormFile> ve veritabanına bir bayt dizisi olarak kaydedin. Aşağıdaki örnekte, `_dbContext` uygulamanın veritabanı bağlamını depolar:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

Yukarıdaki örnek, örnek uygulamada gösterilen senaryoya benzerdir:

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> İkili verileri ilişkisel veritabanlarında depolarken dikkatli olun, çünkü performansı olumsuz etkileyebilir.
>
> Doğrulaması `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> olmadan özelliğine güvenmeyin veya bu özelliğe güvenmeyin. `FileName` Özelliği yalnızca görüntüleme amacıyla ve yalnızca HTML kodlaması sonrasında kullanılmalıdır.
>
> Belirtilen örneklerde dikkate alınması gereken önemli noktalar. Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:
>
> * [Güvenlik konuları](#security-considerations)
> * [Doğrulama](#validation)

### <a name="upload-large-files-with-streaming"></a>Akışa sahip büyük dosyaları karşıya yükleme

Aşağıdaki örnek, bir denetleyiciyi bir denetleyici eyleminde akışa almak için JavaScript 'in nasıl kullanılacağını gösterir. Dosyanın antiforgery belirteci özel bir filtre özniteliği kullanılarak oluşturulur ve istek gövdesi yerine istemci HTTP üst bilgilerine geçirilir. Eylem yöntemi karşıya yüklenen verileri doğrudan işlediğinden, form modeli bağlama başka bir özel filtre tarafından devre dışı bırakıldı. Eylem içinde formun içerikleri, her bir bireyi `MultipartReader` `MultipartSection`okuyan, dosyayı işleyen veya içeriği uygun şekilde depolayan bir kullanılarak okunur. Çok parçalı bölümler okunduktan sonra eylem kendi model bağlamasını gerçekleştirir.

İlk sayfa yanıtı formu yükler ve bir tanımlama bilgisine ( `GenerateAntiforgeryTokenCookieAttribute` özniteliği aracılığıyla) bir antiforgery belirteci kaydeder. Öznitelik, bir istek belirtecine sahip bir tanımlama bilgisi ayarlamak için ASP.NET Core yerleşik [antiforgery desteğini](xref:security/anti-request-forgery) kullanır:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute` Model bağlamayı devre dışı bırakmak için kullanılır:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

Örnek uygulamada `GenerateAntiforgeryTokenCookieAttribute` ve `DisableFormValueModelBindingAttribute` sayfa [ Razor kuralları](xref:razor-pages/razor-pages-conventions)kullanılarak `/StreamedSingleFileUploadDb` ve `/StreamedSingleFileUploadPhysical` içindeki `Startup.ConfigureServices` sayfa uygulama modellerine filtre olarak uygulanır:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

Model bağlama formu okumadığından formdan bağlanan parametreler bağlanamaz (sorgu, yol ve başlık çalışmaya devam eder). Action yöntemi doğrudan `Request` özelliği ile birlikte çalışabilir. `MultipartReader` Her bölümü okumak için kullanılır. Anahtar/değer verileri bir `KeyValueAccumulator`içinde depolanır. Çok parçalı bölümler okunduktan sonra, öğesinin `KeyValueAccumulator` içeriği form verilerini bir model türüne bağlamak için kullanılır.

EF Core bir `StreamingController.UploadDatabase` veritabanına akış için tamamlanan Yöntem:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Utilities/MultipartRequestHelper. cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Fiziksel bir `StreamingController.UploadPhysical` konuma akışa yönelik tüm Yöntem:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

Örnek uygulamada, doğrulama denetimleri tarafından `FileHelpers.ProcessStreamedFile`işlenir.

## <a name="validation"></a>Doğrulama

Örnek uygulamanın `FileHelpers` sınıfı, arabelleğe alınmış <xref:Microsoft.AspNetCore.Http.IFormFile> ve akış dosya yüklemeleri için birkaç denetim gösterir. Örnek uygulamada <xref:Microsoft.AspNetCore.Http.IFormFile> ara belleğe alınmış dosya yüklemelerini işlemek Için, `ProcessFormFile` *Utilities/fileyardımcılar. cs* dosyasındaki yöntemine bakın. Akış dosyalarını işlemek için aynı dosyadaki `ProcessStreamedFile` yöntemine bakın.

> [!WARNING]
> Örnek uygulamada gösterilen doğrulama işleme yöntemleri karşıya yüklenen dosyaların içeriğini taramaz. Çoğu üretim senaryosunda, dosyanın kullanıcılara veya diğer sistemlere kullanılabilir hale getirilmesi için dosya üzerinde bir virüs/kötü amaçlı yazılım tarayıcı API 'SI kullanılır.
>
> Konu örneği, doğrulama tekniklerine yönelik çalışan bir örnek sağlasa da, aşağıdakileri gerçekleştirmediğiniz takdirde `FileHelpers` sınıfı bir üretim uygulamasında uygulamaz:
>
> * Uygulamayı tam olarak anlayın.
> * Uygulamayı uygulamanın ortamı ve belirtimleri için uygun şekilde değiştirin.
>
> **Bu gereksinimleri bilmeden bir uygulamada güvenlik kodunu hiçbir şekilde sayısının fark gözetmeden uygulayın.**

### <a name="content-validation"></a>İçerik doğrulama

**Karşıya yüklenen içerikte üçüncü taraf bir virüs/kötü amaçlı yazılım tarama API 'SI kullanın.**

Dosyaları tarama, yüksek hacimli senaryolarda sunucu kaynaklarında yoğun bir şekilde yapılır. Dosya tarama nedeniyle istek işleme performansı azaldığında, tarama işini, muhtemelen uygulamanın sunucusundan farklı bir sunucuda çalışan bir [arka plan hizmetine](xref:fundamentals/host/hosted-services)devredere göz önünde bulundurun. Genellikle, arka plan virüs tarayıcısı tarafından denetlene kadar karşıya yüklenen dosyalar karantinaya alınmış bir alanda tutulur. Bir dosya geçtiğinde dosya normal dosya depolama konumuna taşınır. Bu adımlar genellikle bir dosyanın tarama durumunu gösteren bir veritabanı kaydıyla birlikte gerçekleştirilir. Böyle bir yaklaşım kullanarak, uygulama ve uygulama sunucusu isteklere yanıt vermeye odaklanmaya devam eder.

### <a name="file-extension-validation"></a>Dosya Uzantısı doğrulaması

Karşıya yüklenen dosyanın uzantısı izin verilen uzantılar listesine göre denetlenmelidir. Örneğin:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Dosya imzası doğrulaması

Bir dosyanın imzası, bir dosyanın başlangıcında ilk birkaç bayta göre belirlenir. Bu baytlar, uzantının dosyanın içeriğiyle eşleşip eşleşmediğini göstermek için kullanılabilir. Örnek uygulama, birkaç ortak dosya türü için dosya imzalarını denetler. Aşağıdaki örnekte, bir JPEG görüntüsünün dosya imzası, dosyaya karşı denetlenir:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Ek dosya imzaları almak için [Dosya Imzaları veritabanı](https://www.filesignatures.net/) ve resmi dosya belirtimleri bölümüne bakın.

### <a name="file-name-security"></a>Dosya adı güvenliği

Fiziksel depolamaya bir dosyayı kaydetmek için hiçbir şekilde istemci tarafından sağlanan dosya adı kullanmayın. Geçici depolama için tam yol (dosya adı da dahil olmak üzere) oluşturmak için [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) veya [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanarak dosya için güvenli bir dosya adı oluşturun.

RazorOtomatik HTML, görüntüleme için özellik değerlerini kodluyor. Aşağıdaki kodun kullanımı güvenlidir:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Dışında Razor, her zaman <xref:System.Net.WebUtility.HtmlEncode*> bir kullanıcının isteğinden dosya adı içeriği.

Birçok uygulama, dosyanın var olduğunu bir denetim içermelidir; Aksi takdirde, dosyanın üzerine aynı ada sahip bir dosya yazılır. Uygulamanızın belirtimlerini karşılamak için ek mantık sağlayın.

### <a name="size-validation"></a>Boyut doğrulaması

Karşıya yüklenen dosyaların boyutunu sınırlayın.

Örnek uygulamada, dosyanın boyutu 2 MB ile sınırlıdır (bayt cinsinden gösterilir). Sınır, *appSettings. JSON* dosyasındaki [yapılandırma](xref:fundamentals/configuration/index) yoluyla sağlanır:

```json
{
  "FileSizeLimit": 2097152
}
```

, `FileSizeLimit` `PageModel` Sınıflara eklenmiş:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Dosya boyutu sınırı aştığında, dosya reddedilir:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Name öznitelik değerini POST yönteminin Parameter adı ile Eşleştir

Form verileri oluşturanRazor veya JavaScript 'in `FormData` doğrudan kullandığı, form öğesinde belirtilen adın veya `FormData` denetleyicinin eyleminde parametrenin adıyla eşleşmesi gereken form olmayan formlar.

Aşağıdaki örnekte:

* Bir `<input>` öğesi kullanılırken, `name` özniteliği değere `battlePlans`ayarlanır:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* JavaScript içinde `FormData` kullanırken, ad değerine `battlePlans`ayarlanır:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

C# yönteminin (`battlePlans`) parametresi için eşleşen bir ad kullanın:

* Bir Razor sayfalar sayfa işleyici yöntemi için şunu `Upload`adlı:

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* MVC POST denetleyicisi eylem yöntemi için:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Sunucu ve uygulama yapılandırması

### <a name="multipart-body-length-limit"></a>Çok parçalı gövde uzunluğu sınırı

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>Her bir çok parçalı gövdenin uzunluk sınırını ayarlar. Bu sınırı aşan form bölümleri ayrıştırıldığında bir <xref:System.IO.InvalidDataException> oluşturur. Varsayılan değer 134.217.728 ' dir (128 MB). Sınırı, içindeki <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> `Startup.ConfigureServices`ayarı kullanarak özelleştirin:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>tek sayfa veya eylem <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> için ayarlamak için kullanılır.

Bir Razor sayfalar uygulamasında, filtresi içindeki `Startup.ConfigureServices`bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın:

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    });
```

Bir Razor sayfalar uygulamasında veya bir MVC uygulamasında, filtreyi sayfa modeline veya eylem yöntemine uygulayın:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel maksimum istek gövdesi boyutu

Kestrel tarafından barındırılan uygulamalar için, varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu, yaklaşık 28,6 MB 'tır. [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel Server seçeneğini kullanarak sınırı özelleştirin:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>, tek sayfa veya eylem için [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) ayarlamak üzere kullanılır.

Bir Razor sayfalar uygulamasında, filtresi içindeki `Startup.ConfigureServices`bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın:

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    });
```

Bir Razor sayfalar uygulamasında veya bir MVC uygulamasında, filtreyi sayfa işleyici sınıfına veya eylem yöntemine uygulayın:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

, `RequestSizeLimitAttribute` Razor Yönergesi kullanılarak [`@attribute`](xref:mvc/views/razor#attribute) da uygulanabilir:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Diğer Kestrel limitleri

Kestrel tarafından barındırılan uygulamalar için diğer Kestrel limitleri de uygulanabilir:

* [İstemci bağlantıları üst sınırı](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [İstek ve yanıt veri ücretleri](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>IIS içerik uzunluğu sınırı

Varsayılan istek sınırı (`maxAllowedContentLength`), YAKLAŞıK 28.6 mb olan 30.000.000 bayttır. *Web. config* dosyasında sınırı özelleştirin:

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Bu ayar yalnızca IIS için geçerlidir. Kestrel üzerinde barındırırken davranış varsayılan olarak gerçekleşmez. Daha fazla bilgi için bkz. [Istek \<sınırları requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

ASP.NET Core modülündeki sınırlamalar veya IIS Istek filtreleme modülünün varlığı, karşıya yüklemeleri 2 veya 4 GB ile sınırlandırabilir. Daha fazla bilgi için bkz. [2 GB 'tan büyük dosya karşıya yüklenemiyor (DotNet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Sorun giderme

Dosyaları karşıya yükleme ve olası çözümleri ile çalışırken karşılaşılan bazı yaygın sorunlar aşağıda verilmiştir.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Bir IIS sunucusuna dağıtılırken bulunamadı hatası

Aşağıdaki hata karşıya yüklenen dosyanın, sunucunun yapılandırılmış içerik uzunluğunu aştığını gösterir:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Limiti artırma hakkında daha fazla bilgi için bkz. [IIS içerik uzunluğu sınırı](#iis-content-length-limit) bölümü.

### <a name="connection-failure"></a>Bağlantı hatası

Bir bağlantı hatası ve bir sıfırlama sunucu bağlantısı büyük olasılıkla karşıya yüklenen dosyanın Kestrel 'in en büyük istek gövdesi boyutunu aştığını gösterir. Daha fazla bilgi için, [Kestrel maksimum istek gövdesi boyutu](#kestrel-maximum-request-body-size) bölümüne bakın. Kestrel istemci bağlantı limitleri de ayarlama gerektirebilir.

### <a name="null-reference-exception-with-iformfile"></a>Iformfile ile null başvuru özel durumu

<xref:Microsoft.AspNetCore.Http.IFormFile> Denetleyici karşıya yüklenen dosyaları kullanarak kabul edilirse, ancak değer Ise `null`, HTML formunun bir `enctype` değerini belirtmesini onaylayın. `multipart/form-data` Bu öznitelik `<form>` öğesinde ayarlanmamışsa, dosya karşıya yükleme gerçekleşmez ve herhangi bir bağlı <xref:Microsoft.AspNetCore.Http.IFormFile> bağımsız değişken. `null` Ayrıca, [form verilerinde karşıya yükleme adlandırmasının uygulamanın adlandırmayla eşleştiğinden](#match-name-attribute-value-to-parameter-name-of-post-method)emin olun.

### <a name="stream-was-too-long"></a>Akış çok uzun

Bu konudaki örnekler karşıya yüklenen dosyanın içeriğini <xref:System.IO.MemoryStream> tutmak için kullanır. Bir `MemoryStream` öğesinin boyut sınırı `int.MaxValue`. Uygulamanın dosya yükleme senaryosu, dosya içeriğinin 50 MB 'tan büyük olmasını gerektiriyorsa, karşıya yüklenen bir dosyanın içeriğini tutmak için tek başına `MemoryStream` olmayan alternatif bir yaklaşım kullanın.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core, daha küçük dosyalar için arabellekli model bağlama ve daha büyük dosyalar için arabelleğe alınmamış akış kullanarak bir veya daha fazla dosyanın yüklenmesini destekler

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Güvenlik konuları

Kullanıcılara bir sunucuya dosya yükleme yeteneği sağlarken dikkatli olun. Saldırganlar şunları deneyebilir:

* [Hizmet reddi](/windows-hardware/drivers/ifs/denial-of-service) saldırıları yürütün.
* Virüsleri veya kötü amaçlı yazılımları karşıya yükleyin.
* Ağları ve sunucuları diğer yollarla tehlikeye atabilir.

Başarılı bir saldırının olasılığını azaltan güvenlik adımları şunlardır:

* Dosyaları, tercihen sistem dışı bir sürücüye, özel bir dosya yükleme alanına yükleyin. Ayrılmış bir konum, karşıya yüklenen dosyalar üzerinde güvenlik kısıtlamaları yapmayı kolaylaştırır. Dosya yükleme konumunda yürütme izinlerini devre dışı bırakın.&dagger;
* Karşıya yüklenen dosyaları uygulamayla aynı dizin **ağacında kalıcı hale** getirme.&dagger;
* Uygulama tarafından belirlenen bir güvenli dosya adı kullanın. Kullanıcı tarafından belirtilen bir dosya adı veya karşıya yüklenen dosyanın güvenilmeyen dosya adı kullanmayın. &dagger; HTML, görüntüleme sırasında güvenilmeyen dosya adını kodlayamıyor. Örneğin, dosya adını günlüğe kaydetme veya Kullanıcı arabiriminde görüntüleme (Razor OTOMATIK olarak HTML kodlama çıktısı).
* Uygulamanın tasarım belirtimi için yalnızca onaylanan dosya uzantılarına izin verin.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Sunucuda istemci tarafı denetimlerinin gerçekleştirildiğinden emin olun. &dagger; İstemci tarafı denetimleri kolayca atmayı kolaylaştırır.
* Karşıya yüklenen dosyanın boyutunu denetleyin. Büyük karşıya yüklemeleri engellemek için en büyük boyut sınırını ayarlayın.&dagger;
* Aynı ada sahip karşıya yüklenen bir dosya tarafından dosyaların üzerine yazılmaması gerektiğinde, dosyayı karşıya yüklemeden önce dosya adını veritabanına veya fiziksel depolamaya göre denetleyin.
* **Dosya depolanmadan önce karşıya yüklenen içerik üzerinde bir virüs/kötü amaçlı yazılım tarayıcısı çalıştırın.**

&dagger;Örnek uygulama, ölçütlere uyan bir yaklaşımı gösterir.

> [!WARNING]
> Kötü amaçlı kodun bir sisteme yüklenmesi genellikle şu şekilde kod yürütmenin ilk adımıdır:
>
> * Sistemin denetimini tamamen elde edin.
> * Sistemin kilitlenme sonucuyla bir sistemi aşırı yükleme.
> * Kullanıcı veya Sistem verilerinin güvenliğini tehlikeye atabilir.
> * Genel Kullanıcı arabirimine Graffiti uygulayın.
>
> Kullanıcılardan dosya kabul edilirken saldırı yüzeyi alanını azaltma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:
>
> * [Kısıtlanmamış dosya yükleme](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Azure güvenliği: kullanıcılardan dosya kabul edilirken uygun denetimlerin yerinde olduğundan emin olun](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Örnek uygulamadaki örnekler de dahil olmak üzere güvenlik önlemlerini uygulama hakkında daha fazla bilgi için [doğrulama](#validation) bölümüne bakın.

## <a name="storage-scenarios"></a>Depolama senaryoları

Dosyalar için ortak depolama seçenekleri şunlardır:

* Veritabanı

  * Küçük dosya yüklemeleri için, bir veritabanı genellikle fiziksel depolama (dosya sistemi veya ağ paylaşımından) seçeneklerinden daha hızlıdır.
  * Kullanıcı verileri için bir veritabanı kaydının alınması eşzamanlı olarak dosya içeriğini (örneğin, avatar görüntüsü) sağlayabildiğinden, veritabanı genellikle fiziksel depolama seçeneklerine göre daha uygundur.
  * Bir veritabanı, veri depolama hizmeti kullanmaktan daha ucuz olabilir.

* Fiziksel depolama (dosya sistemi veya ağ paylaşma)

  * Büyük dosya yüklemeleri için:
    * Veritabanı limitleri karşıya yükleme boyutunu kısıtlayabilir.
    * Fiziksel depolama genellikle bir veritabanındaki depolamadan daha az ekonomik olur.
  * Fiziksel depolama, veri depolama hizmeti kullanmaktan daha ucuz olabilir.
  * Uygulamanın işlemi, depolama konumu için okuma ve yazma izinlerine sahip olmalıdır. **Hiçbir zaman yürütme izni vermeyin.**

* Veri depolama hizmeti (örneğin, [Azure Blob depolama](https://azure.microsoft.com/services/storage/blobs/))

  * Hizmetler genellikle tek hata noktalarına tabi olan şirket içi çözümler üzerinde geliştirilmiş ölçeklenebilirlik ve esneklik sunar.
  * Hizmetler büyük depolama altyapısı senaryolarında düşük maliyetlidir.

  Daha fazla bilgi için bkz. [hızlı başlangıç: nesne depolamada blob oluşturmak için .NET kullanma](/azure/storage/blobs/storage-quickstart-blobs-dotnet). Konu başlığı altında <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> ile çalışırken bir <xref:System.IO.FileStream> BLOB depolama alanına kaydetmek için kullanılabilir. <xref:System.IO.Stream>

## <a name="file-upload-scenarios"></a>Karşıya dosya yükleme senaryoları

Dosyaları karşıya yüklemek için iki genel yaklaşım arabelleğe alınır ve akışlardır.

**Ara**

Dosyanın tamamı <xref:Microsoft.AspNetCore.Http.IFormFile>, dosyayı işlemek veya kaydetmek için kullanılan dosyanın C# temsili olan öğesine okunurdur.

Dosya karşıya yüklemeleri tarafından kullanılan kaynaklar (disk, bellek), eşzamanlı dosya karşıya yüklemelerinin sayısına ve boyutuna bağlıdır. Bir uygulama çok fazla karşıya yükleme arabelleğini denerse, bellek veya disk alanı tükenirse site çöker. Karşıya dosya yükleme boyutu veya sıklığı uygulama kaynaklarını tüketme ise, akış ' ı kullanın.

> [!NOTE]
> 64 KB geçen tek bir arabelleğe alınmış dosya, bellekten diskte geçici bir dosyaya taşınır.

Dosya arabelleğe alma, bu konunun aşağıdaki bölümlerinde ele alınmıştır:

* [Fiziksel depolama alanı](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Veritabanınızı](#upload-small-files-with-buffered-model-binding-to-a-database)

**Akış**

Dosya çok parçalı bir istekten alınır ve doğrudan uygulama tarafından işlenir veya kaydedilir. Akış performansı önemli ölçüde iyileştirmez. Akış, dosya karşıya yüklenirken bellek veya disk alanı taleplerini azaltır.

Akış büyük dosyaları [akış ile büyük dosyaları karşıya yükle](#upload-large-files-with-streaming) bölümünde ele alınmıştır.

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Fiziksel depolamaya arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme

Küçük dosyaları karşıya yüklemek için, çok parçalı bir form kullanın veya JavaScript kullanarak bir POST isteği oluşturun.

Aşağıdaki örnek, tek bir dosyayı karşıya yüklemek Razor için sayfalar formunun kullanımını gösterir (örnek uygulamada*Pages/Bufferedsinglefileuploadfiziksel. cshtml* ):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

Aşağıdaki örnek, önceki örneğe benzerdir, ancak şunları hariç:

* Form verilerini göndermek için JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) kullanılır.
* Doğrulama yok.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

[Fetch API 'sini desteklemeyen](https://caniuse.com/#feat=fetch)istemcilerde form gönderisini JavaScript 'te gerçekleştirmek için aşağıdaki yaklaşımlardan birini kullanın:

* Fetch Polyfill kullanın (örneğin, [Window. Fetch (GitHub/fetch)](https://github.com/github/fetch)).
* `XMLHttpRequest` adresini kullanın. Örneğin:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Dosya yüklemelerini desteklemek için, HTML formları bir kodlama türü (`enctype`) belirtmelidir. `multipart/form-data`

Birden çok `files` dosyayı karşıya yüklemeyi destekleyen bir giriş öğesi için, `multiple` `<input>` öğesine özniteliği sağlar:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Sunucuya yüklenen tek dosyalara, kullanılarak <xref:Microsoft.AspNetCore.Http.IFormFile> [model bağlama](xref:mvc/models/model-binding) yoluyla erişilebilir. Örnek uygulama, veritabanı ve fiziksel depolama senaryoları için birden çok arabellekli dosya yüklemeyi gösterir.

<a name="filename2"></a>

> [!WARNING]
> Görüntüleme ve günlüğe kaydetme `FileName` için dışındaki <xref:Microsoft.AspNetCore.Http.IFormFile> **özelliğini kullanmayın.** Görüntüleme veya günlüğe kaydetme sırasında, HTML dosya adını kodlayın. Saldırgan, tam yollar veya göreli yollar dahil olmak üzere kötü amaçlı bir dosya adı sağlayabilir. Uygulamalar:
>
> * Kullanıcı tarafından sağlanan dosya adının yolunu kaldırın.
> * Kullanıcı arabirimi veya günlüğe kaydetme için HTML kodlu, yol tarafından kaldırılan dosya adını kaydedin.
> * Depolama için yeni bir rastgele dosya adı oluşturun.
>
> Aşağıdaki kod, dosya adından yolu kaldırır:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Bu nedenle, şu ana kadar dikkate alınması gereken örnekler aşağıda verilmiştir. Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:
>
> * [Güvenlik konuları](#security-considerations)
> * [Doğrulama](#validation)

Model bağlama kullanarak dosyaları karşıya yüklerken <xref:Microsoft.AspNetCore.Http.IFormFile>, eylem yöntemi kabul edebilir:

* Tek <xref:Microsoft.AspNetCore.Http.IFormFile>bir.
* Birkaç dosyayı temsil eden aşağıdaki koleksiyonlardan herhangi biri:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Listele](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Bağlama, form dosyaları adına göre eşleşir. Örneğin, içindeki `name` `<input type="file" name="formFile">` HTML değeri C# parametresi/özelliği ile (`FormFile`) eşleşmelidir. Daha fazla bilgi için, [ad öznitelik DEĞERINI Post yönteminin parametre adına eşleştirin](#match-name-attribute-value-to-parameter-name-of-post-method) bölümüne bakın.

Aşağıdaki örnek:

* Karşıya yüklenen bir veya daha fazla dosya üzerinden döngü.
* Dosya adı da dahil olmak üzere bir dosyanın tam yolunu döndürmek için [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanır. 
* Dosyalar, uygulama tarafından oluşturulan bir dosya adı kullanılarak yerel dosya sistemine kaydedilir.
* Karşıya yüklenen dosyaların toplam sayısını ve boyutunu döndürür.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Yol `Path.GetRandomFileName` olmadan bir dosya adı oluşturmak için kullanın. Aşağıdaki örnekte, yol yapılandırmadan alınır:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Öğesine <xref:System.IO.FileStream> geçirilen *yol, dosya adını içermelidir.* Dosya adı sağlanmazsa, çalışma zamanında bir <xref:System.UnauthorizedAccessException> oluşturulur.

Tekniği kullanılarak yüklenen dosyalar <xref:Microsoft.AspNetCore.Http.IFormFile> , işlemeden önce sunucuda veya diskte bellek halinde arabelleğe alınır. Eylem yönteminde, <xref:Microsoft.AspNetCore.Http.IFormFile> içeriğe bir <xref:System.IO.Stream>olarak erişilebilir. Yerel dosya sistemine ek olarak, dosyalar bir ağ paylaşımında veya [Azure Blob depolama](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)gibi bir dosya depolama hizmetine kaydedilebilir.

Karşıya yüklemek için birden çok dosya üzerinde döngü yapan ve güvenli dosya adları kullanan başka bir örnek için, örnek uygulamadaki *Pages/Bufferedmultiplefileuploadfiziksel. cshtml. cs* dosyasına bakın.

> [!WARNING]
> [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , önceki <xref:System.IO.IOException> geçici dosyaları silmeden 65.535 ' den fazla dosya oluşturulduysa bir oluşturur. 65.535 dosya sınırının sunucu başına sınırı vardır. Windows işletim sistemi için bu sınır hakkında daha fazla bilgi için aşağıdaki konulara bakın:
>
> * [GetTempFileNameA işlevi](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Bir veritabanına arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme

İkili dosya verilerini [Entity Framework](/ef/core/index)kullanarak bir veritabanında depolamak için, varlıkta bir <xref:System.Byte> dizi özelliği tanımlayın:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Sınıf için şunu içeren bir sayfa modeli özelliği belirtin <xref:Microsoft.AspNetCore.Http.IFormFile>:

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile>doğrudan bir eylem yöntemi parametresi veya bir bağlantılı model özelliği olarak kullanılabilir. Önceki örnekte, bir bağlantılı model özelliği kullanılmaktadır.

, `FileUpload` Razor Sayfalar formunda kullanılır:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Form sunucuya gönderildiğinde, öğesini bir akışa kopyalayın <xref:Microsoft.AspNetCore.Http.IFormFile> ve veritabanına bir bayt dizisi olarak kaydedin. Aşağıdaki örnekte, `_dbContext` uygulamanın veritabanı bağlamını depolar:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

Yukarıdaki örnek, örnek uygulamada gösterilen senaryoya benzerdir:

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> İkili verileri ilişkisel veritabanlarında depolarken dikkatli olun, çünkü performansı olumsuz etkileyebilir.
>
> Doğrulaması `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> olmadan özelliğine güvenmeyin veya bu özelliğe güvenmeyin. `FileName` Özelliği yalnızca görüntüleme amacıyla ve yalnızca HTML kodlaması sonrasında kullanılmalıdır.
>
> Belirtilen örneklerde dikkate alınması gereken önemli noktalar. Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:
>
> * [Güvenlik konuları](#security-considerations)
> * [Doğrulama](#validation)

### <a name="upload-large-files-with-streaming"></a>Akışa sahip büyük dosyaları karşıya yükleme

Aşağıdaki örnek, bir denetleyiciyi bir denetleyici eyleminde akışa almak için JavaScript 'in nasıl kullanılacağını gösterir. Dosyanın antiforgery belirteci özel bir filtre özniteliği kullanılarak oluşturulur ve istek gövdesi yerine istemci HTTP üst bilgilerine geçirilir. Eylem yöntemi karşıya yüklenen verileri doğrudan işlediğinden, form modeli bağlama başka bir özel filtre tarafından devre dışı bırakıldı. Eylem içinde formun içerikleri, her bir bireyi `MultipartReader` `MultipartSection`okuyan, dosyayı işleyen veya içeriği uygun şekilde depolayan bir kullanılarak okunur. Çok parçalı bölümler okunduktan sonra eylem kendi model bağlamasını gerçekleştirir.

İlk sayfa yanıtı formu yükler ve bir tanımlama bilgisine ( `GenerateAntiforgeryTokenCookieAttribute` özniteliği aracılığıyla) bir antiforgery belirteci kaydeder. Öznitelik, bir istek belirtecine sahip bir tanımlama bilgisi ayarlamak için ASP.NET Core yerleşik [antiforgery desteğini](xref:security/anti-request-forgery) kullanır:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute` Model bağlamayı devre dışı bırakmak için kullanılır:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

Örnek uygulamada `GenerateAntiforgeryTokenCookieAttribute` ve `DisableFormValueModelBindingAttribute` sayfa [ Razor kuralları](xref:razor-pages/razor-pages-conventions)kullanılarak `/StreamedSingleFileUploadDb` ve `/StreamedSingleFileUploadPhysical` içindeki `Startup.ConfigureServices` sayfa uygulama modellerine filtre olarak uygulanır:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Model bağlama formu okumadığından formdan bağlanan parametreler bağlanamaz (sorgu, yol ve başlık çalışmaya devam eder). Action yöntemi doğrudan `Request` özelliği ile birlikte çalışabilir. `MultipartReader` Her bölümü okumak için kullanılır. Anahtar/değer verileri bir `KeyValueAccumulator`içinde depolanır. Çok parçalı bölümler okunduktan sonra, öğesinin `KeyValueAccumulator` içeriği form verilerini bir model türüne bağlamak için kullanılır.

EF Core bir `StreamingController.UploadDatabase` veritabanına akış için tamamlanan Yöntem:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Utilities/MultipartRequestHelper. cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Fiziksel bir `StreamingController.UploadPhysical` konuma akışa yönelik tüm Yöntem:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

Örnek uygulamada, doğrulama denetimleri tarafından `FileHelpers.ProcessStreamedFile`işlenir.

## <a name="validation"></a>Doğrulama

Örnek uygulamanın `FileHelpers` sınıfı, arabelleğe alınmış <xref:Microsoft.AspNetCore.Http.IFormFile> ve akış dosya yüklemeleri için birkaç denetim gösterir. Örnek uygulamada <xref:Microsoft.AspNetCore.Http.IFormFile> ara belleğe alınmış dosya yüklemelerini işlemek Için, `ProcessFormFile` *Utilities/fileyardımcılar. cs* dosyasındaki yöntemine bakın. Akış dosyalarını işlemek için aynı dosyadaki `ProcessStreamedFile` yöntemine bakın.

> [!WARNING]
> Örnek uygulamada gösterilen doğrulama işleme yöntemleri karşıya yüklenen dosyaların içeriğini taramaz. Çoğu üretim senaryosunda, dosyanın kullanıcılara veya diğer sistemlere kullanılabilir hale getirilmesi için dosya üzerinde bir virüs/kötü amaçlı yazılım tarayıcı API 'SI kullanılır.
>
> Konu örneği, doğrulama tekniklerine yönelik çalışan bir örnek sağlasa da, aşağıdakileri gerçekleştirmediğiniz takdirde `FileHelpers` sınıfı bir üretim uygulamasında uygulamaz:
>
> * Uygulamayı tam olarak anlayın.
> * Uygulamayı uygulamanın ortamı ve belirtimleri için uygun şekilde değiştirin.
>
> **Bu gereksinimleri bilmeden bir uygulamada güvenlik kodunu hiçbir şekilde sayısının fark gözetmeden uygulayın.**

### <a name="content-validation"></a>İçerik doğrulama

**Karşıya yüklenen içerikte üçüncü taraf bir virüs/kötü amaçlı yazılım tarama API 'SI kullanın.**

Dosyaları tarama, yüksek hacimli senaryolarda sunucu kaynaklarında yoğun bir şekilde yapılır. Dosya tarama nedeniyle istek işleme performansı azaldığında, tarama işini, muhtemelen uygulamanın sunucusundan farklı bir sunucuda çalışan bir [arka plan hizmetine](xref:fundamentals/host/hosted-services)devredere göz önünde bulundurun. Genellikle, arka plan virüs tarayıcısı tarafından denetlene kadar karşıya yüklenen dosyalar karantinaya alınmış bir alanda tutulur. Bir dosya geçtiğinde dosya normal dosya depolama konumuna taşınır. Bu adımlar genellikle bir dosyanın tarama durumunu gösteren bir veritabanı kaydıyla birlikte gerçekleştirilir. Böyle bir yaklaşım kullanarak, uygulama ve uygulama sunucusu isteklere yanıt vermeye odaklanmaya devam eder.

### <a name="file-extension-validation"></a>Dosya Uzantısı doğrulaması

Karşıya yüklenen dosyanın uzantısı izin verilen uzantılar listesine göre denetlenmelidir. Örneğin:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Dosya imzası doğrulaması

Bir dosyanın imzası, bir dosyanın başlangıcında ilk birkaç bayta göre belirlenir. Bu baytlar, uzantının dosyanın içeriğiyle eşleşip eşleşmediğini göstermek için kullanılabilir. Örnek uygulama, birkaç ortak dosya türü için dosya imzalarını denetler. Aşağıdaki örnekte, bir JPEG görüntüsünün dosya imzası, dosyaya karşı denetlenir:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Ek dosya imzaları almak için [Dosya Imzaları veritabanı](https://www.filesignatures.net/) ve resmi dosya belirtimleri bölümüne bakın.

### <a name="file-name-security"></a>Dosya adı güvenliği

Fiziksel depolamaya bir dosyayı kaydetmek için hiçbir şekilde istemci tarafından sağlanan dosya adı kullanmayın. Geçici depolama için tam yol (dosya adı da dahil olmak üzere) oluşturmak için [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) veya [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanarak dosya için güvenli bir dosya adı oluşturun.

RazorOtomatik HTML, görüntüleme için özellik değerlerini kodluyor. Aşağıdaki kodun kullanımı güvenlidir:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Dışında Razor, her zaman <xref:System.Net.WebUtility.HtmlEncode*> bir kullanıcının isteğinden dosya adı içeriği.

Birçok uygulama, dosyanın var olduğunu bir denetim içermelidir; Aksi takdirde, dosyanın üzerine aynı ada sahip bir dosya yazılır. Uygulamanızın belirtimlerini karşılamak için ek mantık sağlayın.

### <a name="size-validation"></a>Boyut doğrulaması

Karşıya yüklenen dosyaların boyutunu sınırlayın.

Örnek uygulamada, dosyanın boyutu 2 MB ile sınırlıdır (bayt cinsinden gösterilir). Sınır, *appSettings. JSON* dosyasındaki [yapılandırma](xref:fundamentals/configuration/index) yoluyla sağlanır:

```json
{
  "FileSizeLimit": 2097152
}
```

, `FileSizeLimit` `PageModel` Sınıflara eklenmiş:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Dosya boyutu sınırı aştığında, dosya reddedilir:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Name öznitelik değerini POST yönteminin Parameter adı ile Eşleştir

Form verileri oluşturanRazor veya JavaScript 'in `FormData` doğrudan kullandığı, form öğesinde belirtilen adın veya `FormData` denetleyicinin eyleminde parametrenin adıyla eşleşmesi gereken form olmayan formlar.

Aşağıdaki örnekte:

* Bir `<input>` öğesi kullanılırken, `name` özniteliği değere `battlePlans`ayarlanır:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* JavaScript içinde `FormData` kullanırken, ad değerine `battlePlans`ayarlanır:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

C# yönteminin (`battlePlans`) parametresi için eşleşen bir ad kullanın:

* Bir Razor sayfalar sayfa işleyici yöntemi için şunu `Upload`adlı:

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* MVC POST denetleyicisi eylem yöntemi için:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Sunucu ve uygulama yapılandırması

### <a name="multipart-body-length-limit"></a>Çok parçalı gövde uzunluğu sınırı

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>Her bir çok parçalı gövdenin uzunluk sınırını ayarlar. Bu sınırı aşan form bölümleri ayrıştırıldığında bir <xref:System.IO.InvalidDataException> oluşturur. Varsayılan değer 134.217.728 ' dir (128 MB). Sınırı, içindeki <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> `Startup.ConfigureServices`ayarı kullanarak özelleştirin:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>tek sayfa veya eylem <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> için ayarlamak için kullanılır.

Bir Razor sayfalar uygulamasında, filtresi içindeki `Startup.ConfigureServices`bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Bir Razor sayfalar uygulamasında veya bir MVC uygulamasında, filtreyi sayfa modeline veya eylem yöntemine uygulayın:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel maksimum istek gövdesi boyutu

Kestrel tarafından barındırılan uygulamalar için, varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu, yaklaşık 28,6 MB 'tır. [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel Server seçeneğini kullanarak sınırı özelleştirin:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>, tek sayfa veya eylem için [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) ayarlamak üzere kullanılır.

Bir Razor sayfalar uygulamasında, filtresi içindeki `Startup.ConfigureServices`bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Bir Razor sayfalar uygulamasında veya bir MVC uygulamasında, filtreyi sayfa işleyici sınıfına veya eylem yöntemine uygulayın:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Diğer Kestrel limitleri

Kestrel tarafından barındırılan uygulamalar için diğer Kestrel limitleri de uygulanabilir:

* [İstemci bağlantıları üst sınırı](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [İstek ve yanıt veri ücretleri](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>IIS içerik uzunluğu sınırı

Varsayılan istek sınırı (`maxAllowedContentLength`), YAKLAŞıK 28.6 mb olan 30.000.000 bayttır. *Web. config* dosyasında sınırı özelleştirin:

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Bu ayar yalnızca IIS için geçerlidir. Kestrel üzerinde barındırırken davranış varsayılan olarak gerçekleşmez. Daha fazla bilgi için bkz. [Istek \<sınırları requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

ASP.NET Core modülündeki sınırlamalar veya IIS Istek filtreleme modülünün varlığı, karşıya yüklemeleri 2 veya 4 GB ile sınırlandırabilir. Daha fazla bilgi için bkz. [2 GB 'tan büyük dosya karşıya yüklenemiyor (DotNet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Sorun giderme

Dosyaları karşıya yükleme ve olası çözümleri ile çalışırken karşılaşılan bazı yaygın sorunlar aşağıda verilmiştir.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Bir IIS sunucusuna dağıtılırken bulunamadı hatası

Aşağıdaki hata karşıya yüklenen dosyanın, sunucunun yapılandırılmış içerik uzunluğunu aştığını gösterir:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Limiti artırma hakkında daha fazla bilgi için bkz. [IIS içerik uzunluğu sınırı](#iis-content-length-limit) bölümü.

### <a name="connection-failure"></a>Bağlantı hatası

Bir bağlantı hatası ve bir sıfırlama sunucu bağlantısı büyük olasılıkla karşıya yüklenen dosyanın Kestrel 'in en büyük istek gövdesi boyutunu aştığını gösterir. Daha fazla bilgi için, [Kestrel maksimum istek gövdesi boyutu](#kestrel-maximum-request-body-size) bölümüne bakın. Kestrel istemci bağlantı limitleri de ayarlama gerektirebilir.

### <a name="null-reference-exception-with-iformfile"></a>Iformfile ile null başvuru özel durumu

<xref:Microsoft.AspNetCore.Http.IFormFile> Denetleyici karşıya yüklenen dosyaları kullanarak kabul edilirse, ancak değer Ise `null`, HTML formunun bir `enctype` değerini belirtmesini onaylayın. `multipart/form-data` Bu öznitelik `<form>` öğesinde ayarlanmamışsa, dosya karşıya yükleme gerçekleşmez ve herhangi bir bağlı <xref:Microsoft.AspNetCore.Http.IFormFile> bağımsız değişken. `null` Ayrıca, [form verilerinde karşıya yükleme adlandırmasının uygulamanın adlandırmayla eşleştiğinden](#match-name-attribute-value-to-parameter-name-of-post-method)emin olun.

### <a name="stream-was-too-long"></a>Akış çok uzun

Bu konudaki örnekler karşıya yüklenen dosyanın içeriğini <xref:System.IO.MemoryStream> tutmak için kullanır. Bir `MemoryStream` öğesinin boyut sınırı `int.MaxValue`. Uygulamanın dosya yükleme senaryosu, dosya içeriğinin 50 MB 'tan büyük olmasını gerektiriyorsa, karşıya yüklenen bir dosyanın içeriğini tutmak için tek başına `MemoryStream` olmayan alternatif bir yaklaşım kullanın.

::: moniker-end


## <a name="additional-resources"></a>Ek kaynaklar

* [HTTP bağlantı isteği boşaltma](xref:fundamentals/servers/kestrel#http11-request-draining)
* [Kısıtlanmamış dosya yükleme](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [Azure güvenliği: güvenlik çerçevesi: giriş doğrulaması | Karşı](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Azure bulut tasarım desenleri: Valet anahtar düzeni](/azure/architecture/patterns/valet-key)
