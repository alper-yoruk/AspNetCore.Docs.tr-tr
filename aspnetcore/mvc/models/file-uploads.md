---
title: ASP.NET Core dosyaları karşıya yükleme
author: rick-anderson
description: ASP.NET Core MVC 'de dosyaları karşıya yüklemek için model bağlama ve akışı kullanma.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
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
uid: mvc/models/file-uploads
ms.openlocfilehash: c32d20d4616650db004c78fb4d8ea9a4d5a3beab
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252805"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="71d69-103">ASP.NET Core dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="71d69-104">[Steve Smith](https://ardalis.com/) ve [Rutger fırtınası](https://github.com/rutix) tarafından</span><span class="sxs-lookup"><span data-stu-id="71d69-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="71d69-105">ASP.NET Core, daha küçük dosyalar için arabellekli model bağlama ve daha büyük dosyalar için arabelleğe alınmamış akış kullanarak bir veya daha fazla dosyanın yüklenmesini destekler</span><span class="sxs-lookup"><span data-stu-id="71d69-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="71d69-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71d69-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="71d69-107">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="71d69-107">Security considerations</span></span>

<span data-ttu-id="71d69-108">Kullanıcılara bir sunucuya dosya yükleme yeteneği sağlarken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="71d69-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="71d69-109">Saldırganlar şunları deneyebilir:</span><span class="sxs-lookup"><span data-stu-id="71d69-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="71d69-110">[Hizmet reddi](/windows-hardware/drivers/ifs/denial-of-service) saldırıları yürütün.</span><span class="sxs-lookup"><span data-stu-id="71d69-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="71d69-111">Virüsleri veya kötü amaçlı yazılımları karşıya yükleyin.</span><span class="sxs-lookup"><span data-stu-id="71d69-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="71d69-112">Ağları ve sunucuları diğer yollarla tehlikeye atabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="71d69-113">Başarılı bir saldırının olasılığını azaltan güvenlik adımları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="71d69-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="71d69-114">Dosyaları, tercihen sistem dışı bir sürücüye, özel bir dosya yükleme alanına yükleyin.</span><span class="sxs-lookup"><span data-stu-id="71d69-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="71d69-115">Ayrılmış bir konum, karşıya yüklenen dosyalar üzerinde güvenlik kısıtlamaları yapmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="71d69-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="71d69-116">Dosya yükleme konumunda yürütme izinlerini devre dışı bırakın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="71d69-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="71d69-117">Karşıya yüklenen dosyaları uygulamayla aynı dizin **ağacında kalıcı hale** getirme.&dagger;</span><span class="sxs-lookup"><span data-stu-id="71d69-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="71d69-118">Uygulama tarafından belirlenen bir güvenli dosya adı kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="71d69-119">Kullanıcı tarafından belirtilen bir dosya adı veya karşıya yüklenen dosyanın güvenilmeyen dosya adı kullanmayın. &dagger; HTML, görüntüleme sırasında güvenilmeyen dosya adını kodlayamıyor.</span><span class="sxs-lookup"><span data-stu-id="71d69-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="71d69-120">Örneğin, dosya adını günlüğe kaydetme veya Kullanıcı arabiriminde görüntüleme ( Razor otomatik olarak HTML kodlama çıktısı).</span><span class="sxs-lookup"><span data-stu-id="71d69-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="71d69-121">Uygulamanın tasarım belirtimi için yalnızca onaylanan dosya uzantılarına izin verin.&dagger;</span><span class="sxs-lookup"><span data-stu-id="71d69-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="71d69-122">Sunucuda istemci tarafı denetimlerinin gerçekleştirildiğinden emin olun. &dagger; İstemci tarafı denetimleri kolayca atmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="71d69-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="71d69-123">Karşıya yüklenen dosyanın boyutunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="71d69-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="71d69-124">Büyük karşıya yüklemeleri engellemek için en büyük boyut sınırını ayarlayın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="71d69-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="71d69-125">Aynı ada sahip karşıya yüklenen bir dosya tarafından dosyaların üzerine yazılmaması gerektiğinde, dosyayı karşıya yüklemeden önce dosya adını veritabanına veya fiziksel depolamaya göre denetleyin.</span><span class="sxs-lookup"><span data-stu-id="71d69-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="71d69-126">**Dosya depolanmadan önce karşıya yüklenen içerik üzerinde bir virüs/kötü amaçlı yazılım tarayıcısı çalıştırın.**</span><span class="sxs-lookup"><span data-stu-id="71d69-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="71d69-127">&dagger;Örnek uygulama, ölçütlere uyan bir yaklaşımı gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="71d69-128">Kötü amaçlı kodun bir sisteme yüklenmesi genellikle şu şekilde kod yürütmenin ilk adımıdır:</span><span class="sxs-lookup"><span data-stu-id="71d69-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="71d69-129">Sistemin denetimini tamamen elde edin.</span><span class="sxs-lookup"><span data-stu-id="71d69-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="71d69-130">Sistemin kilitlenme sonucuyla bir sistemi aşırı yükleme.</span><span class="sxs-lookup"><span data-stu-id="71d69-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="71d69-131">Kullanıcı veya Sistem verilerinin güvenliğini tehlikeye atabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="71d69-132">Genel Kullanıcı arabirimine Graffiti uygulayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="71d69-133">Kullanıcılardan dosya kabul edilirken saldırı yüzeyi alanını azaltma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="71d69-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="71d69-134">Kısıtlanmamış dosya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="71d69-135">Azure güvenliği: kullanıcılardan dosya kabul edilirken uygun denetimlerin yerinde olduğundan emin olun</span><span class="sxs-lookup"><span data-stu-id="71d69-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="71d69-136">Örnek uygulamadaki örnekler de dahil olmak üzere güvenlik önlemlerini uygulama hakkında daha fazla bilgi için [doğrulama](#validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="71d69-137">Depolama senaryoları</span><span class="sxs-lookup"><span data-stu-id="71d69-137">Storage scenarios</span></span>

<span data-ttu-id="71d69-138">Dosyalar için ortak depolama seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="71d69-138">Common storage options for files include:</span></span>

* <span data-ttu-id="71d69-139">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="71d69-139">Database</span></span>

  * <span data-ttu-id="71d69-140">Küçük dosya yüklemeleri için, bir veritabanı genellikle fiziksel depolama (dosya sistemi veya ağ paylaşımından) seçeneklerinden daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="71d69-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="71d69-141">Kullanıcı verileri için bir veritabanı kaydının alınması eşzamanlı olarak dosya içeriğini (örneğin, avatar görüntüsü) sağlayabildiğinden, veritabanı genellikle fiziksel depolama seçeneklerine göre daha uygundur.</span><span class="sxs-lookup"><span data-stu-id="71d69-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="71d69-142">Bir veritabanı, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="71d69-143">Fiziksel depolama (dosya sistemi veya ağ paylaşma)</span><span class="sxs-lookup"><span data-stu-id="71d69-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="71d69-144">Büyük dosya yüklemeleri için:</span><span class="sxs-lookup"><span data-stu-id="71d69-144">For large file uploads:</span></span>
    * <span data-ttu-id="71d69-145">Veritabanı limitleri karşıya yükleme boyutunu kısıtlayabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="71d69-146">Fiziksel depolama genellikle bir veritabanındaki depolamadan daha az ekonomik olur.</span><span class="sxs-lookup"><span data-stu-id="71d69-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="71d69-147">Fiziksel depolama, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="71d69-148">Uygulamanın işlemi, depolama konumu için okuma ve yazma izinlerine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="71d69-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="71d69-149">**Hiçbir zaman yürütme izni vermeyin.**</span><span class="sxs-lookup"><span data-stu-id="71d69-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="71d69-150">Veri depolama hizmeti (örneğin, [Azure Blob depolama](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="71d69-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="71d69-151">Hizmetler genellikle tek hata noktalarına tabi olan şirket içi çözümler üzerinde geliştirilmiş ölçeklenebilirlik ve esneklik sunar.</span><span class="sxs-lookup"><span data-stu-id="71d69-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="71d69-152">Hizmetler büyük depolama altyapısı senaryolarında düşük maliyetlidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="71d69-153">Daha fazla bilgi için bkz. [hızlı başlangıç: nesne depolamada blob oluşturmak için .NET kullanma](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="71d69-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="71d69-154">Karşıya dosya yükleme senaryoları</span><span class="sxs-lookup"><span data-stu-id="71d69-154">File upload scenarios</span></span>

<span data-ttu-id="71d69-155">Dosyaları karşıya yüklemek için iki genel yaklaşım arabelleğe alınır ve akışlardır.</span><span class="sxs-lookup"><span data-stu-id="71d69-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="71d69-156">**Ara**</span><span class="sxs-lookup"><span data-stu-id="71d69-156">**Buffering**</span></span>

<span data-ttu-id="71d69-157">Dosyanın tamamı <xref:Microsoft.AspNetCore.Http.IFormFile> , dosyayı işlemek veya kaydetmek için kullanılan dosyanın C# temsili olan öğesine okunurdur.</span><span class="sxs-lookup"><span data-stu-id="71d69-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="71d69-158">Dosya karşıya yüklemeleri tarafından kullanılan kaynaklar (disk, bellek), eşzamanlı dosya karşıya yüklemelerinin sayısına ve boyutuna bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="71d69-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="71d69-159">Bir uygulama çok fazla karşıya yükleme arabelleğini denerse, bellek veya disk alanı tükenirse site çöker.</span><span class="sxs-lookup"><span data-stu-id="71d69-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="71d69-160">Karşıya dosya yükleme boyutu veya sıklığı uygulama kaynaklarını tüketme ise, akış ' ı kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="71d69-161">64 KB geçen tek bir arabelleğe alınmış dosya, bellekten diskte geçici bir dosyaya taşınır.</span><span class="sxs-lookup"><span data-stu-id="71d69-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="71d69-162">Dosya arabelleğe alma, bu konunun aşağıdaki bölümlerinde ele alınmıştır:</span><span class="sxs-lookup"><span data-stu-id="71d69-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="71d69-163">Fiziksel depolama alanı</span><span class="sxs-lookup"><span data-stu-id="71d69-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="71d69-164">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="71d69-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="71d69-165">**Akışlar**</span><span class="sxs-lookup"><span data-stu-id="71d69-165">**Streaming**</span></span>

<span data-ttu-id="71d69-166">Dosya çok parçalı bir istekten alınır ve doğrudan uygulama tarafından işlenir veya kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="71d69-167">Akış performansı önemli ölçüde iyileştirmez.</span><span class="sxs-lookup"><span data-stu-id="71d69-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="71d69-168">Akış, dosya karşıya yüklenirken bellek veya disk alanı taleplerini azaltır.</span><span class="sxs-lookup"><span data-stu-id="71d69-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="71d69-169">Akış büyük dosyaları [akış ile büyük dosyaları karşıya yükle](#upload-large-files-with-streaming) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="71d69-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="71d69-170">Fiziksel depolamaya arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="71d69-171">Küçük dosyaları karşıya yüklemek için, çok parçalı bir form kullanın veya JavaScript kullanarak bir POST isteği oluşturun.</span><span class="sxs-lookup"><span data-stu-id="71d69-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="71d69-172">Aşağıdaki örnek, Razor tek bir dosyayı karşıya yüklemek için sayfalar formunun kullanımını gösterir (örnek uygulamada *Pages/Bufferedsinglefileuploadfiziksel. cshtml* ):</span><span class="sxs-lookup"><span data-stu-id="71d69-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="71d69-173">Aşağıdaki örnek, önceki örneğe benzerdir, ancak şunları hariç:</span><span class="sxs-lookup"><span data-stu-id="71d69-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="71d69-174">Form verilerini göndermek için JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="71d69-175">Doğrulama yok.</span><span class="sxs-lookup"><span data-stu-id="71d69-175">There's no validation.</span></span>

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

<span data-ttu-id="71d69-176">[Fetch API 'sini desteklemeyen](https://caniuse.com/#feat=fetch)istemcilerde form gönderisini JavaScript 'te gerçekleştirmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="71d69-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="71d69-177">Fetch Polyfill kullanın (örneğin, [Window. Fetch (GitHub/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="71d69-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="71d69-178">`XMLHttpRequest` komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="71d69-179">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="71d69-179">For example:</span></span>

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

<span data-ttu-id="71d69-180">Dosya yüklemelerini desteklemek için, HTML formları bir kodlama türü ( `enctype` ) belirtmelidir `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="71d69-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="71d69-181">`files`Birden çok dosyayı karşıya yüklemeyi destekleyen bir giriş öğesi için, `multiple` öğesine özniteliği sağlar `<input>` :</span><span class="sxs-lookup"><span data-stu-id="71d69-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="71d69-182">Sunucuya yüklenen tek dosyalara, kullanılarak [model bağlama](xref:mvc/models/model-binding) yoluyla erişilebilir <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="71d69-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="71d69-183">Örnek uygulama, veritabanı ve fiziksel depolama senaryoları için birden çok arabellekli dosya yüklemeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="71d69-184"> `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> Görüntüleme ve günlüğe kaydetme için dışındaki özelliğini kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="71d69-185">Görüntüleme veya günlüğe kaydetme sırasında, HTML dosya adını kodlayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="71d69-186">Saldırgan, tam yollar veya göreli yollar dahil olmak üzere kötü amaçlı bir dosya adı sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="71d69-187">Uygulamalar:</span><span class="sxs-lookup"><span data-stu-id="71d69-187">Applications should:</span></span>
>
> * <span data-ttu-id="71d69-188">Kullanıcı tarafından sağlanan dosya adının yolunu kaldırın.</span><span class="sxs-lookup"><span data-stu-id="71d69-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="71d69-189">Kullanıcı arabirimi veya günlüğe kaydetme için HTML kodlu, yol tarafından kaldırılan dosya adını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="71d69-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="71d69-190">Depolama için yeni bir rastgele dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="71d69-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="71d69-191">Aşağıdaki kod, dosya adından yolu kaldırır:</span><span class="sxs-lookup"><span data-stu-id="71d69-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="71d69-192">Bu nedenle, şu ana kadar dikkate alınması gereken örnekler aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="71d69-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="71d69-193">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="71d69-194">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="71d69-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="71d69-195">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="71d69-195">Validation</span></span>](#validation)

<span data-ttu-id="71d69-196">Model bağlama kullanarak dosyaları karşıya yüklerken <xref:Microsoft.AspNetCore.Http.IFormFile> , eylem yöntemi kabul edebilir:</span><span class="sxs-lookup"><span data-stu-id="71d69-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="71d69-197">Tek bir <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="71d69-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="71d69-198">Birkaç dosyayı temsil eden aşağıdaki koleksiyonlardan herhangi biri:</span><span class="sxs-lookup"><span data-stu-id="71d69-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="71d69-199">[Listele](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="71d69-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="71d69-200">Bağlama, form dosyaları adına göre eşleşir.</span><span class="sxs-lookup"><span data-stu-id="71d69-200">Binding matches form files by name.</span></span> <span data-ttu-id="71d69-201">Örneğin, `name` IÇINDEKI HTML değeri `<input type="file" name="formFile">` C# parametresi/özelliği ile ( `FormFile` ) eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="71d69-202">Daha fazla bilgi için, [ad öznitelik DEĞERINI Post yönteminin parametre adına eşleştirin](#match-name-attribute-value-to-parameter-name-of-post-method) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="71d69-203">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="71d69-203">The following example:</span></span>

* <span data-ttu-id="71d69-204">Karşıya yüklenen bir veya daha fazla dosya üzerinden döngü.</span><span class="sxs-lookup"><span data-stu-id="71d69-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="71d69-205">Dosya adı da dahil olmak üzere bir dosyanın tam yolunu döndürmek için [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanır.</span><span class="sxs-lookup"><span data-stu-id="71d69-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="71d69-206">Dosyalar, uygulama tarafından oluşturulan bir dosya adı kullanılarak yerel dosya sistemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="71d69-207">Karşıya yüklenen dosyaların toplam sayısını ve boyutunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="71d69-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="71d69-208">`Path.GetRandomFileName`Yol olmadan bir dosya adı oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="71d69-209">Aşağıdaki örnekte, yol yapılandırmadan alınır:</span><span class="sxs-lookup"><span data-stu-id="71d69-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="71d69-210">Öğesine geçirilen yol, <xref:System.IO.FileStream>  dosya adını içermelidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="71d69-211">Dosya adı sağlanmazsa, çalışma zamanında bir oluşturulur <xref:System.UnauthorizedAccessException> .</span><span class="sxs-lookup"><span data-stu-id="71d69-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="71d69-212">Tekniği kullanılarak yüklenen dosyalar, <xref:Microsoft.AspNetCore.Http.IFormFile> işlemeden önce sunucuda veya diskte bellek halinde arabelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="71d69-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="71d69-213">Eylem yönteminde, <xref:Microsoft.AspNetCore.Http.IFormFile> içeriğe bir olarak erişilebilir <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="71d69-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="71d69-214">Yerel dosya sistemine ek olarak, dosyalar bir ağ paylaşımında veya [Azure Blob depolama](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)gibi bir dosya depolama hizmetine kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="71d69-215">Karşıya yüklemek için birden çok dosya üzerinde döngü yapan ve güvenli dosya adları kullanan başka bir örnek için, örnek uygulamadaki *Pages/Bufferedmultiplefileuploadfiziksel. cshtml. cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="71d69-216">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) <xref:System.IO.IOException> , önceki geçici dosyaları silmeden 65.535 ' den fazla dosya oluşturulduysa bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="71d69-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="71d69-217">65.535 dosya sınırının sunucu başına sınırı vardır.</span><span class="sxs-lookup"><span data-stu-id="71d69-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="71d69-218">Windows işletim sistemi için bu sınır hakkında daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="71d69-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="71d69-219">GetTempFileNameA işlevi</span><span class="sxs-lookup"><span data-stu-id="71d69-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="71d69-220">Bir veritabanına arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="71d69-221">İkili dosya verilerini [Entity Framework](/ef/core/index)kullanarak bir veritabanında depolamak için, <xref:System.Byte> varlıkta bir dizi özelliği tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="71d69-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="71d69-222">Sınıf için şunu içeren bir sayfa modeli özelliği belirtin <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="71d69-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="71d69-223"><xref:Microsoft.AspNetCore.Http.IFormFile> doğrudan bir eylem yöntemi parametresi veya bir bağlantılı model özelliği olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="71d69-224">Önceki örnekte, bir bağlantılı model özelliği kullanılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="71d69-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="71d69-225">, `FileUpload` Razor Sayfalar formunda kullanılır:</span><span class="sxs-lookup"><span data-stu-id="71d69-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="71d69-226">Form sunucuya gönderildiğinde, öğesini <xref:Microsoft.AspNetCore.Http.IFormFile> bir akışa kopyalayın ve veritabanına bir bayt dizisi olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="71d69-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="71d69-227">Aşağıdaki örnekte, `_dbContext` uygulamanın veritabanı bağlamını depolar:</span><span class="sxs-lookup"><span data-stu-id="71d69-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="71d69-228">Yukarıdaki örnek, örnek uygulamada gösterilen senaryoya benzerdir:</span><span class="sxs-lookup"><span data-stu-id="71d69-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="71d69-229">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="71d69-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="71d69-230">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="71d69-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="71d69-231">İkili verileri ilişkisel veritabanlarında depolarken dikkatli olun, çünkü performansı olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="71d69-232">Doğrulaması olmadan özelliğine güvenmeyin veya bu `FileName` özelliğe güvenmeyin <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="71d69-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="71d69-233">`FileName`Özelliği yalnızca görüntüleme amacıyla ve yalnızca HTML kodlaması sonrasında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="71d69-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="71d69-234">Belirtilen örneklerde dikkate alınması gereken önemli noktalar.</span><span class="sxs-lookup"><span data-stu-id="71d69-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="71d69-235">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="71d69-236">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="71d69-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="71d69-237">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="71d69-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="71d69-238">Akışa sahip büyük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-238">Upload large files with streaming</span></span>

<span data-ttu-id="71d69-239">Aşağıdaki örnek, bir denetleyiciyi bir denetleyici eyleminde akışa almak için JavaScript 'in nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="71d69-240">Dosyanın antiforgery belirteci özel bir filtre özniteliği kullanılarak oluşturulur ve istek gövdesi yerine istemci HTTP üst bilgilerine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="71d69-241">Eylem yöntemi karşıya yüklenen verileri doğrudan işlediğinden, form modeli bağlama başka bir özel filtre tarafından devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="71d69-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="71d69-242">Eylem içinde formun içerikleri, `MultipartReader` her bir bireyi okuyan `MultipartSection` , dosyayı işleyen veya içeriği uygun şekilde depolayan bir kullanılarak okunur.</span><span class="sxs-lookup"><span data-stu-id="71d69-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="71d69-243">Çok parçalı bölümler okunduktan sonra eylem kendi model bağlamasını gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="71d69-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="71d69-244">İlk sayfa yanıtı formu yükler ve bir cookie (özniteliği aracılığıyla) bir antiforgery belirtecini kaydeder `GenerateAntiforgeryTokenCookieAttribute` .</span><span class="sxs-lookup"><span data-stu-id="71d69-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="71d69-245">Özniteliği, bir istek belirteci ile ayarlamak için ASP.NET Core yerleşik [antiforgery desteğini](xref:security/anti-request-forgery) kullanır cookie :</span><span class="sxs-lookup"><span data-stu-id="71d69-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="71d69-246">`DisableFormValueModelBindingAttribute`Model bağlamayı devre dışı bırakmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="71d69-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="71d69-247">Örnek uygulamada `GenerateAntiforgeryTokenCookieAttribute` ve `DisableFormValueModelBindingAttribute` sayfa `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor kuralları](xref:razor-pages/razor-pages-conventions)kullanılarak ve içindeki sayfa uygulama modellerine filtre olarak uygulanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="71d69-248">Model bağlama formu okumadığından formdan bağlanan parametreler bağlanamaz (sorgu, yol ve başlık çalışmaya devam eder).</span><span class="sxs-lookup"><span data-stu-id="71d69-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="71d69-249">Action yöntemi doğrudan özelliği ile birlikte çalışabilir `Request` .</span><span class="sxs-lookup"><span data-stu-id="71d69-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="71d69-250">`MultipartReader`Her bölümü okumak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="71d69-251">Anahtar/değer verileri bir içinde depolanır `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="71d69-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="71d69-252">Çok parçalı bölümler okunduktan sonra, öğesinin içeriği `KeyValueAccumulator` form verilerini bir model türüne bağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="71d69-253">`StreamingController.UploadDatabase`EF Core bir veritabanına akış için tamamlanan Yöntem:</span><span class="sxs-lookup"><span data-stu-id="71d69-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="71d69-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="71d69-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="71d69-255">`StreamingController.UploadPhysical`Fiziksel bir konuma akışa yönelik tüm Yöntem:</span><span class="sxs-lookup"><span data-stu-id="71d69-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="71d69-256">Örnek uygulamada, doğrulama denetimleri tarafından işlenir `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="71d69-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="71d69-257">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="71d69-257">Validation</span></span>

<span data-ttu-id="71d69-258">Örnek uygulamanın sınıfı, `FileHelpers` arabelleğe alınmış <xref:Microsoft.AspNetCore.Http.IFormFile> ve akış dosya yüklemeleri için birkaç denetim gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="71d69-259"><xref:Microsoft.AspNetCore.Http.IFormFile>Örnek uygulamada ara belleğe alınmış dosya yüklemelerini işlemek için, `ProcessFormFile` *Utilities/fileyardımcılar. cs* dosyasındaki yöntemine bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="71d69-260">Akış dosyalarını işlemek için `ProcessStreamedFile` aynı dosyadaki yöntemine bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="71d69-261">Örnek uygulamada gösterilen doğrulama işleme yöntemleri karşıya yüklenen dosyaların içeriğini taramaz.</span><span class="sxs-lookup"><span data-stu-id="71d69-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="71d69-262">Çoğu üretim senaryosunda, dosyanın kullanıcılara veya diğer sistemlere kullanılabilir hale getirilmesi için dosya üzerinde bir virüs/kötü amaçlı yazılım tarayıcı API 'SI kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="71d69-263">Konu örneği, doğrulama tekniklerine yönelik çalışan bir örnek sağlasa da, `FileHelpers` aşağıdakileri gerçekleştirmediğiniz takdirde sınıfı bir üretim uygulamasında uygulamaz:</span><span class="sxs-lookup"><span data-stu-id="71d69-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="71d69-264">Uygulamayı tam olarak anlayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="71d69-265">Uygulamayı uygulamanın ortamı ve belirtimleri için uygun şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="71d69-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="71d69-266">**Bu gereksinimleri bilmeden bir uygulamada güvenlik kodunu hiçbir şekilde sayısının fark gözetmeden uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="71d69-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="71d69-267">İçerik doğrulama</span><span class="sxs-lookup"><span data-stu-id="71d69-267">Content validation</span></span>

<span data-ttu-id="71d69-268">**Karşıya yüklenen içerikte üçüncü taraf bir virüs/kötü amaçlı yazılım tarama API 'SI kullanın.**</span><span class="sxs-lookup"><span data-stu-id="71d69-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="71d69-269">Dosyaları tarama, yüksek hacimli senaryolarda sunucu kaynaklarında yoğun bir şekilde yapılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="71d69-270">Dosya tarama nedeniyle istek işleme performansı azaldığında, tarama işini, muhtemelen uygulamanın sunucusundan farklı bir sunucuda çalışan bir [arka plan hizmetine](xref:fundamentals/host/hosted-services)devredere göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="71d69-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="71d69-271">Genellikle, arka plan virüs tarayıcısı tarafından denetlene kadar karşıya yüklenen dosyalar karantinaya alınmış bir alanda tutulur.</span><span class="sxs-lookup"><span data-stu-id="71d69-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="71d69-272">Bir dosya geçtiğinde dosya normal dosya depolama konumuna taşınır.</span><span class="sxs-lookup"><span data-stu-id="71d69-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="71d69-273">Bu adımlar genellikle bir dosyanın tarama durumunu gösteren bir veritabanı kaydıyla birlikte gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="71d69-274">Böyle bir yaklaşım kullanarak, uygulama ve uygulama sunucusu isteklere yanıt vermeye odaklanmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="71d69-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="71d69-275">Dosya Uzantısı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="71d69-275">File extension validation</span></span>

<span data-ttu-id="71d69-276">Karşıya yüklenen dosyanın uzantısı izin verilen uzantılar listesine göre denetlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="71d69-277">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="71d69-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="71d69-278">Dosya imzası doğrulaması</span><span class="sxs-lookup"><span data-stu-id="71d69-278">File signature validation</span></span>

<span data-ttu-id="71d69-279">Bir dosyanın imzası, bir dosyanın başlangıcında ilk birkaç bayta göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="71d69-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="71d69-280">Bu baytlar, uzantının dosyanın içeriğiyle eşleşip eşleşmediğini göstermek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="71d69-281">Örnek uygulama, birkaç ortak dosya türü için dosya imzalarını denetler.</span><span class="sxs-lookup"><span data-stu-id="71d69-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="71d69-282">Aşağıdaki örnekte, bir JPEG görüntüsünün dosya imzası, dosyaya karşı denetlenir:</span><span class="sxs-lookup"><span data-stu-id="71d69-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="71d69-283">Ek dosya imzaları almak için [Dosya Imzaları veritabanı](https://www.filesignatures.net/) ve resmi dosya belirtimleri bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="71d69-284">Dosya adı güvenliği</span><span class="sxs-lookup"><span data-stu-id="71d69-284">File name security</span></span>

<span data-ttu-id="71d69-285">Fiziksel depolamaya bir dosyayı kaydetmek için hiçbir şekilde istemci tarafından sağlanan dosya adı kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="71d69-286">Geçici depolama için tam yol (dosya adı da dahil olmak üzere) oluşturmak için [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) veya [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanarak dosya için güvenli bir dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="71d69-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="71d69-287">Razor Otomatik HTML, görüntüleme için özellik değerlerini kodluyor.</span><span class="sxs-lookup"><span data-stu-id="71d69-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="71d69-288">Aşağıdaki kodun kullanımı güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="71d69-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="71d69-289">Dışında Razor , her zaman <xref:System.Net.WebUtility.HtmlEncode*> bir kullanıcının isteğinden dosya adı içeriği.</span><span class="sxs-lookup"><span data-stu-id="71d69-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="71d69-290">Birçok uygulama, dosyanın var olduğunu bir denetim içermelidir; Aksi takdirde, dosyanın üzerine aynı ada sahip bir dosya yazılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="71d69-291">Uygulamanızın belirtimlerini karşılamak için ek mantık sağlayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="71d69-292">Boyut doğrulaması</span><span class="sxs-lookup"><span data-stu-id="71d69-292">Size validation</span></span>

<span data-ttu-id="71d69-293">Karşıya yüklenen dosyaların boyutunu sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="71d69-294">Örnek uygulamada, dosyanın boyutu 2 MB ile sınırlıdır (bayt cinsinden gösterilir).</span><span class="sxs-lookup"><span data-stu-id="71d69-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="71d69-295">Sınır, dosyadaki [yapılandırma](xref:fundamentals/configuration/index) yoluyla sağlanır *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="71d69-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="71d69-296">, `FileSizeLimit` `PageModel` Sınıflara eklenmiş:</span><span class="sxs-lookup"><span data-stu-id="71d69-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="71d69-297">Dosya boyutu sınırı aştığında, dosya reddedilir:</span><span class="sxs-lookup"><span data-stu-id="71d69-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="71d69-298">Name öznitelik değerini POST yönteminin Parameter adı ile Eşleştir</span><span class="sxs-lookup"><span data-stu-id="71d69-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="71d69-299">RazorForm verileri oluşturan veya JavaScript 'in `FormData` doğrudan kullandığı, form öğesinde belirtilen adın veya `FormData` denetleyicinin eyleminde parametrenin adıyla eşleşmesi gereken form olmayan formlar.</span><span class="sxs-lookup"><span data-stu-id="71d69-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="71d69-300">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="71d69-300">In the following example:</span></span>

* <span data-ttu-id="71d69-301">Bir öğesi kullanılırken `<input>` , `name` özniteliği değere ayarlanır `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="71d69-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="71d69-302">`FormData`JavaScript içinde kullanırken, ad değerine ayarlanır `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="71d69-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="71d69-303">C# yönteminin () parametresi için eşleşen bir ad kullanın `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="71d69-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="71d69-304">Bir Razor sayfalar sayfa işleyici yöntemi için şunu `Upload` adlı:</span><span class="sxs-lookup"><span data-stu-id="71d69-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="71d69-305">MVC POST denetleyicisi eylem yöntemi için:</span><span class="sxs-lookup"><span data-stu-id="71d69-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="71d69-306">Sunucu ve uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="71d69-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="71d69-307">Çok parçalı gövde uzunluğu sınırı</span><span class="sxs-lookup"><span data-stu-id="71d69-307">Multipart body length limit</span></span>

<span data-ttu-id="71d69-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Her bir çok parçalı gövdenin uzunluk sınırını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="71d69-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="71d69-309">Bu sınırı aşan form bölümleri <xref:System.IO.InvalidDataException> ayrıştırıldığında bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="71d69-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="71d69-310">Varsayılan değer 134.217.728 ' dir (128 MB).</span><span class="sxs-lookup"><span data-stu-id="71d69-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="71d69-311">Sınırı, <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> içindeki ayarı kullanarak özelleştirin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71d69-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="71d69-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>tek sayfa veya eylem için ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="71d69-313">Bir Razor Sayfalar uygulamasında, filtresi içindeki bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71d69-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="71d69-314">Bir Razor Sayfalar uygulamasında veya BIR MVC uygulamasında, filtreyi sayfa modeline veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="71d69-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="71d69-315">Kestrel maksimum istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="71d69-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="71d69-316">Kestrel tarafından barındırılan uygulamalar için, varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu, yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="71d69-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="71d69-317">[MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) Kestrel Server seçeneğini kullanarak sınırı özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="71d69-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="71d69-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> , tek sayfa veya eylem için [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) ayarlamak üzere kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="71d69-319">Bir Razor Sayfalar uygulamasında, filtresi içindeki bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71d69-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="71d69-320">Bir Razor Sayfalar uygulamasında veya BIR MVC uygulamasında, filtreyi sayfa işleyici sınıfına veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="71d69-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="71d69-321">, `RequestSizeLimitAttribute` Yönergesi kullanılarak da uygulanabilir [`@attribute`](xref:mvc/views/razor#attribute) Razor :</span><span class="sxs-lookup"><span data-stu-id="71d69-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="71d69-322">Diğer Kestrel limitleri</span><span class="sxs-lookup"><span data-stu-id="71d69-322">Other Kestrel limits</span></span>

<span data-ttu-id="71d69-323">Kestrel tarafından barındırılan uygulamalar için diğer Kestrel limitleri de uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="71d69-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="71d69-324">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="71d69-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel/options#maximum-client-connections)
* [<span data-ttu-id="71d69-325">İstek ve yanıt veri ücretleri</span><span class="sxs-lookup"><span data-stu-id="71d69-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel/options#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="71d69-326">IIS</span><span class="sxs-lookup"><span data-stu-id="71d69-326">IIS</span></span>

<span data-ttu-id="71d69-327">Varsayılan istek sınırı ( `maxAllowedContentLength` ), yaklaşık 28,6 MB olan 30.000.000 bayttır.</span><span class="sxs-lookup"><span data-stu-id="71d69-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="71d69-328">Dosyadaki sınırı özelleştirin `web.config` .</span><span class="sxs-lookup"><span data-stu-id="71d69-328">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="71d69-329">Aşağıdaki örnekte, sınır 50 MB (52.428.800 bayt) olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-329">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="71d69-330">`maxAllowedContentLength`Ayar yalnızca IIS için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-330">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="71d69-331">Daha fazla bilgi için bkz. [Istek `<requestLimits>` sınırları ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="71d69-331">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="71d69-332">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="71d69-332">Troubleshoot</span></span>

<span data-ttu-id="71d69-333">Dosyaları karşıya yükleme ve olası çözümleri ile çalışırken karşılaşılan bazı yaygın sorunlar aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="71d69-333">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="71d69-334">Bir IIS sunucusuna dağıtılırken bulunamadı hatası</span><span class="sxs-lookup"><span data-stu-id="71d69-334">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="71d69-335">Aşağıdaki hata karşıya yüklenen dosyanın, sunucunun yapılandırılmış içerik uzunluğunu aştığını gösterir:</span><span class="sxs-lookup"><span data-stu-id="71d69-335">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="71d69-336">Daha fazla bilgi için [IIS](#iis) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-336">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="71d69-337">Bağlantı hatası</span><span class="sxs-lookup"><span data-stu-id="71d69-337">Connection failure</span></span>

<span data-ttu-id="71d69-338">Bir bağlantı hatası ve bir sıfırlama sunucu bağlantısı büyük olasılıkla karşıya yüklenen dosyanın Kestrel 'in en büyük istek gövdesi boyutunu aştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-338">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="71d69-339">Daha fazla bilgi için, [Kestrel maksimum istek gövdesi boyutu](#kestrel-maximum-request-body-size) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-339">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="71d69-340">Kestrel istemci bağlantı limitleri de ayarlama gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-340">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="71d69-341">Iformfile ile null başvuru özel durumu</span><span class="sxs-lookup"><span data-stu-id="71d69-341">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="71d69-342">Denetleyici karşıya yüklenen dosyaları kullanarak kabul edilirse, <xref:Microsoft.AspNetCore.Http.IFormFile> ancak değer ise `null` , HTML formunun bir değerini belirtmesini onaylayın `enctype` `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="71d69-342">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="71d69-343">Bu öznitelik `<form>` öğesinde ayarlanmamışsa, dosya karşıya yükleme gerçekleşmez ve herhangi bir bağlı <xref:Microsoft.AspNetCore.Http.IFormFile> bağımsız değişken `null` .</span><span class="sxs-lookup"><span data-stu-id="71d69-343">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="71d69-344">Ayrıca, [form verilerinde karşıya yükleme adlandırmasının uygulamanın adlandırmayla eşleştiğinden](#match-name-attribute-value-to-parameter-name-of-post-method)emin olun.</span><span class="sxs-lookup"><span data-stu-id="71d69-344">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="71d69-345">Akış çok uzun</span><span class="sxs-lookup"><span data-stu-id="71d69-345">Stream was too long</span></span>

<span data-ttu-id="71d69-346">Bu konudaki örnekler <xref:System.IO.MemoryStream> karşıya yüklenen dosyanın içeriğini tutmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="71d69-346">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="71d69-347">Bir öğesinin boyut sınırı `MemoryStream` `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="71d69-347">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="71d69-348">Uygulamanın dosya yükleme senaryosu, dosya içeriğinin 50 MB 'tan büyük olmasını gerektiriyorsa, `MemoryStream` karşıya yüklenen bir dosyanın içeriğini tutmak için tek başına olmayan alternatif bir yaklaşım kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-348">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="71d69-349">ASP.NET Core, daha küçük dosyalar için arabellekli model bağlama ve daha büyük dosyalar için arabelleğe alınmamış akış kullanarak bir veya daha fazla dosyanın yüklenmesini destekler</span><span class="sxs-lookup"><span data-stu-id="71d69-349">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="71d69-350">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71d69-350">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="71d69-351">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="71d69-351">Security considerations</span></span>

<span data-ttu-id="71d69-352">Kullanıcılara bir sunucuya dosya yükleme yeteneği sağlarken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="71d69-352">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="71d69-353">Saldırganlar şunları deneyebilir:</span><span class="sxs-lookup"><span data-stu-id="71d69-353">Attackers may attempt to:</span></span>

* <span data-ttu-id="71d69-354">[Hizmet reddi](/windows-hardware/drivers/ifs/denial-of-service) saldırıları yürütün.</span><span class="sxs-lookup"><span data-stu-id="71d69-354">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="71d69-355">Virüsleri veya kötü amaçlı yazılımları karşıya yükleyin.</span><span class="sxs-lookup"><span data-stu-id="71d69-355">Upload viruses or malware.</span></span>
* <span data-ttu-id="71d69-356">Ağları ve sunucuları diğer yollarla tehlikeye atabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-356">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="71d69-357">Başarılı bir saldırının olasılığını azaltan güvenlik adımları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="71d69-357">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="71d69-358">Dosyaları, tercihen sistem dışı bir sürücüye, özel bir dosya yükleme alanına yükleyin.</span><span class="sxs-lookup"><span data-stu-id="71d69-358">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="71d69-359">Ayrılmış bir konum, karşıya yüklenen dosyalar üzerinde güvenlik kısıtlamaları yapmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="71d69-359">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="71d69-360">Dosya yükleme konumunda yürütme izinlerini devre dışı bırakın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="71d69-360">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="71d69-361">Karşıya yüklenen dosyaları uygulamayla aynı dizin **ağacında kalıcı hale** getirme.&dagger;</span><span class="sxs-lookup"><span data-stu-id="71d69-361">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="71d69-362">Uygulama tarafından belirlenen bir güvenli dosya adı kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-362">Use a safe file name determined by the app.</span></span> <span data-ttu-id="71d69-363">Kullanıcı tarafından belirtilen bir dosya adı veya karşıya yüklenen dosyanın güvenilmeyen dosya adı kullanmayın. &dagger; HTML, görüntüleme sırasında güvenilmeyen dosya adını kodlayamıyor.</span><span class="sxs-lookup"><span data-stu-id="71d69-363">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="71d69-364">Örneğin, dosya adını günlüğe kaydetme veya Kullanıcı arabiriminde görüntüleme ( Razor otomatik olarak HTML kodlama çıktısı).</span><span class="sxs-lookup"><span data-stu-id="71d69-364">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="71d69-365">Uygulamanın tasarım belirtimi için yalnızca onaylanan dosya uzantılarına izin verin.&dagger;</span><span class="sxs-lookup"><span data-stu-id="71d69-365">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="71d69-366">Sunucuda istemci tarafı denetimlerinin gerçekleştirildiğinden emin olun. &dagger; İstemci tarafı denetimleri kolayca atmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="71d69-366">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="71d69-367">Karşıya yüklenen dosyanın boyutunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="71d69-367">Check the size of an uploaded file.</span></span> <span data-ttu-id="71d69-368">Büyük karşıya yüklemeleri engellemek için en büyük boyut sınırını ayarlayın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="71d69-368">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="71d69-369">Aynı ada sahip karşıya yüklenen bir dosya tarafından dosyaların üzerine yazılmaması gerektiğinde, dosyayı karşıya yüklemeden önce dosya adını veritabanına veya fiziksel depolamaya göre denetleyin.</span><span class="sxs-lookup"><span data-stu-id="71d69-369">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="71d69-370">**Dosya depolanmadan önce karşıya yüklenen içerik üzerinde bir virüs/kötü amaçlı yazılım tarayıcısı çalıştırın.**</span><span class="sxs-lookup"><span data-stu-id="71d69-370">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="71d69-371">&dagger;Örnek uygulama, ölçütlere uyan bir yaklaşımı gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-371">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="71d69-372">Kötü amaçlı kodun bir sisteme yüklenmesi genellikle şu şekilde kod yürütmenin ilk adımıdır:</span><span class="sxs-lookup"><span data-stu-id="71d69-372">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="71d69-373">Sistemin denetimini tamamen elde edin.</span><span class="sxs-lookup"><span data-stu-id="71d69-373">Completely gain control of a system.</span></span>
> * <span data-ttu-id="71d69-374">Sistemin kilitlenme sonucuyla bir sistemi aşırı yükleme.</span><span class="sxs-lookup"><span data-stu-id="71d69-374">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="71d69-375">Kullanıcı veya Sistem verilerinin güvenliğini tehlikeye atabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-375">Compromise user or system data.</span></span>
> * <span data-ttu-id="71d69-376">Genel Kullanıcı arabirimine Graffiti uygulayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-376">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="71d69-377">Kullanıcılardan dosya kabul edilirken saldırı yüzeyi alanını azaltma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="71d69-377">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="71d69-378">Kısıtlanmamış dosya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-378">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="71d69-379">Azure güvenliği: kullanıcılardan dosya kabul edilirken uygun denetimlerin yerinde olduğundan emin olun</span><span class="sxs-lookup"><span data-stu-id="71d69-379">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="71d69-380">Örnek uygulamadaki örnekler de dahil olmak üzere güvenlik önlemlerini uygulama hakkında daha fazla bilgi için [doğrulama](#validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-380">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="71d69-381">Depolama senaryoları</span><span class="sxs-lookup"><span data-stu-id="71d69-381">Storage scenarios</span></span>

<span data-ttu-id="71d69-382">Dosyalar için ortak depolama seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="71d69-382">Common storage options for files include:</span></span>

* <span data-ttu-id="71d69-383">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="71d69-383">Database</span></span>

  * <span data-ttu-id="71d69-384">Küçük dosya yüklemeleri için, bir veritabanı genellikle fiziksel depolama (dosya sistemi veya ağ paylaşımından) seçeneklerinden daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="71d69-384">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="71d69-385">Kullanıcı verileri için bir veritabanı kaydının alınması eşzamanlı olarak dosya içeriğini (örneğin, avatar görüntüsü) sağlayabildiğinden, veritabanı genellikle fiziksel depolama seçeneklerine göre daha uygundur.</span><span class="sxs-lookup"><span data-stu-id="71d69-385">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="71d69-386">Bir veritabanı, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-386">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="71d69-387">Fiziksel depolama (dosya sistemi veya ağ paylaşma)</span><span class="sxs-lookup"><span data-stu-id="71d69-387">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="71d69-388">Büyük dosya yüklemeleri için:</span><span class="sxs-lookup"><span data-stu-id="71d69-388">For large file uploads:</span></span>
    * <span data-ttu-id="71d69-389">Veritabanı limitleri karşıya yükleme boyutunu kısıtlayabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-389">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="71d69-390">Fiziksel depolama genellikle bir veritabanındaki depolamadan daha az ekonomik olur.</span><span class="sxs-lookup"><span data-stu-id="71d69-390">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="71d69-391">Fiziksel depolama, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-391">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="71d69-392">Uygulamanın işlemi, depolama konumu için okuma ve yazma izinlerine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="71d69-392">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="71d69-393">**Hiçbir zaman yürütme izni vermeyin.**</span><span class="sxs-lookup"><span data-stu-id="71d69-393">**Never grant execute permission.**</span></span>

* <span data-ttu-id="71d69-394">Veri depolama hizmeti (örneğin, [Azure Blob depolama](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="71d69-394">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="71d69-395">Hizmetler genellikle tek hata noktalarına tabi olan şirket içi çözümler üzerinde geliştirilmiş ölçeklenebilirlik ve esneklik sunar.</span><span class="sxs-lookup"><span data-stu-id="71d69-395">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="71d69-396">Hizmetler büyük depolama altyapısı senaryolarında düşük maliyetlidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-396">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="71d69-397">Daha fazla bilgi için bkz. [hızlı başlangıç: nesne depolamada blob oluşturmak için .NET kullanma](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="71d69-397">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="71d69-398">Karşıya dosya yükleme senaryoları</span><span class="sxs-lookup"><span data-stu-id="71d69-398">File upload scenarios</span></span>

<span data-ttu-id="71d69-399">Dosyaları karşıya yüklemek için iki genel yaklaşım arabelleğe alınır ve akışlardır.</span><span class="sxs-lookup"><span data-stu-id="71d69-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="71d69-400">**Ara**</span><span class="sxs-lookup"><span data-stu-id="71d69-400">**Buffering**</span></span>

<span data-ttu-id="71d69-401">Dosyanın tamamı <xref:Microsoft.AspNetCore.Http.IFormFile> , dosyayı işlemek veya kaydetmek için kullanılan dosyanın C# temsili olan öğesine okunurdur.</span><span class="sxs-lookup"><span data-stu-id="71d69-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="71d69-402">Dosya karşıya yüklemeleri tarafından kullanılan kaynaklar (disk, bellek), eşzamanlı dosya karşıya yüklemelerinin sayısına ve boyutuna bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="71d69-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="71d69-403">Bir uygulama çok fazla karşıya yükleme arabelleğini denerse, bellek veya disk alanı tükenirse site çöker.</span><span class="sxs-lookup"><span data-stu-id="71d69-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="71d69-404">Karşıya dosya yükleme boyutu veya sıklığı uygulama kaynaklarını tüketme ise, akış ' ı kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="71d69-405">64 KB geçen tek bir arabelleğe alınmış dosya, bellekten diskte geçici bir dosyaya taşınır.</span><span class="sxs-lookup"><span data-stu-id="71d69-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="71d69-406">Dosya arabelleğe alma, bu konunun aşağıdaki bölümlerinde ele alınmıştır:</span><span class="sxs-lookup"><span data-stu-id="71d69-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="71d69-407">Fiziksel depolama alanı</span><span class="sxs-lookup"><span data-stu-id="71d69-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="71d69-408">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="71d69-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="71d69-409">**Akışlar**</span><span class="sxs-lookup"><span data-stu-id="71d69-409">**Streaming**</span></span>

<span data-ttu-id="71d69-410">Dosya çok parçalı bir istekten alınır ve doğrudan uygulama tarafından işlenir veya kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="71d69-411">Akış performansı önemli ölçüde iyileştirmez.</span><span class="sxs-lookup"><span data-stu-id="71d69-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="71d69-412">Akış, dosya karşıya yüklenirken bellek veya disk alanı taleplerini azaltır.</span><span class="sxs-lookup"><span data-stu-id="71d69-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="71d69-413">Akış büyük dosyaları [akış ile büyük dosyaları karşıya yükle](#upload-large-files-with-streaming) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="71d69-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="71d69-414">Fiziksel depolamaya arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="71d69-415">Küçük dosyaları karşıya yüklemek için, çok parçalı bir form kullanın veya JavaScript kullanarak bir POST isteği oluşturun.</span><span class="sxs-lookup"><span data-stu-id="71d69-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="71d69-416">Aşağıdaki örnek, Razor tek bir dosyayı karşıya yüklemek için sayfalar formunun kullanımını gösterir (örnek uygulamada *Pages/Bufferedsinglefileuploadfiziksel. cshtml* ):</span><span class="sxs-lookup"><span data-stu-id="71d69-416">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="71d69-417">Aşağıdaki örnek, önceki örneğe benzerdir, ancak şunları hariç:</span><span class="sxs-lookup"><span data-stu-id="71d69-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="71d69-418">Form verilerini göndermek için JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="71d69-419">Doğrulama yok.</span><span class="sxs-lookup"><span data-stu-id="71d69-419">There's no validation.</span></span>

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

<span data-ttu-id="71d69-420">[Fetch API 'sini desteklemeyen](https://caniuse.com/#feat=fetch)istemcilerde form gönderisini JavaScript 'te gerçekleştirmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="71d69-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="71d69-421">Fetch Polyfill kullanın (örneğin, [Window. Fetch (GitHub/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="71d69-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="71d69-422">`XMLHttpRequest` komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="71d69-423">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="71d69-423">For example:</span></span>

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

<span data-ttu-id="71d69-424">Dosya yüklemelerini desteklemek için, HTML formları bir kodlama türü ( `enctype` ) belirtmelidir `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="71d69-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="71d69-425">`files`Birden çok dosyayı karşıya yüklemeyi destekleyen bir giriş öğesi için, `multiple` öğesine özniteliği sağlar `<input>` :</span><span class="sxs-lookup"><span data-stu-id="71d69-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="71d69-426">Sunucuya yüklenen tek dosyalara, kullanılarak [model bağlama](xref:mvc/models/model-binding) yoluyla erişilebilir <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="71d69-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="71d69-427">Örnek uygulama, veritabanı ve fiziksel depolama senaryoları için birden çok arabellekli dosya yüklemeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="71d69-428"> `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> Görüntüleme ve günlüğe kaydetme için dışındaki özelliğini kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="71d69-429">Görüntüleme veya günlüğe kaydetme sırasında, HTML dosya adını kodlayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="71d69-430">Saldırgan, tam yollar veya göreli yollar dahil olmak üzere kötü amaçlı bir dosya adı sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="71d69-431">Uygulamalar:</span><span class="sxs-lookup"><span data-stu-id="71d69-431">Applications should:</span></span>
>
> * <span data-ttu-id="71d69-432">Kullanıcı tarafından sağlanan dosya adının yolunu kaldırın.</span><span class="sxs-lookup"><span data-stu-id="71d69-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="71d69-433">Kullanıcı arabirimi veya günlüğe kaydetme için HTML kodlu, yol tarafından kaldırılan dosya adını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="71d69-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="71d69-434">Depolama için yeni bir rastgele dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="71d69-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="71d69-435">Aşağıdaki kod, dosya adından yolu kaldırır:</span><span class="sxs-lookup"><span data-stu-id="71d69-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="71d69-436">Bu nedenle, şu ana kadar dikkate alınması gereken örnekler aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="71d69-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="71d69-437">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-437">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="71d69-438">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="71d69-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="71d69-439">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="71d69-439">Validation</span></span>](#validation)

<span data-ttu-id="71d69-440">Model bağlama kullanarak dosyaları karşıya yüklerken <xref:Microsoft.AspNetCore.Http.IFormFile> , eylem yöntemi kabul edebilir:</span><span class="sxs-lookup"><span data-stu-id="71d69-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="71d69-441">Tek bir <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="71d69-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="71d69-442">Birkaç dosyayı temsil eden aşağıdaki koleksiyonlardan herhangi biri:</span><span class="sxs-lookup"><span data-stu-id="71d69-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="71d69-443">[Listele](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="71d69-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="71d69-444">Bağlama, form dosyaları adına göre eşleşir.</span><span class="sxs-lookup"><span data-stu-id="71d69-444">Binding matches form files by name.</span></span> <span data-ttu-id="71d69-445">Örneğin, `name` IÇINDEKI HTML değeri `<input type="file" name="formFile">` C# parametresi/özelliği ile ( `FormFile` ) eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="71d69-446">Daha fazla bilgi için, [ad öznitelik DEĞERINI Post yönteminin parametre adına eşleştirin](#match-name-attribute-value-to-parameter-name-of-post-method) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="71d69-447">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="71d69-447">The following example:</span></span>

* <span data-ttu-id="71d69-448">Karşıya yüklenen bir veya daha fazla dosya üzerinden döngü.</span><span class="sxs-lookup"><span data-stu-id="71d69-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="71d69-449">Dosya adı da dahil olmak üzere bir dosyanın tam yolunu döndürmek için [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanır.</span><span class="sxs-lookup"><span data-stu-id="71d69-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="71d69-450">Dosyalar, uygulama tarafından oluşturulan bir dosya adı kullanılarak yerel dosya sistemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="71d69-451">Karşıya yüklenen dosyaların toplam sayısını ve boyutunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="71d69-451">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="71d69-452">`Path.GetRandomFileName`Yol olmadan bir dosya adı oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="71d69-453">Aşağıdaki örnekte, yol yapılandırmadan alınır:</span><span class="sxs-lookup"><span data-stu-id="71d69-453">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="71d69-454">Öğesine geçirilen yol, <xref:System.IO.FileStream>  dosya adını içermelidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="71d69-455">Dosya adı sağlanmazsa, çalışma zamanında bir oluşturulur <xref:System.UnauthorizedAccessException> .</span><span class="sxs-lookup"><span data-stu-id="71d69-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="71d69-456">Tekniği kullanılarak yüklenen dosyalar, <xref:Microsoft.AspNetCore.Http.IFormFile> işlemeden önce sunucuda veya diskte bellek halinde arabelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="71d69-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="71d69-457">Eylem yönteminde, <xref:Microsoft.AspNetCore.Http.IFormFile> içeriğe bir olarak erişilebilir <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="71d69-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="71d69-458">Yerel dosya sistemine ek olarak, dosyalar bir ağ paylaşımında veya [Azure Blob depolama](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)gibi bir dosya depolama hizmetine kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="71d69-459">Karşıya yüklemek için birden çok dosya üzerinde döngü yapan ve güvenli dosya adları kullanan başka bir örnek için, örnek uygulamadaki *Pages/Bufferedmultiplefileuploadfiziksel. cshtml. cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="71d69-460">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) <xref:System.IO.IOException> , önceki geçici dosyaları silmeden 65.535 ' den fazla dosya oluşturulduysa bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="71d69-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="71d69-461">65.535 dosya sınırının sunucu başına sınırı vardır.</span><span class="sxs-lookup"><span data-stu-id="71d69-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="71d69-462">Windows işletim sistemi için bu sınır hakkında daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="71d69-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="71d69-463">GetTempFileNameA işlevi</span><span class="sxs-lookup"><span data-stu-id="71d69-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="71d69-464">Bir veritabanına arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="71d69-465">İkili dosya verilerini [Entity Framework](/ef/core/index)kullanarak bir veritabanında depolamak için, <xref:System.Byte> varlıkta bir dizi özelliği tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="71d69-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="71d69-466">Sınıf için şunu içeren bir sayfa modeli özelliği belirtin <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="71d69-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="71d69-467"><xref:Microsoft.AspNetCore.Http.IFormFile> doğrudan bir eylem yöntemi parametresi veya bir bağlantılı model özelliği olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="71d69-468">Önceki örnekte, bir bağlantılı model özelliği kullanılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="71d69-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="71d69-469">, `FileUpload` Razor Sayfalar formunda kullanılır:</span><span class="sxs-lookup"><span data-stu-id="71d69-469">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="71d69-470">Form sunucuya gönderildiğinde, öğesini <xref:Microsoft.AspNetCore.Http.IFormFile> bir akışa kopyalayın ve veritabanına bir bayt dizisi olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="71d69-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="71d69-471">Aşağıdaki örnekte, `_dbContext` uygulamanın veritabanı bağlamını depolar:</span><span class="sxs-lookup"><span data-stu-id="71d69-471">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="71d69-472">Yukarıdaki örnek, örnek uygulamada gösterilen senaryoya benzerdir:</span><span class="sxs-lookup"><span data-stu-id="71d69-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="71d69-473">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="71d69-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="71d69-474">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="71d69-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="71d69-475">İkili verileri ilişkisel veritabanlarında depolarken dikkatli olun, çünkü performansı olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="71d69-476">Doğrulaması olmadan özelliğine güvenmeyin veya bu `FileName` özelliğe güvenmeyin <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="71d69-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="71d69-477">`FileName`Özelliği yalnızca görüntüleme amacıyla ve yalnızca HTML kodlaması sonrasında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="71d69-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="71d69-478">Belirtilen örneklerde dikkate alınması gereken önemli noktalar.</span><span class="sxs-lookup"><span data-stu-id="71d69-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="71d69-479">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-479">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="71d69-480">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="71d69-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="71d69-481">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="71d69-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="71d69-482">Akışa sahip büyük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-482">Upload large files with streaming</span></span>

<span data-ttu-id="71d69-483">Aşağıdaki örnek, bir denetleyiciyi bir denetleyici eyleminde akışa almak için JavaScript 'in nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="71d69-484">Dosyanın antiforgery belirteci özel bir filtre özniteliği kullanılarak oluşturulur ve istek gövdesi yerine istemci HTTP üst bilgilerine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="71d69-485">Eylem yöntemi karşıya yüklenen verileri doğrudan işlediğinden, form modeli bağlama başka bir özel filtre tarafından devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="71d69-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="71d69-486">Eylem içinde formun içerikleri, `MultipartReader` her bir bireyi okuyan `MultipartSection` , dosyayı işleyen veya içeriği uygun şekilde depolayan bir kullanılarak okunur.</span><span class="sxs-lookup"><span data-stu-id="71d69-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="71d69-487">Çok parçalı bölümler okunduktan sonra eylem kendi model bağlamasını gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="71d69-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="71d69-488">İlk sayfa yanıtı formu yükler ve bir cookie (özniteliği aracılığıyla) bir antiforgery belirtecini kaydeder `GenerateAntiforgeryTokenCookieAttribute` .</span><span class="sxs-lookup"><span data-stu-id="71d69-488">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="71d69-489">Özniteliği, bir istek belirteci ile ayarlamak için ASP.NET Core yerleşik [antiforgery desteğini](xref:security/anti-request-forgery) kullanır cookie :</span><span class="sxs-lookup"><span data-stu-id="71d69-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="71d69-490">`DisableFormValueModelBindingAttribute`Model bağlamayı devre dışı bırakmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="71d69-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="71d69-491">Örnek uygulamada `GenerateAntiforgeryTokenCookieAttribute` ve `DisableFormValueModelBindingAttribute` sayfa `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor kuralları](xref:razor-pages/razor-pages-conventions)kullanılarak ve içindeki sayfa uygulama modellerine filtre olarak uygulanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-491">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="71d69-492">Model bağlama formu okumadığından formdan bağlanan parametreler bağlanamaz (sorgu, yol ve başlık çalışmaya devam eder).</span><span class="sxs-lookup"><span data-stu-id="71d69-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="71d69-493">Action yöntemi doğrudan özelliği ile birlikte çalışabilir `Request` .</span><span class="sxs-lookup"><span data-stu-id="71d69-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="71d69-494">`MultipartReader`Her bölümü okumak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="71d69-495">Anahtar/değer verileri bir içinde depolanır `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="71d69-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="71d69-496">Çok parçalı bölümler okunduktan sonra, öğesinin içeriği `KeyValueAccumulator` form verilerini bir model türüne bağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="71d69-497">`StreamingController.UploadDatabase`EF Core bir veritabanına akış için tamamlanan Yöntem:</span><span class="sxs-lookup"><span data-stu-id="71d69-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="71d69-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="71d69-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="71d69-499">`StreamingController.UploadPhysical`Fiziksel bir konuma akışa yönelik tüm Yöntem:</span><span class="sxs-lookup"><span data-stu-id="71d69-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="71d69-500">Örnek uygulamada, doğrulama denetimleri tarafından işlenir `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="71d69-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="71d69-501">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="71d69-501">Validation</span></span>

<span data-ttu-id="71d69-502">Örnek uygulamanın sınıfı, `FileHelpers` arabelleğe alınmış <xref:Microsoft.AspNetCore.Http.IFormFile> ve akış dosya yüklemeleri için birkaç denetim gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="71d69-503"><xref:Microsoft.AspNetCore.Http.IFormFile>Örnek uygulamada ara belleğe alınmış dosya yüklemelerini işlemek için, `ProcessFormFile` *Utilities/fileyardımcılar. cs* dosyasındaki yöntemine bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="71d69-504">Akış dosyalarını işlemek için `ProcessStreamedFile` aynı dosyadaki yöntemine bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="71d69-505">Örnek uygulamada gösterilen doğrulama işleme yöntemleri karşıya yüklenen dosyaların içeriğini taramaz.</span><span class="sxs-lookup"><span data-stu-id="71d69-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="71d69-506">Çoğu üretim senaryosunda, dosyanın kullanıcılara veya diğer sistemlere kullanılabilir hale getirilmesi için dosya üzerinde bir virüs/kötü amaçlı yazılım tarayıcı API 'SI kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="71d69-507">Konu örneği, doğrulama tekniklerine yönelik çalışan bir örnek sağlasa da, `FileHelpers` aşağıdakileri gerçekleştirmediğiniz takdirde sınıfı bir üretim uygulamasında uygulamaz:</span><span class="sxs-lookup"><span data-stu-id="71d69-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="71d69-508">Uygulamayı tam olarak anlayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="71d69-509">Uygulamayı uygulamanın ortamı ve belirtimleri için uygun şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="71d69-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="71d69-510">**Bu gereksinimleri bilmeden bir uygulamada güvenlik kodunu hiçbir şekilde sayısının fark gözetmeden uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="71d69-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="71d69-511">İçerik doğrulama</span><span class="sxs-lookup"><span data-stu-id="71d69-511">Content validation</span></span>

<span data-ttu-id="71d69-512">**Karşıya yüklenen içerikte üçüncü taraf bir virüs/kötü amaçlı yazılım tarama API 'SI kullanın.**</span><span class="sxs-lookup"><span data-stu-id="71d69-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="71d69-513">Dosyaları tarama, yüksek hacimli senaryolarda sunucu kaynaklarında yoğun bir şekilde yapılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="71d69-514">Dosya tarama nedeniyle istek işleme performansı azaldığında, tarama işini, muhtemelen uygulamanın sunucusundan farklı bir sunucuda çalışan bir [arka plan hizmetine](xref:fundamentals/host/hosted-services)devredere göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="71d69-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="71d69-515">Genellikle, arka plan virüs tarayıcısı tarafından denetlene kadar karşıya yüklenen dosyalar karantinaya alınmış bir alanda tutulur.</span><span class="sxs-lookup"><span data-stu-id="71d69-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="71d69-516">Bir dosya geçtiğinde dosya normal dosya depolama konumuna taşınır.</span><span class="sxs-lookup"><span data-stu-id="71d69-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="71d69-517">Bu adımlar genellikle bir dosyanın tarama durumunu gösteren bir veritabanı kaydıyla birlikte gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="71d69-518">Böyle bir yaklaşım kullanarak, uygulama ve uygulama sunucusu isteklere yanıt vermeye odaklanmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="71d69-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="71d69-519">Dosya Uzantısı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="71d69-519">File extension validation</span></span>

<span data-ttu-id="71d69-520">Karşıya yüklenen dosyanın uzantısı izin verilen uzantılar listesine göre denetlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="71d69-521">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="71d69-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="71d69-522">Dosya imzası doğrulaması</span><span class="sxs-lookup"><span data-stu-id="71d69-522">File signature validation</span></span>

<span data-ttu-id="71d69-523">Bir dosyanın imzası, bir dosyanın başlangıcında ilk birkaç bayta göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="71d69-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="71d69-524">Bu baytlar, uzantının dosyanın içeriğiyle eşleşip eşleşmediğini göstermek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="71d69-525">Örnek uygulama, birkaç ortak dosya türü için dosya imzalarını denetler.</span><span class="sxs-lookup"><span data-stu-id="71d69-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="71d69-526">Aşağıdaki örnekte, bir JPEG görüntüsünün dosya imzası, dosyaya karşı denetlenir:</span><span class="sxs-lookup"><span data-stu-id="71d69-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="71d69-527">Ek dosya imzaları almak için [Dosya Imzaları veritabanı](https://www.filesignatures.net/) ve resmi dosya belirtimleri bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="71d69-528">Dosya adı güvenliği</span><span class="sxs-lookup"><span data-stu-id="71d69-528">File name security</span></span>

<span data-ttu-id="71d69-529">Fiziksel depolamaya bir dosyayı kaydetmek için hiçbir şekilde istemci tarafından sağlanan dosya adı kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="71d69-530">Geçici depolama için tam yol (dosya adı da dahil olmak üzere) oluşturmak için [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) veya [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanarak dosya için güvenli bir dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="71d69-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="71d69-531">Razor Otomatik HTML, görüntüleme için özellik değerlerini kodluyor.</span><span class="sxs-lookup"><span data-stu-id="71d69-531">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="71d69-532">Aşağıdaki kodun kullanımı güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="71d69-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="71d69-533">Dışında Razor , her zaman <xref:System.Net.WebUtility.HtmlEncode*> bir kullanıcının isteğinden dosya adı içeriği.</span><span class="sxs-lookup"><span data-stu-id="71d69-533">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="71d69-534">Birçok uygulama, dosyanın var olduğunu bir denetim içermelidir; Aksi takdirde, dosyanın üzerine aynı ada sahip bir dosya yazılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="71d69-535">Uygulamanızın belirtimlerini karşılamak için ek mantık sağlayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="71d69-536">Boyut doğrulaması</span><span class="sxs-lookup"><span data-stu-id="71d69-536">Size validation</span></span>

<span data-ttu-id="71d69-537">Karşıya yüklenen dosyaların boyutunu sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="71d69-538">Örnek uygulamada, dosyanın boyutu 2 MB ile sınırlıdır (bayt cinsinden gösterilir).</span><span class="sxs-lookup"><span data-stu-id="71d69-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="71d69-539">Sınır, dosyadaki [yapılandırma](xref:fundamentals/configuration/index) yoluyla sağlanır *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="71d69-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="71d69-540">, `FileSizeLimit` `PageModel` Sınıflara eklenmiş:</span><span class="sxs-lookup"><span data-stu-id="71d69-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="71d69-541">Dosya boyutu sınırı aştığında, dosya reddedilir:</span><span class="sxs-lookup"><span data-stu-id="71d69-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="71d69-542">Name öznitelik değerini POST yönteminin Parameter adı ile Eşleştir</span><span class="sxs-lookup"><span data-stu-id="71d69-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="71d69-543">RazorForm verileri oluşturan veya JavaScript 'in `FormData` doğrudan kullandığı, form öğesinde belirtilen adın veya `FormData` denetleyicinin eyleminde parametrenin adıyla eşleşmesi gereken form olmayan formlar.</span><span class="sxs-lookup"><span data-stu-id="71d69-543">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="71d69-544">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="71d69-544">In the following example:</span></span>

* <span data-ttu-id="71d69-545">Bir öğesi kullanılırken `<input>` , `name` özniteliği değere ayarlanır `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="71d69-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="71d69-546">`FormData`JavaScript içinde kullanırken, ad değerine ayarlanır `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="71d69-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="71d69-547">C# yönteminin () parametresi için eşleşen bir ad kullanın `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="71d69-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="71d69-548">Bir Razor sayfalar sayfa işleyici yöntemi için şunu `Upload` adlı:</span><span class="sxs-lookup"><span data-stu-id="71d69-548">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="71d69-549">MVC POST denetleyicisi eylem yöntemi için:</span><span class="sxs-lookup"><span data-stu-id="71d69-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="71d69-550">Sunucu ve uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="71d69-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="71d69-551">Çok parçalı gövde uzunluğu sınırı</span><span class="sxs-lookup"><span data-stu-id="71d69-551">Multipart body length limit</span></span>

<span data-ttu-id="71d69-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Her bir çok parçalı gövdenin uzunluk sınırını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="71d69-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="71d69-553">Bu sınırı aşan form bölümleri <xref:System.IO.InvalidDataException> ayrıştırıldığında bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="71d69-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="71d69-554">Varsayılan değer 134.217.728 ' dir (128 MB).</span><span class="sxs-lookup"><span data-stu-id="71d69-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="71d69-555">Sınırı, <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> içindeki ayarı kullanarak özelleştirin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71d69-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="71d69-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>tek sayfa veya eylem için ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="71d69-557">Bir Razor Sayfalar uygulamasında, filtresi içindeki bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71d69-557">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="71d69-558">Bir Razor Sayfalar uygulamasında veya BIR MVC uygulamasında, filtreyi sayfa modeline veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="71d69-558">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="71d69-559">Kestrel maksimum istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="71d69-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="71d69-560">Kestrel tarafından barındırılan uygulamalar için, varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu, yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="71d69-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="71d69-561">[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel Server seçeneğini kullanarak sınırı özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="71d69-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="71d69-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> , tek sayfa veya eylem için [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) ayarlamak üzere kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="71d69-563">Bir Razor Sayfalar uygulamasında, filtresi içindeki bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71d69-563">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="71d69-564">Bir Razor Sayfalar uygulamasında veya BIR MVC uygulamasında, filtreyi sayfa işleyici sınıfına veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="71d69-564">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="71d69-565">, `RequestSizeLimitAttribute` Yönergesi kullanılarak da uygulanabilir [`@attribute`](xref:mvc/views/razor#attribute) Razor :</span><span class="sxs-lookup"><span data-stu-id="71d69-565">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="71d69-566">Diğer Kestrel limitleri</span><span class="sxs-lookup"><span data-stu-id="71d69-566">Other Kestrel limits</span></span>

<span data-ttu-id="71d69-567">Kestrel tarafından barındırılan uygulamalar için diğer Kestrel limitleri de uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="71d69-567">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="71d69-568">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="71d69-568">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="71d69-569">İstek ve yanıt veri ücretleri</span><span class="sxs-lookup"><span data-stu-id="71d69-569">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="71d69-570">IIS</span><span class="sxs-lookup"><span data-stu-id="71d69-570">IIS</span></span>

<span data-ttu-id="71d69-571">Varsayılan istek sınırı ( `maxAllowedContentLength` ), yaklaşık 28,6 MB olan 30.000.000 bayttır.</span><span class="sxs-lookup"><span data-stu-id="71d69-571">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="71d69-572">Dosyadaki sınırı özelleştirin `web.config` .</span><span class="sxs-lookup"><span data-stu-id="71d69-572">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="71d69-573">Aşağıdaki örnekte, sınır 50 MB (52.428.800 bayt) olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-573">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="71d69-574">`maxAllowedContentLength`Ayar yalnızca IIS için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-574">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="71d69-575">Daha fazla bilgi için bkz. [Istek `<requestLimits>` sınırları ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="71d69-575">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="71d69-576">HTTP isteğinin en büyük istek gövdesi boyutunu <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> ' de ayarlayarak artırın `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="71d69-576">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="71d69-577">Aşağıdaki örnekte, sınır 50 MB (52.428.800 bayt) olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-577">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="71d69-578">Daha fazla bilgi için bkz. <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="71d69-578">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="71d69-579">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="71d69-579">Troubleshoot</span></span>

<span data-ttu-id="71d69-580">Dosyaları karşıya yükleme ve olası çözümleri ile çalışırken karşılaşılan bazı yaygın sorunlar aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="71d69-580">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="71d69-581">Bir IIS sunucusuna dağıtılırken bulunamadı hatası</span><span class="sxs-lookup"><span data-stu-id="71d69-581">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="71d69-582">Aşağıdaki hata karşıya yüklenen dosyanın, sunucunun yapılandırılmış içerik uzunluğunu aştığını gösterir:</span><span class="sxs-lookup"><span data-stu-id="71d69-582">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="71d69-583">Daha fazla bilgi için [IIS](#iis) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-583">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="71d69-584">Bağlantı hatası</span><span class="sxs-lookup"><span data-stu-id="71d69-584">Connection failure</span></span>

<span data-ttu-id="71d69-585">Bir bağlantı hatası ve bir sıfırlama sunucu bağlantısı büyük olasılıkla karşıya yüklenen dosyanın Kestrel 'in en büyük istek gövdesi boyutunu aştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-585">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="71d69-586">Daha fazla bilgi için, [Kestrel maksimum istek gövdesi boyutu](#kestrel-maximum-request-body-size) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-586">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="71d69-587">Kestrel istemci bağlantı limitleri de ayarlama gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-587">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="71d69-588">Iformfile ile null başvuru özel durumu</span><span class="sxs-lookup"><span data-stu-id="71d69-588">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="71d69-589">Denetleyici karşıya yüklenen dosyaları kullanarak kabul edilirse, <xref:Microsoft.AspNetCore.Http.IFormFile> ancak değer ise `null` , HTML formunun bir değerini belirtmesini onaylayın `enctype` `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="71d69-589">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="71d69-590">Bu öznitelik `<form>` öğesinde ayarlanmamışsa, dosya karşıya yükleme gerçekleşmez ve herhangi bir bağlı <xref:Microsoft.AspNetCore.Http.IFormFile> bağımsız değişken `null` .</span><span class="sxs-lookup"><span data-stu-id="71d69-590">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="71d69-591">Ayrıca, [form verilerinde karşıya yükleme adlandırmasının uygulamanın adlandırmayla eşleştiğinden](#match-name-attribute-value-to-parameter-name-of-post-method)emin olun.</span><span class="sxs-lookup"><span data-stu-id="71d69-591">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="71d69-592">Akış çok uzun</span><span class="sxs-lookup"><span data-stu-id="71d69-592">Stream was too long</span></span>

<span data-ttu-id="71d69-593">Bu konudaki örnekler <xref:System.IO.MemoryStream> karşıya yüklenen dosyanın içeriğini tutmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="71d69-593">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="71d69-594">Bir öğesinin boyut sınırı `MemoryStream` `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="71d69-594">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="71d69-595">Uygulamanın dosya yükleme senaryosu, dosya içeriğinin 50 MB 'tan büyük olmasını gerektiriyorsa, `MemoryStream` karşıya yüklenen bir dosyanın içeriğini tutmak için tek başına olmayan alternatif bir yaklaşım kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-595">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71d69-596">ASP.NET Core, daha küçük dosyalar için arabellekli model bağlama ve daha büyük dosyalar için arabelleğe alınmamış akış kullanarak bir veya daha fazla dosyanın yüklenmesini destekler</span><span class="sxs-lookup"><span data-stu-id="71d69-596">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="71d69-597">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71d69-597">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="71d69-598">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="71d69-598">Security considerations</span></span>

<span data-ttu-id="71d69-599">Kullanıcılara bir sunucuya dosya yükleme yeteneği sağlarken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="71d69-599">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="71d69-600">Saldırganlar şunları deneyebilir:</span><span class="sxs-lookup"><span data-stu-id="71d69-600">Attackers may attempt to:</span></span>

* <span data-ttu-id="71d69-601">[Hizmet reddi](/windows-hardware/drivers/ifs/denial-of-service) saldırıları yürütün.</span><span class="sxs-lookup"><span data-stu-id="71d69-601">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="71d69-602">Virüsleri veya kötü amaçlı yazılımları karşıya yükleyin.</span><span class="sxs-lookup"><span data-stu-id="71d69-602">Upload viruses or malware.</span></span>
* <span data-ttu-id="71d69-603">Ağları ve sunucuları diğer yollarla tehlikeye atabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-603">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="71d69-604">Başarılı bir saldırının olasılığını azaltan güvenlik adımları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="71d69-604">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="71d69-605">Dosyaları, tercihen sistem dışı bir sürücüye, özel bir dosya yükleme alanına yükleyin.</span><span class="sxs-lookup"><span data-stu-id="71d69-605">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="71d69-606">Ayrılmış bir konum, karşıya yüklenen dosyalar üzerinde güvenlik kısıtlamaları yapmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="71d69-606">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="71d69-607">Dosya yükleme konumunda yürütme izinlerini devre dışı bırakın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="71d69-607">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="71d69-608">Karşıya yüklenen dosyaları uygulamayla aynı dizin **ağacında kalıcı hale** getirme.&dagger;</span><span class="sxs-lookup"><span data-stu-id="71d69-608">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="71d69-609">Uygulama tarafından belirlenen bir güvenli dosya adı kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-609">Use a safe file name determined by the app.</span></span> <span data-ttu-id="71d69-610">Kullanıcı tarafından belirtilen bir dosya adı veya karşıya yüklenen dosyanın güvenilmeyen dosya adı kullanmayın. &dagger; HTML, görüntüleme sırasında güvenilmeyen dosya adını kodlayamıyor.</span><span class="sxs-lookup"><span data-stu-id="71d69-610">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="71d69-611">Örneğin, dosya adını günlüğe kaydetme veya Kullanıcı arabiriminde görüntüleme ( Razor otomatik olarak HTML kodlama çıktısı).</span><span class="sxs-lookup"><span data-stu-id="71d69-611">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="71d69-612">Uygulamanın tasarım belirtimi için yalnızca onaylanan dosya uzantılarına izin verin.&dagger;</span><span class="sxs-lookup"><span data-stu-id="71d69-612">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="71d69-613">Sunucuda istemci tarafı denetimlerinin gerçekleştirildiğinden emin olun. &dagger; İstemci tarafı denetimleri kolayca atmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="71d69-613">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="71d69-614">Karşıya yüklenen dosyanın boyutunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="71d69-614">Check the size of an uploaded file.</span></span> <span data-ttu-id="71d69-615">Büyük karşıya yüklemeleri engellemek için en büyük boyut sınırını ayarlayın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="71d69-615">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="71d69-616">Aynı ada sahip karşıya yüklenen bir dosya tarafından dosyaların üzerine yazılmaması gerektiğinde, dosyayı karşıya yüklemeden önce dosya adını veritabanına veya fiziksel depolamaya göre denetleyin.</span><span class="sxs-lookup"><span data-stu-id="71d69-616">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="71d69-617">**Dosya depolanmadan önce karşıya yüklenen içerik üzerinde bir virüs/kötü amaçlı yazılım tarayıcısı çalıştırın.**</span><span class="sxs-lookup"><span data-stu-id="71d69-617">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="71d69-618">&dagger;Örnek uygulama, ölçütlere uyan bir yaklaşımı gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-618">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="71d69-619">Kötü amaçlı kodun bir sisteme yüklenmesi genellikle şu şekilde kod yürütmenin ilk adımıdır:</span><span class="sxs-lookup"><span data-stu-id="71d69-619">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="71d69-620">Sistemin denetimini tamamen elde edin.</span><span class="sxs-lookup"><span data-stu-id="71d69-620">Completely gain control of a system.</span></span>
> * <span data-ttu-id="71d69-621">Sistemin kilitlenme sonucuyla bir sistemi aşırı yükleme.</span><span class="sxs-lookup"><span data-stu-id="71d69-621">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="71d69-622">Kullanıcı veya Sistem verilerinin güvenliğini tehlikeye atabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-622">Compromise user or system data.</span></span>
> * <span data-ttu-id="71d69-623">Genel Kullanıcı arabirimine Graffiti uygulayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-623">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="71d69-624">Kullanıcılardan dosya kabul edilirken saldırı yüzeyi alanını azaltma hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="71d69-624">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="71d69-625">Kısıtlanmamış dosya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-625">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="71d69-626">Azure güvenliği: kullanıcılardan dosya kabul edilirken uygun denetimlerin yerinde olduğundan emin olun</span><span class="sxs-lookup"><span data-stu-id="71d69-626">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="71d69-627">Örnek uygulamadaki örnekler de dahil olmak üzere güvenlik önlemlerini uygulama hakkında daha fazla bilgi için [doğrulama](#validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-627">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="71d69-628">Depolama senaryoları</span><span class="sxs-lookup"><span data-stu-id="71d69-628">Storage scenarios</span></span>

<span data-ttu-id="71d69-629">Dosyalar için ortak depolama seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="71d69-629">Common storage options for files include:</span></span>

* <span data-ttu-id="71d69-630">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="71d69-630">Database</span></span>

  * <span data-ttu-id="71d69-631">Küçük dosya yüklemeleri için, bir veritabanı genellikle fiziksel depolama (dosya sistemi veya ağ paylaşımından) seçeneklerinden daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="71d69-631">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="71d69-632">Kullanıcı verileri için bir veritabanı kaydının alınması eşzamanlı olarak dosya içeriğini (örneğin, avatar görüntüsü) sağlayabildiğinden, veritabanı genellikle fiziksel depolama seçeneklerine göre daha uygundur.</span><span class="sxs-lookup"><span data-stu-id="71d69-632">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="71d69-633">Bir veritabanı, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-633">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="71d69-634">Fiziksel depolama (dosya sistemi veya ağ paylaşma)</span><span class="sxs-lookup"><span data-stu-id="71d69-634">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="71d69-635">Büyük dosya yüklemeleri için:</span><span class="sxs-lookup"><span data-stu-id="71d69-635">For large file uploads:</span></span>
    * <span data-ttu-id="71d69-636">Veritabanı limitleri karşıya yükleme boyutunu kısıtlayabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-636">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="71d69-637">Fiziksel depolama genellikle bir veritabanındaki depolamadan daha az ekonomik olur.</span><span class="sxs-lookup"><span data-stu-id="71d69-637">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="71d69-638">Fiziksel depolama, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-638">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="71d69-639">Uygulamanın işlemi, depolama konumu için okuma ve yazma izinlerine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="71d69-639">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="71d69-640">**Hiçbir zaman yürütme izni vermeyin.**</span><span class="sxs-lookup"><span data-stu-id="71d69-640">**Never grant execute permission.**</span></span>

* <span data-ttu-id="71d69-641">Veri depolama hizmeti (örneğin, [Azure Blob depolama](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="71d69-641">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="71d69-642">Hizmetler genellikle tek hata noktalarına tabi olan şirket içi çözümler üzerinde geliştirilmiş ölçeklenebilirlik ve esneklik sunar.</span><span class="sxs-lookup"><span data-stu-id="71d69-642">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="71d69-643">Hizmetler büyük depolama altyapısı senaryolarında düşük maliyetlidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-643">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="71d69-644">Daha fazla bilgi için bkz. [hızlı başlangıç: nesne depolamada blob oluşturmak için .NET kullanma](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="71d69-644">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="71d69-645">Konu başlığı altında <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> <xref:System.IO.FileStream> ile çalışırken bir BLOB depolama alanına kaydetmek için kullanılabilir <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="71d69-645">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="71d69-646">Karşıya dosya yükleme senaryoları</span><span class="sxs-lookup"><span data-stu-id="71d69-646">File upload scenarios</span></span>

<span data-ttu-id="71d69-647">Dosyaları karşıya yüklemek için iki genel yaklaşım arabelleğe alınır ve akışlardır.</span><span class="sxs-lookup"><span data-stu-id="71d69-647">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="71d69-648">**Ara**</span><span class="sxs-lookup"><span data-stu-id="71d69-648">**Buffering**</span></span>

<span data-ttu-id="71d69-649">Dosyanın tamamı <xref:Microsoft.AspNetCore.Http.IFormFile> , dosyayı işlemek veya kaydetmek için kullanılan dosyanın C# temsili olan öğesine okunurdur.</span><span class="sxs-lookup"><span data-stu-id="71d69-649">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="71d69-650">Dosya karşıya yüklemeleri tarafından kullanılan kaynaklar (disk, bellek), eşzamanlı dosya karşıya yüklemelerinin sayısına ve boyutuna bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="71d69-650">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="71d69-651">Bir uygulama çok fazla karşıya yükleme arabelleğini denerse, bellek veya disk alanı tükenirse site çöker.</span><span class="sxs-lookup"><span data-stu-id="71d69-651">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="71d69-652">Karşıya dosya yükleme boyutu veya sıklığı uygulama kaynaklarını tüketme ise, akış ' ı kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-652">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="71d69-653">64 KB geçen tek bir arabelleğe alınmış dosya, bellekten diskte geçici bir dosyaya taşınır.</span><span class="sxs-lookup"><span data-stu-id="71d69-653">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="71d69-654">Dosya arabelleğe alma, bu konunun aşağıdaki bölümlerinde ele alınmıştır:</span><span class="sxs-lookup"><span data-stu-id="71d69-654">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="71d69-655">Fiziksel depolama alanı</span><span class="sxs-lookup"><span data-stu-id="71d69-655">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="71d69-656">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="71d69-656">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="71d69-657">**Akışlar**</span><span class="sxs-lookup"><span data-stu-id="71d69-657">**Streaming**</span></span>

<span data-ttu-id="71d69-658">Dosya çok parçalı bir istekten alınır ve doğrudan uygulama tarafından işlenir veya kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-658">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="71d69-659">Akış performansı önemli ölçüde iyileştirmez.</span><span class="sxs-lookup"><span data-stu-id="71d69-659">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="71d69-660">Akış, dosya karşıya yüklenirken bellek veya disk alanı taleplerini azaltır.</span><span class="sxs-lookup"><span data-stu-id="71d69-660">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="71d69-661">Akış büyük dosyaları [akış ile büyük dosyaları karşıya yükle](#upload-large-files-with-streaming) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="71d69-661">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="71d69-662">Fiziksel depolamaya arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-662">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="71d69-663">Küçük dosyaları karşıya yüklemek için, çok parçalı bir form kullanın veya JavaScript kullanarak bir POST isteği oluşturun.</span><span class="sxs-lookup"><span data-stu-id="71d69-663">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="71d69-664">Aşağıdaki örnek, Razor tek bir dosyayı karşıya yüklemek için sayfalar formunun kullanımını gösterir (örnek uygulamada *Pages/Bufferedsinglefileuploadfiziksel. cshtml* ):</span><span class="sxs-lookup"><span data-stu-id="71d69-664">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="71d69-665">Aşağıdaki örnek, önceki örneğe benzerdir, ancak şunları hariç:</span><span class="sxs-lookup"><span data-stu-id="71d69-665">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="71d69-666">Form verilerini göndermek için JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-666">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="71d69-667">Doğrulama yok.</span><span class="sxs-lookup"><span data-stu-id="71d69-667">There's no validation.</span></span>

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

<span data-ttu-id="71d69-668">[Fetch API 'sini desteklemeyen](https://caniuse.com/#feat=fetch)istemcilerde form gönderisini JavaScript 'te gerçekleştirmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="71d69-668">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="71d69-669">Fetch Polyfill kullanın (örneğin, [Window. Fetch (GitHub/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="71d69-669">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="71d69-670">`XMLHttpRequest` komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-670">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="71d69-671">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="71d69-671">For example:</span></span>

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

<span data-ttu-id="71d69-672">Dosya yüklemelerini desteklemek için, HTML formları bir kodlama türü ( `enctype` ) belirtmelidir `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="71d69-672">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="71d69-673">`files`Birden çok dosyayı karşıya yüklemeyi destekleyen bir giriş öğesi için, `multiple` öğesine özniteliği sağlar `<input>` :</span><span class="sxs-lookup"><span data-stu-id="71d69-673">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="71d69-674">Sunucuya yüklenen tek dosyalara, kullanılarak [model bağlama](xref:mvc/models/model-binding) yoluyla erişilebilir <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="71d69-674">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="71d69-675">Örnek uygulama, veritabanı ve fiziksel depolama senaryoları için birden çok arabellekli dosya yüklemeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-675">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="71d69-676"> `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> Görüntüleme ve günlüğe kaydetme için dışındaki özelliğini kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-676">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="71d69-677">Görüntüleme veya günlüğe kaydetme sırasında, HTML dosya adını kodlayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-677">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="71d69-678">Saldırgan, tam yollar veya göreli yollar dahil olmak üzere kötü amaçlı bir dosya adı sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-678">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="71d69-679">Uygulamalar:</span><span class="sxs-lookup"><span data-stu-id="71d69-679">Applications should:</span></span>
>
> * <span data-ttu-id="71d69-680">Kullanıcı tarafından sağlanan dosya adının yolunu kaldırın.</span><span class="sxs-lookup"><span data-stu-id="71d69-680">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="71d69-681">Kullanıcı arabirimi veya günlüğe kaydetme için HTML kodlu, yol tarafından kaldırılan dosya adını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="71d69-681">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="71d69-682">Depolama için yeni bir rastgele dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="71d69-682">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="71d69-683">Aşağıdaki kod, dosya adından yolu kaldırır:</span><span class="sxs-lookup"><span data-stu-id="71d69-683">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="71d69-684">Bu nedenle, şu ana kadar dikkate alınması gereken örnekler aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="71d69-684">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="71d69-685">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-685">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="71d69-686">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="71d69-686">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="71d69-687">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="71d69-687">Validation</span></span>](#validation)

<span data-ttu-id="71d69-688">Model bağlama kullanarak dosyaları karşıya yüklerken <xref:Microsoft.AspNetCore.Http.IFormFile> , eylem yöntemi kabul edebilir:</span><span class="sxs-lookup"><span data-stu-id="71d69-688">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="71d69-689">Tek bir <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="71d69-689">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="71d69-690">Birkaç dosyayı temsil eden aşağıdaki koleksiyonlardan herhangi biri:</span><span class="sxs-lookup"><span data-stu-id="71d69-690">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="71d69-691">[Listele](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="71d69-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="71d69-692">Bağlama, form dosyaları adına göre eşleşir.</span><span class="sxs-lookup"><span data-stu-id="71d69-692">Binding matches form files by name.</span></span> <span data-ttu-id="71d69-693">Örneğin, `name` IÇINDEKI HTML değeri `<input type="file" name="formFile">` C# parametresi/özelliği ile ( `FormFile` ) eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-693">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="71d69-694">Daha fazla bilgi için, [ad öznitelik DEĞERINI Post yönteminin parametre adına eşleştirin](#match-name-attribute-value-to-parameter-name-of-post-method) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-694">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="71d69-695">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="71d69-695">The following example:</span></span>

* <span data-ttu-id="71d69-696">Karşıya yüklenen bir veya daha fazla dosya üzerinden döngü.</span><span class="sxs-lookup"><span data-stu-id="71d69-696">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="71d69-697">Dosya adı da dahil olmak üzere bir dosyanın tam yolunu döndürmek için [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanır.</span><span class="sxs-lookup"><span data-stu-id="71d69-697">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="71d69-698">Dosyalar, uygulama tarafından oluşturulan bir dosya adı kullanılarak yerel dosya sistemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-698">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="71d69-699">Karşıya yüklenen dosyaların toplam sayısını ve boyutunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="71d69-699">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="71d69-700">`Path.GetRandomFileName`Yol olmadan bir dosya adı oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-700">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="71d69-701">Aşağıdaki örnekte, yol yapılandırmadan alınır:</span><span class="sxs-lookup"><span data-stu-id="71d69-701">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="71d69-702">Öğesine geçirilen yol, <xref:System.IO.FileStream>  dosya adını içermelidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-702">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="71d69-703">Dosya adı sağlanmazsa, çalışma zamanında bir oluşturulur <xref:System.UnauthorizedAccessException> .</span><span class="sxs-lookup"><span data-stu-id="71d69-703">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="71d69-704">Tekniği kullanılarak yüklenen dosyalar, <xref:Microsoft.AspNetCore.Http.IFormFile> işlemeden önce sunucuda veya diskte bellek halinde arabelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="71d69-704">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="71d69-705">Eylem yönteminde, <xref:Microsoft.AspNetCore.Http.IFormFile> içeriğe bir olarak erişilebilir <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="71d69-705">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="71d69-706">Yerel dosya sistemine ek olarak, dosyalar bir ağ paylaşımında veya [Azure Blob depolama](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)gibi bir dosya depolama hizmetine kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-706">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="71d69-707">Karşıya yüklemek için birden çok dosya üzerinde döngü yapan ve güvenli dosya adları kullanan başka bir örnek için, örnek uygulamadaki *Pages/Bufferedmultiplefileuploadfiziksel. cshtml. cs* dosyasına bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-707">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="71d69-708">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) <xref:System.IO.IOException> , önceki geçici dosyaları silmeden 65.535 ' den fazla dosya oluşturulduysa bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="71d69-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="71d69-709">65.535 dosya sınırının sunucu başına sınırı vardır.</span><span class="sxs-lookup"><span data-stu-id="71d69-709">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="71d69-710">Windows işletim sistemi için bu sınır hakkında daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="71d69-710">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="71d69-711">GetTempFileNameA işlevi</span><span class="sxs-lookup"><span data-stu-id="71d69-711">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="71d69-712">Bir veritabanına arabellekli model bağlamaya sahip küçük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-712">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="71d69-713">İkili dosya verilerini [Entity Framework](/ef/core/index)kullanarak bir veritabanında depolamak için, <xref:System.Byte> varlıkta bir dizi özelliği tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="71d69-713">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="71d69-714">Sınıf için şunu içeren bir sayfa modeli özelliği belirtin <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="71d69-714">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="71d69-715"><xref:Microsoft.AspNetCore.Http.IFormFile> doğrudan bir eylem yöntemi parametresi veya bir bağlantılı model özelliği olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-715"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="71d69-716">Önceki örnekte, bir bağlantılı model özelliği kullanılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="71d69-716">The prior example uses a bound model property.</span></span>

<span data-ttu-id="71d69-717">, `FileUpload` Razor Sayfalar formunda kullanılır:</span><span class="sxs-lookup"><span data-stu-id="71d69-717">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="71d69-718">Form sunucuya gönderildiğinde, öğesini <xref:Microsoft.AspNetCore.Http.IFormFile> bir akışa kopyalayın ve veritabanına bir bayt dizisi olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="71d69-718">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="71d69-719">Aşağıdaki örnekte, `_dbContext` uygulamanın veritabanı bağlamını depolar:</span><span class="sxs-lookup"><span data-stu-id="71d69-719">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="71d69-720">Yukarıdaki örnek, örnek uygulamada gösterilen senaryoya benzerdir:</span><span class="sxs-lookup"><span data-stu-id="71d69-720">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="71d69-721">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="71d69-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="71d69-722">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="71d69-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="71d69-723">İkili verileri ilişkisel veritabanlarında depolarken dikkatli olun, çünkü performansı olumsuz etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-723">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="71d69-724">Doğrulaması olmadan özelliğine güvenmeyin veya bu `FileName` özelliğe güvenmeyin <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="71d69-724">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="71d69-725">`FileName`Özelliği yalnızca görüntüleme amacıyla ve yalnızca HTML kodlaması sonrasında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="71d69-725">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="71d69-726">Belirtilen örneklerde dikkate alınması gereken önemli noktalar.</span><span class="sxs-lookup"><span data-stu-id="71d69-726">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="71d69-727">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-727">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="71d69-728">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="71d69-728">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="71d69-729">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="71d69-729">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="71d69-730">Akışa sahip büyük dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-730">Upload large files with streaming</span></span>

<span data-ttu-id="71d69-731">Aşağıdaki örnek, bir denetleyiciyi bir denetleyici eyleminde akışa almak için JavaScript 'in nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-731">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="71d69-732">Dosyanın antiforgery belirteci özel bir filtre özniteliği kullanılarak oluşturulur ve istek gövdesi yerine istemci HTTP üst bilgilerine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-732">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="71d69-733">Eylem yöntemi karşıya yüklenen verileri doğrudan işlediğinden, form modeli bağlama başka bir özel filtre tarafından devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="71d69-733">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="71d69-734">Eylem içinde formun içerikleri, `MultipartReader` her bir bireyi okuyan `MultipartSection` , dosyayı işleyen veya içeriği uygun şekilde depolayan bir kullanılarak okunur.</span><span class="sxs-lookup"><span data-stu-id="71d69-734">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="71d69-735">Çok parçalı bölümler okunduktan sonra eylem kendi model bağlamasını gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="71d69-735">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="71d69-736">İlk sayfa yanıtı formu yükler ve bir cookie (özniteliği aracılığıyla) bir antiforgery belirtecini kaydeder `GenerateAntiforgeryTokenCookieAttribute` .</span><span class="sxs-lookup"><span data-stu-id="71d69-736">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="71d69-737">Özniteliği, bir istek belirteci ile ayarlamak için ASP.NET Core yerleşik [antiforgery desteğini](xref:security/anti-request-forgery) kullanır cookie :</span><span class="sxs-lookup"><span data-stu-id="71d69-737">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="71d69-738">`DisableFormValueModelBindingAttribute`Model bağlamayı devre dışı bırakmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="71d69-738">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="71d69-739">Örnek uygulamada `GenerateAntiforgeryTokenCookieAttribute` ve `DisableFormValueModelBindingAttribute` sayfa `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor kuralları](xref:razor-pages/razor-pages-conventions)kullanılarak ve içindeki sayfa uygulama modellerine filtre olarak uygulanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-739">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="71d69-740">Model bağlama formu okumadığından formdan bağlanan parametreler bağlanamaz (sorgu, yol ve başlık çalışmaya devam eder).</span><span class="sxs-lookup"><span data-stu-id="71d69-740">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="71d69-741">Action yöntemi doğrudan özelliği ile birlikte çalışabilir `Request` .</span><span class="sxs-lookup"><span data-stu-id="71d69-741">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="71d69-742">`MultipartReader`Her bölümü okumak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-742">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="71d69-743">Anahtar/değer verileri bir içinde depolanır `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="71d69-743">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="71d69-744">Çok parçalı bölümler okunduktan sonra, öğesinin içeriği `KeyValueAccumulator` form verilerini bir model türüne bağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-744">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="71d69-745">`StreamingController.UploadDatabase`EF Core bir veritabanına akış için tamamlanan Yöntem:</span><span class="sxs-lookup"><span data-stu-id="71d69-745">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="71d69-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="71d69-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="71d69-747">`StreamingController.UploadPhysical`Fiziksel bir konuma akışa yönelik tüm Yöntem:</span><span class="sxs-lookup"><span data-stu-id="71d69-747">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="71d69-748">Örnek uygulamada, doğrulama denetimleri tarafından işlenir `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="71d69-748">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="71d69-749">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="71d69-749">Validation</span></span>

<span data-ttu-id="71d69-750">Örnek uygulamanın sınıfı, `FileHelpers` arabelleğe alınmış <xref:Microsoft.AspNetCore.Http.IFormFile> ve akış dosya yüklemeleri için birkaç denetim gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-750">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="71d69-751"><xref:Microsoft.AspNetCore.Http.IFormFile>Örnek uygulamada ara belleğe alınmış dosya yüklemelerini işlemek için, `ProcessFormFile` *Utilities/fileyardımcılar. cs* dosyasındaki yöntemine bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-751">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="71d69-752">Akış dosyalarını işlemek için `ProcessStreamedFile` aynı dosyadaki yöntemine bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-752">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="71d69-753">Örnek uygulamada gösterilen doğrulama işleme yöntemleri karşıya yüklenen dosyaların içeriğini taramaz.</span><span class="sxs-lookup"><span data-stu-id="71d69-753">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="71d69-754">Çoğu üretim senaryosunda, dosyanın kullanıcılara veya diğer sistemlere kullanılabilir hale getirilmesi için dosya üzerinde bir virüs/kötü amaçlı yazılım tarayıcı API 'SI kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-754">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="71d69-755">Konu örneği, doğrulama tekniklerine yönelik çalışan bir örnek sağlasa da, `FileHelpers` aşağıdakileri gerçekleştirmediğiniz takdirde sınıfı bir üretim uygulamasında uygulamaz:</span><span class="sxs-lookup"><span data-stu-id="71d69-755">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="71d69-756">Uygulamayı tam olarak anlayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-756">Fully understand the implementation.</span></span>
> * <span data-ttu-id="71d69-757">Uygulamayı uygulamanın ortamı ve belirtimleri için uygun şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="71d69-757">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="71d69-758">**Bu gereksinimleri bilmeden bir uygulamada güvenlik kodunu hiçbir şekilde sayısının fark gözetmeden uygulayın.**</span><span class="sxs-lookup"><span data-stu-id="71d69-758">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="71d69-759">İçerik doğrulama</span><span class="sxs-lookup"><span data-stu-id="71d69-759">Content validation</span></span>

<span data-ttu-id="71d69-760">**Karşıya yüklenen içerikte üçüncü taraf bir virüs/kötü amaçlı yazılım tarama API 'SI kullanın.**</span><span class="sxs-lookup"><span data-stu-id="71d69-760">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="71d69-761">Dosyaları tarama, yüksek hacimli senaryolarda sunucu kaynaklarında yoğun bir şekilde yapılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-761">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="71d69-762">Dosya tarama nedeniyle istek işleme performansı azaldığında, tarama işini, muhtemelen uygulamanın sunucusundan farklı bir sunucuda çalışan bir [arka plan hizmetine](xref:fundamentals/host/hosted-services)devredere göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="71d69-762">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="71d69-763">Genellikle, arka plan virüs tarayıcısı tarafından denetlene kadar karşıya yüklenen dosyalar karantinaya alınmış bir alanda tutulur.</span><span class="sxs-lookup"><span data-stu-id="71d69-763">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="71d69-764">Bir dosya geçtiğinde dosya normal dosya depolama konumuna taşınır.</span><span class="sxs-lookup"><span data-stu-id="71d69-764">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="71d69-765">Bu adımlar genellikle bir dosyanın tarama durumunu gösteren bir veritabanı kaydıyla birlikte gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-765">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="71d69-766">Böyle bir yaklaşım kullanarak, uygulama ve uygulama sunucusu isteklere yanıt vermeye odaklanmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="71d69-766">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="71d69-767">Dosya Uzantısı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="71d69-767">File extension validation</span></span>

<span data-ttu-id="71d69-768">Karşıya yüklenen dosyanın uzantısı izin verilen uzantılar listesine göre denetlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-768">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="71d69-769">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="71d69-769">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="71d69-770">Dosya imzası doğrulaması</span><span class="sxs-lookup"><span data-stu-id="71d69-770">File signature validation</span></span>

<span data-ttu-id="71d69-771">Bir dosyanın imzası, bir dosyanın başlangıcında ilk birkaç bayta göre belirlenir.</span><span class="sxs-lookup"><span data-stu-id="71d69-771">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="71d69-772">Bu baytlar, uzantının dosyanın içeriğiyle eşleşip eşleşmediğini göstermek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-772">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="71d69-773">Örnek uygulama, birkaç ortak dosya türü için dosya imzalarını denetler.</span><span class="sxs-lookup"><span data-stu-id="71d69-773">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="71d69-774">Aşağıdaki örnekte, bir JPEG görüntüsünün dosya imzası, dosyaya karşı denetlenir:</span><span class="sxs-lookup"><span data-stu-id="71d69-774">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="71d69-775">Ek dosya imzaları almak için [Dosya Imzaları veritabanı](https://www.filesignatures.net/) ve resmi dosya belirtimleri bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-775">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="71d69-776">Dosya adı güvenliği</span><span class="sxs-lookup"><span data-stu-id="71d69-776">File name security</span></span>

<span data-ttu-id="71d69-777">Fiziksel depolamaya bir dosyayı kaydetmek için hiçbir şekilde istemci tarafından sağlanan dosya adı kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-777">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="71d69-778">Geçici depolama için tam yol (dosya adı da dahil olmak üzere) oluşturmak için [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) veya [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanarak dosya için güvenli bir dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="71d69-778">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="71d69-779">Razor Otomatik HTML, görüntüleme için özellik değerlerini kodluyor.</span><span class="sxs-lookup"><span data-stu-id="71d69-779">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="71d69-780">Aşağıdaki kodun kullanımı güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="71d69-780">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="71d69-781">Dışında Razor , her zaman <xref:System.Net.WebUtility.HtmlEncode*> bir kullanıcının isteğinden dosya adı içeriği.</span><span class="sxs-lookup"><span data-stu-id="71d69-781">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="71d69-782">Birçok uygulama, dosyanın var olduğunu bir denetim içermelidir; Aksi takdirde, dosyanın üzerine aynı ada sahip bir dosya yazılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-782">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="71d69-783">Uygulamanızın belirtimlerini karşılamak için ek mantık sağlayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-783">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="71d69-784">Boyut doğrulaması</span><span class="sxs-lookup"><span data-stu-id="71d69-784">Size validation</span></span>

<span data-ttu-id="71d69-785">Karşıya yüklenen dosyaların boyutunu sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="71d69-785">Limit the size of uploaded files.</span></span>

<span data-ttu-id="71d69-786">Örnek uygulamada, dosyanın boyutu 2 MB ile sınırlıdır (bayt cinsinden gösterilir).</span><span class="sxs-lookup"><span data-stu-id="71d69-786">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="71d69-787">Sınır, dosyadaki [yapılandırma](xref:fundamentals/configuration/index) yoluyla sağlanır *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="71d69-787">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="71d69-788">, `FileSizeLimit` `PageModel` Sınıflara eklenmiş:</span><span class="sxs-lookup"><span data-stu-id="71d69-788">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="71d69-789">Dosya boyutu sınırı aştığında, dosya reddedilir:</span><span class="sxs-lookup"><span data-stu-id="71d69-789">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="71d69-790">Name öznitelik değerini POST yönteminin Parameter adı ile Eşleştir</span><span class="sxs-lookup"><span data-stu-id="71d69-790">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="71d69-791">RazorForm verileri oluşturan veya JavaScript 'in `FormData` doğrudan kullandığı, form öğesinde belirtilen adın veya `FormData` denetleyicinin eyleminde parametrenin adıyla eşleşmesi gereken form olmayan formlar.</span><span class="sxs-lookup"><span data-stu-id="71d69-791">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="71d69-792">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="71d69-792">In the following example:</span></span>

* <span data-ttu-id="71d69-793">Bir öğesi kullanılırken `<input>` , `name` özniteliği değere ayarlanır `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="71d69-793">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="71d69-794">`FormData`JavaScript içinde kullanırken, ad değerine ayarlanır `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="71d69-794">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="71d69-795">C# yönteminin () parametresi için eşleşen bir ad kullanın `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="71d69-795">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="71d69-796">Bir Razor sayfalar sayfa işleyici yöntemi için şunu `Upload` adlı:</span><span class="sxs-lookup"><span data-stu-id="71d69-796">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="71d69-797">MVC POST denetleyicisi eylem yöntemi için:</span><span class="sxs-lookup"><span data-stu-id="71d69-797">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="71d69-798">Sunucu ve uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="71d69-798">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="71d69-799">Çok parçalı gövde uzunluğu sınırı</span><span class="sxs-lookup"><span data-stu-id="71d69-799">Multipart body length limit</span></span>

<span data-ttu-id="71d69-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Her bir çok parçalı gövdenin uzunluk sınırını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="71d69-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="71d69-801">Bu sınırı aşan form bölümleri <xref:System.IO.InvalidDataException> ayrıştırıldığında bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="71d69-801">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="71d69-802">Varsayılan değer 134.217.728 ' dir (128 MB).</span><span class="sxs-lookup"><span data-stu-id="71d69-802">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="71d69-803">Sınırı, <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> içindeki ayarı kullanarak özelleştirin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71d69-803">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="71d69-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>tek sayfa veya eylem için ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="71d69-805">Bir Razor Sayfalar uygulamasında, filtresi içindeki bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71d69-805">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="71d69-806">Bir Razor Sayfalar uygulamasında veya BIR MVC uygulamasında, filtreyi sayfa modeline veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="71d69-806">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="71d69-807">Kestrel maksimum istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="71d69-807">Kestrel maximum request body size</span></span>

<span data-ttu-id="71d69-808">Kestrel tarafından barındırılan uygulamalar için, varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu, yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="71d69-808">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="71d69-809">[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel Server seçeneğini kullanarak sınırı özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="71d69-809">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="71d69-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> , tek sayfa veya eylem için [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) ayarlamak üzere kullanılır.</span><span class="sxs-lookup"><span data-stu-id="71d69-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="71d69-811">Bir Razor Sayfalar uygulamasında, filtresi içindeki bir [kurala](xref:razor-pages/razor-pages-conventions) uygulayın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71d69-811">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="71d69-812">Bir Razor Sayfalar uygulamasında veya BIR MVC uygulamasında, filtreyi sayfa işleyici sınıfına veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="71d69-812">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="71d69-813">Diğer Kestrel limitleri</span><span class="sxs-lookup"><span data-stu-id="71d69-813">Other Kestrel limits</span></span>

<span data-ttu-id="71d69-814">Kestrel tarafından barındırılan uygulamalar için diğer Kestrel limitleri de uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="71d69-814">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="71d69-815">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="71d69-815">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="71d69-816">İstek ve yanıt veri ücretleri</span><span class="sxs-lookup"><span data-stu-id="71d69-816">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="71d69-817">IIS</span><span class="sxs-lookup"><span data-stu-id="71d69-817">IIS</span></span>

<span data-ttu-id="71d69-818">Varsayılan istek sınırı ( `maxAllowedContentLength` ), yaklaşık 28,6 MB olan 30.000.000 bayttır.</span><span class="sxs-lookup"><span data-stu-id="71d69-818">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="71d69-819">Dosyadaki sınırı özelleştirin `web.config` .</span><span class="sxs-lookup"><span data-stu-id="71d69-819">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="71d69-820">Aşağıdaki örnekte, sınır 50 MB (52.428.800 bayt) olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-820">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="71d69-821">`maxAllowedContentLength`Ayar yalnızca IIS için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="71d69-821">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="71d69-822">Daha fazla bilgi için bkz. [Istek `<requestLimits>` sınırları ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="71d69-822">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="71d69-823">HTTP isteğinin en büyük istek gövdesi boyutunu <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> ' de ayarlayarak artırın `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="71d69-823">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="71d69-824">Aşağıdaki örnekte, sınır 50 MB (52.428.800 bayt) olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="71d69-824">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="71d69-825">Daha fazla bilgi için bkz. <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="71d69-825">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="71d69-826">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="71d69-826">Troubleshoot</span></span>

<span data-ttu-id="71d69-827">Dosyaları karşıya yükleme ve olası çözümleri ile çalışırken karşılaşılan bazı yaygın sorunlar aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="71d69-827">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="71d69-828">Bir IIS sunucusuna dağıtılırken bulunamadı hatası</span><span class="sxs-lookup"><span data-stu-id="71d69-828">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="71d69-829">Aşağıdaki hata karşıya yüklenen dosyanın, sunucunun yapılandırılmış içerik uzunluğunu aştığını gösterir:</span><span class="sxs-lookup"><span data-stu-id="71d69-829">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="71d69-830">Daha fazla bilgi için [IIS](#iis) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-830">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="71d69-831">Bağlantı hatası</span><span class="sxs-lookup"><span data-stu-id="71d69-831">Connection failure</span></span>

<span data-ttu-id="71d69-832">Bir bağlantı hatası ve bir sıfırlama sunucu bağlantısı büyük olasılıkla karşıya yüklenen dosyanın Kestrel 'in en büyük istek gövdesi boyutunu aştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="71d69-832">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="71d69-833">Daha fazla bilgi için, [Kestrel maksimum istek gövdesi boyutu](#kestrel-maximum-request-body-size) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="71d69-833">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="71d69-834">Kestrel istemci bağlantı limitleri de ayarlama gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="71d69-834">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="71d69-835">Iformfile ile null başvuru özel durumu</span><span class="sxs-lookup"><span data-stu-id="71d69-835">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="71d69-836">Denetleyici karşıya yüklenen dosyaları kullanarak kabul edilirse, <xref:Microsoft.AspNetCore.Http.IFormFile> ancak değer ise `null` , HTML formunun bir değerini belirtmesini onaylayın `enctype` `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="71d69-836">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="71d69-837">Bu öznitelik `<form>` öğesinde ayarlanmamışsa, dosya karşıya yükleme gerçekleşmez ve herhangi bir bağlı <xref:Microsoft.AspNetCore.Http.IFormFile> bağımsız değişken `null` .</span><span class="sxs-lookup"><span data-stu-id="71d69-837">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="71d69-838">Ayrıca, [form verilerinde karşıya yükleme adlandırmasının uygulamanın adlandırmayla eşleştiğinden](#match-name-attribute-value-to-parameter-name-of-post-method)emin olun.</span><span class="sxs-lookup"><span data-stu-id="71d69-838">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="71d69-839">Akış çok uzun</span><span class="sxs-lookup"><span data-stu-id="71d69-839">Stream was too long</span></span>

<span data-ttu-id="71d69-840">Bu konudaki örnekler <xref:System.IO.MemoryStream> karşıya yüklenen dosyanın içeriğini tutmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="71d69-840">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="71d69-841">Bir öğesinin boyut sınırı `MemoryStream` `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="71d69-841">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="71d69-842">Uygulamanın dosya yükleme senaryosu, dosya içeriğinin 50 MB 'tan büyük olmasını gerektiriyorsa, `MemoryStream` karşıya yüklenen bir dosyanın içeriğini tutmak için tek başına olmayan alternatif bir yaklaşım kullanın.</span><span class="sxs-lookup"><span data-stu-id="71d69-842">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="71d69-843">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="71d69-843">Additional resources</span></span>

::: moniker range="< aspnetcore-5.0"
* [<span data-ttu-id="71d69-844">HTTP bağlantı isteği boşaltma</span><span class="sxs-lookup"><span data-stu-id="71d69-844">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
::: moniker-end
::: moniker range=">= aspnetcore-5.0"
* [<span data-ttu-id="71d69-845">HTTP bağlantı isteği boşaltma</span><span class="sxs-lookup"><span data-stu-id="71d69-845">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel/request-draining)
::: moniker-end

* [<span data-ttu-id="71d69-846">Kısıtlanmamış dosya yükleme</span><span class="sxs-lookup"><span data-stu-id="71d69-846">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="71d69-847">Azure güvenliği: güvenlik çerçevesi: giriş doğrulaması | Karşı</span><span class="sxs-lookup"><span data-stu-id="71d69-847">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="71d69-848">Azure bulut tasarım desenleri: Valet anahtar düzeni</span><span class="sxs-lookup"><span data-stu-id="71d69-848">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
