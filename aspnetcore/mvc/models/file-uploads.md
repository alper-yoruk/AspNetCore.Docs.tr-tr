---
title: ASP.NET Core'a dosya yükleme
author: rick-anderson
description: ASP.NET Core MVC'de dosya yüklemek için model bağlama ve akış nasıl kullanılır?
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2020
uid: mvc/models/file-uploads
ms.openlocfilehash: e25da0b3867181a16a4636768f36c148a152dd23
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661733"
---
# <a name="upload-files-in-aspnet-core"></a>ASP.NET Core'a dosya yükleme

Yazar: [Steve Smith](https://ardalis.com/) ve [Rutger Storm](https://github.com/rutix)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core, daha küçük dosyalar için arabelleğe alınan modeli ve daha büyük dosyalar için arabelleğe alamayan akışı kullanarak bir veya daha fazla dosya yüklemeyi destekler.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Güvenlik konuları

Kullanıcılara bir sunucuya dosya yükleme olanağı sağlarken dikkatli olun. Saldırganlar şunları deneyebilir:

* Hizmet reddi saldırılarını [gerçekleştirin.](/windows-hardware/drivers/ifs/denial-of-service)
* Virüs veya kötü amaçlı yazılım yükleyin.
* Ağları ve sunucuları başka şekillerde tehlikeye at.

Başarılı bir saldırı olasılığını azaltan güvenlik adımları şunlardır:

* Dosyaları özel bir dosya yükleme alanına, tercihen sistem dışı bir sürücüye yükleyin. Özel bir konum, yüklenen dosyalara güvenlik kısıtlamaları uygulamamayı kolaylaştırır. Dosya yükleme konumundaki yürütme izinlerini devre dışı kaltın.&dagger;
* Yüklenen dosyaları uygulamayla aynı dizin ağacında kalıcı olarak **yapmayın.**&dagger;
* Uygulama tarafından belirlenen güvenli bir dosya adı kullanın. Kullanıcı tarafından sağlanan bir dosya adını veya yüklenen dosyanın güvenilmeyen dosya adını kullanmayın. &dagger; HTML, görüntülenirken güvenilmeyen dosya adını kodlar. Örneğin, dosya adını günlüğe kaydetme veya Kullanıcı Arabirimi'nde görüntülenmesi (Razor otomatik olarak HTML çıktıyı kodlar).
* Uygulamanın tasarım belirtimi için yalnızca onaylı dosya uzantılarına izin verin.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* İstemci tarafı denetimlerinin sunucuda gerçekleştirildiğini doğrulayın. &dagger; İstemci tarafı denetimleri atlatmak kolaydır.
* Yüklenen bir dosyanın boyutunu denetleyin. Büyük yüklemeleri önlemek için maksimum boyut sınırı ayarlayın.&dagger;
* Dosyaların aynı ada sahip yüklenmiş bir dosya tarafından üzerine yazılmaması gerektiğinde, dosyayı yüklemeden önce dosya adını veritabanıyla veya fiziksel depolama alanına karşı denetleyin.
* **Dosya depolandırılmadan önce yüklenen içeriğe virüs/kötü amaçlı yazılım tarayıcısı çalıştırın.**

&dagger;Örnek uygulama, ölçütleri karşılayan bir yaklaşım gösterir.

> [!WARNING]
> Kötü amaçlı kodu bir sisteme yüklemek, sık sık aşağıdakileri yapan kodu yürütmenin ilk adımıdır:
>
> * Bir sistemin kontrolünü tamamen ele geçirmek.
> * Sistemin çöktüğı sonucu bir sistem aşırı yükleyin.
> * Kullanıcı veya sistem verilerini tehlikeye at.
> * Genel bir uI grafiti uygulayın.
>
> Kullanıcılardan dosya kabul ederken saldırı yüzey alanını azaltma hakkında bilgi için aşağıdaki kaynaklara bakın:
>
> * [Sınırsız Dosya Yükleme](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Azure Güvenliği: Kullanıcılardan dosya kabul ederken uygun denetimlerin yerinde olduğundan emin olun](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Örnek uygulamadan örnekler de dahil olmak üzere güvenlik önlemlerinin uygulanması hakkında daha fazla bilgi için [Doğrulama](#validation) bölümüne bakın.

## <a name="storage-scenarios"></a>Depolama senaryoları

Dosyalar için ortak depolama seçenekleri şunlardır:

* Veritabanı

  * Küçük dosya yüklemeleri için veritabanı genellikle fiziksel depolama (dosya sistemi veya ağ paylaşımı) seçeneklerinden daha hızlıdır.
  * Kullanıcı verileri için bir veritabanı kaydının alınması dosya içeriğini (örneğin, bir avatar görüntüsü) aynı anda sağlayabildiği için veritabanı genellikle fiziksel depolama seçeneklerinden daha kullanışlıdır.
  * Veritabanı, veri depolama hizmeti kullanmaktan daha ucuz olabilir.

* Fiziksel depolama (dosya sistemi veya ağ paylaşımı)

  * Büyük dosya yüklemeleri için:
    * Veritabanı sınırları yüklemeboyutunu kısıtlayabilir.
    * Fiziksel depolama genellikle veritabanındaki depolamadan daha az ekonomiktir.
  * Fiziksel depolama, veri depolama hizmeti kullanmaktan daha ucuz olabilir.
  * Uygulamanın işlemi, depolama konumuna izinleri okuyup yazmış olmalıdır. **Yürütme izni asla vermez.**

* Veri depolama hizmeti (örneğin, [Azure Blob Depolama)](https://azure.microsoft.com/services/storage/blobs/)

  * Hizmetler genellikle tek hata noktalarına maruz kalan şirket içi çözümler üzerinde gelişmiş ölçeklenebilirlik ve esneklik sunar.
  * Hizmetler, büyük depolama altyapısı senaryolarında potansiyel olarak daha düşük maliyetlidir.

  Daha fazla bilgi için [Quickstart: Object depolamaalanında bir leke oluşturmak için .NET'i kullanın.](/azure/storage/blobs/storage-quickstart-blobs-dotnet)

## <a name="file-upload-scenarios"></a>Dosya yükleme senaryoları

Dosya yüklemek için iki genel yaklaşım arabelleğe alma ve akış vardır.

**Tamponlama**

Dosyanın tamamı, dosyayı işlemek veya kaydetmek için kullanılan dosyanın C# gösterimi olan bir <xref:Microsoft.AspNetCore.Http.IFormFile>dosyaya doğru okunur.

Dosya yüklemeleri tarafından kullanılan kaynaklar (disk, bellek) eşzamanlı dosya yüklemelerinin sayısına ve boyutuna bağlıdır. Bir uygulama çok fazla yükleme arabelleğe almaya çalışırsa, bellek veya disk alanı bittiğinde site çöker. Dosya yüklemelerinin boyutu veya sıklığı uygulama kaynaklarını tüketiyorsa, akışı kullanın.

> [!NOTE]
> 64 KB'yi aşan tek bir arabelleğe alınan dosya bellekten diskteki geçici bir dosyaya taşınır.

Küçük dosyaların arabelleğe alma bu konunun aşağıdaki bölümlerinde ele alınmıştır:

* [Fiziksel depolama](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Veritabanı](#upload-small-files-with-buffered-model-binding-to-a-database)

**Akış**

Dosya çok parçalı bir istekten alınır ve uygulama tarafından doğrudan işlenir veya kaydedilir. Akış performansı önemli ölçüde artırmaz. Akış, dosya yüklerken bellek veya disk alanı taleplerini azaltır.

Büyük dosyaların akış [akışı, akış bölümüyle](#upload-large-files-with-streaming) birlikte Yükle büyük dosyaları yla kaplıdır.

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Fiziksel depolamaya bağlanan arabelleğe alınan modelle küçük dosyaları yükleme

Küçük dosyalar yüklemek için çok parçalı bir form kullanın veya JavaScript kullanarak bir POST isteği oluşturun.

Aşağıdaki örnek, tek bir dosyayı *(Pages/BufferedSingleFileUploadPhysical.cshtml)* örnek uygulamada yüklemek için bir Razor Pages formunun kullanımını göstermektedir:

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

Aşağıdaki örnek, aşağıdakiler dışında önceki örneğe benzer:

* JavaScript's ([ApI Getir](https://developer.mozilla.org/docs/Web/API/Fetch_API)) formun verilerini göndermek için kullanılır.
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

[ApI'yi desteklemeyen](https://caniuse.com/#feat=fetch)istemciler için JavaScript'te POST formunu gerçekleştirmek için aşağıdaki yaklaşımlardan birini kullanın:

* Bir Fetch Polyfill kullanın (örneğin, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
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

Dosya yüklemelerini desteklemek için HTML formlarında bir kodlama`enctype`türü `multipart/form-data`belirtilmelidir .

Birden `files` çok dosya yüklemeyi destekleyen bir `multiple` giriş öğesi `<input>` için öğedeki özniteliği sağlar:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Sunucuya yüklenen tek tek dosyalara [Model Bağlama](xref:mvc/models/model-binding) <xref:Microsoft.AspNetCore.Http.IFormFile>ile erişilebilir. Örnek uygulama, veritabanı ve fiziksel depolama senaryoları için birden çok arabelleğe alınan dosya yüklemesini gösterir.

<a name="filename"></a>

> [!WARNING]
> Görüntülemek **not** ve `FileName` günlüğe <xref:Microsoft.AspNetCore.Http.IFormFile> kaydetme dışında özelliğikullanmayın. Html, görüntüveya günlüğe kaydetme de dosya adını kodlar. Saldırgan, tam yollar veya göreli yollar da dahil olmak üzere kötü amaçlı bir dosya adı sağlayabilir. Uygulamalar:
>
> * Kullanıcı tarafından sağlanan dosya adından yolu kaldırın.
> * HTML kodlu, yol kaldırılmış dosya adını Kullanıcı Arabirimi veya günlüğe kaydetmeye kaydedin.
> * Depolama için yeni bir rasgele dosya adı oluşturun.
>
> Aşağıdaki kod, yolu dosya adından kaldırır:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Şimdiye kadar verilen örnekler güvenlik hususları dikkate almaz. Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanmaktadır:
>
> * [Güvenlik konuları](#security-considerations)
> * [Doğrulama](#validation)

Model bağlama ve <xref:Microsoft.AspNetCore.Http.IFormFile>, eylem yöntemi ni kullanarak dosya yüklerken şunları kabul edebilir:

* Tek <xref:Microsoft.AspNetCore.Http.IFormFile>bir.
* Birkaç dosyayı temsil eden aşağıdaki koleksiyonlardan herhangi biri:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Liste](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Bağlama eşleşmeleri form dosyalarını ada göre bağlar. Örneğin, HTML `name` değeri `<input type="file" name="formFile">` C# parametresi/özellik bağlı`FormFile`( eşleşmelidir. Daha fazla bilgi için POST yöntemi bölümünün [parametre adına Maç adı özniteliği değerine](#match-name-attribute-value-to-parameter-name-of-post-method) bakın.

Aşağıdaki örnek:

* Yüklenen bir veya daha fazla dosyayı döngüye sokabilir.
* Dosya adı da dahil olmak üzere bir dosya için tam bir yol döndürmek için [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanır. 
* Uygulama tarafından oluşturulan bir dosya adını kullanarak dosyaları yerel dosya sistemine kaydeder.
* Yüklenen dosyaların toplam sayısını ve boyutunu verir.

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

Yol `Path.GetRandomFileName` olmayan bir dosya adı oluşturmak için kullanın. Aşağıdaki örnekte, yol yapılandırmadan elde edilir:

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

Yol için geçirilen <xref:System.IO.FileStream> dosya adı *içermelidir.* Dosya adı sağlanmadıysa, çalışma <xref:System.UnauthorizedAccessException> saatinde bir a.k.

<xref:Microsoft.AspNetCore.Http.IFormFile> Teknik kullanılarak yüklenen dosyalar, işleme den önce bellekte veya sunucudaki diskte arabelleğe alınır. Eylem yöntemi içinde, <xref:Microsoft.AspNetCore.Http.IFormFile> içeriği ne <xref:System.IO.Stream>olarak erişilebilir bir . Yerel dosya sistemine ek olarak, dosyalar ağ paylaşımına veya [Azure Blob depolama](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)gibi bir dosya depolama hizmetine kaydedilebilir.

Yüklemek için birden çok dosyayı döngüye alan ve güvenli dosya adlarını kullanan başka bir örnek için, örnek uygulamada *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* s. bakın.

> [!WARNING]
> [Path.GetTempFileName,](xref:System.IO.Path.GetTempFileName*) önceki <xref:System.IO.IOException> geçici dosyaları silmeden 65.535'ten fazla dosya oluşturulursa atar. 65.535 dosya sınırı sunucu başına bir sınırdır. Windows işletim sistemi yle ilgili bu sınır hakkında daha fazla bilgi için aşağıdaki konulardaki açıklamalara bakın:
>
> * [GetTempFileNameA fonksiyonu](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Veritabanına arabelleğe alınan modele sahip küçük dosyaları yükleme

[Entity Framework'ü](/ef/core/index)kullanarak ikili dosya verilerini <xref:System.Byte> bir veritabanında depolamak için varlık üzerinde bir dizi özelliği tanımlayın:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Bir içeren sınıf için bir sayfa <xref:Microsoft.AspNetCore.Http.IFormFile>modeli özelliği belirtin:

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
> <xref:Microsoft.AspNetCore.Http.IFormFile>doğrudan bir eylem yöntemi parametresi olarak veya bağlı bir model özelliği olarak kullanılabilir. Önceki örnek, bağlı bir model özelliği kullanır.

`FileUpload` Razor Pages formunda kullanılır:

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

Form sunucuya verildiğinde, akışı <xref:Microsoft.AspNetCore.Http.IFormFile> bir akışa kopyalayın ve veritabanına bayt dizisi olarak kaydedin. Aşağıdaki örnekte, `_dbContext` uygulamanın veritabanı bağlamını saklar:

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

Önceki örnek, örnek uygulamada gösterilen bir senaryoya benzer:

* *Sayfalar/TamponluSingleFileUploadDb.cshtml*
* *Sayfalar/TamponluTekFileUploadDb.cshtml.cs*

> [!WARNING]
> Performansı olumsuz etkileyebileceğigibi, ikili verileri ilişkisel veritabanlarında depolamak için dikkatli olun.
>
> Doğrulama `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> olmadan özelliğine güvenmeyin veya güvenmeyin. Özellik `FileName` yalnızca görüntüleme amacıyla ve yalnızca HTML kodlaması sonrasında kullanılmalıdır.
>
> Verilen örnekler güvenlik hususlarını dikkate almaz. Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanmaktadır:
>
> * [Güvenlik konuları](#security-considerations)
> * [Doğrulama](#validation)

### <a name="upload-large-files-with-streaming"></a>Akışla büyük dosyaları yükleme

Aşağıdaki örnek, bir dosyayı denetleyici eylemine aktarmak için JavaScript'in nasıl kullanılacağını gösterir. Dosyanın antiforgery belirteci özel bir filtre özniteliği kullanılarak oluşturulur ve istek gövdesi yerine istemci HTTP üstbilgilerine geçirilir. Eylem yöntemi yüklenen verileri doğrudan işlediğiiçin, form modeli bağlama başka bir özel filtre tarafından devre dışı bırakılır. Eylem içinde, formun içeriği, her `MultipartReader`bir bireyi `MultipartSection`okuyan, dosyayı işleyen veya içeriği uygun şekilde depolayan bir , kullanılarak okunur. Çok parçalı bölümler okunduktan sonra, eylem kendi model bağlama gerçekleştirir.

İlk sayfa yanıtı formu yükler ve bir çerez (öznitelik üzerinden) `GenerateAntiforgeryTokenCookieAttribute` bir antiforgery belirteci kaydeder. Öznitelik, istek belirteci içeren bir çerez ayarlamak için ASP.NET Core'un yerleşik [antiforgery desteğini](xref:security/anti-request-forgery) kullanır:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Model `DisableFormValueModelBindingAttribute` bağlamadevre dışı kalmak için kullanılır:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

`GenerateAntiforgeryTokenCookieAttribute` Örnek uygulamada ve `DisableFormValueModelBindingAttribute` sayfa uygulama modellerine filtre olarak `/StreamedSingleFileUploadDb` uygulanır `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` ve [Jilet Sayfaları kuralları](xref:razor-pages/razor-pages-conventions)nı kullanır:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

Model bağlama formu okumadığından, formdan bağlanan parametreler bağlanmaz (sorgu, rota ve üstbilgi çalışmaya devam eder). Eylem yöntemi doğrudan `Request` özellik ile çalışır. A `MultipartReader` her bölümü okumak için kullanılır. Anahtar/değer verileri bir `KeyValueAccumulator`. Çok parçalı bölümler okunduktan sonra, `KeyValueAccumulator` form verilerini bir model türüne bağlamak için içeriği kullanılır.

EF `StreamingController.UploadDatabase` Core ile bir veritabanına akış için tam yöntem:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Yardımcı Programlar/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Fiziksel `StreamingController.UploadPhysical` bir konuma akış için tam yöntem:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

Örnek uygulamada, doğrulama denetimleri `FileHelpers.ProcessStreamedFile`.

## <a name="validation"></a>Doğrulama

Örnek uygulamanın `FileHelpers` sınıfı, arabelleğe alınan ve <xref:Microsoft.AspNetCore.Http.IFormFile> akışlı dosya yüklemeleri için birkaç denetim gösterir. Arabelleğe alınan dosya yüklemelerini örnek uygulamada <xref:Microsoft.AspNetCore.Http.IFormFile> `ProcessFormFile` işlemek için *Yardımcı Programlar/FileHelpers.cs* dosyasındaki yönteme bakın. Akışlı dosyaları işlemek için `ProcessStreamedFile` yönteme aynı dosyada bakın.

> [!WARNING]
> Örnek uygulamada gösterilen doğrulama işleme yöntemleri yüklenen dosyaların içeriğini tazmaz. Çoğu üretim senaryosunda, dosyayı kullanıcılar veya diğer sistemler için kullanılabilir hale getirmeden önce dosyada bir virüs/kötü amaçlı yazılım tarayıcı API kullanılır.
>
> Konu örneği doğrulama tekniklerinin çalışma örneğini sağlasa `FileHelpers` da, aşağıdakiler olmadıkça sınıfı bir üretim uygulamasında uygulamayın:
>
> * Uygulamayı tamamen anlayın.
> * Uygulamayı uygulamanın ortamına ve özelliklerine uygun şekilde değiştirin.
>
> **Bu gereksinimleri karşılamadan bir uygulamada hiçbir uygulamada ayrım gözetmeksizin güvenlik kodu uygulamayın.**

### <a name="content-validation"></a>İçerik doğrulama

**Yüklenen içerikte üçüncü taraf virüs/kötü amaçlı yazılım taraması API'si kullanın.**

Dosyaları taramak, yüksek hacimli senaryolarda sunucu kaynaklarında talep tedir. Dosya taraması nedeniyle istek işleme performansı azalmışsa, tarama çalışmasını bir [arka plan hizmetine](xref:fundamentals/host/hosted-services)(muhtemelen uygulamanın sunucusundan farklı bir sunucuda çalışan bir hizmete) yüklemeyi düşünün. Genellikle, yüklenen dosyalar, arka plan virüs tarayıcısı bunları kontrol edene kadar karantinaya alınan bir alanda tutulur. Dosya geçtiğinde, dosya normal dosya depolama konumuna taşınır. Bu adımlar genellikle bir dosyanın tarama durumunu gösteren bir veritabanı kaydı ile birlikte gerçekleştirilir. Böyle bir yaklaşım kullanarak, uygulama ve uygulama sunucusu istekleri yanıtlamaya odaklanmış kalır.

### <a name="file-extension-validation"></a>Dosya uzantısı doğrulaması

Yüklenen dosyanın uzantısı, izin verilen uzantılar listesiyle karşılaştırılmalıdır. Örneğin:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Dosya imza doğrulaması

Bir dosyanın imzası, bir dosyanın başındaki ilk birkaç bayt tarafından belirlenir. Bu baytlar, uzantının dosyanın içeriğiyle eşleşip eşleşmemediğini belirtmek için kullanılabilir. Örnek uygulama, birkaç yaygın dosya türü için dosya imzalarını denetler. Aşağıdaki örnekte, JPEG görüntüsünün dosya imzası dosyayla karşılaştırılır:

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

Ek dosya imzaları almak için [Dosya İmzaları Veritabanı'na](https://www.filesignatures.net/) ve resmi dosya özelliklerine bakın.

### <a name="file-name-security"></a>Dosya adı güvenliği

Bir dosyayı fiziksel depolama alanına kaydetmek için istemci tarafından sağlanan bir dosya adını asla kullanmayın. Geçici depolama alanı için tam bir yol (dosya adı dahil) oluşturmak için [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) veya [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanarak dosya için güvenli bir dosya adı oluşturun.

Razor HTML, görüntü lenmek için özellik değerlerini otomatik olarak kodlar. Aşağıdaki kodukullanmak güvenlidir:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Razor dışında, <xref:System.Net.WebUtility.HtmlEncode*> her zaman bir kullanıcının isteğinden ad içeriği dosya.

Birçok uygulama, dosyanın var olup olmadığını denetlemeyi içermelidir; aksi takdirde, dosya aynı adı taşıyan bir dosya tarafından üzerine yazılır. Uygulamanızın teknik özelliklerini karşılamak için ek mantık kaynağı.

### <a name="size-validation"></a>Boyut doğrulama

Yüklenen dosyaların boyutunu sınırlayın.

Örnek uygulamada, dosyanın boyutu 2 MB ile sınırlıdır (baytlarda gösterilir). Limit *appsettings.json* dosyasından [Configuration](xref:fundamentals/configuration/index) üzerinden sağlanır:

```json
{
  "FileSizeLimit": 2097152
}
```

Sınıflara `FileSizeLimit` `PageModel` enjekte edilir:

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

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>POST yönteminin parametre adı ile eşleme adı özniteliği değeri

POST'un veri oluşturduğu veya doğrudan JavaScript'i `FormData` kullandığı Jilet olmayan formlarda, `FormData` formun öğesinde belirtilen ad veya denetleyicinin eyleminde parametrenin adı ile eşleşmesi gerekir.

Aşağıdaki örnekte:

* Bir `<input>` öğe kullanırken, `name` öznitelik değere `battlePlans`ayarlanır:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* JavaScript'te kullanırken, `FormData` ad değerine `battlePlans`ayarlanır:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

C# yönteminin parametresi için eşleşen`battlePlans`bir ad kullanın ( ):

* Adlı bir Razor Pages sayfa `Upload`işleyicisi yöntemi için:

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* MVC POST denetleyici eylem yöntemi için:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Sunucu ve uygulama yapılandırması

### <a name="multipart-body-length-limit"></a>Çok parçalı gövde uzunluk sınırı

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>her çok parçalı gövdenin uzunluğu için sınırı ayarlar. Bu sınırı aşan form <xref:System.IO.InvalidDataException> bölümleri ayrıştırıldığında bir atar. Varsayılan değer 134.217.728 (128 MB) 'dir. Aşağıdaki ayarı kullanarak <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sınırı `Startup.ConfigureServices`özelleştirin:

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>tek bir sayfa <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> veya eylem için ayarlamak için kullanılır.

Razor Pages uygulamasında, filtreyi aşağıdaki ler `Startup.ConfigureServices`için bir [kuralla](xref:razor-pages/razor-pages-conventions) uygulayın:

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

Razor Pages uygulamasında veya Bir MVC uygulamasında, filtreyi sayfa modeline veya eylem yöntemine uygulayın:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kerkenez maksimum istek vücut boyutu

Kestrel tarafından barındırılan uygulamalar için varsayılan maksimum istek gövde boyutu yaklaşık 28,6 MB olan 30.000.000 bayttır. [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel sunucu seçeneğini kullanarak sınırı özelleştirin:

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

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>[MaxRequestBodySize'ı](xref:fundamentals/servers/kestrel#maximum-request-body-size) tek bir sayfa veya eylem için ayarlamak için kullanılır.

Razor Pages uygulamasında, filtreyi aşağıdaki ler `Startup.ConfigureServices`için bir [kuralla](xref:razor-pages/razor-pages-conventions) uygulayın:

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

Razor sayfaları uygulamasında veya Bir MVC uygulamasında, filtreyi sayfa işleyicisi sınıfına veya eylem yöntemine uygulayın:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

Ayrıca `RequestSizeLimitAttribute` Razor direktifi [`@attribute`](xref:mvc/views/razor#attribute) kullanılarak da uygulanabilir:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Diğer Kerkenez limitleri

Diğer Kerkenez limitleri Kerkenez tarafından barındırılan uygulamalar için geçerli olabilir:

* [Maksimum istemci bağlantıları](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [İstek ve yanıt veri oranları](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>IIS içerik uzunluğu sınırı

Varsayılan istek sınırı`maxAllowedContentLength`( ) yaklaşık 28,6 MB olan 30.000.000 bayttır. *web.config* dosyasındaki sınırı özelleştirin:

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

Bu ayar yalnızca IIS için geçerlidir. Kestrel'de barındırma yaparken davranış varsayılan olarak oluşmaz. Daha fazla bilgi için bkz [>. \< ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)

ASP.NET Çekirdek Modülündeki sınırlamalar veya IIS İstek Filtreleme Modülü'nün varlığı yüklemeleri 2 veya 4 GB ile sınırlayabilir. Daha fazla bilgi için bkz: [2GB boyutundan büyük dosya yükleyemiyorum (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Sorun giderme

Aşağıda, dosya yükleme ve bunların olası çözümleriyle çalışırken karşılaşılan bazı sık karşılaşılan sorunlar verilmiştir.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Bir IIS sunucusuna dağıtıldığında bulunamadı hatası

Aşağıdaki hata, yüklenen dosyanın sunucunun yapılandırılmış içerik uzunluğunu aştığını gösterir:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Sınırı artırma hakkında daha fazla bilgi için [IIS içerik uzunluğu sınırı](#iis-content-length-limit) bölümüne bakın.

### <a name="connection-failure"></a>Bağlantı hatası

Bir bağlantı hatası ve sıfırlama sunucusu bağlantısı büyük olasılıkla yüklenen dosyanın Kestrel'in maksimum istek gövde boyutunu aştığını gösterir. Daha fazla bilgi için [Kestrel maksimum istek gövde boyutu](#kestrel-maximum-request-body-size) bölümüne bakın. Kerkenez istemci bağlantı limitleri de ayar gerektirir.

### <a name="null-reference-exception-with-iformfile"></a>IFormFile ile Null Referans Özel Durum

Denetleyici yüklenen dosyaları <xref:Microsoft.AspNetCore.Http.IFormFile> kullanarak kabul ediyorsa `null`ancak değeri şuise, HTML `enctype` formunun bir değer belirtdiğini `multipart/form-data`doğrulayın. Bu öznitelik `<form>` öğe üzerinde ayarlı değilse, dosya yükleme sayılmaz ve bağlı <xref:Microsoft.AspNetCore.Http.IFormFile> bağımsız değişkenler . `null` Ayrıca, [form verilerindeki yükleme adlandırmasının uygulamanın adlandırmayla eşleştiğini](#match-name-attribute-value-to-parameter-name-of-post-method)de doğrulayın.

### <a name="stream-was-too-long"></a>Akış çok uzundu.

Bu konudaki örnekler, <xref:System.IO.MemoryStream> yüklenen dosyanın içeriğini tutmak için direnitir. A'nın `MemoryStream` boyut `int.MaxValue`sınırı. Uygulamanın dosya yükleme senaryosu 50 MB'dan büyük dosya içeriğinin tutulmasını gerektiriyorsa, `MemoryStream` yüklenen bir dosyanın içeriğini tutmak için tek bir dosyaya dayanmayan alternatif bir yaklaşım kullanın.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core, daha küçük dosyalar için arabelleğe alınan modeli ve daha büyük dosyalar için arabelleğe alamayan akışı kullanarak bir veya daha fazla dosya yüklemeyi destekler.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Güvenlik konuları

Kullanıcılara bir sunucuya dosya yükleme olanağı sağlarken dikkatli olun. Saldırganlar şunları deneyebilir:

* Hizmet reddi saldırılarını [gerçekleştirin.](/windows-hardware/drivers/ifs/denial-of-service)
* Virüs veya kötü amaçlı yazılım yükleyin.
* Ağları ve sunucuları başka şekillerde tehlikeye at.

Başarılı bir saldırı olasılığını azaltan güvenlik adımları şunlardır:

* Dosyaları özel bir dosya yükleme alanına, tercihen sistem dışı bir sürücüye yükleyin. Özel bir konum, yüklenen dosyalara güvenlik kısıtlamaları uygulamamayı kolaylaştırır. Dosya yükleme konumundaki yürütme izinlerini devre dışı kaltın.&dagger;
* Yüklenen dosyaları uygulamayla aynı dizin ağacında kalıcı olarak **yapmayın.**&dagger;
* Uygulama tarafından belirlenen güvenli bir dosya adı kullanın. Kullanıcı tarafından sağlanan bir dosya adını veya yüklenen dosyanın güvenilmeyen dosya adını kullanmayın. &dagger; HTML, görüntülenirken güvenilmeyen dosya adını kodlar. Örneğin, dosya adını günlüğe kaydetme veya Kullanıcı Arabirimi'nde görüntülenmesi (Razor otomatik olarak HTML çıktıyı kodlar).
* Uygulamanın tasarım belirtimi için yalnızca onaylı dosya uzantılarına izin verin.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* İstemci tarafı denetimlerinin sunucuda gerçekleştirildiğini doğrulayın. &dagger; İstemci tarafı denetimleri atlatmak kolaydır.
* Yüklenen bir dosyanın boyutunu denetleyin. Büyük yüklemeleri önlemek için maksimum boyut sınırı ayarlayın.&dagger;
* Dosyaların aynı ada sahip yüklenmiş bir dosya tarafından üzerine yazılmaması gerektiğinde, dosyayı yüklemeden önce dosya adını veritabanıyla veya fiziksel depolama alanına karşı denetleyin.
* **Dosya depolandırılmadan önce yüklenen içeriğe virüs/kötü amaçlı yazılım tarayıcısı çalıştırın.**

&dagger;Örnek uygulama, ölçütleri karşılayan bir yaklaşım gösterir.

> [!WARNING]
> Kötü amaçlı kodu bir sisteme yüklemek, sık sık aşağıdakileri yapan kodu yürütmenin ilk adımıdır:
>
> * Bir sistemin kontrolünü tamamen ele geçirmek.
> * Sistemin çöktüğı sonucu bir sistem aşırı yükleyin.
> * Kullanıcı veya sistem verilerini tehlikeye at.
> * Genel bir uI grafiti uygulayın.
>
> Kullanıcılardan dosya kabul ederken saldırı yüzey alanını azaltma hakkında bilgi için aşağıdaki kaynaklara bakın:
>
> * [Sınırsız Dosya Yükleme](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Azure Güvenliği: Kullanıcılardan dosya kabul ederken uygun denetimlerin yerinde olduğundan emin olun](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Örnek uygulamadan örnekler de dahil olmak üzere güvenlik önlemlerinin uygulanması hakkında daha fazla bilgi için [Doğrulama](#validation) bölümüne bakın.

## <a name="storage-scenarios"></a>Depolama senaryoları

Dosyalar için ortak depolama seçenekleri şunlardır:

* Veritabanı

  * Küçük dosya yüklemeleri için veritabanı genellikle fiziksel depolama (dosya sistemi veya ağ paylaşımı) seçeneklerinden daha hızlıdır.
  * Kullanıcı verileri için bir veritabanı kaydının alınması dosya içeriğini (örneğin, bir avatar görüntüsü) aynı anda sağlayabildiği için veritabanı genellikle fiziksel depolama seçeneklerinden daha kullanışlıdır.
  * Veritabanı, veri depolama hizmeti kullanmaktan daha ucuz olabilir.

* Fiziksel depolama (dosya sistemi veya ağ paylaşımı)

  * Büyük dosya yüklemeleri için:
    * Veritabanı sınırları yüklemeboyutunu kısıtlayabilir.
    * Fiziksel depolama genellikle veritabanındaki depolamadan daha az ekonomiktir.
  * Fiziksel depolama, veri depolama hizmeti kullanmaktan daha ucuz olabilir.
  * Uygulamanın işlemi, depolama konumuna izinleri okuyup yazmış olmalıdır. **Yürütme izni asla vermez.**

* Veri depolama hizmeti (örneğin, [Azure Blob Depolama)](https://azure.microsoft.com/services/storage/blobs/)

  * Hizmetler genellikle tek hata noktalarına maruz kalan şirket içi çözümler üzerinde gelişmiş ölçeklenebilirlik ve esneklik sunar.
  * Hizmetler, büyük depolama altyapısı senaryolarında potansiyel olarak daha düşük maliyetlidir.

  Daha fazla bilgi için [Quickstart: Object depolamaalanında bir leke oluşturmak için .NET'i kullanın.](/azure/storage/blobs/storage-quickstart-blobs-dotnet) <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>Konu gösterir , <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> ancak bir <xref:System.IO.FileStream> <xref:System.IO.Stream>ile çalışırken blob depolama kaydetmek için kullanılabilir .

## <a name="file-upload-scenarios"></a>Dosya yükleme senaryoları

Dosya yüklemek için iki genel yaklaşım arabelleğe alma ve akış vardır.

**Tamponlama**

Dosyanın tamamı, dosyayı işlemek veya kaydetmek için kullanılan dosyanın C# gösterimi olan bir <xref:Microsoft.AspNetCore.Http.IFormFile>dosyaya doğru okunur.

Dosya yüklemeleri tarafından kullanılan kaynaklar (disk, bellek) eşzamanlı dosya yüklemelerinin sayısına ve boyutuna bağlıdır. Bir uygulama çok fazla yükleme arabelleğe almaya çalışırsa, bellek veya disk alanı bittiğinde site çöker. Dosya yüklemelerinin boyutu veya sıklığı uygulama kaynaklarını tüketiyorsa, akışı kullanın.

> [!NOTE]
> 64 KB'yi aşan tek bir arabelleğe alınan dosya bellekten diskteki geçici bir dosyaya taşınır.

Küçük dosyaların arabelleğe alma bu konunun aşağıdaki bölümlerinde ele alınmıştır:

* [Fiziksel depolama](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Veritabanı](#upload-small-files-with-buffered-model-binding-to-a-database)

**Akış**

Dosya çok parçalı bir istekten alınır ve uygulama tarafından doğrudan işlenir veya kaydedilir. Akış performansı önemli ölçüde artırmaz. Akış, dosya yüklerken bellek veya disk alanı taleplerini azaltır.

Büyük dosyaların akış [akışı, akış bölümüyle](#upload-large-files-with-streaming) birlikte Yükle büyük dosyaları yla kaplıdır.

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Fiziksel depolamaya bağlanan arabelleğe alınan modelle küçük dosyaları yükleme

Küçük dosyalar yüklemek için çok parçalı bir form kullanın veya JavaScript kullanarak bir POST isteği oluşturun.

Aşağıdaki örnek, tek bir dosyayı *(Pages/BufferedSingleFileUploadPhysical.cshtml)* örnek uygulamada yüklemek için bir Razor Pages formunun kullanımını göstermektedir:

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

Aşağıdaki örnek, aşağıdakiler dışında önceki örneğe benzer:

* JavaScript's ([ApI Getir](https://developer.mozilla.org/docs/Web/API/Fetch_API)) formun verilerini göndermek için kullanılır.
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

[ApI'yi desteklemeyen](https://caniuse.com/#feat=fetch)istemciler için JavaScript'te POST formunu gerçekleştirmek için aşağıdaki yaklaşımlardan birini kullanın:

* Bir Fetch Polyfill kullanın (örneğin, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
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

Dosya yüklemelerini desteklemek için HTML formlarında bir kodlama`enctype`türü `multipart/form-data`belirtilmelidir .

Birden `files` çok dosya yüklemeyi destekleyen bir `multiple` giriş öğesi `<input>` için öğedeki özniteliği sağlar:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Sunucuya yüklenen tek tek dosyalara [Model Bağlama](xref:mvc/models/model-binding) <xref:Microsoft.AspNetCore.Http.IFormFile>ile erişilebilir. Örnek uygulama, veritabanı ve fiziksel depolama senaryoları için birden çok arabelleğe alınan dosya yüklemesini gösterir.

<a name="filename2"></a>

> [!WARNING]
> Görüntülemek **not** ve `FileName` günlüğe <xref:Microsoft.AspNetCore.Http.IFormFile> kaydetme dışında özelliğikullanmayın. Html, görüntüveya günlüğe kaydetme de dosya adını kodlar. Saldırgan, tam yollar veya göreli yollar da dahil olmak üzere kötü amaçlı bir dosya adı sağlayabilir. Uygulamalar:
>
> * Kullanıcı tarafından sağlanan dosya adından yolu kaldırın.
> * HTML kodlu, yol kaldırılmış dosya adını Kullanıcı Arabirimi veya günlüğe kaydetmeye kaydedin.
> * Depolama için yeni bir rasgele dosya adı oluşturun.
>
> Aşağıdaki kod, yolu dosya adından kaldırır:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Şimdiye kadar verilen örnekler güvenlik hususları dikkate almaz. Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanmaktadır:
>
> * [Güvenlik konuları](#security-considerations)
> * [Doğrulama](#validation)

Model bağlama ve <xref:Microsoft.AspNetCore.Http.IFormFile>, eylem yöntemi ni kullanarak dosya yüklerken şunları kabul edebilir:

* Tek <xref:Microsoft.AspNetCore.Http.IFormFile>bir.
* Birkaç dosyayı temsil eden aşağıdaki koleksiyonlardan herhangi biri:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Liste](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Bağlama eşleşmeleri form dosyalarını ada göre bağlar. Örneğin, HTML `name` değeri `<input type="file" name="formFile">` C# parametresi/özellik bağlı`FormFile`( eşleşmelidir. Daha fazla bilgi için POST yöntemi bölümünün [parametre adına Maç adı özniteliği değerine](#match-name-attribute-value-to-parameter-name-of-post-method) bakın.

Aşağıdaki örnek:

* Yüklenen bir veya daha fazla dosyayı döngüye sokabilir.
* Dosya adı da dahil olmak üzere bir dosya için tam bir yol döndürmek için [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanır. 
* Uygulama tarafından oluşturulan bir dosya adını kullanarak dosyaları yerel dosya sistemine kaydeder.
* Yüklenen dosyaların toplam sayısını ve boyutunu verir.

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

Yol `Path.GetRandomFileName` olmayan bir dosya adı oluşturmak için kullanın. Aşağıdaki örnekte, yol yapılandırmadan elde edilir:

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

Yol için geçirilen <xref:System.IO.FileStream> dosya adı *içermelidir.* Dosya adı sağlanmadıysa, çalışma <xref:System.UnauthorizedAccessException> saatinde bir a.k.

<xref:Microsoft.AspNetCore.Http.IFormFile> Teknik kullanılarak yüklenen dosyalar, işleme den önce bellekte veya sunucudaki diskte arabelleğe alınır. Eylem yöntemi içinde, <xref:Microsoft.AspNetCore.Http.IFormFile> içeriği ne <xref:System.IO.Stream>olarak erişilebilir bir . Yerel dosya sistemine ek olarak, dosyalar ağ paylaşımına veya [Azure Blob depolama](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)gibi bir dosya depolama hizmetine kaydedilebilir.

Yüklemek için birden çok dosyayı döngüye alan ve güvenli dosya adlarını kullanan başka bir örnek için, örnek uygulamada *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* s. bakın.

> [!WARNING]
> [Path.GetTempFileName,](xref:System.IO.Path.GetTempFileName*) önceki <xref:System.IO.IOException> geçici dosyaları silmeden 65.535'ten fazla dosya oluşturulursa atar. 65.535 dosya sınırı sunucu başına bir sınırdır. Windows işletim sistemi yle ilgili bu sınır hakkında daha fazla bilgi için aşağıdaki konulardaki açıklamalara bakın:
>
> * [GetTempFileNameA fonksiyonu](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Veritabanına arabelleğe alınan modele sahip küçük dosyaları yükleme

[Entity Framework'ü](/ef/core/index)kullanarak ikili dosya verilerini <xref:System.Byte> bir veritabanında depolamak için varlık üzerinde bir dizi özelliği tanımlayın:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Bir içeren sınıf için bir sayfa <xref:Microsoft.AspNetCore.Http.IFormFile>modeli özelliği belirtin:

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
> <xref:Microsoft.AspNetCore.Http.IFormFile>doğrudan bir eylem yöntemi parametresi olarak veya bağlı bir model özelliği olarak kullanılabilir. Önceki örnek, bağlı bir model özelliği kullanır.

`FileUpload` Razor Pages formunda kullanılır:

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

Form sunucuya verildiğinde, akışı <xref:Microsoft.AspNetCore.Http.IFormFile> bir akışa kopyalayın ve veritabanına bayt dizisi olarak kaydedin. Aşağıdaki örnekte, `_dbContext` uygulamanın veritabanı bağlamını saklar:

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

Önceki örnek, örnek uygulamada gösterilen bir senaryoya benzer:

* *Sayfalar/TamponluSingleFileUploadDb.cshtml*
* *Sayfalar/TamponluTekFileUploadDb.cshtml.cs*

> [!WARNING]
> Performansı olumsuz etkileyebileceğigibi, ikili verileri ilişkisel veritabanlarında depolamak için dikkatli olun.
>
> Doğrulama `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> olmadan özelliğine güvenmeyin veya güvenmeyin. Özellik `FileName` yalnızca görüntüleme amacıyla ve yalnızca HTML kodlaması sonrasında kullanılmalıdır.
>
> Verilen örnekler güvenlik hususlarını dikkate almaz. Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanmaktadır:
>
> * [Güvenlik konuları](#security-considerations)
> * [Doğrulama](#validation)

### <a name="upload-large-files-with-streaming"></a>Akışla büyük dosyaları yükleme

Aşağıdaki örnek, bir dosyayı denetleyici eylemine aktarmak için JavaScript'in nasıl kullanılacağını gösterir. Dosyanın antiforgery belirteci özel bir filtre özniteliği kullanılarak oluşturulur ve istek gövdesi yerine istemci HTTP üstbilgilerine geçirilir. Eylem yöntemi yüklenen verileri doğrudan işlediğiiçin, form modeli bağlama başka bir özel filtre tarafından devre dışı bırakılır. Eylem içinde, formun içeriği, her `MultipartReader`bir bireyi `MultipartSection`okuyan, dosyayı işleyen veya içeriği uygun şekilde depolayan bir , kullanılarak okunur. Çok parçalı bölümler okunduktan sonra, eylem kendi model bağlama gerçekleştirir.

İlk sayfa yanıtı formu yükler ve bir çerez (öznitelik üzerinden) `GenerateAntiforgeryTokenCookieAttribute` bir antiforgery belirteci kaydeder. Öznitelik, istek belirteci içeren bir çerez ayarlamak için ASP.NET Core'un yerleşik [antiforgery desteğini](xref:security/anti-request-forgery) kullanır:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Model `DisableFormValueModelBindingAttribute` bağlamadevre dışı kalmak için kullanılır:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

`GenerateAntiforgeryTokenCookieAttribute` Örnek uygulamada ve `DisableFormValueModelBindingAttribute` sayfa uygulama modellerine filtre olarak `/StreamedSingleFileUploadDb` uygulanır `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` ve [Jilet Sayfaları kuralları](xref:razor-pages/razor-pages-conventions)nı kullanır:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Model bağlama formu okumadığından, formdan bağlanan parametreler bağlanmaz (sorgu, rota ve üstbilgi çalışmaya devam eder). Eylem yöntemi doğrudan `Request` özellik ile çalışır. A `MultipartReader` her bölümü okumak için kullanılır. Anahtar/değer verileri bir `KeyValueAccumulator`. Çok parçalı bölümler okunduktan sonra, `KeyValueAccumulator` form verilerini bir model türüne bağlamak için içeriği kullanılır.

EF `StreamingController.UploadDatabase` Core ile bir veritabanına akış için tam yöntem:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Yardımcı Programlar/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Fiziksel `StreamingController.UploadPhysical` bir konuma akış için tam yöntem:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

Örnek uygulamada, doğrulama denetimleri `FileHelpers.ProcessStreamedFile`.

## <a name="validation"></a>Doğrulama

Örnek uygulamanın `FileHelpers` sınıfı, arabelleğe alınan ve <xref:Microsoft.AspNetCore.Http.IFormFile> akışlı dosya yüklemeleri için birkaç denetim gösterir. Arabelleğe alınan dosya yüklemelerini örnek uygulamada <xref:Microsoft.AspNetCore.Http.IFormFile> `ProcessFormFile` işlemek için *Yardımcı Programlar/FileHelpers.cs* dosyasındaki yönteme bakın. Akışlı dosyaları işlemek için `ProcessStreamedFile` yönteme aynı dosyada bakın.

> [!WARNING]
> Örnek uygulamada gösterilen doğrulama işleme yöntemleri yüklenen dosyaların içeriğini tazmaz. Çoğu üretim senaryosunda, dosyayı kullanıcılar veya diğer sistemler için kullanılabilir hale getirmeden önce dosyada bir virüs/kötü amaçlı yazılım tarayıcı API kullanılır.
>
> Konu örneği doğrulama tekniklerinin çalışma örneğini sağlasa `FileHelpers` da, aşağıdakiler olmadıkça sınıfı bir üretim uygulamasında uygulamayın:
>
> * Uygulamayı tamamen anlayın.
> * Uygulamayı uygulamanın ortamına ve özelliklerine uygun şekilde değiştirin.
>
> **Bu gereksinimleri karşılamadan bir uygulamada hiçbir uygulamada ayrım gözetmeksizin güvenlik kodu uygulamayın.**

### <a name="content-validation"></a>İçerik doğrulama

**Yüklenen içerikte üçüncü taraf virüs/kötü amaçlı yazılım taraması API'si kullanın.**

Dosyaları taramak, yüksek hacimli senaryolarda sunucu kaynaklarında talep tedir. Dosya taraması nedeniyle istek işleme performansı azalmışsa, tarama çalışmasını bir [arka plan hizmetine](xref:fundamentals/host/hosted-services)(muhtemelen uygulamanın sunucusundan farklı bir sunucuda çalışan bir hizmete) yüklemeyi düşünün. Genellikle, yüklenen dosyalar, arka plan virüs tarayıcısı bunları kontrol edene kadar karantinaya alınan bir alanda tutulur. Dosya geçtiğinde, dosya normal dosya depolama konumuna taşınır. Bu adımlar genellikle bir dosyanın tarama durumunu gösteren bir veritabanı kaydı ile birlikte gerçekleştirilir. Böyle bir yaklaşım kullanarak, uygulama ve uygulama sunucusu istekleri yanıtlamaya odaklanmış kalır.

### <a name="file-extension-validation"></a>Dosya uzantısı doğrulaması

Yüklenen dosyanın uzantısı, izin verilen uzantılar listesiyle karşılaştırılmalıdır. Örneğin:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Dosya imza doğrulaması

Bir dosyanın imzası, bir dosyanın başındaki ilk birkaç bayt tarafından belirlenir. Bu baytlar, uzantının dosyanın içeriğiyle eşleşip eşleşmemediğini belirtmek için kullanılabilir. Örnek uygulama, birkaç yaygın dosya türü için dosya imzalarını denetler. Aşağıdaki örnekte, JPEG görüntüsünün dosya imzası dosyayla karşılaştırılır:

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

Ek dosya imzaları almak için [Dosya İmzaları Veritabanı'na](https://www.filesignatures.net/) ve resmi dosya özelliklerine bakın.

### <a name="file-name-security"></a>Dosya adı güvenliği

Bir dosyayı fiziksel depolama alanına kaydetmek için istemci tarafından sağlanan bir dosya adını asla kullanmayın. Geçici depolama alanı için tam bir yol (dosya adı dahil) oluşturmak için [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) veya [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanarak dosya için güvenli bir dosya adı oluşturun.

Razor HTML, görüntü lenmek için özellik değerlerini otomatik olarak kodlar. Aşağıdaki kodukullanmak güvenlidir:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Razor dışında, <xref:System.Net.WebUtility.HtmlEncode*> her zaman bir kullanıcının isteğinden ad içeriği dosya.

Birçok uygulama, dosyanın var olup olmadığını denetlemeyi içermelidir; aksi takdirde, dosya aynı adı taşıyan bir dosya tarafından üzerine yazılır. Uygulamanızın teknik özelliklerini karşılamak için ek mantık kaynağı.

### <a name="size-validation"></a>Boyut doğrulama

Yüklenen dosyaların boyutunu sınırlayın.

Örnek uygulamada, dosyanın boyutu 2 MB ile sınırlıdır (baytlarda gösterilir). Limit *appsettings.json* dosyasından [Configuration](xref:fundamentals/configuration/index) üzerinden sağlanır:

```json
{
  "FileSizeLimit": 2097152
}
```

Sınıflara `FileSizeLimit` `PageModel` enjekte edilir:

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

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>POST yönteminin parametre adı ile eşleme adı özniteliği değeri

POST'un veri oluşturduğu veya doğrudan JavaScript'i `FormData` kullandığı Jilet olmayan formlarda, `FormData` formun öğesinde belirtilen ad veya denetleyicinin eyleminde parametrenin adı ile eşleşmesi gerekir.

Aşağıdaki örnekte:

* Bir `<input>` öğe kullanırken, `name` öznitelik değere `battlePlans`ayarlanır:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* JavaScript'te kullanırken, `FormData` ad değerine `battlePlans`ayarlanır:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

C# yönteminin parametresi için eşleşen`battlePlans`bir ad kullanın ( ):

* Adlı bir Razor Pages sayfa `Upload`işleyicisi yöntemi için:

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* MVC POST denetleyici eylem yöntemi için:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Sunucu ve uygulama yapılandırması

### <a name="multipart-body-length-limit"></a>Çok parçalı gövde uzunluk sınırı

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>her çok parçalı gövdenin uzunluğu için sınırı ayarlar. Bu sınırı aşan form <xref:System.IO.InvalidDataException> bölümleri ayrıştırıldığında bir atar. Varsayılan değer 134.217.728 (128 MB) 'dir. Aşağıdaki ayarı kullanarak <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sınırı `Startup.ConfigureServices`özelleştirin:

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>tek bir sayfa <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> veya eylem için ayarlamak için kullanılır.

Razor Pages uygulamasında, filtreyi aşağıdaki ler `Startup.ConfigureServices`için bir [kuralla](xref:razor-pages/razor-pages-conventions) uygulayın:

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

Razor Pages uygulamasında veya Bir MVC uygulamasında, filtreyi sayfa modeline veya eylem yöntemine uygulayın:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kerkenez maksimum istek vücut boyutu

Kestrel tarafından barındırılan uygulamalar için varsayılan maksimum istek gövde boyutu yaklaşık 28,6 MB olan 30.000.000 bayttır. [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel sunucu seçeneğini kullanarak sınırı özelleştirin:

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

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>[MaxRequestBodySize'ı](xref:fundamentals/servers/kestrel#maximum-request-body-size) tek bir sayfa veya eylem için ayarlamak için kullanılır.

Razor Pages uygulamasında, filtreyi aşağıdaki ler `Startup.ConfigureServices`için bir [kuralla](xref:razor-pages/razor-pages-conventions) uygulayın:

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

Razor sayfaları uygulamasında veya Bir MVC uygulamasında, filtreyi sayfa işleyicisi sınıfına veya eylem yöntemine uygulayın:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Diğer Kerkenez limitleri

Diğer Kerkenez limitleri Kerkenez tarafından barındırılan uygulamalar için geçerli olabilir:

* [Maksimum istemci bağlantıları](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [İstek ve yanıt veri oranları](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>IIS içerik uzunluğu sınırı

Varsayılan istek sınırı`maxAllowedContentLength`( ) yaklaşık 28,6 MB olan 30.000.000 bayttır. *web.config* dosyasındaki sınırı özelleştirin:

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

Bu ayar yalnızca IIS için geçerlidir. Kestrel'de barındırma yaparken davranış varsayılan olarak oluşmaz. Daha fazla bilgi için bkz [>. \< ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)

ASP.NET Çekirdek Modülündeki sınırlamalar veya IIS İstek Filtreleme Modülü'nün varlığı yüklemeleri 2 veya 4 GB ile sınırlayabilir. Daha fazla bilgi için bkz: [2GB boyutundan büyük dosya yükleyemiyorum (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Sorun giderme

Aşağıda, dosya yükleme ve bunların olası çözümleriyle çalışırken karşılaşılan bazı sık karşılaşılan sorunlar verilmiştir.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Bir IIS sunucusuna dağıtıldığında bulunamadı hatası

Aşağıdaki hata, yüklenen dosyanın sunucunun yapılandırılmış içerik uzunluğunu aştığını gösterir:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Sınırı artırma hakkında daha fazla bilgi için [IIS içerik uzunluğu sınırı](#iis-content-length-limit) bölümüne bakın.

### <a name="connection-failure"></a>Bağlantı hatası

Bir bağlantı hatası ve sıfırlama sunucusu bağlantısı büyük olasılıkla yüklenen dosyanın Kestrel'in maksimum istek gövde boyutunu aştığını gösterir. Daha fazla bilgi için [Kestrel maksimum istek gövde boyutu](#kestrel-maximum-request-body-size) bölümüne bakın. Kerkenez istemci bağlantı limitleri de ayar gerektirir.

### <a name="null-reference-exception-with-iformfile"></a>IFormFile ile Null Referans Özel Durum

Denetleyici yüklenen dosyaları <xref:Microsoft.AspNetCore.Http.IFormFile> kullanarak kabul ediyorsa `null`ancak değeri şuise, HTML `enctype` formunun bir değer belirtdiğini `multipart/form-data`doğrulayın. Bu öznitelik `<form>` öğe üzerinde ayarlı değilse, dosya yükleme sayılmaz ve bağlı <xref:Microsoft.AspNetCore.Http.IFormFile> bağımsız değişkenler . `null` Ayrıca, [form verilerindeki yükleme adlandırmasının uygulamanın adlandırmayla eşleştiğini](#match-name-attribute-value-to-parameter-name-of-post-method)de doğrulayın.

### <a name="stream-was-too-long"></a>Akış çok uzundu.

Bu konudaki örnekler, <xref:System.IO.MemoryStream> yüklenen dosyanın içeriğini tutmak için direnitir. A'nın `MemoryStream` boyut `int.MaxValue`sınırı. Uygulamanın dosya yükleme senaryosu 50 MB'dan büyük dosya içeriğinin tutulmasını gerektiriyorsa, `MemoryStream` yüklenen bir dosyanın içeriğini tutmak için tek bir dosyaya dayanmayan alternatif bir yaklaşım kullanın.

::: moniker-end


## <a name="additional-resources"></a>Ek kaynaklar

* [Sınırsız Dosya Yükleme](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [Azure Güvenliği: Güvenlik Çerçevesi: Giriş Doğrulama | Azaltıcı etken](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Azure Bulut Tasarım Desenleri: Vale Anahtarı deseni](/azure/architecture/patterns/valet-key)
