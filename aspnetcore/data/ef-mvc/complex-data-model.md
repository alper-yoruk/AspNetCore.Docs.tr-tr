---
title: 'Öğretici: EF Core ile karmaşık veri modeli oluşturma-ASP.NET MVC'
description: Bu öğreticide, daha fazla varlık ve ilişki ekleyin ve biçimlendirme, doğrulama ve eşleme kurallarını belirterek veri modelini özelleştirin.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 10d6f0bd6f6b95efbe868e4bc21513460e1f0b67
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652467"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a>Öğretici: EF Core ile karmaşık veri modeli oluşturma-ASP.NET MVC

Önceki öğreticilerde, üç varlıktan oluşan basit bir veri modeliyle çalıştık. Bu öğreticide, daha fazla varlık ve ilişki ekleyeceksiniz ve biçimlendirme, doğrulama ve veritabanı eşleme kurallarını belirterek veri modelini özelleştireceksiniz.

İşiniz bittiğinde, varlık sınıfları aşağıdaki çizimde gösterilen tamamlanmış veri modelini oluşturacak:

![Varlık diyagramı](complex-data-model/_static/diagram.png)

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Veri modelini özelleştirme
> * Öğrenci varlığında değişiklik yap
> * Eğitmen varlığı oluşturma
> * OfficeAssignment varlığı oluştur
> * Kurs varlığını değiştirme
> * Departman varlığı oluştur
> * Kayıt varlığını değiştirme
> * Veritabanı bağlamını güncelleştirme
> * Test verileriyle çekirdek veritabanı
> * Geçiş Ekle
> * Bağlantı dizesini değiştirme
> * Veritabanını güncelleştirme

## <a name="prerequisites"></a>Önkoşullar

* [EF Core geçişleri kullanma](migrations.md)

## <a name="customize-the-data-model"></a>Veri modelini özelleştirme

Bu bölümde, biçimlendirme, doğrulama ve veritabanı eşleme kurallarını belirten öznitelikleri kullanarak veri modelini nasıl özelleştireceğinizi göreceksiniz. Ardından, aşağıdaki bölümlerde, zaten oluşturduğunuz sınıflara öznitelikler ekleyerek ve modeldeki kalan varlık türleri için yeni sınıflar oluşturarak tüm okul veri modelini oluşturacaksınız.

### <a name="the-datatype-attribute"></a>DataType özniteliği

Öğrenci kayıt tarihleri için tüm Web sayfaları Şu anda tarihle birlikte görüntülenir, ancak bu alan için tüm önemli bir tarih olması gerekir. Veri ek açıklaması özniteliklerini kullanarak, verileri gösteren her görünümde görüntü biçimini giderecek bir kod değişikliği yapabilirsiniz. Bunun nasıl yapılacağını gösteren bir örnek görmek için, sınıfındaki özelliğine bir özniteliği ekleyeceksiniz `EnrollmentDate` `Student` .

*Modeller/öğrenci. cs*' de, `using` ad alanı için bir ifade ekleyin `System.ComponentModel.DataAnnotations` ve `DataType` `DisplayFormat` `EnrollmentDate` Aşağıdaki örnekte gösterildiği gibi özelliğe ve özniteliklerini ekleyin:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

`DataType`Özniteliği, veritabanı iç türünden daha belirgin bir veri türü belirtmek için kullanılır. Bu durumda, tarihi ve saati değil yalnızca tarihi izlemek istiyoruz. `DataType`Sabit listesi, tarih, saat, PhoneNumber, para birimi, Emaadresi gibi birçok veri türünü sağlar. `DataType`Özniteliği Ayrıca uygulamanın türe özgü özellikleri otomatik olarak sağlamasını da sağlayabilir. Örneğin, için bir `mailto:` bağlantı oluşturulabilir `DataType.EmailAddress` ve HTML5 'i destekleyen tarayıcılarda için bir tarih seçici sağlaneklenebilir `DataType.Date` . `DataType`ÖZNITELIĞI `data-` HTML 5 tarayıcılarının ANLAYABILMESI için HTML 5 (bir veri Dash) öznitelikleri yayar. `DataType`Öznitelikler hiçbir doğrulama sağlamaz.

`DataType.Date`görüntülenen tarihin biçimini belirtmez. Varsayılan olarak, veri alanı sunucunun CultureInfo öğesine göre varsayılan biçimlere göre görüntülenir.

`DisplayFormat`Öznitelik, tarih biçimini açıkça belirtmek için kullanılır:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode`Ayar, bir metin kutusunda değer görüntülenmek üzere görüntülendiğinde biçimlendirmenin de uygulanacağını belirtir. (Bazı alanlar için bunu istemiyor olabilirsiniz; örneğin, para birimi değerleri için metin kutusundaki para birimi sembolünü düzenlemenin değiştirilmesini istemeyebilirsiniz.)

`DisplayFormat`Özniteliğini kendisi kullanabilirsiniz, ancak özniteliği de kullanmak iyi bir fikir olabilir `DataType` . `DataType`Özniteliği, bir ekranda nasıl işlenirim aksine, verilerin semantiğini sunar ve aşağıdaki avantajları sağlar `DisplayFormat` :

* Tarayıcı HTML5 özelliklerini etkinleştirebilir (örneğin, bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını, bazı istemci tarafı giriş doğrulamasını vb. göstermek için).

* Varsayılan olarak tarayıcı, verileri yerel ayarınızı temel alarak doğru biçimi kullanarak işleyebilir.

Daha fazla bilgi için bkz. [ \<input> etiket Yardımcısı belgeleri](../../mvc/views/working-with-forms.md#the-input-tag-helper).

Uygulamayı çalıştırın, öğrenciler dizin sayfasına gidin ve kayıt tarihleri için saatlerin artık gösterilmediğine dikkat edin. Aynı değer öğrenci modelini kullanan herhangi bir görünüm için de geçerli olacaktır.

![Öğrenciler Dizin sayfası tarihleri zamansız gösterme](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>StringLength özniteliği

Ayrıca, öznitelikleri kullanarak veri doğrulama kuralları ve doğrulama hata iletileri de belirtebilirsiniz. `StringLength`Öznitelik, veritabanında en fazla uzunluğu ayarlar ve ASP.NET Core MVC için istemci tarafı ve sunucu tarafı doğrulaması sağlar. Bu öznitelikte en küçük dize uzunluğunu da belirtebilirsiniz, ancak en küçük değerin veritabanı şemasında hiçbir etkisi yoktur.

Kullanıcıların bir ad için 50 ' den fazla karakter girmemesini istediğinizi varsayalım. Bu kısıtlamayı eklemek için `StringLength` `LastName` `FirstMidName` Aşağıdaki örnekte gösterildiği gibi öznitelikleri ve özelliklerine ekleyin:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Öznitelik, bir `StringLength` kullanıcının ad için boşluk girmesini engellemez. `RegularExpression`Girişe kısıtlama uygulamak için özniteliğini kullanabilirsiniz. Örneğin, aşağıdaki kod ilk karakterin büyük küçük harf olmasını ve geri kalan karakterlerin alfabetik olmasını gerektirir:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

`MaxLength`Öznitelik, `StringLength` özniteliğe benzer ancak istemci tarafı doğrulaması sağlamayan işlevselliği sağlar.

Veritabanı modeli, veritabanı şemasında değişiklik yapılmasını gerektiren bir şekilde değiştirildi. Uygulama kullanıcı arabirimini kullanarak, veritabanına eklemiş olduğunuz herhangi bir veriyi kaybetmeden Şemayı güncelleştirmek için geçişleri kullanacaksınız.

Değişikliklerinizi kaydedin ve projeyi derleyin. Ardından proje klasöründe komut penceresini açın ve aşağıdaki komutları girin:

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

`migrations add`Bu komut, iki sütun için en fazla uzunluğu daha kısa hale yaptığından, veri kaybının gerçekleşebileceğini uyarır.  Geçişler * \<timeStamp> _MaxLengthOnNames. cs*adlı bir dosya oluşturur. Bu dosya, `Up` geçerli veri modeliyle eşleşecek şekilde veritabanını güncelleştirecek yöntemdeki kodu içerir. `database update`Komut bu kodu çalıştırdı.

Geçiş dosyası adının ön eki olan zaman damgası, geçişleri sıralamak için Entity Framework tarafından kullanılır. Update-database komutunu çalıştırmadan önce birden çok geçiş oluşturabilirsiniz ve sonra tüm geçişler oluşturuldukları sırada uygulanır.

Uygulamayı çalıştırın, **öğrenciler** sekmesini seçin, **Yeni oluştur**' a tıklayın ve 50 karakterden daha uzun bir ad girmeyi deneyin. Uygulamanın bunu yapmasını önleyebilmelidir. 

### <a name="the-column-attribute"></a>Column özniteliği

Ayrıca, sınıflarınızın ve özelliklerinin veritabanına nasıl eşlenildiğini denetlemek için özniteliklerini de kullanabilirsiniz. `FirstMidName`Alan aynı zamanda bir orta ad içerebileceğinden, birinci ad alanı için adı kullandığınızı varsayalım. Ancak veritabanına `FirstName` karşı geçici sorgular yazmayacak olan kullanıcılar bu ada alışkın olduğundan, veritabanı sütununun adlandırılmak istiyorsunuz. Bu eşlemeyi yapmak için `Column` özniteliğini kullanabilirsiniz.

`Column`Özniteliği veritabanı oluşturulduğunda, `Student` özelliği ile eşlenen tablonun sütununun `FirstMidName` adlandırılacağını belirtir `FirstName` . Diğer bir deyişle, kodunuz öğesine başvurduğunda `Student.FirstMidName` , veriler tablonun sütununda gelir veya güncelleştirilir `FirstName` `Student` . Sütun adları belirtmezseniz, bunlar Özellik adı ile aynı ada verilir.

*Student.cs* dosyasında, için bir ifade ekleyin `using` `System.ComponentModel.DataAnnotations.Schema` ve `FirstMidName` aşağıdaki vurgulanmış kodda gösterildiği gibi, özelliği için bir sütun adı özniteliği ekleyin:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Özniteliği ekleme modeli, `Column` `SchoolContext` tarafından yedeklendiğinden, veritabanıyla eşleşmeyecek şekilde değişir.

Değişikliklerinizi kaydedin ve projeyi derleyin. Ardından proje klasöründe komut penceresini açın ve başka bir geçiş oluşturmak için aşağıdaki komutları girin:

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

**SQL Server Nesne Gezgini** **, öğrenci tablosuna** çift tıklayarak öğrenci tablosu tasarımcısını açın.

![Geçişlerde SSOX 'teki öğrenciler tablosu](complex-data-model/_static/ssox-after-migration.png)

İlk iki geçişi uygulamadan önce ad sütunları nvarchar (MAX) türünde. Artık nvarchar (50) ve sütun adı FirstMidName iken FirstName olarak değiştirilmiştir.

> [!Note]
> Aşağıdaki bölümlerde tüm varlık sınıflarını oluşturmayı bitirmeden önce derlemeyi denerseniz Derleyici hataları alabilirsiniz.

## <a name="changes-to-student-entity"></a>Öğrenci varlığındaki değişiklikler

![Öğrenci varlığı](complex-data-model/_static/student-entity.png)

*Modeller/öğrenci. cs*' de, daha önce eklediğiniz kodu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Gerekli öznitelik

`Required`Özniteliği, ad özellikleri gerekli alanları yapar. `Required`Değer türleri (DateTime, int, Double, float, vb.) gibi null yapılamayan türler için öznitelik gerekli değildir. Null olmayan türler otomatik olarak gerekli alanlar olarak değerlendirilir.

`Required`Özniteliğinin zorlanmak üzere ile birlikte kullanılması gerekir `MinimumLength` `MinimumLength` .

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Display özniteliği

`Display`Öznitelik, metin kutularının açıklamalı alt yazısının her örnekteki Özellik adı yerine "ilk ad", "soyadı", "tam ad" ve "kayıt tarihi" olması gerektiğini belirtir (kelimeleri hiçbir alan içermez).

### <a name="the-fullname-calculated-property"></a>FullName hesaplanmış özelliği

`FullName`, iki diğer özelliğin bitiştirerek oluşturulmuş bir değer döndüren hesaplanmış bir özelliktir. Bu nedenle, yalnızca bir get erişimcisine sahiptir ve `FullName` veritabanında hiçbir sütun oluşturulmaz.

## <a name="create-instructor-entity"></a>Eğitmen varlığı oluşturma

![Eğitmen varlığı](complex-data-model/_static/instructor-entity.png)

Şablon kodunu aşağıdaki kodla değiştirerek *modeller/eğitmen. cs*oluşturun:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Öğrenci ve eğitmen varlıklarında birçok özellik aynı olduğuna dikkat edin. Devralma öğreticisini bu serinin ilerleyen kısımlarında [uyguladığınızda](inheritance.md) , artıklığı ortadan kaldırmak için bu kodu yeniden düzenlemelisiniz.

Birden çok özniteliği tek bir satıra koyabilirsiniz, böylece `HireDate` öznitelikleri aşağıdaki şekilde yazabilirsiniz:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Courseatamalar ve OfficeAssignment gezinti özellikleri

`CourseAssignments`Ve `OfficeAssignment` özellikleri gezinti özellikleridir.

Bir eğitmen herhangi bir sayıda kurs öğretebilir, bu nedenle `CourseAssignments` bir koleksiyon olarak tanımlanır.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Bir gezinti özelliği birden çok varlık tukiyorsa, türü girişlerin eklenebileceği, silinebileceği ve güncelleştirilemeyebilir bir liste olmalıdır.  Veya gibi `ICollection<T>` bir tür belirtebilirsiniz `List<T>` `HashSet<T>` . Belirtirseniz `ICollection<T>` , EF `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur.

Bunların `CourseAssignment` varlıkların, çoktan çoğa ilişkiler hakkında bölümünde aşağıda açıklanmasının nedeni aşağıda açıklanmıştır.

Contoso Üniversitesi iş kuralları, bir eğitmenin yalnızca en fazla bir ofisin olabileceğini, bu nedenle `OfficeAssignment` özelliğin tek bir OfficeAssignment varlığı (Office atanmamışsa null olabilir) bulundurduğunu belirten bir durumdur.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a>OfficeAssignment varlığı oluştur

![OfficeAssignment varlığı](complex-data-model/_static/officeassignment-entity.png)

Aşağıdaki kodla *modeller/OfficeAssignment. cs* oluşturun:

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Anahtar özniteliği

Eğitmen ve OfficeAssignment varlıkları arasında bire sıfır veya-bir ilişki vardır. Office ataması, atandığı eğitmenle ilişkili olarak yalnızca kendi birincil anahtarı da eğitmen varlığına ait yabancı anahtarıdır. Ancak Entity Framework, adı ID veya Classnameıd adlandırma kuralını izlemediği için bu varlığın birincil anahtarı olarak Komutctorıd 'yi otomatik olarak tanıyamaz. Bu nedenle, `Key` özniteliği anahtar olarak tanımlamak için kullanılır:

```csharp
[Key]
public int InstructorID { get; set; }
```

`Key`Varlık kendi birincil anahtarına sahip olsa da, özelliği Classnameıd veya ID dışında bir şey olarak adlandırmak istiyorsanız özniteliğini de kullanabilirsiniz.

Varsayılan olarak, tam olarak, sütun tanımlayıcı bir ilişki için olduğundan EF, anahtarı veritabanı olmayan bir şekilde değerlendirir.

### <a name="the-instructor-navigation-property"></a>Eğitmen gezintisi özelliği

Eğitmen varlığı, null yapılabilir bir `OfficeAssignment` gezinti özelliğine sahiptir (bir eğitmenin bir Office ataması olmayabilir) ve OfficeAssignment varlığı null atanamaz bir `Instructor` gezinti özelliğine sahiptir (bir Office ataması bir eğitmen olmadan mevcut olamaz-- `InstructorID` null değer atanamaz). Bir eğitmen varlığı ilgili bir OfficeAssignment varlığına sahip olduğunda, her varlığın gezinti özelliğinde diğer birine bir başvurusu olur.

`[Required]`İlgili bir eğitmen olması gerektiğini belirtmek Için eğitmen gezinti özelliğine bir öznitelik koyabilirsiniz, ancak `InstructorID` yabancı anahtar (aynı zamanda bu tabloya yönelik olan anahtar) null değer atanamaz olduğundan bunu yapmanız gerekmez.

## <a name="modify-course-entity"></a>Kurs varlığını değiştirme

![Kurs varlığı](complex-data-model/_static/course-entity.png)

*Modeller/kurs. cs*' de, daha önce eklediğiniz kodu aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Kurs varlığının, `DepartmentID` Ilgili departman varlığını işaret eden ve bir gezinti özelliği olan yabancı anahtar özelliği vardır `Department` .

Entity Framework, ilgili varlık için bir gezinti özelliği olduğunda, veri modelinize yabancı anahtar özelliği eklemenizi gerektirmez.  EF, gerektiğinde otomatik olarak yabancı anahtarlar oluşturur ve bunlar için [gölge Özellikler](/ef/core/modeling/shadow-properties) oluşturur. Ancak veri modelinde yabancı anahtar olması, güncelleştirmeleri daha basit ve daha verimli hale getirir. Örneğin, düzenlemek üzere bir kurs varlığı aldığınızda, bunu yüklemezseniz departman varlığı null olur, bu nedenle kurs varlığını güncelleştirdiğinizde öncelikle departman varlığını almanız gerekir. Yabancı anahtar özelliği `DepartmentID` veri modeline dahil edildiğinde, güncelleştirmeden önce bölüm varlığını almanız gerekmez.

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated özniteliği

`DatabaseGenerated`Özelliğindeki parametresi olan özniteliği, `None` `CourseID` birincil anahtar değerlerinin veritabanı tarafından oluşturulması yerine Kullanıcı tarafından sağlandığını belirtir.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Varsayılan olarak, Entity Framework birincil anahtar değerlerinin veritabanı tarafından oluşturulduğunu varsayar. Bu, Çoğu senaryoda istediğiniz şeydir. Ancak, kurs varlıkları için bir departman için 1000 serisi, başka bir departman için 2000 serisi vb. gibi kullanıcı tarafından belirtilen bir kurs numarası kullanırsınız.

`DatabaseGenerated`Özniteliği, bir satırın oluşturulduğu veya güncelleştirildiği tarihi kaydetmek için kullanılan veritabanı sütunlarında olduğu gibi varsayılan değerleri oluşturmak için de kullanılabilir.  Daha fazla bilgi için bkz. [üretilen Özellikler](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

Kurs varlığındaki yabancı anahtar özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

Bir kurs bir departmana atanır, bu nedenle `DepartmentID` yukarıda bahsedilen nedenlerden dolayı bir yabancı anahtar ve bir `Department` gezinti özelliği vardır.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir, bu nedenle `Enrollments` gezinti özelliği bir koleksiyondur:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Kurs, birden fazla eğitmen tarafından tada olabilir, bu nedenle `CourseAssignments` gezinti özelliği bir koleksiyon olur (tür `CourseAssignment` [daha sonra](#many-to-many-relationships)açıklanmıştır):

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a>Departman varlığı oluştur

![Bölüm varlığı](complex-data-model/_static/department-entity.png)

Aşağıdaki kodla *modeller/departman. cs* oluşturun:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Column özniteliği

Daha önce, `Column` sütun adı eşlemesini değiştirmek için özniteliğini kullandınız. Bölüm varlığının kodunda, `Column` sütun, veritabanının SQL Server para türü kullanılarak tanımlanacak şekılde SQL veri türü eşlemesini değiştirmek için kullanılır:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Entity Framework, özellik için tanımladığınız CLR türüne göre uygun SQL Server veri türünü seçtiği için sütun eşlemesi genellikle gerekli değildir. CLR `decimal` türü SQL Server bir `decimal` türe eşlenir. Ancak bu durumda, sütunun para birimi tutarlarını tutuını ve para veri türü bunun için daha uygun olduğunu bilirsiniz.

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

Yabancı anahtar ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

Bir departman yönetici olabilir veya olmayabilir ve yönetici her zaman bir eğitmendir. Bu nedenle, `InstructorID` özelliği, eğitmen varlığına yabancı anahtar olarak dahil edilir ve `int` tür atamadan sonra özelliği null yapılabilir olarak işaretlemek için bir soru işareti eklenir. Gezinti özelliği adlandırılır `Administrator` ancak bir eğitmen varlığı tutar:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Bir departmanın birçok kursu olabilir, bu nedenle bir kurs gezintisi özelliği vardır:

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> Kurala göre Entity Framework, null olamayan yabancı anahtarlar ve çoktan çoğa ilişkiler için art arda silme imkanı sağlar. Bu, bir geçiş eklemeye çalıştığınızda bir özel duruma neden olacak dairesel basamaklı silme kurallarına neden olabilir. Örneğin, Department. Komutctorıd özelliğini null yapılabilir olarak tanımlamadıysanız, bu, bir basamak silme kuralını, eğitmeni sildiğinizde, ne yapmak istediğinize ilişkin olmayan bir şeyi silmek için yapılandırır. İş kurallarınız `InstructorID` özelliği null değer atanamaz olarak gerektiriyorsa, ilişkide basamaklı silmeyi devre dışı bırakmak için aşağıdaki Fluent API ifadesini kullanmanız gerekir:
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a>Kayıt varlığını değiştirme

![Kayıt varlığı](complex-data-model/_static/enrollment-entity.png)

*Modeller/kayıt. cs*' de, daha önce eklediğiniz kodu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Yabancı anahtar ve gezinti özellikleri

Yabancı anahtar özellikleri ve gezinti özellikleri aşağıdaki ilişkileri yansıtır:

Kayıt kaydı tek bir kurs için olduğundan, `CourseID` yabancı anahtar özelliği ve bir `Course` gezinti özelliği vardır:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Kayıt kaydı tek bir öğrenci içindir, bu nedenle bir `StudentID` yabancı anahtar özelliği ve bir `Student` gezinti özelliği vardır:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Çoktan çoğa ilişkiler

Öğrenci ve kurs varlıkları arasında çok-çok ilişkisi vardır ve kayıt varlığı, veritabanında *Yük içeren* çoktan çoğa bir JOIN tablosu gibi çalışır. "Yük ile", kayıt tablosunun birleştirilmiş tablolar için yabancı anahtarlar (Bu durumda bir birincil anahtar ve bir sınıf özelliği) yanında ek veriler içerdiği anlamına gelir.

Aşağıdaki çizimde bu ilişkilerin bir varlık diyagramında nasıl göründüğünü gösterilmektedir. (Bu diyagram EF 6. x için Entity Framework güç araçları kullanılarak oluşturulmuştur; diyagramı oluşturmak öğreticinin bir parçası değildir, burada yalnızca bir çizim olarak kullanılmaktadır.)

![Öğrenci-çok fazla ilişki](complex-data-model/_static/student-course.png)

Her ilişki satırında 1 bir sonda ve diğeri de bir yıldız işareti (*) bulunur. Bu, bire çok ilişkiyi belirtir.

Kayıt tablosu, sınıf bilgilerini içermiyorsa, yalnızca iki yabancı anahtar olan CourseID ve Studentitıd 'yi içermelidir. Bu durumda, veritabanına yük (veya saf bir JOIN tablosu) olmadan çok-çok arasında bir JOIN tablosu olacaktır. Eğitmen ve kurs varlıklarının bu tür çok-çok ilişkisi vardır ve bir sonraki adımınız, yük olmadan bir JOIN tablosu olarak çalışacak bir varlık sınıfı oluşturmaktır.

(EF 6. x, çoktan çoğa ilişkiler için örtük birleştirmeyi destekler, ancak EF Core. Daha fazla bilgi için, [EF Core GitHub deposundaki tartışmaya](https://github.com/aspnet/EntityFramework/issues/1368)bakın.)

## <a name="the-courseassignment-entity"></a>Courseatama varlığı

![Courseatama varlığı](complex-data-model/_static/courseassignment-entity.png)

Aşağıdaki kodla *modeller/Courseatama. cs* oluşturun:

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>Varlık adlarını Birleştir

Eğitim--çok ilişkisi için veritabanında bir JOIN tablosu gereklidir ve bir varlık kümesi tarafından temsil edilir. Bu örnekte olduğu gibi bir JOIN varlığı adı yaygın olarak `EntityName1EntityName2` kullanılır `CourseInstructor` . Ancak, ilişkiyi açıklayan bir ad seçmenizi öneririz. Veri modelleri, daha sonra yükleri daha sonra almak için, yük olmayan birleşimler olmadan basit ve büyümeye başlar. Açıklayıcı bir varlık adıyla başladıysanız, daha sonra adı değiştirmeniz gerekmez. İdeal olarak, JOIN varlığının iş etki alanında kendi doğal (muhtemelen tek bir kelime) adına sahip olması gerekir. Örneğin, kitaplar ve müşteriler derecelendirmeler aracılığıyla bağlanabilir. Bu ilişki için, `CourseAssignment` daha iyi bir seçenektir `CourseInstructor` .

### <a name="composite-key"></a>Bileşik anahtar

Yabancı anahtarlar null değer atanmadığından ve tablodaki her satırı benzersiz bir şekilde tanımladığından, ayrı bir birincil anahtar gerekmez. *Komutctorıd* ve *CourseID* özellikleri bir bileşik birincil anahtar olarak çalışır. EF için bileşik birincil anahtarları tanımlamanın tek yolu *Fluent API* kullanmaktır (öznitelikleri kullanılarak gerçekleştirilemez). Bir sonraki bölümde bileşik birincil anahtarı nasıl yapılandıracağınızı göreceksiniz.

Bileşik anahtar, tek bir kurs için birden çok satır ve bir eğitmen için birden fazla satır, aynı eğitmen ve kurs için birden çok satıra sahip olmanızı sağlar. `Enrollment`JOIN varlığı kendi birincil anahtarını tanımlar, bu nedenle bu sıralamanın yinelemeleri mümkündür. Bu tür yinelemeleri engellemek için yabancı anahtar alanlarına benzersiz bir dizin ekleyebilir veya `Enrollment` benzer bir birincil bileşik anahtarla yapılandırabilirsiniz `CourseAssignment` . Daha fazla bilgi için bkz. [dizinler](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Veritabanı bağlamını güncelleştirme

*Data/SchoolContext. cs* dosyasına aşağıdaki vurgulanmış kodu ekleyin:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Bu kod yeni varlıkları ekler ve Courseatama varlığının bileşik birincil anahtarını yapılandırır.

## <a name="about-a-fluent-api-alternative"></a>Fluent API alternatifi hakkında

Sınıf yöntemindeki kod, `OnModelCreating` `DbContext` EF davranışını yapılandırmak için *Fluent API* kullanır. Bu örnekte, [EF Core belgelerinden](/ef/core/modeling/#use-fluent-api-to-configure-a-model)farklı olarak, bir dizi yöntemi çağıran tek bir bildirimde dize tarafından KULLANıLDıĞıNDAN, API "floent" olarak adlandırılır:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

Bu öğreticide, yalnızca öznitelikleri ile yapaamıyoruz veritabanı eşlemesi için Fluent API kullanıyorsunuz. Ancak, özniteliklerini kullanarak yapabileceğiniz biçimlendirme, doğrulama ve eşleme kurallarının çoğunu belirtmek için Fluent API de kullanabilirsiniz. Gibi bazı öznitelikler `MinimumLength` Fluent API uygulanamaz. Daha önce belirtildiği gibi, `MinimumLength` Şemayı değiştirmez, yalnızca bir istemci ve sunucu tarafı doğrulama kuralı uygular.

Bazı geliştiriciler, varlık sınıflarının "temiz" olmasını sağlamak için Fluent API özel olarak kullanmayı tercih eder. İsterseniz öznitelikleri ve Fluent API karıştırabilir ve yalnızca Fluent API kullanılarak gerçekleştirilebilecek bazı özelleştirmeler de vardır ancak genel olarak önerilen uygulama, bu iki yaklaşımdan birini seçmek ve bunları mümkün olduğunca tutarlı bir şekilde kullanmaktır. Her ikisini de kullanırsanız, bir çakışma olduğunda, akıcı API 'nin öznitelikleri geçersiz kıldığını unutmayın.

Öznitelikler ile Fluent API hakkında daha fazla bilgi için bkz. [yapılandırma yöntemleri](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Ilişkileri gösteren varlık diyagramı

Aşağıdaki çizimde, Entity Framework Power Tools 'un tamamlanmış okul modeli için kullandığı diyagram gösterilmektedir.

![Varlık diyagramı](complex-data-model/_static/diagram.png)

Bire çok ilişki çizgilerinin yanı sıra (1 ' den fazla \* ), eğitmen ve OfficeAssignment varlıkları arasında (1 ila 0.. 1) ve eğitmen ve departman varlıkları arasında sıfır veya-bire çok ilişki satırı (0.. 1 ile *) arasında bir-sıfır veya-bir ilişki satırını görebilirsiniz.

## <a name="seed-database-with-test-data"></a>Test verileriyle çekirdek veritabanı

*Veri/Dbınınitializer. cs* dosyasındaki kodu, oluşturduğunuz yeni varlıklar için çekirdek verileri sağlamak üzere aşağıdaki kodla değiştirin.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

İlk öğreticide gördüğünüz gibi, bu kodun çoğu yalnızca yeni varlık nesneleri oluşturur ve test için gereken şekilde, örnek verileri özelliklere yükler. Çoktan çoğa ilişkilerin nasıl işlendiği hakkında dikkat edin: kod, `Enrollments` ve varlık kümelerinde varlıklar oluşturarak ilişkiler oluşturur `CourseAssignment` .

## <a name="add-a-migration"></a>Geçiş Ekle

Değişikliklerinizi kaydedin ve projeyi derleyin. Ardından proje klasöründe komut penceresini açın ve `migrations add` komutu girin (henüz Update-database komutunu yapmayın):

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

Olası veri kaybı hakkında bir uyarı alırsınız.

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

`database update`Komutu bu noktada çalıştırmayı denediyseniz (henüz yapmayın), şu hatayı alırsınız:

> ALTER TABLE ifadesi, "FK_dbo yabancı anahtar kısıtlaması ile çakışıyor. Course_dbo. Department_DepartmentID ". "ContosoUniversity" veritabanında, "dbo" tablosunda çakışma oluştu. Bölüm ", sütun ' DepartmentID '.

Bazı durumlarda, mevcut verilerle geçişleri yürüttüğünüzde, yabancı anahtar kısıtlamalarını karşılamak için saplama verilerini veritabanına eklemeniz gerekir. Yönteminde oluşturulan kod, `Up` Kurs tablosuna null yapılamayan bir DepartmentID yabancı anahtar ekler. Kurs tablosunda kod çalıştırıldığında zaten satırlar varsa, `AddColumn` SQL Server null olmayan sütuna hangi değerin yerleştirileceğini bilmediği için işlem başarısız olur. Bu öğreticide, geçişi yeni bir veritabanında çalıştıracaksınız, ancak bir üretim uygulamasında geçiş, mevcut verileri işleyeceğinizden, bu sayede bunun nasıl yapılacağını gösteren bir örnek gösterilmektedir.

Bu geçişi mevcut verilerle birlikte çalışarak, yeni sütuna varsayılan bir değer vermek için kodu değiştirmeniz ve varsayılan departman görevi gören "Temp" adlı bir saplama departmanı oluşturmanız gerekir. Sonuç olarak, mevcut kurs satırları, yöntem çalıştıktan sonra "geçici" departmanla ilgili olacaktır `Up` .

* *{Timestamp} _ComplexDataModel. cs* dosyasını açın.

* DepartmentID sütununu kurs tablosuna ekleyen kod satırını açıklama satırı yapın.

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* Departman tablosunu oluşturan koddan sonra aşağıdaki vurgulanmış kodu ekleyin:

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Bir üretim uygulamasında, departman satırları eklemek ve kurs satırlarını yeni departman satırlarıyla ilişkilendirmek için kod veya komut dosyaları yazın. Bundan sonra "geçici" Departmanı veya kurs. DepartmentID sütununda Varsayılan değer gerekmez.

Değişikliklerinizi kaydedin ve projeyi derleyin.

## <a name="change-the-connection-string"></a>Bağlantı dizesini değiştirme

Artık `DbInitializer` sınıfta yeni varlıklar için tohum verilerini boş bir veritabanına ekleyen yeni kodunuz var. EF 'in yeni boş bir veritabanı oluşturmasını sağlamak için *appSettings. JSON* içindeki bağlantı dizesinde veritabanının adını ContosoUniversity3 veya kullandığınız bilgisayarda kullanmadığınız başka bir adla değiştirin.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

Değişiklerinizi *appSettings. JSON*' da kaydedin.

> [!NOTE]
> Veritabanı adını değiştirmeye alternatif olarak, veritabanını silebilirsiniz. **SQL Server Nesne Gezgini** (ssox) veya `database drop` CLI komutunu kullanın:
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a>Veritabanını güncelleştirme

Veritabanı adını değiştirdikten veya veritabanını sildikten sonra, `database update` geçişleri yürütmek için komut penceresinde komutunu çalıştırın.

```dotnetcli
dotnet ef database update
```

`DbInitializer.Initialize`Yöntemi çalıştırmak ve yeni veritabanını doldurmak için uygulamayı çalıştırın.

Veritabanını daha önce yaptığınız gibi SSOX içinde açın ve tabloların tümünün oluşturulduğunu görmek için **Tables** düğümünü genişletin. (Yine de bir daha önceki zamanda SSOX açıksa **Yenile** düğmesine tıklayın.)

![SSOX içindeki tablolar](complex-data-model/_static/ssox-tables.png)

Veritabanını gösteren Başlatıcı kodunu tetiklemek için uygulamayı çalıştırın.

**Courseatama** tablosuna sağ tıklayın ve verileri **görüntüle** ' yi seçerek veri içerdiğinden emin olun.

![SSOX 'te Courseatama verileri](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a>Kodu alma

[Tamamlanmış uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Veri modeli özelleştirildi
> * Öğrenci varlığında değişiklikler yapıldı
> * Eğitmen varlığı oluşturuldu
> * OfficeAssignment varlığı oluşturuldu
> * Değiştirilen kurs varlığı
> * Departman varlığı oluşturuldu
> * Değiştirilen kayıt varlığı
> * Veritabanı bağlamı güncelleştirildi
> * Test verileriyle birlikte sağlanan veritabanı
> * Geçiş eklendi
> * Bağlantı dizesi değiştirildi
> * Veritabanı güncelleştirildi

İlgili verilere erişme hakkında daha fazla bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Sonraki: ilgili verilere erişin](read-related-data.md)
