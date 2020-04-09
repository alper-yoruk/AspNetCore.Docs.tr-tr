# <a name="contribute-to-the-aspnet-core-documentation"></a>ASP.NET Çekirdek belgelerine katkıda bulunun

Bu belge, [ASP.NET dokümantasyon sitesinde](https://docs.microsoft.com/aspnet/)barındırılan makalelere ve kod örneklerine katkıda bulunmak için yapılan süreci kapsar. Yazım hatası düzeltmeleri ve yeni makaleler katkıları memnuniyetle karşılanmaktadır.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Basit bir düzeltme veya öneri nasıl yapılır?

Makaleler, Markdown dosyaları olarak depoda depolanır. Bir Markdown dosyasının içeriğinde basit değişiklikler tarayıcı penceresinin sağ üst köşesindeki **Düzenleme** bağlantısını seçerek tarayıcıda yapılır. (Dar bir tarayıcı penceresinde, **Edit** bağlantısını görmek için **seçenekler** çubuğunu genişletin.) Çekme isteği (PR) oluşturmak için yönergeleri izleyin. Pr'ı gözden geçirip kabul edeceğiz ya da değişiklik önereceğiz.

## <a name="how-to-make-a-more-complex-submission"></a>Nasıl daha karmaşık bir gönderme yapmak

[Git ve GitHub.com](https://guides.github.com/activities/hello-world/)temel bir anlayış gerekir.

* Varolan bir makaleyi değiştirme veya yeni bir makale oluşturma gibi ne yapmak istediğinizi açıklayan bir [sorun](https://github.com/dotnet/AspNetCore.Docs/issues/new) açın. Sık sık yeni bir konu önerisi için bir anahat talep ediyoruz. Çok zaman yatırım yapmadan önce ekibin onayını bekleyin.
* [Aspnet/Dokümanlar](https://github.com/dotnet/AspNetCore.Docs/) repo'yu çatalve değişiklikleriniz için bir dal oluşturun.
* Değişikliklerinizde ustalaşmak için bir PR gönderin.
* PR'ınızda 'cla-required' etiketi [atanmışsa, Katkı Lisansı Sözleşmesini (CLA) tamamlayın.](https://cla.dotnetfoundation.org/)
* PR geri bildirimlerine yanıt verin.

Bu işlemin yeni bir makalenin yayınlanmasına yol açtığı bir örnek için, .NET Dokümanlar deposunda [ &num;Sayı 67](https://github.com/dotnet/docs/issues/67) ve [Çekme İsteği &num;798'e](https://github.com/dotnet/docs/pull/798) bakın. Yeni makale [kodunuzu belgeleme.](https://docs.microsoft.com/dotnet/articles/csharp/codedoc)

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a>Visual Studio Code'da Dokümanlar Yazma Paketi uzantısı

ASP.NET belgelerine katkıda bulunmak için Visual Studio Code kullanıyorsanız, [Dokümanlar Yazma Paketi](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) uzantısını yükleyerek üretkenliğinizi artırabilirsiniz. Uzantı, Markdown linting, kod yazım denetimi ve makale şablonları ile yardımcı olan çeşitli araçlar sağlar.

## <a name="markdown-syntax"></a>İşaretle günözeti

Makaleler [DocFx aromalı Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html)yazılmıştır , [GitHub aromalı Markdown](https://guides.github.com/features/mastering-markdown/)bir superset (GFM) . ASP.NET belgelerinde yaygın olarak kullanılan Web-sonuç birimi özellikleri için DFM sözdizimi örnekleri için .NET Dokümanlar repo stil kılavuzunda [Meta veri ve İşaretleme Şablonu'na](https://github.com/dotnet/docs/blob/master/styleguide/template.md) bakın. 

## <a name="folder-structure-conventions"></a>Klasör yapısı kuralları

Her Markdown dosyası için resimler için bir klasör ve örnek kod için bir klasör bulunabilir. Makale [temelleri / configuration /index.md](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md)ise, görüntüler [temelleri /\_yapılandırma / dizin / statik](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static) ve örnek uygulama proje dosyaları [temelleri / yapılandırma / dizin / örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample)bulunmaktadır. *Temeller/yapılandırma/index.md* dosyasındaki bir resim aşağıdaki Markdown tarafından işlenir:

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

Tüm [resimleralternatif (alt) metin](https://wikipedia.org/wiki/Alt_attribute)olmalıdır. Alt metin belirtme konusunda tavsiye için [WebAIM: Alternatif Metin](https://webaim.org/techniques/alttext/)gibi çevrimiçi kaynaklara bakın.

Markdown dosya adları ve resim dosyası adları için küçük harf kullanın.

## <a name="internal-links"></a>Dahili bağlantılar

Dahili bağlantılar `uid` xref bağlantısı ile hedef makalenin kullanmalıdır (bağlantı metni bağlantılı içeriğin başlığına ayarlanır):

```md
<xref:uid_of_the_topic>
```

Makalenin başlığı bağlantı metni için uygun değilse (örneğin, cümledeki bir sözcük veya tümcecik bağlantı metnidir), xref bağlantısını belirtin ve aşağıdakileri içeren bağlantı metnini belirtin:

```md
[link text](xref:uid_of_the_topic)
```

Daha fazla bilgi için [DocFX Çapraz Başvuru'ya](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference)bakın.

## <a name="images-and-screenshots"></a>Görüntüler ve ekran görüntüleri

Makaleleri içeren resimler eklemeyin, şunlar dışında

* Temel onboarding (acemi) öğreticiler.
* Netlik için bir görüntü gerektiğinde.

Bu kısıtlamalar deponun boyutunu azaltır.

İsteğe bağlı bir adım olarak, belgelerde kullanılan görüntülerin ve ekran görüntülerinin sıkıştırılmış olduğundan emin olun, bu da dosya boyutu ve sayfa yükleme performansına yardımcı olur. Birkaç popüler araçlar TinyPNG [(TinyPNG web sitesi](https://tinypng.com/) veya [TinyPNG API](https://tinypng.com/developers)kullanarak) veya [Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) Visual Studio uzantısı içerir. 

## <a name="code-snippets"></a>Kod parçacıkları

Makaleler sık sık noktaları göstermek için kod parçacıkları içerir. DFM, kodu Markdown dosyasına kopyalamanızı veya ayrı bir kod dosyasına başvurmanızı sağlar. Koddaki hata olasılığını en aza indirmek için mümkün olduğunca ayrı kod dosyaları kullanmayı tercih ediyoruz. Kod dosyaları, örnek projeler için daha önce açıklanan klasör yapısı kullanılarak repo'da depolanır. 

Aşağıdaki örnekler, *configuration/index.md* dosyasında kullanılmak üzere [DFM kodu snippet sözdizimini](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) göstermektedir.

Tüm kod dosyasını parçacık olarak işlemek için:

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

Satır numaralarını kullanarak dosyanın bir bölümünü parçacık olarak işlemek için:

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

C# parçacıkları için [c# bölgesine](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region)başvurun. Mümkün olduğunda, kod dosyasındaki satır numaraları değişme eğiliminde olduğundan ve Markdown'daki satır numarası başvurularıyla eşitlenmemiş olduğundan, satır numaraları yerine bölgeleri kullanın. C# bölgeleri iç içe olabilir. Dış bölgeye başvuruyorsanız, iç `#region` `#endregion` ve yönergeler bir snippet'te işlenmez. 

"snippet_Example" adlı bir C# bölgesini işlemek için:

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

İşlenen bir snippet'te seçili satırları vurgulamak için (genellikle sarı arka plan rengi olarak işlenir):

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a>DocFX ile test değişiklikleri

Değişikliklerinizi, sitenin yerel olarak barındırılan bir sürümünü oluşturan [DocFX komut satırı aracıyla](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool)test edin. DocFX, docs.microsoft.com için oluşturulan stil ve site uzantılarını oluşturmaz.

DocFX şunları gerektirir:

* .NET Windows Çerçevesi.
* Linux veya macOS için Mono. 

### <a name="windows-instructions"></a>Windows yönergeleri

* DocFX bültenlerinden *docfx.zip* indirin ve [indirin.](https://github.com/dotnet/docfx/releases)
* PATH'inize DocFX ekleyin.
* Komut kabuğunda, *docfx.json* dosyasını içeren klasöre (ASP.NET içeriği için*aspnet* veya ASP.NET Çekirdek içeriği için *aspnetcore)* gidin ve aşağıdaki komutu çalıştırın:

  ```console
  docfx --serve
  ```

* Tarayıcıda, `http://localhost:8080/group1-dest/`''

### <a name="mono-instructions"></a>Mono talimatlar

* Homebrew üzerinden Mono yükleyin:

  ```console
  brew install mono
  ```

* [DocFX'in en son sürümünü indirin.](https://github.com/dotnet/docfx/releases)
* *Arşivi $HOME/bin/docfx'e*ayıklayın.
* Bash kabuğunda **docfx** için bir çift takma ad oluşturun. İlk diğer ad, belgeleri oluşturmak için kullanılır. İkinci diğer ad, belgeleri oluşturmak ve hizmet etmek için kullanılır.

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* Komut kabuğunda, *docfx.json* dosyasını içeren klasöre gidin (ASP.NET içeriği için*aspnet* veya ASP.NET Core içeriği için *aspnetcore)* ve diğer adıyla dokümanları oluşturmak ve hizmet etmek için aşağıdaki komutu çalıştırın:

  ```console
  docfx-serve
  ```

* Tarayıcıda, `http://localhost:8080/group1-dest/`''

## <a name="voice-and-tone"></a>Ses ve ses tonu

Amacımız mümkün olan en geniş kitleye kolayca anlaşılabilir belgeler yazmaktır. Bu amaçla, katkıda bulunanlardan uymalarını istediğimiz stil yazma yönergeleri oluşturduk. Daha fazla bilgi için .NET repo'daki [Ses ve ton yönergelerine](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) bakın.

## <a name="microsoft-writing-style-guide"></a>Microsoft Yazma Stili Kılavuzu

[Microsoft Yazma Stili Kılavuzu,](https://docs.microsoft.com/style-guide/welcome/) ASP.NET Çekirdek belgeleri de dahil olmak üzere tüm teknoloji iletişim biçimleri için yazma stili ve terminoloji kılavuzu sağlar.

## <a name="redirects"></a>Yönlendirir

Bir makaleyi silerseniz, dosya adını değiştirirseniz veya farklı bir klasöre taşırsanız, makaleyi işaretleyen kişilerin *404 Bulunamadı* hatası almaması için bir yönlendirme oluşturun. [Ana yönlendirme dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json)yönlendirme ler ekleyin.
