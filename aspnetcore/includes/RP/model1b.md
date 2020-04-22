<!-- THIS INCLUDE USED BY MVC AND RP -->
`Movie` Sınıfa aşağıdaki özellikleri ekleyin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Sınıf `Movie` şunları içerir:

* Alan `ID` birincil anahtar için veritabanı tarafından gereklidir.
* `[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği, veri türünü (Tarih) belirtir. Bu öznitelik ile:

  * Kullanıcının tarih alanına saat bilgilerini girmesi gerekmez.
  * Yalnızca tarih görüntülenir, saat bilgisi değil.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) daha sonraki bir öğretici ele alınmıştır.
