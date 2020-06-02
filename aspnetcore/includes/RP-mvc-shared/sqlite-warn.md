
> [!NOTE]
> Bu öğreticide, mümkün olduğunda Entity Framework Core *geçişleri* özelliğini kullanırsınız. Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir. Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır. Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez. Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur. Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz. Bunun yerine, şema değiştiğinde veritabanını bırakıp yeniden oluşturursunuz.
>
>SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır. Tablo yeniden oluşturma şunları içerir:
>
>* Yeni bir tablo oluşturuluyor.
>* Eski tablodaki veriler yeni tabloya kopyalanıyor.
>* Eski tablo bırakılıyor.
>* Yeni tablo yeniden adlandırılıyor.
>
>Daha fazla bilgi için aşağıdaki kaynaklara bakın:
>
> * [SQLite EF Core veritabanı sağlayıcısı sınırlamaları](/ef/core/providers/sqlite/limitations)
> * [Geçiş kodunu özelleştirme](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Veri çekirdeği oluşturma](/ef/core/modeling/data-seeding)
  * [SQLite ALTER TABLE ifadesi](https://sqlite.org/lang_altertable.html)