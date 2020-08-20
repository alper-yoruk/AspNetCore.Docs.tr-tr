---
title: Bölüm 5, Razor ASP.NET Core veri modelinde EF Core olan sayfalar
author: rick-anderson
description: RazorSayfaların 5. bölümü ve öğretici serisinin Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 3fab57df84e6902a8041940939c067da41f1674c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629736"
---
# <a name="part-5-no-locrazor-pages-with-ef-core-in-aspnet-core---data-model"></a>Bölüm 5, Razor ASP.NET Core veri modelinde EF Core olan sayfalar

, [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Önceki öğreticiler, üç varlıktan oluşan temel bir veri modeliyle çalışmıştır. Bu öğreticide:

* Daha fazla varlık ve ilişki eklenmiştir.
* Veri modeli biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirtilerek özelleştirilir.

Tamamlanan veri modeli aşağıdaki çizimde gösterilmiştir:

![Varlık diyagramı](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a>Öğrenci varlığı

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

*Modeller/öğrenci. cs* dosyasındaki kodu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

Yukarıdaki kod, bir `FullName` özelliği ekler ve var olan özelliklere aşağıdaki öznitelikleri ekler:

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a>FullName hesaplanmış özelliği

`FullName` , iki diğer özelliğin bitiştirerek oluşturulmuş bir değer döndüren hesaplanmış bir özelliktir. `FullName` ayarlanamaz, bu nedenle yalnızca bir get erişimcisi vardır. `FullName`Veritabanında hiçbir sütun oluşturulmaz.

### <a name="the-datatype-attribute"></a>DataType özniteliği

```csharp
[DataType(DataType.Date)]
```

Öğrenci kayıt tarihleri için, tüm sayfalar şu anda tarihle birlikte tarih ile görüntülenir, ancak yalnızca tarihin ilgili olması gerekir. Veri ek açıklaması özniteliklerini kullanarak, verileri gösteren her sayfada görüntü biçimini giderecek bir kod değişikliği yapabilirsiniz. 

[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) özniteliği, veritabanı iç türünden daha belirgin bir veri türünü belirtir. Bu durumda, tarih ve saat değil yalnızca tarih görüntülenmelidir. Veri [türü numaralandırması](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) , tarih, saat, PhoneNumber, para birimi, emaadresi vb. gibi birçok veri türü sağlar. `DataType` Özniteliği Ayrıca uygulamanın türe özgü özellikleri otomatik olarak sağlamasını da sağlayabilir. Örnek:

* `mailto:`Bağlantı için otomatik olarak oluşturulur `DataType.EmailAddress` .
* Tarih Seçici çoğu tarayıcıda için verilmiştir `DataType.Date` .

`DataType`ÖZNITELIK HTML 5 (bir `data-` veri Dash) özniteliklerini yayar. `DataType`Öznitelikler doğrulama sağlamaz.

### <a name="the-displayformat-attribute"></a>DisplayFormat özniteliği

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`DataType.Date` görüntülenen tarihin biçimini belirtmez. Varsayılan olarak, Tarih alanı sunucunun [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)öğesine göre varsayılan biçimlere göre görüntülenir.

`DisplayFormat`Öznitelik, tarih biçimini açıkça belirtmek için kullanılır. `ApplyFormatInEditMode`Ayar, biçimlendirmenin düzenleme kullanıcı arabirimine de uygulanacağını belirtir. Bazı alanlar kullanmamanız gerekir `ApplyFormatInEditMode` . Örneğin, para birimi simgesi genellikle bir düzenleme metin kutusunda gösterilmemelidir.

`DisplayFormat`Özniteliği kendi başına kullanılabilir. Özniteliği özniteliği ile kullanmak genellikle iyi bir fikirdir `DataType` `DisplayFormat` . `DataType`Özniteliği, bir ekranda nasıl işleneceğini değil, verilerin semantiğini alır. `DataType`Özniteliği, içinde kullanılamayan aşağıdaki avantajları sağlar `DisplayFormat` :

* Tarayıcı HTML5 özelliklerini etkinleştirebilir. Örneğin, bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını ve istemci tarafı giriş doğrulamasını gösterin.
* Varsayılan olarak tarayıcı, verileri yerel ayara göre doğru biçimi kullanarak işler.

Daha fazla bilgi için bkz. [ \<input> etiket Yardımcısı belgeleri](xref:mvc/views/working-with-forms#the-input-tag-helper).

### <a name="the-stringlength-attribute"></a>StringLength özniteliği

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

Veri doğrulama kuralları ve doğrulama hatası iletileri özniteliklerle belirtilebilir. [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) özniteliği, bir veri alanında izin verilen en düşük ve en fazla karakter uzunluğunu belirtir. Gösterilen kod, adları 50 karakterden fazla olmayacak şekilde sınırlar. En küçük dize uzunluğunu ayarlayan bir örnek [daha sonra](#the-required-attribute)gösterilmiştir.

`StringLength`Öznitelik Ayrıca istemci tarafı ve sunucu tarafı doğrulaması sağlar. En küçük değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.

Öznitelik, bir `StringLength` kullanıcının ad için boşluk girmesini engellemez. [Cevap içerisinde RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) özniteliği, girişe kısıtlamalar uygulamak için kullanılabilir. Örneğin, aşağıdaki kod ilk karakterin büyük küçük harf olmasını ve geri kalan karakterlerin alfabetik olmasını gerektirir:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**SQL Server Nesne Gezgini** (ssox) içinde **öğrenci** tablosuna çift tıklayarak öğrenci tablosu tasarımcısını açın.

![Geçişle önce SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-before-migration.png)

Önceki görüntüde tablo için şema gösterilmektedir `Student` . Ad alanlarının türü vardır `nvarchar(MAX)` . Bu öğreticide daha sonra bir geçiş oluşturulup uygulandığında, ad alanları `nvarchar(50)` dize uzunluğu özniteliklerinin bir sonucu olarak olur.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

SQLite aracınız içinde tablo için sütun tanımlarını inceleyin `Student` . Ad alanlarının türü vardır `Text` . İlk ad alanının çağrıldığından emin olun `FirstMidName` . Sonraki bölümde, bu sütunun adını olarak değiştirirsiniz `FirstName` .

---

### <a name="the-column-attribute"></a>Column özniteliği

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

Öznitelikler sınıfların ve özelliklerin veritabanına nasıl eşlenildiğini denetleyebilir. `Student`Modelinde `Column` öznitelik, `FirstMidName` özelliğin adını veritabanında "FirstName" olarak eşlemek için kullanılır.

Veritabanı oluşturulduğunda, modeldeki Özellik adları sütun adları için kullanılır ( `Column` özniteliği kullanıldığı durumlar dışında). `Student`Model, `FirstMidName` birinci ad alanı için kullanılır çünkü alan de bir orta ad içerebilir.

Özniteliği ile `[Column]` , `Student.FirstMidName` veri modelinde `FirstName` tablonun sütunuyla eşlenir `Student` . `Column`Özniteliği ekleme modeli, öğesini yedekleyen olarak değiştirir `SchoolContext` . ' İ destekleyen model `SchoolContext` artık veritabanıyla eşleşmez. Bu tutarsızlık, daha sonra bu öğreticide bir geçiş eklenerek çözümlenir.

### <a name="the-required-attribute"></a>Gerekli öznitelik

```csharp
[Required]
```

`Required`Özniteliği, ad özellikleri gerekli alanları yapar. `Required`Öznitelik, değer türleri (örneğin,, `DateTime` `int` ve) gibi null yapılamayan türler için gerekli değildir `double` . Null olmayan türler otomatik olarak gerekli alanlar olarak değerlendirilir.

`Required`Özniteliğinin zorlanmak üzere ile birlikte kullanılması gerekir `MinimumLength` `MinimumLength` .

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

`MinimumLength` ve `Required` doğrulamanın doğrulanmasını karşılamamak için boşluk. `RegularExpression`Dize üzerinde tam denetim için özniteliğini kullanın.

### <a name="the-display-attribute"></a>Display özniteliği

```csharp
[Display(Name = "Last Name")]
```

`Display`Öznitelik, metin kutuları için başlığın "ad", "soyadı", "tam ad" ve "kayıt tarihi" olması gerektiğini belirtir. Varsayılan açıklamalı alt yazıların sözcükleri bölen bir boşluk yoktu, örneğin "LastName".

### <a name="create-a-migration"></a>Geçiş oluşturma

Uygulamayı çalıştırın ve öğrenciler sayfasına gidin. Bir özel durum oluşturulur. `[Column]`Özniteliği, EF 'in adlı bir sütun bulmasını beklemesine neden olur `FirstName` , ancak veritabanındaki sütun adı hala kalır `FirstMidName` .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Hata iletisi aşağıdaki örneğe benzer:

```
SqlException: Invalid column name 'FirstName'.
```

* PMC 'de yeni bir geçiş oluşturmak ve veritabanını güncelleştirmek için aşağıdaki komutları girin:

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  Bu komutlardan ilki aşağıdaki uyarı iletisini oluşturur:

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  Ad alanları artık 50 karakterle sınırlı olduğundan uyarı oluşturulur. Veritabanındaki bir ad 50 karakterden fazlasına sahipse, son karakter 51 ' i kaybedersiniz.

* Öğrenci tablosunu SSOX içinde açın:

  ![Geçişlerde SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-after-migration.png)

  Geçiş uygulanmadan önce ad sütunları [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)türünde idi. Ad sütunları artık `nvarchar(50)` . Sütun adı `FirstMidName` olarak değiştirildi `FirstName` .

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

  Veritabanı güncelleştirme komutu aşağıdaki örnekte olduğu gibi bir hata görüntüler:

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

Bu öğreticide, bu hatayı geçmenin yolu ilk geçişi silmek ve yeniden oluşturmaktır. Daha fazla bilgi için, [geçiş öğreticisinin](xref:data/ef-rp/migrations)en üstündeki SQLite uyarı notuna bakın.

* *Geçişler* klasörünü silin.
* Veritabanını bırakmak, yeni bir başlangıç geçişi oluşturmak ve geçişi uygulamak için aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* SQLite araclarınızın öğrenci tablosunu inceleyin. FirstMidName sütunu artık FirstName.

---

* Uygulamayı çalıştırın ve öğrenciler sayfasına gidin.
* Saatin giriş veya tarih ile birlikte görüntülenmediğine dikkat edin.
* **Yeni oluştur**' u seçin ve 50 karakterden daha uzun bir ad girmeyi deneyin.

> [!Note]
> Aşağıdaki bölümlerde, uygulamanın bazı aşamalardan oluşturulması derleyici hataları oluşturur. Yönergeler uygulamanın ne zaman derbir olduğunu belirtir.

## <a name="the-instructor-entity"></a>Eğitmen varlığı

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

Aşağıdaki kodla *modeller/eğitmen. cs* oluşturun:

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

Birden çok öznitelik tek bir satırda olabilir. `HireDate`Öznitelikler aşağıdaki gibi yazılabilir:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a>Gezinti özellikleri

`CourseAssignments`Ve `OfficeAssignment` özellikleri gezinti özellikleridir.

Bir eğitmen herhangi bir sayıda kurs öğretebilir, bu nedenle `CourseAssignments` bir koleksiyon olarak tanımlanır.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Bir eğitmenin en fazla bir ofisi olabilir, bu nedenle `OfficeAssignment` özellik tek bir varlık içerir `OfficeAssignment` . `OfficeAssignment` hiçbir Office atanmamışsa null olur.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a>OfficeAssignment varlığı

![OfficeAssignment varlığı](complex-data-model/_static/officeassignment-entity.png)

Aşağıdaki kodla *modeller/OfficeAssignment. cs* oluşturun:

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Anahtar özniteliği

Öznitelik, özellik `[Key]` adı Classnameıd veya ID dışında bir şey olduğunda, bir özelliği birincil anahtar (PK) olarak tanımlamak için kullanılır.

Ve varlıkları arasında bire sıfır veya-bir ilişkisi vardır `Instructor` `OfficeAssignment` . Office ataması, atandığı eğitmenle ilişkili olarak yalnızca vardır. `OfficeAssignment`PK Ayrıca varlığa ait yabancı anahtardır (FK) `Instructor` .

EF Core `InstructorID` , `OfficeAssignment` `InstructorID` ID veya classnameıd adlandırma kuralını takip ettiğinden, ' ın ' ın ' i tarafından otomatik olarak tanıyamamaktadır. Bu nedenle, `Key` ÖZNITELIĞI PK olarak tanımlamak için kullanılır `InstructorID` :

```csharp
[Key]
public int InstructorID { get; set; }
```

Varsayılan olarak, EF Core, sütun tanımlayıcı bir ilişki için olduğundan, anahtarı veritabanı olmayan bir şekilde değerlendirir.

### <a name="the-instructor-navigation-property"></a>Eğitmen gezintisi özelliği

`Instructor.OfficeAssignment`Belirtilen bir eğitmen için bir satır olmadığı için, gezinti özelliği null olabilir `OfficeAssignment` . Bir eğitmenin Office ataması olmayabilir.

`OfficeAssignment.Instructor`Yabancı anahtar `InstructorID` türü `int` null yapılamayan bir değer türü olduğundan, gezinti özelliği her zaman bir eğitmen varlığına sahip olur. Bir Office ataması, bir eğitmen olmadan bulunamaz.

Bir `Instructor` varlık ilişkili bir varlığa sahip olduğunda `OfficeAssignment` , her varlığın gezinti özelliğinde diğer bir başvurusu vardır.

## <a name="the-course-entity"></a>Kurs varlığı

![Kurs varlığı](complex-data-model/_static/course-entity.png)

*Modelleri/kursu. cs* aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

`Course`Varlığın yabancı anahtar (FK) özelliği vardır `DepartmentID` . `DepartmentID` ilgili varlığa işaret eder `Department` . `Course`Varlığın bir `Department` gezinti özelliği vardır.

EF Core, modelin ilgili bir varlık için gezinti özelliği olduğunda bir veri modeli için yabancı anahtar özelliği gerektirmez. EF Core, gerektiği yerde otomatik olarak veritabanında FKs 'ler oluşturur. EF Core otomatik olarak oluşturulan FKs 'ler için [gölge Özellikler](/ef/core/modeling/shadow-properties) oluşturur. Ancak, doğrudan veri modelinde FK dahil edilmesi, güncelleştirmelerin daha basit ve daha verimli olmasını sağlayabilir. Örneğin, FK özelliğinin `DepartmentID` dahil *olmadığı* bir model düşünün. Bir kurs varlığı düzenlemek üzere getirilirken:

* `Department`Açık bir şekilde yüklenmediyse özelliği null olur.
* Kurs varlığını güncelleştirmek için `Department` önce varlığın getirilmesi gerekir.

FK özelliği `DepartmentID` veri modeline dahil edildiğinde, `Department` bir güncelleştirmeden önce varlığı getirme gerekmez.

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated özniteliği

`[DatabaseGenerated(DatabaseGeneratedOption.None)]`Özniteliği, PK 'nin veritabanı tarafından oluşturulması yerine uygulama tarafından sağlandığını belirtir.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Varsayılan olarak, EF Core PK değerlerinin veritabanı tarafından oluşturulduğunu varsayar. Veritabanı tarafından oluşturulan genellikle en iyi yaklaşım vardır. `Course`Varlıklar için Kullanıcı PK 'yi belirtir. Örneğin, matematik departmanı için 1000 serisi, Ingilizce departmanı için 2000 serisi gibi bir kurs numarası.

`DatabaseGenerated`Öznitelik varsayılan değerler oluşturmak için de kullanılabilir. Örneğin, veritabanı bir satırın oluşturulduğu veya güncelleştirildiği tarihi kaydetmek için otomatik olarak bir tarih alanı oluşturabilir. Daha fazla bilgi için bkz. [üretilen Özellikler](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

Varlıktaki yabancı anahtar (FK) özellikleri ve gezinti özellikleri `Course` aşağıdaki ilişkileri yansıtır:

Bir kurs bir departmana atanır, bu nedenle bir `DepartmentID` FK ve bir `Department` gezinti özelliği vardır.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir, bu nedenle `Enrollments` gezinti özelliği bir koleksiyondur:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurs, birden fazla eğitmen tarafından tada olabilir, bu nedenle `CourseAssignments` gezinti özelliği bir koleksiyon olur:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment`[daha sonra](#many-to-many-relationships)açıklanmaktadır.

## <a name="the-department-entity"></a>Departman varlığı

![Bölüm varlığı](complex-data-model/_static/department-entity.png)

Aşağıdaki kodla *modeller/departman. cs* oluşturun:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a>Column özniteliği

Daha önce `Column` öznitelik, sütun adı eşlemesini değiştirmek için kullanıldı. `Department`Varlığın kodunda, `Column` özniteliği SQL veri türü eşlemesini değiştirmek için kullanılır. `Budget`Sütun, veritabanında SQL Server para türü kullanılarak tanımlanır:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Sütun eşlemesi genellikle gerekli değildir. EF Core, özelliğin CLR türüne göre uygun SQL Server veri türünü seçer. CLR `decimal` türü SQL Server bir `decimal` türe eşlenir. `Budget` para birimi için, para veri türü ise para birimi için daha uygundur.

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

FK ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

* Bir departman yönetici olabilir veya olmayabilir.
* Yönetici her zaman bir eğitmendir. Bu nedenle, `InstructorID` özelliği VARLıĞA FK olarak dahil edilir `Instructor` .

Gezinti özelliği adlandırılır `Administrator` ancak bir `Instructor` varlık barındırır:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Önceki koddaki soru işareti (?) özelliği null yapılabilir olduğunu belirtiyor.

Bir departmanın birçok kursu olabilir, bu nedenle bir kurs gezintisi özelliği vardır:

```csharp
public ICollection<Course> Courses { get; set; }
```

Kural gereği EF Core, null yapılamayan ve çok-çok ilişkiler için basamaklı silme imkanı sağlar. Bu varsayılan davranış, dairesel basamaklı silme kurallarına neden olabilir. Bir geçiş eklendiğinde dairesel basamaklı silme kuralları bir özel duruma neden olur.

Örneğin, `Department.InstructorID` özellik null yapılamayan olarak tanımlanmışsa EF Core basamaklı silme kuralı yapılandırır. Bu durumda, yönetici olarak atanan eğitmen silindiğinde departman silinir. Bu senaryoda kısıtlama kuralı daha anlamlı hale getirir. Aşağıdaki [Fluent API](#fluent-api-alternative-to-attributes) bir kısıtlama kuralı ayarlar ve art arda silmeyi devre dışı bırakır.

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a>Kayıt varlığı

Kayıt kaydı, tek bir öğrenci tarafından gerçekleştirilen bir kurs içindir.

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

*Modelleri/kaydı. cs* 'yi aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

FK özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

Kayıt kaydı tek bir kurs için olduğundan, `CourseID` FK özelliği ve bir `Course` gezinti özelliği vardır:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Kayıt kaydı bir öğrenci içindir, bu nedenle bir `StudentID` FK özelliği ve bir `Student` gezinti özelliği vardır:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Çoktan çoğa Ilişkiler

Ve varlıkları arasında çoktan çoğa bir ilişki vardır `Student` `Course` . `Enrollment`Varlık, veritabanında *Yük içeren* çoktan çoğa bir JOIN tablosu olarak çalışır. "Yükle", `Enrollment` tablonun birleştirilmiş tablolar Için FKs 'ler (Bu durumda, PK ve) gibi ek veriler içerdiği anlamına gelir `Grade` .

Aşağıdaki çizimde bu ilişkilerin bir varlık diyagramında nasıl göründüğünü gösterilmektedir. (Bu diyagram EF 6. x için [EF güç araçları](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) kullanılarak oluşturulmuştur. Diyagram oluşturmak öğreticinin bir parçası değildir.)

![Öğrenci-çok fazla ilişki](complex-data-model/_static/student-course.png)

Her ilişki satırında 1 bir sonda ve diğeri de bir yıldız işareti (*) bulunur. Bu, bire çok ilişkiyi belirtir.

Tablo, `Enrollment` sınıf bilgileri içermiyorsa, yalnızca Iki FKs ( `CourseID` ve) içermesi gerekir `StudentID` . Yük olmadan çoktan çoğa bir JOIN tablosu bazen saf JOIN tablosu (PJT) olarak adlandırılır.

`Instructor`Ve `Course` varlıklarının saf bir JOIN tablosu kullanılarak çoktan çoğa bir ilişkisi vardır.

Note: EF 6. x, çoktan çoğa ilişkiler için örtük birleştirmeyi destekler, ancak EF Core değildir. Daha fazla bilgi için [EF Core 2,0 ' de çoktan çoğa ilişkiler](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)bölümüne bakın.

## <a name="the-courseassignment-entity"></a>Courseatama varlığı

![Courseatama varlığı](complex-data-model/_static/courseassignment-entity.png)

Aşağıdaki kodla *modeller/Courseatama. cs* oluşturun:

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

Eğitmenden çok-çok ilişkisi için bir JOIN tablosu gerekir ve bu ekleme tablosu için varlık kurs atamasıdır.

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

Bir JOIN varlığına ad vermek yaygındır `EntityName1EntityName2` . Örneğin, bu model kullanılarak eğitmen-kurslar 'a katılması tablosu olacaktır `CourseInstructor` . Ancak, ilişkiyi açıklayan bir ad kullanmanızı öneririz.

Veri modelleri basit ve büyümeye başlar. Yük (PJTs) olmayan ekleme tabloları genellikle yükü içerecek şekilde gelişmektedir. Açıklayıcı bir varlık adıyla başlayarak, ekleme tablosu değiştiğinde adın değiştirilmesi gerekmez. İdeal olarak, JOIN varlığının iş etki alanında kendi doğal (muhtemelen tek bir kelime) adına sahip olması gerekir. Örneğin, kitaplar ve müşteriler, derecelendirmeler adlı bir JOIN varlığıyla bağlantı kurulabilir. Eğitmenin kursa çok-çok ilişkisi için `CourseAssignment` tercih edilir `CourseInstructor` .

### <a name="composite-key"></a>Bileşik anahtar

(Ve) içindeki iki FKs, `CourseAssignment` `InstructorID` `CourseID` tablonun her satırını benzersiz bir şekilde tanımlar `CourseAssignment` . `CourseAssignment` adanmış bir PK gerektirmez. `InstructorID`Ve `CourseID` özellikleri BILEŞIK bir PK olarak çalışır. EF Core bileşik PKs 'leri belirtmenin tek yolu *Fluent API*' dir. Sonraki bölümde, bileşik PK 'nin nasıl yapılandırılacağı gösterilmektedir.

Bileşik anahtar şunları sağlar:

* Tek bir kurs için birden çok satıra izin verilir.
* Birden çok satıra bir eğitmen için izin verilir.
* Aynı eğitmen ve kurs için birden çok satıra izin verilmez.

`Enrollment`JOIN varlığı kendı PK 'yi tanımlar, bu nedenle bu sıralamanın yinelemeleri mümkündür. Bu tür yinelemeleri engellemek için:

* FK alanlara benzersiz bir dizin ekleyin veya
* `Enrollment`İle benzer bir birincil bileşik anahtarla yapılandırın `CourseAssignment` . Daha fazla bilgi için bkz. [dizinler](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Veritabanı bağlamını güncelleştirme

*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

Yukarıdaki kod, yeni varlıkları ekler ve `CourseAssignment` varlığın BILEŞIK PK 'yi yapılandırır.

## <a name="fluent-api-alternative-to-attributes"></a>Tutarlı API 'nin özniteliklere alternatif

`OnModelCreating`Önceki koddaki yöntemi EF Core davranışını yapılandırmak için *Fluent API* kullanır. Genellikle tek bir bildirimde bir dizi yöntem çağrısı olan dize olarak kullanıldığından, API "akıcı" olarak adlandırılır. [Aşağıdaki kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) Fluent API bir örneğidir:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

Bu öğreticide, Fluent API yalnızca özniteliklerle yapılamadığını veritabanı eşlemesi için kullanılır. Ancak Fluent API, özniteliklerle yapılabilecek biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtebilir.

Gibi bazı öznitelikler `MinimumLength` Fluent API uygulanamaz. `MinimumLength` şemayı değiştirmez, yalnızca bir minimum uzunluk doğrulama kuralı uygular.

Bazı geliştiriciler, varlık sınıflarının "temiz" olmasını sağlamak için Fluent API özel olarak kullanmayı tercih eder. Öznitelikler ve Fluent API karışık olabilir. Yalnızca Fluent API (bileşik bir PK belirterek) yapılabilecek bazı konfigürasyonlar vardır. Yalnızca özniteliklerle () yapılabilecek bazı konfigürasyonlar vardır `MinimumLength` . Fluent API veya özniteliklerini kullanmak için önerilen uygulama:

* Bu iki yaklaşımdan birini seçin.
* Seçilen yaklaşımı mümkün olduğunca düzenli olarak kullanın.

Bu öğreticide kullanılan özniteliklerin bazıları için kullanılır:

* Yalnızca doğrulama (örneğin, `MinimumLength` ).
* Yalnızca yapılandırma EF Core (örneğin, `HasKey` ).
* Doğrulama ve EF Core yapılandırma (örneğin, `[StringLength(50)]` ).

Öznitelikler ile Fluent API hakkında daha fazla bilgi için bkz. [yapılandırma yöntemleri](/ef/core/modeling/).

## <a name="entity-diagram"></a>Varlık diyagramı

Aşağıdaki çizimde, tamamlanmış okul modeli için EF Power Tools 'un oluştura diyagramı gösterilmektedir.

![Varlık diyagramı](complex-data-model/_static/diagram.png)

Önceki diyagramda şunları gösterir:

* Birden çok çoktan çoğa ilişki satırı (1 ile \* ).
* Ve varlıkları arasında bire sıfır veya-bir ilişki çizgisi (1 ila 0.. 1) `Instructor` `OfficeAssignment` .
* Ve varlıkları arasında sıfır veya-bire çok ilişki çizgisi (0.. 1-*) `Instructor` `Department` .

## <a name="seed-the-database"></a>Veritabanını çekirdek

*Data/Dbınizer. cs*dosyasındaki kodu güncelleştirin:

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

Yukarıdaki kod, yeni varlıklar için tohum verileri sağlar. Bu kodun çoğu yeni varlık nesneleri oluşturur ve örnek verileri yükler. Örnek veriler test için kullanılır. `Enrollments` `CourseAssignments` Birden çok-çok JOIN tablosunun nasıl çalıştırılabilir olduğunu gösteren örnekler için bkz. ve.

## <a name="add-a-migration"></a>Geçiş Ekle

Projeyi derleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

PMC 'de aşağıdaki komutu çalıştırın.

```powershell
Add-Migration ComplexDataModel
```

Yukarıdaki komut, olası veri kaybı hakkında bir uyarı görüntüler.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

`database update`Komut çalıştırıldığında, aşağıdaki hata oluşturulur:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

Sonraki bölümde, bu hatayla ilgili ne yapılacağını görürsünüz.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Bir geçiş ekler ve `database update` komutu çalıştırırsanız, aşağıdaki hata üretilir:

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

Sonraki bölümde, bu hatanın nasıl önleneceğini görürsünüz.

---

## <a name="apply-the-migration-or-drop-and-re-create"></a>Geçişi uygulayın veya bırakıp yeniden oluşturun

Artık var olan bir veritabanınız olduğuna göre, değişikliklere nasıl uygulanacağını düşünmeniz gerekir. Bu öğreticide iki alternatif gösterilmektedir:

* [Veritabanını bırakıp yeniden oluşturun](#drop). SQLite kullanıyorsanız bu bölümü seçin.
* [Geçişi mevcut veritabanına uygulayın](#applyexisting). Bu bölümdeki yönergeler yalnızca SQL Server için geçerlidir, **SQLite için değildir**. 

Her iki seçenek de SQL Server için geçerlidir. Apply-Migration yöntemi daha karmaşıktır ve zaman alabilir. Bu, gerçek dünyada üretim ortamları için tercih edilen yaklaşımdır. 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a>Veritabanını bırakıp yeniden oluşturun

SQL Server kullanıyorsanız ve aşağıdaki bölümde uygulanacak geçiş yaklaşımını yapmak istiyorsanız [Bu bölümü atlayın](#apply-the-migration) .

Yeni bir veritabanı oluşturmak için EF Core zorlamak için veritabanını bırakıp güncelleştirin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:

  ```powershell
  Drop-Database
  ```

* *Geçişler* klasörünü silin, ardından aşağıdaki komutu çalıştırın:

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Bir komut penceresi açın ve proje klasörüne gidin. Proje klasörü *Contosouniversity. csproj* dosyasını içerir.

* Şu komutu çalıştırın:

  ```dotnetcli
  dotnet ef database drop --force
  ```

* *Geçişler* klasörünü silin, ardından aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Uygulamayı çalıştırın. Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemini çalıştırır. `DbInitializer.Initialize`Yeni veritabanını doldurur.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Veritabanını SSOX içinde açın:

* Daha önce SSOX açıldıysa **Yenile** düğmesine tıklayın.
* **Tables** düğümünü genişletin. Oluşturulan tablolar görüntülenir.

  ![SSOX içindeki tablolar](complex-data-model/_static/ssox-tables.png)

* **Courseatama** tablosunu inceleyin:

  * **Courseatama** tablosuna sağ tıklayın ve **verileri görüntüle**' yi seçin.
  * **Courseatama** tablosunun veri içerdiğini doğrulayın.

  ![SSOX 'te Courseatama verileri](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Veritabanını incelemek için SQLite aracınızı kullanın:

* Yeni tablolar ve sütunlar.
* Tablolardaki verileri, örneğin, **Courseatama** tablosu.

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a>Geçişi Uygula

Bu bölüm isteğe bağlıdır. Bu adımlar yalnızca SQL Server LocalDB için çalışır ve yalnızca önceki [bırakma ve veritabanını yeniden oluştur](#drop) bölümünü atladıktan sonra.

Geçişler mevcut verilerle çalıştırıldığında, mevcut verilerin karşılanmadığı FK kısıtlamalar olabilir. Üretim verileriyle, mevcut verilerin geçirilmesi için adımların alınması gerekir. Bu bölüm, FK kısıtlama ihlallerinin düzeltilmesiyle bir örnek sağlar. Bu kod değişikliklerini yedekleme olmadan yapmayın. Önceki [bırakmayı tamamlayıp veritabanını yeniden oluşturduktan sonra](#drop) Bu kod değişikliklerini yapmayın.

*{Timestamp} _ComplexDataModel. cs* dosyası aşağıdaki kodu içerir:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

Yukarıdaki kod, tabloya null yapılamayan bir `DepartmentID` FK ekler `Course` . Önceki öğreticideki veritabanı, içindeki satırları içerir `Course` , böylece tablo geçişler tarafından güncelleştirilemez.

`ComplexDataModel`Geçişin mevcut verilerle çalışmasını sağlamak için:

* Yeni sütuna () varsayılan değer vermek için kodu değiştirin `DepartmentID` .
* Varsayılan departman olarak davranacak "Temp" adlı sahte bir departman oluşturun.

#### <a name="fix-the-foreign-key-constraints"></a>Yabancı anahtar kısıtlamalarını çözme

`ComplexDataModel`Geçiş sınıfında, `Up` yöntemi güncelleştirin:

* *{Timestamp} _ComplexDataModel. cs* dosyasını açın.
* Sütunu tabloya ekleyen kod satırını açıklama `DepartmentID` olarak yapın `Course` .

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Aşağıdaki vurgulanmış kodu ekleyin. Yeni kod bloğundan sonra geçer `.CreateTable( name: "Department"` :

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

Önceki değişikliklerle, varolan satırlar, `Course` Yöntem çalıştıktan sonra "geçici" departmanıyla ilişkilendirilir `ComplexDataModel.Up` .

Burada gösterilen durumu işlemenin yolu, bu öğretici için basitleştirilmiştir. Bir üretim uygulaması şöyle olacaktır:

* `Department`Yeni satırlara satırlar ve ilgili satırlar eklemek için kod veya komut dosyaları ekleyin `Course` `Department` .
* İçin "geçici" Departmanı veya varsayılan değeri kullanmayın `Course.DepartmentID` .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:

  ```powershell
  Update-Database
  ```

`DbInitializer.Initialize`Yöntemi yalnızca boş bir veritabanıyla çalışacak şekilde tasarlandığından, öğrenci ve kurs tablolarındaki tüm satırları silmek IÇIN SSOX kullanın. (Cascade silme, kayıt tablosundan işlem gerçekleştirir.)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Visual Studio Code ile SQL Server LocalDB kullanıyorsanız şu komutu çalıştırın:

  ```dotnetcli
  dotnet ef database update
  ```

---

Uygulamayı çalıştırın. Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemini çalıştırır. `DbInitializer.Initialize`Yeni veritabanını doldurur.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki iki öğretici ilgili verilerin nasıl okunacağını ve güncelleştirilmesini gösterir.

> [!div class="step-by-step"]
> [Önceki öğretici](xref:data/ef-rp/migrations) 
>  [Sonraki öğretici](xref:data/ef-rp/read-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Önceki öğreticiler, üç varlıktan oluşan temel bir veri modeliyle çalışmıştır. Bu öğreticide:

* Daha fazla varlık ve ilişki eklenmiştir.
* Veri modeli biçimlendirme, doğrulama ve veritabanı eşleme kuralları belirtilerek özelleştirilir.

Tamamlanan veri modeli için varlık sınıfları aşağıdaki çizimde gösterilmiştir:

![Varlık diyagramı](complex-data-model/_static/diagram.png)

Çözemediğiniz sorunlarla karşılaşırsanız, [Tamamlanmış uygulamayı](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)indirin.

## <a name="customize-the-data-model-with-attributes"></a>Veri modelini özniteliklerle özelleştirme

Bu bölümde, veri modeli öznitelikler kullanılarak özelleştirilir.

### <a name="the-datatype-attribute"></a>DataType özniteliği

Öğrenci sayfaları Şu anda kayıt tarihinin saatini görüntüler. Genellikle, tarih alanları saati değil yalnızca tarihi gösterir.

*Modelleri/öğrenci. cs* 'yi aşağıdaki vurgulanmış kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) özniteliği, veritabanı iç türünden daha belirgin bir veri türünü belirtir. Bu durumda, tarih ve saat değil yalnızca tarih görüntülenmelidir. Veri [türü numaralandırması](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) , tarih, saat, PhoneNumber, para birimi, emaadresi vb. gibi birçok veri türü sağlar. `DataType` Özniteliği Ayrıca uygulamanın türe özgü özellikleri otomatik olarak sağlamasını da sağlayabilir. Örnek:

* `mailto:`Bağlantı için otomatik olarak oluşturulur `DataType.EmailAddress` .
* Tarih Seçici çoğu tarayıcıda için verilmiştir `DataType.Date` .

`DataType`ÖZNITELIĞI `data-` HTML 5 TARAYıCıLARıNıN kullandığı HTML 5 (bir veri Dash) özniteliklerini yayar. `DataType`Öznitelikler doğrulama sağlamaz.

`DataType.Date` görüntülenen tarihin biçimini belirtmez. Varsayılan olarak, Tarih alanı sunucunun [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)öğesine göre varsayılan biçimlere göre görüntülenir.

`DisplayFormat`Öznitelik, tarih biçimini açıkça belirtmek için kullanılır:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode`Ayar, biçimlendirmenin düzenleme kullanıcı arabirimine de uygulanacağını belirtir. Bazı alanlar kullanmamanız gerekir `ApplyFormatInEditMode` . Örneğin, para birimi simgesi genellikle bir düzenleme metin kutusunda gösterilmemelidir.

`DisplayFormat`Özniteliği kendi başına kullanılabilir. Özniteliği özniteliği ile kullanmak genellikle iyi bir fikirdir `DataType` `DisplayFormat` . `DataType`Özniteliği, bir ekranda nasıl işleneceğini değil, verilerin semantiğini alır. `DataType`Özniteliği, içinde kullanılamayan aşağıdaki avantajları sağlar `DisplayFormat` :

* Tarayıcı HTML5 özelliklerini etkinleştirebilir. Örneğin, bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını, istemci tarafı giriş doğrulamasını vb. göster
* Varsayılan olarak tarayıcı, verileri yerel ayara göre doğru biçimi kullanarak işler.

Daha fazla bilgi için bkz. [ \<input> etiket Yardımcısı belgeleri](xref:mvc/views/working-with-forms#the-input-tag-helper).

Uygulamayı çalıştırın. Öğrenciler dizin sayfasına gidin. Süreler artık görüntülenmiyor. Modeli kullanan her görünüm `Student` tarihi zaman içinde görüntüler.

![Öğrenciler Dizin sayfası tarihleri zamansız gösterme](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>StringLength özniteliği

Veri doğrulama kuralları ve doğrulama hatası iletileri özniteliklerle belirtilebilir. [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) özniteliği, bir veri alanında izin verilen en düşük ve en fazla karakter uzunluğunu belirtir. `StringLength`Öznitelik Ayrıca istemci tarafı ve sunucu tarafı doğrulaması sağlar. En küçük değerin veritabanı şeması üzerinde hiçbir etkisi yoktur.

`Student`Modeli aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Yukarıdaki kod, adları 50 karakterden fazla olmayacak şekilde sınırlandırır. Öznitelik, bir `StringLength` kullanıcının ad için boşluk girmesini engellemez. [Cevap içerisinde RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) özniteliği, girişe kısıtlamalar uygulamak için kullanılır. Örneğin, aşağıdaki kod ilk karakterin büyük küçük harf olmasını ve geri kalan karakterlerin alfabetik olmasını gerektirir:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

Uygulamayı çalıştırın: 

* Öğrenciler sayfasına gidin.
* **Yeni oluştur**' u seçin ve 50 karakterden daha uzun bir ad girin.
* **Oluştur**' u seçin, istemci tarafı doğrulama bir hata iletisi gösterir.

![Öğrenciler Dizin sayfası dize uzunluğu hatalarını gösteriyor](complex-data-model/_static/string-length-errors.png)

**SQL Server Nesne Gezgini** (ssox) içinde **öğrenci** tablosuna çift tıklayarak öğrenci tablosu tasarımcısını açın.

![Geçişle önce SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-before-migration.png)

Önceki görüntüde tablo için şema gösterilmektedir `Student` . `nvarchar(MAX)`Veritabanı üzerinde geçişler çalıştırılmadığından ad alanlarının türü var. Bu öğreticide daha sonra geçişler çalıştırıldığında, ad alanları olur `nvarchar(50)` .

### <a name="the-column-attribute"></a>Column özniteliği

Öznitelikler sınıfların ve özelliklerin veritabanına nasıl eşlenildiğini denetleyebilir. Bu bölümde, özniteliği, `Column` `FirstMidName` ÖZELLIĞIN adını DB 'Deki "FirstName" olarak eşlemek için kullanılır.

DB oluşturulduğunda modeldeki Özellik adları sütun adları için kullanılır ( `Column` özniteliği kullanıldığı durumlar dışında).

`Student`Model, `FirstMidName` birinci ad alanı için kullanılır çünkü alan de bir orta ad içerebilir.

*Student.cs* dosyasını aşağıdaki vurgulanmış kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Uygulamanın önceki değişikliği ile `Student.FirstMidName` `FirstName` tablonun sütunuyla eşlenir `Student` .

`Column`Özniteliği ekleme modeli, öğesini yedekleyen olarak değiştirir `SchoolContext` . ' İ destekleyen model `SchoolContext` artık veritabanıyla eşleşmez. Geçiş uygulamadan önce uygulama çalışıyorsa aşağıdaki özel durum oluşturulur:

```
SqlException: Invalid column name 'FirstName'.
```

DB 'yi güncelleştirmek için:

* Projeyi derleyin.
* Proje klasöründe bir komut penceresi açın. Yeni bir geçiş oluşturmak ve DB 'yi güncelleştirmek için aşağıdaki komutları girin:

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

`migrations add ColumnFirstName`Komut aşağıdaki uyarı iletisini oluşturur:

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

Ad alanları artık 50 karakterle sınırlı olduğundan uyarı oluşturulur. VERITABANıNDAKI bir ad 50 karakterden fazlasına sahipse, son karakter 51 ' i kaybedersiniz.

* Uygulamayı test etme.

Öğrenci tablosunu SSOX içinde açın:

![Geçişlerde SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-after-migration.png)

Geçiş uygulanmadan önce ad sütunları [nvarchar (max)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)türünde idi. Ad sütunları artık `nvarchar(50)` . Sütun adı `FirstMidName` olarak değiştirildi `FirstName` .

> [!Note]
> Aşağıdaki bölümde, uygulamanın bazı aşamalardan oluşturulması derleyici hataları oluşturur. Yönergeler uygulamanın ne zaman derbir olduğunu belirtir.

## <a name="student-entity-update"></a>Öğrenci varlık güncelleştirmesi

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

*Modelleri/öğrenci. cs* 'yi aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Gerekli öznitelik

`Required`Özniteliği, ad özellikleri gerekli alanları yapar. `Required`Öznitelik, değer türleri ( `DateTime` , `int` , vb.) gibi null yapılamayan türler için gerekli değildir `double` . Null olmayan türler otomatik olarak gerekli alanlar olarak değerlendirilir.

Öznitelik, `Required` özniteliğinde bir minimum length parametresiyle değiştirilebilir `StringLength` :

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Display özniteliği

`Display`Öznitelik, metin kutuları için başlığın "ad", "soyadı", "tam ad" ve "kayıt tarihi" olması gerektiğini belirtir. Varsayılan açıklamalı alt yazıların sözcükleri bölen bir boşluk yoktu, örneğin "LastName".

### <a name="the-fullname-calculated-property"></a>FullName hesaplanmış özelliği

`FullName` , iki diğer özelliğin bitiştirerek oluşturulmuş bir değer döndüren hesaplanmış bir özelliktir. `FullName` ayarlanamaz, yalnızca bir get erişimcisine sahip. `FullName`Veritabanında hiçbir sütun oluşturulmaz.

## <a name="create-the-instructor-entity"></a>Eğitmen varlığı oluşturma

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

Aşağıdaki kodla *modeller/eğitmen. cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

Birden çok öznitelik tek bir satırda olabilir. `HireDate`Öznitelikler aşağıdaki gibi yazılabilir:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Courseatamalar ve OfficeAssignment gezinti özellikleri

`CourseAssignments`Ve `OfficeAssignment` özellikleri gezinti özellikleridir.

Bir eğitmen herhangi bir sayıda kurs öğretebilir, bu nedenle `CourseAssignments` bir koleksiyon olarak tanımlanır.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Bir gezinti özelliği birden çok varlık tutuyorsa:

* Girişlerin eklenebileceği, silinebileceği ve güncelleştirilebileceği bir liste türü olmalıdır.

Gezinti özelliği türleri şunları içerir:

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

`ICollection<T>`Belirtilmişse, EF Core `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur.

`CourseAssignment`Varlık, çoktan çoğa ilişkilerin bölümünde açıklanmaktadır.

Contoso Üniversitesi iş kuralları, bir eğitmenin en fazla bir ofisiniz olabilir. `OfficeAssignment`Özelliği tek bir varlık içerir `OfficeAssignment` . `OfficeAssignment` hiçbir Office atanmamışsa null olur.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>OfficeAssignment varlığını oluşturma

![OfficeAssignment varlığı](complex-data-model/_static/officeassignment-entity.png)

Aşağıdaki kodla *modeller/OfficeAssignment. cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Anahtar özniteliği

Öznitelik, özellik `[Key]` adı Classnameıd veya ID dışında bir şey olduğunda, bir özelliği birincil anahtar (PK) olarak tanımlamak için kullanılır.

Ve varlıkları arasında bire sıfır veya-bir ilişkisi vardır `Instructor` `OfficeAssignment` . Office ataması, atandığı eğitmenle ilişkili olarak yalnızca vardır. `OfficeAssignment`PK Ayrıca varlığa ait yabancı anahtardır (FK) `Instructor` . EF Core, şu nedenle otomatik olarak tanıyamaz `InstructorID` `OfficeAssignment` :

* `InstructorID` ID veya Classnameıd adlandırma kuralını takip etmez.

Bu nedenle, `Key` ÖZNITELIĞI PK olarak tanımlamak için kullanılır `InstructorID` :

```csharp
[Key]
public int InstructorID { get; set; }
```

Varsayılan olarak, EF Core, sütun tanımlayıcı bir ilişki için olduğundan, anahtarı veritabanı olmayan bir şekilde değerlendirir.

### <a name="the-instructor-navigation-property"></a>Eğitmen gezintisi özelliği

`OfficeAssignment`Varlık için gezinti özelliği `Instructor` null yapılabilir çünkü:

* Başvuru türleri (örneğin, sınıflar Nullable).
* Bir eğitmenin Office ataması olmayabilir.

`OfficeAssignment`Varlık null atanamaz bir `Instructor` gezinti özelliğine sahip, çünkü:

* `InstructorID` null atanamaz.
* Bir Office ataması, bir eğitmen olmadan bulunamaz.

Bir `Instructor` varlık ilişkili bir varlığa sahip olduğunda `OfficeAssignment` , her varlığın gezinti özelliğinde diğer bir başvurusu vardır.

`[Required]`Öznitelik, `Instructor` gezinti özelliğine uygulanabilir:

```csharp
[Required]
public Instructor Instructor { get; set; }
```

Yukarıdaki kod, ilgili bir eğitmen olması gerektiğini belirtir. `InstructorID`Yabancı anahtar (aynı zamanda PK) null değer atanamaz olduğundan, yukarıdaki kod gereksizdir.

## <a name="modify-the-course-entity"></a>Kurs varlığını değiştirme

![Kurs varlığı](complex-data-model/_static/course-entity.png)

*Modelleri/kursu. cs* aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

`Course`Varlığın yabancı anahtar (FK) özelliği vardır `DepartmentID` . `DepartmentID` ilgili varlığa işaret eder `Department` . `Course`Varlığın bir `Department` gezinti özelliği vardır.

EF Core, modelin ilgili bir varlık için gezinti özelliği olduğunda bir veri modeli için FK özelliği gerektirmez.

EF Core, gerektiği yerde otomatik olarak veritabanında FKs 'ler oluşturur. EF Core otomatik olarak oluşturulan FKs 'ler için [gölge Özellikler](/ef/core/modeling/shadow-properties) oluşturur. FK 'in veri modelinde olması, güncelleştirmeleri daha basit ve daha verimli hale getirir. Örneğin, FK özelliğinin `DepartmentID` dahil *olmadığı* bir model düşünün. Bir kurs varlığı düzenlemek üzere getirilirken:

* `Department`Açık bir şekilde yüklenmediyse varlık null olur.
* Kurs varlığını güncelleştirmek için `Department` önce varlığın getirilmesi gerekir.

FK özelliği `DepartmentID` veri modeline dahil edildiğinde, `Department` bir güncelleştirmeden önce varlığı getirme gerekmez.

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated özniteliği

`[DatabaseGenerated(DatabaseGeneratedOption.None)]`Özniteliği, PK 'nin veritabanı tarafından oluşturulması yerine uygulama tarafından sağlandığını belirtir.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Varsayılan olarak, EF Core PK değerlerinin DB tarafından oluşturulduğunu varsayar. VERITABANı tarafından oluşturulan PK değerleri genellikle en iyi yaklaşımdır. `Course`Varlıklar için Kullanıcı PK 'yi belirtir. Örneğin, matematik departmanı için 1000 serisi, Ingilizce departmanı için 2000 serisi gibi bir kurs numarası.

`DatabaseGenerated`Öznitelik varsayılan değerler oluşturmak için de kullanılabilir. Örneğin, VERITABANı bir satırın oluşturulduğu veya güncelleştirildiği tarihi kaydetmek için otomatik olarak bir tarih alanı oluşturabilir. Daha fazla bilgi için bkz. [üretilen Özellikler](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

Varlıktaki yabancı anahtar (FK) özellikleri ve gezinti özellikleri `Course` aşağıdaki ilişkileri yansıtır:

Bir kurs bir departmana atanır, bu nedenle bir `DepartmentID` FK ve bir `Department` gezinti özelliği vardır.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir, bu nedenle `Enrollments` gezinti özelliği bir koleksiyondur:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurs, birden fazla eğitmen tarafından tada olabilir, bu nedenle `CourseAssignments` gezinti özelliği bir koleksiyon olur:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment`[daha sonra](#many-to-many-relationships)açıklanmaktadır.

## <a name="create-the-department-entity"></a>Departman varlığı oluşturma

![Bölüm varlığı](complex-data-model/_static/department-entity.png)

Aşağıdaki kodla *modeller/departman. cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Column özniteliği

Daha önce `Column` öznitelik, sütun adı eşlemesini değiştirmek için kullanıldı. `Department`Varlığın kodunda, `Column` özniteliği SQL veri türü eşlemesini değiştirmek için kullanılır. `Budget`Sütun, veritabanında SQL Server para türü kullanılarak tanımlanır:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Sütun eşlemesi genellikle gerekli değildir. EF Core genellikle özelliğin CLR türüne göre uygun SQL Server veri türünü seçer. CLR `decimal` türü SQL Server bir `decimal` türe eşlenir. `Budget` para birimi için, para veri türü ise para birimi için daha uygundur.

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

FK ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

* Bir departman yönetici olabilir veya olmayabilir.
* Yönetici her zaman bir eğitmendir. Bu nedenle, `InstructorID` özelliği VARLıĞA FK olarak dahil edilir `Instructor` .

Gezinti özelliği adlandırılır `Administrator` ancak bir `Instructor` varlık barındırır:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Önceki koddaki soru işareti (?) özelliği null yapılabilir olduğunu belirtiyor.

Bir departmanın birçok kursu olabilir, bu nedenle bir kurs gezintisi özelliği vardır:

```csharp
public ICollection<Course> Courses { get; set; }
```

Note: kurala göre EF Core, null yapılamayan ve çoktan çoğa ilişkiler için art arda silme imkanı sağlar. Basamaklı silme, dairesel basamaklı silme kurallarına neden olabilir. Döngüsel basamaklı silme kuralları, bir geçiş eklendiğinde özel duruma neden olur.

Örneğin, `Department.InstructorID` özellik null yapılamayan olarak tanımlanmışsa:

* EF Core, eğitmen silindiğinde departmanı silmek için bir basamakla silme kuralı yapılandırır.
* Eğitmen silindiğinde departmanı silmek amaçlanan davranış değildir.
* Aşağıdaki [Fluent API](#fluent-api-alternative-to-attributes) Cascade yerine bir kısıtlama kuralı ayarlar.

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

Yukarıdaki kod, departman-eğitmen ilişkisindeki basamaklı silmeyi devre dışı bırakır.

## <a name="update-the-enrollment-entity"></a>Kayıt varlığını güncelleştirme

Kayıt kaydı, tek bir öğrenci tarafından gerçekleştirilen bir kurs içindir.

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

*Modelleri/kaydı. cs* 'yi aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

FK özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

Kayıt kaydı tek bir kurs için olduğundan, `CourseID` FK özelliği ve bir `Course` gezinti özelliği vardır:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Kayıt kaydı bir öğrenci içindir, bu nedenle bir `StudentID` FK özelliği ve bir `Student` gezinti özelliği vardır:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Çoktan çoğa Ilişkiler

Ve varlıkları arasında çoktan çoğa bir ilişki vardır `Student` `Course` . `Enrollment`Varlık, veritabanında *Yük içeren* çoktan çoğa bir JOIN tablosu olarak çalışır. "Yükle", `Enrollment` tablonun birleştirilmiş tablolar Için FKs 'ler (Bu durumda, PK ve) gibi ek veriler içerdiği anlamına gelir `Grade` .

Aşağıdaki çizimde bu ilişkilerin bir varlık diyagramında nasıl göründüğünü gösterilmektedir. (Bu diyagram EF 6. x için [EF güç araçları](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) kullanılarak oluşturulmuştur. Diyagram oluşturmak öğreticinin bir parçası değildir.)

![Öğrenci-çok fazla ilişki](complex-data-model/_static/student-course.png)

Her ilişki satırında 1 bir sonda ve diğeri de bir yıldız işareti (*) bulunur. Bu, bire çok ilişkiyi belirtir.

Tablo, `Enrollment` sınıf bilgileri içermiyorsa, yalnızca Iki FKs ( `CourseID` ve) içermesi gerekir `StudentID` . Yük olmadan çoktan çoğa bir JOIN tablosu bazen saf JOIN tablosu (PJT) olarak adlandırılır.

`Instructor`Ve `Course` varlıklarının saf bir JOIN tablosu kullanılarak çoktan çoğa bir ilişkisi vardır.

Note: EF 6. x, çoktan çoğa ilişkiler için örtük birleştirmeyi destekler, ancak EF Core değildir. Daha fazla bilgi için [EF Core 2,0 ' de çoktan çoğa ilişkiler](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)bölümüne bakın.

## <a name="the-courseassignment-entity"></a>Courseatama varlığı

![Courseatama varlığı](complex-data-model/_static/courseassignment-entity.png)

Aşağıdaki kodla *modeller/Courseatama. cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>Eğitmenden kurslar

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

Eğitmenin kurslardan çok-çok ilişkisi:

* Bir varlık kümesiyle temsil etmelidir bir JOIN tablosu gerektirir.
* , Saf bir JOIN tablosu (yük içermeyen tablo).

Bir JOIN varlığına ad vermek yaygındır `EntityName1EntityName2` . Örneğin, bu model kullanılarak eğitmen-kurslar 'a katılması tablosu `CourseInstructor` . Ancak, ilişkiyi açıklayan bir ad kullanmanızı öneririz.

Veri modelleri basit ve büyümeye başlar. Yük yükü dahil olmak üzere genellikle yük-yük birleştirmeleri (PJTs) gelişmektedir. Açıklayıcı bir varlık adıyla başlayarak, ekleme tablosu değiştiğinde adın değiştirilmesi gerekmez. İdeal olarak, JOIN varlığının iş etki alanında kendi doğal (muhtemelen tek bir kelime) adına sahip olması gerekir. Örneğin, kitaplar ve müşteriler, derecelendirmeler adlı bir JOIN varlığıyla bağlantı kurulabilir. Eğitmenin kursa çok-çok ilişkisi için `CourseAssignment` tercih edilir `CourseInstructor` .

### <a name="composite-key"></a>Bileşik anahtar

FKs null değer atanamaz. (Ve) içindeki iki FKs, `CourseAssignment` `InstructorID` `CourseID` tablonun her satırını benzersiz bir şekilde tanımlar `CourseAssignment` . `CourseAssignment` adanmış bir PK gerektirmez. `InstructorID`Ve `CourseID` özellikleri BILEŞIK bir PK olarak çalışır. EF Core bileşik PKs 'leri belirtmenin tek yolu *Fluent API*' dir. Sonraki bölümde, bileşik PK 'nin nasıl yapılandırılacağı gösterilmektedir.

Bileşik anahtar şunları sağlar:

* Tek bir kurs için birden çok satıra izin verilir.
* Birden çok satıra bir eğitmen için izin verilir.
* Aynı eğitmen ve kurs için birden çok satıra izin verilmez.

`Enrollment`JOIN varlığı kendı PK 'yi tanımlar, bu nedenle bu sıralamanın yinelemeleri mümkündür. Bu tür yinelemeleri engellemek için:

* FK alanlara benzersiz bir dizin ekleyin veya
* `Enrollment`İle benzer bir birincil bileşik anahtarla yapılandırın `CourseAssignment` . Daha fazla bilgi için bkz. [dizinler](/ef/core/modeling/indexes).

## <a name="update-the-db-context"></a>DB bağlamını güncelleştirme

*Data/SchoolContext. cs*' ye aşağıdaki vurgulanmış kodu ekleyin:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Yukarıdaki kod, yeni varlıkları ekler ve `CourseAssignment` varlığın BILEŞIK PK 'yi yapılandırır.

## <a name="fluent-api-alternative-to-attributes"></a>Tutarlı API 'nin özniteliklere alternatif

`OnModelCreating`Önceki koddaki yöntemi EF Core davranışını yapılandırmak için *Fluent API* kullanır. Genellikle tek bir bildirimde bir dizi yöntem çağrısı olan dize olarak kullanıldığından, API "akıcı" olarak adlandırılır. [Aşağıdaki kod](/ef/core/modeling/#use-fluent-api-to-configure-a-model) Fluent API bir örneğidir:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

Bu öğreticide, Fluent API yalnızca özniteliklerle yapılamadığını DB eşlemesi için kullanılır. Ancak Fluent API, özniteliklerle yapılabilecek biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtebilir.

Gibi bazı öznitelikler `MinimumLength` Fluent API uygulanamaz. `MinimumLength` şemayı değiştirmez, yalnızca bir minimum uzunluk doğrulama kuralı uygular.

Bazı geliştiriciler, varlık sınıflarının "temiz" olmasını sağlamak için Fluent API özel olarak kullanmayı tercih eder. Öznitelikler ve Fluent API karışık olabilir. Yalnızca Fluent API (bileşik bir PK belirterek) yapılabilecek bazı konfigürasyonlar vardır. Yalnızca özniteliklerle () yapılabilecek bazı konfigürasyonlar vardır `MinimumLength` . Fluent API veya özniteliklerini kullanmak için önerilen uygulama:

* Bu iki yaklaşımdan birini seçin.
* Seçilen yaklaşımı mümkün olduğunca düzenli olarak kullanın.

Bu öğreticide kullanılan özniteliklerin bazıları için kullanılır:

* Yalnızca doğrulama (örneğin, `MinimumLength` ).
* Yalnızca yapılandırma EF Core (örneğin, `HasKey` ).
* Doğrulama ve EF Core yapılandırma (örneğin, `[StringLength(50)]` ).

Öznitelikler ile Fluent API hakkında daha fazla bilgi için bkz. [yapılandırma yöntemleri](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Ilişkileri gösteren varlık diyagramı

Aşağıdaki çizimde, tamamlanmış okul modeli için EF Power Tools 'un oluştura diyagramı gösterilmektedir.

![Varlık diyagramı](complex-data-model/_static/diagram.png)

Önceki diyagramda şunları gösterir:

* Birden çok çoktan çoğa ilişki satırı (1 ile \* ).
* Ve varlıkları arasında bire sıfır veya-bir ilişki çizgisi (1 ila 0.. 1) `Instructor` `OfficeAssignment` .
* Ve varlıkları arasında sıfır veya-bire çok ilişki çizgisi (0.. 1-*) `Instructor` `Department` .

## <a name="seed-the-db-with-test-data"></a>VERITABANıNı test verileriyle çekirdek olarak

*Data/Dbınizer. cs*dosyasındaki kodu güncelleştirin:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

Yukarıdaki kod, yeni varlıklar için tohum verileri sağlar. Bu kodun çoğu yeni varlık nesneleri oluşturur ve örnek verileri yükler. Örnek veriler test için kullanılır. `Enrollments` `CourseAssignments` Birden çok-çok JOIN tablosunun nasıl çalıştırılabilir olduğunu gösteren örnekler için bkz. ve.

## <a name="add-a-migration"></a>Geçiş Ekle

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

Yukarıdaki komut, olası veri kaybı hakkında bir uyarı görüntüler.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

`database update`Komut çalıştırıldığında, aşağıdaki hata oluşturulur:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>Geçişi Uygula

Artık var olan bir veritabanınız olduğuna göre, bundan sonraki değişikliklere nasıl uygulanacağını düşünmeniz gerekir. Bu öğreticide iki yaklaşım gösterilmektedir:

* [Veritabanını bırakıp yeniden oluşturun](#drop)
* [Geçişi mevcut veritabanına uygulayın](#applyexisting). Bu yöntem daha karmaşıktır ve zaman alabilir. Bu, gerçek dünyada üretim ortamları için tercih edilen yaklaşımdır. **Note**: Bu, öğreticinin isteğe bağlı bir bölümüdür. Bırakma ve yeniden oluşturma adımlarını gerçekleştirebilir ve bu bölümü atlayabilirsiniz. Bu bölümdeki adımları izlemek isterseniz, bırakma ve yeniden oluşturma adımlarını yapmayın. 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>Veritabanını bırakıp yeniden oluşturun

Güncelleştirilmiş kod, `DbInitializer` Yeni varlıklar için tohum verileri ekler. Yeni bir VERITABANı oluşturmak için EF Core zorlamak için DB 'yi bırakıp güncelleştirin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:

```powershell
Drop-Database
Update-Database
```

`Get-Help about_EntityFrameworkCore`Yardım bilgileri almak IÇIN PMC 'den çalıştırın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Bir komut penceresi açın ve proje klasörüne gidin. Proje klasörü *Startup.cs* dosyasını içerir.

Komut penceresine şunu girin:

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

Uygulamayı çalıştırın. Uygulamayı çalıştırmak `DbInitializer.Initialize` yöntemini çalıştırır. `DbInitializer.Initialize`Yenı DB 'yi doldurur.

VERITABANıNı SSOX içinde açın:

* Daha önce SSOX açıldıysa **Yenile** düğmesine tıklayın.
* **Tables** düğümünü genişletin. Oluşturulan tablolar görüntülenir.

![SSOX içindeki tablolar](complex-data-model/_static/ssox-tables.png)

**Courseatama** tablosunu inceleyin:

* **Courseatama** tablosuna sağ tıklayın ve **verileri görüntüle**' yi seçin.
* **Courseatama** tablosunun veri içerdiğini doğrulayın.

![SSOX 'te Courseatama verileri](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>Geçişi mevcut veritabanına Uygula

Bu bölüm isteğe bağlıdır. Bu adımlar yalnızca önceki [bırakma ve veritabanını yeniden oluşturma](#drop) bölümünü atladıysanız çalışır.

Geçişler mevcut verilerle çalıştırıldığında, mevcut verilerin karşılanmadığı FK kısıtlamalar olabilir. Üretim verileriyle, mevcut verilerin geçirilmesi için adımların alınması gerekir. Bu bölüm, FK kısıtlama ihlallerinin düzeltilmesiyle bir örnek sağlar. Bu kod değişikliklerini yedekleme olmadan yapmayın. Önceki bölümü tamamlayıp veritabanını güncelleştirdiyseniz, bu kod değişikliklerini yapmayın.

*{Timestamp} _ComplexDataModel. cs* dosyası aşağıdaki kodu içerir:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

Yukarıdaki kod, tabloya null yapılamayan bir `DepartmentID` FK ekler `Course` . Önceki öğreticideki VERITABANı, içindeki satırları içerir `Course` , böylece tablo geçişler tarafından güncelleştirilemez.

`ComplexDataModel`Geçişin mevcut verilerle çalışmasını sağlamak için:

* Yeni sütuna () varsayılan değer vermek için kodu değiştirin `DepartmentID` .
* Varsayılan departman olarak davranacak "Temp" adlı sahte bir departman oluşturun.

#### <a name="fix-the-foreign-key-constraints"></a>Yabancı anahtar kısıtlamalarını çözme

`ComplexDataModel`Classes metodunu güncelleştirin `Up` :

* *{Timestamp} _ComplexDataModel. cs* dosyasını açın.
* Sütunu tabloya ekleyen kod satırını açıklama `DepartmentID` olarak yapın `Course` .

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Aşağıdaki vurgulanmış kodu ekleyin. Yeni kod bloğundan sonra geçer `.CreateTable( name: "Department"` :

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Önceki değişikliklerle, varolan satırlar, `Course` Yöntem çalıştıktan sonra "geçici" departmanıyla ilişkilendirilir `ComplexDataModel` `Up` .

Bir üretim uygulaması şöyle olacaktır:

* `Department`Yeni satırlara satırlar ve ilgili satırlar eklemek için kod veya komut dosyaları ekleyin `Course` `Department` .
* İçin "geçici" Departmanı veya varsayılan değeri kullanmayın `Course.DepartmentID` .

Sonraki öğreticide ilgili veriler ele alınmaktadır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü (Bölüm 1)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [Bu öğreticinin YouTube sürümü (Bölüm 2)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> [Önceki](xref:data/ef-rp/migrations) 
>  [Sonraki](xref:data/ef-rp/read-related-data)

::: moniker-end
