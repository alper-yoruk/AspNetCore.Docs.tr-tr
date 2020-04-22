<!-- THIS INCLUDE USED BY MVC AND RP -->
<span data-ttu-id="b052a-101">`Movie` Sınıfa aşağıdaki özellikleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b052a-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="b052a-102">Sınıf `Movie` şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="b052a-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="b052a-103">Alan `ID` birincil anahtar için veritabanı tarafından gereklidir.</span><span class="sxs-lookup"><span data-stu-id="b052a-103">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="b052a-104">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği, veri türünü (Tarih) belirtir.</span><span class="sxs-lookup"><span data-stu-id="b052a-104">`[DataType(DataType.Date)]`:  The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (Date).</span></span> <span data-ttu-id="b052a-105">Bu öznitelik ile:</span><span class="sxs-lookup"><span data-stu-id="b052a-105">With this attribute:</span></span>

  * <span data-ttu-id="b052a-106">Kullanıcının tarih alanına saat bilgilerini girmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="b052a-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="b052a-107">Yalnızca tarih görüntülenir, saat bilgisi değil.</span><span class="sxs-lookup"><span data-stu-id="b052a-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="b052a-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) daha sonraki bir öğretici ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="b052a-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>
