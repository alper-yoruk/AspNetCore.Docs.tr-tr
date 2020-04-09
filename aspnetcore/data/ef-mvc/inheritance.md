---
title: 'Öğretici: Devralma uygulayın - EF Core ile MVC ASP.NET'
description: Bu öğretici, ASP.NET Core uygulamasında Entity Framework Core'u kullanarak veri modelinde devralmayı nasıl uygulayacağınızı gösterecektir.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/inheritance
ms.openlocfilehash: dab3d2b057162f6d986db10e74e3681acc0ada3b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657243"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a>Öğretici: Devralma uygulayın - EF Core ile MVC ASP.NET

Önceki öğreticide, eşzamanlılık özel durumlarını işlettiniz. Bu öğretici, veri modelinde devralmayı nasıl uygulayacağınızı gösterir.

Nesne yönelimli programlamada, kodun yeniden kullanımını kolaylaştırmak için devralmayı kullanabilirsiniz. Bu öğreticide, sınıfları `Instructor` ve `Student` sınıfları, hem eğitmenler `Person` hem de öğrenciler `LastName` için ortak olan özellikler içeren bir taban sınıftan türeecek şekilde değiştirirsiniz. Web sayfaları eklemez veya değiştirmezsiniz, ancak kodun bir kısmını değiştirirsiniz ve bu değişiklikler otomatik olarak veritabanına yansıtılır.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Veritabanına harita kalıbı
> * Kişi sınıfını oluşturma
> * Eğitmen ve Öğrenciyi Güncelleştir
> * Modele Kişi Ekle
> * Geçişler oluşturma ve güncelleştirme
> * Uygulamayı test edin

## <a name="prerequisites"></a>Ön koşullar

* [Eşzamanlılığı Tut](concurrency.md)

## <a name="map-inheritance-to-database"></a>Veritabanına harita kalıbı

Okul `Instructor` `Student` veri modelindeki ve sınıfların aynı olan birkaç özelliği vardır:

![Öğrenci ve Eğitmen sınıfları](inheritance/_static/no-inheritance.png)

Varlıklar `Instructor` ve `Student` varlıklar tarafından paylaşılan özellikler için gereksiz kodu ortadan kaldırmak istediğinizi varsayalım. Ya da adın bir eğitmenden mi yoksa bir öğrenciden mi geldiğini umursamadan isimleri biçimlendirebilen bir hizmet yazmak istiyorsunuz. Yalnızca paylaşılan `Person` özellikleri içeren bir taban sınıf oluşturabilir, `Instructor` `Student` ardından aşağıdaki resimde gösterildiği gibi, bu taban sınıftan ve sınıflardan devralabilir:

![Kişi sınıfından kaynaklanan öğrenci ve eğitmen sınıfları](inheritance/_static/inheritance.png)

Bu devralma yapısıveritabanında temsil edilebilir çeşitli yolları vardır. Tek bir tabloda hem öğrenciler hem de eğitmenler hakkında bilgi içeren bir Kişi tablosu na sahip olabilirsiniz. Sütunlardan bazıları yalnızca eğitmenler (HireDate), bazıları sadece öğrenciler (EnrollmentDate), bazıları her ikisi için de (Soyadı, İlkAd) için geçerli olabilir. Genellikle, her satırın hangi türü temsil ettiğini belirtmek için bir ayrımcı sütununuzun olur. Örneğin, ayrımcı sütunda eğitmenler için "Eğitmen" ve öğrenciler için "Öğrenci" olabilir.

![Tablo başına hiyerarşi örneği](inheritance/_static/tph.png)

Tek bir veritabanı tablosundan varlık kalıtım yapısı oluşturma bu desen tablo başına hiyerarşi (TPH) kalıtım denir.

Alternatif veritabanı devralma yapısı gibi görünmesini sağlamaktır. Örneğin, yalnızca Kişi tablosundaki ad alanlarına sahip olabilir ve tarih alanlarıyla birlikte ayrı Eğitmen ve Öğrenci tablolarınız olabilir.

![Tablo başına tür devralma](inheritance/_static/tpt.png)

Her varlık sınıfı için bir veritabanı tablosu yapma bu desen tür başına tablo (TPT) kalıtım denir.

Ancak başka bir seçenek tek tek tablolar için tüm soyut olmayan türleri haritalamaktır. Devralınan özellikler de dahil olmak üzere bir sınıfın tüm özellikleri, ilgili tablonun sütunlarına eşlenir. Bu desen, Beton Başına Tablo Sınıfı (TPC) kalıtım olarak adlandırılır. Daha önce gösterildiği gibi Kişi, Öğrenci ve Eğitmen sınıfları için TPC kalıtım ını uygularsanız, Öğrenci ve Eğitmen tabloları, kalıtım uygulandıktan sonra öncekinden farklı görünmeyecek.

TPT desenleri karmaşık birleştirme sorgularına neden olabileceğinden, TPC ve TPH kalıtım desenleri genellikle TPT kalıtım desenlerinden daha iyi performans sağlar.

Bu öğretici, TPH kalıtım nasıl uygulanacağını gösterir. TPH, Varlık Çerçeve Çekirdeği'nin desteklediği tek kalıtım desenidir.  Yapacağınız şey bir `Person` sınıf oluşturmak, `Instructor` sınıfları ve `Student` sınıfları değiştirmek, `Person`yeni `DbContext`sınıfı eklemek ve bir geçiş oluşturmaktır.

> [!TIP]
> Aşağıdaki değişiklikleri yapmadan önce projenin bir kopyasını kaydetmeyi düşünün.  Sonra sorunlarla karşınıza çıkmak ve baştan başlamak gerekiyorsa, bu öğretici için yapılan adımları tersine çevirmek veya tüm serinin başına geri dönmek yerine kaydedilen projeden başlamak daha kolay olacaktır.

## <a name="create-the-person-class"></a>Kişi sınıfını oluşturma

Modeller klasöründe, Person.cs oluşturun ve şablon kodunu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a>Eğitmen ve Öğrenciyi Güncelleştir

*Instructor.cs,* Eğitmen sınıfını Kişi sınıfından türetin ve anahtar ve ad alanlarını kaldırın. Kod aşağıdaki örnek gibi görünecektir:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

*Student.cs*aynı değişiklikleri yapın.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a>Modele Kişi Ekle

*SchoolContext.cs'a*Kişi varlık türünü ekleyin. Yeni satırlar vurgulanır.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

Tablo başına hiyerarşi kalıtımını yapılandırmak için Varlık Çerçevesi'nin ihtiyacı olan tek şey budur. Göreceğiniz gibi, veritabanı güncelleştirildiğinde, Öğrenci ve Eğitmen tablolarının yerine bir Kişi tablosu olacaktır.

## <a name="create-and-update-migrations"></a>Geçişler oluşturma ve güncelleştirme

Değişikliklerinizi kaydedin ve projeyi oluşturun. Ardından proje klasöründeki komut penceresini açın ve aşağıdaki komutu girin:

```dotnetcli
dotnet ef migrations add Inheritance
```

Henüz komutu `database update` çalıştırma. Bu komut, Instructor tablosunu düşüreceği ve Öğrenci tablosunu Kişiye yeniden adlandıracağı için verilerin kaybolmasına neden olur. Varolan verileri korumak için özel kod sağlamanız gerekir.

*Geçişleri/\<zaman damgasını>_Inheritance.cs* açın ve yöntemi aşağıdaki kodla değiştirin: `Up`

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

Bu kod, aşağıdaki veritabanı güncelleştirme görevleri ilgilenir:

* Öğrenci tablosuna işaret eden yabancı anahtar kısıtlamalarını ve dizinleri kaldırır.

* Eğitmen tablosunu Kişi olarak yeniden adlandırır ve Öğrenci verilerini depolaması için gerekli değişiklikleri yapar:

* Öğrenciler için geçersiz kayıt tarihi ekler.

* Bir satırın öğrenci veya eğitmen için olup olmadığını belirtmek için Ayrımcı sütunu ekler.

* Öğrenci satırları işe alma tarihleri olmayacağından HireDate'i geçersiz kılar.

* Öğrencilere işaret eden yabancı anahtarları güncelleştirmek için kullanılacak geçici bir alan ekler. Öğrencileri Kişi tablosuna kopyaladiğinizde yeni temel anahtar değerleri elde ederler.

* Öğrenci tablosundaki verileri Kişi tablosuna kopyalar. Bu, öğrencilerin yeni birincil anahtar değerleri atanmasına neden olur.

* Öğrencileri işaret eden yabancı anahtar değerlerini düzeltir.

* Yabancı anahtar kısıtlamalarını ve dizinlerini yeniden oluşturur ve şimdi bunları Kişi tablosuna işaret eder.

(Birincil anahtar türü olarak tamsayı yerine GUID kullansaydınız, öğrenci birincil anahtar değerlerinin değişmesi gerekmezdi ve bu adımlardan birkaçı atlanmış olabilir.)

Komutu `database update` çalıştırın:

```dotnetcli
dotnet ef database update
```

(Bir üretim sisteminde, önceki veritabanı `Down` sürümüne geri dönmek için bunu kullanmanız gerekme ihtimaline karşı yöntemde karşılık gelen değişiklikler yaparsınız. Bu öğretici için `Down` yöntemi kullanmayacaksınız.)

> [!NOTE]
> Varolan verilere sahip bir veritabanında şema değişiklikleri yaparken başka hatalar da almak mümkündür. Çözemediğiniz geçiş hataları alırsanız, bağlantı dizesinde veritabanı adını değiştirebilir veya veritabanını silebilirsiniz. Yeni bir veritabanında, geçirilen veri yoktur ve güncelleştirme veritabanı komutu hatasız olarak tamamlanma olasılığı daha yüksektir. Veritabanını silmek için SSOX'ı kullanın veya CLI komutunu çalıştırın. `database drop`

## <a name="test-the-implementation"></a>Uygulamayı test edin

Uygulamayı çalıştırın ve çeşitli sayfaları deneyin. Her şey eskisi gibi çalışıyor.

**SQL Server Object Explorer'da**Veri **Bağlantılarını/SchoolContext'ı** ve ardından **Tabloları**genişletin ve Öğrenci ve Eğitmen tablolarının Bir Kişi tablosuyla değiştirildiğini görürsünüz. Kişi tablosu tasarımcısını açın ve öğrenci ve eğitmen tablolarında kullanılan tüm sütunlara sahip olduğunu görün.

![SSOX'ta kişi tablosu](inheritance/_static/ssox-person-table.png)

Kişi tablosunu sağ tıklatın ve sonra ayırıcı sütunu görmek için **Tablo Verilerini Göster'i** tıklatın.

![SSOX'ta kişi tablosu - tablo verileri](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a>Kodu alma

[Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Ek kaynaklar

Entity Framework Core'da devralma hakkında daha fazla bilgi için [bkz.](/ef/core/modeling/inheritance)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Veritabanına eşlenen devralma
> * Kişi sınıfı oluşturuldu
> * Güncellenen Eğitmen ve Öğrenci
> * Modele Kişi Eklendi
> * Geçişleri oluşturma ve güncelleştirme
> * Uygulama test edildi

Göreceli olarak gelişmiş Varlık Çerçevesi senaryolarının çeşitli işlemek için öğrenmek için bir sonraki öğretici için ilerlemek.

> [!div class="nextstepaction"]
> [Sonraki: Gelişmiş konular](advanced.md)
