İç içe bileşenler genellikle ' de açıklandığı gibi *zincirleme bağlama* kullanarak verileri bağlar <xref:blazor/components/data-binding> . İç içe ve iç içe geçmiş bileşenler, kayıtlı bir bellek içi durum kapsayıcısını kullanarak verilere erişimi paylaşabilir. Özel durum kapsayıcı sınıfı <xref:System.Action> , bileşenleri durum değişikliği uygulamasının farklı bölümlerinde bilgilendirmek için atanabilir bir şekilde kullanılabilir. Aşağıdaki örnekte:

* Bir çift bileşen, bir özelliği izlemek için bir durum kapsayıcısı kullanır.
* Örneğin bileşenleri iç içe, ancak bu yaklaşımın çalışması için iç içe geçme gerekli değildir.

`StateContainer.cs`:

```csharp
public class StateContainer
{
    public string Property { get; set; } = "Initial value from StateContainer";

    public event Action OnChange;

    public void SetProperty(string value)
    {
        Property = value;
        NotifyStateChanged();
    }

    private void NotifyStateChanged() => OnChange?.Invoke();
}
```

İçinde `Program.Main` (Blazor WebAssembly):

```csharp
builder.Services.AddSingleton<StateContainer>();
```

İçinde `Startup.ConfigureServices` (Blazor Server):

```csharp
services.AddSingleton<StateContainer>();
```

`Pages/Component1.razor`:

```razor
@page "/Component1"
@inject StateContainer StateContainer
@implements IDisposable

<h1>Component 1</h1>

<p>Component 1 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 1</button>
</p>

<Component2 />

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 1 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

`Shared/Component2.razor`:

```razor
@inject StateContainer StateContainer
@implements IDisposable

<h2>Component 2</h2>

<p>Component 2 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 2</button>
</p>

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 2 {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```
