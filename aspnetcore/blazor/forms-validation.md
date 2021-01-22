---
title: BlazorForms ve doğrulama ASP.NET Core
author: guardrex
description: İçindeki form ve alan doğrulama senaryolarını nasıl kullanacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 1287ab5ce61e58848329c96393c3ee8c37610245
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658696"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a>BlazorForms ve doğrulama ASP.NET Core

[Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/)ve [Luke Latham](https://github.com/guardrex)

Formlar ve doğrulama, Blazor [veri ek açıklamaları](xref:mvc/models/validation)kullanılarak desteklenir.

Aşağıdaki `ExampleModel` tür, veri ek açıklamalarını kullanarak doğrulama mantığını tanımlar:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Bir form, bileşeni kullanılarak tanımlanır <xref:Microsoft.AspNetCore.Components.Forms.EditForm> . Aşağıdaki form tipik öğeleri, bileşenleri ve Razor kodu gösterir:

```razor
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }
}
```

Yukarıdaki örnekte:

* Form, `name` türünde tanımlanan doğrulamayı kullanarak alanda Kullanıcı girişini doğrular `ExampleModel` . Model bileşen `@code` bloğunda oluşturulur ve özel bir alanda ( `exampleModel` ) tutulur. Alanı, `Model` öğesinin özniteliğine atanır `<EditForm>` .
* <xref:Microsoft.AspNetCore.Components.Forms.InputText>Bileşenin `@bind-Value` bağlamaları:
  * Model özelliği ( `exampleModel.Name` ) <xref:Microsoft.AspNetCore.Components.Forms.InputText> bileşen `Value` özelliğine. Özellik bağlama hakkında daha fazla bilgi için bkz <xref:blazor/components/data-binding#binding-with-component-parameters> ..
  * Bileşen özelliğine bir değişiklik olayı temsilcisi <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` .
* <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [Doğrulayıcı bileşeni](#validator-components) , veri ek açıklamalarını kullanarak doğrulama desteğini iliştirir.
* <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Bileşen doğrulama iletilerini özetler.
* `HandleValidSubmit` Form başarıyla gönderdiğinde tetiklenir (doğrulamayı geçirir).

## <a name="built-in-forms-components"></a>Yerleşik Forms bileşenleri

Bir dizi yerleşik bileşen, Kullanıcı girişini almak ve doğrulamak için kullanılabilir. Girişler değiştirildiklerinde ve bir form gönderildiğinde onaylanır. Kullanılabilir giriş bileşenleri aşağıdaki tabloda gösterilmiştir.

::: moniker range=">= aspnetcore-5.0"

| Giriş bileşeni | Olarak işlendi&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| [`InputFile`](xref:blazor/file-uploads) | `<input type="file">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| Giriş bileşeni | Olarak işlendi&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> `InputRadio`Ve `InputRadioGroup` bileşenleri, ASP.NET Core 5,0 veya üzeri sürümlerde kullanılabilir. Daha fazla bilgi için bu makalenin 5,0 veya sonraki bir sürümünü seçin.

::: moniker-end

Dahil olmak üzere tüm giriş bileşenleri, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> rastgele öznitelikleri destekler. Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik işlenmiş HTML öğesine eklenir.

Giriş bileşenleri, alan CSS sınıfının alan durumunu yansıtacak şekilde güncelleştirilmesi dahil olmak üzere, bir alan değiştirildiğinde doğrulamak için varsayılan davranışı sağlar. Bazı bileşenler, yararlı ayrıştırma mantığını içerir. Örneğin, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> düzeltilemez değerleri <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> doğrulama hatası olarak kaydederek, düzeltilemez değerleri düzgün şekilde işleyin. Null değerleri kabul edebilecek türler, hedef alanın null değer alabilme durumunu da destekler (örneğin, `int?` ).

Aşağıdaki tür, daha `Starship` önce daha büyük bir özellik kümesi ve daha önceki veri açıklamalarını kullanarak doğrulama mantığını tanımlar `ExampleModel` :

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

Önceki örnekte, `Description` hiçbir veri ek açıklaması mevcut olmadığından isteğe bağlıdır.

Aşağıdaki form, modelde tanımlanan doğrulamayı kullanarak Kullanıcı girişini doğrular `Starship` :

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> Hangi alanların değiştirildiği ve geçerli doğrulama iletileri de dahil olmak üzere düzenleme işlemiyle ilgili meta verileri izleyen [basamaklı bir değer](xref:blazor/components/cascading-values-and-parameters) olarak oluşturur.

Ya **da** ' a atayın <xref:Microsoft.AspNetCore.Components.Forms.EditContext>  <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Components.Forms.EditForm> . Her ikisinin atanması desteklenmez ve bir **çalışma zamanı hatası** oluşturur.

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>Geçerli ve geçersiz form gönderimi için uygun olaylar sağlar:

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>Doğrulamayı tetiklemek ve alan değerlerini denetlemek için özel kod kullanmak üzere kullanın.

Aşağıdaki örnekte:

* `HandleSubmit` **`Submit`** Düğme seçildiğinde Yöntem yürütülür.
* Form çağırarak onaylanır <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> .
* Doğrulama sonucuna bağlı olarak ek kod yürütülür. İş mantığını atanan yöntemine yerleştirin <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> .

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> Framework API 'SI, doğrulama iletilerini doğrudan bir öğesinden temizlemek için yok <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . Bu nedenle, genellikle bir form içinde yeni bir doğrulama iletileri eklemeniz önerilmez <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> . Doğrulama iletilerini yönetmek için, bu makalede açıklandığı gibi [iş mantığı doğrulama kodunuzla](#business-logic-validation)bir [Doğrulayıcı bileşeni](#validator-components) kullanın.

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a>Görünen ad desteği

*Bu bölüm, .NET 5 Release Candidate 1 (RC1) veya sonraki sürümlerde ASP.NET Core için geçerlidir.*

Aşağıdaki yerleşik bileşenler parametresiyle görünen adları destekler `DisplayName` :

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

Aşağıdaki `InputDate` bileşen örneğinde:

* Görünen ad ( `DisplayName` ) olarak ayarlanır `birthday` .
* Bileşen, `BirthDate` bir tür olarak özelliğe bağlanır `DateTime` .

```razor
<InputDate @bind-Value="BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

Kullanıcı bir tarih değeri sağlamıyorsa, doğrulama hatası şöyle görünür:

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a>Doğrulayıcı bileşenleri

Doğrulayıcı bileşenleri, form için bir için yöneterek form doğrulamasını destekler <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .

BlazorFramework, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [doğrulama özniteliklerine (veri ek açıklamaları)](xref:mvc/models/validation#validation-attributes)göre formlara doğrulama desteği eklemek için bileşeni sağlar. Farklı formlar için doğrulama mesajlarını aynı sayfada veya aynı formda farklı form işleme adımlarında işlemek için özel Doğrulayıcı bileşenleri oluşturun, örneğin, istemci tarafı doğrulama ve ardından sunucu tarafı doğrulama. Bu bölümde gösterilen Doğrulayıcı bileşeni örneği, `CustomValidator` Bu makalenin aşağıdaki bölümlerinde kullanılır:

* [İş mantığı doğrulaması](#business-logic-validation)
* [Sunucu doğrulaması](#server-validation)

> [!NOTE]
> Özel veri ek açıklaması doğrulama öznitelikleri, birçok durumda özel Doğrulayıcı bileşenleri yerine kullanılabilir. Formun modeline uygulanan özel öznitelikler bileşenin kullanımıyla etkinleştirilir <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> . Sunucu tarafı doğrulama ile kullanıldığında, modele uygulanan özel özniteliklerin sunucuda çalıştırılabilir olması gerekir. Daha fazla bilgi için bkz. <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

Bir doğrulayıcı bileşeni oluştur <xref:Microsoft.AspNetCore.Components.ComponentBase> :

* Form, <xref:Microsoft.AspNetCore.Components.Forms.EditContext> bileşenin geçişli bir [parametresidir](xref:blazor/components/cascading-values-and-parameters) .
* Doğrulayıcı bileşeni başlatıldığında, <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> form hatalarının geçerli bir listesini korumak için yeni bir oluşturulur.
* İleti deposu, form bileşenindeki geliştirici kodu yöntemi çağırdığında hata alır `DisplayErrors` . Hatalar, `DisplayErrors` içindeki yöntemine geçirilir [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) . Sözlükte, anahtar bir veya daha fazla hata içeren form alanının adıdır. Değer, hata listesidir.
* Aşağıdakilerden herhangi biri oluştuğunda iletiler temizlenir:
  * <xref:Microsoft.AspNetCore.Components.Forms.EditContext>Olay oluşturulduğunda doğrulama istenir <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> . Tüm hatalar temizlenir.
  * Olay ortaya çıktığında formdaki bir alan değişir <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> . Yalnızca alanın hataları temizlenir.
  * `ClearErrors`Yöntemi geliştirici kodu tarafından çağrılır. Tüm hatalar temizlenir.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

public class CustomValidator : ComponentBase
{
    private ValidationMessageStore messageStore;

    [CascadingParameter]
    private EditContext CurrentEditContext { get; set; }

    protected override void OnInitialized()
    {
        if (CurrentEditContext == null)
        {
            throw new InvalidOperationException(
                $"{nameof(CustomValidator)} requires a cascading " +
                $"parameter of type {nameof(EditContext)}. " +
                $"For example, you can use {nameof(CustomValidator)} " +
                $"inside an {nameof(EditForm)}.");
        }

        messageStore = new ValidationMessageStore(CurrentEditContext);

        CurrentEditContext.OnValidationRequested += (s, e) => 
            messageStore.Clear();
        CurrentEditContext.OnFieldChanged += (s, e) => 
            messageStore.Clear(e.FieldIdentifier);
    }

    public void DisplayErrors(Dictionary<string, List<string>> errors)
    {
        foreach (var err in errors)
        {
            messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
        }

        CurrentEditContext.NotifyValidationStateChanged();
    }

    public void ClearErrors()
    {
        messageStore.Clear();
        CurrentEditContext.NotifyValidationStateChanged();
    }
}
```

## <a name="business-logic-validation"></a>İş mantığı doğrulaması

İş mantığı doğrulaması, bir sözlükte form hataları alan bir [Doğrulayıcı bileşeniyle](#validator-components) gerçekleştirilebilir.

Aşağıdaki örnekte:

* `CustomValidator`Bu makalenin [Doğrulayıcı bileşenleri](#validator-components) bölümündeki bileşen kullanılır.
* Kullanıcı, gönderme `Description` `Defense` sınıflandırmasını () seçerse, bu doğrulama, alıcının açıklaması () için bir değer gerektirir `Classification` .

, Bileşende doğrulama iletileri ayarlandığında, bu, doğrulayıcının öğesine eklenir ve şu şekilde <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> gösterilir <xref:Microsoft.AspNetCore.Components.Forms.EditForm> :

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> [Doğrulama bileşenlerinin](#validator-components)kullanılmasına alternatif olarak, veri ek açıklaması doğrulama öznitelikleri de kullanılabilir. Formun modeline uygulanan özel öznitelikler bileşenin kullanımıyla etkinleştirilir <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> . Sunucu tarafı doğrulama ile kullanıldığında, özniteliklerin sunucuda çalıştırılabilir olması gerekir. Daha fazla bilgi için bkz. <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

## <a name="server-validation"></a>Sunucu doğrulaması

Sunucu doğrulama, bir sunucu [Doğrulayıcı bileşeniyle](#validator-components)gerçekleştirilebilir:

* Bileşeniyle birlikte istemci tarafı doğrulamayı işleyin <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .
* Form, istemci tarafı doğrulaması ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> çağrıldığında) geçtiğinde, <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> Form işleme için bir arka uç sunucu API 'sine gönderin.
* Sunucuda işlem modeli doğrulaması.
* Sunucu API 'SI, geliştirici tarafından sağlanan yerleşik Framework veri ek açıklamaları doğrulama ve özel doğrulama mantığını içerir. Sunucuda doğrulama başarılı olursa, formu işleyin ve bir başarı durum kodu geri gönderin (*200-Tamam*). Doğrulama başarısız olursa, bir hata durum kodu (*400-hatalı istek*) ve alan doğrulama hatalarını döndürün.
* Başarı durumunda formu devre dışı bırakın ya da hataları görüntüleyin.

Aşağıdaki örnek temel alınarak verilmiştir:

* Barındırılan Blazor [ Blazor proje şablonu](xref:blazor/hosting-models#blazor-webassembly)tarafından oluşturulan barındırılan bir çözüm. Örnek, Blazor [güvenlik ve Identity belgelerde](xref:blazor/security/webassembly/index#implementation-guidance)açıklanan Güvenli barındırılan çözümlerin herhangi biriyle birlikte kullanılabilir.
* Önceki [yerleşik form bileşenleri](#built-in-forms-components) bölümünde *Starfleet Stargeme veritabanı* formu örneği.
* BlazorFramework <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> bileşeni.
* `CustomValidator` [Doğrulayıcı bileşenleri](#validator-components) bölümünde gösterilen bileşen.

Aşağıdaki örnekte, sunucu API 'SI, `Description` Kullanıcı `Defense` teslim sınıflandırmasını () seçerse, alıcının açıklaması () için bir değer sağlandığını doğrular `Classification` .

`Starship` `Shared` Hem istemci hem de sunucu uygulamalarının modeli kullanabilmesi için modeli çözümün projesine yerleştirin. Model veri ek açıklamaları gerektirdiğinden, [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) projenin proje dosyasına bir paket başvurusu ekleyin `Shared` :

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

Paketin en son önizleme dışı sürümünü öğrenmek için [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations)adresindeki paket **sürümü geçmişine** bakın.

Sunucu API 'SI projesinde, starsevkiyat doğrulama isteklerini () işlemek için bir denetleyici ekleyin `Controllers/StarshipValidation.cs` ve başarısız doğrulama iletilerini geri döndürün:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace {ASSEMBLY NAME}.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {Message}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

Önceki örnekte, yer tutucu `{ASSEMBLY NAME}` uygulamanın derleme adıdır (örneğin, `BlazorSample.Server` ).

Sunucuda bir model bağlama doğrulama hatası oluştuğunda, [`ApiController`](xref:web-api/index) ( <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> ) normalde ile [varsayılan hatalı istek yanıtı](xref:web-api/index#default-badrequest-response) döndürür <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> . Yanıt, şu örnekte gösterildiği gibi yalnızca doğrulama hatalarından daha fazla veri içerir: *Starfleet Starsevk veritabanı* formunun tüm alanları gönderilmediğinde ve form doğrulama başarısız olduğunda:

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

Sunucu API 'SI önceki varsayılan JSON yanıtını döndürürse, istemcinin düğümün alt öğelerini elde etmek için yanıtı ayrıştırması mümkündür `errors` . Ancak, dosyayı ayrıştırmaya uygun değildir. JSON ayrıştırılırken, <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) form doğrulama hatası işleme hataları üretmek için çağrıldıktan sonra ek kod gerekir. İdeal olarak, sunucu API 'SI yalnızca doğrulama hatalarını döndürmelidir:

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

Sunucu API 'sinin yanıtını yalnızca doğrulama hatalarını döndürecek şekilde değiştirmek için, içinde ile açıklanmakta olan eylemlerde çağrılan temsilciyi değiştirin <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> `Startup.ConfigureServices` . API uç noktası ( `/StarshipValidation` ) için ile bir döndürür <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> . Diğer API uç noktaları için, nesne sonucunu yeni bir ile döndürerek varsayılan davranışı koruyun <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> :

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

Daha fazla bilgi için bkz. <xref:web-api/handle-errors#validation-failure-error-response>.

İstemci projesinde, [Doğrulayıcı bileşenleri](#validator-components) bölümünde gösterilen Doğrulayıcı bileşenini ekleyin.

İstemci projesinde, *Starfleet Başlangıçgönder veritabanı* formu, bileşen yardımı ile sunucu doğrulama hatalarını gösterecek şekilde güncelleştirilir `CustomValidator` . Sunucu API 'SI doğrulama iletileri döndürdüğünde, bileşen öğesine eklenir `CustomValidator` <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> . Hatalar, formun <xref:Microsoft.AspNetCore.Components.Forms.EditContext> form tarafından görüntülenmek üzere tarafından kullanılabilir <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> :

```razor
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {Message}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> [Doğrulama bileşenlerine](#validator-components)alternatif olarak, veri ek açıklaması doğrulama öznitelikleri de kullanılabilir. Formun modeline uygulanan özel öznitelikler bileşenin kullanımıyla etkinleştirilir <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> . Sunucu tarafı doğrulama ile kullanıldığında, özniteliklerin sunucuda çalıştırılabilir olması gerekir. Daha fazla bilgi için bkz. <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

> [!NOTE]
> Bu bölümdeki sunucu tarafı doğrulama yaklaşımı, Blazor WebAssembly Bu belge kümesindeki barındırılan her türlü çözüm örneği için uygundur:
>
> * [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [Identity Server](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a>Giriş olayına göre InputText

<xref:Microsoft.AspNetCore.Components.Forms.InputText>Olayı yerine olayını kullanan özel bir bileşen oluşturmak için bileşenini kullanın `input` `change` .

Aşağıdaki örnekte, `CustomInputText` bileşen Framework `InputText` bileşenini devralır ve olaya olay bağlamayı ayarlar `oninput` .

`Shared/CustomInputText.razor`:

```razor
@inherits InputText

<input @attributes="AdditionalAttributes" class="@CssClass" 
    @bind="CurrentValueAsString" @bind:event="oninput" />
```

`CustomInputText`Bileşen her yerde kullanılabilir <xref:Microsoft.AspNetCore.Components.Forms.InputText> :

`Pages/TestForm.razor`:

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a>Radyo düğmeleri

::: moniker range=">= aspnetcore-5.0"

`InputRadio` `InputRadioGroup` Radyo düğmesi grubu oluşturmak için bileşeni olan bileşenleri kullanın. Aşağıdaki örnekte, Özellikler `Starship` [yerleşik Forms bileşenleri](#built-in-forms-components) bölümünde açıklanan modele eklenir:

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

Aşağıdakileri uygulamaya ekleyin `enums` . ' İ barındıracak yeni bir dosya oluşturun `enums` veya `enums` `Starship.cs` dosyayı dosyasına ekleyin. `enums` `Starship` Model ve *Starfleet başlangıçgönder veritabanı* formu için erişilebilir yapın:

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, VirginGalactic, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

[Yerleşik Forms bileşenleri](#built-in-forms-components) bölümünde açıklanan *Starfleet starsevkiyat veritabanı* formunu güncelleştirin. Üretilecek bileşenleri ekleyin:

* Sevk üreticisi için bir radyo düğmesi grubu.
* Sevk rengi ve motoru için iç içe bir radyo düğmesi grubu.

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> `Name`Atlanırsa, `InputRadio` Bileşenler en son üst öğesine göre gruplandırılır.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Bir formda radyo düğmeleriyle çalışırken, radyo düğmeleri bir grup olarak değerlendirildiğinden veri bağlama diğer öğelerden farklı işlenir. Her radyo düğmesinin değeri sabittir, ancak radyo düğmesi grubunun değeri seçili radyo düğmesinin değeridir. Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:

* Radyo düğmesi grubu için veri bağlamayı işleyin.
* Özel bir bileşen kullanarak doğrulamayı destekler `InputRadio` .

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

Aşağıdaki, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> `InputRadio` kullanıcıdan bir derecelendirme almak ve doğrulamak için önceki bileşeni kullanır:

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a>`<select>`Öğe seçeneklerini C# nesne değerlerine bağlama `null`

Bir `<select>` öğe seçeneği değerini C# nesne değeri olarak göstermek için herhangi bir mümkün değildir `null` , çünkü:

* HTML özniteliklerinin değerleri olamaz `null` . HTML olarak en yakın eşdeğeri, `null` ÖĞESINDEN html özniteliğinin yokluğunda olur `value` `<option>` .
* `<option>`Özniteliği olmayan bir seçerken `value` , tarayıcı değeri bu öğenin *metin içeriği* olarak değerlendirir `<option>` .

BlazorFramework, aşağıdakileri içereceği varsayılan davranışı bastırmak için denenmez:

* Çerçevede özel durum geçici çözümleri zinciri oluşturma.
* Geçerli çerçeve davranışında son değişiklikler.

::: moniker range=">= aspnetcore-5.0"

HTML 'deki en büyük plausible `null` eşdeğeri boş bir *dizedir* `value` . BlazorÇerçeve, `null` bir değerine iki yönlü bağlama yönelik boş dize dönüştürmelerini işler `<select>` .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

BlazorÇerçeve, `null` iki yönlü bir değere bağlamayı denerken boş dize dönüştürmeleri için otomatik olarak işleme yapmaz `<select>` . Daha fazla bilgi için bkz. [ `<select>` null değere bağlamayı çözme (DotNet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).

::: moniker-end

## <a name="validation-support"></a>Doğrulama desteği

<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Bileşen, Basamaklandırılan veri açıklamalarını kullanarak doğrulama desteğini iliştirir <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . Veri ek açıklamalarını kullanarak doğrulama desteğinin etkinleştirilmesi bu açık hareketi gerektirir. Veri ek açıklamalarıyla farklı bir doğrulama sistemi kullanmak için, öğesini <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> özel bir uygulamayla değiştirin. ASP.NET Core uygulama, başvuru kaynağında İnceleme için kullanılabilir: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) . Başvuru kaynağına yapılan önceki bağlantılar, deponun `master` dalından kod sağlar ve bu, ürün biriminin ASP.NET Core sonraki sürümü için geçerli geliştirmeyi temsil eder. Farklı bir sürümün dalını seçmek için GitHub dal seçicisini (örneğin `release/3.1` ) kullanın.

Blazor iki tür doğrulama gerçekleştirir:

* *Alan doğrulama* , Kullanıcı bir alanın dışına eklendiğinde gerçekleştirilir. Alan doğrulama sırasında, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> bileşen bildirilen tüm doğrulama sonuçlarını alanla ilişkilendirir.
* Kullanıcı formu gönderdiğinde *model doğrulaması* gerçekleştirilir. Model doğrulama sırasında, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Bileşen, doğrulama sonucunun raporlandığı üye adına göre alanı belirlemeyi dener. Tek bir üyeyle ilişkilendirilmeyen doğrulama sonuçları, bir alan yerine modeliyle ilişkilendirilir.

### <a name="validation-summary-and-validation-message-components"></a>Doğrulama özeti ve doğrulama Iletisi bileşenleri

<xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Bileşen, [doğrulama özeti etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)'na benzer olan tüm doğrulama iletilerini özetler:

```razor
<ValidationSummary />
```

Parametresi ile belirli bir model için çıkış doğrulama iletileri `Model` :
  
```razor
<ValidationSummary Model="@starship" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Bileşeni, [doğrulama Iletisi etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)'na benzer şekilde belirli bir alan için doğrulama iletileri görüntüler. Özniteliği ile doğrulama için alanı `For` ve model özelliğini adlandırırken bir lambda ifadesini belirtin:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Ve <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> bileşenleri, rastgele öznitelikleri destekler. Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik oluşturulan `<div>` or `<ul>` öğesine eklenir.

Uygulamanın stil sayfasındaki (veya) doğrulama iletilerinin stilini denetleyin `wwwroot/css/app.css` `wwwroot/css/site.css` . Varsayılan `validation-message` sınıf, doğrulama iletilerinin metin rengini kırmızı olarak ayarlar:

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a>Özel doğrulama öznitelikleri

Bir doğrulama sonucunun [özel bir doğrulama özniteliği](xref:mvc/models/validation#custom-attributes)kullanılırken bir alanla doğru şekilde ilişkilendirildiğinden emin olmak için, şunu oluştururken doğrulama bağlamını geçirin <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> <xref:System.ComponentModel.DataAnnotations.ValidationResult> :

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType>, `null` değeridir. Yönteminde doğrulama için ekleme Hizmetleri `IsValid` desteklenmiyor.

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a>Özel doğrulama sınıfı öznitelikleri

Özel doğrulama sınıfı adları, [önyükleme](https://getbootstrap.com/)gibi CSS çerçeveleri ile tümleştirilirken faydalıdır. Özel doğrulama sınıfı adlarını belirtmek için, öğesinden türetilmiş bir sınıf oluşturun `FieldCssClassProvider` ve örneği üzerinde sınıfı ayarlayın <xref:Microsoft.AspNetCore.Components.Forms.EditContext> :

```csharp
var editContext = new EditContext(model);
editContext.SetFieldCssClassProvider(new MyFieldClassProvider());

...

private class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext, 
        in FieldIdentifier fieldIdentifier)
    {
        var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

        return isValid ? "good field" : "bad field";
    }
}
```

::: moniker-end

### <a name="no-locblazor-data-annotations-validation-package"></a>Blazor veri ek açıklamaları doğrulama paketi

, [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) Bileşeni kullanarak doğrulama deneyimini boşlukları dolduran bir pakettir <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> . Paket şu anda *deneysel*.

> [!NOTE]
> [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)Paketin en son sürüm *adayı* sürümü [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)adresinde bulunur. Şu anda *deneysel* yayın aday paketini kullanmaya devam edin. Paketin derlemesi, gelecekteki bir sürümdeki çerçeveye veya çalışma zamanına taşınmış olabilir. Daha fazla güncelleştirme için [Duyurular GitHub deposu](https://github.com/aspnet/Announcements), [DotNet/aspnetcore GitHub deposu](https://github.com/dotnet/aspnetcore)veya bu konu başlığı bölümüne bakın.

::: moniker range="< aspnetcore-5.0"

### <a name="compareproperty-attribute"></a>`[CompareProperty]` özniteliği

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Doğrulama sonucunu belirli bir üyeyle ilişkilendirmediği için bileşen ile iyi çalışmaz. Bu, alan düzeyi doğrulama ve tüm modelin bir gönderme sırasında doğrulanması arasındaki tutarsız davranışa neden olabilir. [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *Deneysel* paket, `ComparePropertyAttribute` Bu sınırlamalar etrafında çalışabilen ek bir doğrulama özniteliği sunar. Bir Blazor uygulamada, `[CompareProperty]` özniteliği için doğrudan değiştirme olur [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) .

::: moniker-end

### <a name="nested-models-collection-types-and-complex-types"></a>İç içe modeller, koleksiyon türleri ve karmaşık türler

Blazor yerleşik olan veri açıklamalarını kullanarak form girişini doğrulama desteği sağlar <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> . Ancak, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> yalnızca koleksiyonun üst düzey özelliklerini, koleksiyon veya karmaşık tür özellikleri olmayan forma doğrular.

Koleksiyon ve karmaşık tür özellikleri dahil olmak üzere, bağlantılı modelin tüm nesne grafiğini doğrulamak için `ObjectGraphDataAnnotationsValidator` *deneysel* paket tarafından sunulan öğesini kullanın [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

İle model özelliklerine açıklama ekleyin `[ValidateComplexType]` . Aşağıdaki model sınıflarında, `ShipDescription` sınıfı, model forma bağlandığında doğrulanacak ek veri açıklamalarını içerir:

`Starship.cs`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

    ...
}
```

`ShipDescription.cs`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }

    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>Form doğrulamasına göre Gönder düğmesini etkinleştir

Form doğrulamasına göre Gönder düğmesini etkinleştirmek ve devre dışı bırakmak için:

* <xref:Microsoft.AspNetCore.Components.Forms.EditContext>Bileşen başlatıldığında modeli atamak için formunu kullanın.
* <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>Gönder düğmesini etkinleştirmek ve devre dışı bırakmak için bağlam geri aramasında formu doğrulayın.
* Yöntemi içindeki olay işleyicisinin üstünden geri dön `Dispose` . Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

> [!NOTE]
> Kullanırken <xref:Microsoft.AspNetCore.Components.Forms.EditContext> , öğesine de bir atayın <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

Yukarıdaki örnekte, şu şekilde ayarlayın `formInvalid` `false` :

* Form geçerli varsayılan değerlerle önceden yüklenir.
* Form yüklendiğinde Gönder düğmesinin etkinleştirilmesini istiyorsunuz.

Önceki yaklaşımın yan etkisi, <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> Kullanıcı herhangi bir alanla etkileşime geçtiğinde bileşen geçersiz alanlarla doldurulmuştur. Bu senaryoya aşağıdaki yollarla değinilerek şunlar olabilir:

* <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Form üzerinde bir bileşen kullanmayın.
* <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Gönder düğmesi seçildiğinde bileşeni görünür hale getirin (örneğin, bir `HandleValidSubmit` yöntemde).

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a>Sorun giderme

> InvalidOperationException: EditForm bir model parametresi veya bir EditContext parametresi gerektiriyor, ancak her ikisini de içermemelidir.

Veya olduğunu doğrulayın <xref:Microsoft.AspNetCore.Components.Forms.EditForm> <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>  <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . Her ikisini de aynı form için kullanmayın.

Forma atama yaparken <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> , aşağıdaki örnekte gösterildiği gibi model türünün örneği oluşturulmuş olduğunu doğrulayın:

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/file-uploads>

::: moniker-end
