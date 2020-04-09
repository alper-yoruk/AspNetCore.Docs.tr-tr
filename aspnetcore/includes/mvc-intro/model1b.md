<span data-ttu-id="e35c7-101">`Movie` Sınıfa aşağıdaki özellikleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e35c7-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e35c7-102">Sınıf `Movie` şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e35c7-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="e35c7-103">Birincil `Id` anahtar için veritabanı tarafından gerekli olan alan.</span><span class="sxs-lookup"><span data-stu-id="e35c7-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="e35c7-104">`[DataType(DataType.Date)]`: [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği, veri türünü belirtir`Date`( ).</span><span class="sxs-lookup"><span data-stu-id="e35c7-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e35c7-105">Bu öznitelik ile:</span><span class="sxs-lookup"><span data-stu-id="e35c7-105">With this attribute:</span></span>

  * <span data-ttu-id="e35c7-106">Kullanıcının tarih alanına saat bilgilerini girmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e35c7-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e35c7-107">Yalnızca tarih görüntülenir, saat bilgisi değil.</span><span class="sxs-lookup"><span data-stu-id="e35c7-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e35c7-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) daha sonraki bir öğretici ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e35c7-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>