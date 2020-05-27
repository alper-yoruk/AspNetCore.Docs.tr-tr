---
Başlık: ' ASP.NET Core Blazor düzenleri ' Yazar: Açıklama: ' uygulamalar için yeniden kullanılabilir düzen bileşenleri oluşturmayı öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="aspnet-core-blazor-layouts"></a>ASP.NET Core Blazor düzenleri

Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Luke Latham](https://github.com/guardrex)

Menüler, telif hakkı iletileri ve şirket logoları gibi bazı uygulama öğeleri genellikle uygulamanın genel düzeninin parçasıdır ve uygulamadaki her bileşen tarafından kullanılır. Bu öğelerin kodunu bir uygulamanın tüm bileşenlerine kopyalamak etkili bir yaklaşım değildir. Öğelerden biri bir güncelleştirme gerektirdiğinde her bileşenin güncelleştirilmesi gerekir. Bu tür çoğaltmaya devam etmek zordur ve zaman içinde tutarsız içeriğe yol açabilir. *Düzenler* bu sorunu çözüyor.

Teknik olarak, düzen yalnızca başka bir bileşendir. Düzen bir Razor şablonda veya C# kodunda tanımlanır ve [veri bağlama](xref:blazor/data-binding), [bağımlılık ekleme](xref:blazor/dependency-injection)ve diğer bileşen senaryolarını kullanabilir.

Bir *bileşeni* bir *düzene*dönüştürmek için bileşen:

* <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> Düzen içindeki işlenmiş içerik için bir özellik tanımlayan öğesinden devralır.
* Razor `@Body` İçeriğin işlendiği yerleşim biçimlendirmesinde konumu belirtmek için söz dizimini kullanır.

Aşağıdaki kod örneğinde Razor , *mainlayout. Razor*olan bir düzen bileşeninin şablonu gösterilmektedir. Düzen, <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> `@Body` Gezinti çubuğu ve alt bilgi arasında devralır ve Ayarlar:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

Uygulama şablonlarından birini temel alan bir uygulamada Blazor , `MainLayout` bileşen (*mainlayout. Razor*) uygulamanın *paylaşılan* klasöründedir.

## <a name="default-layout"></a>Varsayılan düzen

<xref:Microsoft.AspNetCore.Components.Routing.Router>Uygulamanın App *. Razor* dosyasındaki bileşende varsayılan uygulama yerleşimini belirtin. <xref:Microsoft.AspNetCore.Components.Routing.Router>Varsayılan Şablonlar tarafından belirtilen aşağıdaki bileşen, Blazor bileşene varsayılan düzeni ayarlar `MainLayout` :

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

İçerik için varsayılan bir düzen sağlamak üzere <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> bir içerik belirtin <xref:Microsoft.AspNetCore.Components.LayoutView> <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> :

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Bileşen hakkında daha fazla bilgi için <xref:Microsoft.AspNetCore.Components.Routing.Router> bkz <xref:blazor/routing> ..

Düzen bir varsayılan düzen olarak belirtildiğinde, bileşen başına veya klasör temelinde geçersiz kılınabileceğinden, yararlı bir uygulamadır. En genel teknik olduğundan, uygulamanın varsayılan yerleşimini ayarlamak için yönlendiriciyi kullanmayı tercih edin.

## <a name="specify-a-layout-in-a-component"></a>Bir bileşende düzen belirtme

Razor `@layout` Bir bileşene düzen uygulamak için yönergesini kullanın. Derleyici, `@layout` <xref:Microsoft.AspNetCore.Components.LayoutAttribute> bileşen sınıfına uygulanan öğesine dönüştürür.

Aşağıdaki `MasterList` bileşenin içeriği konumuna öğesine eklenir `MasterLayout` `@Body` :

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Düzen doğrudan bir bileşen içinde belirtildiğinde, yönlendiricide ayarlanan *varsayılan bir düzen* veya `@layout` *_Imports. Razor*'den içeri aktarılan bir yönerge geçersiz kılınır.

## <a name="centralized-layout-selection"></a>Merkezi düzen seçimi

Bir uygulamanın her klasörü, isteğe bağlı olarak *_Imports. Razor*adlı bir şablon dosyası içerebilir. Derleyici, içeri aktarmalar dosyasında belirtilen yönergeleri Razor aynı klasörde ve özyinelemeli olarak tüm alt klasörlerinde bulunan tüm şablonlarda içerir. Bu nedenle, içeren bir *_Imports. Razor* dosyası, `@layout MyCoolLayout` bir klasördeki tüm bileşenlerin kullanımını sağlar `MyCoolLayout` . `@layout MyCoolLayout`Klasör ve alt klasörlerdeki tüm *. Razor* dosyalarına tekrar tekrar ekleme gereksinimi yoktur. `@using`yönergeler aynı zamanda bileşenlere aynı şekilde uygulanır.

Aşağıdaki *_Imports. Razor* dosyası içeri aktarmaları:

* `MyCoolLayout`.
* RazorAynı klasörde ve tüm alt klasörlerde bulunan tüm bileşenler.
* `BlazorApp1.Data`Ad alanı.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

*_Imports. Razor* dosyası, [ Razor görünümler ve sayfalar için _ViewImports. cshtml dosyasına](xref:mvc/views/layout#importing-shared-directives) benzerdir, ancak özellikle Razor bileşen dosyalarına uygulanır.

_Imports bir düzen belirtme *. Razor* , yönlendiricinin *varsayılan düzeni*olarak belirtilen bir düzeni geçersiz kılar.

## <a name="nested-layouts"></a>İç içe düzenleri

Uygulamalar iç içe düzenleri içerebilir. Bir bileşen, daha sonra başka bir düzene başvuruda bulunan bir düzene başvurabilir. Örneğin, iç içe düzenleri çok düzeyli bir menü yapısı oluşturmak için kullanılır.

Aşağıdaki örnek, iç içe düzenleri nasıl kullanacağınızı gösterir. *Epısodescomponent. Razor* dosyası görüntülenecek bileşendir. Bileşen öğesine başvurur `MasterListLayout` :

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

*Masterlistlayout. Razor* dosyası sağlar `MasterListLayout` . Düzen, nerede işlendiği başka bir düzene başvurur `MasterLayout` . `EpisodesComponent`, görüntülendiği yerde işlenir `@Body` :

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Son olarak `MasterLayout` , *masterlayout. Razor* içinde üstbilgi, ana menü ve altbilgi gibi en üst düzey düzen öğelerini içerir. `MasterListLayout`ile birlikte `EpisodesComponent` işlendiğinde, şu şekilde `@Body` görünür:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>RazorTümleşik bileşenlerle bir sayfa düzeni paylaşma

Yönlendirilebilir bileşenler bir sayfalar uygulamasıyla tümleştirildiğinde Razor , uygulamanın paylaşılan düzeni bileşenlerle birlikte kullanılabilir. Daha fazla bilgi için bkz. <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/layout>
