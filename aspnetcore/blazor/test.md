---
title: ASP.NET Core 'de test bileşenleri Blazor
author: guardrex
description: Uygulamalarda bileşen oluşturmayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
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
uid: blazor/test
ms.openlocfilehash: cd4aee66fd6df6cc0ce520d8ca66e0a2cf130eff
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054872"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a>ASP.NET Core 'de test bileşenleri Blazor

[Yumurıl Hansen](https://egilhansen.com/)

Test, kararlı ve sürdürülebilir yazılım oluşturmanın önemli bir yönüdür.

Bir bileşeni test etmek için Blazor , test (kes) *altındaki bileşen* :

* Test için ilgili girişle birlikte işlenir.
* Gerçekleştirilen test türüne bağlı olarak etkileşime veya değişikliğe tabi olabilir. Örneğin, `onclick` bir düğme için olay gibi olay işleyicileri tetiklenebilir.
* Beklenen değerler için inceledi.

## <a name="test-approaches"></a>Test yaklaşımları

Bileşenleri test etmek için iki yaygın yaklaşım Blazor , uçtan uca (e2e) test ve birim sınamalardır:

* **Birim testi**: [birim testleri](/dotnet/core/testing/) şunları sağlayan bir birim testi kitaplığıyla yazılır:
  * Bileşen işleme.
  * Bileşen çıkışının ve durumunun incelemesi.
  * Olay işleyicileri ve yaşam döngüsü yöntemlerinin tetiklenmesi.
  * Bileşen davranışının doğru olduğu Onaylamalar.

  [bunbu](https://github.com/egil/bUnit) , bileşen birim testini sağlayan bir kitaplık örneğidir Razor .

* **E2e** testi: Test Çalıştırıcısı Blazor , BIR tarayıcı örneğini kesme ve otomatikleştirir olarak içeren bir uygulamayı çalıştırır. Test aracı tarayıcı aracılığıyla göz atma ve bunlarla etkileşim kurma. [Selenium](https://github.com/SeleniumHQ/selenium) , uygulamalarla KULLANıLABILECEK bir e2e test çerçevesinin örneğidir Blazor .

Birim testinde yalnızca Blazor bileşen ( Razor /c #) dahil edilir. Hizmetler ve JS birlikte çalışma gibi dış bağımlılıklar, moclanmış olmalıdır. E2E testinde, Blazor bileşeni ve tüm yardımcı altyapısı, CSS, js ve DOM ve tarayıcı API 'leri dahil olmak üzere testin bir parçasıdır.

*Test kapsamı* , testlerin ne kadar geniş olduğunu tanımlar. Test kapsamı genellikle testlerin hızını etkiler. Birim testleri, uygulama alt sistemlerinin bir alt kümesinde çalışır ve genellikle milisaniye cinsinden yürütülür. Uygulama alt sistemlerinin geniş bir grubunu test eden E2E testleri, tamamlanması birkaç saniye sürebilir. 

Birim testi Ayrıca, kes örneğine erişim sağlar ve bileşenin iç durumunun incelemesini ve doğrulanması için izin verir. Bu, normal olarak E2E testinde mümkün değildir.

Bileşenin ortamıyla ilgili olarak, E2E testlerin, doğrulama başlamadan önce beklenen çevresel duruma ulaşıldığından emin olması gerekir. Aksi takdirde, sonuç tahmin edilemez. Birim testinde, testin kesme ve yaşam döngüsünün işlenmesi daha tümleşiktir ve bu da test kararlılığını geliştirir.

E2E testi, genellikle zayıf test güvenilirliğini sağlayan birden çok işlem, ağ ve disk g/ç ve diğer alt sistem etkinliklerini başlatmayı içerir. Birim testleri, genellikle bu tür sorunlardan yalıtımlı.

Aşağıdaki tabloda iki test yaklaşımının farkı özetlenmektedir.

| Özellik                       | Birim testi                     | E2E testi                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| Test kapsamı                       | Blazor yalnızca bileşen ( Razor /c #) | BlazorRazorCSS/JS ile bileşen (/c #) |
| Test yürütme süresi              | Mayacak                     | Saniye                                 |
| Bileşen örneğine erişim | Yes                              | Hayır                                      |
| Ortamla duyarlı     | Hayır                               | Yes                                     |
| Güvenilirlik                      | Daha güvenilir                    | Daha az güvenilir                           |

## <a name="choose-the-most-appropriate-test-approach"></a>En uygun test yaklaşımını seçin

Gerçekleştirilecek test türünü seçerken senaryoyu göz önünde bulundurun. Aşağıdaki tabloda bazı konular açıklanmaktadır.

| Senaryo | Önerilen yaklaşım | Açıklamalar |
| -------- | ------------------ | ------- |
| JS birlikte çalışma mantığı olmayan bileşen | Birim testi | Bir bileşende JS birlikte çalışabilirliğine bağımlılık olmadığında Blazor , BILEŞEN js veya DOM API 'sine erişim olmadan test edilebilir. Bu senaryoda birim testi seçmenin olumsuz yönleri yoktur. |
| Basit JS birlikte çalışma mantığını içeren bileşen | Birim testi | Bileşenler, DOM 'yi sorgulamak veya JS birlikte çalışma yoluyla animasyonları tetiklemek için ortaktır. Birim testi genellikle bu senaryoda tercih edilir, çünkü arabirim aracılığıyla JS etkileşimini yalındır <xref:Microsoft.JSInterop.IJSRuntime> . |
| Karmaşık JS koduna bağlı bileşen | Birim testi ve ayrı JS testi | Bir bileşen büyük veya karmaşık bir JS kitaplığı çağırmak için JS birlikte çalışabilirliği kullanıyorsa ancak Blazor bileşen ve js kitaplığı arasındaki etkileşim basittir, en iyi yaklaşım, bileşen ve js kitaplığı veya kodu iki ayrı parça olarak değerlendirmek ve her birini test etmek olabilir. BlazorBir birim testi kitaplığıyla bileşeni test edin ve JS 'yi BIR js test kitaplığı ile test edin. |
| Bir mantığı olan ve tarayıcı DOM 'ın JS işlemesini bağımlı bileşen | E2E testi | Bir bileşenin işlevselliği JS 'ye bağımlıysa ve DOM üzerinde değişiklik yapıldığında, E2E testinde hem JS hem de Code 'u Blazor birlikte doğrulayın. Bu, Blazor çerçeve geliştiricilerinin, Blazor sıkı şekilde bağlanmış C# ve js kodu bulunan tarayıcı işleme mantığı ile aldığı yaklaşımdır. Bileşenleri bir tarayıcıda doğru şekilde işlemek için C# ve JS kodunun birlikte çalışması gerekir Blazor .
| Sabit-sahte bağımlılıklarla 3. taraf bileşen kitaplığına bağlı olan bileşen | E2E testi | Bir bileşenin işlevselliği, JS birlikte çalışma gibi, bir üçüncü taraf bileşen kitaplığına bağımlıysa, E2E testi, bileşeni test etmeye yönelik tek seçenek olabilir. |

## <a name="test-components-with-bunit"></a>BUnit ile bileşenleri test etme

İçin resmi Microsoft Test çerçevesi yoktur Blazor , ancak topluluk odaklı proje, birim test bileşenlerine [](https://github.com/egil/bUnit) kolay bir yol sağlar Blazor .

> [!NOTE]
> Bu, üçüncü taraf bir test Kitaplığı olduğundan Microsoft tarafından desteklenmez veya korunmaz.

Bunbu, [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/)ve [xUnit](https://xunit.github.io/)gibi genel amaçlı test çerçeveleri ile birlikte çalışmaktadır. Bu test çerçeveleri, Bunıt testlerini normal birim testleri gibi görünür hale getirir. Genel amaçlı bir test çerçevesiyle tümleştirilmiş, normalde şu şekilde yürütülen testler:

* [Visual Studio 'Nun test Gezgini](/visualstudio/test/run-unit-tests-with-test-explorer).
* [`dotnet test`](/dotnet/core/tools/dotnet-test) Bir komut kabuğunda CLı komutu.
* Otomatikleştirilmiş bir DevOps test işlem hattı.

> [!NOTE]
> Farklı test çerçeveleri genelinde test kavramları ve test uygulamaları benzerdir ancak aynı değildir. Rehberlik için test çerçevesinin belgelerine bakın.

Aşağıda, bir proje şablonunu temel alan bir uygulamadaki bileşen üzerindeki bir bUnit testinin yapısı gösterilmektedir `Counter` Blazor . `Counter`Bileşen, sayfadaki bir düğmeyi seçerek kullanıcıya göre bir sayacı görüntüler ve arttırır:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Aşağıdaki BIT testi, düğme seçildiğinde KESILEN sayacın doğru şekilde arttırıldığının doğrular:

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

Testin her adımında aşağıdaki eylemler gerçekleşir:

* *Düzenle*: `Counter` Bileşen, bunit ' i kullanılarak işlenir `TestContext` . KESILEN paragraf öğesi ( `<p>` ) bulunur ve öğesine atanır `paraElm` .

* *Sahne*: düğmenin öğesi ( `<button>` ) bulunur ve sonra öğesini çağırarak seçilir ve bu da `Click` sayacı artırmanız ve paragraf etiketinin () içeriğini güncelleştirmelidir `<p>` . Paragraf öğesi metin içeriği çağırarak elde edilir `TextContent` .

* Onay: `MarkupMatches` metin içeriğinde, bir olan beklenen dizeyle eşleştiğini doğrulamak için çağrılır `Current count: 1` .

> [!NOTE]
> `MarkupMatches`Onaylama yöntemi bir normal dize karşılaştırma onaylamadan farklıdır (örneğin, `Assert.Equal("Current count: 1", paraElmText);` ) `MarkupMatches` girişin anlam KARŞıLAŞTıRMASıNı ve beklenen HTML işaretlemesini gerçekleştirir. Anlamsal bir karşılaştırma, HTML semantiğinin farkındadır, yani çok önemli boşluk gibi şeyler yok sayılır. Bu, daha kararlı testlere neden olur. Daha fazla bilgi için bkz. [anlam HTML karşılaştırmasını özelleştirme](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).

## <a name="additional-resources"></a>Ek kaynaklar

* [Bunıt Ile çalışmaya](https://bunit.egilhansen.com/docs/getting-started/)başlama: bunıt yönergeleri test projesi oluşturma, test çerçevesi paketlerine başvurma ve Testleri oluşturma ve çalıştırma hakkında rehberlik içerir.
