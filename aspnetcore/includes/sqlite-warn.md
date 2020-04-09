> [!NOTE]
> 
> **SQLite sınırlamaları**
>
> Bu öğretici, mümkünse Varlık Framework Core *geçişleri* özelliğini kullanır. Geçişler, veri modelindeki değişiklikleri eşleşecek şekilde veritabanı şemasını güncelleştirir. Ancak, geçişler yalnızca veritabanı altyapısının desteklediği değişiklik türlerini yapar ve SQLite'ın şema değiştirme yetenekleri sınırlıdır. Örneğin, sütun eklemek desteklenir, ancak sütunun kaldırılması desteklenmez. Bir sütunu kaldırmak için bir `ef migrations add` geçiş oluşturulursa, komut başarılı olur, ancak `ef database update` komut başarısız olur. 
>
> SQLite sınırlamaları için geçici çözüm, tablodaki bir şey değiştiğinde tabloyu yeniden oluşturmak için geçişler kodunu el ile yazmaktır. Kod, bir geçiş `Up` `Down` için ve yöntemleri gider ve içerir:
>
> * Yeni bir tablo oluşturma.
> * Eski tablodaki verileri yeni tabloya kopyalama.
> * Eski masayı düşürüyor.
> * Yeni tablonun adını yeniden adlendirmek.
>
> Bu tür veritabanına özgü kod yazmak bu öğreticinin kapsamı dışındadır. Bunun yerine, bu öğretici bir geçiş uygulama girişimi başarısız olduğunda veritabanını düşürür ve yeniden oluşturur. Daha fazla bilgi için aşağıdaki kaynaklara bakın:
>
> * [SQLite EF Çekirdek Veritabanı Sağlayıcısı Sınırlamaları](/ef/core/providers/sqlite/limitations)
> * [Geçiş kodunu özelleştirme](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Veri tohumlama](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE deyimi](https://sqlite.org/lang_altertable.html)