---
title: ASP.NET Core dosyaları karşıya yükleme
author: rick-anderson
description: ASP.NET Core MVC 'de dosyaları karşıya yüklemek için model bağlama ve akışı kullanma.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/03/2020
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
uid: mvc/models/file-uploads
ms.openlocfilehash: 93ffa3a5313e63a1e9b98fb5bf9788944254213f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635222"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="114fb-103">ASP.NET Core dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="114fb-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="114fb-104">[Steve Smith](https://ardalis.com/) ve [Rutger fırtınası](https://github.com/rutix) tarafından</span><span class="sxs-lookup"><span data-stu-id="114fb-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="114fb-105">ASP.NET Core, daha küçük dosyalar için arabellekli model bağlama ve daha büyük dosyalar için arabelleğe alınmamış akış kullanarak bir veya daha fazla dosyanın yüklenmesini destekler</span><span class="sxs-lookup"><span data-stu-id="114fb-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="114fb-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="114fb-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="114fb-107">Güvenlikle ilgili dikkat edilmesi gerekenler</span><span class="sxs-lookup"><span data-stu-id="114fb-107">Security considerations</span></span>

<span data-ttu-id="114fb-108">Kullanıcılara bir sunucuya dosya yükleme yeteneği sağlarken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="114fb-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="114fb-109">Saldırganlar şunları deneyebilir:</span><span class="sxs-lookup"><span data-stu-id="114fb-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="114fb-110">[Hizmet reddi](/windows-hardware/drivers/ifs/denial-of-service) saldırıları yürütün.</span><span class="sxs-lookup"><span data-stu-id="114fb-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="114fb-111">Virüsleri veya kötü amaçlı yazılımları karşıya yükleyin.</span><span class="sxs-lookup"><span data-stu-id="114fb-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="114fb-112">Ağları ve sunucuları diğer yollarla tehlikeye atabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="114fb-113">Başarılı bir saldırının olasılığını azaltan güvenlik adımları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="114fb-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="114fb-114">Dosyaları, tercihen sistem dışı bir sürücüye, özel bir dosya yükleme alanına yükleyin.</span><span class="sxs-lookup"><span data-stu-id="114fb-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="114fb-115">Ayrılmış bir konum, karşıya yüklenen dosyalar üzerinde güvenlik kısıtlamaları yapmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="114fb-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="114fb-116">Dosya yükleme konumunda yürütme izinlerini devre dışı bırakın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="114fb-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="114fb-117">Karşıya yüklenen dosyaları uygulamayla aynı dizin **ağacında kalıcı hale** getirme.&dagger;</span><span class="sxs-lookup"><span data-stu-id="114fb-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="114fb-118">Uygulama tarafından belirlenen bir güvenli dosya adı kullanın.</span><span class="sxs-lookup"><span data-stu-id="114fb-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="114fb-119">Kullanıcı tarafından belirtilen bir dosya adı veya karşıya yüklenen dosyanın güvenilmeyen dosya adı kullanmayın. &dagger; HTML, görüntüleme sırasında güvenilmeyen dosya adını kodlayamıyor.</span><span class="sxs-lookup"><span data-stu-id="114fb-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="114fb-120">Örneğin, dosya adını günlüğe kaydetme veya Kullanıcı arabiriminde görüntüleme ( Razor otomatik olarak HTML kodlama çıktısı).</span><span class="sxs-lookup"><span data-stu-id="114fb-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="114fb-121">Uygulamanın tasarım belirtimi için yalnızca onaylanan dosya uzantılarına izin verin.&dagger;</span><span class="sxs-lookup"><span data-stu-id="114fb-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="114fb-122">Sunucuda istemci tarafı denetimlerinin gerçekleştirildiğinden emin olun. &dagger; İstemci tarafı denetimleri kolayca atmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="114fb-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="114fb-123">Karşıya yüklenen dosyanın boyutunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="114fb-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="114fb-124">Büyük karşıya yüklemeleri engellemek için en büyük boyut sınırını ayarlayın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="114fb-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="114fb-125">Aynı ada sahip karşıya yüklenen bir dosya tarafından dosyaların üzerine yazılmaması gerektiğinde, dosyayı karşıya yüklemeden önce dosya adını veritabanına veya fiziksel depolamaya göre denetleyin.</span><span class="sxs-lookup"><span data-stu-id="114fb-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="114fb-126">**Dosya depolanmadan önce karşıya yüklenen içerik üzerinde bir virüs/kötü amaçlı yazılım tarayıcısı çalıştırın.**</span><span class="sxs-lookup"><span data-stu-id="114fb-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="114fb-127">&dagger;Örnek uygulama, ölçütlere uyan bir yaklaşımı gösterir.</span><span class="sxs-lookup"><span data-stu-id="114fb-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="114fb-128">Kötü amaçlı kodun bir sisteme yüklenmesi genellikle şu şekilde kod yürütmenin ilk adımıdır:</span><span class="sxs-lookup"><span data-stu-id="114fb-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="114fb-129">Sistemin denetimini tamamen elde edin.</span><span class="sxs-lookup"><span data-stu-id="114fb-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="114fb-130">Sistemin kilitlenme sonucuyla bir sistemi aşırı yükleme.</span><span class="sxs-lookup"><span data-stu-id="114fb-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="114fb-131">Kullanıcı veya Sistem verilerinin güvenliğini tehlikeye atabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="114fb-132">Genel Kullanıcı arabirimine Graffiti uygulayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="114fb-133">Kullanıcılardan dosya kabul edilirken saldırı yüzeyi alanını azaltma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="114fb-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="114fb-134">Kısıtlanmamış dosya yükleme</span><span class="sxs-lookup"><span data-stu-id="114fb-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="114fb-135">Azure güvenliği: kullanıcılardan dosya kabul edilirken uygun denetimlerin yerinde olduğundan emin olun</span><span class="sxs-lookup"><span data-stu-id="114fb-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="114fb-136">Örnek uygulamadaki örnekler de dahil olmak üzere güvenlik önlemlerini uygulama hakkında daha fazla bilgi için [doğrulama](#validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="114fb-137">Depolama senaryoları</span><span class="sxs-lookup"><span data-stu-id="114fb-137">Storage scenarios</span></span>

<span data-ttu-id="114fb-138">Dosyalar için ortak depolama seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="114fb-138">Common storage options for files include:</span></span>

* <span data-ttu-id="114fb-139">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="114fb-139">Database</span></span>

  * <span data-ttu-id="114fb-140">Küçük dosya yüklemeleri için, bir veritabanı genellikle fiziksel depolama (dosya sistemi veya ağ paylaşımından) seçeneklerinden daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="114fb-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="114fb-141">Kullanıcı verileri için bir veritabanı kaydının alınması eşzamanlı olarak dosya içeriğini (örneğin, avatar görüntüsü) sağlayabildiğinden, veritabanı genellikle fiziksel depolama seçeneklerine göre daha uygundur.</span><span class="sxs-lookup"><span data-stu-id="114fb-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="114fb-142">Bir veritabanı, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="114fb-143">Fiziksel depolama (dosya sistemi veya ağ paylaşma)</span><span class="sxs-lookup"><span data-stu-id="114fb-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="114fb-144">Büyük dosya yüklemeleri için:</span><span class="sxs-lookup"><span data-stu-id="114fb-144">For large file uploads:</span></span>
    * <span data-ttu-id="114fb-145">Veritabanı limitleri karşıya yükleme boyutunu kısıtlayabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="114fb-146">Fiziksel depolama genellikle bir veritabanındaki depolamadan daha az ekonomik olur.</span><span class="sxs-lookup"><span data-stu-id="114fb-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="114fb-147">Fiziksel depolama, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="114fb-148">Uygulamanın işlemi, depolama konumu için okuma ve yazma izinlerine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="114fb-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="114fb-149">**Hiçbir zaman yürütme izni vermeyin.**</span><span class="sxs-lookup"><span data-stu-id="114fb-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="114fb-150">Veri depolama hizmeti (örneğin, [Azure Blob depolama](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="114fb-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="114fb-151">Hizmetler genellikle tek hata noktalarına tabi olan şirket içi çözümler üzerinde geliştirilmiş ölçeklenebilirlik ve esneklik sunar.</span><span class="sxs-lookup"><span data-stu-id="114fb-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="114fb-152">Hizmetler büyük depolama altyapısı senaryolarında düşük maliyetlidir.</span><span class="sxs-lookup"><span data-stu-id="114fb-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="114fb-153">Daha fazla bilgi için bkz. [hızlı başlangıç: nesne depolamada blob oluşturmak için .NET kullanma](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="114fb-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="114fb-154">Karşıya dosya yükleme senaryoları</span><span class="sxs-lookup"><span data-stu-id="114fb-154">File upload scenarios</span></span>

<span data-ttu-id="114fb-155">Dosyaları karşıya yüklemek için iki genel yaklaşım arabelleğe alınır ve akışlardır.</span><span class="sxs-lookup"><span data-stu-id="114fb-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="114fb-156">**Ara**</span><span class="sxs-lookup"><span data-stu-id="114fb-156">**Buffering**</span></span>

<span data-ttu-id="114fb-157">Dosyanın tamamı <xref:Microsoft.AspNetCore.Http.IFormFile> , dosyayı işlemek veya kaydetmek için kullanılan dosyanın C# temsili olan öğesine okunurdur.</span><span class="sxs-lookup"><span data-stu-id="114fb-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="114fb-158">Dosya karşıya yüklemeleri tarafından kullanılan kaynaklar (disk, bellek), eşzamanlı dosya karşıya yüklemelerinin sayısına ve boyutuna bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="114fb-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="114fb-159">Bir uygulama çok fazla karşıya yükleme arabelleğini denerse, bellek veya disk alanı tükenirse site çöker.</span><span class="sxs-lookup"><span data-stu-id="114fb-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="114fb-160">Karşıya dosya yükleme boyutu veya sıklığı uygulama kaynaklarını tüketme ise, akış ' ı kullanın.</span><span class="sxs-lookup"><span data-stu-id="114fb-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="114fb-161">64 KB geçen tek bir arabelleğe alınmış dosya, bellekten diskte geçici bir dosyaya taşınır.</span><span class="sxs-lookup"><span data-stu-id="114fb-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="114fb-162">Dosya arabelleğe alma, bu konunun aşağıdaki bölümlerinde ele alınmıştır:</span><span class="sxs-lookup"><span data-stu-id="114fb-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="114fb-163">Fiziksel depolama alanı</span><span class="sxs-lookup"><span data-stu-id="114fb-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="114fb-164">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="114fb-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="114fb-165">**Akışlar**</span><span class="sxs-lookup"><span data-stu-id="114fb-165">**Streaming**</span></span>

<span data-ttu-id="114fb-166">Dosya çok parçalı bir istekten alınır ve doğrudan uygulama tarafından işlenir veya kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="114fb-167">Akış performansı önemli ölçüde iyileştirmez.</span><span class="sxs-lookup"><span data-stu-id="114fb-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="114fb-168">Akış, dosya karşıya yüklenirken bellek veya disk alanı taleplerini azaltır.</span><span class="sxs-lookup"><span data-stu-id="114fb-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="114fb-169">Akış büyük dosyaları [akış ile büyük dosyaları karşıya yükle](#upload-large-files-with-streaming) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="114fb-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="114fb-170">Fiziksel depolamaya arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="114fb-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="114fb-171">Küçük dosyaları karşıya yüklemek için, çok parçalı bir form kullanın veya JavaScript kullanarak bir POST isteği oluşturun.</span><span class="sxs-lookup"><span data-stu-id="114fb-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="114fb-172">Aşağıdaki örnek, Razor tek bir dosyayı karşıya yüklemek için sayfalar formunun kullanımını gösterir (örnek uygulamada*Pages/Bufferedsinglefileuploadfiziksel. cshtml* ):</span><span class="sxs-lookup"><span data-stu-id="114fb-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="114fb-173">Aşağıdaki örnek, önceki örneğe benzerdir, ancak şunları hariç:</span><span class="sxs-lookup"><span data-stu-id="114fb-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="114fb-174">Form verilerini göndermek için JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="114fb-175">Doğrulama yok.</span><span class="sxs-lookup"><span data-stu-id="114fb-175">There's no validation.</span></span>

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

<span data-ttu-id="114fb-176">[Fetch API 'sini desteklemeyen](https://caniuse.com/#feat=fetch)istemcilerde form gönderisini JavaScript 'te gerçekleştirmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="114fb-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="114fb-177">Fetch Polyfill kullanın (örneğin, [Window. Fetch (GitHub/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="114fb-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="114fb-178">`XMLHttpRequest` komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="114fb-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="114fb-179">Örnek:</span><span class="sxs-lookup"><span data-stu-id="114fb-179">For example:</span></span>

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

<span data-ttu-id="114fb-180">Dosya yüklemelerini desteklemek için, HTML formları bir kodlama türü ( `enctype` ) belirtmelidir `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="114fb-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="114fb-181">`files`Birden çok dosyayı karşıya yüklemeyi destekleyen bir giriş öğesi için, `multiple` öğesine özniteliği sağlar `<input>` :</span><span class="sxs-lookup"><span data-stu-id="114fb-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="114fb-182">Sunucuya yüklenen tek dosyalara, kullanılarak [model bağlama](xref:mvc/models/model-binding) yoluyla erişilebilir <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="114fb-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="114fb-183">Örnek uygulama, veritabanı ve fiziksel depolama senaryoları için birden çok arabellekli dosya yüklemeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="114fb-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="114fb-184">**not** `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> Görüntüleme ve günlüğe kaydetme için dışındaki özelliğini kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="114fb-185">Görüntüleme veya günlüğe kaydetme sırasında, HTML dosya adını kodlayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="114fb-186">Saldırgan, tam yollar veya göreli yollar dahil olmak üzere kötü amaçlı bir dosya adı sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="114fb-187">Uygulamalar:</span><span class="sxs-lookup"><span data-stu-id="114fb-187">Applications should:</span></span>
>
> * <span data-ttu-id="114fb-188">Kullanıcı tarafından sağlanan dosya adının yolunu kaldırın.</span><span class="sxs-lookup"><span data-stu-id="114fb-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="114fb-189">Kullanıcı arabirimi veya günlüğe kaydetme için HTML kodlu, yol tarafından kaldırılan dosya adını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="114fb-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="114fb-190">Depolama için yeni bir rastgele dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="114fb-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="114fb-191">Aşağıdaki kod, dosya adından yolu kaldırır:</span><span class="sxs-lookup"><span data-stu-id="114fb-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="114fb-192">Bu nedenle, şu ana kadar dikkate alınması gereken örnekler aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="114fb-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="114fb-193">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="114fb-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="114fb-194">Güvenlikle ilgili dikkat edilmesi gerekenler</span><span class="sxs-lookup"><span data-stu-id="114fb-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="114fb-195">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="114fb-195">Validation</span></span>](#validation)

<span data-ttu-id="114fb-196">Model bağlama kullanarak dosyaları karşıya yüklerken <xref:Microsoft.AspNetCore.Http.IFormFile> , eylem yöntemi kabul edebilir:</span><span class="sxs-lookup"><span data-stu-id="114fb-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="114fb-197">Tek bir <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="114fb-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="114fb-198">Birkaç dosyayı temsil eden aşağıdaki koleksiyonlardan herhangi biri:</span><span class="sxs-lookup"><span data-stu-id="114fb-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="114fb-199">[Listele](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="114fb-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="114fb-200">Bağlama, form dosyaları adına göre eşleşir.</span><span class="sxs-lookup"><span data-stu-id="114fb-200">Binding matches form files by name.</span></span> <span data-ttu-id="114fb-201">Örneğin, `name` IÇINDEKI HTML değeri `<input type="file" name="formFile">` C# parametresi/özelliği ile ( `FormFile` ) eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="114fb-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="114fb-202">Daha fazla bilgi için, [ad öznitelik DEĞERINI Post yönteminin parametre adına eşleştirin](#match-name-attribute-value-to-parameter-name-of-post-method) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="114fb-203">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="114fb-203">The following example:</span></span>

* <span data-ttu-id="114fb-204">Karşıya yüklenen bir veya daha fazla dosya üzerinden döngü.</span><span class="sxs-lookup"><span data-stu-id="114fb-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="114fb-205">Dosya adı da dahil olmak üzere bir dosyanın tam yolunu döndürmek için [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanır.</span><span class="sxs-lookup"><span data-stu-id="114fb-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="114fb-206">Dosyalar, uygulama tarafından oluşturulan bir dosya adı kullanılarak yerel dosya sistemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="114fb-207">Karşıya yüklenen dosyaların toplam sayısını ve boyutunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="114fb-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="114fb-208">`Path.GetRandomFileName`Yol olmadan bir dosya adı oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="114fb-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="114fb-209">Aşağıdaki örnekte, yol yapılandırmadan alınır:</span><span class="sxs-lookup"><span data-stu-id="114fb-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="114fb-210">Öğesine geçirilen yol, <xref:System.IO.FileStream> *must* dosya adını içermelidir.</span><span class="sxs-lookup"><span data-stu-id="114fb-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="114fb-211">Dosya adı sağlanmazsa, çalışma zamanında bir oluşturulur <xref:System.UnauthorizedAccessException> .</span><span class="sxs-lookup"><span data-stu-id="114fb-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="114fb-212">Tekniği kullanılarak yüklenen dosyalar, <xref:Microsoft.AspNetCore.Http.IFormFile> işlemeden önce sunucuda veya diskte bellek halinde arabelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="114fb-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="114fb-213">Eylem yönteminde, <xref:Microsoft.AspNetCore.Http.IFormFile> içeriğe bir olarak erişilebilir <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="114fb-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="114fb-214">Yerel dosya sistemine ek olarak, dosyalar bir ağ paylaşımında veya [Azure Blob depolama](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)gibi bir dosya depolama hizmetine kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="114fb-215">Karşıya yüklemek için birden çok dosya üzerinde döngü yapan ve güvenli dosya adları kullanan başka bir örnek için, örnek uygulamadaki *Pages/Bufferedmultiplefileuploadfiziksel. cshtml. cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="114fb-216">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) <xref:System.IO.IOException> , önceki geçici dosyaları silmeden 65.535 ' den fazla dosya oluşturulduysa bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="114fb-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="114fb-217">65.535 dosya sınırının sunucu başına sınırı vardır.</span><span class="sxs-lookup"><span data-stu-id="114fb-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="114fb-218">Windows işletim sistemi için bu sınır hakkında daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="114fb-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="114fb-219">GetTempFileNameA işlevi</span><span class="sxs-lookup"><span data-stu-id="114fb-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="114fb-220">Bir veritabanına arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="114fb-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="114fb-221">İkili dosya verilerini [Entity Framework](/ef/core/index)kullanarak bir veritabanında depolamak için, <xref:System.Byte> varlıkta bir dizi özelliği tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="114fb-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="114fb-222">Sınıf için şunu içeren bir sayfa modeli özelliği belirtin <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="114fb-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="114fb-223"><xref:Microsoft.AspNetCore.Http.IFormFile> doğrudan bir eylem yöntemi parametresi veya bir bağlantılı model özelliği olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="114fb-224">Önceki örnekte, bir bağlantılı model özelliği kullanılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="114fb-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="114fb-225">, `FileUpload` Razor Sayfalar formunda kullanılır:</span><span class="sxs-lookup"><span data-stu-id="114fb-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="114fb-226">Form sunucuya gönderildiğinde, öğesini <xref:Microsoft.AspNetCore.Http.IFormFile> bir akışa kopyalayın ve veritabanına bir bayt dizisi olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="114fb-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="114fb-227">Aşağıdaki örnekte, `_dbContext` uygulamanın veritabanı bağlamını depolar:</span><span class="sxs-lookup"><span data-stu-id="114fb-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="114fb-228">Yukarıdaki örnek, örnek uygulamada gösterilen senaryoya benzerdir:</span><span class="sxs-lookup"><span data-stu-id="114fb-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="114fb-229">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="114fb-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="114fb-230">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="114fb-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="114fb-231">İkili verileri ilişkisel veritabanlarında depolarken dikkatli olun, çünkü performansı olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="114fb-232">Doğrulaması olmadan özelliğine güvenmeyin veya bu `FileName` özelliğe güvenmeyin <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="114fb-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="114fb-233">`FileName`Özelliği yalnızca görüntüleme amacıyla ve yalnızca HTML kodlaması sonrasında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="114fb-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="114fb-234">Belirtilen örneklerde dikkate alınması gereken önemli noktalar.</span><span class="sxs-lookup"><span data-stu-id="114fb-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="114fb-235">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="114fb-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="114fb-236">Güvenlikle ilgili dikkat edilmesi gerekenler</span><span class="sxs-lookup"><span data-stu-id="114fb-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="114fb-237">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="114fb-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="114fb-238">Akışa sahip büyük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="114fb-238">Upload large files with streaming</span></span>

<span data-ttu-id="114fb-239">Aşağıdaki örnek, bir denetleyiciyi bir denetleyici eyleminde akışa almak için JavaScript 'in nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="114fb-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="114fb-240">Dosyanın antiforgery belirteci özel bir filtre özniteliği kullanılarak oluşturulur ve istek gövdesi yerine istemci HTTP üst bilgilerine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="114fb-241">Eylem yöntemi karşıya yüklenen verileri doğrudan işlediğinden, form modeli bağlama başka bir özel filtre tarafından devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="114fb-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="114fb-242">Eylem içinde formun içerikleri, `MultipartReader` her bir bireyi okuyan `MultipartSection` , dosyayı işleyen veya içeriği uygun şekilde depolayan bir kullanılarak okunur.</span><span class="sxs-lookup"><span data-stu-id="114fb-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="114fb-243">Çok parçalı bölümler okunduktan sonra eylem kendi model bağlamasını gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="114fb-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="114fb-244">İlk sayfa yanıtı formu yükler ve bir cookie (özniteliği aracılığıyla) bir antiforgery belirtecini kaydeder `GenerateAntiforgeryTokenCookieAttribute` .</span><span class="sxs-lookup"><span data-stu-id="114fb-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="114fb-245">Özniteliği, bir istek belirteci ile ayarlamak için ASP.NET Core yerleşik [antiforgery desteğini](xref:security/anti-request-forgery) kullanır cookie :</span><span class="sxs-lookup"><span data-stu-id="114fb-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="114fb-246">`DisableFormValueModelBindingAttribute`Model bağlamayı devre dışı bırakmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="114fb-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="114fb-247">Örnek uygulamada `GenerateAntiforgeryTokenCookieAttribute` ve `DisableFormValueModelBindingAttribute` sayfa `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor kuralları](xref:razor-pages/razor-pages-conventions)kullanılarak ve içindeki sayfa uygulama modellerine filtre olarak uygulanır:</span><span class="sxs-lookup"><span data-stu-id="114fb-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="114fb-248">Model bağlama formu okumadığından formdan bağlanan parametreler bağlanamaz (sorgu, yol ve başlık çalışmaya devam eder).</span><span class="sxs-lookup"><span data-stu-id="114fb-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="114fb-249">Action yöntemi doğrudan özelliği ile birlikte çalışabilir `Request` .</span><span class="sxs-lookup"><span data-stu-id="114fb-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="114fb-250">`MultipartReader`Her bölümü okumak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="114fb-251">Anahtar/değer verileri bir içinde depolanır `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="114fb-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="114fb-252">Çok parçalı bölümler okunduktan sonra, öğesinin içeriği `KeyValueAccumulator` form verilerini bir model türüne bağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="114fb-253">`StreamingController.UploadDatabase`EF Core bir veritabanına akış için tamamlanan Yöntem:</span><span class="sxs-lookup"><span data-stu-id="114fb-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="114fb-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="114fb-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="114fb-255">`StreamingController.UploadPhysical`Fiziksel bir konuma akışa yönelik tüm Yöntem:</span><span class="sxs-lookup"><span data-stu-id="114fb-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="114fb-256">Örnek uygulamada, doğrulama denetimleri tarafından işlenir `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="114fb-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="114fb-257">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="114fb-257">Validation</span></span>

<span data-ttu-id="114fb-258">Örnek uygulamanın sınıfı, `FileHelpers` arabelleğe alınmış <xref:Microsoft.AspNetCore.Http.IFormFile> ve akış dosya yüklemeleri için birkaç denetim gösterir.</span><span class="sxs-lookup"><span data-stu-id="114fb-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="114fb-259"><xref:Microsoft.AspNetCore.Http.IFormFile>Örnek uygulamada ara belleğe alınmış dosya yüklemelerini işlemek için, `ProcessFormFile` *Utilities/fileyardımcılar. cs* dosyasındaki yöntemine bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="114fb-260">Akış dosyalarını işlemek için `ProcessStreamedFile` aynı dosyadaki yöntemine bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="114fb-261">Örnek uygulamada gösterilen doğrulama işleme yöntemleri karşıya yüklenen dosyaların içeriğini taramaz.</span><span class="sxs-lookup"><span data-stu-id="114fb-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="114fb-262">Çoğu üretim senaryosunda, dosyanın kullanıcılara veya diğer sistemlere kullanılabilir hale getirilmesi için dosya üzerinde bir virüs/kötü amaçlı yazılım tarayıcı API 'SI kullanılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="114fb-263">Konu örneği, doğrulama tekniklerine yönelik çalışan bir örnek sağlasa da, `FileHelpers` aşağıdakileri gerçekleştirmediğiniz takdirde sınıfı bir üretim uygulamasında uygulamaz:</span><span class="sxs-lookup"><span data-stu-id="114fb-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="114fb-264">Uygulamayı tam olarak anlayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="114fb-265">Uygulamayı uygulamanın ortamı ve belirtimleri için uygun şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="114fb-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="114fb-266">**Bu gereksinimleri bilmeden bir uygulamada güvenlik kodunu hiçbir şekilde sayısının fark gözetmeden uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="114fb-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="114fb-267">İçerik doğrulama</span><span class="sxs-lookup"><span data-stu-id="114fb-267">Content validation</span></span>

<span data-ttu-id="114fb-268">**Karşıya yüklenen içerikte üçüncü taraf bir virüs/kötü amaçlı yazılım tarama API 'SI kullanın.**</span><span class="sxs-lookup"><span data-stu-id="114fb-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="114fb-269">Dosyaları tarama, yüksek hacimli senaryolarda sunucu kaynaklarında yoğun bir şekilde yapılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="114fb-270">Dosya tarama nedeniyle istek işleme performansı azaldığında, tarama işini, muhtemelen uygulamanın sunucusundan farklı bir sunucuda çalışan bir [arka plan hizmetine](xref:fundamentals/host/hosted-services)devredere göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="114fb-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="114fb-271">Genellikle, arka plan virüs tarayıcısı tarafından denetlene kadar karşıya yüklenen dosyalar karantinaya alınmış bir alanda tutulur.</span><span class="sxs-lookup"><span data-stu-id="114fb-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="114fb-272">Bir dosya geçtiğinde dosya normal dosya depolama konumuna taşınır.</span><span class="sxs-lookup"><span data-stu-id="114fb-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="114fb-273">Bu adımlar genellikle bir dosyanın tarama durumunu gösteren bir veritabanı kaydıyla birlikte gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="114fb-274">Böyle bir yaklaşım kullanarak, uygulama ve uygulama sunucusu isteklere yanıt vermeye odaklanmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="114fb-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="114fb-275">Dosya Uzantısı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="114fb-275">File extension validation</span></span>

<span data-ttu-id="114fb-276">Karşıya yüklenen dosyanın uzantısı izin verilen uzantılar listesine göre denetlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="114fb-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="114fb-277">Örnek:</span><span class="sxs-lookup"><span data-stu-id="114fb-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="114fb-278">Dosya imzası doğrulaması</span><span class="sxs-lookup"><span data-stu-id="114fb-278">File signature validation</span></span>

<span data-ttu-id="114fb-279">Bir dosyanın imzası, bir dosyanın başlangıcında ilk birkaç bayta göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="114fb-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="114fb-280">Bu baytlar, uzantının dosyanın içeriğiyle eşleşip eşleşmediğini göstermek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="114fb-281">Örnek uygulama, birkaç ortak dosya türü için dosya imzalarını denetler.</span><span class="sxs-lookup"><span data-stu-id="114fb-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="114fb-282">Aşağıdaki örnekte, bir JPEG görüntüsünün dosya imzası, dosyaya karşı denetlenir:</span><span class="sxs-lookup"><span data-stu-id="114fb-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="114fb-283">Ek dosya imzaları almak için [Dosya Imzaları veritabanı](https://www.filesignatures.net/) ve resmi dosya belirtimleri bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="114fb-284">Dosya adı güvenliği</span><span class="sxs-lookup"><span data-stu-id="114fb-284">File name security</span></span>

<span data-ttu-id="114fb-285">Fiziksel depolamaya bir dosyayı kaydetmek için hiçbir şekilde istemci tarafından sağlanan dosya adı kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="114fb-286">Geçici depolama için tam yol (dosya adı da dahil olmak üzere) oluşturmak için [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) veya [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanarak dosya için güvenli bir dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="114fb-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="114fb-287">Razor Otomatik HTML, görüntüleme için özellik değerlerini kodluyor.</span><span class="sxs-lookup"><span data-stu-id="114fb-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="114fb-288">Aşağıdaki kodun kullanımı güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="114fb-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="114fb-289">Dışında Razor , her zaman <xref:System.Net.WebUtility.HtmlEncode*> bir kullanıcının isteğinden dosya adı içeriği.</span><span class="sxs-lookup"><span data-stu-id="114fb-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="114fb-290">Birçok uygulama, dosyanın var olduğunu bir denetim içermelidir; Aksi takdirde, dosyanın üzerine aynı ada sahip bir dosya yazılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="114fb-291">Uygulamanızın belirtimlerini karşılamak için ek mantık sağlayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="114fb-292">Boyut doğrulaması</span><span class="sxs-lookup"><span data-stu-id="114fb-292">Size validation</span></span>

<span data-ttu-id="114fb-293">Karşıya yüklenen dosyaların boyutunu sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="114fb-294">Örnek uygulamada, dosyanın boyutu 2 MB ile sınırlıdır (bayt cinsinden gösterilir).</span><span class="sxs-lookup"><span data-stu-id="114fb-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="114fb-295">Sınır, dosyadaki *appsettings.js* [yapılandırma](xref:fundamentals/configuration/index) yoluyla sağlanır:</span><span class="sxs-lookup"><span data-stu-id="114fb-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="114fb-296">, `FileSizeLimit` `PageModel` Sınıflara eklenmiş:</span><span class="sxs-lookup"><span data-stu-id="114fb-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="114fb-297">Dosya boyutu sınırı aştığında, dosya reddedilir:</span><span class="sxs-lookup"><span data-stu-id="114fb-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="114fb-298">Name öznitelik değerini POST yönteminin Parameter adı ile Eşleştir</span><span class="sxs-lookup"><span data-stu-id="114fb-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="114fb-299">RazorForm verileri oluşturan veya JavaScript 'in `FormData` doğrudan kullandığı, form öğesinde belirtilen adın veya `FormData` denetleyicinin eyleminde parametrenin adıyla eşleşmesi gereken form olmayan formlar.</span><span class="sxs-lookup"><span data-stu-id="114fb-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="114fb-300">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="114fb-300">In the following example:</span></span>

* <span data-ttu-id="114fb-301">Bir öğesi kullanılırken `<input>` , `name` özniteliği değere ayarlanır `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="114fb-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="114fb-302">`FormData`JavaScript içinde kullanırken, ad değerine ayarlanır `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="114fb-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="114fb-303">C# yönteminin () parametresi için eşleşen bir ad kullanın `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="114fb-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="114fb-304">Bir Razor sayfalar sayfa işleyici yöntemi için şunu `Upload` adlı:</span><span class="sxs-lookup"><span data-stu-id="114fb-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="114fb-305">MVC POST denetleyicisi eylem yöntemi için:</span><span class="sxs-lookup"><span data-stu-id="114fb-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="114fb-306">Sunucu ve uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="114fb-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="114fb-307">Çok parçalı gövde uzunluğu sınırı</span><span class="sxs-lookup"><span data-stu-id="114fb-307">Multipart body length limit</span></span>

<span data-ttu-id="114fb-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Her bir çok parçalı gövdenin uzunluk sınırını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="114fb-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="114fb-309">Bu sınırı aşan form bölümleri <xref:System.IO.InvalidDataException> ayrıştırıldığında bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="114fb-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="114fb-310">Varsayılan değer 134.217.728 ' dir (128 MB).</span><span class="sxs-lookup"><span data-stu-id="114fb-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="114fb-311">Sınırı, <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> içindeki ayarı kullanarak özelleştirin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="114fb-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="114fb-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>tek sayfa veya eylem için ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="114fb-313">Bir Razor Sayfalar uygulamasında, filtresi içindeki bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="114fb-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
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

<span data-ttu-id="114fb-314">Bir Razor Sayfalar uygulamasında veya BIR MVC uygulamasında, filtreyi sayfa modeline veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="114fb-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="114fb-315">Kestrel maksimum istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="114fb-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="114fb-316">Kestrel tarafından barındırılan uygulamalar için, varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu, yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="114fb-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="114fb-317">[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel Server seçeneğini kullanarak sınırı özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="114fb-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="114fb-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> , tek sayfa veya eylem için [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) ayarlamak üzere kullanılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="114fb-319">Bir Razor Sayfalar uygulamasında, filtresi içindeki bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="114fb-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
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

<span data-ttu-id="114fb-320">Bir Razor Sayfalar uygulamasında veya BIR MVC uygulamasında, filtreyi sayfa işleyici sınıfına veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="114fb-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="114fb-321">, `RequestSizeLimitAttribute` Yönergesi kullanılarak da uygulanabilir [`@attribute`](xref:mvc/views/razor#attribute) Razor :</span><span class="sxs-lookup"><span data-stu-id="114fb-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="114fb-322">Diğer Kestrel limitleri</span><span class="sxs-lookup"><span data-stu-id="114fb-322">Other Kestrel limits</span></span>

<span data-ttu-id="114fb-323">Kestrel tarafından barındırılan uygulamalar için diğer Kestrel limitleri de uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="114fb-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="114fb-324">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="114fb-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="114fb-325">İstek ve yanıt veri ücretleri</span><span class="sxs-lookup"><span data-stu-id="114fb-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="114fb-326">IIS içerik uzunluğu sınırı</span><span class="sxs-lookup"><span data-stu-id="114fb-326">IIS content length limit</span></span>

<span data-ttu-id="114fb-327">Varsayılan istek sınırı ( `maxAllowedContentLength` ), yaklaşık 28.6 MB olan 30.000.000 bayttır.</span><span class="sxs-lookup"><span data-stu-id="114fb-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="114fb-328">*web.config* dosyasında sınırı özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="114fb-328">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="114fb-329">Bu ayar yalnızca IIS için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="114fb-329">This setting only applies to IIS.</span></span> <span data-ttu-id="114fb-330">Kestrel üzerinde barındırırken davranış varsayılan olarak gerçekleşmez.</span><span class="sxs-lookup"><span data-stu-id="114fb-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="114fb-331">Daha fazla bilgi için bkz. [Istek \<requestLimits> sınırları ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="114fb-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="114fb-332">ASP.NET Core modülündeki sınırlamalar veya IIS Istek filtreleme modülünün varlığı, karşıya yüklemeleri 2 veya 4 GB ile sınırlandırabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="114fb-333">Daha fazla bilgi için bkz. [2 GB 'tan büyük dosya karşıya yüklenemiyor (DotNet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="114fb-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="114fb-334">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="114fb-334">Troubleshoot</span></span>

<span data-ttu-id="114fb-335">Dosyaları karşıya yükleme ve olası çözümleri ile çalışırken karşılaşılan bazı yaygın sorunlar aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="114fb-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="114fb-336">Bir IIS sunucusuna dağıtılırken bulunamadı hatası</span><span class="sxs-lookup"><span data-stu-id="114fb-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="114fb-337">Aşağıdaki hata karşıya yüklenen dosyanın, sunucunun yapılandırılmış içerik uzunluğunu aştığını gösterir:</span><span class="sxs-lookup"><span data-stu-id="114fb-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="114fb-338">Limiti artırma hakkında daha fazla bilgi için bkz. [IIS içerik uzunluğu sınırı](#iis-content-length-limit) bölümü.</span><span class="sxs-lookup"><span data-stu-id="114fb-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="114fb-339">Bağlantı hatası</span><span class="sxs-lookup"><span data-stu-id="114fb-339">Connection failure</span></span>

<span data-ttu-id="114fb-340">Bir bağlantı hatası ve bir sıfırlama sunucu bağlantısı büyük olasılıkla karşıya yüklenen dosyanın Kestrel 'in en büyük istek gövdesi boyutunu aştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="114fb-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="114fb-341">Daha fazla bilgi için, [Kestrel maksimum istek gövdesi boyutu](#kestrel-maximum-request-body-size) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="114fb-342">Kestrel istemci bağlantı limitleri de ayarlama gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="114fb-343">Iformfile ile null başvuru özel durumu</span><span class="sxs-lookup"><span data-stu-id="114fb-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="114fb-344">Denetleyici karşıya yüklenen dosyaları kullanarak kabul edilirse, <xref:Microsoft.AspNetCore.Http.IFormFile> ancak değer ise `null` , HTML formunun bir değerini belirtmesini onaylayın `enctype` `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="114fb-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="114fb-345">Bu öznitelik `<form>` öğesinde ayarlanmamışsa, dosya karşıya yükleme gerçekleşmez ve herhangi bir bağlı <xref:Microsoft.AspNetCore.Http.IFormFile> bağımsız değişken `null` .</span><span class="sxs-lookup"><span data-stu-id="114fb-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="114fb-346">Ayrıca, [form verilerinde karşıya yükleme adlandırmasının uygulamanın adlandırmayla eşleştiğinden](#match-name-attribute-value-to-parameter-name-of-post-method)emin olun.</span><span class="sxs-lookup"><span data-stu-id="114fb-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="114fb-347">Akış çok uzun</span><span class="sxs-lookup"><span data-stu-id="114fb-347">Stream was too long</span></span>

<span data-ttu-id="114fb-348">Bu konudaki örnekler <xref:System.IO.MemoryStream> karşıya yüklenen dosyanın içeriğini tutmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="114fb-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="114fb-349">Bir öğesinin boyut sınırı `MemoryStream` `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="114fb-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="114fb-350">Uygulamanın dosya yükleme senaryosu, dosya içeriğinin 50 MB 'tan büyük olmasını gerektiriyorsa, `MemoryStream` karşıya yüklenen bir dosyanın içeriğini tutmak için tek başına olmayan alternatif bir yaklaşım kullanın.</span><span class="sxs-lookup"><span data-stu-id="114fb-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="114fb-351">ASP.NET Core, daha küçük dosyalar için arabellekli model bağlama ve daha büyük dosyalar için arabelleğe alınmamış akış kullanarak bir veya daha fazla dosyanın yüklenmesini destekler</span><span class="sxs-lookup"><span data-stu-id="114fb-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="114fb-352">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="114fb-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="114fb-353">Güvenlikle ilgili dikkat edilmesi gerekenler</span><span class="sxs-lookup"><span data-stu-id="114fb-353">Security considerations</span></span>

<span data-ttu-id="114fb-354">Kullanıcılara bir sunucuya dosya yükleme yeteneği sağlarken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="114fb-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="114fb-355">Saldırganlar şunları deneyebilir:</span><span class="sxs-lookup"><span data-stu-id="114fb-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="114fb-356">[Hizmet reddi](/windows-hardware/drivers/ifs/denial-of-service) saldırıları yürütün.</span><span class="sxs-lookup"><span data-stu-id="114fb-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="114fb-357">Virüsleri veya kötü amaçlı yazılımları karşıya yükleyin.</span><span class="sxs-lookup"><span data-stu-id="114fb-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="114fb-358">Ağları ve sunucuları diğer yollarla tehlikeye atabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="114fb-359">Başarılı bir saldırının olasılığını azaltan güvenlik adımları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="114fb-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="114fb-360">Dosyaları, tercihen sistem dışı bir sürücüye, özel bir dosya yükleme alanına yükleyin.</span><span class="sxs-lookup"><span data-stu-id="114fb-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="114fb-361">Ayrılmış bir konum, karşıya yüklenen dosyalar üzerinde güvenlik kısıtlamaları yapmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="114fb-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="114fb-362">Dosya yükleme konumunda yürütme izinlerini devre dışı bırakın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="114fb-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="114fb-363">Karşıya yüklenen dosyaları uygulamayla aynı dizin **ağacında kalıcı hale** getirme.&dagger;</span><span class="sxs-lookup"><span data-stu-id="114fb-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="114fb-364">Uygulama tarafından belirlenen bir güvenli dosya adı kullanın.</span><span class="sxs-lookup"><span data-stu-id="114fb-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="114fb-365">Kullanıcı tarafından belirtilen bir dosya adı veya karşıya yüklenen dosyanın güvenilmeyen dosya adı kullanmayın. &dagger; HTML, görüntüleme sırasında güvenilmeyen dosya adını kodlayamıyor.</span><span class="sxs-lookup"><span data-stu-id="114fb-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="114fb-366">Örneğin, dosya adını günlüğe kaydetme veya Kullanıcı arabiriminde görüntüleme ( Razor otomatik olarak HTML kodlama çıktısı).</span><span class="sxs-lookup"><span data-stu-id="114fb-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="114fb-367">Uygulamanın tasarım belirtimi için yalnızca onaylanan dosya uzantılarına izin verin.&dagger;</span><span class="sxs-lookup"><span data-stu-id="114fb-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="114fb-368">Sunucuda istemci tarafı denetimlerinin gerçekleştirildiğinden emin olun. &dagger; İstemci tarafı denetimleri kolayca atmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="114fb-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="114fb-369">Karşıya yüklenen dosyanın boyutunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="114fb-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="114fb-370">Büyük karşıya yüklemeleri engellemek için en büyük boyut sınırını ayarlayın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="114fb-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="114fb-371">Aynı ada sahip karşıya yüklenen bir dosya tarafından dosyaların üzerine yazılmaması gerektiğinde, dosyayı karşıya yüklemeden önce dosya adını veritabanına veya fiziksel depolamaya göre denetleyin.</span><span class="sxs-lookup"><span data-stu-id="114fb-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="114fb-372">**Dosya depolanmadan önce karşıya yüklenen içerik üzerinde bir virüs/kötü amaçlı yazılım tarayıcısı çalıştırın.**</span><span class="sxs-lookup"><span data-stu-id="114fb-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="114fb-373">&dagger;Örnek uygulama, ölçütlere uyan bir yaklaşımı gösterir.</span><span class="sxs-lookup"><span data-stu-id="114fb-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="114fb-374">Kötü amaçlı kodun bir sisteme yüklenmesi genellikle şu şekilde kod yürütmenin ilk adımıdır:</span><span class="sxs-lookup"><span data-stu-id="114fb-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="114fb-375">Sistemin denetimini tamamen elde edin.</span><span class="sxs-lookup"><span data-stu-id="114fb-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="114fb-376">Sistemin kilitlenme sonucuyla bir sistemi aşırı yükleme.</span><span class="sxs-lookup"><span data-stu-id="114fb-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="114fb-377">Kullanıcı veya Sistem verilerinin güvenliğini tehlikeye atabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="114fb-378">Genel Kullanıcı arabirimine Graffiti uygulayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="114fb-379">Kullanıcılardan dosya kabul edilirken saldırı yüzeyi alanını azaltma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="114fb-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="114fb-380">Kısıtlanmamış dosya yükleme</span><span class="sxs-lookup"><span data-stu-id="114fb-380">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="114fb-381">Azure güvenliği: kullanıcılardan dosya kabul edilirken uygun denetimlerin yerinde olduğundan emin olun</span><span class="sxs-lookup"><span data-stu-id="114fb-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="114fb-382">Örnek uygulamadaki örnekler de dahil olmak üzere güvenlik önlemlerini uygulama hakkında daha fazla bilgi için [doğrulama](#validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="114fb-383">Depolama senaryoları</span><span class="sxs-lookup"><span data-stu-id="114fb-383">Storage scenarios</span></span>

<span data-ttu-id="114fb-384">Dosyalar için ortak depolama seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="114fb-384">Common storage options for files include:</span></span>

* <span data-ttu-id="114fb-385">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="114fb-385">Database</span></span>

  * <span data-ttu-id="114fb-386">Küçük dosya yüklemeleri için, bir veritabanı genellikle fiziksel depolama (dosya sistemi veya ağ paylaşımından) seçeneklerinden daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="114fb-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="114fb-387">Kullanıcı verileri için bir veritabanı kaydının alınması eşzamanlı olarak dosya içeriğini (örneğin, avatar görüntüsü) sağlayabildiğinden, veritabanı genellikle fiziksel depolama seçeneklerine göre daha uygundur.</span><span class="sxs-lookup"><span data-stu-id="114fb-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="114fb-388">Bir veritabanı, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="114fb-389">Fiziksel depolama (dosya sistemi veya ağ paylaşma)</span><span class="sxs-lookup"><span data-stu-id="114fb-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="114fb-390">Büyük dosya yüklemeleri için:</span><span class="sxs-lookup"><span data-stu-id="114fb-390">For large file uploads:</span></span>
    * <span data-ttu-id="114fb-391">Veritabanı limitleri karşıya yükleme boyutunu kısıtlayabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="114fb-392">Fiziksel depolama genellikle bir veritabanındaki depolamadan daha az ekonomik olur.</span><span class="sxs-lookup"><span data-stu-id="114fb-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="114fb-393">Fiziksel depolama, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="114fb-394">Uygulamanın işlemi, depolama konumu için okuma ve yazma izinlerine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="114fb-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="114fb-395">**Hiçbir zaman yürütme izni vermeyin.**</span><span class="sxs-lookup"><span data-stu-id="114fb-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="114fb-396">Veri depolama hizmeti (örneğin, [Azure Blob depolama](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="114fb-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="114fb-397">Hizmetler genellikle tek hata noktalarına tabi olan şirket içi çözümler üzerinde geliştirilmiş ölçeklenebilirlik ve esneklik sunar.</span><span class="sxs-lookup"><span data-stu-id="114fb-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="114fb-398">Hizmetler büyük depolama altyapısı senaryolarında düşük maliyetlidir.</span><span class="sxs-lookup"><span data-stu-id="114fb-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="114fb-399">Daha fazla bilgi için bkz. [hızlı başlangıç: nesne depolamada blob oluşturmak için .NET kullanma](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="114fb-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="114fb-400">Konu başlığı altında <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> <xref:System.IO.FileStream> ile çalışırken bir BLOB depolama alanına kaydetmek için kullanılabilir <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="114fb-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="114fb-401">Karşıya dosya yükleme senaryoları</span><span class="sxs-lookup"><span data-stu-id="114fb-401">File upload scenarios</span></span>

<span data-ttu-id="114fb-402">Dosyaları karşıya yüklemek için iki genel yaklaşım arabelleğe alınır ve akışlardır.</span><span class="sxs-lookup"><span data-stu-id="114fb-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="114fb-403">**Ara**</span><span class="sxs-lookup"><span data-stu-id="114fb-403">**Buffering**</span></span>

<span data-ttu-id="114fb-404">Dosyanın tamamı <xref:Microsoft.AspNetCore.Http.IFormFile> , dosyayı işlemek veya kaydetmek için kullanılan dosyanın C# temsili olan öğesine okunurdur.</span><span class="sxs-lookup"><span data-stu-id="114fb-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="114fb-405">Dosya karşıya yüklemeleri tarafından kullanılan kaynaklar (disk, bellek), eşzamanlı dosya karşıya yüklemelerinin sayısına ve boyutuna bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="114fb-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="114fb-406">Bir uygulama çok fazla karşıya yükleme arabelleğini denerse, bellek veya disk alanı tükenirse site çöker.</span><span class="sxs-lookup"><span data-stu-id="114fb-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="114fb-407">Karşıya dosya yükleme boyutu veya sıklığı uygulama kaynaklarını tüketme ise, akış ' ı kullanın.</span><span class="sxs-lookup"><span data-stu-id="114fb-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="114fb-408">64 KB geçen tek bir arabelleğe alınmış dosya, bellekten diskte geçici bir dosyaya taşınır.</span><span class="sxs-lookup"><span data-stu-id="114fb-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="114fb-409">Dosya arabelleğe alma, bu konunun aşağıdaki bölümlerinde ele alınmıştır:</span><span class="sxs-lookup"><span data-stu-id="114fb-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="114fb-410">Fiziksel depolama alanı</span><span class="sxs-lookup"><span data-stu-id="114fb-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="114fb-411">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="114fb-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="114fb-412">**Akışlar**</span><span class="sxs-lookup"><span data-stu-id="114fb-412">**Streaming**</span></span>

<span data-ttu-id="114fb-413">Dosya çok parçalı bir istekten alınır ve doğrudan uygulama tarafından işlenir veya kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="114fb-414">Akış performansı önemli ölçüde iyileştirmez.</span><span class="sxs-lookup"><span data-stu-id="114fb-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="114fb-415">Akış, dosya karşıya yüklenirken bellek veya disk alanı taleplerini azaltır.</span><span class="sxs-lookup"><span data-stu-id="114fb-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="114fb-416">Akış büyük dosyaları [akış ile büyük dosyaları karşıya yükle](#upload-large-files-with-streaming) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="114fb-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="114fb-417">Fiziksel depolamaya arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="114fb-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="114fb-418">Küçük dosyaları karşıya yüklemek için, çok parçalı bir form kullanın veya JavaScript kullanarak bir POST isteği oluşturun.</span><span class="sxs-lookup"><span data-stu-id="114fb-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="114fb-419">Aşağıdaki örnek, Razor tek bir dosyayı karşıya yüklemek için sayfalar formunun kullanımını gösterir (örnek uygulamada*Pages/Bufferedsinglefileuploadfiziksel. cshtml* ):</span><span class="sxs-lookup"><span data-stu-id="114fb-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="114fb-420">Aşağıdaki örnek, önceki örneğe benzerdir, ancak şunları hariç:</span><span class="sxs-lookup"><span data-stu-id="114fb-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="114fb-421">Form verilerini göndermek için JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="114fb-422">Doğrulama yok.</span><span class="sxs-lookup"><span data-stu-id="114fb-422">There's no validation.</span></span>

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

<span data-ttu-id="114fb-423">[Fetch API 'sini desteklemeyen](https://caniuse.com/#feat=fetch)istemcilerde form gönderisini JavaScript 'te gerçekleştirmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="114fb-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="114fb-424">Fetch Polyfill kullanın (örneğin, [Window. Fetch (GitHub/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="114fb-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="114fb-425">`XMLHttpRequest` komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="114fb-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="114fb-426">Örnek:</span><span class="sxs-lookup"><span data-stu-id="114fb-426">For example:</span></span>

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

<span data-ttu-id="114fb-427">Dosya yüklemelerini desteklemek için, HTML formları bir kodlama türü ( `enctype` ) belirtmelidir `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="114fb-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="114fb-428">`files`Birden çok dosyayı karşıya yüklemeyi destekleyen bir giriş öğesi için, `multiple` öğesine özniteliği sağlar `<input>` :</span><span class="sxs-lookup"><span data-stu-id="114fb-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="114fb-429">Sunucuya yüklenen tek dosyalara, kullanılarak [model bağlama](xref:mvc/models/model-binding) yoluyla erişilebilir <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="114fb-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="114fb-430">Örnek uygulama, veritabanı ve fiziksel depolama senaryoları için birden çok arabellekli dosya yüklemeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="114fb-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="114fb-431">**not** `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> Görüntüleme ve günlüğe kaydetme için dışındaki özelliğini kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="114fb-432">Görüntüleme veya günlüğe kaydetme sırasında, HTML dosya adını kodlayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="114fb-433">Saldırgan, tam yollar veya göreli yollar dahil olmak üzere kötü amaçlı bir dosya adı sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="114fb-434">Uygulamalar:</span><span class="sxs-lookup"><span data-stu-id="114fb-434">Applications should:</span></span>
>
> * <span data-ttu-id="114fb-435">Kullanıcı tarafından sağlanan dosya adının yolunu kaldırın.</span><span class="sxs-lookup"><span data-stu-id="114fb-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="114fb-436">Kullanıcı arabirimi veya günlüğe kaydetme için HTML kodlu, yol tarafından kaldırılan dosya adını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="114fb-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="114fb-437">Depolama için yeni bir rastgele dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="114fb-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="114fb-438">Aşağıdaki kod, dosya adından yolu kaldırır:</span><span class="sxs-lookup"><span data-stu-id="114fb-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="114fb-439">Bu nedenle, şu ana kadar dikkate alınması gereken örnekler aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="114fb-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="114fb-440">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="114fb-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="114fb-441">Güvenlikle ilgili dikkat edilmesi gerekenler</span><span class="sxs-lookup"><span data-stu-id="114fb-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="114fb-442">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="114fb-442">Validation</span></span>](#validation)

<span data-ttu-id="114fb-443">Model bağlama kullanarak dosyaları karşıya yüklerken <xref:Microsoft.AspNetCore.Http.IFormFile> , eylem yöntemi kabul edebilir:</span><span class="sxs-lookup"><span data-stu-id="114fb-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="114fb-444">Tek bir <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="114fb-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="114fb-445">Birkaç dosyayı temsil eden aşağıdaki koleksiyonlardan herhangi biri:</span><span class="sxs-lookup"><span data-stu-id="114fb-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="114fb-446">[Listele](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="114fb-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="114fb-447">Bağlama, form dosyaları adına göre eşleşir.</span><span class="sxs-lookup"><span data-stu-id="114fb-447">Binding matches form files by name.</span></span> <span data-ttu-id="114fb-448">Örneğin, `name` IÇINDEKI HTML değeri `<input type="file" name="formFile">` C# parametresi/özelliği ile ( `FormFile` ) eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="114fb-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="114fb-449">Daha fazla bilgi için, [ad öznitelik DEĞERINI Post yönteminin parametre adına eşleştirin](#match-name-attribute-value-to-parameter-name-of-post-method) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="114fb-450">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="114fb-450">The following example:</span></span>

* <span data-ttu-id="114fb-451">Karşıya yüklenen bir veya daha fazla dosya üzerinden döngü.</span><span class="sxs-lookup"><span data-stu-id="114fb-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="114fb-452">Dosya adı da dahil olmak üzere bir dosyanın tam yolunu döndürmek için [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanır.</span><span class="sxs-lookup"><span data-stu-id="114fb-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="114fb-453">Dosyalar, uygulama tarafından oluşturulan bir dosya adı kullanılarak yerel dosya sistemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="114fb-454">Karşıya yüklenen dosyaların toplam sayısını ve boyutunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="114fb-454">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="114fb-455">`Path.GetRandomFileName`Yol olmadan bir dosya adı oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="114fb-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="114fb-456">Aşağıdaki örnekte, yol yapılandırmadan alınır:</span><span class="sxs-lookup"><span data-stu-id="114fb-456">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="114fb-457">Öğesine geçirilen yol, <xref:System.IO.FileStream> *must* dosya adını içermelidir.</span><span class="sxs-lookup"><span data-stu-id="114fb-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="114fb-458">Dosya adı sağlanmazsa, çalışma zamanında bir oluşturulur <xref:System.UnauthorizedAccessException> .</span><span class="sxs-lookup"><span data-stu-id="114fb-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="114fb-459">Tekniği kullanılarak yüklenen dosyalar, <xref:Microsoft.AspNetCore.Http.IFormFile> işlemeden önce sunucuda veya diskte bellek halinde arabelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="114fb-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="114fb-460">Eylem yönteminde, <xref:Microsoft.AspNetCore.Http.IFormFile> içeriğe bir olarak erişilebilir <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="114fb-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="114fb-461">Yerel dosya sistemine ek olarak, dosyalar bir ağ paylaşımında veya [Azure Blob depolama](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)gibi bir dosya depolama hizmetine kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="114fb-462">Karşıya yüklemek için birden çok dosya üzerinde döngü yapan ve güvenli dosya adları kullanan başka bir örnek için, örnek uygulamadaki *Pages/Bufferedmultiplefileuploadfiziksel. cshtml. cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="114fb-463">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) <xref:System.IO.IOException> , önceki geçici dosyaları silmeden 65.535 ' den fazla dosya oluşturulduysa bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="114fb-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="114fb-464">65.535 dosya sınırının sunucu başına sınırı vardır.</span><span class="sxs-lookup"><span data-stu-id="114fb-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="114fb-465">Windows işletim sistemi için bu sınır hakkında daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="114fb-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="114fb-466">GetTempFileNameA işlevi</span><span class="sxs-lookup"><span data-stu-id="114fb-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="114fb-467">Bir veritabanına arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="114fb-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="114fb-468">İkili dosya verilerini [Entity Framework](/ef/core/index)kullanarak bir veritabanında depolamak için, <xref:System.Byte> varlıkta bir dizi özelliği tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="114fb-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="114fb-469">Sınıf için şunu içeren bir sayfa modeli özelliği belirtin <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="114fb-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="114fb-470"><xref:Microsoft.AspNetCore.Http.IFormFile> doğrudan bir eylem yöntemi parametresi veya bir bağlantılı model özelliği olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="114fb-471">Önceki örnekte, bir bağlantılı model özelliği kullanılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="114fb-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="114fb-472">, `FileUpload` Razor Sayfalar formunda kullanılır:</span><span class="sxs-lookup"><span data-stu-id="114fb-472">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="114fb-473">Form sunucuya gönderildiğinde, öğesini <xref:Microsoft.AspNetCore.Http.IFormFile> bir akışa kopyalayın ve veritabanına bir bayt dizisi olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="114fb-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="114fb-474">Aşağıdaki örnekte, `_dbContext` uygulamanın veritabanı bağlamını depolar:</span><span class="sxs-lookup"><span data-stu-id="114fb-474">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="114fb-475">Yukarıdaki örnek, örnek uygulamada gösterilen senaryoya benzerdir:</span><span class="sxs-lookup"><span data-stu-id="114fb-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="114fb-476">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="114fb-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="114fb-477">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="114fb-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="114fb-478">İkili verileri ilişkisel veritabanlarında depolarken dikkatli olun, çünkü performansı olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="114fb-479">Doğrulaması olmadan özelliğine güvenmeyin veya bu `FileName` özelliğe güvenmeyin <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="114fb-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="114fb-480">`FileName`Özelliği yalnızca görüntüleme amacıyla ve yalnızca HTML kodlaması sonrasında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="114fb-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="114fb-481">Belirtilen örneklerde dikkate alınması gereken önemli noktalar.</span><span class="sxs-lookup"><span data-stu-id="114fb-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="114fb-482">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="114fb-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="114fb-483">Güvenlikle ilgili dikkat edilmesi gerekenler</span><span class="sxs-lookup"><span data-stu-id="114fb-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="114fb-484">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="114fb-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="114fb-485">Akışa sahip büyük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="114fb-485">Upload large files with streaming</span></span>

<span data-ttu-id="114fb-486">Aşağıdaki örnek, bir denetleyiciyi bir denetleyici eyleminde akışa almak için JavaScript 'in nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="114fb-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="114fb-487">Dosyanın antiforgery belirteci özel bir filtre özniteliği kullanılarak oluşturulur ve istek gövdesi yerine istemci HTTP üst bilgilerine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="114fb-488">Eylem yöntemi karşıya yüklenen verileri doğrudan işlediğinden, form modeli bağlama başka bir özel filtre tarafından devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="114fb-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="114fb-489">Eylem içinde formun içerikleri, `MultipartReader` her bir bireyi okuyan `MultipartSection` , dosyayı işleyen veya içeriği uygun şekilde depolayan bir kullanılarak okunur.</span><span class="sxs-lookup"><span data-stu-id="114fb-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="114fb-490">Çok parçalı bölümler okunduktan sonra eylem kendi model bağlamasını gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="114fb-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="114fb-491">İlk sayfa yanıtı formu yükler ve bir cookie (özniteliği aracılığıyla) bir antiforgery belirtecini kaydeder `GenerateAntiforgeryTokenCookieAttribute` .</span><span class="sxs-lookup"><span data-stu-id="114fb-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="114fb-492">Özniteliği, bir istek belirteci ile ayarlamak için ASP.NET Core yerleşik [antiforgery desteğini](xref:security/anti-request-forgery) kullanır cookie :</span><span class="sxs-lookup"><span data-stu-id="114fb-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="114fb-493">`DisableFormValueModelBindingAttribute`Model bağlamayı devre dışı bırakmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="114fb-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="114fb-494">Örnek uygulamada `GenerateAntiforgeryTokenCookieAttribute` ve `DisableFormValueModelBindingAttribute` sayfa `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor kuralları](xref:razor-pages/razor-pages-conventions)kullanılarak ve içindeki sayfa uygulama modellerine filtre olarak uygulanır:</span><span class="sxs-lookup"><span data-stu-id="114fb-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="114fb-495">Model bağlama formu okumadığından formdan bağlanan parametreler bağlanamaz (sorgu, yol ve başlık çalışmaya devam eder).</span><span class="sxs-lookup"><span data-stu-id="114fb-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="114fb-496">Action yöntemi doğrudan özelliği ile birlikte çalışabilir `Request` .</span><span class="sxs-lookup"><span data-stu-id="114fb-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="114fb-497">`MultipartReader`Her bölümü okumak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="114fb-498">Anahtar/değer verileri bir içinde depolanır `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="114fb-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="114fb-499">Çok parçalı bölümler okunduktan sonra, öğesinin içeriği `KeyValueAccumulator` form verilerini bir model türüne bağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="114fb-500">`StreamingController.UploadDatabase`EF Core bir veritabanına akış için tamamlanan Yöntem:</span><span class="sxs-lookup"><span data-stu-id="114fb-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="114fb-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="114fb-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="114fb-502">`StreamingController.UploadPhysical`Fiziksel bir konuma akışa yönelik tüm Yöntem:</span><span class="sxs-lookup"><span data-stu-id="114fb-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="114fb-503">Örnek uygulamada, doğrulama denetimleri tarafından işlenir `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="114fb-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="114fb-504">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="114fb-504">Validation</span></span>

<span data-ttu-id="114fb-505">Örnek uygulamanın sınıfı, `FileHelpers` arabelleğe alınmış <xref:Microsoft.AspNetCore.Http.IFormFile> ve akış dosya yüklemeleri için birkaç denetim gösterir.</span><span class="sxs-lookup"><span data-stu-id="114fb-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="114fb-506"><xref:Microsoft.AspNetCore.Http.IFormFile>Örnek uygulamada ara belleğe alınmış dosya yüklemelerini işlemek için, `ProcessFormFile` *Utilities/fileyardımcılar. cs* dosyasındaki yöntemine bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="114fb-507">Akış dosyalarını işlemek için `ProcessStreamedFile` aynı dosyadaki yöntemine bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="114fb-508">Örnek uygulamada gösterilen doğrulama işleme yöntemleri karşıya yüklenen dosyaların içeriğini taramaz.</span><span class="sxs-lookup"><span data-stu-id="114fb-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="114fb-509">Çoğu üretim senaryosunda, dosyanın kullanıcılara veya diğer sistemlere kullanılabilir hale getirilmesi için dosya üzerinde bir virüs/kötü amaçlı yazılım tarayıcı API 'SI kullanılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="114fb-510">Konu örneği, doğrulama tekniklerine yönelik çalışan bir örnek sağlasa da, `FileHelpers` aşağıdakileri gerçekleştirmediğiniz takdirde sınıfı bir üretim uygulamasında uygulamaz:</span><span class="sxs-lookup"><span data-stu-id="114fb-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="114fb-511">Uygulamayı tam olarak anlayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="114fb-512">Uygulamayı uygulamanın ortamı ve belirtimleri için uygun şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="114fb-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="114fb-513">**Bu gereksinimleri bilmeden bir uygulamada güvenlik kodunu hiçbir şekilde sayısının fark gözetmeden uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="114fb-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="114fb-514">İçerik doğrulama</span><span class="sxs-lookup"><span data-stu-id="114fb-514">Content validation</span></span>

<span data-ttu-id="114fb-515">**Karşıya yüklenen içerikte üçüncü taraf bir virüs/kötü amaçlı yazılım tarama API 'SI kullanın.**</span><span class="sxs-lookup"><span data-stu-id="114fb-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="114fb-516">Dosyaları tarama, yüksek hacimli senaryolarda sunucu kaynaklarında yoğun bir şekilde yapılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="114fb-517">Dosya tarama nedeniyle istek işleme performansı azaldığında, tarama işini, muhtemelen uygulamanın sunucusundan farklı bir sunucuda çalışan bir [arka plan hizmetine](xref:fundamentals/host/hosted-services)devredere göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="114fb-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="114fb-518">Genellikle, arka plan virüs tarayıcısı tarafından denetlene kadar karşıya yüklenen dosyalar karantinaya alınmış bir alanda tutulur.</span><span class="sxs-lookup"><span data-stu-id="114fb-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="114fb-519">Bir dosya geçtiğinde dosya normal dosya depolama konumuna taşınır.</span><span class="sxs-lookup"><span data-stu-id="114fb-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="114fb-520">Bu adımlar genellikle bir dosyanın tarama durumunu gösteren bir veritabanı kaydıyla birlikte gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="114fb-521">Böyle bir yaklaşım kullanarak, uygulama ve uygulama sunucusu isteklere yanıt vermeye odaklanmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="114fb-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="114fb-522">Dosya Uzantısı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="114fb-522">File extension validation</span></span>

<span data-ttu-id="114fb-523">Karşıya yüklenen dosyanın uzantısı izin verilen uzantılar listesine göre denetlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="114fb-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="114fb-524">Örnek:</span><span class="sxs-lookup"><span data-stu-id="114fb-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="114fb-525">Dosya imzası doğrulaması</span><span class="sxs-lookup"><span data-stu-id="114fb-525">File signature validation</span></span>

<span data-ttu-id="114fb-526">Bir dosyanın imzası, bir dosyanın başlangıcında ilk birkaç bayta göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="114fb-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="114fb-527">Bu baytlar, uzantının dosyanın içeriğiyle eşleşip eşleşmediğini göstermek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="114fb-528">Örnek uygulama, birkaç ortak dosya türü için dosya imzalarını denetler.</span><span class="sxs-lookup"><span data-stu-id="114fb-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="114fb-529">Aşağıdaki örnekte, bir JPEG görüntüsünün dosya imzası, dosyaya karşı denetlenir:</span><span class="sxs-lookup"><span data-stu-id="114fb-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="114fb-530">Ek dosya imzaları almak için [Dosya Imzaları veritabanı](https://www.filesignatures.net/) ve resmi dosya belirtimleri bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="114fb-531">Dosya adı güvenliği</span><span class="sxs-lookup"><span data-stu-id="114fb-531">File name security</span></span>

<span data-ttu-id="114fb-532">Fiziksel depolamaya bir dosyayı kaydetmek için hiçbir şekilde istemci tarafından sağlanan dosya adı kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="114fb-533">Geçici depolama için tam yol (dosya adı da dahil olmak üzere) oluşturmak için [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) veya [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanarak dosya için güvenli bir dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="114fb-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="114fb-534">Razor Otomatik HTML, görüntüleme için özellik değerlerini kodluyor.</span><span class="sxs-lookup"><span data-stu-id="114fb-534">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="114fb-535">Aşağıdaki kodun kullanımı güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="114fb-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="114fb-536">Dışında Razor , her zaman <xref:System.Net.WebUtility.HtmlEncode*> bir kullanıcının isteğinden dosya adı içeriği.</span><span class="sxs-lookup"><span data-stu-id="114fb-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="114fb-537">Birçok uygulama, dosyanın var olduğunu bir denetim içermelidir; Aksi takdirde, dosyanın üzerine aynı ada sahip bir dosya yazılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="114fb-538">Uygulamanızın belirtimlerini karşılamak için ek mantık sağlayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="114fb-539">Boyut doğrulaması</span><span class="sxs-lookup"><span data-stu-id="114fb-539">Size validation</span></span>

<span data-ttu-id="114fb-540">Karşıya yüklenen dosyaların boyutunu sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="114fb-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="114fb-541">Örnek uygulamada, dosyanın boyutu 2 MB ile sınırlıdır (bayt cinsinden gösterilir).</span><span class="sxs-lookup"><span data-stu-id="114fb-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="114fb-542">Sınır, dosyadaki *appsettings.js* [yapılandırma](xref:fundamentals/configuration/index) yoluyla sağlanır:</span><span class="sxs-lookup"><span data-stu-id="114fb-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="114fb-543">, `FileSizeLimit` `PageModel` Sınıflara eklenmiş:</span><span class="sxs-lookup"><span data-stu-id="114fb-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="114fb-544">Dosya boyutu sınırı aştığında, dosya reddedilir:</span><span class="sxs-lookup"><span data-stu-id="114fb-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="114fb-545">Name öznitelik değerini POST yönteminin Parameter adı ile Eşleştir</span><span class="sxs-lookup"><span data-stu-id="114fb-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="114fb-546">RazorForm verileri oluşturan veya JavaScript 'in `FormData` doğrudan kullandığı, form öğesinde belirtilen adın veya `FormData` denetleyicinin eyleminde parametrenin adıyla eşleşmesi gereken form olmayan formlar.</span><span class="sxs-lookup"><span data-stu-id="114fb-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="114fb-547">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="114fb-547">In the following example:</span></span>

* <span data-ttu-id="114fb-548">Bir öğesi kullanılırken `<input>` , `name` özniteliği değere ayarlanır `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="114fb-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="114fb-549">`FormData`JavaScript içinde kullanırken, ad değerine ayarlanır `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="114fb-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="114fb-550">C# yönteminin () parametresi için eşleşen bir ad kullanın `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="114fb-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="114fb-551">Bir Razor sayfalar sayfa işleyici yöntemi için şunu `Upload` adlı:</span><span class="sxs-lookup"><span data-stu-id="114fb-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="114fb-552">MVC POST denetleyicisi eylem yöntemi için:</span><span class="sxs-lookup"><span data-stu-id="114fb-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="114fb-553">Sunucu ve uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="114fb-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="114fb-554">Çok parçalı gövde uzunluğu sınırı</span><span class="sxs-lookup"><span data-stu-id="114fb-554">Multipart body length limit</span></span>

<span data-ttu-id="114fb-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Her bir çok parçalı gövdenin uzunluk sınırını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="114fb-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="114fb-556">Bu sınırı aşan form bölümleri <xref:System.IO.InvalidDataException> ayrıştırıldığında bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="114fb-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="114fb-557">Varsayılan değer 134.217.728 ' dir (128 MB).</span><span class="sxs-lookup"><span data-stu-id="114fb-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="114fb-558">Sınırı, <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> içindeki ayarı kullanarak özelleştirin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="114fb-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="114fb-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>tek sayfa veya eylem için ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="114fb-560">Bir Razor Sayfalar uygulamasında, filtresi içindeki bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="114fb-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="114fb-561">Bir Razor Sayfalar uygulamasında veya BIR MVC uygulamasında, filtreyi sayfa modeline veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="114fb-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="114fb-562">Kestrel maksimum istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="114fb-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="114fb-563">Kestrel tarafından barındırılan uygulamalar için, varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu, yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="114fb-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="114fb-564">[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel Server seçeneğini kullanarak sınırı özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="114fb-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="114fb-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> , tek sayfa veya eylem için [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) ayarlamak üzere kullanılır.</span><span class="sxs-lookup"><span data-stu-id="114fb-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="114fb-566">Bir Razor Sayfalar uygulamasında, filtresi içindeki bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="114fb-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="114fb-567">Bir Razor Sayfalar uygulamasında veya BIR MVC uygulamasında, filtreyi sayfa işleyici sınıfına veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="114fb-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="114fb-568">Diğer Kestrel limitleri</span><span class="sxs-lookup"><span data-stu-id="114fb-568">Other Kestrel limits</span></span>

<span data-ttu-id="114fb-569">Kestrel tarafından barındırılan uygulamalar için diğer Kestrel limitleri de uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="114fb-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="114fb-570">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="114fb-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="114fb-571">İstek ve yanıt veri ücretleri</span><span class="sxs-lookup"><span data-stu-id="114fb-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="114fb-572">IIS içerik uzunluğu sınırı</span><span class="sxs-lookup"><span data-stu-id="114fb-572">IIS content length limit</span></span>

<span data-ttu-id="114fb-573">Varsayılan istek sınırı ( `maxAllowedContentLength` ), yaklaşık 28.6 MB olan 30.000.000 bayttır.</span><span class="sxs-lookup"><span data-stu-id="114fb-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="114fb-574">*web.config* dosyasında sınırı özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="114fb-574">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="114fb-575">Bu ayar yalnızca IIS için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="114fb-575">This setting only applies to IIS.</span></span> <span data-ttu-id="114fb-576">Kestrel üzerinde barındırırken davranış varsayılan olarak gerçekleşmez.</span><span class="sxs-lookup"><span data-stu-id="114fb-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="114fb-577">Daha fazla bilgi için bkz. [Istek \<requestLimits> sınırları ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="114fb-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="114fb-578">ASP.NET Core modülündeki sınırlamalar veya IIS Istek filtreleme modülünün varlığı, karşıya yüklemeleri 2 veya 4 GB ile sınırlandırabilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="114fb-579">Daha fazla bilgi için bkz. [2 GB 'tan büyük dosya karşıya yüklenemiyor (DotNet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="114fb-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="114fb-580">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="114fb-580">Troubleshoot</span></span>

<span data-ttu-id="114fb-581">Dosyaları karşıya yükleme ve olası çözümleri ile çalışırken karşılaşılan bazı yaygın sorunlar aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="114fb-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="114fb-582">Bir IIS sunucusuna dağıtılırken bulunamadı hatası</span><span class="sxs-lookup"><span data-stu-id="114fb-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="114fb-583">Aşağıdaki hata karşıya yüklenen dosyanın, sunucunun yapılandırılmış içerik uzunluğunu aştığını gösterir:</span><span class="sxs-lookup"><span data-stu-id="114fb-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="114fb-584">Limiti artırma hakkında daha fazla bilgi için bkz. [IIS içerik uzunluğu sınırı](#iis-content-length-limit) bölümü.</span><span class="sxs-lookup"><span data-stu-id="114fb-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="114fb-585">Bağlantı hatası</span><span class="sxs-lookup"><span data-stu-id="114fb-585">Connection failure</span></span>

<span data-ttu-id="114fb-586">Bir bağlantı hatası ve bir sıfırlama sunucu bağlantısı büyük olasılıkla karşıya yüklenen dosyanın Kestrel 'in en büyük istek gövdesi boyutunu aştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="114fb-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="114fb-587">Daha fazla bilgi için, [Kestrel maksimum istek gövdesi boyutu](#kestrel-maximum-request-body-size) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="114fb-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="114fb-588">Kestrel istemci bağlantı limitleri de ayarlama gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="114fb-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="114fb-589">Iformfile ile null başvuru özel durumu</span><span class="sxs-lookup"><span data-stu-id="114fb-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="114fb-590">Denetleyici karşıya yüklenen dosyaları kullanarak kabul edilirse, <xref:Microsoft.AspNetCore.Http.IFormFile> ancak değer ise `null` , HTML formunun bir değerini belirtmesini onaylayın `enctype` `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="114fb-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="114fb-591">Bu öznitelik `<form>` öğesinde ayarlanmamışsa, dosya karşıya yükleme gerçekleşmez ve herhangi bir bağlı <xref:Microsoft.AspNetCore.Http.IFormFile> bağımsız değişken `null` .</span><span class="sxs-lookup"><span data-stu-id="114fb-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="114fb-592">Ayrıca, [form verilerinde karşıya yükleme adlandırmasının uygulamanın adlandırmayla eşleştiğinden](#match-name-attribute-value-to-parameter-name-of-post-method)emin olun.</span><span class="sxs-lookup"><span data-stu-id="114fb-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="114fb-593">Akış çok uzun</span><span class="sxs-lookup"><span data-stu-id="114fb-593">Stream was too long</span></span>

<span data-ttu-id="114fb-594">Bu konudaki örnekler <xref:System.IO.MemoryStream> karşıya yüklenen dosyanın içeriğini tutmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="114fb-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="114fb-595">Bir öğesinin boyut sınırı `MemoryStream` `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="114fb-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="114fb-596">Uygulamanın dosya yükleme senaryosu, dosya içeriğinin 50 MB 'tan büyük olmasını gerektiriyorsa, `MemoryStream` karşıya yüklenen bir dosyanın içeriğini tutmak için tek başına olmayan alternatif bir yaklaşım kullanın.</span><span class="sxs-lookup"><span data-stu-id="114fb-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="114fb-597">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="114fb-597">Additional resources</span></span>

* [<span data-ttu-id="114fb-598">HTTP bağlantı isteği boşaltma</span><span class="sxs-lookup"><span data-stu-id="114fb-598">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="114fb-599">Kısıtlanmamış dosya yükleme</span><span class="sxs-lookup"><span data-stu-id="114fb-599">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="114fb-600">Azure güvenliği: güvenlik çerçevesi: giriş doğrulaması | Karşı</span><span class="sxs-lookup"><span data-stu-id="114fb-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="114fb-601">Azure bulut tasarım desenleri: Valet anahtar düzeni</span><span class="sxs-lookup"><span data-stu-id="114fb-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
