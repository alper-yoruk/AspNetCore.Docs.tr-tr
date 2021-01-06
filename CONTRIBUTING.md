# <a name="contribute-to-the-aspnet-core-documentation"></a>ASP.NET Core belgelerine katkıda bulunun

Bu belge, [ASP.net belge sitesinde](https://docs.microsoft.com/aspnet/)barındırılan makalelere ve kod örneklerine katkıda bulunma sürecini ele alır. Typo düzeltmeleri ve yeni makaleler hoş geldiniz katkılardır.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Basit bir düzeltme veya öneri oluşturma

Makaleler depoda markas dosyaları olarak depolanır. Bir Markaşağı dosyasının içeriğinde basit değişiklikler tarayıcı penceresinin sağ üst köşesindeki **Düzenle** bağlantısını seçerek tarayıcıda yapılır. (Dar tarayıcı penceresinde, **düzenleme** bağlantısını görmek için **Seçenekler** çubuğunu genişletin.) Çekme isteği (PR) oluşturmak için yönergeleri izleyin. Çekme isteğini gözden geçiyoruz ve kabul edecek veya değişiklik önereceğiz.

## <a name="how-to-make-a-more-complex-submission"></a>Daha karmaşık bir gönderim yapma

[Git ve GitHub.com](https://guides.github.com/activities/hello-world/)'in temel bir şekilde anlaşılmasına ihtiyacınız vardır.

* Mevcut bir makaleyi değiştirme veya yeni bir makale oluşturma gibi, ne yapmak istediğinizi açıklayan bir [sorun](https://github.com/dotnet/AspNetCore.Docs/issues/new) açın. Genellikle yeni bir konu önerisi için bir ana hat istedik. Çok zaman yatırmadan önce takımdan onay beklemeniz gerekir.
* [ASPNET/docs](https://github.com/dotnet/AspNetCore.Docs/) deposunun çatalını yapın ve değişiklikleriniz için bir dal oluşturun.
* Değişikliklerinizle birlikte Master 'a bir PR gönderebilirsiniz.
* PR 'niz ' ClA-Required ' etiketini içeriyorsa, [katkı lisans sözleşmesi 'ni (CLA) doldurun](https://cla.dotnetfoundation.org/).
* PR geri bildirimlerine yanıt verin.

Bu işlemin yeni bir makalenin yayınlanmasını gerektiren bir örnek için, .NET docs Repository ['de &num; 67 sorun](https://github.com/dotnet/docs/issues/67) ve [çekme isteği &num; 798](https://github.com/dotnet/docs/pull/798) bölümüne bakın. Yeni makale, [kodunuzun belgelenmesi](https://docs.microsoft.com/dotnet/articles/csharp/codedoc).

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a>Visual Studio Code 'de docs yazma paketi uzantısı

ASP.NET belgelerine katkıda bulunmak için Visual Studio Code kullanıyorsanız, [docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) uzantısını yükleyerek üretkenliğinizi artırın. Uzantı, markı 'ye, kod yazım denetimine ve makale şablonlarına yardımcı olan çeşitli araçlar sunar.

## <a name="markdown-syntax"></a>Markın sözdizimi

Makaleler, [GitHub-flavored markaşağı (GFM)](https://guides.github.com/features/mastering-markdown/)üst kümesi olan [docfx-flavored markaşağı](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html)' de yazılır. ASP.NET belgelerinde yaygın olarak kullanılan UI özelliklerine ilişkin DFM sözdizimi örnekleri için, .NET docs deposu Stil Kılavuzu 'nda [meta veriler ve Markaşağı şablonu](https://github.com/dotnet/docs/blob/master/styleguide/template.md) bölümüne bakın. 

## <a name="folder-structure-conventions"></a>Klasör yapısı kuralları

Her markı dosyası için, bir resim klasörü ve örnek kod için bir klasör bulunabilir. Makale [temel bilgiler/yapılandırma/dizin. MD](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md)ise, görüntüler [temel bilgiler/yapılandırma/dizin/ \_ statik](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static) , örnek uygulama proje dosyaları ise [temel bilgiler/yapılandırma/dizin/örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample)bölümünde bulunur. *Temel bilgiler/yapılandırma/dizin. MD* dosyasındaki bir görüntü aşağıdaki markaşağı tarafından işlenir:

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

Tüm görüntülerin [Alternatif (alt) metni](https://wikipedia.org/wiki/Alt_attribute)olmalıdır. Alternatif metin belirtme hakkında öneri için [WebAIM: alternatif metin](https://webaim.org/techniques/alttext/)gibi çevrimiçi kaynaklar bölümüne bakın.

Markaşağı dosya adları ve resim dosya adları için küçük harf kullanın.

## <a name="internal-links"></a>İç bağlantılar

İç bağlantılar, `uid` bir XREF bağlantısı ile hedef makaleyi kullanmalıdır (bağlantı metni bağlantılı içeriğin başlığına ayarlanır):

```md
<xref:uid_of_the_topic>
```

Makalenin başlığı bağlantı metni için uygun değilse (örneğin, tümce içindeki bir sözcük veya tümcecik bağlantı metni ise), XREF bağlantısını ve bağlantı metnini aşağıdaki gibi belirtin:

```md
[link text](xref:uid_of_the_topic)
```

Daha fazla bilgi için bkz. [Docfx çapraz başvurusu](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).

## <a name="images-and-screenshots"></a>Görüntüler ve ekran görüntüleri

Şu durumlar dışında makalelerle görüntü eklemeyin:

* Temel ekleme (acemi) öğreticilerinde.
* Açıklık için bir görüntü gerektiğinde.

Bu kısıtlamalar deponun boyutunu azaltır.

İsteğe bağlı bir adım olarak, belgelerde kullanılan tüm görüntülerin ve ekran görüntülerinin sıkıştırıldığından emin olun, bu da dosya boyutu ve sayfa yükleme performansına yardımcı olur. Bazı popüler araçlar, TinyPNG ( [ınypng Web sitesini](https://tinypng.com/) veya [TINYPNG API](https://tinypng.com/developers)'Sini kullanarak) veya [görüntü iyileştirici](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) Visual Studio uzantısı 'nı içerir. 

## <a name="code-snippets"></a>Kod parçacıkları

Makaleler genellikle noktaları göstermek için kod parçacıkları içerir. DFM, kodu Markaşağı dosyasına kopyalamanızı veya ayrı bir kod dosyasına başvureklemenizi sağlar. Koddaki hata olasılığını en aza indirmek için mümkün olduğunda ayrı kod dosyaları kullanmayı tercih ediyoruz. Kod dosyaları, örnek projelerde daha önce açıklanan klasör yapısı kullanılarak depodadır. 

Aşağıdaki örneklerde, bir *yapılandırma/index. MD* dosyasında kullanılmak üzere [DFM kod parçacığı sözdizimi](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) gösterilmektedir.

Kod dosyasının tamamını kod parçacığı olarak işlemek için:

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

Satır numaralarını kullanarak bir dosyanın bir bölümünü kod parçacığı olarak işlemek için:

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

C# parçacıkları için bir [C# bölgesine](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region)başvurun. Mümkün olduğunda satır numaraları yerine bölgeleri kullanın, çünkü bir kod dosyasındaki satır numaraları değişir ve Marku 'daki satır numarası başvurularıyla eşitlenmemiş hale gelir. C# bölgeleri iç içe olabilir. Dış bölgeye başvurulması halinde, iç `#region` ve `#endregion` yönergeler bir kod parçacığında işlenmez. 

"Snippet_Example" adlı bir C# bölgesini işlemek için:

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

Oluşturulmuş bir kod parçacığında seçili satırları vurgulamak için (genellikle sarı arka plan rengi olarak işlenir):

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a>DocFX ile değişiklikleri test etme

Sitenizin yerel olarak barındırılan bir sürümünü oluşturan [docfx komut satırı aracıyla](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool)yaptığınız değişiklikleri test edin. DocFX, docs.microsoft.com için oluşturulan stili ve site uzantılarını işlemez.

DocFX şunları gerektirir:

* Windows üzerinde .NET Framework.
* Linux veya macOS için mono. 

### <a name="windows-instructions"></a>Windows yönergeleri

* [Docfx yayımlarından](https://github.com/dotnet/docfx/releases) *docfx.zip* indirip sıkıştırmasını açın.
* YOLUNUZA DocFX ekleyin.
* Bir komut kabuğunda, dosyadaki *docfx.js* içeren klasöre gidin (ASP.net içeriği için *ASPNET* veya ASP.NET Core içerik için *aspnetcore* ) ve şu komutu çalıştırın:

  ```console
  docfx --serve
  ```

* Bir tarayıcıda öğesine gidin `http://localhost:8080/group1-dest/` .

### <a name="mono-instructions"></a>Mono yönergeleri

* Homebrew aracılığıyla mono 'yı yükler:

  ```console
  brew install mono
  ```

* [DocFX 'in en son sürümünü](https://github.com/dotnet/docfx/releases)indirin.
* Arşivi *$Home/bin/docfx* için ayıklayın.
* Bash kabuğu 'nda **docfx** için bir diğer ad çifti oluşturun. İlk diğer ad, belgeleri oluşturmak için kullanılır. İkinci diğer ad, belgeleri derlemek ve çalıştırmak için kullanılır.

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* Bir komut kabuğunda, dosya *docfx.js* içeren klasöre gidin (ASP.net içeriği için *ASPNET* veya ASP.NET Core içerik için *aspnetcore* ) ve belgeleri diğer adıyla oluşturup sağlamak için aşağıdaki komutu çalıştırın:

  ```console
  docfx-serve
  ```

* Bir tarayıcıda öğesine gidin `http://localhost:8080/group1-dest/` .

## <a name="voice-and-tone"></a>Ses ve ton

Amacınız, mümkün olan en geniş kitle tarafından kolayca anlaşılabilir olan belgeleri yazmaktır. Bu uçta, katkıda bulunduğumuz katkılarımızı izlediğimiz stili yazmak için yönergeler geliştirdik. Daha fazla bilgi için bkz. .NET depodaki [ses ve ton kılavuzları](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) .

## <a name="microsoft-writing-style-guide"></a>Microsoft Yazma Stili Kılavuzu

[Microsoft yazma Stil Kılavuzu](https://docs.microsoft.com/style-guide/welcome/) , ASP.NET Core belgeleri de dahil olmak üzere tüm teknoloji iletişim biçimleri için stil ve terminoloji kılavuzu sağlar.

## <a name="redirects"></a>Melere

Bir makaleyi siler, dosya adını değiştirin veya farklı bir klasöre taşırsanız, makaleyi yer alan kişilerin *404* bulunmayan bir hata almamasını sağlamak için bir yeniden yönlendirme oluşturun. [Ana yeniden yönlendirme dosyasına yeniden](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json)yönlendirmeler ekleyin.
