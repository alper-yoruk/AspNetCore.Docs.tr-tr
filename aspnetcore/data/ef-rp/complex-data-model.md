---
title: ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - Veri Modeli - 8'de 5
author: rick-anderson
description: Bu öğreticide, daha fazla varlık ve ilişki ekleyin ve biçimlendirme, doğrulama ve eşleme kuralları belirterek veri modelini özelleştirin.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1d81a0444487c6396bb32381ed2cb26d44312c3a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665720"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a>ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - Veri Modeli - 8'de 5

Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Önceki öğreticiler üç varlıktan oluşan temel bir veri modeli ile çalıştı. Bu eğitimde:

* Daha fazla varlık ve ilişki eklenir.
* Veri modeli biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirterek özelleştirilir.

Tamamlanan veri modeli aşağıdaki resimde gösterilmiştir:

![Varlık diyagramı](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a>Öğrenci varlığı

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

*Modeller/Student.cs'deki* kodu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

Önceki kod bir `FullName` özellik ekler ve varolan özelliklere aşağıdaki öznitelikleri ekler:

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a>FullName hesaplanan özellik

`FullName`diğer iki özelliği nkiçleme tarafından oluşturulan bir değeri döndüren hesaplanmış bir özelliktir. `FullName`ayarlanamaz, bu nedenle yalnızca bir erişime sahip. Veritabanında `FullName` sütun oluşturulmaz.

### <a name="the-datatype-attribute"></a>DataType özniteliği

```csharp
[DataType(DataType.Date)]
```

Öğrenci kayıt tarihleri için, yalnızca tarih alakalı olsa da, tüm sayfalar şu anda tarihle birlikte günün saatini görüntüler. Veri ek açıklama özniteliklerini kullanarak, verileri gösteren her sayfada görüntü biçimini düzeltecek bir kod değişikliği yapabilirsiniz. 

[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) özniteliği, veritabanı içsel türünden daha özel bir veri türü belirtir. Bu durumda tarih ve saat değil, yalnızca tarih görüntülenmelidir. [DataType Numaralandırma,](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) Tarih, Saat, Telefon Numarası, Para Birimi, E-posta Adresi gibi birçok veri türü sağlar. Öznitelik, `DataType` uygulamanın türe özgü özellikleri otomatik olarak sağlamasına da olanak tanır. Örneğin:

* Bağlantı `mailto:` otomatik olarak `DataType.EmailAddress`.
* Tarih seçici çoğu tarayıcıda sağlanır. `DataType.Date`

Öznitelik `DataType` HTML 5 `data-` (belirgin veri tire) öznitelikleri yakar. Öznitelikler `DataType` doğrulama sağlamaz.

### <a name="the-displayformat-attribute"></a>DisplayFormat özniteliği

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`DataType.Date`görüntülenen tarihin biçimini belirtmez. Varsayılan olarak, tarih alanı sunucunun [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)dayalı varsayılan biçimlerine göre görüntülenir.

Öznitelik, `DisplayFormat` tarih biçimini açıkça belirtmek için kullanılır. Ayar, `ApplyFormatInEditMode` biçimlendirmenin edit UI'ye de uygulanması gerektiğini belirtir. Bazı alanlar kullanmamalıdır. `ApplyFormatInEditMode` Örneğin, para birimi simgesi genellikle bir edit metin kutusunda görüntülenmemelidir.

Öznitelik `DisplayFormat` kendisi tarafından kullanılabilir. Özniteliği öznitelik ile `DataType` `DisplayFormat` kullanmak genellikle iyi bir fikirdir. Öznitelik, `DataType` verilerin bir ekranda nasıl işlenirse, anlambilimini aktarıyor. Öznitelik, `DataType` aşağıdaki avantajlarda `DisplayFormat`bulunmayan avantajları sağlar:

* Tarayıcı HTML5 özelliklerini etkinleştirebilir. Örneğin, bir takvim denetimi, yerel para birimi simgesi, e-posta bağlantıları ve istemci tarafı giriş doğrulaması gösterin.
* Varsayılan olarak, tarayıcı verileri yerel eki göre doğru biçimi kullanarak işler.

Daha fazla bilgi [ \<için, Yardımcı yı etiketleme> girişine](xref:mvc/views/working-with-forms#the-input-tag-helper)bakın.

### <a name="the-stringlength-attribute"></a>StringLength özniteliği

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

Veri doğrulama kuralları ve doğrulama hatası iletileri öznitelikleri ile belirtilebilir. [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) özniteliği, veri alanında izin verilen en az ve en büyük karakter uzunluğunu belirtir. Gösterilen kod adları en fazla 50 karakterle sınırlar. En düşük dize uzunluğunu ayarlayan bir örnek [daha sonra](#the-required-attribute)gösterilir.

Öznitelik, `StringLength` istemci tarafı ve sunucu tarafı doğrulaması da sağlar. Minimum değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.

Öznitelik, `StringLength` kullanıcının bir ad için beyaz alan girmesini engellemez. [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) özniteliği, girişe kısıtlamalar uygulamak için kullanılabilir. Örneğin, aşağıdaki kod ilk karakterin büyük harf, kalan karakterlerin ise alfabetik olmasını gerektirir:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**SQL Server Object Explorer'da** (SSOX), **Öğrenci** tablosunu çift tıklatarak Öğrenci tablosu tasarımcısını açın.

![Göçlerden önce SSOX'ta öğrenci tablosu](complex-data-model/_static/ssox-before-migration.png)

Önceki resimde `Student` tablo için şema gösterir. Ad alanları türü `nvarchar(MAX)`var. Bu öğreticide bir geçiş oluşturulduğunda ve daha `nvarchar(50)` sonra uygulandığında, ad alanları dize uzunluk özniteliklerinin bir sonucu olarak olur.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

SQLite aracınızda, tablonun sütun tanımlarını inceleyin. `Student` Ad alanları türü `Text`var. İlk ad alanının çağrıldığına `FirstMidName`dikkat edin. Sonraki bölümde, bu sütunun adını ' `FirstName`da ' olarak değiştirirsiniz.

---

### <a name="the-column-attribute"></a>Sütun özniteliği

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

Öznitelikler, sınıfların ve özelliklerin veritabanına nasıl eşlenebileceğini denetleyebilir. `Student` Modelde, `Column` özellik veritabanındaki "FirstName" ile `FirstMidName` özelliğin adını eşlemek için kullanılır.

Veritabanı oluşturulduğunda, modeldeki özellik adları sütun adları için `Column` (öznitelik kullanıldığı durumlar hariç) kullanılır. Alan `Student` da `FirstMidName` bir göbek adı içerebilir, çünkü model ilk ad alanı için kullanır.

`[Column]` Öznitelik ile, `Student.FirstMidName` veri modeli nde `FirstName` `Student` tablonun sütununa eşler. Öznitelik eklenmesi `Column` modeli destekleyen `SchoolContext`değiştirir. Artık veritabanını `SchoolContext` destekleyen model eşleşmez. Bu tutarsızlık daha sonra bu öğreticide bir geçiş eklenerek çözülecektir.

### <a name="the-required-attribute"></a>Gerekli öznitelik

```csharp
[Required]
```

Öznitelik `Required` ad özelliklerini gerekli alanları yapar. Öznitelik `Required` değer türleri (örneğin, `DateTime`, `int`, ve). `double`gibi boşolmayan türleri için gerekli değildir. Null olamaz türleri otomatik olarak gerekli alanlar olarak kabul edilir.

Öznitelik, `Required` uygulanacak olması `MinimumLength` `MinimumLength` için kullanılmalıdır.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

`MinimumLength`ve `Required` doğrulamayı karşılamak için beyaz uzaya izin verin. Dize `RegularExpression` üzerinde tam denetim için özniteliği kullanın.

### <a name="the-display-attribute"></a>Görüntü özniteliği

```csharp
[Display(Name = "Last Name")]
```

Öznitelik, `Display` metin kutularıiçin alt yazının "Ad", "Soyadı", "Tam Ad" ve "Kayıt Tarihi" olması gerektiğini belirtir. Varsayılan alt yazılarda sözcükleri bölen boşluk yoktu, örneğin "Soyadı" gibi.

### <a name="create-a-migration"></a>Geçiş oluşturma

Uygulamayı çalıştırın ve Öğrenciler sayfasına gidin. Bir özel durum atılır. Öznitelik, `[Column]` EF'nin adlı `FirstName`bir sütun bulmayı beklemesine neden olur, ancak veritabanındaki sütun adı hala. `FirstMidName`

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Hata iletisi aşağıdaki örneğe benzer:

```
SqlException: Invalid column name 'FirstName'.
```

* PMC'de, yeni bir geçiş oluşturmak ve veritabanını güncelleştirmek için aşağıdaki komutları girin:

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  Bu komutlardan ilki aşağıdaki uyarı iletisini oluşturur:

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  Ad alanları artık 50 karakterle sınırlı olduğundan uyarı oluşturulur. Veritabanındaki bir ad 50'den fazla karaktere sahipse, 51'den sonkaraktere kadar olan karakter kaybolur.

* SSOX'ta Öğrenci tablosunu açın:

  ![Göçlerden sonra SSOX'ta öğrenci tablosu](complex-data-model/_static/ssox-after-migration.png)

  Geçiş uygulanmadan önce, ad sütunları [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)türündeydi. Ad sütunları `nvarchar(50)`şimdi. Sütun adı ' `FirstMidName` ndan `FirstName`' a değiştirildi

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Hata iletisi aşağıdaki örneğe benzer:

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* Proje klasöründe bir komut penceresi açın. Yeni bir geçiş oluşturmak ve veritabanını güncelleştirmek için aşağıdaki komutları girin:

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  Veritabanı güncelleştirme komutu aşağıdaki örnek gibi bir hata görüntüler:

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

Bu öğretici için, bu hatayı aşmanın yolu ilk geçişi silmek ve yeniden oluşturmaktır. Daha fazla bilgi için, [geçişler öğretici](xref:data/ef-rp/migrations)üst kısmında SQLite uyarı notu bakın.

* *Geçişler* klasörünü silin.
* Veritabanını bırakmak, yeni bir ilk geçiş oluşturmak ve geçişuygulamak için aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* SQLite aracınızdaki Öğrenci tablosunu inceleyin. FirstMidName olan sütun artık FirstName'dir.

---

* Uygulamayı çalıştırın ve Öğrenciler sayfasına gidin.
* Tarihlerle birlikte zaman girişi nin veya görüntülenmediğini unutmayın.
* **Yeni Oluştur'u**seçin ve 50 karakterden uzun bir ad girmeyi deneyin.

> [!Note]
> Aşağıdaki bölümlerde, uygulamayı bazı aşamalarda oluşturmak derleyici hataları oluşturur. Yönergeler, uygulamanın ne zaman oluşturacağını belirtir.

## <a name="the-instructor-entity"></a>Eğitmen Varlık

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

Aşağıdaki kodile *Modeller/Instructor.cs* oluşturun:

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

Birden çok öznitelik tek satırda olabilir. Öznitelikler `HireDate` aşağıdaki gibi yazılabilir:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a>Gezinme özellikleri

Ve `CourseAssignments` `OfficeAssignment` özellikleri navigasyon özellikleridir.

Bir eğitmen herhangi bir sayıda `CourseAssignments` ders verebilir, bu nedenle bir koleksiyon olarak tanımlanır.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Bir eğitmen in en fazla bir `OfficeAssignment` ofisi olabilir, bu nedenle özellik tek `OfficeAssignment` bir varlık tutar. `OfficeAssignment`ofis atanmamışsa geçersizdir.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a>OfficeAssignment tüzel kişiliği

![OfficeAssignment tüzel kişiliği](complex-data-model/_static/officeassignment-entity.png)

Aşağıdaki kodla *Modeller/OfficeAssignment.cs* oluşturun:

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Anahtar özniteliği

Özellik `[Key]` adı classnameID veya ID dışında bir şey olduğunda öznitelik birincil anahtar (PK) olarak bir özellik tanımlamak için kullanılır.

Varlıklarla varlıklar arasında `Instructor` `OfficeAssignment` bire bir ya da bir ilişki var. Bir ofis ataması yalnızca atandığı eğitmenle ilgili olarak bulunur. `OfficeAssignment` PK aynı zamanda `Instructor` varlık için yabancı anahtarıdır (FK).

EF Core, kimlik veya `InstructorID` classnameID `OfficeAssignment` adlandırma `InstructorID` kuralını izlemediği için PK'yı otomatik olarak tanıyamaz. Bu nedenle, `Key` öznitelik PK olarak tanımlamak `InstructorID` için kullanılır:

```csharp
[Key]
public int InstructorID { get; set; }
```

Varsayılan olarak, SÜTUN tanımlayıcı bir ilişki için olduğundan, EF Core anahtarı veritabanı oluşturulmayan olarak ele alır.

### <a name="the-instructor-navigation-property"></a>Eğitmen navigasyon özelliği

Belirli `Instructor.OfficeAssignment` bir eğitmen için bir `OfficeAssignment` satır olmayabilir, çünkü gezinti özelliği geçersiz olabilir. Bir eğitmenin ofis görevi olmayabilir.

Yabancı `OfficeAssignment.Instructor` anahtar `InstructorID` türü , nullable olmayan bir `int`değer türü olduğundan navigasyon özelliği her zaman bir eğitmen varlık olacaktır. Bir ofis ödevi bir eğitmen olmadan var olamaz.

Bir `Instructor` varlığın ilgili `OfficeAssignment` bir varlığı varsa, her varlığın gezinti özelliğinde diğerine bir göndermesi vardır.

## <a name="the-course-entity"></a>Kurs Varlığı

![Kurs varlığı](complex-data-model/_static/course-entity.png)

*Modelleri/Course.cs'yi* aşağıdaki kodla güncelleyin:

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

Varlığın `Course` yabancı bir anahtarı (FK) özelliği `DepartmentID`vardır. `DepartmentID`ilgili `Department` varlığa işaret ediyor. Varlığın `Course` bir `Department` gezinti özelliği vardır.

EF Core, model ilgili bir varlık için bir gezinti özelliği ne zaman bir veri modeli için yabancı bir anahtar özelliği gerektirmez. EF Core, ihtiyaç duyulan her yerde veritabanında otomatik olarak FK'lar oluşturur. EF Core, otomatik olarak oluşturulan FK'lar için [gölge özellikleri](/ef/core/modeling/shadow-properties) oluşturur. Ancak, veri modeline FK'yı açıkça dahil etmek güncelleştirmeleri daha basit ve daha verimli hale getirebilir. Örneğin, FK özelliğinin `DepartmentID` dahil *olmadığı* bir model düşünün. Bir kurs varlığı, bir ders varlığının edinimi için getirildiğinde:

* Özellik `Department` açıkça yüklenmezse geçersizdir.
* Kurs varlığını güncelleştirmek `Department` için öncelikle varlığın getirilmiş olması gerekir.

FK özelliği `DepartmentID` veri modeline dahil edildiğinde, güncelleştirmeden `Department` önce varlığı almanıza gerek yoktur.

### <a name="the-databasegenerated-attribute"></a>DatabaseOluşturulan öznitelik

Öznitelik, `[DatabaseGenerated(DatabaseGeneratedOption.None)]` PK'nın veritabanı tarafından oluşturulmadı yerine uygulama tarafından sağlandığını belirtir.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Varsayılan olarak, EF Core PK değerlerinin veritabanı tarafından oluşturulduğunu varsayar. Veritabanı tarafından oluşturulan genellikle en iyi yaklaşımdır. Varlıklar `Course` için, kullanıcı PK'yı belirtir. Örneğin, matematik bölümü için 1000 serisi, İngilizce bölümü için 2000 serisi gibi bir ders numarası.

Öznitelik, `DatabaseGenerated` varsayılan değerleri oluşturmak için de kullanılabilir. Örneğin, veritabanı, bir satırın oluşturulduğu veya güncelleştirıldığı tarihi kaydetmek için otomatik olarak bir tarih alanı oluşturabilir. Daha fazla bilgi [için, oluşturulan özellikler](/ef/core/modeling/generated-properties)e bakın.

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve navigasyon özellikleri

`Course` Varlıktaki yabancı anahtar (FK) özellikleri ve gezinme özellikleri aşağıdaki ilişkileri yansıtır:

Bir bölüme bir kurs atanır, `DepartmentID` bu yüzden `Department` bir FK ve navigasyon özelliği vardır.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Bir kursa herhangi bir sayıda öğrenci kaydolabilir, bu nedenle `Enrollments` navigasyon özelliği bir koleksiyondur:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Bir kurs birden çok eğitmen tarafından `CourseAssignments` öğretilebilir, bu nedenle navigasyon özelliği bir koleksiyondur:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment`daha [sonra](#many-to-many-relationships)açıklanır.

## <a name="the-department-entity"></a>Bölüm tüzel kişiliği

![Bölüm tüzel kişiliği](complex-data-model/_static/department-entity.png)

Aşağıdaki kodile *Modeller/Department.cs* oluşturun:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a>Sütun özniteliği

Daha önce `Column` öznitelik sütun adı eşlemi değiştirmek için kullanılmıştır. Varlığın `Department` kodunda, `Column` öznitelik SQL veri türü eşlemi değiştirmek için kullanılır. Sütun `Budget` veritabanında SQL Server para türü kullanılarak tanımlanır:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Sütun eşleme genellikle gerekli değildir. EF Core, özellik için CLR türüne göre uygun SQL Server veri türünü seçer. CLR `decimal` türü, SQL Server `decimal` türüyle eşleştirilir. `Budget`para birimi içindir ve para veri türü para birimi için daha uygundur.

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve navigasyon özellikleri

FK ve gezinme özellikleri aşağıdaki ilişkileri yansıtır:

* Bir bölümün bir yöneticisi olabilir veya olmayabilir.
* Yönetici her zaman bir eğitmendir. Bu `InstructorID` nedenle özellik `Instructor` varlık için FK olarak dahil edilir.

Gezinti özelliği adlandırılmış, `Administrator` `Instructor` ancak bir varlık tutar:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Önceki koddaki soru işareti (?) özelliğin geçersiz olduğunu belirtir.

Bir bölümün birçok dersi olabilir, bu nedenle Kurslar navigasyon özelliği vardır:

```csharp
public ICollection<Course> Courses { get; set; }
```

Geçersiz kılmaya göre, EF Core nullable OLMAYAN FKs ve çok-çok ilişkiler için basamaklı silme sağlar. Bu varsayılan davranış dairesel basamaklı silme kuralları neden olabilir. Dairesel basamaklı silme kuralları, geçiş eklendiğinde özel bir özel durum neden olur.

Örneğin, `Department.InstructorID` özellik nullable olarak tanımlanmışsa, EF Core basamaklı silme kuralını yapılandırırdı. Bu durumda, yönetici olarak atanan eğitmen silindiğinde bölüm silinir. Bu senaryoda, bir kısıtlama kuralı daha mantıklı olacaktır. Aşağıdaki [akıcı API](#fluent-api-alternative-to-attributes) bir kısıtlama kuralı ayarlar ve basamaklı silme devre dışı bırak.

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a>Kayıt varlığı

Kayıt kaydı, bir öğrencitarafından alınan bir ders için dir.

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

*Modelleri/Enrollment.cs'yi* aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve navigasyon özellikleri

FK özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

Kayıt kaydı tek bir kurs içindir, `CourseID` bu nedenle `Course` bir FK özelliği ve navigasyon özelliği vardır:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Kayıt kaydı bir öğrenci içindir, bu `StudentID` nedenle bir `Student` FK özelliği ve navigasyon özelliği vardır:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Çok-Çok İlişkiler

Varlıklar `Student` la `Course` çok arasında çok büyük bir ilişki var. Varlık, `Enrollment` veritabanında *yükü olan* çok-çok birleştirme tablosu olarak işlev görür. "Yük ile" `Enrollment` tablo (bu durumda, PK ve) `Grade`birleştirilmiş tablolar için FKs yanında ek veri içerdiği anlamına gelir.

Aşağıdaki resimde, bu ilişkilerin varlık diyagramında nasıl göründüğü gösterilmektedir. (Bu diyagram EF 6.x için [EF Güç Araçları](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) kullanılarak oluşturulmuştur. Diyagramı oluşturmak öğreticinin bir parçası değildir.)

![Öğrenci-Kurs birçok ilişki için çok](complex-data-model/_static/student-course.png)

Her ilişki satırının bir ucunda 1, diğerucunda bir yıldız (*) vardır ve bu da bir-çok ilişkisi gösterir.

`Enrollment` Tablo not bilgilerini içermiyorsa, yalnızca iki FK'yı (ve)`CourseID` `StudentID`içermesi gerekir. Yükü olmayan çok-çok birleştirme tablosu bazen saf birleştirme tablosu (PJT) olarak adlandırılır.

Ve `Instructor` `Course` varlıklar saf bir birleştirme tablosu nu kullanarak çok-çok ilişkisi vardır.

Not: EF 6.x, çok-çok ilişkiler için örtülü birleştirme tablolarını destekler, ancak EF Core desteklemez. Daha fazla bilgi [için, EF Core 2.0'daki çok-çok ilişkilere](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)bakın.

## <a name="the-courseassignment-entity"></a>Ders Atama sı

![Ders Atama varlığı](complex-data-model/_static/courseassignment-entity.png)

Aşağıdaki kodile *Modeller/DersAtama.cs* oluşturun:

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

Derslerden Derslere çok sayıda ilişki bir birleştirme tablosu gerektirir ve bu katılma tablosunun varlığı Ders Atamasi'dır.

![Öğretim-Kurs m:M](complex-data-model/_static/courseassignment.png)

Birleştirilmiş varlık `EntityName1EntityName2`adlamak yaygındır. Örneğin, Bu deseni kullanarak Eğitmen-Kurslar a)'ya katılma tablosu olacaktır. `CourseInstructor` Ancak, ilişkiyi açıklayan bir ad kullanmanızı öneririz.

Veri modelleri basit başlar ve büyür. Birleştirme tabloları nayük olmadan (PJTs) sık sık yükü içerecek şekilde gelişir. Açıklayıcı bir varlık adı ile başlayarak, birleştirme tablosu değiştiğinde adın değişmesi gerekmez. İdeal olarak, birleştirme varlığı iş alanında kendi doğal (muhtemelen tek kelime) adı olurdu. Örneğin, Kitaplar ve Müşteriler Derecelendirme adlı bir birleştirme varlığıyla bağlantılı olabilir. Eğitmen-to-Kurslar için çok-çok ilişki, `CourseAssignment` yerine `CourseInstructor`tercih edilir.

### <a name="composite-key"></a>Kompozit anahtar

İki FKs `CourseAssignment` (`InstructorID` `CourseID`ve ) birlikte benzersiz `CourseAssignment` tablonun her satırı tanımlar. `CourseAssignment`özel bir PK gerektirmez. Ve `InstructorID` `CourseID` özellikleri bileşik PK olarak işlev görür. BILEŞIK PK'ları EF Core'a belirtmenin tek yolu *akıcı API'dir.* Sonraki bölümde, bileşik PK'nın nasıl yapılandırılabildiğini gösterir.

Kompozit anahtar şunları sağlar:

* Bir kurs için birden fazla satıra izin verilir.
* Bir eğitmen için birden fazla satıra izin verilir.
* Aynı eğitmen ve kurs için birden fazla satıra izin verilmez.

Birleştirme `Enrollment` varlığı kendi PK'sını tanımlar, bu nedenle bu tür yinelemeler mümkündür. Bu tür yinelemeleri önlemek için:

* FK alanlarına benzersiz bir dizin ekleme veya
* 'ye `Enrollment` `CourseAssignment`benzer bir birincil bileşik anahtarla yapılandırın. Daha fazla bilgi için [Dizinler'e](/ef/core/modeling/indexes)bakın.

## <a name="update-the-database-context"></a>Veritabanı bağlamını güncelleştirme

*Verileri/SchoolContext.cs'yi* aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

Önceki kod yeni varlıkları ekler ve varlığın `CourseAssignment` bileşik PK'sını yapılandırır.

## <a name="fluent-api-alternative-to-attributes"></a>Özniteliklere akıcı API alternatifi

Önceki `OnModelCreating` koddaki yöntem, EF Core davranışını yapılandırmak için *akıcı API* kullanır. GENELLIKLE bir dizi yöntem çağrısını tek bir deyimde bir araya getirerek kullanıldığından API'ye "akıcı" denir. [Aşağıdaki kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) akıcı API bir örnektir:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

Bu öğreticide, akıcı API yalnızca özniteliklerle yapılabilen veritabanı eşlemeleri için kullanılır. Ancak, akıcı API, özniteliklerle yapılabilecek biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtebilir.

Akıcı API `MinimumLength` ile uygulanamıyor gibi bazı öznitelikler. `MinimumLength`şema değişmez, yalnızca minimum uzunluk doğrulama kuralı nı uygular.

Bazı geliştiriciler, varlık sınıflarını "temiz" tutabilmek için akıcı API'yi yalnızca kullanmayı tercih eder. Öznitelikler ve akıcı API karıştırılabilir. Yalnızca akıcı API (bileşik PK belirterek) ile yapılabilir bazı yapılandırmaları vardır. Yalnızca özniteliklerle yapılabilecek bazı yapılandırmalar`MinimumLength`vardır ( ). Akıcı API veya öznitelikleri kullanmak için önerilen uygulama:

* Bu iki yaklaşımdan birini seçin.
* Seçilen yaklaşımı mümkün olduğunca tutarlı bir şekilde kullanın.

Bu öğreticide kullanılan özniteliklerden bazıları şunlardır:

* Yalnızca doğrulama (örneğin, `MinimumLength`).
* Yalnızca EF Core yapılandırması `HasKey`(örneğin, ).
* Doğrulama ve EF Core yapılandırması `[StringLength(50)]`(örneğin, ).

Öznitelikler ve akıcı API hakkında daha fazla bilgi için yapılandırma [yöntemlerine](/ef/core/modeling/)bakın.

## <a name="entity-diagram"></a>Varlık diyagramı

Aşağıdaki resimde, EF Power Tools'un tamamlanan Okul modeli için oluşturduğu diyagram gösterilmektedir.

![Varlık diyagramı](complex-data-model/_static/diagram.png)

Önceki diyagram da gösterir:

* Birkaç bir-to-çok ilişki satırları \*(1 için).
* Varlıklar ve `Instructor` `OfficeAssignment` varlıklar arasındaki birden sıfıra veya bir ilişkisi satırına (1-0.1).
* Varlıklar ve `Instructor` `Department` varlıklar arasındaki sıfır veya bir-birden çok ilişki çizgisi (0..1 ile *) arasındaki ilişki çizgisi.

## <a name="seed-the-database"></a>Veritabanını tohumla

*Veri/DbInitializer.cs*kodu güncelleştirin:

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

Önceki kod, yeni varlıklar için tohum verileri sağlar. Bu kodun çoğu yeni varlık nesneleri oluşturur ve örnek verileri yükler. Örnek veriler sınama için kullanılır. Kaç-çok `Enrollments` birleştirme tablolarının tohumlanabildiğini görün. `CourseAssignments`

## <a name="add-a-migration"></a>Geçiş ekleme

Projeyi derleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

PMC'de aşağıdaki komutu çalıştırın.

```powershell
Add-Migration ComplexDataModel
```

Önceki komut, olası veri kaybı yla ilgili bir uyarı görüntüler.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

`database update` Komut çalıştırılırsa, aşağıdaki hata üretilir:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

Sonraki bölümde, bu hata hakkında ne yapacağınızı görürsünüz.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Geçiş ekler ve komutu `database update` çalıştırırsanız, aşağıdaki hata üretilir:

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

Sonraki bölümde, bu hatayı nasıl önleyebildiğinizi görürsünüz.

---

## <a name="apply-the-migration-or-drop-and-re-create"></a>Geçiş veya bırakma ve yeniden oluşturma uygulama

Artık varolan bir veritabanınız olduğuna göre, bu veritabanına nasıl değişiklik uygulayacağınızı düşünmeniz gerekir. Bu öğretici iki alternatif gösterir:

* [Veritabanını bırakın ve yeniden oluşturun.](#drop) SQLite kullanıyorsanız bu bölümü seçin.
* [Geçişi varolan veritabanına uygulayın.](#applyexisting) Bu bölümdeki talimatlar yalnızca SQL Server için çalışır, **SQLite için değil.** 

Her iki seçenek de SQL Server için çalışır. Uygulama-geçiş yöntemi daha karmaşık ve zaman alıcı olsa da, gerçek dünya, üretim ortamları için tercih edilen bir yaklaşımdır. 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a>Veritabanını bırakma ve yeniden oluşturma

SQL Server kullanıyorsanız ve aşağıdaki bölümde uygula-geçiş yaklaşımını yapmak istiyorsanız [bu bölümü atlayın.](#apply-the-migration)

EF Core'u yeni bir veritabanı oluşturmaya zorlamak için veritabanını bırakın ve güncelleştirin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Paket **Yöneticisi Konsolunda** (PMC) aşağıdaki komutu çalıştırın:

  ```powershell
  Drop-Database
  ```

* *Geçişler* klasörünü silin ve aşağıdaki komutu çalıştırın:

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Komut penceresini açın ve proje klasörüne gidin. Proje klasörü *ContosoUniversity.csproj* dosyasını içerir.

* Şu komutu çalıştırın:

  ```dotnetcli
  dotnet ef database drop --force
  ```

* *Geçişler* klasörünü silin ve aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Uygulamayı çalıştırın. Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemi çalıştırıyor. Yeni `DbInitializer.Initialize` veritabanını dolduruyor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Veritabanını SSOX'ta açın:

* SSOX daha önce açılmışsa, **Yenile** düğmesini tıklatın.
* **Tablolar** düğümlerini genişletin. Oluşturulan tablolar görüntülenir.

  ![SSOX tabloları](complex-data-model/_static/ssox-tables.png)

* Ders **Atama** tablosunu inceleyin:

  * **Ders Atama** tablosuna sağ tıklayın ve **Verileri Görüntüle'yi**seçin.
  * Ders **Atama** tablosunun veri içerdiğini doğrulayın.

  ![SSOX'ta Ders Atama verileri](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Veritabanını incelemek için SQLite aracınızı kullanın:

* Yeni tablolar ve sütunlar.
* Tablolarda tohumlanmış veriler, örneğin **Ders Atama** tablosu.

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a>Geçişi uygulayın

Bu bölüm isteğe bağlıdır. Bu adımlar yalnızca SQL Server LocalDB için çalışır ve yalnızca önceki [Açılan'ı](#drop) atlayıp veritabanı bölümünü yeniden oluşturursanız.

Geçişler varolan verilerle çalıştırıldığında, varolan verilerle memnun olmayan FK kısıtlamaları olabilir. Üretim verileriyle, varolan verileri geçirmek için adımlar atılmalıdır. Bu bölümde FK kısıtlama ihlalleri nin düzeltilmesi ne örnek verilmektedir. Yedekleme olmadan bu kod değişikliklerini yapmayın. Önceki Açılan'ı tamamlayıp veritabanı bölümünü yeniden oluşturduysanız bu kod değişikliklerini [yapmayın.](#drop)

*{timestamp}_ComplexDataModel.cs* dosyası aşağıdaki kodu içerir:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

Önceki kod `DepartmentID` `Course` tabloya nullable olmayan bir FK ekler. Önceki öğreticideki `Course`veritabanı, tablonun geçişler tarafından güncelleştirilemeyeceği için satırlar içerir.

Geçişin `ComplexDataModel` varolan verilerle çalışmasını sağlamak için:

* Yeni sütuna varsayılan`DepartmentID`değer vermek için kodu değiştirin.
* Varsayılan departman olarak hareket etmek için "Temp" adlı sahte bir departman oluşturun.

#### <a name="fix-the-foreign-key-constraints"></a>Yabancı anahtar kısıtlamalarını düzeltme

`ComplexDataModel` Geçiş sınıfında, yöntemi `Up` güncelleştirin:

* *{timestamp}_ComplexDataModel.cs* dosyasını açın.
* `DepartmentID` Sütunu tabloya ekleyen kod satırının `Course` dışına yorum yapın.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Aşağıdaki vurgulanan kodu ekleyin. Yeni kod `.CreateTable( name: "Department"` bloğun ardından gider:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

Önceki değişikliklerle, varolan `Course` satırlar `ComplexDataModel.Up` yöntem çalıştırdıktan sonra "Geçici" bölümüyle ilişkilendirilecektir.

Burada gösterilen durumu ele alma yolu bu öğretici için basitleştirilmiştir. Bir üretim uygulaması:

* Yeni `Department` satırlara satır `Department` ve ilgili `Course` satırlar eklemek için kod veya komut dosyaları ekleyin.
* "Temp" bölümünü veya varsayılan değeri `Course.DepartmentID`kullanma.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Paket **Yöneticisi Konsolunda** (PMC) aşağıdaki komutu çalıştırın:

  ```powershell
  Update-Database
  ```

`DbInitializer.Initialize` Yöntem yalnızca boş bir veritabanıyla çalışacak şekilde tasarlandığından, Öğrenci ve Kurs tablolarındaki tüm satırları silmek için SSOX'u kullanın. (Basamaklı silme Kayıt tablosunun ilgilenir.)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Visual Studio Code ile SQL Server LocalDB kullanıyorsanız aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet ef database update
  ```

---

Uygulamayı çalıştırın. Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemi çalıştırıyor. Yeni `DbInitializer.Initialize` veritabanını dolduruyor.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki iki öğretici, ilgili verilerin nasıl okunup güncelleştirilebildiğini gösterir.

> [!div class="step-by-step"]
> [Önceki öğretici](xref:data/ef-rp/migrations)
> [Sonraki öğretici](xref:data/ef-rp/read-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Önceki öğreticiler üç varlıktan oluşan temel bir veri modeli ile çalıştı. Bu eğitimde:

* Daha fazla varlık ve ilişki eklenir.
* Veri modeli biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirterek özelleştirilir.

Tamamlanan veri modelinin varlık sınıfları aşağıdaki resimde gösterilmiştir:

![Varlık diyagramı](complex-data-model/_static/diagram.png)

Çözemediğiniz sorunlarla karşılaştıysanız, tamamlanan [uygulamayı](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)indirin.

## <a name="customize-the-data-model-with-attributes"></a>Veri modelini özniteliklerle özelleştirme

Bu bölümde, veri modeli öznitelikleri kullanılarak özelleştirilmiştir.

### <a name="the-datatype-attribute"></a>DataType özniteliği

Öğrenci sayfaları şu anda kayıt tarihinin saatini görüntüler. Genellikle, tarih alanları yalnızca tarih değil, saat gösterir.

Aşağıdaki vurgulanan kod ile *Modelleri/Student.cs'yi* güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) özniteliği, veritabanı içsel türünden daha özel bir veri türü belirtir. Bu durumda tarih ve saat değil, yalnızca tarih görüntülenmelidir. [DataType Numaralandırma,](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) Tarih, Saat, Telefon Numarası, Para Birimi, E-posta Adresi gibi birçok veri türü sağlar. Öznitelik, `DataType` uygulamanın türe özgü özellikleri otomatik olarak sağlamasına da olanak tanır. Örneğin:

* Bağlantı `mailto:` otomatik olarak `DataType.EmailAddress`.
* Tarih seçici çoğu tarayıcıda sağlanır. `DataType.Date`

Öznitelik `DataType` HTML 5 `data-` (telaffuz veri tire) html 5 tarayıcılar tüketmek öznitelikleri yakar. Öznitelikler `DataType` doğrulama sağlamaz.

`DataType.Date`görüntülenen tarihin biçimini belirtmez. Varsayılan olarak, tarih alanı sunucunun [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)dayalı varsayılan biçimlerine göre görüntülenir.

Öznitelik, `DisplayFormat` tarih biçimini açıkça belirtmek için kullanılır:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Ayar, `ApplyFormatInEditMode` biçimlendirmenin edit UI'ye de uygulanması gerektiğini belirtir. Bazı alanlar kullanmamalıdır. `ApplyFormatInEditMode` Örneğin, para birimi simgesi genellikle bir edit metin kutusunda görüntülenmemelidir.

Öznitelik `DisplayFormat` kendisi tarafından kullanılabilir. Özniteliği öznitelik ile `DataType` `DisplayFormat` kullanmak genellikle iyi bir fikirdir. Öznitelik, `DataType` verilerin bir ekranda nasıl işlenirse, anlambilimini aktarıyor. Öznitelik, `DataType` aşağıdaki avantajlarda `DisplayFormat`bulunmayan avantajları sağlar:

* Tarayıcı HTML5 özelliklerini etkinleştirebilir. Örneğin, bir takvim denetimi, yerel duruma uygun para birimi simgesi, e-posta bağlantıları, istemci tarafı giriş doğrulaması vb. gösterin.
* Varsayılan olarak, tarayıcı verileri yerel eki göre doğru biçimi kullanarak işler.

Daha fazla bilgi [ \<için, Yardımcı yı etiketleme> girişine](xref:mvc/views/working-with-forms#the-input-tag-helper)bakın.

Uygulamayı çalıştırın. Öğrenci Dizini sayfasına gidin. Süreler artık görüntülenmiyor. `Student` Modeli kullanan her görünüm, tarihi zaman olmadan görüntüler.

![Tarihlerini saatsiz gösteren öğrenci dizini sayfası](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>StringLength özniteliği

Veri doğrulama kuralları ve doğrulama hatası iletileri öznitelikleri ile belirtilebilir. [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) özniteliği, veri alanında izin verilen en az ve en büyük karakter uzunluğunu belirtir. Öznitelik, `StringLength` istemci tarafı ve sunucu tarafı doğrulaması da sağlar. Minimum değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.

Modeli `Student` aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Önceki kod adları en fazla 50 karakterle sınırlar. Öznitelik, `StringLength` kullanıcının bir ad için beyaz alan girmesini engellemez. [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) özniteliği, girişe kısıtlamalar uygulamak için kullanılır. Örneğin, aşağıdaki kod ilk karakterin büyük harf, kalan karakterlerin ise alfabetik olmasını gerektirir:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Uygulamayı çalıştırın:

* Öğrenciler sayfasına gidin.
* **Yeni Oluştur'u**seçin ve 50 karakterden uzun bir ad girin.
* **Oluştur'u**seçin, istemci tarafı doğrulamabir hata iletisi gösterir.

![Dize uzunluğu hatalarını gösteren öğrenci dizini sayfası](complex-data-model/_static/string-length-errors.png)

**SQL Server Object Explorer'da** (SSOX), **Öğrenci** tablosunu çift tıklatarak Öğrenci tablosu tasarımcısını açın.

![Göçlerden önce SSOX'ta öğrenci tablosu](complex-data-model/_static/ssox-before-migration.png)

Önceki resimde `Student` tablo için şema gösterir. Geçişler DB'de çalıştırılamadığı için ad alanları türüne `nvarchar(MAX)` sahiptir. Geçişler daha sonra bu öğreticide çalıştırıldığında, ad alanları `nvarchar(50)`.

### <a name="the-column-attribute"></a>Sütun özniteliği

Öznitelikler, sınıfların ve özelliklerin veritabanına nasıl eşlenebileceğini denetleyebilir. Bu bölümde, `Column` özellik db "FirstName" `FirstMidName` için özelliğin adını eşlemek için kullanılır.

DB oluşturulduğunda, modeldeki özellik adları sütun adları için kullanılır `Column` (öznitelik kullanıldığı durumlar hariç).

Alan `Student` da `FirstMidName` bir göbek adı içerebilir, çünkü model ilk ad alanı için kullanır.

Aşağıdaki vurgulanan kod ile *Student.cs* dosyasını güncelleştirme:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Önceki değişiklikle, `Student.FirstMidName` uygulamadaki tablonun `FirstName` sütununa `Student` eşler.

Öznitelik eklenmesi `Column` modeli destekleyen `SchoolContext`değiştirir. Artık veritabanını `SchoolContext` destekleyen model eşleşmez. Uygulama geçişleri uygulamadan önce çalıştırılırsa, aşağıdaki özel durum oluşturulur:

```
SqlException: Invalid column name 'FirstName'.
```

DB'yi güncellemek için:

* Projeyi derleyin.
* Proje klasöründe bir komut penceresi açın. Yeni bir geçiş oluşturmak ve DB'yi güncelleştirmek için aşağıdaki komutları girin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

Komut `migrations add ColumnFirstName` aşağıdaki uyarı iletisini oluşturur:

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

Ad alanları artık 50 karakterle sınırlı olduğundan uyarı oluşturulur. DB'deki bir ismin 50'den fazla karakteri olsaydı, 51'den sonkaraktere kadar olan karakter kaybolur.

* Uygulamayı test etme.

SSOX'ta Öğrenci tablosunu açın:

![Göçlerden sonra SSOX'ta öğrenci tablosu](complex-data-model/_static/ssox-after-migration.png)

Geçiş uygulanmadan önce, ad sütunları [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)türündendi. Ad sütunları `nvarchar(50)`şimdi. Sütun adı ' `FirstMidName` ndan `FirstName`' a değiştirildi

> [!Note]
> Aşağıdaki bölümde, uygulamayı bazı aşamalarda oluşturmak derleyici hataları oluşturur. Yönergeler, uygulamanın ne zaman oluşturacağını belirtir.

## <a name="student-entity-update"></a>Öğrenci varlık güncelleştirmesi

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

*Modelleri/Student.cs'yi* aşağıdaki kodla güncelleyin:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Gerekli öznitelik

Öznitelik `Required` ad özelliklerini gerekli alanları yapar. Öznitelik, `Required` değer türleri`DateTime`(, , `int`, `double`vb.) gibi kullanışsız türler için gerekli değildir. Null olamaz türleri otomatik olarak gerekli alanlar olarak kabul edilir.

Öznitelik, `Required` öznitelikteki `StringLength` en az uzunluk parametresi ile değiştirilebilir:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Görüntü özniteliği

Öznitelik, `Display` metin kutularıiçin alt yazının "Ad", "Soyadı", "Tam Ad" ve "Kayıt Tarihi" olması gerektiğini belirtir. Varsayılan alt yazılarda sözcükleri bölen boşluk yoktu, örneğin "Soyadı" gibi.

### <a name="the-fullname-calculated-property"></a>FullName hesaplanan özellik

`FullName`diğer iki özelliği nkiçleme tarafından oluşturulan bir değeri döndüren hesaplanmış bir özelliktir. `FullName`ayarlanamazsa, yalnızca bir erişime sahip olur. Veritabanında `FullName` sütun oluşturulmaz.

## <a name="create-the-instructor-entity"></a>Eğitmen Varlığını Oluşturma

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

Aşağıdaki kodile *Modeller/Instructor.cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

Birden çok öznitelik tek satırda olabilir. Öznitelikler `HireDate` aşağıdaki gibi yazılabilir:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Ders Atamaları ve OfisAtama navigasyon özellikleri

Ve `CourseAssignments` `OfficeAssignment` özellikleri navigasyon özellikleridir.

Bir eğitmen herhangi bir sayıda `CourseAssignments` ders verebilir, bu nedenle bir koleksiyon olarak tanımlanır.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Bir gezinti özelliği birden çok varlık barındırAn:

* Girişlerin eklenebileceği, silinebileceği ve güncelleştirilebileceği bir liste türü olmalıdır.

Gezinme özelliği türleri şunlardır:

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

`ICollection<T>` Belirtilirse, EF Core `HashSet<T>` varsayılan olarak bir koleksiyon oluşturur.

Varlık, `CourseAssignment` çok-çok ilişkiler bölümünde açıklanmıştır.

Contoso Üniversitesi iş kuralları, bir eğitmenin en fazla bir ofis te sahip olabileceğini belirtir. Özellik `OfficeAssignment` tek `OfficeAssignment` bir varlık tutar. `OfficeAssignment`ofis atanmamışsa geçersizdir.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>OfficeAtama tüzel kişiliğini oluşturma

![OfficeAssignment tüzel kişiliği](complex-data-model/_static/officeassignment-entity.png)

Aşağıdaki kodla *Modeller/OfficeAssignment.cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Anahtar özniteliği

Özellik `[Key]` adı classnameID veya ID dışında bir şey olduğunda öznitelik birincil anahtar (PK) olarak bir özellik tanımlamak için kullanılır.

Varlıklarla varlıklar arasında `Instructor` `OfficeAssignment` bire bir ya da bir ilişki var. Bir ofis ataması yalnızca atandığı eğitmenle ilgili olarak bulunur. `OfficeAssignment` PK aynı zamanda `Instructor` varlık için yabancı anahtarıdır (FK). EF Core, `InstructorID` `OfficeAssignment` aşağıdakilerden dolayı PK olarak otomatik olarak tanıyamaz:

* `InstructorID`kimlik veya classnameID adlandırma kuralını izlemez.

Bu nedenle, `Key` öznitelik PK olarak tanımlamak `InstructorID` için kullanılır:

```csharp
[Key]
public int InstructorID { get; set; }
```

Varsayılan olarak, SÜTUN tanımlayıcı bir ilişki için olduğundan, EF Core anahtarı veritabanı oluşturulmayan olarak ele alır.

### <a name="the-instructor-navigation-property"></a>Eğitmen navigasyon özelliği

Varlığın `OfficeAssignment` `Instructor` gezinti özelliği geçersizdir, çünkü:

* Başvuru türleri (sınıflar gibi geçersizdir).
* Bir eğitmenin ofis görevi olmayabilir.

Varlığın `OfficeAssignment` nullable olmayan `Instructor` bir navigasyon özelliği vardır, çünkü:

* `InstructorID`hükümsüzdür.
* Bir ofis ödevi bir eğitmen olmadan var olamaz.

Bir `Instructor` varlığın ilgili `OfficeAssignment` bir varlığı varsa, her varlığın gezinti özelliğinde diğerine bir göndermesi vardır.

Öznitelik `[Required]` `Instructor` gezinti özelliğine uygulanabilir:

```csharp
[Required]
public Instructor Instructor { get; set; }
```

Önceki kod, ilgili bir eğitmen olması gerektiğini belirtir. `InstructorID` Yabancı anahtar (aynı zamanda PK) geçersiz olduğundan, önceki kod gereksizdir.

## <a name="modify-the-course-entity"></a>Kurs Varlığını Değiştir

![Kurs varlığı](complex-data-model/_static/course-entity.png)

*Modelleri/Course.cs'yi* aşağıdaki kodla güncelleyin:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Varlığın `Course` yabancı bir anahtarı (FK) özelliği `DepartmentID`vardır. `DepartmentID`ilgili `Department` varlığa işaret ediyor. Varlığın `Course` bir `Department` gezinti özelliği vardır.

MODEL ilgili bir varlık için bir gezinti özelliği olduğunda EF Core bir veri modeli için bir FK özelliği gerektirmez.

EF Core, ihtiyaç duyulan her yerde veritabanında otomatik olarak FK'lar oluşturur. EF Core, otomatik olarak oluşturulan FK'lar için [gölge özellikleri](/ef/core/modeling/shadow-properties) oluşturur. Veri modelinde FK olması güncellemeleri daha basit ve daha verimli hale getirebilir. Örneğin, FK özelliğinin `DepartmentID` dahil *olmadığı* bir model düşünün. Bir kurs varlığı, bir ders varlığının edinimi için getirildiğinde:

* Varlık `Department` açıkça yüklenmezse geçersizdir.
* Kurs varlığını güncelleştirmek `Department` için öncelikle varlığın getirilmiş olması gerekir.

FK özelliği `DepartmentID` veri modeline dahil edildiğinde, güncelleştirmeden `Department` önce varlığı almanıza gerek yoktur.

### <a name="the-databasegenerated-attribute"></a>DatabaseOluşturulan öznitelik

Öznitelik, `[DatabaseGenerated(DatabaseGeneratedOption.None)]` PK'nın veritabanı tarafından oluşturulmadı yerine uygulama tarafından sağlandığını belirtir.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Varsayılan olarak, EF Core PK değerlerinin DB tarafından oluşturulduğunu varsayar. DB oluşturulan PK değerleri genellikle en iyi yaklaşımdır. Varlıklar `Course` için, kullanıcı PK'yı belirtir. Örneğin, matematik bölümü için 1000 serisi, İngilizce bölümü için 2000 serisi gibi bir ders numarası.

Öznitelik, `DatabaseGenerated` varsayılan değerleri oluşturmak için de kullanılabilir. Örneğin, DB, bir satırın oluşturulduğu veya güncelleştirıldığı tarihi kaydetmek için otomatik olarak bir tarih alanı oluşturabilir. Daha fazla bilgi [için, oluşturulan özellikler](/ef/core/modeling/generated-properties)e bakın.

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve navigasyon özellikleri

`Course` Varlıktaki yabancı anahtar (FK) özellikleri ve gezinme özellikleri aşağıdaki ilişkileri yansıtır:

Bir bölüme bir kurs atanır, `DepartmentID` bu yüzden `Department` bir FK ve navigasyon özelliği vardır.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Bir kursa herhangi bir sayıda öğrenci kaydolabilir, bu nedenle `Enrollments` navigasyon özelliği bir koleksiyondur:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Bir kurs birden çok eğitmen tarafından `CourseAssignments` öğretilebilir, bu nedenle navigasyon özelliği bir koleksiyondur:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment`daha [sonra](#many-to-many-relationships)açıklanır.

## <a name="create-the-department-entity"></a>Bölüm tüzel kişiliğini oluşturma

![Bölüm tüzel kişiliği](complex-data-model/_static/department-entity.png)

Aşağıdaki kodile *Modeller/Department.cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Sütun özniteliği

Daha önce `Column` öznitelik sütun adı eşlemi değiştirmek için kullanılmıştır. Varlığın `Department` kodunda, `Column` öznitelik SQL veri türü eşlemi değiştirmek için kullanılır. Sütun, `Budget` DB'deki SQL Server para türü kullanılarak tanımlanır:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Sütun eşleme genellikle gerekli değildir. EF Core genellikle özellik için CLR türüne göre uygun SQL Server veri türünü seçer. CLR `decimal` türü, SQL Server `decimal` türüyle eşleştirilir. `Budget`para birimi içindir ve para veri türü para birimi için daha uygundur.

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve navigasyon özellikleri

FK ve gezinme özellikleri aşağıdaki ilişkileri yansıtır:

* Bir bölümün bir yöneticisi olabilir veya olmayabilir.
* Yönetici her zaman bir eğitmendir. Bu `InstructorID` nedenle özellik `Instructor` varlık için FK olarak dahil edilir.

Gezinti özelliği adlandırılmış, `Administrator` `Instructor` ancak bir varlık tutar:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Önceki koddaki soru işareti (?) özelliğin geçersiz olduğunu belirtir.

Bir bölümün birçok dersi olabilir, bu nedenle Kurslar navigasyon özelliği vardır:

```csharp
public ICollection<Course> Courses { get; set; }
```

Not: Sözleşmeye göre, EF Core nullable olmayan FK'lar ve çok-çok ilişkiler için basamaklı silme sağlar. Basamaklı silme dairesel basamaklı silme kurallarına neden olabilir. Dairesel basamaklı silme kuralları, geçiş eklendiğinde özel bir özel durum oluşturur.

Örneğin, `Department.InstructorID` özellik nullable olarak tanımlanmışsa:

* EF Core, eğitmen silindiğinde bölümü silmek için basamaklı silme kuralını yapılandırır.
* Eğitmen silindiğinde bölümün silinmesi amaçlanan davranış değildir.
* Aşağıdaki [akıcı API](#fluent-api-alternative-to-attributes) basamaklı yerine bir kısıtlama kuralı ayarlar.

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

Önceki kod bölüm-eğitmen ilişkisinde basamaklı silme devre dışı kalır.

## <a name="update-the-enrollment-entity"></a>Kayıt varlığını güncelleştirme

Kayıt kaydı, bir öğrencitarafından alınan bir ders için dir.

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

*Modelleri/Enrollment.cs'yi* aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve navigasyon özellikleri

FK özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

Kayıt kaydı tek bir kurs içindir, `CourseID` bu nedenle `Course` bir FK özelliği ve navigasyon özelliği vardır:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Kayıt kaydı bir öğrenci içindir, bu `StudentID` nedenle bir `Student` FK özelliği ve navigasyon özelliği vardır:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Çok-Çok İlişkiler

Varlıklar `Student` la `Course` çok arasında çok büyük bir ilişki var. Varlık, `Enrollment` veritabanında *yükü olan* çok-çok birleştirme tablosu olarak işlev görür. "Yük ile" `Enrollment` tablo (bu durumda, PK ve) `Grade`birleştirilmiş tablolar için FKs yanında ek veri içerdiği anlamına gelir.

Aşağıdaki resimde, bu ilişkilerin varlık diyagramında nasıl göründüğü gösterilmektedir. (Bu diyagram EF 6.x için [EF Güç Araçları](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) kullanılarak oluşturulmuştur. Diyagramı oluşturmak öğreticinin bir parçası değildir.)

![Öğrenci-Kurs birçok ilişki için çok](complex-data-model/_static/student-course.png)

Her ilişki satırının bir ucunda 1, diğerucunda bir yıldız (*) vardır ve bu da bir-çok ilişkisi gösterir.

`Enrollment` Tablo not bilgilerini içermiyorsa, yalnızca iki FK'yı (ve)`CourseID` `StudentID`içermesi gerekir. Yükü olmayan çok-çok birleştirme tablosu bazen saf birleştirme tablosu (PJT) olarak adlandırılır.

Ve `Instructor` `Course` varlıklar saf bir birleştirme tablosu nu kullanarak çok-çok ilişkisi vardır.

Not: EF 6.x, çok-çok ilişkiler için örtülü birleştirme tablolarını destekler, ancak EF Core desteklemez. Daha fazla bilgi [için, EF Core 2.0'daki çok-çok ilişkilere](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)bakın.

## <a name="the-courseassignment-entity"></a>Ders Atama sı

![Ders Atama varlığı](complex-data-model/_static/courseassignment-entity.png)

Aşağıdaki kodile *Modeller/DersAtama.cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>Eğitmen-Kurslar

![Öğretim-Kurs m:M](complex-data-model/_static/courseassignment.png)

Eğitmen-to-Kurslar çok-çok ilişki:

* Bir varlık kümesi tarafından temsil edilmesi gereken bir birleştirme tablosu gerektirir.
* Saf birleştirme tablosudur (yüksüz tablo).

Birleştirilmiş varlık `EntityName1EntityName2`adlamak yaygındır. Örneğin, Bu deseni kullanarak Eğitmen-Kurslara `CourseInstructor`katılma tablosu . Ancak, ilişkiyi açıklayan bir ad kullanmanızı öneririz.

Veri modelleri basit başlar ve büyür. Yük yok birleştirmeleri (PJT'ler) sık sık yükü içerecek şekilde gelişir. Açıklayıcı bir varlık adı ile başlayarak, birleştirme tablosu değiştiğinde adın değişmesi gerekmez. İdeal olarak, birleştirme varlığı iş alanında kendi doğal (muhtemelen tek kelime) adı olurdu. Örneğin, Kitaplar ve Müşteriler Derecelendirme adlı bir birleştirme varlığıyla bağlantılı olabilir. Eğitmen-to-Kurslar için çok-çok ilişki, `CourseAssignment` yerine `CourseInstructor`tercih edilir.

### <a name="composite-key"></a>Kompozit anahtar

FK'lar geçersiz değildir. İki FKs `CourseAssignment` (`InstructorID` `CourseID`ve ) birlikte benzersiz `CourseAssignment` tablonun her satırı tanımlar. `CourseAssignment`özel bir PK gerektirmez. Ve `InstructorID` `CourseID` özellikleri bileşik PK olarak işlev görür. BILEŞIK PK'ları EF Core'a belirtmenin tek yolu *akıcı API'dir.* Sonraki bölümde, bileşik PK'nın nasıl yapılandırılabildiğini gösterir.

Kompozit anahtar şunları sağlar:

* Bir kurs için birden fazla satıra izin verilir.
* Bir eğitmen için birden fazla satıra izin verilir.
* Aynı eğitmen ve kurs için birden fazla satıra izin verilmez.

Birleştirme `Enrollment` varlığı kendi PK'sını tanımlar, bu nedenle bu tür yinelemeler mümkündür. Bu tür yinelemeleri önlemek için:

* FK alanlarına benzersiz bir dizin ekleme veya
* 'ye `Enrollment` `CourseAssignment`benzer bir birincil bileşik anahtarla yapılandırın. Daha fazla bilgi için [Dizinler'e](/ef/core/modeling/indexes)bakın.

## <a name="update-the-db-context"></a>DB bağlamını güncelleştirme

*Data/SchoolContext.cs'ye*aşağıdaki vurgulanan kodu ekleyin:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Önceki kod yeni varlıkları ekler ve varlığın `CourseAssignment` bileşik PK'sını yapılandırır.

## <a name="fluent-api-alternative-to-attributes"></a>Özniteliklere akıcı API alternatifi

Önceki `OnModelCreating` koddaki yöntem, EF Core davranışını yapılandırmak için *akıcı API* kullanır. GENELLIKLE bir dizi yöntem çağrısını tek bir deyimde bir araya getirerek kullanıldığından API'ye "akıcı" denir. [Aşağıdaki kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) akıcı API bir örnektir:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

Bu öğreticide, akıcı API yalnızca özniteliklerle yapılamaz DB eşleme için kullanılır. Ancak, akıcı API, özniteliklerle yapılabilecek biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtebilir.

Akıcı API `MinimumLength` ile uygulanamıyor gibi bazı öznitelikler. `MinimumLength`şema değişmez, yalnızca minimum uzunluk doğrulama kuralı nı uygular.

Bazı geliştiriciler, varlık sınıflarını "temiz" tutabilmek için akıcı API'yi yalnızca kullanmayı tercih eder. Öznitelikler ve akıcı API karıştırılabilir. Yalnızca akıcı API (bileşik PK belirterek) ile yapılabilir bazı yapılandırmaları vardır. Yalnızca özniteliklerle yapılabilecek bazı yapılandırmalar`MinimumLength`vardır ( ). Akıcı API veya öznitelikleri kullanmak için önerilen uygulama:

* Bu iki yaklaşımdan birini seçin.
* Seçilen yaklaşımı mümkün olduğunca tutarlı bir şekilde kullanın.

Bu öğreticide kullanılan özniteliklerden bazıları şunlardır:

* Yalnızca doğrulama (örneğin, `MinimumLength`).
* Yalnızca EF Core yapılandırması `HasKey`(örneğin, ).
* Doğrulama ve EF Core yapılandırması `[StringLength(50)]`(örneğin, ).

Öznitelikler ve akıcı API hakkında daha fazla bilgi için yapılandırma [yöntemlerine](/ef/core/modeling/)bakın.

## <a name="entity-diagram-showing-relationships"></a>İlişkileri Gösteren Varlık Diyagramı

Aşağıdaki resimde, EF Power Tools'un tamamlanan Okul modeli için oluşturduğu diyagram gösterilmektedir.

![Varlık diyagramı](complex-data-model/_static/diagram.png)

Önceki diyagram da gösterir:

* Birkaç bir-to-çok ilişki satırları \*(1 için).
* Varlıklar ve `Instructor` `OfficeAssignment` varlıklar arasındaki birden sıfıra veya bir ilişkisi satırına (1-0.1).
* Varlıklar ve `Instructor` `Department` varlıklar arasındaki sıfır veya bir-birden çok ilişki çizgisi (0..1 ile *) arasındaki ilişki çizgisi.

## <a name="seed-the-db-with-test-data"></a>Test Verileri ile DB Tohum

*Veri/DbInitializer.cs*kodu güncelleştirin:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

Önceki kod, yeni varlıklar için tohum verileri sağlar. Bu kodun çoğu yeni varlık nesneleri oluşturur ve örnek verileri yükler. Örnek veriler sınama için kullanılır. Kaç-çok `Enrollments` birleştirme tablolarının tohumlanabildiğini görün. `CourseAssignments`

## <a name="add-a-migration"></a>Geçiş ekleme

Projeyi derleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

Önceki komut, olası veri kaybı yla ilgili bir uyarı görüntüler.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

`database update` Komut çalıştırılırsa, aşağıdaki hata üretilir:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>Geçişi uygulayın

Artık varolan bir veritabanınız olduğuna göre, gelecekteki değişiklikleri nasıl uygulayacağınızı düşünmeniz gerekir. Bu öğretici iki yaklaşım gösterir:

* [Veritabanını bırakma ve yeniden oluşturma](#drop)
* [Geçişi varolan veritabanına uygulayın.](#applyexisting) Bu yöntem daha karmaşık ve zaman alıcı olsa da, gerçek dünya, üretim ortamları için tercih edilen bir yaklaşımdır. **Not**: Bu, öğreticinin isteğe bağlı bir bölümüdür. Açılan adımları yapabilir ve yeniden oluşturabilir ve bu bölümü atlayabilirsiniz. Bu bölümdeki adımları izlemek istiyorsanız, bırakma ve yeniden oluşturma adımlarını yapmayın. 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>Veritabanını bırakma ve yeniden oluşturma

Güncelleştirilen `DbInitializer` kod, yeni varlıklar için tohum verileri ekler. EF Core'u yeni bir DB oluşturmaya zorlamak için DB'yi bırakın ve güncelleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Paket **Yöneticisi Konsolunda** (PMC) aşağıdaki komutu çalıştırın:

```powershell
Drop-Database
Update-Database
```

Yardım `Get-Help about_EntityFrameworkCore` bilgileri almak için PMC'den çalıştırın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Komut penceresini açın ve proje klasörüne gidin. Proje klasörü *Startup.cs* dosyasını içerir.

Komut penceresine aşağıdakileri girin:

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

Uygulamayı çalıştırın. Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemi çalıştırıyor. Yeni `DbInitializer.Initialize` DB'yi dolduruyor.

SSOX'ta DB'yi açın:

* SSOX daha önce açılmışsa, **Yenile** düğmesini tıklatın.
* **Tablolar** düğümlerini genişletin. Oluşturulan tablolar görüntülenir.

![SSOX tabloları](complex-data-model/_static/ssox-tables.png)

Ders **Atama** tablosunu inceleyin:

* **Ders Atama** tablosuna sağ tıklayın ve **Verileri Görüntüle'yi**seçin.
* Ders **Atama** tablosunun veri içerdiğini doğrulayın.

![SSOX'ta Ders Atama verileri](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>Geçişi varolan veritabanına uygulama

Bu bölüm isteğe bağlıdır. Bu adımlar yalnızca önceki Açılan'ı atlayıp veritabanı bölümünü [yeniden oluşturduğunuzda](#drop) çalışır.

Geçişler varolan verilerle çalıştırıldığında, varolan verilerle memnun olmayan FK kısıtlamaları olabilir. Üretim verileriyle, varolan verileri geçirmek için adımlar atılmalıdır. Bu bölümde FK kısıtlama ihlalleri nin düzeltilmesi ne örnek verilmektedir. Yedekleme olmadan bu kod değişikliklerini yapmayın. Önceki bölümü tamamlayıp veritabanını güncelleştirdiyseniz bu kod değişikliklerini yapmayın.

*{timestamp}_ComplexDataModel.cs* dosyası aşağıdaki kodu içerir:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

Önceki kod `DepartmentID` `Course` tabloya nullable olmayan bir FK ekler. Önceki öğreticideki DB, tablonun `Course`geçişler tarafından güncelleştirilemesin diye satırlar içerir.

Geçişin `ComplexDataModel` varolan verilerle çalışmasını sağlamak için:

* Yeni sütuna varsayılan`DepartmentID`değer vermek için kodu değiştirin.
* Varsayılan departman olarak hareket etmek için "Temp" adlı sahte bir departman oluşturun.

#### <a name="fix-the-foreign-key-constraints"></a>Yabancı anahtar kısıtlamalarını düzeltme

Sınıflar `Up` `ComplexDataModel` yöntemini güncelleştirin:

* *{timestamp}_ComplexDataModel.cs* dosyasını açın.
* `DepartmentID` Sütunu tabloya ekleyen kod satırının `Course` dışına yorum yapın.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Aşağıdaki vurgulanan kodu ekleyin. Yeni kod `.CreateTable( name: "Department"` bloğun ardından gider:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Önceki değişikliklerle, varolan `Course` satırlar `ComplexDataModel` `Up` yöntem çalıştırdıktan sonra "Geçici" bölümüyle ilişkilendirilecektir.

Bir üretim uygulaması:

* Yeni `Department` satırlara satır `Department` ve ilgili `Course` satırlar eklemek için kod veya komut dosyaları ekleyin.
* "Temp" bölümünü veya varsayılan değeri `Course.DepartmentID`kullanma.

Sonraki öğretici ilgili verileri kapsar.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğretici YouTube sürümü (Bölüm 1)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [Bu öğretici YouTube sürümü (Bölüm 2)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> [Önceki](xref:data/ef-rp/migrations)
> [Sonraki](xref:data/ef-rp/read-related-data)

::: moniker-end
