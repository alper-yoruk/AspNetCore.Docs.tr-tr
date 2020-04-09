
> [!NOTE]
> Bu öğretici için, mümkün olduğunca Varlık Framework Core *geçişleri* özelliğini kullanırsınız. Geçişler, veri modelindeki değişiklikleri eşleşecek şekilde veritabanı şemasını güncelleştirir. Ancak, geçişler yalnızca EF Core sağlayıcısının desteklediği türde değişiklikler yapabilir ve SQLite sağlayıcısının yetenekleri sınırlıdır. Örneğin, sütun eklemek desteklenir, ancak sütunun kaldırılması veya değiştirilmesi desteklenmez. Bir sütunu kaldırmak veya değiştirmek için `ef migrations add` bir geçiş oluşturulursa, komut başarılı olur, ancak `ef database update` komut başarısız olur. Bu sınırlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişler kullanmaz. Bunun yerine, şema değiştiğinde veritabanını bırakır ve yeniden oluşturursunuz.
>
>SQLite sınırlamaları için geçici çözüm, tablodaki bir şey değiştiğinde tabloyu yeniden oluşturmak için geçişler kodunu el ile yazmaktır. Tablo yeniden oluşturma içerir:
>
>* Yeni bir tablo oluşturma.
>* Eski tablodaki verileri yeni tabloya kopyalama.
>* Eski masayı düşürüyor.
>* Yeni tablonun adını yeniden adlendirmek.
>
>Daha fazla bilgi için aşağıdaki kaynaklara bakın:
>
> * [SQLite EF Çekirdek Veritabanı Sağlayıcısı Sınırlamaları](/ef/core/providers/sqlite/limitations)
> * [Geçiş kodunu özelleştirme](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Veri tohumlama](/ef/core/modeling/data-seeding)
  * [SQLite ALTER TABLE deyimi](https://sqlite.org/lang_altertable.html)