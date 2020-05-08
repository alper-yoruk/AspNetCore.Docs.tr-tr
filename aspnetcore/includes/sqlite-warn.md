> [!NOTE]
> 
> **SQLite sınırlamaları**
>
> Bu öğretici, mümkün olduğunda Entity Framework Core *geçişleri* özelliğini kullanır. Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir. Bununla birlikte, geçişler yalnızca veritabanı altyapısının desteklediği değişiklik türlerini ve SQLite 'un şema değiştirme özellikleri sınırlıdır. Örneğin, bir sütun ekleme desteklenir, ancak bir sütunu kaldırmak desteklenmez. Bir sütunu kaldırmak için bir geçiş oluşturulduysa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur. 
>
> SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır. Kod, `Up` bir geçiş için ve `Down` yöntemlerine gidip şunları içerir:
>
> * Yeni bir tablo oluşturuluyor.
> * Eski tablodaki veriler yeni tabloya kopyalanıyor.
> * Eski tablo bırakılıyor.
> * Yeni tablo yeniden adlandırılıyor.
>
> Bu türün veritabanına özgü kodu yazma, Bu öğreticinin kapsamı dışındadır. Bunun yerine, bu öğretici bir geçiş uygulama girişimi başarısız olduğunda veritabanını bırakır ve yeniden oluşturur. Daha fazla bilgi için aşağıdaki kaynaklara bakın:
>
> * [SQLite EF Core veritabanı sağlayıcısı sınırlamaları](/ef/core/providers/sqlite/limitations)
> * [Geçiş kodunu özelleştirme](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Veri çekirdeği oluşturma](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE ifadesi](https://sqlite.org/lang_altertable.html)