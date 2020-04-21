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
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="77bc7-103">ASP.NET Core'a dosya yükleme</span><span class="sxs-lookup"><span data-stu-id="77bc7-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="77bc7-104">Yazar: [Steve Smith](https://ardalis.com/) ve [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="77bc7-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77bc7-105">ASP.NET Core, daha küçük dosyalar için arabelleğe alınan modeli ve daha büyük dosyalar için arabelleğe alamayan akışı kullanarak bir veya daha fazla dosya yüklemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="77bc7-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="77bc7-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="77bc7-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="77bc7-107">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="77bc7-107">Security considerations</span></span>

<span data-ttu-id="77bc7-108">Kullanıcılara bir sunucuya dosya yükleme olanağı sağlarken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="77bc7-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="77bc7-109">Saldırganlar şunları deneyebilir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="77bc7-110">Hizmet reddi saldırılarını [gerçekleştirin.](/windows-hardware/drivers/ifs/denial-of-service)</span><span class="sxs-lookup"><span data-stu-id="77bc7-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="77bc7-111">Virüs veya kötü amaçlı yazılım yükleyin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="77bc7-112">Ağları ve sunucuları başka şekillerde tehlikeye at.</span><span class="sxs-lookup"><span data-stu-id="77bc7-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="77bc7-113">Başarılı bir saldırı olasılığını azaltan güvenlik adımları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="77bc7-114">Dosyaları özel bir dosya yükleme alanına, tercihen sistem dışı bir sürücüye yükleyin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="77bc7-115">Özel bir konum, yüklenen dosyalara güvenlik kısıtlamaları uygulamamayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="77bc7-116">Dosya yükleme konumundaki yürütme izinlerini devre dışı kaltın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="77bc7-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="77bc7-117">Yüklenen dosyaları uygulamayla aynı dizin ağacında kalıcı olarak **yapmayın.**&dagger;</span><span class="sxs-lookup"><span data-stu-id="77bc7-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="77bc7-118">Uygulama tarafından belirlenen güvenli bir dosya adı kullanın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="77bc7-119">Kullanıcı tarafından sağlanan bir dosya adını veya yüklenen dosyanın güvenilmeyen dosya adını kullanmayın. &dagger; HTML, görüntülenirken güvenilmeyen dosya adını kodlar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="77bc7-120">Örneğin, dosya adını günlüğe kaydetme veya Kullanıcı Arabirimi'nde görüntülenmesi (Razor otomatik olarak HTML çıktıyı kodlar).</span><span class="sxs-lookup"><span data-stu-id="77bc7-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="77bc7-121">Uygulamanın tasarım belirtimi için yalnızca onaylı dosya uzantılarına izin verin.&dagger;</span><span class="sxs-lookup"><span data-stu-id="77bc7-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="77bc7-122">İstemci tarafı denetimlerinin sunucuda gerçekleştirildiğini doğrulayın. &dagger; İstemci tarafı denetimleri atlatmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="77bc7-123">Yüklenen bir dosyanın boyutunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="77bc7-124">Büyük yüklemeleri önlemek için maksimum boyut sınırı ayarlayın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="77bc7-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="77bc7-125">Dosyaların aynı ada sahip yüklenmiş bir dosya tarafından üzerine yazılmaması gerektiğinde, dosyayı yüklemeden önce dosya adını veritabanıyla veya fiziksel depolama alanına karşı denetleyin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="77bc7-126">**Dosya depolandırılmadan önce yüklenen içeriğe virüs/kötü amaçlı yazılım tarayıcısı çalıştırın.**</span><span class="sxs-lookup"><span data-stu-id="77bc7-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="77bc7-127">&dagger;Örnek uygulama, ölçütleri karşılayan bir yaklaşım gösterir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="77bc7-128">Kötü amaçlı kodu bir sisteme yüklemek, sık sık aşağıdakileri yapan kodu yürütmenin ilk adımıdır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="77bc7-129">Bir sistemin kontrolünü tamamen ele geçirmek.</span><span class="sxs-lookup"><span data-stu-id="77bc7-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="77bc7-130">Sistemin çöktüğı sonucu bir sistem aşırı yükleyin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="77bc7-131">Kullanıcı veya sistem verilerini tehlikeye at.</span><span class="sxs-lookup"><span data-stu-id="77bc7-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="77bc7-132">Genel bir uI grafiti uygulayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="77bc7-133">Kullanıcılardan dosya kabul ederken saldırı yüzey alanını azaltma hakkında bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="77bc7-134">Sınırsız Dosya Yükleme</span><span class="sxs-lookup"><span data-stu-id="77bc7-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="77bc7-135">Azure Güvenliği: Kullanıcılardan dosya kabul ederken uygun denetimlerin yerinde olduğundan emin olun</span><span class="sxs-lookup"><span data-stu-id="77bc7-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="77bc7-136">Örnek uygulamadan örnekler de dahil olmak üzere güvenlik önlemlerinin uygulanması hakkında daha fazla bilgi için [Doğrulama](#validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="77bc7-137">Depolama senaryoları</span><span class="sxs-lookup"><span data-stu-id="77bc7-137">Storage scenarios</span></span>

<span data-ttu-id="77bc7-138">Dosyalar için ortak depolama seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-138">Common storage options for files include:</span></span>

* <span data-ttu-id="77bc7-139">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="77bc7-139">Database</span></span>

  * <span data-ttu-id="77bc7-140">Küçük dosya yüklemeleri için veritabanı genellikle fiziksel depolama (dosya sistemi veya ağ paylaşımı) seçeneklerinden daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="77bc7-141">Kullanıcı verileri için bir veritabanı kaydının alınması dosya içeriğini (örneğin, bir avatar görüntüsü) aynı anda sağlayabildiği için veritabanı genellikle fiziksel depolama seçeneklerinden daha kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="77bc7-142">Veritabanı, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="77bc7-143">Fiziksel depolama (dosya sistemi veya ağ paylaşımı)</span><span class="sxs-lookup"><span data-stu-id="77bc7-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="77bc7-144">Büyük dosya yüklemeleri için:</span><span class="sxs-lookup"><span data-stu-id="77bc7-144">For large file uploads:</span></span>
    * <span data-ttu-id="77bc7-145">Veritabanı sınırları yüklemeboyutunu kısıtlayabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="77bc7-146">Fiziksel depolama genellikle veritabanındaki depolamadan daha az ekonomiktir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="77bc7-147">Fiziksel depolama, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="77bc7-148">Uygulamanın işlemi, depolama konumuna izinleri okuyup yazmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="77bc7-149">**Yürütme izni asla vermez.**</span><span class="sxs-lookup"><span data-stu-id="77bc7-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="77bc7-150">Veri depolama hizmeti (örneğin, [Azure Blob Depolama)](https://azure.microsoft.com/services/storage/blobs/)</span><span class="sxs-lookup"><span data-stu-id="77bc7-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="77bc7-151">Hizmetler genellikle tek hata noktalarına maruz kalan şirket içi çözümler üzerinde gelişmiş ölçeklenebilirlik ve esneklik sunar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="77bc7-152">Hizmetler, büyük depolama altyapısı senaryolarında potansiyel olarak daha düşük maliyetlidir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="77bc7-153">Daha fazla bilgi için [Quickstart: Object depolamaalanında bir leke oluşturmak için .NET'i kullanın.](/azure/storage/blobs/storage-quickstart-blobs-dotnet)</span><span class="sxs-lookup"><span data-stu-id="77bc7-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="77bc7-154">Dosya yükleme senaryoları</span><span class="sxs-lookup"><span data-stu-id="77bc7-154">File upload scenarios</span></span>

<span data-ttu-id="77bc7-155">Dosya yüklemek için iki genel yaklaşım arabelleğe alma ve akış vardır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="77bc7-156">**Tamponlama**</span><span class="sxs-lookup"><span data-stu-id="77bc7-156">**Buffering**</span></span>

<span data-ttu-id="77bc7-157">Dosyanın tamamı, dosyayı işlemek veya kaydetmek için kullanılan dosyanın C# gösterimi olan bir <xref:Microsoft.AspNetCore.Http.IFormFile>dosyaya doğru okunur.</span><span class="sxs-lookup"><span data-stu-id="77bc7-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="77bc7-158">Dosya yüklemeleri tarafından kullanılan kaynaklar (disk, bellek) eşzamanlı dosya yüklemelerinin sayısına ve boyutuna bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="77bc7-159">Bir uygulama çok fazla yükleme arabelleğe almaya çalışırsa, bellek veya disk alanı bittiğinde site çöker.</span><span class="sxs-lookup"><span data-stu-id="77bc7-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="77bc7-160">Dosya yüklemelerinin boyutu veya sıklığı uygulama kaynaklarını tüketiyorsa, akışı kullanın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="77bc7-161">64 KB'yi aşan tek bir arabelleğe alınan dosya bellekten diskteki geçici bir dosyaya taşınır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="77bc7-162">Küçük dosyaların arabelleğe alma bu konunun aşağıdaki bölümlerinde ele alınmıştır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="77bc7-163">Fiziksel depolama</span><span class="sxs-lookup"><span data-stu-id="77bc7-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="77bc7-164">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="77bc7-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="77bc7-165">**Akış**</span><span class="sxs-lookup"><span data-stu-id="77bc7-165">**Streaming**</span></span>

<span data-ttu-id="77bc7-166">Dosya çok parçalı bir istekten alınır ve uygulama tarafından doğrudan işlenir veya kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="77bc7-167">Akış performansı önemli ölçüde artırmaz.</span><span class="sxs-lookup"><span data-stu-id="77bc7-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="77bc7-168">Akış, dosya yüklerken bellek veya disk alanı taleplerini azaltır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="77bc7-169">Büyük dosyaların akış [akışı, akış bölümüyle](#upload-large-files-with-streaming) birlikte Yükle büyük dosyaları yla kaplıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="77bc7-170">Fiziksel depolamaya bağlanan arabelleğe alınan modelle küçük dosyaları yükleme</span><span class="sxs-lookup"><span data-stu-id="77bc7-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="77bc7-171">Küçük dosyalar yüklemek için çok parçalı bir form kullanın veya JavaScript kullanarak bir POST isteği oluşturun.</span><span class="sxs-lookup"><span data-stu-id="77bc7-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="77bc7-172">Aşağıdaki örnek, tek bir dosyayı *(Pages/BufferedSingleFileUploadPhysical.cshtml)* örnek uygulamada yüklemek için bir Razor Pages formunun kullanımını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="77bc7-173">Aşağıdaki örnek, aşağıdakiler dışında önceki örneğe benzer:</span><span class="sxs-lookup"><span data-stu-id="77bc7-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="77bc7-174">JavaScript's ([ApI Getir](https://developer.mozilla.org/docs/Web/API/Fetch_API)) formun verilerini göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="77bc7-175">Doğrulama yok.</span><span class="sxs-lookup"><span data-stu-id="77bc7-175">There's no validation.</span></span>

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

<span data-ttu-id="77bc7-176">[ApI'yi desteklemeyen](https://caniuse.com/#feat=fetch)istemciler için JavaScript'te POST formunu gerçekleştirmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="77bc7-177">Bir Fetch Polyfill kullanın (örneğin, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="77bc7-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="77bc7-178">`XMLHttpRequest` adresini kullanın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="77bc7-179">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="77bc7-179">For example:</span></span>

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

<span data-ttu-id="77bc7-180">Dosya yüklemelerini desteklemek için HTML formlarında bir kodlama`enctype`türü `multipart/form-data`belirtilmelidir .</span><span class="sxs-lookup"><span data-stu-id="77bc7-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="77bc7-181">Birden `files` çok dosya yüklemeyi destekleyen bir `multiple` giriş öğesi `<input>` için öğedeki özniteliği sağlar:</span><span class="sxs-lookup"><span data-stu-id="77bc7-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="77bc7-182">Sunucuya yüklenen tek tek dosyalara [Model Bağlama](xref:mvc/models/model-binding) <xref:Microsoft.AspNetCore.Http.IFormFile>ile erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="77bc7-183">Örnek uygulama, veritabanı ve fiziksel depolama senaryoları için birden çok arabelleğe alınan dosya yüklemesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="77bc7-184">Görüntülemek **not** ve `FileName` günlüğe <xref:Microsoft.AspNetCore.Http.IFormFile> kaydetme dışında özelliğikullanmayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="77bc7-185">Html, görüntüveya günlüğe kaydetme de dosya adını kodlar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="77bc7-186">Saldırgan, tam yollar veya göreli yollar da dahil olmak üzere kötü amaçlı bir dosya adı sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="77bc7-187">Uygulamalar:</span><span class="sxs-lookup"><span data-stu-id="77bc7-187">Applications should:</span></span>
>
> * <span data-ttu-id="77bc7-188">Kullanıcı tarafından sağlanan dosya adından yolu kaldırın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="77bc7-189">HTML kodlu, yol kaldırılmış dosya adını Kullanıcı Arabirimi veya günlüğe kaydetmeye kaydedin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="77bc7-190">Depolama için yeni bir rasgele dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="77bc7-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="77bc7-191">Aşağıdaki kod, yolu dosya adından kaldırır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="77bc7-192">Şimdiye kadar verilen örnekler güvenlik hususları dikkate almaz.</span><span class="sxs-lookup"><span data-stu-id="77bc7-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="77bc7-193">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="77bc7-194">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="77bc7-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="77bc7-195">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="77bc7-195">Validation</span></span>](#validation)

<span data-ttu-id="77bc7-196">Model bağlama ve <xref:Microsoft.AspNetCore.Http.IFormFile>, eylem yöntemi ni kullanarak dosya yüklerken şunları kabul edebilir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="77bc7-197">Tek <xref:Microsoft.AspNetCore.Http.IFormFile>bir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="77bc7-198">Birkaç dosyayı temsil eden aşağıdaki koleksiyonlardan herhangi biri:</span><span class="sxs-lookup"><span data-stu-id="77bc7-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="77bc7-199">[Liste](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="77bc7-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="77bc7-200">Bağlama eşleşmeleri form dosyalarını ada göre bağlar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-200">Binding matches form files by name.</span></span> <span data-ttu-id="77bc7-201">Örneğin, HTML `name` değeri `<input type="file" name="formFile">` C# parametresi/özellik bağlı`FormFile`( eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="77bc7-202">Daha fazla bilgi için POST yöntemi bölümünün [parametre adına Maç adı özniteliği değerine](#match-name-attribute-value-to-parameter-name-of-post-method) bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="77bc7-203">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="77bc7-203">The following example:</span></span>

* <span data-ttu-id="77bc7-204">Yüklenen bir veya daha fazla dosyayı döngüye sokabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="77bc7-205">Dosya adı da dahil olmak üzere bir dosya için tam bir yol döndürmek için [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="77bc7-206">Uygulama tarafından oluşturulan bir dosya adını kullanarak dosyaları yerel dosya sistemine kaydeder.</span><span class="sxs-lookup"><span data-stu-id="77bc7-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="77bc7-207">Yüklenen dosyaların toplam sayısını ve boyutunu verir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="77bc7-208">Yol `Path.GetRandomFileName` olmayan bir dosya adı oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="77bc7-209">Aşağıdaki örnekte, yol yapılandırmadan elde edilir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="77bc7-210">Yol için geçirilen <xref:System.IO.FileStream> dosya adı *içermelidir.*</span><span class="sxs-lookup"><span data-stu-id="77bc7-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="77bc7-211">Dosya adı sağlanmadıysa, çalışma <xref:System.UnauthorizedAccessException> saatinde bir a.k.</span><span class="sxs-lookup"><span data-stu-id="77bc7-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="77bc7-212"><xref:Microsoft.AspNetCore.Http.IFormFile> Teknik kullanılarak yüklenen dosyalar, işleme den önce bellekte veya sunucudaki diskte arabelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="77bc7-213">Eylem yöntemi içinde, <xref:Microsoft.AspNetCore.Http.IFormFile> içeriği ne <xref:System.IO.Stream>olarak erişilebilir bir .</span><span class="sxs-lookup"><span data-stu-id="77bc7-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="77bc7-214">Yerel dosya sistemine ek olarak, dosyalar ağ paylaşımına veya [Azure Blob depolama](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)gibi bir dosya depolama hizmetine kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="77bc7-215">Yüklemek için birden çok dosyayı döngüye alan ve güvenli dosya adlarını kullanan başka bir örnek için, örnek uygulamada *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* s. bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="77bc7-216">[Path.GetTempFileName,](xref:System.IO.Path.GetTempFileName*) önceki <xref:System.IO.IOException> geçici dosyaları silmeden 65.535'ten fazla dosya oluşturulursa atar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="77bc7-217">65.535 dosya sınırı sunucu başına bir sınırdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="77bc7-218">Windows işletim sistemi yle ilgili bu sınır hakkında daha fazla bilgi için aşağıdaki konulardaki açıklamalara bakın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="77bc7-219">GetTempFileNameA fonksiyonu</span><span class="sxs-lookup"><span data-stu-id="77bc7-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="77bc7-220">Veritabanına arabelleğe alınan modele sahip küçük dosyaları yükleme</span><span class="sxs-lookup"><span data-stu-id="77bc7-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="77bc7-221">[Entity Framework'ü](/ef/core/index)kullanarak ikili dosya verilerini <xref:System.Byte> bir veritabanında depolamak için varlık üzerinde bir dizi özelliği tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="77bc7-222">Bir içeren sınıf için bir sayfa <xref:Microsoft.AspNetCore.Http.IFormFile>modeli özelliği belirtin:</span><span class="sxs-lookup"><span data-stu-id="77bc7-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="77bc7-223"><xref:Microsoft.AspNetCore.Http.IFormFile>doğrudan bir eylem yöntemi parametresi olarak veya bağlı bir model özelliği olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="77bc7-224">Önceki örnek, bağlı bir model özelliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="77bc7-225">`FileUpload` Razor Pages formunda kullanılır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="77bc7-226">Form sunucuya verildiğinde, akışı <xref:Microsoft.AspNetCore.Http.IFormFile> bir akışa kopyalayın ve veritabanına bayt dizisi olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="77bc7-227">Aşağıdaki örnekte, `_dbContext` uygulamanın veritabanı bağlamını saklar:</span><span class="sxs-lookup"><span data-stu-id="77bc7-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="77bc7-228">Önceki örnek, örnek uygulamada gösterilen bir senaryoya benzer:</span><span class="sxs-lookup"><span data-stu-id="77bc7-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="77bc7-229">*Sayfalar/TamponluSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="77bc7-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="77bc7-230">*Sayfalar/TamponluTekFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="77bc7-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="77bc7-231">Performansı olumsuz etkileyebileceğigibi, ikili verileri ilişkisel veritabanlarında depolamak için dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="77bc7-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="77bc7-232">Doğrulama `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> olmadan özelliğine güvenmeyin veya güvenmeyin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="77bc7-233">Özellik `FileName` yalnızca görüntüleme amacıyla ve yalnızca HTML kodlaması sonrasında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="77bc7-234">Verilen örnekler güvenlik hususlarını dikkate almaz.</span><span class="sxs-lookup"><span data-stu-id="77bc7-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="77bc7-235">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="77bc7-236">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="77bc7-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="77bc7-237">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="77bc7-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="77bc7-238">Akışla büyük dosyaları yükleme</span><span class="sxs-lookup"><span data-stu-id="77bc7-238">Upload large files with streaming</span></span>

<span data-ttu-id="77bc7-239">Aşağıdaki örnek, bir dosyayı denetleyici eylemine aktarmak için JavaScript'in nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="77bc7-240">Dosyanın antiforgery belirteci özel bir filtre özniteliği kullanılarak oluşturulur ve istek gövdesi yerine istemci HTTP üstbilgilerine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="77bc7-241">Eylem yöntemi yüklenen verileri doğrudan işlediğiiçin, form modeli bağlama başka bir özel filtre tarafından devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="77bc7-242">Eylem içinde, formun içeriği, her `MultipartReader`bir bireyi `MultipartSection`okuyan, dosyayı işleyen veya içeriği uygun şekilde depolayan bir , kullanılarak okunur.</span><span class="sxs-lookup"><span data-stu-id="77bc7-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="77bc7-243">Çok parçalı bölümler okunduktan sonra, eylem kendi model bağlama gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="77bc7-244">İlk sayfa yanıtı formu yükler ve bir çerez (öznitelik üzerinden) `GenerateAntiforgeryTokenCookieAttribute` bir antiforgery belirteci kaydeder.</span><span class="sxs-lookup"><span data-stu-id="77bc7-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="77bc7-245">Öznitelik, istek belirteci içeren bir çerez ayarlamak için ASP.NET Core'un yerleşik [antiforgery desteğini](xref:security/anti-request-forgery) kullanır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="77bc7-246">Model `DisableFormValueModelBindingAttribute` bağlamadevre dışı kalmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="77bc7-247">`GenerateAntiforgeryTokenCookieAttribute` Örnek uygulamada ve `DisableFormValueModelBindingAttribute` sayfa uygulama modellerine filtre olarak `/StreamedSingleFileUploadDb` uygulanır `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` ve [Jilet Sayfaları kuralları](xref:razor-pages/razor-pages-conventions)nı kullanır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="77bc7-248">Model bağlama formu okumadığından, formdan bağlanan parametreler bağlanmaz (sorgu, rota ve üstbilgi çalışmaya devam eder).</span><span class="sxs-lookup"><span data-stu-id="77bc7-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="77bc7-249">Eylem yöntemi doğrudan `Request` özellik ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="77bc7-250">A `MultipartReader` her bölümü okumak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="77bc7-251">Anahtar/değer verileri bir `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="77bc7-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="77bc7-252">Çok parçalı bölümler okunduktan sonra, `KeyValueAccumulator` form verilerini bir model türüne bağlamak için içeriği kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="77bc7-253">EF `StreamingController.UploadDatabase` Core ile bir veritabanına akış için tam yöntem:</span><span class="sxs-lookup"><span data-stu-id="77bc7-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="77bc7-254">`MultipartRequestHelper`(*Yardımcı Programlar/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="77bc7-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="77bc7-255">Fiziksel `StreamingController.UploadPhysical` bir konuma akış için tam yöntem:</span><span class="sxs-lookup"><span data-stu-id="77bc7-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="77bc7-256">Örnek uygulamada, doğrulama denetimleri `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="77bc7-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="77bc7-257">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="77bc7-257">Validation</span></span>

<span data-ttu-id="77bc7-258">Örnek uygulamanın `FileHelpers` sınıfı, arabelleğe alınan ve <xref:Microsoft.AspNetCore.Http.IFormFile> akışlı dosya yüklemeleri için birkaç denetim gösterir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="77bc7-259">Arabelleğe alınan dosya yüklemelerini örnek uygulamada <xref:Microsoft.AspNetCore.Http.IFormFile> `ProcessFormFile` işlemek için *Yardımcı Programlar/FileHelpers.cs* dosyasındaki yönteme bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="77bc7-260">Akışlı dosyaları işlemek için `ProcessStreamedFile` yönteme aynı dosyada bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="77bc7-261">Örnek uygulamada gösterilen doğrulama işleme yöntemleri yüklenen dosyaların içeriğini tazmaz.</span><span class="sxs-lookup"><span data-stu-id="77bc7-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="77bc7-262">Çoğu üretim senaryosunda, dosyayı kullanıcılar veya diğer sistemler için kullanılabilir hale getirmeden önce dosyada bir virüs/kötü amaçlı yazılım tarayıcı API kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="77bc7-263">Konu örneği doğrulama tekniklerinin çalışma örneğini sağlasa `FileHelpers` da, aşağıdakiler olmadıkça sınıfı bir üretim uygulamasında uygulamayın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="77bc7-264">Uygulamayı tamamen anlayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="77bc7-265">Uygulamayı uygulamanın ortamına ve özelliklerine uygun şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="77bc7-266">**Bu gereksinimleri karşılamadan bir uygulamada hiçbir uygulamada ayrım gözetmeksizin güvenlik kodu uygulamayın.**</span><span class="sxs-lookup"><span data-stu-id="77bc7-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="77bc7-267">İçerik doğrulama</span><span class="sxs-lookup"><span data-stu-id="77bc7-267">Content validation</span></span>

<span data-ttu-id="77bc7-268">**Yüklenen içerikte üçüncü taraf virüs/kötü amaçlı yazılım taraması API'si kullanın.**</span><span class="sxs-lookup"><span data-stu-id="77bc7-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="77bc7-269">Dosyaları taramak, yüksek hacimli senaryolarda sunucu kaynaklarında talep tedir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="77bc7-270">Dosya taraması nedeniyle istek işleme performansı azalmışsa, tarama çalışmasını bir [arka plan hizmetine](xref:fundamentals/host/hosted-services)(muhtemelen uygulamanın sunucusundan farklı bir sunucuda çalışan bir hizmete) yüklemeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="77bc7-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="77bc7-271">Genellikle, yüklenen dosyalar, arka plan virüs tarayıcısı bunları kontrol edene kadar karantinaya alınan bir alanda tutulur.</span><span class="sxs-lookup"><span data-stu-id="77bc7-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="77bc7-272">Dosya geçtiğinde, dosya normal dosya depolama konumuna taşınır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="77bc7-273">Bu adımlar genellikle bir dosyanın tarama durumunu gösteren bir veritabanı kaydı ile birlikte gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="77bc7-274">Böyle bir yaklaşım kullanarak, uygulama ve uygulama sunucusu istekleri yanıtlamaya odaklanmış kalır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="77bc7-275">Dosya uzantısı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="77bc7-275">File extension validation</span></span>

<span data-ttu-id="77bc7-276">Yüklenen dosyanın uzantısı, izin verilen uzantılar listesiyle karşılaştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="77bc7-277">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="77bc7-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="77bc7-278">Dosya imza doğrulaması</span><span class="sxs-lookup"><span data-stu-id="77bc7-278">File signature validation</span></span>

<span data-ttu-id="77bc7-279">Bir dosyanın imzası, bir dosyanın başındaki ilk birkaç bayt tarafından belirlenir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="77bc7-280">Bu baytlar, uzantının dosyanın içeriğiyle eşleşip eşleşmemediğini belirtmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="77bc7-281">Örnek uygulama, birkaç yaygın dosya türü için dosya imzalarını denetler.</span><span class="sxs-lookup"><span data-stu-id="77bc7-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="77bc7-282">Aşağıdaki örnekte, JPEG görüntüsünün dosya imzası dosyayla karşılaştırılır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="77bc7-283">Ek dosya imzaları almak için [Dosya İmzaları Veritabanı'na](https://www.filesignatures.net/) ve resmi dosya özelliklerine bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="77bc7-284">Dosya adı güvenliği</span><span class="sxs-lookup"><span data-stu-id="77bc7-284">File name security</span></span>

<span data-ttu-id="77bc7-285">Bir dosyayı fiziksel depolama alanına kaydetmek için istemci tarafından sağlanan bir dosya adını asla kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="77bc7-286">Geçici depolama alanı için tam bir yol (dosya adı dahil) oluşturmak için [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) veya [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanarak dosya için güvenli bir dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="77bc7-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="77bc7-287">Razor HTML, görüntü lenmek için özellik değerlerini otomatik olarak kodlar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="77bc7-288">Aşağıdaki kodukullanmak güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="77bc7-289">Razor dışında, <xref:System.Net.WebUtility.HtmlEncode*> her zaman bir kullanıcının isteğinden ad içeriği dosya.</span><span class="sxs-lookup"><span data-stu-id="77bc7-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="77bc7-290">Birçok uygulama, dosyanın var olup olmadığını denetlemeyi içermelidir; aksi takdirde, dosya aynı adı taşıyan bir dosya tarafından üzerine yazılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="77bc7-291">Uygulamanızın teknik özelliklerini karşılamak için ek mantık kaynağı.</span><span class="sxs-lookup"><span data-stu-id="77bc7-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="77bc7-292">Boyut doğrulama</span><span class="sxs-lookup"><span data-stu-id="77bc7-292">Size validation</span></span>

<span data-ttu-id="77bc7-293">Yüklenen dosyaların boyutunu sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="77bc7-294">Örnek uygulamada, dosyanın boyutu 2 MB ile sınırlıdır (baytlarda gösterilir).</span><span class="sxs-lookup"><span data-stu-id="77bc7-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="77bc7-295">Limit *appsettings.json* dosyasından [Configuration](xref:fundamentals/configuration/index) üzerinden sağlanır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="77bc7-296">Sınıflara `FileSizeLimit` `PageModel` enjekte edilir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="77bc7-297">Dosya boyutu sınırı aştığında, dosya reddedilir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="77bc7-298">POST yönteminin parametre adı ile eşleme adı özniteliği değeri</span><span class="sxs-lookup"><span data-stu-id="77bc7-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="77bc7-299">POST'un veri oluşturduğu veya doğrudan JavaScript'i `FormData` kullandığı Jilet olmayan formlarda, `FormData` formun öğesinde belirtilen ad veya denetleyicinin eyleminde parametrenin adı ile eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="77bc7-300">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="77bc7-300">In the following example:</span></span>

* <span data-ttu-id="77bc7-301">Bir `<input>` öğe kullanırken, `name` öznitelik değere `battlePlans`ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="77bc7-302">JavaScript'te kullanırken, `FormData` ad değerine `battlePlans`ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="77bc7-303">C# yönteminin parametresi için eşleşen`battlePlans`bir ad kullanın ( ):</span><span class="sxs-lookup"><span data-stu-id="77bc7-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="77bc7-304">Adlı bir Razor Pages sayfa `Upload`işleyicisi yöntemi için:</span><span class="sxs-lookup"><span data-stu-id="77bc7-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="77bc7-305">MVC POST denetleyici eylem yöntemi için:</span><span class="sxs-lookup"><span data-stu-id="77bc7-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="77bc7-306">Sunucu ve uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="77bc7-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="77bc7-307">Çok parçalı gövde uzunluk sınırı</span><span class="sxs-lookup"><span data-stu-id="77bc7-307">Multipart body length limit</span></span>

<span data-ttu-id="77bc7-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>her çok parçalı gövdenin uzunluğu için sınırı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="77bc7-309">Bu sınırı aşan form <xref:System.IO.InvalidDataException> bölümleri ayrıştırıldığında bir atar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="77bc7-310">Varsayılan değer 134.217.728 (128 MB) 'dir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="77bc7-311">Aşağıdaki ayarı kullanarak <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sınırı `Startup.ConfigureServices`özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="77bc7-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="77bc7-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>tek bir sayfa <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> veya eylem için ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="77bc7-313">Razor Pages uygulamasında, filtreyi aşağıdaki ler `Startup.ConfigureServices`için bir [kuralla](xref:razor-pages/razor-pages-conventions) uygulayın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="77bc7-314">Razor Pages uygulamasında veya Bir MVC uygulamasında, filtreyi sayfa modeline veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="77bc7-315">Kerkenez maksimum istek vücut boyutu</span><span class="sxs-lookup"><span data-stu-id="77bc7-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="77bc7-316">Kestrel tarafından barındırılan uygulamalar için varsayılan maksimum istek gövde boyutu yaklaşık 28,6 MB olan 30.000.000 bayttır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="77bc7-317">[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel sunucu seçeneğini kullanarak sınırı özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="77bc7-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="77bc7-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>[MaxRequestBodySize'ı](xref:fundamentals/servers/kestrel#maximum-request-body-size) tek bir sayfa veya eylem için ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="77bc7-319">Razor Pages uygulamasında, filtreyi aşağıdaki ler `Startup.ConfigureServices`için bir [kuralla](xref:razor-pages/razor-pages-conventions) uygulayın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="77bc7-320">Razor sayfaları uygulamasında veya Bir MVC uygulamasında, filtreyi sayfa işleyicisi sınıfına veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="77bc7-321">Ayrıca `RequestSizeLimitAttribute` Razor direktifi [`@attribute`](xref:mvc/views/razor#attribute) kullanılarak da uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="77bc7-322">Diğer Kerkenez limitleri</span><span class="sxs-lookup"><span data-stu-id="77bc7-322">Other Kestrel limits</span></span>

<span data-ttu-id="77bc7-323">Diğer Kerkenez limitleri Kerkenez tarafından barındırılan uygulamalar için geçerli olabilir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="77bc7-324">Maksimum istemci bağlantıları</span><span class="sxs-lookup"><span data-stu-id="77bc7-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="77bc7-325">İstek ve yanıt veri oranları</span><span class="sxs-lookup"><span data-stu-id="77bc7-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="77bc7-326">IIS içerik uzunluğu sınırı</span><span class="sxs-lookup"><span data-stu-id="77bc7-326">IIS content length limit</span></span>

<span data-ttu-id="77bc7-327">Varsayılan istek sınırı`maxAllowedContentLength`( ) yaklaşık 28,6 MB olan 30.000.000 bayttır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="77bc7-328">*web.config* dosyasındaki sınırı özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="77bc7-328">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="77bc7-329">Bu ayar yalnızca IIS için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-329">This setting only applies to IIS.</span></span> <span data-ttu-id="77bc7-330">Kestrel'de barındırma yaparken davranış varsayılan olarak oluşmaz.</span><span class="sxs-lookup"><span data-stu-id="77bc7-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="77bc7-331">Daha fazla bilgi için bkz [>. \< ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)</span><span class="sxs-lookup"><span data-stu-id="77bc7-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="77bc7-332">ASP.NET Çekirdek Modülündeki sınırlamalar veya IIS İstek Filtreleme Modülü'nün varlığı yüklemeleri 2 veya 4 GB ile sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="77bc7-333">Daha fazla bilgi için bkz: [2GB boyutundan büyük dosya yükleyemiyorum (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="77bc7-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="77bc7-334">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="77bc7-334">Troubleshoot</span></span>

<span data-ttu-id="77bc7-335">Aşağıda, dosya yükleme ve bunların olası çözümleriyle çalışırken karşılaşılan bazı sık karşılaşılan sorunlar verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="77bc7-336">Bir IIS sunucusuna dağıtıldığında bulunamadı hatası</span><span class="sxs-lookup"><span data-stu-id="77bc7-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="77bc7-337">Aşağıdaki hata, yüklenen dosyanın sunucunun yapılandırılmış içerik uzunluğunu aştığını gösterir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="77bc7-338">Sınırı artırma hakkında daha fazla bilgi için [IIS içerik uzunluğu sınırı](#iis-content-length-limit) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="77bc7-339">Bağlantı hatası</span><span class="sxs-lookup"><span data-stu-id="77bc7-339">Connection failure</span></span>

<span data-ttu-id="77bc7-340">Bir bağlantı hatası ve sıfırlama sunucusu bağlantısı büyük olasılıkla yüklenen dosyanın Kestrel'in maksimum istek gövde boyutunu aştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="77bc7-341">Daha fazla bilgi için [Kestrel maksimum istek gövde boyutu](#kestrel-maximum-request-body-size) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="77bc7-342">Kerkenez istemci bağlantı limitleri de ayar gerektirir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="77bc7-343">IFormFile ile Null Referans Özel Durum</span><span class="sxs-lookup"><span data-stu-id="77bc7-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="77bc7-344">Denetleyici yüklenen dosyaları <xref:Microsoft.AspNetCore.Http.IFormFile> kullanarak kabul ediyorsa `null`ancak değeri şuise, HTML `enctype` formunun bir değer belirtdiğini `multipart/form-data`doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="77bc7-345">Bu öznitelik `<form>` öğe üzerinde ayarlı değilse, dosya yükleme sayılmaz ve bağlı <xref:Microsoft.AspNetCore.Http.IFormFile> bağımsız değişkenler . `null`</span><span class="sxs-lookup"><span data-stu-id="77bc7-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="77bc7-346">Ayrıca, [form verilerindeki yükleme adlandırmasının uygulamanın adlandırmayla eşleştiğini](#match-name-attribute-value-to-parameter-name-of-post-method)de doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="77bc7-347">Akış çok uzundu.</span><span class="sxs-lookup"><span data-stu-id="77bc7-347">Stream was too long</span></span>

<span data-ttu-id="77bc7-348">Bu konudaki örnekler, <xref:System.IO.MemoryStream> yüklenen dosyanın içeriğini tutmak için direnitir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="77bc7-349">A'nın `MemoryStream` boyut `int.MaxValue`sınırı.</span><span class="sxs-lookup"><span data-stu-id="77bc7-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="77bc7-350">Uygulamanın dosya yükleme senaryosu 50 MB'dan büyük dosya içeriğinin tutulmasını gerektiriyorsa, `MemoryStream` yüklenen bir dosyanın içeriğini tutmak için tek bir dosyaya dayanmayan alternatif bir yaklaşım kullanın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77bc7-351">ASP.NET Core, daha küçük dosyalar için arabelleğe alınan modeli ve daha büyük dosyalar için arabelleğe alamayan akışı kullanarak bir veya daha fazla dosya yüklemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="77bc7-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="77bc7-352">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="77bc7-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="77bc7-353">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="77bc7-353">Security considerations</span></span>

<span data-ttu-id="77bc7-354">Kullanıcılara bir sunucuya dosya yükleme olanağı sağlarken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="77bc7-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="77bc7-355">Saldırganlar şunları deneyebilir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="77bc7-356">Hizmet reddi saldırılarını [gerçekleştirin.](/windows-hardware/drivers/ifs/denial-of-service)</span><span class="sxs-lookup"><span data-stu-id="77bc7-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="77bc7-357">Virüs veya kötü amaçlı yazılım yükleyin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="77bc7-358">Ağları ve sunucuları başka şekillerde tehlikeye at.</span><span class="sxs-lookup"><span data-stu-id="77bc7-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="77bc7-359">Başarılı bir saldırı olasılığını azaltan güvenlik adımları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="77bc7-360">Dosyaları özel bir dosya yükleme alanına, tercihen sistem dışı bir sürücüye yükleyin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="77bc7-361">Özel bir konum, yüklenen dosyalara güvenlik kısıtlamaları uygulamamayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="77bc7-362">Dosya yükleme konumundaki yürütme izinlerini devre dışı kaltın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="77bc7-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="77bc7-363">Yüklenen dosyaları uygulamayla aynı dizin ağacında kalıcı olarak **yapmayın.**&dagger;</span><span class="sxs-lookup"><span data-stu-id="77bc7-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="77bc7-364">Uygulama tarafından belirlenen güvenli bir dosya adı kullanın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="77bc7-365">Kullanıcı tarafından sağlanan bir dosya adını veya yüklenen dosyanın güvenilmeyen dosya adını kullanmayın. &dagger; HTML, görüntülenirken güvenilmeyen dosya adını kodlar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="77bc7-366">Örneğin, dosya adını günlüğe kaydetme veya Kullanıcı Arabirimi'nde görüntülenmesi (Razor otomatik olarak HTML çıktıyı kodlar).</span><span class="sxs-lookup"><span data-stu-id="77bc7-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="77bc7-367">Uygulamanın tasarım belirtimi için yalnızca onaylı dosya uzantılarına izin verin.&dagger;</span><span class="sxs-lookup"><span data-stu-id="77bc7-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="77bc7-368">İstemci tarafı denetimlerinin sunucuda gerçekleştirildiğini doğrulayın. &dagger; İstemci tarafı denetimleri atlatmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="77bc7-369">Yüklenen bir dosyanın boyutunu denetleyin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="77bc7-370">Büyük yüklemeleri önlemek için maksimum boyut sınırı ayarlayın.&dagger;</span><span class="sxs-lookup"><span data-stu-id="77bc7-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="77bc7-371">Dosyaların aynı ada sahip yüklenmiş bir dosya tarafından üzerine yazılmaması gerektiğinde, dosyayı yüklemeden önce dosya adını veritabanıyla veya fiziksel depolama alanına karşı denetleyin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="77bc7-372">**Dosya depolandırılmadan önce yüklenen içeriğe virüs/kötü amaçlı yazılım tarayıcısı çalıştırın.**</span><span class="sxs-lookup"><span data-stu-id="77bc7-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="77bc7-373">&dagger;Örnek uygulama, ölçütleri karşılayan bir yaklaşım gösterir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="77bc7-374">Kötü amaçlı kodu bir sisteme yüklemek, sık sık aşağıdakileri yapan kodu yürütmenin ilk adımıdır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="77bc7-375">Bir sistemin kontrolünü tamamen ele geçirmek.</span><span class="sxs-lookup"><span data-stu-id="77bc7-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="77bc7-376">Sistemin çöktüğı sonucu bir sistem aşırı yükleyin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="77bc7-377">Kullanıcı veya sistem verilerini tehlikeye at.</span><span class="sxs-lookup"><span data-stu-id="77bc7-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="77bc7-378">Genel bir uI grafiti uygulayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="77bc7-379">Kullanıcılardan dosya kabul ederken saldırı yüzey alanını azaltma hakkında bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="77bc7-380">Sınırsız Dosya Yükleme</span><span class="sxs-lookup"><span data-stu-id="77bc7-380">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="77bc7-381">Azure Güvenliği: Kullanıcılardan dosya kabul ederken uygun denetimlerin yerinde olduğundan emin olun</span><span class="sxs-lookup"><span data-stu-id="77bc7-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="77bc7-382">Örnek uygulamadan örnekler de dahil olmak üzere güvenlik önlemlerinin uygulanması hakkında daha fazla bilgi için [Doğrulama](#validation) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="77bc7-383">Depolama senaryoları</span><span class="sxs-lookup"><span data-stu-id="77bc7-383">Storage scenarios</span></span>

<span data-ttu-id="77bc7-384">Dosyalar için ortak depolama seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-384">Common storage options for files include:</span></span>

* <span data-ttu-id="77bc7-385">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="77bc7-385">Database</span></span>

  * <span data-ttu-id="77bc7-386">Küçük dosya yüklemeleri için veritabanı genellikle fiziksel depolama (dosya sistemi veya ağ paylaşımı) seçeneklerinden daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="77bc7-387">Kullanıcı verileri için bir veritabanı kaydının alınması dosya içeriğini (örneğin, bir avatar görüntüsü) aynı anda sağlayabildiği için veritabanı genellikle fiziksel depolama seçeneklerinden daha kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="77bc7-388">Veritabanı, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="77bc7-389">Fiziksel depolama (dosya sistemi veya ağ paylaşımı)</span><span class="sxs-lookup"><span data-stu-id="77bc7-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="77bc7-390">Büyük dosya yüklemeleri için:</span><span class="sxs-lookup"><span data-stu-id="77bc7-390">For large file uploads:</span></span>
    * <span data-ttu-id="77bc7-391">Veritabanı sınırları yüklemeboyutunu kısıtlayabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="77bc7-392">Fiziksel depolama genellikle veritabanındaki depolamadan daha az ekonomiktir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="77bc7-393">Fiziksel depolama, veri depolama hizmeti kullanmaktan daha ucuz olabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="77bc7-394">Uygulamanın işlemi, depolama konumuna izinleri okuyup yazmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="77bc7-395">**Yürütme izni asla vermez.**</span><span class="sxs-lookup"><span data-stu-id="77bc7-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="77bc7-396">Veri depolama hizmeti (örneğin, [Azure Blob Depolama)](https://azure.microsoft.com/services/storage/blobs/)</span><span class="sxs-lookup"><span data-stu-id="77bc7-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="77bc7-397">Hizmetler genellikle tek hata noktalarına maruz kalan şirket içi çözümler üzerinde gelişmiş ölçeklenebilirlik ve esneklik sunar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="77bc7-398">Hizmetler, büyük depolama altyapısı senaryolarında potansiyel olarak daha düşük maliyetlidir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="77bc7-399">Daha fazla bilgi için [Quickstart: Object depolamaalanında bir leke oluşturmak için .NET'i kullanın.](/azure/storage/blobs/storage-quickstart-blobs-dotnet)</span><span class="sxs-lookup"><span data-stu-id="77bc7-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="77bc7-400"><xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>Konu gösterir , <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> ancak bir <xref:System.IO.FileStream> <xref:System.IO.Stream>ile çalışırken blob depolama kaydetmek için kullanılabilir .</span><span class="sxs-lookup"><span data-stu-id="77bc7-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="77bc7-401">Dosya yükleme senaryoları</span><span class="sxs-lookup"><span data-stu-id="77bc7-401">File upload scenarios</span></span>

<span data-ttu-id="77bc7-402">Dosya yüklemek için iki genel yaklaşım arabelleğe alma ve akış vardır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="77bc7-403">**Tamponlama**</span><span class="sxs-lookup"><span data-stu-id="77bc7-403">**Buffering**</span></span>

<span data-ttu-id="77bc7-404">Dosyanın tamamı, dosyayı işlemek veya kaydetmek için kullanılan dosyanın C# gösterimi olan bir <xref:Microsoft.AspNetCore.Http.IFormFile>dosyaya doğru okunur.</span><span class="sxs-lookup"><span data-stu-id="77bc7-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="77bc7-405">Dosya yüklemeleri tarafından kullanılan kaynaklar (disk, bellek) eşzamanlı dosya yüklemelerinin sayısına ve boyutuna bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="77bc7-406">Bir uygulama çok fazla yükleme arabelleğe almaya çalışırsa, bellek veya disk alanı bittiğinde site çöker.</span><span class="sxs-lookup"><span data-stu-id="77bc7-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="77bc7-407">Dosya yüklemelerinin boyutu veya sıklığı uygulama kaynaklarını tüketiyorsa, akışı kullanın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="77bc7-408">64 KB'yi aşan tek bir arabelleğe alınan dosya bellekten diskteki geçici bir dosyaya taşınır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="77bc7-409">Küçük dosyaların arabelleğe alma bu konunun aşağıdaki bölümlerinde ele alınmıştır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="77bc7-410">Fiziksel depolama</span><span class="sxs-lookup"><span data-stu-id="77bc7-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="77bc7-411">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="77bc7-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="77bc7-412">**Akış**</span><span class="sxs-lookup"><span data-stu-id="77bc7-412">**Streaming**</span></span>

<span data-ttu-id="77bc7-413">Dosya çok parçalı bir istekten alınır ve uygulama tarafından doğrudan işlenir veya kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="77bc7-414">Akış performansı önemli ölçüde artırmaz.</span><span class="sxs-lookup"><span data-stu-id="77bc7-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="77bc7-415">Akış, dosya yüklerken bellek veya disk alanı taleplerini azaltır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="77bc7-416">Büyük dosyaların akış [akışı, akış bölümüyle](#upload-large-files-with-streaming) birlikte Yükle büyük dosyaları yla kaplıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="77bc7-417">Fiziksel depolamaya bağlanan arabelleğe alınan modelle küçük dosyaları yükleme</span><span class="sxs-lookup"><span data-stu-id="77bc7-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="77bc7-418">Küçük dosyalar yüklemek için çok parçalı bir form kullanın veya JavaScript kullanarak bir POST isteği oluşturun.</span><span class="sxs-lookup"><span data-stu-id="77bc7-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="77bc7-419">Aşağıdaki örnek, tek bir dosyayı *(Pages/BufferedSingleFileUploadPhysical.cshtml)* örnek uygulamada yüklemek için bir Razor Pages formunun kullanımını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="77bc7-420">Aşağıdaki örnek, aşağıdakiler dışında önceki örneğe benzer:</span><span class="sxs-lookup"><span data-stu-id="77bc7-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="77bc7-421">JavaScript's ([ApI Getir](https://developer.mozilla.org/docs/Web/API/Fetch_API)) formun verilerini göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="77bc7-422">Doğrulama yok.</span><span class="sxs-lookup"><span data-stu-id="77bc7-422">There's no validation.</span></span>

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

<span data-ttu-id="77bc7-423">[ApI'yi desteklemeyen](https://caniuse.com/#feat=fetch)istemciler için JavaScript'te POST formunu gerçekleştirmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="77bc7-424">Bir Fetch Polyfill kullanın (örneğin, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="77bc7-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="77bc7-425">`XMLHttpRequest` adresini kullanın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="77bc7-426">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="77bc7-426">For example:</span></span>

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

<span data-ttu-id="77bc7-427">Dosya yüklemelerini desteklemek için HTML formlarında bir kodlama`enctype`türü `multipart/form-data`belirtilmelidir .</span><span class="sxs-lookup"><span data-stu-id="77bc7-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="77bc7-428">Birden `files` çok dosya yüklemeyi destekleyen bir `multiple` giriş öğesi `<input>` için öğedeki özniteliği sağlar:</span><span class="sxs-lookup"><span data-stu-id="77bc7-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="77bc7-429">Sunucuya yüklenen tek tek dosyalara [Model Bağlama](xref:mvc/models/model-binding) <xref:Microsoft.AspNetCore.Http.IFormFile>ile erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="77bc7-430">Örnek uygulama, veritabanı ve fiziksel depolama senaryoları için birden çok arabelleğe alınan dosya yüklemesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="77bc7-431">Görüntülemek **not** ve `FileName` günlüğe <xref:Microsoft.AspNetCore.Http.IFormFile> kaydetme dışında özelliğikullanmayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="77bc7-432">Html, görüntüveya günlüğe kaydetme de dosya adını kodlar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="77bc7-433">Saldırgan, tam yollar veya göreli yollar da dahil olmak üzere kötü amaçlı bir dosya adı sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="77bc7-434">Uygulamalar:</span><span class="sxs-lookup"><span data-stu-id="77bc7-434">Applications should:</span></span>
>
> * <span data-ttu-id="77bc7-435">Kullanıcı tarafından sağlanan dosya adından yolu kaldırın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="77bc7-436">HTML kodlu, yol kaldırılmış dosya adını Kullanıcı Arabirimi veya günlüğe kaydetmeye kaydedin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="77bc7-437">Depolama için yeni bir rasgele dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="77bc7-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="77bc7-438">Aşağıdaki kod, yolu dosya adından kaldırır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="77bc7-439">Şimdiye kadar verilen örnekler güvenlik hususları dikkate almaz.</span><span class="sxs-lookup"><span data-stu-id="77bc7-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="77bc7-440">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="77bc7-441">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="77bc7-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="77bc7-442">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="77bc7-442">Validation</span></span>](#validation)

<span data-ttu-id="77bc7-443">Model bağlama ve <xref:Microsoft.AspNetCore.Http.IFormFile>, eylem yöntemi ni kullanarak dosya yüklerken şunları kabul edebilir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="77bc7-444">Tek <xref:Microsoft.AspNetCore.Http.IFormFile>bir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="77bc7-445">Birkaç dosyayı temsil eden aşağıdaki koleksiyonlardan herhangi biri:</span><span class="sxs-lookup"><span data-stu-id="77bc7-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="77bc7-446">[Liste](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="77bc7-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="77bc7-447">Bağlama eşleşmeleri form dosyalarını ada göre bağlar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-447">Binding matches form files by name.</span></span> <span data-ttu-id="77bc7-448">Örneğin, HTML `name` değeri `<input type="file" name="formFile">` C# parametresi/özellik bağlı`FormFile`( eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="77bc7-449">Daha fazla bilgi için POST yöntemi bölümünün [parametre adına Maç adı özniteliği değerine](#match-name-attribute-value-to-parameter-name-of-post-method) bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="77bc7-450">Aşağıdaki örnek:</span><span class="sxs-lookup"><span data-stu-id="77bc7-450">The following example:</span></span>

* <span data-ttu-id="77bc7-451">Yüklenen bir veya daha fazla dosyayı döngüye sokabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="77bc7-452">Dosya adı da dahil olmak üzere bir dosya için tam bir yol döndürmek için [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="77bc7-453">Uygulama tarafından oluşturulan bir dosya adını kullanarak dosyaları yerel dosya sistemine kaydeder.</span><span class="sxs-lookup"><span data-stu-id="77bc7-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="77bc7-454">Yüklenen dosyaların toplam sayısını ve boyutunu verir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-454">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="77bc7-455">Yol `Path.GetRandomFileName` olmayan bir dosya adı oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="77bc7-456">Aşağıdaki örnekte, yol yapılandırmadan elde edilir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-456">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="77bc7-457">Yol için geçirilen <xref:System.IO.FileStream> dosya adı *içermelidir.*</span><span class="sxs-lookup"><span data-stu-id="77bc7-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="77bc7-458">Dosya adı sağlanmadıysa, çalışma <xref:System.UnauthorizedAccessException> saatinde bir a.k.</span><span class="sxs-lookup"><span data-stu-id="77bc7-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="77bc7-459"><xref:Microsoft.AspNetCore.Http.IFormFile> Teknik kullanılarak yüklenen dosyalar, işleme den önce bellekte veya sunucudaki diskte arabelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="77bc7-460">Eylem yöntemi içinde, <xref:Microsoft.AspNetCore.Http.IFormFile> içeriği ne <xref:System.IO.Stream>olarak erişilebilir bir .</span><span class="sxs-lookup"><span data-stu-id="77bc7-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="77bc7-461">Yerel dosya sistemine ek olarak, dosyalar ağ paylaşımına veya [Azure Blob depolama](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)gibi bir dosya depolama hizmetine kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="77bc7-462">Yüklemek için birden çok dosyayı döngüye alan ve güvenli dosya adlarını kullanan başka bir örnek için, örnek uygulamada *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* s. bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="77bc7-463">[Path.GetTempFileName,](xref:System.IO.Path.GetTempFileName*) önceki <xref:System.IO.IOException> geçici dosyaları silmeden 65.535'ten fazla dosya oluşturulursa atar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="77bc7-464">65.535 dosya sınırı sunucu başına bir sınırdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="77bc7-465">Windows işletim sistemi yle ilgili bu sınır hakkında daha fazla bilgi için aşağıdaki konulardaki açıklamalara bakın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="77bc7-466">GetTempFileNameA fonksiyonu</span><span class="sxs-lookup"><span data-stu-id="77bc7-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="77bc7-467">Veritabanına arabelleğe alınan modele sahip küçük dosyaları yükleme</span><span class="sxs-lookup"><span data-stu-id="77bc7-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="77bc7-468">[Entity Framework'ü](/ef/core/index)kullanarak ikili dosya verilerini <xref:System.Byte> bir veritabanında depolamak için varlık üzerinde bir dizi özelliği tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="77bc7-469">Bir içeren sınıf için bir sayfa <xref:Microsoft.AspNetCore.Http.IFormFile>modeli özelliği belirtin:</span><span class="sxs-lookup"><span data-stu-id="77bc7-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="77bc7-470"><xref:Microsoft.AspNetCore.Http.IFormFile>doğrudan bir eylem yöntemi parametresi olarak veya bağlı bir model özelliği olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="77bc7-471">Önceki örnek, bağlı bir model özelliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="77bc7-472">`FileUpload` Razor Pages formunda kullanılır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-472">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="77bc7-473">Form sunucuya verildiğinde, akışı <xref:Microsoft.AspNetCore.Http.IFormFile> bir akışa kopyalayın ve veritabanına bayt dizisi olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="77bc7-474">Aşağıdaki örnekte, `_dbContext` uygulamanın veritabanı bağlamını saklar:</span><span class="sxs-lookup"><span data-stu-id="77bc7-474">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="77bc7-475">Önceki örnek, örnek uygulamada gösterilen bir senaryoya benzer:</span><span class="sxs-lookup"><span data-stu-id="77bc7-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="77bc7-476">*Sayfalar/TamponluSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="77bc7-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="77bc7-477">*Sayfalar/TamponluTekFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="77bc7-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="77bc7-478">Performansı olumsuz etkileyebileceğigibi, ikili verileri ilişkisel veritabanlarında depolamak için dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="77bc7-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="77bc7-479">Doğrulama `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> olmadan özelliğine güvenmeyin veya güvenmeyin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="77bc7-480">Özellik `FileName` yalnızca görüntüleme amacıyla ve yalnızca HTML kodlaması sonrasında kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="77bc7-481">Verilen örnekler güvenlik hususlarını dikkate almaz.</span><span class="sxs-lookup"><span data-stu-id="77bc7-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="77bc7-482">Ek bilgiler aşağıdaki bölümler ve [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)tarafından sağlanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="77bc7-483">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="77bc7-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="77bc7-484">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="77bc7-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="77bc7-485">Akışla büyük dosyaları yükleme</span><span class="sxs-lookup"><span data-stu-id="77bc7-485">Upload large files with streaming</span></span>

<span data-ttu-id="77bc7-486">Aşağıdaki örnek, bir dosyayı denetleyici eylemine aktarmak için JavaScript'in nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="77bc7-487">Dosyanın antiforgery belirteci özel bir filtre özniteliği kullanılarak oluşturulur ve istek gövdesi yerine istemci HTTP üstbilgilerine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="77bc7-488">Eylem yöntemi yüklenen verileri doğrudan işlediğiiçin, form modeli bağlama başka bir özel filtre tarafından devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="77bc7-489">Eylem içinde, formun içeriği, her `MultipartReader`bir bireyi `MultipartSection`okuyan, dosyayı işleyen veya içeriği uygun şekilde depolayan bir , kullanılarak okunur.</span><span class="sxs-lookup"><span data-stu-id="77bc7-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="77bc7-490">Çok parçalı bölümler okunduktan sonra, eylem kendi model bağlama gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="77bc7-491">İlk sayfa yanıtı formu yükler ve bir çerez (öznitelik üzerinden) `GenerateAntiforgeryTokenCookieAttribute` bir antiforgery belirteci kaydeder.</span><span class="sxs-lookup"><span data-stu-id="77bc7-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="77bc7-492">Öznitelik, istek belirteci içeren bir çerez ayarlamak için ASP.NET Core'un yerleşik [antiforgery desteğini](xref:security/anti-request-forgery) kullanır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="77bc7-493">Model `DisableFormValueModelBindingAttribute` bağlamadevre dışı kalmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="77bc7-494">`GenerateAntiforgeryTokenCookieAttribute` Örnek uygulamada ve `DisableFormValueModelBindingAttribute` sayfa uygulama modellerine filtre olarak `/StreamedSingleFileUploadDb` uygulanır `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` ve [Jilet Sayfaları kuralları](xref:razor-pages/razor-pages-conventions)nı kullanır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="77bc7-495">Model bağlama formu okumadığından, formdan bağlanan parametreler bağlanmaz (sorgu, rota ve üstbilgi çalışmaya devam eder).</span><span class="sxs-lookup"><span data-stu-id="77bc7-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="77bc7-496">Eylem yöntemi doğrudan `Request` özellik ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="77bc7-497">A `MultipartReader` her bölümü okumak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="77bc7-498">Anahtar/değer verileri bir `KeyValueAccumulator`.</span><span class="sxs-lookup"><span data-stu-id="77bc7-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="77bc7-499">Çok parçalı bölümler okunduktan sonra, `KeyValueAccumulator` form verilerini bir model türüne bağlamak için içeriği kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="77bc7-500">EF `StreamingController.UploadDatabase` Core ile bir veritabanına akış için tam yöntem:</span><span class="sxs-lookup"><span data-stu-id="77bc7-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="77bc7-501">`MultipartRequestHelper`(*Yardımcı Programlar/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="77bc7-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="77bc7-502">Fiziksel `StreamingController.UploadPhysical` bir konuma akış için tam yöntem:</span><span class="sxs-lookup"><span data-stu-id="77bc7-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="77bc7-503">Örnek uygulamada, doğrulama denetimleri `FileHelpers.ProcessStreamedFile`.</span><span class="sxs-lookup"><span data-stu-id="77bc7-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="77bc7-504">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="77bc7-504">Validation</span></span>

<span data-ttu-id="77bc7-505">Örnek uygulamanın `FileHelpers` sınıfı, arabelleğe alınan ve <xref:Microsoft.AspNetCore.Http.IFormFile> akışlı dosya yüklemeleri için birkaç denetim gösterir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="77bc7-506">Arabelleğe alınan dosya yüklemelerini örnek uygulamada <xref:Microsoft.AspNetCore.Http.IFormFile> `ProcessFormFile` işlemek için *Yardımcı Programlar/FileHelpers.cs* dosyasındaki yönteme bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="77bc7-507">Akışlı dosyaları işlemek için `ProcessStreamedFile` yönteme aynı dosyada bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="77bc7-508">Örnek uygulamada gösterilen doğrulama işleme yöntemleri yüklenen dosyaların içeriğini tazmaz.</span><span class="sxs-lookup"><span data-stu-id="77bc7-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="77bc7-509">Çoğu üretim senaryosunda, dosyayı kullanıcılar veya diğer sistemler için kullanılabilir hale getirmeden önce dosyada bir virüs/kötü amaçlı yazılım tarayıcı API kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="77bc7-510">Konu örneği doğrulama tekniklerinin çalışma örneğini sağlasa `FileHelpers` da, aşağıdakiler olmadıkça sınıfı bir üretim uygulamasında uygulamayın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="77bc7-511">Uygulamayı tamamen anlayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="77bc7-512">Uygulamayı uygulamanın ortamına ve özelliklerine uygun şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="77bc7-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="77bc7-513">**Bu gereksinimleri karşılamadan bir uygulamada hiçbir uygulamada ayrım gözetmeksizin güvenlik kodu uygulamayın.**</span><span class="sxs-lookup"><span data-stu-id="77bc7-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="77bc7-514">İçerik doğrulama</span><span class="sxs-lookup"><span data-stu-id="77bc7-514">Content validation</span></span>

<span data-ttu-id="77bc7-515">**Yüklenen içerikte üçüncü taraf virüs/kötü amaçlı yazılım taraması API'si kullanın.**</span><span class="sxs-lookup"><span data-stu-id="77bc7-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="77bc7-516">Dosyaları taramak, yüksek hacimli senaryolarda sunucu kaynaklarında talep tedir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="77bc7-517">Dosya taraması nedeniyle istek işleme performansı azalmışsa, tarama çalışmasını bir [arka plan hizmetine](xref:fundamentals/host/hosted-services)(muhtemelen uygulamanın sunucusundan farklı bir sunucuda çalışan bir hizmete) yüklemeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="77bc7-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="77bc7-518">Genellikle, yüklenen dosyalar, arka plan virüs tarayıcısı bunları kontrol edene kadar karantinaya alınan bir alanda tutulur.</span><span class="sxs-lookup"><span data-stu-id="77bc7-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="77bc7-519">Dosya geçtiğinde, dosya normal dosya depolama konumuna taşınır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="77bc7-520">Bu adımlar genellikle bir dosyanın tarama durumunu gösteren bir veritabanı kaydı ile birlikte gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="77bc7-521">Böyle bir yaklaşım kullanarak, uygulama ve uygulama sunucusu istekleri yanıtlamaya odaklanmış kalır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="77bc7-522">Dosya uzantısı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="77bc7-522">File extension validation</span></span>

<span data-ttu-id="77bc7-523">Yüklenen dosyanın uzantısı, izin verilen uzantılar listesiyle karşılaştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="77bc7-524">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="77bc7-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="77bc7-525">Dosya imza doğrulaması</span><span class="sxs-lookup"><span data-stu-id="77bc7-525">File signature validation</span></span>

<span data-ttu-id="77bc7-526">Bir dosyanın imzası, bir dosyanın başındaki ilk birkaç bayt tarafından belirlenir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="77bc7-527">Bu baytlar, uzantının dosyanın içeriğiyle eşleşip eşleşmemediğini belirtmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="77bc7-528">Örnek uygulama, birkaç yaygın dosya türü için dosya imzalarını denetler.</span><span class="sxs-lookup"><span data-stu-id="77bc7-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="77bc7-529">Aşağıdaki örnekte, JPEG görüntüsünün dosya imzası dosyayla karşılaştırılır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="77bc7-530">Ek dosya imzaları almak için [Dosya İmzaları Veritabanı'na](https://www.filesignatures.net/) ve resmi dosya özelliklerine bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="77bc7-531">Dosya adı güvenliği</span><span class="sxs-lookup"><span data-stu-id="77bc7-531">File name security</span></span>

<span data-ttu-id="77bc7-532">Bir dosyayı fiziksel depolama alanına kaydetmek için istemci tarafından sağlanan bir dosya adını asla kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="77bc7-533">Geçici depolama alanı için tam bir yol (dosya adı dahil) oluşturmak için [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) veya [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) kullanarak dosya için güvenli bir dosya adı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="77bc7-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="77bc7-534">Razor HTML, görüntü lenmek için özellik değerlerini otomatik olarak kodlar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-534">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="77bc7-535">Aşağıdaki kodukullanmak güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="77bc7-536">Razor dışında, <xref:System.Net.WebUtility.HtmlEncode*> her zaman bir kullanıcının isteğinden ad içeriği dosya.</span><span class="sxs-lookup"><span data-stu-id="77bc7-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="77bc7-537">Birçok uygulama, dosyanın var olup olmadığını denetlemeyi içermelidir; aksi takdirde, dosya aynı adı taşıyan bir dosya tarafından üzerine yazılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="77bc7-538">Uygulamanızın teknik özelliklerini karşılamak için ek mantık kaynağı.</span><span class="sxs-lookup"><span data-stu-id="77bc7-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="77bc7-539">Boyut doğrulama</span><span class="sxs-lookup"><span data-stu-id="77bc7-539">Size validation</span></span>

<span data-ttu-id="77bc7-540">Yüklenen dosyaların boyutunu sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="77bc7-541">Örnek uygulamada, dosyanın boyutu 2 MB ile sınırlıdır (baytlarda gösterilir).</span><span class="sxs-lookup"><span data-stu-id="77bc7-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="77bc7-542">Limit *appsettings.json* dosyasından [Configuration](xref:fundamentals/configuration/index) üzerinden sağlanır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="77bc7-543">Sınıflara `FileSizeLimit` `PageModel` enjekte edilir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="77bc7-544">Dosya boyutu sınırı aştığında, dosya reddedilir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="77bc7-545">POST yönteminin parametre adı ile eşleme adı özniteliği değeri</span><span class="sxs-lookup"><span data-stu-id="77bc7-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="77bc7-546">POST'un veri oluşturduğu veya doğrudan JavaScript'i `FormData` kullandığı Jilet olmayan formlarda, `FormData` formun öğesinde belirtilen ad veya denetleyicinin eyleminde parametrenin adı ile eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="77bc7-547">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="77bc7-547">In the following example:</span></span>

* <span data-ttu-id="77bc7-548">Bir `<input>` öğe kullanırken, `name` öznitelik değere `battlePlans`ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="77bc7-549">JavaScript'te kullanırken, `FormData` ad değerine `battlePlans`ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="77bc7-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="77bc7-550">C# yönteminin parametresi için eşleşen`battlePlans`bir ad kullanın ( ):</span><span class="sxs-lookup"><span data-stu-id="77bc7-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="77bc7-551">Adlı bir Razor Pages sayfa `Upload`işleyicisi yöntemi için:</span><span class="sxs-lookup"><span data-stu-id="77bc7-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="77bc7-552">MVC POST denetleyici eylem yöntemi için:</span><span class="sxs-lookup"><span data-stu-id="77bc7-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="77bc7-553">Sunucu ve uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="77bc7-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="77bc7-554">Çok parçalı gövde uzunluk sınırı</span><span class="sxs-lookup"><span data-stu-id="77bc7-554">Multipart body length limit</span></span>

<span data-ttu-id="77bc7-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>her çok parçalı gövdenin uzunluğu için sınırı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="77bc7-556">Bu sınırı aşan form <xref:System.IO.InvalidDataException> bölümleri ayrıştırıldığında bir atar.</span><span class="sxs-lookup"><span data-stu-id="77bc7-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="77bc7-557">Varsayılan değer 134.217.728 (128 MB) 'dir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="77bc7-558">Aşağıdaki ayarı kullanarak <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sınırı `Startup.ConfigureServices`özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="77bc7-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="77bc7-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>tek bir sayfa <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> veya eylem için ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="77bc7-560">Razor Pages uygulamasında, filtreyi aşağıdaki ler `Startup.ConfigureServices`için bir [kuralla](xref:razor-pages/razor-pages-conventions) uygulayın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="77bc7-561">Razor Pages uygulamasında veya Bir MVC uygulamasında, filtreyi sayfa modeline veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="77bc7-562">Kerkenez maksimum istek vücut boyutu</span><span class="sxs-lookup"><span data-stu-id="77bc7-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="77bc7-563">Kestrel tarafından barındırılan uygulamalar için varsayılan maksimum istek gövde boyutu yaklaşık 28,6 MB olan 30.000.000 bayttır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="77bc7-564">[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel sunucu seçeneğini kullanarak sınırı özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="77bc7-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="77bc7-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>[MaxRequestBodySize'ı](xref:fundamentals/servers/kestrel#maximum-request-body-size) tek bir sayfa veya eylem için ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="77bc7-566">Razor Pages uygulamasında, filtreyi aşağıdaki ler `Startup.ConfigureServices`için bir [kuralla](xref:razor-pages/razor-pages-conventions) uygulayın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="77bc7-567">Razor sayfaları uygulamasında veya Bir MVC uygulamasında, filtreyi sayfa işleyicisi sınıfına veya eylem yöntemine uygulayın:</span><span class="sxs-lookup"><span data-stu-id="77bc7-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="77bc7-568">Diğer Kerkenez limitleri</span><span class="sxs-lookup"><span data-stu-id="77bc7-568">Other Kestrel limits</span></span>

<span data-ttu-id="77bc7-569">Diğer Kerkenez limitleri Kerkenez tarafından barındırılan uygulamalar için geçerli olabilir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="77bc7-570">Maksimum istemci bağlantıları</span><span class="sxs-lookup"><span data-stu-id="77bc7-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="77bc7-571">İstek ve yanıt veri oranları</span><span class="sxs-lookup"><span data-stu-id="77bc7-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="77bc7-572">IIS içerik uzunluğu sınırı</span><span class="sxs-lookup"><span data-stu-id="77bc7-572">IIS content length limit</span></span>

<span data-ttu-id="77bc7-573">Varsayılan istek sınırı`maxAllowedContentLength`( ) yaklaşık 28,6 MB olan 30.000.000 bayttır.</span><span class="sxs-lookup"><span data-stu-id="77bc7-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="77bc7-574">*web.config* dosyasındaki sınırı özelleştirin:</span><span class="sxs-lookup"><span data-stu-id="77bc7-574">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="77bc7-575">Bu ayar yalnızca IIS için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-575">This setting only applies to IIS.</span></span> <span data-ttu-id="77bc7-576">Kestrel'de barındırma yaparken davranış varsayılan olarak oluşmaz.</span><span class="sxs-lookup"><span data-stu-id="77bc7-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="77bc7-577">Daha fazla bilgi için bkz [>. \< ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)</span><span class="sxs-lookup"><span data-stu-id="77bc7-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="77bc7-578">ASP.NET Çekirdek Modülündeki sınırlamalar veya IIS İstek Filtreleme Modülü'nün varlığı yüklemeleri 2 veya 4 GB ile sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="77bc7-579">Daha fazla bilgi için bkz: [2GB boyutundan büyük dosya yükleyemiyorum (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span><span class="sxs-lookup"><span data-stu-id="77bc7-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="77bc7-580">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="77bc7-580">Troubleshoot</span></span>

<span data-ttu-id="77bc7-581">Aşağıda, dosya yükleme ve bunların olası çözümleriyle çalışırken karşılaşılan bazı sık karşılaşılan sorunlar verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="77bc7-582">Bir IIS sunucusuna dağıtıldığında bulunamadı hatası</span><span class="sxs-lookup"><span data-stu-id="77bc7-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="77bc7-583">Aşağıdaki hata, yüklenen dosyanın sunucunun yapılandırılmış içerik uzunluğunu aştığını gösterir:</span><span class="sxs-lookup"><span data-stu-id="77bc7-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="77bc7-584">Sınırı artırma hakkında daha fazla bilgi için [IIS içerik uzunluğu sınırı](#iis-content-length-limit) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="77bc7-585">Bağlantı hatası</span><span class="sxs-lookup"><span data-stu-id="77bc7-585">Connection failure</span></span>

<span data-ttu-id="77bc7-586">Bir bağlantı hatası ve sıfırlama sunucusu bağlantısı büyük olasılıkla yüklenen dosyanın Kestrel'in maksimum istek gövde boyutunu aştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="77bc7-587">Daha fazla bilgi için [Kestrel maksimum istek gövde boyutu](#kestrel-maximum-request-body-size) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="77bc7-588">Kerkenez istemci bağlantı limitleri de ayar gerektirir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="77bc7-589">IFormFile ile Null Referans Özel Durum</span><span class="sxs-lookup"><span data-stu-id="77bc7-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="77bc7-590">Denetleyici yüklenen dosyaları <xref:Microsoft.AspNetCore.Http.IFormFile> kullanarak kabul ediyorsa `null`ancak değeri şuise, HTML `enctype` formunun bir değer belirtdiğini `multipart/form-data`doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="77bc7-591">Bu öznitelik `<form>` öğe üzerinde ayarlı değilse, dosya yükleme sayılmaz ve bağlı <xref:Microsoft.AspNetCore.Http.IFormFile> bağımsız değişkenler . `null`</span><span class="sxs-lookup"><span data-stu-id="77bc7-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="77bc7-592">Ayrıca, [form verilerindeki yükleme adlandırmasının uygulamanın adlandırmayla eşleştiğini](#match-name-attribute-value-to-parameter-name-of-post-method)de doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="77bc7-593">Akış çok uzundu.</span><span class="sxs-lookup"><span data-stu-id="77bc7-593">Stream was too long</span></span>

<span data-ttu-id="77bc7-594">Bu konudaki örnekler, <xref:System.IO.MemoryStream> yüklenen dosyanın içeriğini tutmak için direnitir.</span><span class="sxs-lookup"><span data-stu-id="77bc7-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="77bc7-595">A'nın `MemoryStream` boyut `int.MaxValue`sınırı.</span><span class="sxs-lookup"><span data-stu-id="77bc7-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="77bc7-596">Uygulamanın dosya yükleme senaryosu 50 MB'dan büyük dosya içeriğinin tutulmasını gerektiriyorsa, `MemoryStream` yüklenen bir dosyanın içeriğini tutmak için tek bir dosyaya dayanmayan alternatif bir yaklaşım kullanın.</span><span class="sxs-lookup"><span data-stu-id="77bc7-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="77bc7-597">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="77bc7-597">Additional resources</span></span>

* [<span data-ttu-id="77bc7-598">Sınırsız Dosya Yükleme</span><span class="sxs-lookup"><span data-stu-id="77bc7-598">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="77bc7-599">Azure Güvenliği: Güvenlik Çerçevesi: Giriş Doğrulama | Azaltıcı etken</span><span class="sxs-lookup"><span data-stu-id="77bc7-599">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="77bc7-600">Azure Bulut Tasarım Desenleri: Vale Anahtarı deseni</span><span class="sxs-lookup"><span data-stu-id="77bc7-600">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
