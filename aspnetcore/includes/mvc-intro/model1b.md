`Movie` Sınıfa aşağıdaki özellikleri ekleyin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

Sınıf `Movie` şunları içerir:

* Birincil `Id` anahtar için veritabanı tarafından gerekli olan alan.
* `[DataType(DataType.Date)]`: [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği, veri türünü belirtir`Date`( ). Bu öznitelik ile:

  * Kullanıcının tarih alanına saat bilgilerini girmesi gerekmez.
  * Yalnızca tarih görüntülenir, saat bilgisi değil.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) daha sonraki bir öğretici ele alınmıştır.