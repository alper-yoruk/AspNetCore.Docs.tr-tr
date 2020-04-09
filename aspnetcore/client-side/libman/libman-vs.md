---
title: Visual Studio'da ASP.NET Core ile LibMan'ı Kullanın
author: scottaddie
description: Visual Studio ile ASP.NET Core projesinde LibMan'ı nasıl kullanacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658314"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="6c120-103">Visual Studio'da ASP.NET Core ile LibMan'ı Kullanın</span><span class="sxs-lookup"><span data-stu-id="6c120-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="6c120-104">Yazar: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="6c120-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="6c120-105">Visual Studio, ASP.NET Core projelerinde [LibMan'a](xref:client-side/libman/index) şunları dahil olmak üzere yerleşik destek vermektedir:</span><span class="sxs-lookup"><span data-stu-id="6c120-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="6c120-106">LibMan geri yükleme işlemlerini yapılandırma ve çalıştırma desteği.</span><span class="sxs-lookup"><span data-stu-id="6c120-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="6c120-107">LibMan geri yükleme ve temizleme işlemlerini tetiklemek için menü öğeleri.</span><span class="sxs-lookup"><span data-stu-id="6c120-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="6c120-108">Kitaplıkları bulmak ve dosyaları projeye eklemek için iletişim kutusunda arama yapın.</span><span class="sxs-lookup"><span data-stu-id="6c120-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="6c120-109">LibMan manifesto dosyası *libman.json*&mdash;için düzenleme desteği.</span><span class="sxs-lookup"><span data-stu-id="6c120-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="6c120-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(nasıl indirilir)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="6c120-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6c120-111">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="6c120-111">Prerequisites</span></span>

* <span data-ttu-id="6c120-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) **ASP.NET ve web geliştirme** iş yükü ile</span><span class="sxs-lookup"><span data-stu-id="6c120-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="6c120-113">Kitaplık dosyaları ekleme</span><span class="sxs-lookup"><span data-stu-id="6c120-113">Add library files</span></span>

<span data-ttu-id="6c120-114">Kitaplık dosyaları ASP.NET Core projesine iki farklı şekilde eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="6c120-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="6c120-115">İstemci Tarafı Kitaplığı Ekle iletişim kutusunu kullanma</span><span class="sxs-lookup"><span data-stu-id="6c120-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="6c120-116">LibMan bildirim dosya girişlerini el ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6c120-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="6c120-117">İstemci Tarafı Kitaplığı Ekle iletişim kutusunu kullanma</span><span class="sxs-lookup"><span data-stu-id="6c120-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="6c120-118">İstemci tarafı kitaplığı yüklemek için aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="6c120-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="6c120-119">**Çözüm Gezgini'nde,** dosyaların eklenmesi gereken proje klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6c120-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="6c120-120">**İstemci Tarafı Kitaplığı** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="6c120-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="6c120-121">**İstemci-Yan Kitaplık Ekle** iletişim kutusu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6c120-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![İstemci Tarafı Kitaplığı ekle iletişim kutusu](_static/add-library-dialog.png)

* <span data-ttu-id="6c120-123">**Sağlayıcı** açılır tarafından kitaplık sağlayıcısını seçin.</span><span class="sxs-lookup"><span data-stu-id="6c120-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="6c120-124">CDNJS varsayılan sağlayıcıdır.</span><span class="sxs-lookup"><span data-stu-id="6c120-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="6c120-125">**Kitaplık** metin kutusuna getirmek için kitaplık adını yazın.</span><span class="sxs-lookup"><span data-stu-id="6c120-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="6c120-126">IntelliSense, sağlanan metinden başlayarak kitaplıkların bir listesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="6c120-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="6c120-127">IntelliSense listesinden kitaplığı seçin.</span><span class="sxs-lookup"><span data-stu-id="6c120-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="6c120-128">Kitaplık adının `@` sembolle ve seçili sağlayıcıtarafından bilinen en son kararlı sürümle sabitlenmiş olduğuna dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="6c120-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="6c120-129">Hangi dosyaların ekleneceğine karar verin:</span><span class="sxs-lookup"><span data-stu-id="6c120-129">Decide which files to include:</span></span>
  * <span data-ttu-id="6c120-130">Kitaplığın tüm dosyalarını eklemek için **tüm kitaplık dosyalarını ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6c120-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="6c120-131">Kitaplığın dosyalarının bir alt kümesini eklemek için **belirli dosyaları** seç düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6c120-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="6c120-132">Radyo düğmesi seçildiğinde, dosya seçici ağaç etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6c120-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="6c120-133">İndirmek için dosya adlarının solundaki kutuları işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="6c120-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="6c120-134">Dosyaları **Hedef Konum** metin kutusunda depolamak için proje klasörünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="6c120-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="6c120-135">Öneri olarak, her kitaplığı ayrı bir klasörde saklayın.</span><span class="sxs-lookup"><span data-stu-id="6c120-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="6c120-136">Önerilen **Hedef Konum** klasörü, iletişim kutusunun başlatıldığı konuma bağlıdır:</span><span class="sxs-lookup"><span data-stu-id="6c120-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="6c120-137">Proje kökünden başlatılırsa:</span><span class="sxs-lookup"><span data-stu-id="6c120-137">If launched from the project root:</span></span>
    * <span data-ttu-id="6c120-138">*wwwroot/lib* *varsa* kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6c120-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="6c120-139">*wwwroot* yoksa *wwwroot* lib kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6c120-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="6c120-140">Bir proje klasöründen başlatılırsa, ilgili klasör adı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6c120-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="6c120-141">Klasör önerisi kitaplık adı ile sabitlenir.</span><span class="sxs-lookup"><span data-stu-id="6c120-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="6c120-142">Aşağıdaki tabloda, jilet sayfaları projesine jQuery yüklerken klasör önerileri gösterilebilir.</span><span class="sxs-lookup"><span data-stu-id="6c120-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="6c120-143">Fırlatma konumu</span><span class="sxs-lookup"><span data-stu-id="6c120-143">Launch location</span></span>                           |<span data-ttu-id="6c120-144">Önerilen klasör</span><span class="sxs-lookup"><span data-stu-id="6c120-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="6c120-145">proje kökü *(wwwroot* varsa)</span><span class="sxs-lookup"><span data-stu-id="6c120-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="6c120-146">*wwwroot/lib/jquery/ adresinden*</span><span class="sxs-lookup"><span data-stu-id="6c120-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="6c120-147">proje kökü *(wwwroot* yoksa)</span><span class="sxs-lookup"><span data-stu-id="6c120-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="6c120-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="6c120-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="6c120-149">Projedeki *sayfalar* klasörü</span><span class="sxs-lookup"><span data-stu-id="6c120-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="6c120-150">*Sayfalar/jquery/*</span><span class="sxs-lookup"><span data-stu-id="6c120-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="6c120-151">Dosyaları indirmek için **yükle** düğmesini tıklatın, *libman.json'daki*yapılandırmaya göre.</span><span class="sxs-lookup"><span data-stu-id="6c120-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="6c120-152">Yükleme ayrıntıları için **Çıktı** penceresinin **Kitaplık Yöneticisi** akışını gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="6c120-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="6c120-153">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="6c120-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="6c120-154">LibMan bildirim dosya girişlerini el ile yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6c120-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="6c120-155">Visual Studio'daki tüm LibMan işlemleri, proje kökünün LibMan manifestosunun *(libman.json)* içeriğine dayanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="6c120-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="6c120-156">Proje için kitaplık dosyalarını yapılandırmak için *libman.json'u* el ile edinebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6c120-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="6c120-157">Visual *Studio, libman.json* kaydedildikten sonra tüm kitaplık dosyalarını geri yükler.</span><span class="sxs-lookup"><span data-stu-id="6c120-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="6c120-158">Düzenleme için *libman.json'u* açmak için aşağıdaki seçenekler vardır:</span><span class="sxs-lookup"><span data-stu-id="6c120-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="6c120-159">**Çözüm Gezgini'ndeki** *libman.json* dosyasına çift tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6c120-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="6c120-160">**Solution Explorer'da** projeyi sağ tıklatın ve **İstemci Tarafı Kitaplıklarını Yönet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="6c120-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="6c120-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="6c120-161">**&#8224;**</span></span>
* <span data-ttu-id="6c120-162">Visual Studio **Project** menüsünden **İstemci Tarafı Kitaplıklarını Yönet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="6c120-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="6c120-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="6c120-163">**&#8224;**</span></span>

<span data-ttu-id="6c120-164">**&#8224;** *libman.json* dosyası proje kökünde zaten yoksa, varsayılan öğe şablonu içeriğiyle oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6c120-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="6c120-165">Visual Studio renklendirme, biçimlendirme, IntelliSense ve şema doğrulama gibi zengin JSON düzenleme desteği sunar.</span><span class="sxs-lookup"><span data-stu-id="6c120-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="6c120-166">LibMan manifestosunun JSON şeması [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span><span class="sxs-lookup"><span data-stu-id="6c120-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="6c120-167">Aşağıdaki bildirim dosyasıyla, LibMan `libraries` özellikte tanımlanan yapılandırmaya göre dosyaları alır.</span><span class="sxs-lookup"><span data-stu-id="6c120-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="6c120-168">İçinde `libraries` tanımlanan nesne literals bir açıklama:</span><span class="sxs-lookup"><span data-stu-id="6c120-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="6c120-169">[jQuery](https://jquery.com/) sürüm 3.3.1'in bir alt kümesi CDNJS sağlayıcısından alınır.</span><span class="sxs-lookup"><span data-stu-id="6c120-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="6c120-170">Alt `files` küme özelliği&mdash;*jquery.min.js*, *jquery.js*ve *jquery.min.map*tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="6c120-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="6c120-171">Dosyalar projenin *wwwroot/lib/jquery* klasörüne yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6c120-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="6c120-172">[Bootstrap](https://getbootstrap.com/) sürüm 4.1.3'ün tamamı alınır ve *wwwroot/lib/bootstrap* klasörüne yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6c120-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="6c120-173">Nesneliter'in `provider` özelliği `defaultProvider` özellik değerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="6c120-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="6c120-174">LibMan, Bootstrap dosyalarını unpkg sağlayıcısından alır.</span><span class="sxs-lookup"><span data-stu-id="6c120-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="6c120-175">[Lodash'in](https://lodash.com/) bir alt kümesi organizasyon içindeki bir yönetim organı tarafından onaylandı.</span><span class="sxs-lookup"><span data-stu-id="6c120-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="6c120-176">*Lodash.js* ve *lodash.min.js* dosyaları C yerel dosya sisteminden *alınır:\\temp\\lodash\\*.</span><span class="sxs-lookup"><span data-stu-id="6c120-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="6c120-177">Dosyalar projenin *wwwroot/lib/lodash* klasörüne kopyalanır.</span><span class="sxs-lookup"><span data-stu-id="6c120-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="6c120-178">LibMan, her sağlayıcıdan her kitaplığın yalnızca bir sürümünü destekler.</span><span class="sxs-lookup"><span data-stu-id="6c120-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="6c120-179">*Libman.json* dosyası, belirli bir sağlayıcı için aynı kitaplık adına sahip iki kitaplık içeriyorsa şema doğrulamayı başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="6c120-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="6c120-180">Kitaplık dosyalarını geri yükleme</span><span class="sxs-lookup"><span data-stu-id="6c120-180">Restore library files</span></span>

<span data-ttu-id="6c120-181">Visual Studio içinden kitaplık dosyalarını geri yüklemek için proje kökünde geçerli bir *libman.json* dosyası olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6c120-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="6c120-182">Geri yüklenen dosyalar projeye her kitaplık için belirtilen konuma yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6c120-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="6c120-183">Kitaplık dosyaları ASP.NET Core projesinde iki şekilde geri yüklenebilir:</span><span class="sxs-lookup"><span data-stu-id="6c120-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="6c120-184">Yapı sırasında dosyaları geri yükleme</span><span class="sxs-lookup"><span data-stu-id="6c120-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="6c120-185">Dosyaları el ile geri yükleme</span><span class="sxs-lookup"><span data-stu-id="6c120-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="6c120-186">Yapı sırasında dosyaları geri yükleme</span><span class="sxs-lookup"><span data-stu-id="6c120-186">Restore files during build</span></span>

<span data-ttu-id="6c120-187">LibMan, yapı işleminin bir parçası olarak tanımlanan kitaplık dosyalarını geri yükleyebilir.</span><span class="sxs-lookup"><span data-stu-id="6c120-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="6c120-188">Varsayılan olarak, *yeniden oluşturma* davranışı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="6c120-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="6c120-189">Yeniden oluşturma davranışını etkinleştirmek ve sınamak için:</span><span class="sxs-lookup"><span data-stu-id="6c120-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="6c120-190">**Solution Explorer'da** *libman.json'a* sağ tıklayın ve bağlam menüsünden **Yapı'da İstemci Tarafı Kitaplıklarını Geri Yükle'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="6c120-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="6c120-191">NuGet paketi yüklemesi istendiğinde **Evet** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="6c120-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="6c120-192">[Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet paketi projeye eklenir:</span><span class="sxs-lookup"><span data-stu-id="6c120-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="6c120-193">LibMan dosya geri yüklemesinin gerçekleştiğini onaylamak için projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6c120-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="6c120-194">Paket, `Microsoft.Web.LibraryManager.Build` projenin oluşturma işlemi sırasında LibMan'ı çalıştıran bir MSBuild hedefi enjekte eder.</span><span class="sxs-lookup"><span data-stu-id="6c120-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="6c120-195">LibMan etkinlik günlüğü için **Çıktı** penceresinin **Yapı** akışını gözden geçirin:</span><span class="sxs-lookup"><span data-stu-id="6c120-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="6c120-196">Yeniden oluşturma davranışı etkinleştirildiğinde, *libman.json* bağlam **menüsü, Yapı'da Istemci Tarafı Kitaplıklarını Devre Dışı Nı Devre-Devre Teşlede** seçeneğini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6c120-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="6c120-197">Bu seçeneğin seçilmesi, `Microsoft.Web.LibraryManager.Build` paket başvurularını proje dosyasından kaldırır.</span><span class="sxs-lookup"><span data-stu-id="6c120-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="6c120-198">Sonuç olarak, istemci tarafındaki kitaplıklar artık her yapıda geri yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="6c120-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="6c120-199">Yeniden oluşturma ayarından bağımsız olarak, *libman.json* bağlam menüsünden istediğiniz zaman el ile geri yükleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6c120-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="6c120-200">Daha fazla bilgi için [dosyaları el ile geri yükle'ye](#restore-files-manually)bakın.</span><span class="sxs-lookup"><span data-stu-id="6c120-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="6c120-201">Dosyaları el ile geri yükleme</span><span class="sxs-lookup"><span data-stu-id="6c120-201">Restore files manually</span></span>

<span data-ttu-id="6c120-202">Kitaplık dosyalarını el ile geri yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="6c120-202">To manually restore library files:</span></span>

* <span data-ttu-id="6c120-203">Çözümdeki tüm projeler için:</span><span class="sxs-lookup"><span data-stu-id="6c120-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="6c120-204">**Çözüm Gezgini'nde**çözüm adını sağ tıklatın.</span><span class="sxs-lookup"><span data-stu-id="6c120-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="6c120-205">**İstemci Tarafı Kitaplıklarını Geri Yükle** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="6c120-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="6c120-206">Belirli bir proje için:</span><span class="sxs-lookup"><span data-stu-id="6c120-206">For a specific project:</span></span>
  * <span data-ttu-id="6c120-207">**Solution Explorer'daki** *libman.json* dosyasına sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6c120-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="6c120-208">**İstemci Tarafı Kitaplıklarını Geri Yükle** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="6c120-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="6c120-209">Geri yükleme işlemi çalışırken:</span><span class="sxs-lookup"><span data-stu-id="6c120-209">While the restore operation is running:</span></span>

* <span data-ttu-id="6c120-210">Visual Studio durum çubuğundaki Görev Durum Merkezi (TSC) simgesi animasyonlu olacak ve yeniden başlatılan *geri yükleme işlemi*okunacak.</span><span class="sxs-lookup"><span data-stu-id="6c120-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="6c120-211">Simgeyi tıklattığınızda, bilinen arka plan görevlerini listeleyen bir araç ipucu açılır.</span><span class="sxs-lookup"><span data-stu-id="6c120-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="6c120-212">İletiler durum çubuğuna ve **Çıktı** penceresinin **Kitaplık Yöneticisi** akışına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6c120-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="6c120-213">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="6c120-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="6c120-214">Kitaplık dosyalarını silme</span><span class="sxs-lookup"><span data-stu-id="6c120-214">Delete library files</span></span>

<span data-ttu-id="6c120-215">Visual Studio'da daha önce geri yüklenen kitaplık dosyalarını silen *temiz* işlemi gerçekleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="6c120-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="6c120-216">**Solution Explorer'daki** *libman.json* dosyasına sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6c120-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="6c120-217">**İstemci Tarafı Kitaplıklarını Temizle** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="6c120-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="6c120-218">Kitaplık dışı dosyaların istemeden kaldırılmasını önlemek için, temiz işlem tüm dizinleri silmez.</span><span class="sxs-lookup"><span data-stu-id="6c120-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="6c120-219">Yalnızca önceki geri yüklemede bulunan dosyaları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="6c120-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="6c120-220">Temiz işlem çalışırken:</span><span class="sxs-lookup"><span data-stu-id="6c120-220">While the clean operation is running:</span></span>

* <span data-ttu-id="6c120-221">Visual Studio durum çubuğundaki TSC simgesi animasyonlu olacak ve *okunacak İstemci kitaplıkları işlemi başlatıldı.*</span><span class="sxs-lookup"><span data-stu-id="6c120-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="6c120-222">Simgeyi tıklattığınızda, bilinen arka plan görevlerini listeleyen bir araç ipucu açılır.</span><span class="sxs-lookup"><span data-stu-id="6c120-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="6c120-223">İletiler durum çubuğuna ve **Çıktı** penceresinin **Kitaplık Yöneticisi** akışına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6c120-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="6c120-224">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="6c120-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="6c120-225">Temiz işlem yalnızca projedeki dosyaları siler.</span><span class="sxs-lookup"><span data-stu-id="6c120-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="6c120-226">Kitaplık dosyaları, gelecekteki geri yükleme işlemlerinde daha hızlı alma için önbellekte kalır.</span><span class="sxs-lookup"><span data-stu-id="6c120-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="6c120-227">Yerel makinenin önbelleğinde depolanan kitaplık dosyalarını yönetmek için [LibMan CLI'yi](xref:client-side/libman/libman-cli)kullanın.</span><span class="sxs-lookup"><span data-stu-id="6c120-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="6c120-228">Kitaplık dosyalarını kaldırma</span><span class="sxs-lookup"><span data-stu-id="6c120-228">Uninstall library files</span></span>

<span data-ttu-id="6c120-229">Kitaplık dosyalarını kaldırmak için:</span><span class="sxs-lookup"><span data-stu-id="6c120-229">To uninstall library files:</span></span>

* <span data-ttu-id="6c120-230">Açık *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="6c120-230">Open *libman.json*.</span></span>
* <span data-ttu-id="6c120-231">Caret'i ilgili `libraries` nesnenin içine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="6c120-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="6c120-232">Sol kenar boşluğunda görünen ampul simgesini tıklatın ve library_name \*\*kaldır \<> library_name@\<library_version>: \*\*</span><span class="sxs-lookup"><span data-stu-id="6c120-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Kitaplık bağlamı menüsü seçeneğini kaldırma](_static/uninstall-menu-option.png)

<span data-ttu-id="6c120-234">Alternatif olarak, el ile edinebilir ve LibMan bildirimi *(libman.json)* kaydedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6c120-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="6c120-235">Dosya kaydedildiğinde [geri yükleme işlemi](#restore-library-files) çalışır.</span><span class="sxs-lookup"><span data-stu-id="6c120-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="6c120-236">*Libman.json'da* artık tanımlanmayan kitaplık dosyaları projeden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="6c120-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="6c120-237">Kitaplık sürümünü güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="6c120-237">Update library version</span></span>

<span data-ttu-id="6c120-238">Güncelleştirilmiş kitaplık sürümünü denetlemek için:</span><span class="sxs-lookup"><span data-stu-id="6c120-238">To check for an updated library version:</span></span>

* <span data-ttu-id="6c120-239">Açık *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="6c120-239">Open *libman.json*.</span></span>
* <span data-ttu-id="6c120-240">Caret'i ilgili `libraries` nesnenin içine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="6c120-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="6c120-241">Sol kenar boşluğunda görünen ampul simgesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="6c120-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="6c120-242">**Güncellemeler için Kontrol'ün**üzerine yıkın.</span><span class="sxs-lookup"><span data-stu-id="6c120-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="6c120-243">LibMan, yüklenen sürümden daha yeni bir kitaplık sürümünü denetler.</span><span class="sxs-lookup"><span data-stu-id="6c120-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="6c120-244">Aşağıdaki sonuçlar ortaya çıkabilir:</span><span class="sxs-lookup"><span data-stu-id="6c120-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="6c120-245">A **No güncelleştirmeleri bulunan** ileti, en son sürüm zaten yüklenmişse görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6c120-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="6c120-246">En son kararlı sürüm zaten yüklü değilse görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6c120-246">The latest stable version is displayed if not already installed.</span></span>

  ![Güncelleştirmeler bağlam menüsü seçeneğini denetle](_static/update-menu-option.png)

* <span data-ttu-id="6c120-248">Yüklenen sürümden daha yeni bir ön sürüm varsa, ön sürüm görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6c120-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="6c120-249">Eski bir kitaplık sürümüne düşürmek için *libman.json* dosyasını el ile edin.</span><span class="sxs-lookup"><span data-stu-id="6c120-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="6c120-250">Dosya kaydedildiğinde, LibMan [geri yükleme işlemi:](#restore-library-files)</span><span class="sxs-lookup"><span data-stu-id="6c120-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="6c120-251">Önceki sürümden gereksiz dosyaları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="6c120-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="6c120-252">Yeni sürümden yeni ve güncelleştirilmiş dosyalar ekler.</span><span class="sxs-lookup"><span data-stu-id="6c120-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6c120-253">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6c120-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="6c120-254">LibMan GitHub deposu</span><span class="sxs-lookup"><span data-stu-id="6c120-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
