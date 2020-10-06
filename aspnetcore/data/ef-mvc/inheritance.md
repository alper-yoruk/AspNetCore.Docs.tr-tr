---
title: 'Öğretici: EF Core devralma-ASP.NET MVC uygulama'
description: Bu öğretici, bir ASP.NET Core uygulamasındaki Entity Framework Core kullanarak veri modelinde devralmayı nasıl uygulayacağınızı gösterir.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/inheritance
ms.openlocfilehash: fabb0ed9c0f64a529b516d521d60a7d862e2654a
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754573"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a>Öğretici: EF Core devralma-ASP.NET MVC uygulama

Önceki öğreticide eşzamanlılık özel durumlarını ele alırsınız. Bu öğretici, veri modelinde devralmayı nasıl uygulayacağınızı gösterir.

Nesne odaklı programlamada, kod yeniden kullanımını kolaylaştırmak için devralmayı kullanabilirsiniz. Bu öğreticide, `Instructor` ve `Student` sınıflarını, `Person` `LastName` hem Eğitmenler hem de öğrenciler için ortak olan gibi özellikleri içeren bir temel sınıftan türetireceğiz şekilde değiştireceksiniz. Herhangi bir Web sayfası eklemez veya değiştirmezsiniz, ancak koddan bazılarını değiştireceksiniz ve bu değişiklikler otomatik olarak veritabanına yansıtılacaktır.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Devralmayı veritabanına eşle
> * Kişi sınıfını oluşturma
> * Eğitmeni ve öğrenci 'yi güncelleştirme
> * Modele kişi ekleme
> * Geçişleri oluşturma ve güncelleştirme
> * Uygulamayı test etme

## <a name="prerequisites"></a>Önkoşullar

* [Eşzamanlılık işle](concurrency.md)

## <a name="map-inheritance-to-database"></a>Devralmayı veritabanına eşle

`Instructor` `Student` Okul veri modelindeki ve sınıflarının özdeş birçok özelliği vardır:

![Öğrenci ve eğitmen sınıfları](inheritance/_static/no-inheritance.png)

Ve varlıkları tarafından paylaşılan özellikler için gereksiz kodu ortadan kaldırmak istediğinizi varsayalım `Instructor` `Student` . Ya da adın bir eğitmenden veya bir öğrenciye ait olup olmadığına bakılmaksızın adları biçimlendirmeden bir hizmet yazmak isteyebilirsiniz. `Person`Yalnızca bu paylaşılan özellikleri içeren bir temel sınıf oluşturabilir `Instructor` ve sonra `Student` Aşağıdaki çizimde gösterildiği gibi, ve sınıflarının bu temel sınıftan devralmasını sağlayabilirsiniz:

![Kişi sınıfından türetilen öğrenci ve eğitmen sınıfları](inheritance/_static/inheritance.png)

Bu devralma yapısının veritabanında temsil edilebilmesi için birkaç yol vardır. Tek bir tabloda hem öğrenciler hem de eğitmenler hakkında bilgi içeren bir kişi tablonuz olabilir. Bazı sütunlar yalnızca Eğitmenler (HireDate), bazıları yalnızca öğrencilerle (kayıttarihi), bazıları ise (soyadı, adı) için geçerlidir. Genellikle, her bir satırın temsil ettiği türü belirtmek için bir Ayrıştırıcı sütunu vardır. Örneğin, ayrıştırıcı sütununda, Eğitmenler için "eğitmen" ve öğrenciler için "öğrenci" bulunabilir.

![Hiyerarşi başına tablo örneği](inheritance/_static/tph.png)

Tek bir veritabanı tablosundan bir varlık devralma yapısı oluşturmanın bu düzeni, hiyerarşi başına tablo (TPH) devralma olarak adlandırılır.

Alternatif olarak, veritabanının devralma yapısına benzer bir şekilde görünmesini sağlayabilirsiniz. Örneğin, kişi tablosunda yalnızca ad alanları olabilir ve Tarih alanlarıyla ayrı eğitmen ve öğrenci tabloları vardır.

> [!WARNING]
> Tür başına tablo (TPT) EF Core 3. x tarafından desteklenmez, ancak bu [EF Core 5,0](/ef/core/what-is-new/ef-core-5.0/plan)' de uygulanmıştır.

![Tablo türü devralma](inheritance/_static/tpt.png)

Her varlık sınıfı için bir veritabanı tablosu yapmanın bu düzeni, tür başına tablo (TPT) devralma olarak adlandırılır.

Başka bir seçenek de Özet olmayan tüm türleri tek tek tablolarla eşlemenize olanak sağlar. Devralınan özellikler de dahil olmak üzere bir sınıfın tüm özellikleri karşılık gelen tablonun sütunlarına eşlenir. Bu düzene, tablo başına somut sınıf (TPC) devralma adı verilir. Daha önce gösterildiği gibi, kişi, öğrenci ve eğitmen sınıfları için TPC devralmayı uyguladıysanız, öğrenci ve eğitmen tabloları, devralındıktan sonra, devralma uygulandıktan sonra farklı şekilde görünür.

TPC ve TPH devralma desenleri genellikle TPT devralma desenlerinden daha iyi performans sağlar, çünkü TPT desenleri karmaşık JOIN sorgularına yol açabilir.

Bu öğreticide, TPH devralmanın nasıl uygulanacağı gösterilmektedir. TPH Entity Framework Core desteklediği tek devralma modelidir.  Ne yapacaklarınız bir sınıf oluşturur `Person` , ' `Instructor` `Student` den türetmek için ve sınıflarını değiştirin `Person` , yeni sınıfını öğesine ekleyin `DbContext` ve bir geçiş oluşturun.

> [!TIP]
> Aşağıdaki değişiklikleri yapmadan önce projenin bir kopyasını kaydetmeyi göz önünde bulundurun.  Daha sonra sorunlarla karşılaşırsanız ve baştan başlamak gerekirse, bu öğretici için yapılan adımları tersine çevirme veya tüm serinin başlangıcına geri dönme yerine kaydedilen projeden başlamak daha kolay olacaktır.

## <a name="create-the-person-class"></a>Kişi sınıfını oluşturma

Modeller klasöründe Person.cs oluşturun ve şablon kodunu şu kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a>Eğitmeni ve öğrenci 'yi güncelleştirme

*Instructor.cs*' de, kişi sınıfından eğitmen sınıfını türetirsiniz ve anahtar ve ad alanlarını kaldırın. Kod aşağıdaki örneğe benzer şekilde görünür:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

*Student.cs*' de aynı değişiklikleri yapın.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a>Modele kişi ekleme

Kişi varlık türünü *SchoolContext.cs*öğesine ekleyin. Yeni satırlar vurgulanır.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

Bu, Entity Framework hiyerarşinin devralma devralınmasını yapılandırmak için gereklidir. Gördüğünüz gibi, veritabanı güncelleştirildiği sırada öğrenci ve eğitmen tablolarının yerine bir kişi tablosu olacaktır.

## <a name="create-and-update-migrations"></a>Geçişleri oluşturma ve güncelleştirme

Değişikliklerinizi kaydedin ve projeyi derleyin. Ardından proje klasöründe komut penceresini açın ve aşağıdaki komutu girin:

```dotnetcli
dotnet ef migrations add Inheritance
```

`database update`Komutu henüz çalıştırmayın. Bu komut, eğitmen tablosunu bırakacak ve öğrenci tablosunu kişi olarak yeniden adlandırdığı için kayıp veri oluşmasına neden olur. Varolan verileri korumak için özel kod sağlamanız gerekir.

*Geçişler/ \<timestamp> _Inheritance. cs* ' i açın ve `Up` yöntemi aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

Bu kod aşağıdaki veritabanı güncelleştirme görevlerini gerçekleştirir:

* Yabancı anahtar kısıtlamalarını ve öğrenci tablosuna işaret eden dizinleri kaldırır.

* Eğitmen tablosunu kişi olarak yeniden adlandırır ve öğrenci verilerini depolamak için gerekli değişiklikleri yapar:

* Öğrenciler için Nullable kayıt tarihi ekler.

* Bir satırın bir öğrenci mi yoksa bir eğitmen mi olduğunu göstermek için ayrıştırıcı sütunu ekler.

* Öğrenci satırlarında işe alma tarihleri olmadığından, HireDate null yapılabilir hale gelir.

* Öğrencilere işaret eden yabancı anahtarları güncelleştirmek için kullanılacak geçici bir alan ekler. Öğrencileri kişi tablosuna kopyaladığınızda, yeni birincil anahtar değerleri alırlar.

* Öğrenci tablosundaki verileri kişi tablosuna kopyalar. Bu, öğrencilerden yeni birincil anahtar değerleri atanmasını sağlar.

* Öğrencilere işaret eden yabancı anahtar değerlerini düzeltir.

* Yabancı anahtar kısıtlamalarını ve dizinleri yeniden oluşturur, şimdi bunları kişi tablosuna işaret eder.

(Birincil anahtar türü olarak tamsayı yerine GUID kullandıysanız, öğrenci birincil anahtar değerlerinin değiştirilmesi gerekmez ve bu adımların bazıları atlanamaz.)

Şu `database update` komutu çalıştırın:

```dotnetcli
dotnet ef database update
```

(Bir üretim sisteminde, `Down` önceki veritabanı sürümüne geri dönmek için bunu kullanmanız durumunda bu yöntemde ilgili değişiklikleri yapmanız gerekir. Bu öğreticide, yöntemini kullanmayacağız `Down` .)

> [!NOTE]
> Varolan verileri içeren bir veritabanında şema değişiklikleri yaparken başka hatalar almak mümkündür. Çözemiyoruz geçiş hataları alırsanız, bağlantı dizesindeki veritabanı adını değiştirebilir veya veritabanını silebilirsiniz. Yeni bir veritabanı ile geçirilecek veri yoktur ve Update-Database komutunun hatasız tamamlanabilmesi daha olasıdır. Veritabanını silmek için, SSOX kullanın veya `database drop` CLI komutunu çalıştırın.

## <a name="test-the-implementation"></a>Uygulamayı test etme

Uygulamayı çalıştırın ve çeşitli sayfaları deneyin. Her şey, daha önce olduğu gibi çalışmaktadır.

**SQL Server Nesne Gezgini**, **veri bağlantıları/SchoolContext** ve ardından **Tablolar**' ı genişletin ve öğrenci ve eğitmen tablolarının bir kişi tablosu ile değiştirildiğini görürsünüz. Kişi tablosu tasarımcısını açın ve öğrencinin ve eğitmen tablolarında kullanılan tüm sütunları olduğunu görürsünüz.

![SSOX 'teki kişi tablosu](inheritance/_static/ssox-person-table.png)

Kişi tablosuna sağ tıklayın ve ardından **tablo verilerini göster** ' e tıklayarak ayrıştırıcı sütununu görüntüleyin.

![SSOX tablo verilerinde kişi tablosu](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a>Kodu alma

[Tamamlanmış uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Ek kaynaklar

Entity Framework Core devralma hakkında daha fazla bilgi için bkz. [Devralma](/ef/core/modeling/inheritance).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Veritabanına devralma eşlenmiş
> * Kişi sınıfı oluşturuldu
> * Güncelleştirilmiş eğitmen ve öğrenci
> * Modele kişi eklendi
> * Geçişleri oluşturma ve güncelleştirme
> * Uygulama test edildi

Çeşitli görece gelişmiş Entity Framework senaryolarını nasıl işleyeceğinizi öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [İleri: gelişmiş konular](advanced.md)