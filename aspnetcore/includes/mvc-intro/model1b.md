<span data-ttu-id="05a60-101">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="05a60-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="05a60-102">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="05a60-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="05a60-103">`Id`Birincil anahtar için veritabanı için gereken alan.</span><span class="sxs-lookup"><span data-stu-id="05a60-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="05a60-104">`[DataType(DataType.Date)]`: [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="05a60-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="05a60-105">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="05a60-105">With this attribute:</span></span>

  * <span data-ttu-id="05a60-106">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="05a60-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="05a60-107">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="05a60-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="05a60-108">[Veri açıklamaları](/dotnet/api/system.componentmodel.dataannotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="05a60-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>