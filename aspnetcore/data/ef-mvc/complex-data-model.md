---
title: 'Öğretici: Karmaşık bir veri modeli oluşturun - EF Core ile MVC ASP.NET'
description: Bu öğreticide, daha fazla varlık ve ilişki ekleyin ve biçimlendirme, doğrulama ve eşleme kuralları belirterek veri modelini özelleştirin.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 91fd09874ecab8bfdb6a38a404faba04aeb73edc
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657432"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a>Öğretici: Karmaşık bir veri modeli oluşturun - EF Core ile MVC ASP.NET

Önceki öğreticilerde, üç varlıktan oluşan basit bir veri modeliyle çalıştınız. Bu öğreticide, daha fazla varlık ve ilişki ekler siniz ve biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirterek veri modelini özelleştireceksiniz.

İşi bittiğinde, varlık sınıfları aşağıdaki resimde gösterilen tamamlanmış veri modelini yapar:

![Varlık diyagramı](complex-data-model/_static/diagram.png)

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Veri modelini özelleştirme
> * Öğrenci varlığında değişiklik yapma
> * Eğitmen varlığı oluşturma
> * OfficeAtama tüzel kişiliği oluşturma
> * Kurs varlığını değiştir
> * Bölüm varlığı oluşturma
> * Kayıt varlığını değiştirin
> * Veritabanı bağlamını güncelleştirme
> * Test verileri ile tohum veritabanı
> * Geçiş ekleme
> * Bağlantı dizesini değiştirme
> * Veritabanını güncelleştirme

## <a name="prerequisites"></a>Ön koşullar

* [EF Core geçişlerini kullanma](migrations.md)

## <a name="customize-the-data-model"></a>Veri modelini özelleştirme

Bu bölümde biçimlendirme, doğrulama ve veritabanı eşleme kurallarını belirten öznitelikleri kullanarak veri modelini nasıl özelleştireceğiniz göreceksiniz. Ardından, aşağıdaki bölümlerin birkaçında, önceden oluşturduğunuz sınıflara öznitelikler ekleyerek ve modelde kalan varlık türleri için yeni sınıflar oluşturarak tüm Okul veri modelini oluşturursunuz.

### <a name="the-datatype-attribute"></a>DataType özniteliği

Öğrenci kayıt tarihleri için, tüm web sayfaları şu anda tarihle birlikte saati görüntüler, ancak bu alan için tek umursadığınız tarihtir. Veri ek açıklama özniteliklerini kullanarak, verileri gösteren her görünümde görüntü biçimini düzeltecek bir kod değişikliği yapabilirsiniz. Bunun nasıl yapılacağının bir örneğini görmek için, `EnrollmentDate` `Student` sınıftaki özelliğe bir öznitelik eklersiniz.

*Modeller/Student.cs'de,* `using` `System.ComponentModel.DataAnnotations` ad alanı için bir `DataType` `DisplayFormat` deyim ekleyin `EnrollmentDate` ve aşağıdaki örnekte gösterildiği gibi özellik eklemek ve öznitelikleri:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

Öznitelik, `DataType` veritabanı içsel türünden daha spesifik bir veri türünü belirtmek için kullanılır. Bu durumda biz sadece tarih ve saat değil, tarih ve saat izlemek istiyorum. Numaralandırma, `DataType` Tarih, Saat, Telefon Numarası, Para Birimi, E-posta Adresi ve daha fazlası gibi birçok veri türü sağlar. Öznitelik, `DataType` uygulamanın türe özgü özellikleri otomatik olarak sağlamasına da olanak tanır. Örneğin, HTML5'i destekleyen tarayıcılarda bir `mailto:` bağlantı oluşturulabilir `DataType.Date` `DataType.EmailAddress`ve tarih seçici kullanılabilir. Öznitelik `DataType` HTML 5 `data-` (telaffuz veri tire) html 5 tarayıcılar anlayabileceği öznitelikleri yakar. Öznitelikler `DataType` herhangi bir doğrulama sağlamaz.

`DataType.Date`görüntülenen tarihin biçimini belirtmez. Varsayılan olarak, veri alanı sunucunun CultureInfo'suna dayalı varsayılan biçimlere göre görüntülenir.

Öznitelik, `DisplayFormat` tarih biçimini açıkça belirtmek için kullanılır:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Ayar, `ApplyFormatInEditMode` değer düzenleme için bir metin kutusunda görüntülendiğinde biçimlendirmenin de uygulanması gerektiğini belirtir. (Bazı alanlar için (örneğin, para birimi değerleri için, düzenleme için metin kutusundaki para birimi simgesini istemeyebilirsiniz.)

Özniteliği tek `DisplayFormat` başına kullanabilirsiniz, ancak özniteliği de kullanmak `DataType` genellikle iyi bir fikirdir. Öznitelik, `DataType` verilerin bir ekranda nasıl işlenir, aksine anlamsallarını iletir ve aşağıdaki avantajları `DisplayFormat`sağlar:

* Tarayıcı HTML5 özelliklerini etkinleştirebilir (örneğin bir takvim denetimi, yerel para birimi simgesi, e-posta bağlantıları, bazı istemci tarafı giriş doğrulama, vb. göstermek için).

* Varsayılan olarak, tarayıcı verileri bulunduğunuz yerin temeline göre doğru biçimi kullanarak işler.

Daha fazla bilgi [ \<için, giriş> etiketi yardımcı belgeleri](../../mvc/views/working-with-forms.md#the-input-tag-helper)bakın.

Uygulamayı çalıştırın, Öğrenci Dizini sayfasına gidin ve kayıt tarihleri için artık saatlerin görüntülenmediğini fark edin. Aynı öğrenci modelini kullanan herhangi bir görünüm için de geçerli olacaktır.

![Tarihlerini saatsiz gösteren öğrenci dizini sayfası](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>StringLength özniteliği

Öznitelikleri kullanarak veri doğrulama kuralları ve doğrulama hatası iletileri de belirtebilirsiniz. Öznitelik `StringLength` veritabanındaki maksimum uzunluğu ayarlar ve ASP.NET Core MVC için istemci tarafı ve sunucu tarafı doğrulama sağlar. Bu öznitelikteki minimum dize uzunluğunu da belirtebilirsiniz, ancak minimum değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.

Kullanıcıların bir ad için 50'den fazla karakter girmediğinden emin olmak istediğinizi varsayalım. Bu sınırlamayı eklemek `StringLength` için, `LastName` `FirstMidName` aşağıdaki örnekte gösterildiği gibi, öznitelikleri ve özellikleri ekleyin:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Öznitelik, `StringLength` kullanıcının bir ad için beyaz alan girmesini engellemez. Girişe `RegularExpression` kısıtlamalar uygulamak için özniteliği kullanabilirsiniz. Örneğin, aşağıdaki kod ilk karakterin büyük harf, kalan karakterlerin ise alfabetik olmasını gerektirir:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Öznitelik özniteliğe `MaxLength` benzer `StringLength` işlevsellik sağlar, ancak istemci tarafı doğrulaması sağlamaz.

Veritabanı modeli artık veritabanı şemasında değişiklik gerektiren bir şekilde değişti. Uygulama Kullanıcı GI'sini kullanarak veritabanına eklemiş olabileceğiniz verileri kaybetmeden şemayı güncelleştirmek için geçişleri kullanırsınız.

Değişikliklerinizi kaydedin ve projeyi oluşturun. Ardından proje klasöründeki komut penceresini açın ve aşağıdaki komutları girin:

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

Komut, `migrations add` değişiklik iki sütun için maksimum uzunluğu kısalttığından, veri kaybının meydana gelebileceği konusunda uyarır.  Geçişler * \<timeStamp>_MaxLengthOnNames.cs*adlı bir dosya oluşturur. Bu dosya, geçerli `Up` veri modeliyle eşleşecek şekilde veritabanını güncelleştirecek yöntemde kod içerir. Komut `database update` bu kodu çalıştırıyor.

Geçişler dosya adı için önceden belirlenmiş zaman damgası, geçişleri sıralamak için Entity Framework tarafından kullanılır. Güncelleştirme veritabanı komutunu çalıştırmadan önce birden çok geçiş oluşturabilirsiniz ve daha sonra tüm geçişler oluşturuldukları sırada uygulanır.

Uygulamayı çalıştırın, **Öğrenciler** sekmesini seçin, **Yeni Oluştur'u**tıklatın ve 50 karakterden daha uzun bir ad girmeyi deneyin. Uygulama bunu yapmanızı engellemelidir. 

### <a name="the-column-attribute"></a>Sütun özniteliği

Sınıflarınızın ve özelliklerinizin veritabanına nasıl eşlenebileceğini denetlemek için öznitelikleri de kullanabilirsiniz. Alan da bir `FirstMidName` göbek adı içerebileceğinden, ilk ad alanı için adı kullandığınızı varsayalım. Ancak veritabanı sütununa adlandırılmasını `FirstName`istiyorsunuz, çünkü veritabanına karşı geçici sorgular yazacak kullanıcılar bu ada alışkındır. Bu eşlemi yapmak için `Column` özniteliği kullanabilirsiniz.

Öznitelik, `Column` veritabanı oluşturulduğunda, `Student` `FirstMidName` tablonun özelliğieşleyen sütununun adının `FirstName`verolacağını belirtir. Başka bir deyişle, kodunuz `Student.FirstMidName`, veriler `FirstName` `Student` tablonun sütunundan gelir veya güncelleştirilir. Sütun adlarını belirtmezseniz, bunlara özellik adı ile aynı ad verilir.

*Student.cs* dosyasında, aşağıdaki `using` vurgulanan kodda gösterildiği gibi, özellik için `System.ComponentModel.DataAnnotations.Schema` bir deyim ekleyin ve `FirstMidName` özelliğe sütun adı özniteliği ekleyin:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Öznitelik eklenmesi, `Column` veritabanıyla eşleşmeyemeyeceği için modeli destekleyen `SchoolContext`modeli değiştirir.

Değişikliklerinizi kaydedin ve projeyi oluşturun. Ardından proje klasöründeki komut penceresini açın ve başka bir geçiş oluşturmak için aşağıdaki komutları girin:

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

**SQL Server Object Explorer'da,** **Öğrenci** tablosunu çift tıklatarak Öğrenci tablosu tasarımcısını açın.

![Göçlerden sonra SSOX'ta öğrenci tablosu](complex-data-model/_static/ssox-after-migration.png)

İlk iki geçişi uygulamadan önce, ad sütunları nvarchar (MAX) türündendi. Onlar şimdi nvarchar (50) konum ve sütun adı FirstMidName firstname olarak değiştirildi.

> [!Note]
> Aşağıdaki bölümlerdeki tüm varlık sınıflarını oluşturmayı bitirmeden önce derlemeye çalışırsanız, derleyici hataları alabilirsiniz.

## <a name="changes-to-student-entity"></a>Öğrenci varlığında yapılan değişiklikler

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

*Modeller/Student.cs'de,* daha önce eklediğiniz kodu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Gerekli öznitelik

Öznitelik `Required` ad özelliklerini gerekli alanları yapar. Öznitelik `Required` değer türleri (DateTime, int, double, float, vb) gibi nullable olmayan türleri için gerekli değildir. Null olamaz türleri otomatik olarak gerekli alanlar olarak kabul edilir.

Öznitelik, `Required` uygulanacak olması `MinimumLength` `MinimumLength` için kullanılmalıdır.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Görüntü özniteliği

Öznitelik, `Display` metin kutularıiçin alt yazının her örnekte özellik adı yerine "Ad", "Soyadı", "Tam Ad" ve "Kayıt Tarihi" olması gerektiğini belirtir (sözcükleri bölen boşluk yoktur).

### <a name="the-fullname-calculated-property"></a>FullName hesaplanan özellik

`FullName`diğer iki özelliği nkiçleme tarafından oluşturulan bir değeri döndüren hesaplanmış bir özelliktir. Bu nedenle yalnızca bir erişime `FullName` sahip ve veritabanında hiçbir sütun oluşturulur.

## <a name="create-instructor-entity"></a>Eğitmen varlığı oluşturma

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

Şablon kodunu aşağıdaki kodla değiştirerek *Modeller/Instructor.cs*oluşturun:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Öğrenci ve Eğitmen kuruluşlarında çeşitli özelliklerin aynı olduğuna dikkat edin. Bu serinin ilerleyen saatlerinde [Devralma](inheritance.md) Uygulama öğreticisinde, artıklığı ortadan kaldırmak için bu kodu yeniden düzenlemeniz gerekir.

Bir satıra birden çok öznitelik koyabilirsiniz, `HireDate` böylece öznitelikleri aşağıdaki gibi de yazabilirsiniz:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Ders Atamaları ve OfisAtama navigasyon özellikleri

Ve `CourseAssignments` `OfficeAssignment` özellikleri navigasyon özellikleridir.

Bir eğitmen herhangi bir sayıda `CourseAssignments` ders verebilir, bu nedenle bir koleksiyon olarak tanımlanır.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Bir gezinti özelliği birden çok varlığı barındırabiliyorsa, türü girişlerin eklenebileceği, silinebileceği ve güncelleştirilebildiği bir liste olmalıdır.  Belirtebilirsiniz `ICollection<T>` veya gibi bir `List<T>` `HashSet<T>`tür ya da . Belirtirseniz, `ICollection<T>`EF varsayılan `HashSet<T>` olarak bir koleksiyon oluşturur.

Bu `CourseAssignment` varlıkların neden çok-çok ilişkiler hakkında bölümde aşağıda açıklanmıştır.

Contoso Üniversitesi iş kuralları, bir eğitmenin en fazla `OfficeAssignment` bir ofis te sahip olabileceğini belirtir, bu nedenle özellik tek bir OfficeAssignment tüzel kişiliğine sahiptir (ofis atanmamışsa geçersiz olabilir).

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a>OfficeAtama tüzel kişiliği oluşturma

![OfficeAssignment tüzel kişiliği](complex-data-model/_static/officeassignment-entity.png)

Aşağıdaki kodla *Modeller/OfficeAssignment.cs* oluşturun:

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Anahtar özniteliği

Eğitmen ve OfficeAssignment varlıkları arasında bire sıfır veya bir ilişkisi vardır. Bir ofis ataması yalnızca atandığı eğitmenle ilişkili olarak bulunur ve bu nedenle birincil anahtarı aynı zamanda Eğitmen tüzel kişiliğinin yabancı anahtarıdır. Ancak Entity Framework, adı Kimlik veya classnameID adlandırma kuralını izlemediği için InstructorID'yi bu varlığın birincil anahtarı olarak otomatik olarak tanıyamaz. Bu nedenle, `Key` öznitelik anahtar olarak tanımlamak için kullanılır:

```csharp
[Key]
public int InstructorID { get; set; }
```

Varlığın `Key` kendi birincil anahtarı varsa, ancak özelliği classnameID veya ID'den başka bir şey olarak adlandırmak istiyorsanız, özniteliği de kullanabilirsiniz.

Varsayılan olarak, sütun tanımlayıcı bir ilişki için olduğundan, EF anahtarı veritabanı tarafından oluşturulmayan olarak ele alır.

### <a name="the-instructor-navigation-property"></a>Eğitmen navigasyon özelliği

Eğitmen tüzel kişiliğinin `OfficeAssignment` boşsayılabilir bir gezinti özelliği vardır (çünkü bir eğitmenin ofis `Instructor` ataması olmayabilir) ve OfficeAssignment tüzel kişiliğinin geçersiz olmayan bir gezinti özelliği vardır (çünkü bir ofis ataması eğitmen olmadan var olamaz - `InstructorID` geçersizdir). Bir Eğitmen tüzel kişiliğinin ilgili bir OfficeAssignment tüzel kişiliği varsa, her varlığın gezinti özelliğinde diğerine bir başvurusu olur.

İlgili bir `[Required]` eğitmen olması gerektiğini belirtmek için Eğitmen navigasyon özelliğine bir öznitelik koyabilirsiniz, ancak `InstructorID` yabancı anahtar (bu tablonun anahtarı da) geçersiz olduğundan bunu yapmanız alabilirsiniz.

## <a name="modify-course-entity"></a>Kurs varlığını değiştir

![Kurs varlığı](complex-data-model/_static/course-entity.png)

*Modeller/Course.cs'de,* daha önce eklediğiniz kodu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Kurs tüzel kişiliği, `DepartmentID` ilgili Bölüm tüzel kişiliğini gösteren `Department` yabancı bir anahtar özelliğine ve navigasyon özelliğine sahiptir.

Varlık Çerçevesi, ilgili bir varlık için gezinti özelliğiniz olduğunda veri modelinize yabancı bir anahtar özelliği eklemenizi gerektirmez.  EF, ihtiyaç duyulan her yerde veritabanında otomatik olarak yabancı anahtarlar oluşturur ve bunlar için [gölge özellikleri](/ef/core/modeling/shadow-properties) oluşturur. Ancak veri modelinde yabancı anahtara sahip olmak güncelleştirmeleri daha basit ve daha verimli hale getirebilir. Örneğin, bir ders tüzel kişiliğini yeniden yüklemek için getirdiğinizde, yüklemezseniz Bölüm varlığı geçersiz olur, bu nedenle kurs varlığını güncelleştirdiğinizde, önce Bölüm varlığını getirmeniz gerekir. Yabancı anahtar özelliği `DepartmentID` veri modeline dahil edildiğinde, güncelleştirmeden önce Departman varlığını almanız gerekmez.

### <a name="the-databasegenerated-attribute"></a>DatabaseOluşturulan öznitelik

Özellik üzerinde `DatabaseGenerated` `None` parametre ile öznitelik birincil anahtar değerleri veritabanı tarafından oluşturulan yerine kullanıcı tarafından sağlanmaktadır belirtir. `CourseID`

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Varsayılan olarak, Varlık Çerçevesi birincil anahtar değerlerinveritabanı tarafından oluşturulduğunu varsayar. Çoğu senaryoda istediğin bu. Ancak, Kurs kuruluşları için, bir bölüm için 1000 serisi, başka bir bölüm için 2000 serisi gibi kullanıcı tarafından belirtilen bir kurs numarası kullanırsınız.

Öznitelik, `DatabaseGenerated` bir satırın oluşturulduğu veya güncelleştirilme tarihini kaydetmek için kullanılan veritabanı sütunlarında olduğu gibi varsayılan değerleri oluşturmak için de kullanılabilir.  Daha fazla bilgi [için, oluşturulan özellikler](/ef/core/modeling/generated-properties)e bakın.

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve navigasyon özellikleri

Ders varlığındaki yabancı anahtar özellikleri ve navigasyon özellikleri aşağıdaki ilişkileri yansıtır:

Bir kurs bir bölüme atanır, `DepartmentID` bu nedenle `Department` yukarıda belirtilen nedenlerle yabancı bir anahtar ve navigasyon özelliği vardır.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Bir kursa herhangi bir sayıda öğrenci kaydolabilir, bu nedenle `Enrollments` navigasyon özelliği bir koleksiyondur:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Bir ders birden fazla eğitmen tarafından `CourseAssignments` öğretilebilir, bu nedenle `CourseAssignment` navigasyon özelliği bir koleksiyondur (türü [daha sonra](#many-to-many-relationships)açıklanır):

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a>Bölüm varlığı oluşturma

![Bölüm tüzel kişiliği](complex-data-model/_static/department-entity.png)

Aşağıdaki kodile *Modeller/Department.cs* oluşturun:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Sütun özniteliği

Daha önce `Column` sütun adı eşlemi değiştirmek için öznitelik kullanılır. Bölüm varlığı kodunda, `Column` öznitelik, veritabanındaki SQL Server para türü kullanılarak sütunun tanımlanabilmesi için SQL veri türü eşlemi eşlemi değiştirmek için kullanılır:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Varlık Çerçevesi, özellik için tanımladığınız CLR türüne göre uygun SQL Server veri türünü seçtiğinden, sütun eşleme genellikle gerekli değildir. CLR `decimal` türü, SQL Server `decimal` türüyle eşleştirilir. Ancak bu durumda, sütunun para birimi tutarlarını tutacağını ve para veri türünün bunun için daha uygun olduğunu bilirsiniz.

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve navigasyon özellikleri

Yabancı anahtar ve gezinme özellikleri aşağıdaki ilişkileri yansıtır:

Bir bölümün bir yöneticisi olabilir veya olmayabilir ve yönetici her zaman bir eğitmendir. Bu `InstructorID` nedenle özellik, Eğitmen varlığına yabancı anahtar olarak eklenir ve özelliği `int` nullable olarak işaretlemek için tür atamasonra bir soru işareti eklenir. Gezinti özelliği adlandırılmış, `Administrator` ancak bir Eğitmen varlık tutar:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Bir bölümün birçok dersi olabilir, bu nedenle Kurslar navigasyon özelliği vardır:

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> Taraf Çerçevesi, sözleşmeyle, geçersiz olmayan yabancı anahtarlar ve çok-çok ilişkiler için basamaklı silmeyi sağlar. Bu, geçiş eklemeye çalıştığınızda bir özel durum neden olacak dairesel basamaklı silme kuralları, neden olabilir. Örneğin, Department.InstructorID özelliğini nullable olarak tanımlamadıysanız, EF, eğitmeni sildiğinizde bölümü silmek için basamaklı bir silme kuralını yapılandırır, ki bu olmasını istediğiniz şey değildir. İş kurallarınız özelliğin `InstructorID` geçersiz olmasını gerektiriyorsa, ilişkide basamaklı silmeyi devre dışı ksaymak için aşağıdaki akıcı API deyimini kullanmanız gerekir:
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a>Kayıt varlığını değiştirin

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

*Modeller/Enrollment.cs'de,* daha önce eklediğiniz kodu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve navigasyon özellikleri

Yabancı anahtar özellikleri ve gezinme özellikleri aşağıdaki ilişkileri yansıtır:

Kayıt kaydı tek bir kurs içindir, `CourseID` bu nedenle yabancı `Course` bir anahtar özelliği ve navigasyon özelliği vardır:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Kayıt kaydı tek bir öğrenci içindir, `StudentID` bu nedenle yabancı `Student` bir anahtar özelliği ve navigasyon özelliği vardır:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Çok-Çok ilişkiler

Öğrenci ve Kurs varlıkları arasında çok-çok ilişkisi vardır ve Kayıt varlığı veritabanında *yük yüklü* bir çok-çok birleştirme tablosu olarak işlev görür. "Yük ile" Kayıt tablosu, birleştirilmiş tablolar için yabancı anahtarların yanı sıra ek veriler içerdiği anlamına gelir (bu durumda, birincil anahtar ve Not özelliği).

Aşağıdaki resimde, bu ilişkilerin varlık diyagramında nasıl göründüğü gösterilmektedir. (Bu diyagram EF 6.x için Entity Framework Power Tools kullanılarak oluşturuldu; diyagramı oluşturmak öğreticinin bir parçası değil, sadece burada bir illüstrasyon olarak kullanılıyor.)

![Öğrenci-Kurs birçok ilişki için çok](complex-data-model/_static/student-course.png)

Her ilişki satırının bir ucunda 1, diğerucunda bir yıldız (*) vardır ve bu da bir-çok ilişkisi gösterir.

Kayıt tablosunda not bilgileri içermiyorsa, yalnızca iki yabancı anahtar CourseID ve StudentID içermelidir. Bu durumda, veritabanında yük (veya saf birleştirme tablosu) olmayan çok-çok birleştirme tablosu olacaktır. Eğitmen ve Kurs varlıkları bu tür çok-çok ilişki vardır ve bir sonraki adım yük olmadan bir birleştirme tablosu olarak işlev bir varlık sınıfı oluşturmaktır.

(EF 6.x, çok-çok ilişkiler için örtülü birleştirme tablolarını destekler, ancak EF Core desteklemez. Daha fazla bilgi [için, EF Core GitHub deposundaki tartışmaya](https://github.com/aspnet/EntityFramework/issues/1368)bakın.)

## <a name="the-courseassignment-entity"></a>Ders Atama sı

![Ders Atama varlığı](complex-data-model/_static/courseassignment-entity.png)

Aşağıdaki kodile *Modeller/DersAtama.cs* oluşturun:

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>Varlık adlarını birleştirme

Bir join tablo eğitmen-to-kurslar çok-çok ilişki için veritabanında gereklidir ve bir varlık kümesi tarafından temsil edilmesi gerekir. Bu durumda bir birleştirme varlık `EntityName1EntityName2`adlandırmak yaygındır `CourseInstructor`. Ancak, ilişkiyi açıklayan bir ad seçmenizi öneririz. Veri modelleri basit başlar ve büyür, yük yok sık sık daha sonra yük alma birleştirir. Açıklayıcı bir varlık adı ile başlarsanız, daha sonra adı değiştirmeniz gerekir. İdeal olarak, birleştirme varlığı iş alanında kendi doğal (muhtemelen tek kelime) adı olurdu. Örneğin, Kitaplar ve Müşteriler Derecelendirmeler aracılığıyla bağlanabilir. Bu ilişki `CourseAssignment` için, daha `CourseInstructor`iyi bir seçimdir .

### <a name="composite-key"></a>Kompozit anahtar

Yabancı anahtarlar nullable ve birlikte benzersiz tablonun her satırı tanımlamak olmadığından, ayrı bir birincil anahtar için gerek yoktur. *InstructorID* ve *CourseID* özellikleri kompozit birincil anahtar olarak işlev almalıdır. EF için bileşik birincil anahtarları tanımlamak için tek yolu *akıcı API* kullanarak (öznitelikleri kullanılarak yapılamaz). Bileşik birincil anahtarı sonraki bölümde nasıl yapılandıracağınızı görürsünüz.

Bileşik anahtar, bir kurs için birden çok satır ve bir eğitmen için birden çok satır olsa da, aynı eğitmen ve kurs için birden çok satır alamamanızı sağlar. Birleştirme `Enrollment` varlığı kendi birincil anahtarını tanımlar, bu nedenle bu tür yinelemeler mümkündür. Bu tür yinelemeleri önlemek için, yabancı anahtar alanlarına benzersiz `Enrollment` bir dizin ekleyebilir `CourseAssignment`veya ''ye benzer bir birincil bileşik anahtarla yapılandırabilirsiniz. Daha fazla bilgi için [Dizinler'e](/ef/core/modeling/indexes)bakın.

## <a name="update-the-database-context"></a>Veritabanı bağlamını güncelleştirme

*Data/SchoolContext.cs* dosyasına aşağıdaki vurgulanmış kodu ekleyin:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Bu kod yeni varlıkları ekler ve Ders Atama kuruluşunun bileşik birincil anahtarını yapılandırır.

## <a name="about-a-fluent-api-alternative"></a>Akıcı bir API alternatifi hakkında

Sınıfın yöntemindeki `OnModelCreating` kod, EF davranışını yapılandırmak için *akıcı API* kullanır. `DbContext` API'ye "akıcı" denir, çünkü genellikle ef [core belgelerinden](/ef/core/modeling/#use-fluent-api-to-configure-a-model)bu örnekte olduğu gibi, bir dizi yöntem çağrısını tek bir deyimde bir araya getirerek kullanılır:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

Bu öğreticide, akıcı API'yi yalnızca özniteliklerle yapamayacağınız veritabanı eşlemeiçin kullanıyorsunuz. Ancak, öznitelikleri kullanarak yapabileceğiniz biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtmek için akıcı API'yi de kullanabilirsiniz. Akıcı API `MinimumLength` ile uygulanamıyor gibi bazı öznitelikler. Daha önce de `MinimumLength` belirtildiği gibi, şema değişmez, sadece bir istemci ve sunucu tarafı doğrulama kuralı uygular.

Bazı geliştiriciler, varlık sınıflarını "temiz" tutabilmek için akıcı API'yi yalnızca kullanmayı tercih eder. İsterseniz öznitelikleri ve akıcı API karıştırabilirsiniz ve sadece akıcı API kullanılarak yapılabilir birkaç özelleştirmeler vardır, ancak genel olarak önerilen uygulama bu iki yaklaşımbirini seçmek ve mümkün olduğunca tutarlı bir şekilde kullanmaktır. Her ikisini de kullanırsanız, çakışma olan her yerde Akıcı API'nin öznitelikleri geçersiz kladığını unutmayın.

Öznitelikler ve akıcı API hakkında daha fazla bilgi için yapılandırma [yöntemlerine](/ef/core/modeling/)bakın.

## <a name="entity-diagram-showing-relationships"></a>İlişkileri Gösteren Varlık Diyagramı

Aşağıdaki resimde, Tamamlanan Okul modeli için Entity Framework Power Tools'un oluşturduğu diyagram gösterilmektedir.

![Varlık diyagramı](complex-data-model/_static/diagram.png)

Bir-çok ilişki satırlarının (1'den 1'e) \*yanı sıra, Burada Eğitmen ve OfficeAtama varlıkları ile Eğitmen ve Bölüm varlıkları arasındaki sıfır veya bir-çok ilişkisi çizgisi (0..1 ila *) arasındaki birden sıfıra veya birilişki çizgisini (1'den 0.0'a) görebilirsiniz.

## <a name="seed-database-with-test-data"></a>Test verileri ile tohum veritabanı

Oluşturduğunuz yeni varlıklar için tohum verileri sağlamak için *Data/DbInitializer.cs* dosyasındaki kodu aşağıdaki kodla değiştirin.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

İlk öğreticide gördüğünüz gibi, bu kodun çoğu yalnızca yeni varlık nesneleri oluşturur ve örnek verileri test için gerekli olan özelliklere yükler. Birden çok ilişkinin nasıl işlendiğine dikkat edin: kod, varlık `Enrollments` kümelerinde `CourseAssignment` varlıklar oluşturarak ilişkiler oluşturur.

## <a name="add-a-migration"></a>Geçiş ekleme

Değişikliklerinizi kaydedin ve projeyi oluşturun. Ardından proje klasöründeki komut penceresini `migrations add` açın ve komutu girin (güncelleştirme veritabanı komutunu henüz yapmayın):

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

Olası veri kaybı hakkında bir uyarı alırsınız.

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Bu noktada komutu `database update` çalıştırmayı denediyseniz (henüz yapmayın), aşağıdaki hatayı alırsınız:

> ALTER TABLE deyimi FOREIGN KEY kısıtlaması ile çelişen "FK_dbo. Course_dbo. Department_DepartmentID". Çakışma veritabanı "ContosoUniversity", tablo "dbo oluştu. Bölüm", sütun 'DepartmentID'.

Bazen varolan verilerle geçişleri yürüttüğüzde, yabancı anahtar kısıtlamalarını karşılamak için veritabanına saplama verileri eklemeniz gerekir. `Up` Yöntemde oluşturulan kod, Ders tablosuna nullable olmayan DepartmentID yabancı anahtarı ekler. Kod çalıştığında Ders tablosunda zaten satırlar varsa, `AddColumn` SQL Server sütuna null olmayan hangi değeri koyacağımı bilmediği için işlem başarısız olur. Bu öğretici için geçişi yeni bir veritabanında çalıştıracaksınız, ancak bir üretim uygulamasında geçişin varolan verileri işlemesini yapmanız gerekir, bu nedenle aşağıdaki yönler bunun nasıl yapılacağınıgösteren bir örneği gösterir.

Varolan verilerle bu geçiş çalışması için yeni sütuna varsayılan değer vermek için kodu değiştirmeniz ve varsayılan departman olarak hareket etmek için "Geçici" adlı bir saplama bölümü oluşturmanız gerekir. Sonuç olarak, mevcut Kurs satırlarının `Up` tümü, yöntem çalıştırıldıktan sonra "Temp" bölümüyle ilgili olacaktır.

* *{timestamp}_ComplexDataModel.cs* dosyasını açın.

* Ders tablosuna DepartmentID sütununa ekleyen kod satırına yorum yapın.

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* Bölüm tablosunu oluşturan koddan sonra aşağıdaki vurgulanan kodu ekleyin:

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Bir üretim uygulamasında, Bölüm satırlarını eklemek ve Ders satırlarını yeni Bölüm satırlarıyla ilişkilendirmek için kod veya komut dosyası yazarsınız. Daha sonra artık "Temp" bölümüne veya Course.DepartmentID sütunundaki varsayılan değere ihtiyacınız olmaz.

Değişikliklerinizi kaydedin ve projeyi oluşturun.

## <a name="change-the-connection-string"></a>Bağlantı dizesini değiştirme

Artık sınıfta boş bir `DbInitializer` veritabanına yeni varlıklar için tohum verileri ekleyen yeni kodunuz var. EF'nin yeni bir boş veritabanı oluşturması için *appsettings.json'daki* bağlantı dizesindeki veritabanının adını ContosoUniversity3 veya kullandığınız bilgisayarda kullanmadığınız başka bir adla değiştirin.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

Değişikliğinizi *appsettings.json'a*kaydedin.

> [!NOTE]
> Veritabanı adını değiştirmeye alternatif olarak veritabanını silebilirsiniz. **SQL Server Object Explorer** (SSOX) veya CLI komutunu `database drop` kullanın:
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a>Veritabanını güncelleştirme

Veritabanı adını değiştirdikten veya veritabanını sildikten `database update` sonra, geçişleri yürütmek için komut penceresindeki komutu çalıştırın.

```dotnetcli
dotnet ef database update
```

Yöntemin `DbInitializer.Initialize` çalışmasına ve yeni veritabanını doldurmasına neden olacak şekilde uygulamayı çalıştırın.

Veritabanını daha önce yaptığınız gibi SSOX'ta açın ve tüm tabloların oluşturulduğunu görmek için **Tablolar** düğümünün genişletilmesini. (SSOX'unuzun hala önceki nden itibaren açık olması durumunda, **Yenile** düğmesini tıklatın.)

![SSOX tabloları](complex-data-model/_static/ssox-tables.png)

Veritabanını oluşturan baş harf kodunu tetiklemek için uygulamayı çalıştırın.

**Ders Atama** tablosuna sağ tıklayın ve içinde veri olduğunu doğrulamak için **Verileri Görüntüle'yi** seçin.

![SSOX'ta Ders Atama verileri](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a>Kodu alma

[Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Veri modelini özelleştirilmiş
> * Öğrenci varlığında değişiklik yapıldı
> * Oluşturulan Eğitmen varlık
> * Oluşturulan OfficeAssignment tüzel kişiliği
> * Modifiye Kurs varlığı
> * Oluşturulan Bölüm varlığı
> * Değiştirilmiş Kayıt varlığı
> * Veritabanı bağlamını güncelleştirildi
> * Test verileri ile seri veritabanı
> * Geçiş eklendi
> * Bağlantı dizesini değiştirme
> * Veritabanını güncelleştirildi

İlgili verilere nasıl erişiniriz hakkında daha fazla bilgi edinmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Sonraki: İlgili verilere erişin](read-related-data.md)
