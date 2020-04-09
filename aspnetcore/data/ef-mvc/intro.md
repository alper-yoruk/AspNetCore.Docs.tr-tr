---
title: 'Öğretici: ASP.NET bir MVC web uygulamasında EF Core ile başlayın'
description: Bu sıfırdan Contoso Üniversitesi örnek uygulama oluşturmak için nasıl açıklayan öğreticiler bir dizi ilkidir.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: fca9fdc425506ec8b4eec5c609237208f4c0d7b5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511307"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Öğretici: ASP.NET bir MVC web uygulamasında EF Core ile başlayın

Bu öğretici Core 3.0 ASP.NET **güncelleştirilemedi.** [Razor Pages sürümü](xref:data/ef-rp/intro) güncellendi. Bu öğreticinin ASP.NET Core 3.0 ve sonraki sürümü için kod değişikliklerinin çoğu:

* *Startup.cs* ve *Program.cs* dosyalarında bulunmaktadır.
* [Razor Pages sürümünde](xref:data/ef-rp/intro)bulunabilir. 

Bunun ne zaman güncelleştirilebileceği hakkında bilgi için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/13920)bakın.

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Contoso Üniversitesi örnek web uygulaması, Entity Framework (EF) Core 2.2 ve Visual Studio 2017 veya 2019'u kullanarak ASP.NET Core 2.2 MVC web uygulamalarının nasıl oluşturulacak larını göstermektedir.

Örnek uygulama kurgusal bir Contoso Üniversitesi için bir web sitesidir. Öğrenci kabulü, ders oluşturma ve eğitmen atamaları gibi işlevleri içerir. Bu sıfırdan Contoso Üniversitesi örnek uygulama oluşturmak için nasıl açıklayan öğreticiler bir dizi ilkidir.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * ASP.NET Core MVC web uygulaması oluşturma
> * Site stilini ayarlama
> * EF Core NuGet paketleri hakkında bilgi edinin
> * Veri modelini oluşturma
> * Veritabanı bağlamını oluşturma
> * Bağımlılık enjeksiyonu için bağlamı kaydetme
> * Test verileriyle veritabanını başlatma
> * Denetleyici ve görünümler oluşturma
> * Veritabanını görüntüleme

## <a name="prerequisites"></a>Ön koşullar

* [.NET Çekirdek SDK 2.2](https://dotnet.microsoft.com/download)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) aşağıdaki iş yükleri ile:
  * **ASP.NET ve web geliştirme** iş yükü
  * **.NET Core çapraz platform geliştirme** iş yükü

## <a name="troubleshooting"></a>Sorun giderme

Çözemediğiniz bir sorunla karşınıza çıkarsa, kodunuzu [tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)karşılaştırarak genellikle çözümü bulabilirsiniz. Sık karşılaşılan hataların listesi ve bunları nasıl çözeceğiniz için, [serinin son öğreticisinin Sorun Giderme bölümüne](advanced.md#common-errors)bakın. İhtiyacınız olanı bulamazsanız, [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya EF [Core](https://stackoverflow.com/questions/tagged/entity-framework-core)için StackOverflow.com bir soru gönderebilirsiniz.

> [!TIP]
> Bu 10 öğreticiler, her biri önceki öğreticiler yapılır ne üzerine inşa bir dizi. Her başarılı öğretici tamamlandıktan sonra projenin bir kopyasını kaydetmeyi düşünün. Sonra sorunlarla karşınıza çıkarsa, tüm serinin başına geri dönmek yerine önceki öğretici baştan başlayabilirsiniz.

## <a name="contoso-university-web-app"></a>Contoso Üniversitesi web uygulaması

Bu öğreticiler inşa olacak uygulama basit bir üniversite web sitesidir.

Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleyebilir. Oluşturacağınız ekranlardan birkaçı aşağıda veda edebilirsiniz.

![Öğrenci Endeksi sayfası](intro/_static/students-index.png)

![Öğrenciler Sayfayı Edit](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Web uygulaması oluşturma

* Visual Studio'yu açın.

* **Dosya** menüsünden **Yeni > Projesi'ni**seçin.

* Sol **bölmeden, Yüklü > Visual C# > Web'i**seçin.

* ASP.NET **Çekirdek Web Uygulaması** proje şablonu seçin.

* Ad olarak **ContosoUniversity** girin ve **Tamam'ı**tıklatın.

  ![Yeni Proje iletişim kutusu](intro/_static/new-project2.png)

* **Yeni ASP.NET Çekirdek Web Uygulaması** iletişim kutusunun görünmesini bekleyin.

* **.NET Core**, **ASP.NET Core 2.2** ve **Web Application (Model-View-Controller)** şablonu'nu seçin.

* Kimlik **Doğrulamanın** Kimlik **Doğrulama Yok**olarak ayarlandıklarına emin olun.

* **Tamam'ı** seçin

  ![Yeni ASP.NET Çekirdek Projesi iletişim kutusu](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Site stilini ayarlama

Birkaç basit değişiklik site menüsünü, düzeni ve ana sayfayı ayarlar.

*Görünümleri Aç/Paylaşılan/_Layout.cshtml* ve aşağıdaki değişiklikleri yapın:

* "ContosoUniversity"in her oluşumunu "Contoso Üniversitesi" olarak değiştirin. Üç olay var.

* **Hakkında**, **Öğrenciler**, **Kurslar**, **Eğitmenler**ve **Bölümler**için menü girişleri ekleyin ve **Gizlilik** menü girişini silin.

Değişiklikler vurgulanır.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

*Görünümler/Ana Sayfa/Index.cshtml'de,* ASP.NET ve MVC hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Projeyi çalıştırmak için CTRL+F5 tuşuna basın veya menüden **Hata Ayıklama olmadan Başlat > Hata Ayıklama'yı** seçin. Bu öğreticilerde oluşturacağınız sayfaların sekmelerini içeren ana sayfayı görürsünüz.

![Contoso Üniversitesi giriş sayfası](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>EF Core NuGet paketleri hakkında

Projeye EF Core desteği eklemek için hedeflemek istediğiniz veritabanı sağlayıcısını yükleyin. Bu öğretici SQL Server kullanır ve sağlayıcı paketi [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)olduğunu. Bu paket [Microsoft.AspNetCore.App meta paketine](xref:fundamentals/metapackage-app)dahildir, bu nedenle pakete başvurmanız gerekmez.

EF SQL Server paketi`Microsoft.EntityFrameworkCore` ve bağımlılıkları ( ve `Microsoft.EntityFrameworkCore.Relational`) EF için çalışma zamanı desteği sağlar. Daha [sonra, Geçişler](migrations.md) öğreticisinde bir araç paketi eklersiniz.

Entity Framework Core için kullanılabilen diğer veritabanı sağlayıcıları hakkında bilgi için veritabanı [sağlayıcılarına](/ef/core/providers/)bakın.

## <a name="create-the-data-model"></a>Veri modelini oluşturma

Daha sonra Contoso Üniversitesi uygulaması için varlık sınıfları oluşturacaksınız. Aşağıdaki üç varlıkla başlayacaksınız.

![Ders-Kayıt-Öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

Varlıklar `Student` la `Enrollment` varlıklar arasında bir-çok ilişkisi vardır ve varlıklarla varlıklar arasında `Course` `Enrollment` bire bir ilişki vardır. Başka bir deyişle, bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir derse herhangi bir sayıda öğrenci kaydolabilir.

Aşağıdaki bölümlerde bu varlıkların her biri için bir sınıf oluşturursunuz.

### <a name="the-student-entity"></a>Öğrenci varlığı

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

*Modeller* klasöründe, *Student.cs* adında bir sınıf dosyası oluşturun ve şablon kodunu aşağıdaki kodla değiştirin.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

Özellik, `ID` veritabanı tablosunun bu sınıfa karşılık gelen birincil anahtar sütunu olur. Varsayılan olarak Entity Framework, `ID` veya `classnameID` olarak adlandırılan özellikleri birincil anahtar olarak algılar.

Özellik `Enrollments` bir [navigasyon özelliğidir.](/ef/core/modeling/relationships) Gezinti özellikleri, bu varlıkla ilişkili diğer varlıkları tutar. Bu durumda, `Enrollments` a'nın `Student entity` özelliği, o `Enrollment` `Student` varlıkla ilişkili tüm varlıkları tutar. Başka bir deyişle, veritabanındaki belirli bir Öğrenci satırında ilgili iki Kayıt satırı varsa (öğrencinin Öğrenci Kimliği yabancı `Student` anahtar sütunundaki birincil anahtar değerini içeren satırlar), bu varlığın `Enrollments` gezinti özelliği bu iki `Enrollment` varlığı içerir.

Bir gezinti özelliği birden çok tüzel kişilik tutabiliyorsa (çok-çok veya bir-çok ilişkisinde olduğu gibi), türü girişlerin `ICollection<T>`eklenebileceği, silinebileceği ve güncelleştirilebildiği bir liste olmalıdır. Belirtebilirsiniz `ICollection<T>` veya gibi bir `List<T>` `HashSet<T>`tür ya da . Belirtirseniz, `ICollection<T>`EF varsayılan `HashSet<T>` olarak bir koleksiyon oluşturur.

### <a name="the-enrollment-entity"></a>Kayıt varlığı

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

*Modeller* klasöründe, *Enrollment.cs* oluşturun ve varolan kodu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

Özellik `EnrollmentID` birincil anahtar olacak; bu varlık, `classnameID` `Student` varlıkta `ID` gördüğünüz gibi kendi başına değil deseni kullanır. Normalde bir desen seçer ve veri modeli boyunca kullanabilirsiniz. Burada, varyasyon her iki deseni de kullanabileceğinizi göstermektedir. Daha [sonraki](inheritance.md)bir öğreticide, sınıf adı olmadan kimlik kullanmanın veri modelinde devralmayı nasıl kolaylaştırdığını görürsünüz.

Özellik `Grade` bir `enum`. Tür bildiriminden `Grade` sonraki soru işareti `Grade` özelliğin geçersiz olduğunu gösterir. Sıfır notundan farklı olan bir not - null, bir notun bilinmediği veya henüz atanmadığı anlamına gelir.

Özellik `StudentID` yabancı bir anahtardır ve ilgili `Student`navigasyon özelliği . Bir `Enrollment` varlık tek `Student` bir varlıkla ilişkilidir, bu `Student` nedenle özellik `Student.Enrollments` yalnızca tek bir varlığı tutabilir `Enrollment` (daha önce gördüğünüz ve birden çok varlığı tutabilen gezinti özelliğinden farklı olarak).

Özellik `CourseID` yabancı bir anahtardır ve ilgili `Course`navigasyon özelliği . Bir `Enrollment` varlık tek `Course` bir varlıkla ilişkilidir.

Entity Framework, bir özelliği, `<navigation property name><primary key property name>` adlandırılmışsa yabancı bir anahtar `StudentID` özelliği `Student` olarak yorumlar `Student` (örneğin, varlığın birincil anahtarı olduğundan `ID`gezinti özelliği için). Yabancı anahtar özellikleri de `<primary key property name>` basitçe adlandırılabilir `CourseID` (örneğin, varlığın `Course` `CourseID`birincil anahtarı olduğundan).

### <a name="the-course-entity"></a>Kurs varlığı

![Ders varlık diyagramı](intro/_static/course-entity.png)

*Modeller* klasöründe, *Course.cs* oluşturun ve varolan kodu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

Tesis `Enrollments` bir navigasyon özelliğidir. Bir `Course` varlık herhangi bir sayıda `Enrollment` varlıkla ilişkili olabilir.

Biz bu serinin `DatabaseGenerated` daha sonraki bir [öğretici](complex-data-model.md) öznitelik hakkında daha fazla şey söyleyeceğiz. Temel olarak, bu öznitelik, veritabanının oluşturmasıyerine kursun birincil anahtarını girmenizi sağlar.

## <a name="create-the-database-context"></a>Veritabanı bağlamını oluşturma

Belirli bir veri modeli için Varlık Çerçevesi işlevselliğini koordine eden ana sınıf veritabanı bağlam sınıfıdır. Bu sınıfı `Microsoft.EntityFrameworkCore.DbContext` sınıftan çıkararak oluşturursunuz. Kodunuzda veri modeline hangi varlıkların dahil olduğunu belirtirsiniz. Belirli Varlık Çerçevesi davranışını da özelleştirebilirsiniz. Bu projede sınıfadlandırılmış. `SchoolContext`

Proje *klasöründe, Veri*adlı bir klasör oluşturun.

*Veri* klasöründe *SchoolContext.cs*adında yeni bir sınıf dosyası oluşturun ve şablon kodunu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Bu kod, `DbSet` her varlık kümesi için bir özellik oluşturur. Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir ve varlık tablodaki bir satıra karşılık gelir.

İfadeleri atlayabilirdin `DbSet<Enrollment>` `DbSet<Course>` ve aynı şekilde işe yarayacak. `Student` Varlık, `Enrollment` varlık referansları ve `Enrollment` varlık varlık referansları `Course` nedeniyle Varlık Çerçevesi bunları dolaylı olarak içerecektir.

Veritabanı oluşturulduğunda, EF `DbSet` özellik adlarıyla aynı adlara sahip tablolar oluşturur. Koleksiyonlar için özellik adları genellikle çoğuldur (Öğrenci yerine Öğrenciler), ancak geliştiriciler tablo adlarının çoğul olarak verilip verilmeyeceği konusunda hemfikir değildir. Bu öğreticiler için, DbContext'da tekil tablo adlarını belirterek varsayılan davranışı geçersiz kılarsınız. Bunu yapmak için, son DbSet özelliğinden sonra aşağıdaki vurgulanan kodu ekleyin.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Okul Bağlamını Kaydedin

ASP.NET Core varsayılan olarak [bağımlılık enjeksiyonuyguluyor.](../../fundamentals/dependency-injection.md) Hizmetler (EF veritabanı bağlamı gibi) uygulama başlatma sırasında bağımlılık enjeksiyonu ile kaydedilir. Bu hizmetleri gerektiren bileşenlere (MVC denetleyicileri gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır. Bu öğreticide daha sonra bir bağlam örneği alan denetleyici oluşturucu kodunu görürsünüz.

Hizmet `SchoolContext` olarak kaydolmak için *Startup.cs*açın ve vurgulanan `ConfigureServices` satırları yönteme ekleyin.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

Bağlantı dizesinin adı, `DbContextOptionsBuilder` bir nesne üzerinde bir yöntem çağırılarak içeriğe aktarılır. Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.

`ContosoUniversity.Data` Ekstreler `Microsoft.EntityFrameworkCore` ve ad alanları ekleyin `using` ve sonra projeyi oluşturun.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

*appsettings.json* dosyasını açın ve aşağıdaki örnekte gösterildiği gibi bir bağlantı dizesi ekleyin.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Bağlantı dizesi bir SQL Server LocalDB veritabanı belirtir. LocalDB, SQL Server Express Veritabanı Altyapısının hafif bir sürümüdür ve üretim kullanımı için değil, uygulama geliştirme için tasarlanmıştır. LocalDB isteğe bağlı olarak başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur. Varsayılan olarak, LocalDB `C:/Users/<user>` dizinde *.mdf* veritabanı dosyaları oluşturur.

## <a name="initialize-db-with-test-data"></a>Test verileriyle DB'yi başlatma

Varlık Çerçevesi sizin için boş bir veritabanı oluşturur. Bu bölümde, veritabanı test verileriyle doldurmak için oluşturulduktan sonra çağrılan bir yöntem yazarsınız.

Burada veritabanını `EnsureCreated` otomatik olarak oluşturmak için yöntemi kullanırsınız. Daha [sonraki](migrations.md) bir öğreticide, veritabanını bırakmak ve yeniden oluşturmak yerine veritabanı şemasını değiştirmek için Kod İlk Geçişleri'ni kullanarak model değişikliklerini nasıl işleyeceğiniz göreceksiniz.

*Veri* klasöründe, *DbInitializer.cs* adında yeni bir sınıf dosyası oluşturun ve gerektiğinde bir veritabanıoluşturulmasına neden olan ve test verilerini yeni veritabanına yükleyen şablon kodunu aşağıdaki kodla değiştirin.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Kod, veritabanında öğrenci olup olmadığını denetler ve varsa, veritabanının yeni olduğunu ve test verileriyle tohumlanması gerektiğini varsayar. Performansı en iyi duruma getirmek `List<T>` için test verilerini koleksiyonlar yerine dizilere yükler.

*Program.cs,* uygulama `Main` başlatma da aşağıdaki leri yapmak için yöntemi değiştirin:

* Bağımlılık enjeksiyon kapsayıcısından bir veritabanı bağlam örneği alın.
* Tohum yöntemini çağırın, ona bağlamı geçirin.
* Tohum yöntemi yapıldığında bağlamı atın.

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

Deyim `using` ler ekleyin:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

Eski öğreticilerde, `Configure` *Startup.cs*yönteminde benzer kod görebilirsiniz. `Configure` Yöntemi yalnızca istek ardışık hattını ayarlamak için kullanmanızı öneririz. Uygulama başlangıç kodu `Main` yönteme aittir.

Şimdi uygulamayı ilk çalıştırdığınızda, veritabanı oluşturulur ve test verileriyle tohumlanır. Veri modelinizi her değiştirdiğinizde, veritabanını silebilir, tohum yönteminizi güncelleyebilir ve aynı şekilde yeni bir veritabanıyla yeniden başlayabilirsiniz. Daha sonraki öğreticilerde, veri modeli değiştiğinde veritabanını silmeden ve yeniden oluşturmadan nasıl değiştireceğinizi görürsünüz.

## <a name="create-controller-and-views"></a>Denetleyici ve görünümler oluşturma

Ardından, Visual Studio'daki iskele altyapısını kullanarak bir MVC denetleyicisi ve verileri sorgulamak ve kaydetmek için EF'yi kullanacak görünümler eklersiniz.

CRUD eylem yöntemleri ve görünümleri otomatik oluşturma iskele olarak bilinir. İskele, iskele kodun kendi gereksinimlerinize uyacak şekilde değiştirebileceğiniz bir başlangıç noktası olması nedeniyle kod oluşturma noktasından farklıdır, oysa genellikle oluşturulan kodu değiştirmezsiniz. Oluşturulan kodu özelleştirmeniz gerektiğinde, kısmi sınıflar kullanırsınız veya bir şeyler değiştiğinde kodu yeniden oluşturursunuz.

* **Solution Explorer'daki** **Denetleyiciler** klasörüne sağ tıklayın ve Yeni İskele Öğesi **> ekle'yi**seçin.

* İskele **Ekle** iletişim kutusunda:

  * **Entity Framework'u kullanarak görünüme sahip MVC denetleyicisi**seçin.

  * **Ekle**’ye tıklayın. **Görünümli MVC Denetleyicisi Ekle, Entity Framework** iletişim kutusunu kullanarak görüntülenir.

    ![İskele Öğrenci](intro/_static/scaffold-student2.png)

  * **Model sınıfında** **Öğrenci**seçin.

  * **Veri bağlamında sınıf** seçin **SchoolContext**.

  * Varsayılan **StudentsController'ı** ad olarak kabul edin.

  * **Ekle**’ye tıklayın.

  **Ekle'yi**tıklattığınızda, Visual Studio iskele motoru *denetleyiciyle* çalışan bir StudentsController.cs dosyası ve bir görünüm kümesi *(.cshtml* dosyaları) oluşturur.

(İskele motoru, bu öğretici için daha önce yaptığınız gibi ilk el ile oluşturmazsanız, veritabanı bağlamını da sizin için oluşturabilir. **Veri bağlamı sınıfının**sağındaki artı işaretini tıklatarak **Denetleyici Ekle** kutusunda yeni bir bağlam sınıfı belirtebilirsiniz.  Visual Studio daha `DbContext` sonra sınıfınızın yanı sıra denetleyici ve görünümler oluşturacaktır.)

Denetleyicinin bir `SchoolContext` oluşturucu parametre olarak aldığını fark edeceksiniz.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET Çekirdek bağımlılık enjeksiyonu denetleyici `SchoolContext` içine bir örnek geçen ilgilenir. Bunu daha önce *Startup.cs* dosyasında yapılandırmışsınız.

Denetleyici, veritabanındaki tüm öğrencileri görüntüleyen bir `Index` eylem yöntemi içerir. Yöntem, veritabanı bağlamı örneğinin özelliğini `Students` okuyarak Öğrenci varlığından belirlenen öğrencilerin listesini alır:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Bu koddaki eşzamanlı programlama öğeleri hakkında daha sonra öğreticide öğreneceksiniz.

*Görünümler/Öğrenciler/Index.cshtml* görünümü bu listeyi bir tabloda görüntüler:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Projeyi çalıştırmak için CTRL+F5 tuşuna basın veya menüden **Hata Ayıklama olmadan Başlat > Hata Ayıklama'yı** seçin.

Yöntemin ekteki test verilerini görmek `DbInitializer.Initialize` için Öğrenciler sekmesini tıklatın. Tarayıcı pencerenizin ne kadar dar olduğuna bağlı `Students` olarak, sayfanın üst kısmındaki sekme bağlantısını görürsünüz veya bağlantıyı görmek için sağ üst köşedeki gezinti simgesini tıklatmanız gerekir.

![Contoso Üniversitesi giriş sayfası dar](intro/_static/home-page-narrow.png)

![Öğrenci Endeksi sayfası](intro/_static/students-index.png)

## <a name="view-the-database"></a>Veritabanını görüntüleme

Uygulamayı başlattığınızda, `DbInitializer.Initialize` yöntem çağırır. `EnsureCreated` EF hiçbir veritabanı olduğunu gördüm ve bu yüzden bir `Initialize` oluşturdu, sonra yöntem kodunun geri kalanı veri ile veritabanı doldurulur. Visual Studio'daki veritabanını görüntülemek için **SQL Server Object Explorer'ı** (SSOX) kullanabilirsiniz.

Tarayıcıyı kapatın.

SSOX penceresi zaten açık değilse, Visual Studio'daki **Görünüm** menüsünden seçin.

SSOX'ta **(localdb)\MSSQLLocalDB > Databases'ı**tıklatın ve ardından *appsettings.json* dosyanızdaki bağlantı dizesinde bulunan veritabanı adının girişini tıklatın.

Veritabanınızdaki tabloları görmek için **Tablolar** düğümunu genişletin.

![SSOX tabloları](intro/_static/ssox-tables.png)

Oluşturulan sütunları ve tabloya eklenen satırları görmek için **Öğrenci** tablosuna sağ tıklayın ve **Verileri Görüntüle'yi** tıklatın.

![SSOX öğrenci tablosu](intro/_static/ssox-student-table.png)

*.mdf* ve *.ldf* veritabanı dosyaları *C:\Users\\\<yourusername>* klasöründe bulunmaktadır.

Uygulama başlangıcında çalışan `EnsureCreated` başlatım yöntemini aradığınızdan, artık `Student` sınıfta bir değişiklik yapabilir, veritabanını silebilir, uygulamayı yeniden çalıştırabilirsiniz ve veritabanı değişikliğinize uyacak şekilde otomatik olarak yeniden oluşturulur. Örneğin, `EmailAddress` `Student` sınıfa bir özellik eklerseniz, yeniden oluşturulan `EmailAddress` tabloda yeni bir sütun görürsünüz.

## <a name="conventions"></a>Kurallar

Varlık Çerçevesi'nin sizin için tam bir veritabanı oluşturabilmesi için yazmanız gereken kod miktarı, Varlık Çerçevesi'nin yaptığı kuralların veya varsayımların kullanımı nedeniyle çok azdır.

* Özelliklerin `DbSet` adları tablo adları olarak kullanılır. Bir `DbSet` özellik tarafından başvurulmayan varlıklar için, varlık sınıf adları tablo adları olarak kullanılır.

* Sütun adları için varlık özellik adları kullanılır.

* ID veya classnameID adlı varlık özellikleri birincil anahtar özellikleri olarak kabul edilir.

* Bir özellik, * \<gezinti özelliği adı \<>birincil anahtar özellik adı>* (örneğin, `StudentID` `Student` `Student` varlığın birincil anahtarı olduğundan `ID`gezinti özelliği için) adlı yabancı anahtar özelliği olarak yorumlanır. Yabancı anahtar özellikleri de `Enrollment` yalnızca `EnrollmentID` * \<birincil anahtar özellik adı>* adlandırılabilir (örneğin, `EnrollmentID` varlığın birincil anahtarı olduğundan).

Geleneksel davranışlar geçersiz kılınabilir. Örneğin, bu öğreticide daha önce gördüğünüz gibi tablo adlarını açıkça belirtebilirsiniz. Sütun adlarını ayarlayabilir ve bu serinin daha sonraki bir [öğreticisinde](complex-data-model.md) göreceğiniz gibi, herhangi bir özelliği birincil anahtar veya yabancı anahtar olarak ayarlayabilirsiniz.

## <a name="asynchronous-code"></a>Asynchronous kodu

Asynchronous programlama ASP.NET Core ve EF Core için varsayılan moddur.

Bir web sunucusunda sınırlı sayıda iş parçacığı vardır ve yüksek yük durumlarında kullanılabilir tüm iş parçacıkları kullanılıyor olabilir. Bu durumda, iş parçacıkları serbest bırakılıncaya kadar sunucu yeni istekleri işleyebilir. Senkron kodla, G/Ç'nin tamamlanmasını bekledikleri için birçok iş parçacığı aslında herhangi bir iş yapmasalar da bağlanabilir. Bir işlem G/Ç'nin tamamlanmasını beklerken, iş parçacığı sunucunun diğer istekleri işlemek için kullanması için serbest bırakılır. Sonuç olarak, eşzamanlı kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucunun gecikmeden daha fazla trafiği işlemesi sağlanır.

Asynchronous kodu çalışma zamanında ek yükü küçük bir miktar tanıtmak yok, ancak düşük trafik durumları için performans isabet ihmal edilebilir, yüksek trafik durumları için ise, potansiyel performans geliştirme önemli.

Aşağıdaki kodda, `async` anahtar kelime, `Task<T>` iade `await` değeri, `ToListAsync` anahtar kelime ve yöntem kodu eşzamanlı olarak yürütmeyi yapar.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* Anahtar `async` kelime derleyiciye yöntem gövdesinin bölümleri için geri aramalar oluşturmasını `Task<IActionResult>` ve döndürülen nesneyi otomatik olarak oluşturmasını söyler.

* İade türü, `Task<IActionResult>` türünden `IActionResult`bir sonucu olan devam eden çalışmayı temsil eder.

* Anahtar `await` kelime derleyicinin yöntemi iki bölüme bölmesine neden olur. İlk bölüm, eş zamanlı olarak başlatılan işlemle sona erer. İkinci bölüm, işlem tamamlandığında çağrılan bir geri arama yöntemine konur.

* `ToListAsync`uzantı yönteminin `ToList` eşzamanlı sürümüdür.

Varlık Çerçevesi'ni kullanan eşzamanlı kod yazarken dikkat edilmesi gereken bazı noktalar:

* Yalnızca sorguların veya komutların veritabanına gönderilmesine neden olan ifadeler eşsenkronize olarak yürütülür. Buna, örneğin, `ToListAsync`, `SingleOrDefaultAsync`, `SaveChangesAsync`ve . Örneğin, bir `IQueryable`şeyi değiştiren ifadeler `var students = context.Students.Where(s => s.LastName == "Davolio")`içermez.

* EF bağlamı iş parçacığı güvenli değildir: paralel olarak birden çok işlem yapmaya çalışmayın. Herhangi bir async EF yöntemini `await` çağırdığınızda, her zaman anahtar kelimeyi kullanın.

* Async kodunun performans avantajlarından yararlanmak istiyorsanız, kullandığınız kitaplık paketlerinin (sayfa lama gibi) sorguların veritabanına gönderilmesine neden olan Varlık Çerçevesi yöntemlerini aramaları durumunda da async kullandığından emin olun.

.NET'teki eşzamanlı programlama hakkında daha fazla bilgi için [Async Genel Bakış'a](/dotnet/articles/standard/async)bakın.

## <a name="get-the-code"></a>Kodu alma

[Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Core MVC web uygulaması ASP.NET oluşturuldu
> * Site stilini ayarlama
> * EF Core NuGet paketleri hakkında bilgi edinilmiş
> * Veri modelini oluşturma
> * Veritabanı bağlamını oluşturma
> * Okul Bağlamını Kaydetti
> * Test verileri ile başharfe çevrilmiş DB
> * Oluşturulan denetleyici ve görünümler
> * Veritabanı görüntülendi

Aşağıdaki öğreticide, temel CRUD (oluşturma, okuma, güncelleme, silme) işlemlerini nasıl yapacağınızı öğreneceksiniz.

Temel CRUD (oluşturma, okuma, güncelleme, silme) işlemlerinin nasıl yapılacağını öğrenmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Temel CRUD işlevselliğini uygulayın](crud.md)

