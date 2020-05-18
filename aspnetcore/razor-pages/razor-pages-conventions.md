---
title: Razor le convenzioni di route e app in ASP.NET Core autore: Rick-Anderson Description: scoprire in che modo le convenzioni del provider di modelli di app e route consentono di controllare il routing, l'individuazione e l'elaborazione delle pagine.
monikerRange:' >= aspnetcore-2,1' ms. Author: Riande ms. Custom: MVC ms. Date: 02/07/2020 no-loc: [Blazer, "Identity", "Let ' s Encrypt", Razor, SignalR] UID: Razor-Pages/Razor-Pages-Conventions
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Convenzioni di route e app per Razor Pages in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Informazioni su come usare le [convenzioni di route e del provider di modello di app](xref:mvc/controllers/application-model#conventions) della pagina per controllare routing, individuazione ed elaborazione nelle app Razor Pages.

Quando è necessario configurare la route di una pagina personalizzata per le singole pagine, configurare il routing alle pagine con la [convenzione AddPageRoute](#configure-a-page-route) descritta più avanti in questo argomento.

Per specificare una route di pagina, aggiungere segmenti di route o aggiungere parametri a una route, usare la `@page` direttiva della pagina. Per altre informazioni, vedere [route personalizzate](xref:razor-pages/index#custom-routes).

Sono disponibili parole riservate che non possono essere usate come segmenti di route o nomi di parametro. Per ulteriori informazioni, vedere [routing: nomi di routing riservati](xref:mvc/controllers/routing#reserved-routing-names).

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

| Scenario | L'esempio illustra come eseguire le seguenti operazioni: |
| -------- | --------------------------- |
| [Convenzioni del modello](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Aggiungere un modello e un'intestazione di route alle pagine di un'app. |
| [Convenzioni per le azioni di route di pagina](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Aggiungere un modello di route alle pagine in una cartella e a una pagina singola. |
| [Convenzioni per le azioni del modello di pagina](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe di filtro, espressione lambda o factory di filtro)</li></ul> | Aggiungere un'intestazione alle pagine in una cartella, aggiungere un'intestazione a una pagina singola e configurare una [factory di filtro](xref:mvc/controllers/filters#ifilterfactory) per aggiungere un'intestazione alle pagine di un'app. |

Le convenzioni Razor Pages vengono aggiunte e configurate <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> mediante <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> il metodo di estensione a nella `Startup` raccolta di servizi nella classe. Gli esempi di convenzione seguenti sono illustrati più avanti in questo argomento:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordine Route

Le route specificano un oggetto per l' <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> elaborazione (corrispondenza della route).

| JSON            | Comportamento |
| :--------------: | -------- |
| -1               | La route viene elaborata prima dell'elaborazione di altre route. |
| 0                | L'ordine non è specificato (valore predefinito). Non assegnando `Order` (`Order = null`) il percorso `Order` viene impostato su 0 (zero) per l'elaborazione. |
| 1, 2, &hellip; n | Specifica l'ordine di elaborazione della route. |

L'elaborazione delle route viene stabilita per convenzione:

* Le route vengono elaborate in ordine sequenziale (-1, 0, 1 &hellip; , 2, n).
* Quando le route hanno lo `Order`stesso, la route più specifica viene confrontata per prima, seguita da route meno specifiche.
* Quando le route con lo `Order` stesso numero di parametri corrispondono a un URL di richiesta, le route vengono elaborate nell'ordine in cui sono state aggiunte <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>a.

Se possibile, evitare di basarsi su un ordine di elaborazione di Route definito. In genere, il routing seleziona la route corretta con l'URL corrispondente. Se è necessario impostare le `Order` proprietà della route in modo che le richieste vengano indirizzate correttamente, lo schema di routing dell'app è probabilmente confuso per i client ed è fragile da mantenere. Cercare di semplificare lo schema di routing dell'app. L'app di esempio richiede un ordine esplicito di elaborazione della route per illustrare diversi scenari di routing usando una singola app. Tuttavia, è consigliabile provare a evitare la procedura di impostazione della `Order` route nelle app di produzione.

Il routing di Razor Pages e il routing del controller MVC condividono un'implementazione. Le informazioni sugli ordini di route negli argomenti di MVC sono disponibili in [routing alle azioni del controller: ordinamento delle route degli attributi](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Convenzioni del modello

Aggiungere un delegato per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> per aggiungere le [convenzioni del modello](xref:mvc/controllers/application-model#conventions) che si applicano a Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di route a tutte le pagine

Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oggetto alla raccolta di <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> istanze di che vengono applicate durante la costruzione del modello di route della pagina.

L'app di esempio aggiunge un modello di route `{globalTemplate?}` a tutte le pagine nell'app:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `1`. In questo modo si garantisce il comportamento di corrispondenza Route seguente nell'app di esempio:

* Un modello di route `TheContactPage/{text?}` per viene aggiunto più avanti nell'argomento. La route della pagina di contatto ha un ordine `null` predefinito`Order = 0`di (), quindi corrisponde prima `{globalTemplate?}` del modello di route.
* Un `{aboutTemplate?}` modello di route verrà aggiunto più avanti nell'argomento. Al modello `{aboutTemplate?}` viene assegnato un `Order` di `2`. Quando viene richiesta la pagina di informazioni in `/About/RouteDataValue`, "RouteDataValue" viene caricato in `RouteData.Values["globalTemplate"]` (`Order = 1`) e non in `RouteData.Values["aboutTemplate"]` (`Order = 2`) a causa dell'impostazione della proprietà `Order`.
* Un `{otherPagesTemplate?}` modello di route verrà aggiunto più avanti nell'argomento. Al modello `{otherPagesTemplate?}` viene assegnato un `Order` di `2`. Quando viene richiesta una pagina nella cartella *pages/OtherPages* con un parametro di route, ad esempio `/OtherPages/Page1/RouteDataValue`, "RouteDataValue" viene `RouteData.Values["globalTemplate"]` caricato in`Order = 1`() e non `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) a causa dell'impostazione della `Order` proprietà.

Laddove possibile, non impostare `Order`, che restituisce. `Order = 0` Basarsi sul routing per selezionare la route corretta.

Vengono aggiunte opzioni di Razor Pages, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>ad esempio l'aggiunta di, quando MVC viene aggiunto alla raccolta `Startup.ConfigureServices`di servizi in. Per un esempio completo, vedere [l'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About/GlobalRouteValue` ed esaminare il risultato:

![La pagina di informazioni viene richiesta con un segmento di route di GlobalRouteValue. La pagina sottoposta a rendering indica che il valore di dati della route viene acquisito nel metodo OnGet della pagina.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di app a tutte le pagine

Usare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oggetto alla raccolta di <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> istanze che vengono applicate durante la costruzione del modello di app di pagina.

Per dimostrare questa e altre convenzioni più avanti in questo argomento, l'app di esempio include una classe `AddHeaderAttribute`. Il costruttore della classe accetta una stringa `name` e una matrice di stringhe `values`. Questi valori vengono usati nel relativo metodo `OnResultExecuting` per impostare un'intestazione di risposta. La classe completa è illustrata nella sezione [Convenzioni per le azioni del modello di pagina](#page-model-action-conventions) più avanti in questo argomento.

L'app di esempio usa la classe `AddHeaderAttribute` per aggiungere un'intestazione, `GlobalHeader`, a tutte le pagine nell'app:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina di informazioni indicano che è stato aggiunto l'elemento GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di gestore a tutte le pagine

Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> oggetto alla raccolta di <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> istanze di che vengono applicate durante la costruzione del modello del gestore di pagina.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Convenzioni per le azioni di route di pagina

Il provider del modello di route predefinito che deriva <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> da richiama le convenzioni progettate per fornire punti di estendibilità per la configurazione delle route di pagina.

### <a name="folder-route-model-convention"></a>Convenzione per il modello di route cartella

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oggetto che richiama un'azione su <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> per tutte le pagine nella cartella specificata.

L'app di esempio usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> per aggiungere un modello di route `{otherPagesTemplate?}` alle pagine nella cartella *OtherPages*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `2`. In questo modo si garantisce che `{globalTemplate?}` al modello per (impostato in precedenza `1`nell'argomento a) venga assegnata la priorità per la prima posizione del valore di dati della route quando viene specificato un singolo valore di route. Se una pagina nella cartella *pages/OtherPages* è richiesta con un valore di parametro di route (ad `/OtherPages/Page1/RouteDataValue`esempio,), "RouteDataValue" viene `RouteData.Values["globalTemplate"]` caricato`Order = 1`in () `RouteData.Values["otherPagesTemplate"]` e`Order = 2`non () a causa `Order` dell'impostazione della proprietà.

Laddove possibile, non impostare `Order`, che restituisce. `Order = 0` Basarsi sul routing per selezionare la route corretta.

Richiedere la pagina Page1 dell'esempio in `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ed esaminare il risultato:

![La pagina Page1 nella cartella OtherPages viene richiesta con un segmento di route di GlobalRouteValue e OtherPagesRouteValue. La pagina sottoposta a rendering indica che i valori di dati della route vengono acquisiti nel metodo OnGet della pagina.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convenzione per il modello di route pagina

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oggetto che richiama un'azione sull'oggetto <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> per la pagina con il nome specificato.

L'app di esempio usa `AddPageRouteModelConvention` per aggiungere un modello di route `{aboutTemplate?}` alla pagina di informazioni:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `2`. In questo modo si garantisce che `{globalTemplate?}` al modello per (impostato in precedenza `1`nell'argomento a) venga assegnata la priorità per la prima posizione del valore di dati della route quando viene specificato un singolo valore di route. Se la pagina about è richiesta con un valore di parametro di `/About/RouteDataValue`route in, "RouteDataValue" viene `RouteData.Values["globalTemplate"]` caricato`Order = 1`in () `RouteData.Values["aboutTemplate"]` e`Order = 2`non () a causa `Order` dell'impostazione della proprietà.

Laddove possibile, non impostare `Order`, che restituisce. `Order = 0` Basarsi sul routing per selezionare la route corretta.

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ed esaminare il risultato:

![La pagina di informazioni viene richiesta con segmenti di route per GlobalRouteValue e AboutRouteValue. La pagina sottoposta a rendering indica che i valori di dati della route vengono acquisiti nel metodo OnGet della pagina.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Usare un trasformatore di parametri per personalizzare le route di pagina

Le route di pagina generate da ASP.NET Core possono essere personalizzate usando un trasformatore di parametro. Un trasformatore di parametri implementa `IOutboundParameterTransformer` e trasforma il valore dei parametri. Ad esempio, un trasformatore di parametri `SlugifyParameterTransformer` personalizzato cambia il valore di route `SubscriptionManagement` in `subscription-management`.

La `PageRouteTransformerConvention` convenzione del modello di route di pagina applica un trasformatore di parametri ai segmenti di cartella e nome file delle route di pagina generate automaticamente in un'app. Ad esempio, il file Razor Pages in */pages/SubscriptionManagement/ViewAll.cshtml* avrebbe la route riscritta da `/SubscriptionManagement/ViewAll` a. `/subscription-management/view-all`

`PageRouteTransformerConvention`trasforma solo i segmenti generati automaticamente di una route di pagina che provengono dalla Razor Pages cartella e dal nome file. Non trasforma i segmenti di route aggiunti con `@page` la direttiva. La convenzione non trasforma inoltre le route aggiunte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>da.

Il `PageRouteTransformerConvention` è registrato come opzione in `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a>Configurare una route di pagina

Utilizzare <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> per configurare una route a una pagina nel percorso di pagina specificato. I collegamenti generati alla pagina usano la route specificata. `AddPageRoute` usa `AddPageRouteModelConvention` per stabilire la route.

L'app di esempio crea una route a `/TheContactPage` per *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

La pagina di contatto può anche essere raggiunta in corrispondenza di `/Contact` tramite la route predefinita.

La route personalizzata dell'app di esempio alla pagina di contatto consente un segmento di route `text` facoltativo (`{text?}`). La pagina include anche tale segmento facoltativo nella relativa istruzione `@page` nel caso in cui il visitatore acceda alla pagina in corrispondenza della relativa route `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Si noti che l'URL generato per il collegamento **Contatto** nella pagina sottoposta a rendering riflette la route aggiornata:

![Collegamento alla pagina di contatto dell'app di esempio nella barra di spostamento](razor-pages-conventions/_static/contact-link.png)

![L'esame del collegamento alla pagina di contatto nell'HTML sottoposto a rendering indica che href è impostato su '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

Visitare la pagina di contatto nella route normale, `/Contact`, o nella route personalizzata `/TheContactPage`. Se si specifica un segmento di route `text` aggiuntivo, la pagina visualizza il segmento codificato in formato HTML specificato dall'utente:

![Esempio di browser Microsoft Edge in cui viene specificato un segmento di route facoltativo 'text' corrispondente a 'TextValue' nell'URL. La pagina sottoposta a rendering visualizza il valore del segmento 'text'.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Convenzioni per le azioni del modello di pagina

Il provider del modello di pagina predefinito <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> che implementa le convenzioni Invokes progettate per fornire punti di estendibilità per la configurazione dei modelli di pagina. Queste convenzioni sono utili durante la compilazione e la modifica degli scenari di individuazione ed elaborazione delle pagine.

Per gli esempi in questa sezione, l'app di esempio usa `AddHeaderAttribute` una classe, che è <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>un oggetto, che applica un'intestazione di risposta:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Tramite le convenzioni, nell'esempio viene illustrato come applicare l'attributo a tutte le pagine in una cartella e a una singola pagina.

**Convenzione per il modello di app cartella**

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oggetto che richiama un'azione sulle <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> istanze di per tutte le pagine nella cartella specificata.

Nell'esempio viene illustrato l'uso di `AddFolderApplicationModelConvention` aggiungendo un'intestazione, `OtherPagesHeader`, alle pagine all'interno della cartella *OtherPages* dell'app:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Richiedere la pagina Page1 dell'esempio in `localhost:5000/OtherPages/Page1` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina OtherPages/Page1 indicano che è stato aggiunto l'elemento OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convenzione per il modello di app cartella**

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oggetto che richiama un'azione sull'oggetto <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> per la pagina con il nome specificato.

Nell'esempio viene illustrato l'uso di `AddPageApplicationModelConvention` aggiungendo un'intestazione, `AboutHeader`, alla pagina di informazioni:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina di informazioni indicano che è stato aggiunto l'elemento AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurare un filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Configura il filtro specificato da applicare. È possibile implementare una classe di filtro, ma l'app di esempio illustra come implementare un filtro in un'espressione lambda, che viene implementata in background come una factory che restituisce un filtro:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

Viene usato il modello di app della pagina per verificare il percorso relativo per i segmenti che portano alla pagina Page2 nella cartella *OtherPages*. Se la condizione viene superata, viene aggiunta un'intestazione. In caso contrario, viene applicato `EmptyFilter`.

`EmptyFilter` è un [filtro azione](xref:mvc/controllers/filters#action-filters). Poiché i filtri azione vengono ignorati da Razor Pages `EmptyFilter` , non ha alcun effetto come previsto se il percorso `OtherPages/Page2`non contiene.

Richiedere la pagina Page2 dell'esempio in `localhost:5000/OtherPages/Page2` ed esaminare le intestazioni per visualizzare il risultato:

![L'elemento OtherPagesPage2Header viene aggiunto alla risposta per Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurare una factory di filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Configura la factory specificata per applicare i [filtri](xref:mvc/controllers/filters) a tutti i Razor Pages.

L'app di esempio illustra un esempio dell'uso di una [factory di filtro](xref:mvc/controllers/filters#ifilterfactory) aggiungendo un'intestazione, `FilterFactoryHeader`, con due valori per le pagine dell'app:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina informazioni indicano che sono state aggiunte due intestazioni FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtri MVC e il filtro di pagina (IPageFilter)

I [filtri azione](xref:mvc/controllers/filters#action-filters) MVC vengono ignorati da Razor Pages, poiché vengono usati i metodi del gestore. Sono disponibili altri tipi di filtri MVC: [autorizzazione](xref:mvc/controllers/filters#authorization-filters), [eccezione](xref:mvc/controllers/filters#exception-filters), [risorse](xref:mvc/controllers/filters#resource-filters) e [risultati](xref:mvc/controllers/filters#result-filters). Per altre informazioni, vedere l'argomento [Filtri](xref:mvc/controllers/filters).

Il filtro di pagina<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>() è un filtro che si applica a Razor Pages. Per altre informazioni, vedere [Modalità di filtro per pagine Razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Informazioni su come usare le [convenzioni di route e del provider di modello di app](xref:mvc/controllers/application-model#conventions) della pagina per controllare routing, individuazione ed elaborazione nelle app Razor Pages.

Quando è necessario configurare la route di una pagina personalizzata per le singole pagine, configurare il routing alle pagine con la [convenzione AddPageRoute](#configure-a-page-route) descritta più avanti in questo argomento.

Per specificare una route di pagina, aggiungere segmenti di route o aggiungere parametri a una route, usare la `@page` direttiva della pagina. Per altre informazioni, vedere [route personalizzate](xref:razor-pages/index#custom-routes).

Sono disponibili parole riservate che non possono essere usate come segmenti di route o nomi di parametro. Per ulteriori informazioni, vedere [routing: nomi di routing riservati](xref:fundamentals/routing#reserved-routing-names).

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

| Scenario | L'esempio illustra come eseguire le seguenti operazioni: |
| -------- | --------------------------- |
| [Convenzioni del modello](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Aggiungere un modello e un'intestazione di route alle pagine di un'app. |
| [Convenzioni per le azioni di route di pagina](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Aggiungere un modello di route alle pagine in una cartella e a una pagina singola. |
| [Convenzioni per le azioni del modello di pagina](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe di filtro, espressione lambda o factory di filtro)</li></ul> | Aggiungere un'intestazione alle pagine in una cartella, aggiungere un'intestazione a una pagina singola e configurare una [factory di filtro](xref:mvc/controllers/filters#ifilterfactory) per aggiungere un'intestazione alle pagine di un'app. |

Le convenzioni Razor Pages vengono aggiunte e configurate <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> mediante <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> il metodo di estensione a nella `Startup` raccolta di servizi nella classe. Gli esempi di convenzione seguenti sono illustrati più avanti in questo argomento:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordine Route

Le route specificano un oggetto per l' <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> elaborazione (corrispondenza della route).

| JSON            | Comportamento |
| :--------------: | -------- |
| -1               | La route viene elaborata prima dell'elaborazione di altre route. |
| 0                | L'ordine non è specificato (valore predefinito). Non assegnando `Order` (`Order = null`) il percorso `Order` viene impostato su 0 (zero) per l'elaborazione. |
| 1, 2, &hellip; n | Specifica l'ordine di elaborazione della route. |

L'elaborazione delle route viene stabilita per convenzione:

* Le route vengono elaborate in ordine sequenziale (-1, 0, 1 &hellip; , 2, n).
* Quando le route hanno lo `Order`stesso, la route più specifica viene confrontata per prima, seguita da route meno specifiche.
* Quando le route con lo `Order` stesso numero di parametri corrispondono a un URL di richiesta, le route vengono elaborate nell'ordine in cui sono state aggiunte <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>a.

Se possibile, evitare di basarsi su un ordine di elaborazione di Route definito. In genere, il routing seleziona la route corretta con l'URL corrispondente. Se è necessario impostare le `Order` proprietà della route in modo che le richieste vengano indirizzate correttamente, lo schema di routing dell'app è probabilmente confuso per i client ed è fragile da mantenere. Cercare di semplificare lo schema di routing dell'app. L'app di esempio richiede un ordine esplicito di elaborazione della route per illustrare diversi scenari di routing usando una singola app. Tuttavia, è consigliabile provare a evitare la procedura di impostazione della `Order` route nelle app di produzione.

Il routing di Razor Pages e il routing del controller MVC condividono un'implementazione. Le informazioni sugli ordini di route negli argomenti di MVC sono disponibili in [routing alle azioni del controller: ordinamento delle route degli attributi](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Convenzioni del modello

Aggiungere un delegato per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> per aggiungere le [convenzioni del modello](xref:mvc/controllers/application-model#conventions) che si applicano a Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di route a tutte le pagine

Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oggetto alla raccolta di <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> istanze di che vengono applicate durante la costruzione del modello di route della pagina.

L'app di esempio aggiunge un modello di route `{globalTemplate?}` a tutte le pagine nell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `1`. In questo modo si garantisce il comportamento di corrispondenza Route seguente nell'app di esempio:

* Un modello di route `TheContactPage/{text?}` per viene aggiunto più avanti nell'argomento. La route della pagina di contatto ha un ordine `null` predefinito`Order = 0`di (), quindi corrisponde prima `{globalTemplate?}` del modello di route.
* Un `{aboutTemplate?}` modello di route verrà aggiunto più avanti nell'argomento. Al modello `{aboutTemplate?}` viene assegnato un `Order` di `2`. Quando viene richiesta la pagina di informazioni in `/About/RouteDataValue`, "RouteDataValue" viene caricato in `RouteData.Values["globalTemplate"]` (`Order = 1`) e non in `RouteData.Values["aboutTemplate"]` (`Order = 2`) a causa dell'impostazione della proprietà `Order`.
* Un `{otherPagesTemplate?}` modello di route verrà aggiunto più avanti nell'argomento. Al modello `{otherPagesTemplate?}` viene assegnato un `Order` di `2`. Quando viene richiesta una pagina nella cartella *pages/OtherPages* con un parametro di route, ad esempio `/OtherPages/Page1/RouteDataValue`, "RouteDataValue" viene `RouteData.Values["globalTemplate"]` caricato in`Order = 1`() e non `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) a causa dell'impostazione della `Order` proprietà.

Laddove possibile, non impostare `Order`, che restituisce. `Order = 0` Basarsi sul routing per selezionare la route corretta.

Vengono aggiunte opzioni di Razor Pages, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>ad esempio l'aggiunta di, quando MVC viene aggiunto alla raccolta `Startup.ConfigureServices`di servizi in. Per un esempio completo, vedere [l'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About/GlobalRouteValue` ed esaminare il risultato:

![La pagina di informazioni viene richiesta con un segmento di route di GlobalRouteValue. La pagina sottoposta a rendering indica che il valore di dati della route viene acquisito nel metodo OnGet della pagina.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di app a tutte le pagine

Usare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oggetto alla raccolta di <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> istanze che vengono applicate durante la costruzione del modello di app di pagina.

Per dimostrare questa e altre convenzioni più avanti in questo argomento, l'app di esempio include una classe `AddHeaderAttribute`. Il costruttore della classe accetta una stringa `name` e una matrice di stringhe `values`. Questi valori vengono usati nel relativo metodo `OnResultExecuting` per impostare un'intestazione di risposta. La classe completa è illustrata nella sezione [Convenzioni per le azioni del modello di pagina](#page-model-action-conventions) più avanti in questo argomento.

L'app di esempio usa la classe `AddHeaderAttribute` per aggiungere un'intestazione, `GlobalHeader`, a tutte le pagine nell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina di informazioni indicano che è stato aggiunto l'elemento GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di gestore a tutte le pagine

Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> oggetto alla raccolta di <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> istanze di che vengono applicate durante la costruzione del modello del gestore di pagina.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Convenzioni per le azioni di route di pagina

Il provider del modello di route predefinito che deriva <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> da richiama le convenzioni progettate per fornire punti di estendibilità per la configurazione delle route di pagina.

### <a name="folder-route-model-convention"></a>Convenzione per il modello di route cartella

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oggetto che richiama un'azione su <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> per tutte le pagine nella cartella specificata.

L'app di esempio usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> per aggiungere un modello di route `{otherPagesTemplate?}` alle pagine nella cartella *OtherPages*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `2`. In questo modo si garantisce che `{globalTemplate?}` al modello per (impostato in precedenza `1`nell'argomento a) venga assegnata la priorità per la prima posizione del valore di dati della route quando viene specificato un singolo valore di route. Se una pagina nella cartella *pages/OtherPages* è richiesta con un valore di parametro di route (ad `/OtherPages/Page1/RouteDataValue`esempio,), "RouteDataValue" viene `RouteData.Values["globalTemplate"]` caricato`Order = 1`in () `RouteData.Values["otherPagesTemplate"]` e`Order = 2`non () a causa `Order` dell'impostazione della proprietà.

Laddove possibile, non impostare `Order`, che restituisce. `Order = 0` Basarsi sul routing per selezionare la route corretta.

Richiedere la pagina Page1 dell'esempio in `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ed esaminare il risultato:

![La pagina Page1 nella cartella OtherPages viene richiesta con un segmento di route di GlobalRouteValue e OtherPagesRouteValue. La pagina sottoposta a rendering indica che i valori di dati della route vengono acquisiti nel metodo OnGet della pagina.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convenzione per il modello di route pagina

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oggetto che richiama un'azione sull'oggetto <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> per la pagina con il nome specificato.

L'app di esempio usa `AddPageRouteModelConvention` per aggiungere un modello di route `{aboutTemplate?}` alla pagina di informazioni:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `2`. In questo modo si garantisce che `{globalTemplate?}` al modello per (impostato in precedenza `1`nell'argomento a) venga assegnata la priorità per la prima posizione del valore di dati della route quando viene specificato un singolo valore di route. Se la pagina about è richiesta con un valore di parametro di `/About/RouteDataValue`route in, "RouteDataValue" viene `RouteData.Values["globalTemplate"]` caricato`Order = 1`in () `RouteData.Values["aboutTemplate"]` e`Order = 2`non () a causa `Order` dell'impostazione della proprietà.

Laddove possibile, non impostare `Order`, che restituisce. `Order = 0` Basarsi sul routing per selezionare la route corretta.

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ed esaminare il risultato:

![La pagina di informazioni viene richiesta con segmenti di route per GlobalRouteValue e AboutRouteValue. La pagina sottoposta a rendering indica che i valori di dati della route vengono acquisiti nel metodo OnGet della pagina.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Usare un trasformatore di parametri per personalizzare le route di pagina

Le route di pagina generate da ASP.NET Core possono essere personalizzate usando un trasformatore di parametro. Un trasformatore di parametri implementa `IOutboundParameterTransformer` e trasforma il valore dei parametri. Ad esempio, un trasformatore di parametri `SlugifyParameterTransformer` personalizzato cambia il valore di route `SubscriptionManagement` in `subscription-management`.

La `PageRouteTransformerConvention` convenzione del modello di route di pagina applica un trasformatore di parametri ai segmenti di cartella e nome file delle route di pagina generate automaticamente in un'app. Ad esempio, il file Razor Pages in */pages/SubscriptionManagement/ViewAll.cshtml* avrebbe la route riscritta da `/SubscriptionManagement/ViewAll` a. `/subscription-management/view-all`

`PageRouteTransformerConvention`trasforma solo i segmenti generati automaticamente di una route di pagina che provengono dalla Razor Pages cartella e dal nome file. Non trasforma i segmenti di route aggiunti con `@page` la direttiva. La convenzione non trasforma inoltre le route aggiunte <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>da.

Il `PageRouteTransformerConvention` è registrato come opzione in `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a>Configurare una route di pagina

Utilizzare <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> per configurare una route a una pagina nel percorso di pagina specificato. I collegamenti generati alla pagina usano la route specificata. `AddPageRoute` usa `AddPageRouteModelConvention` per stabilire la route.

L'app di esempio crea una route a `/TheContactPage` per *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

La pagina di contatto può anche essere raggiunta in corrispondenza di `/Contact` tramite la route predefinita.

La route personalizzata dell'app di esempio alla pagina di contatto consente un segmento di route `text` facoltativo (`{text?}`). La pagina include anche tale segmento facoltativo nella relativa istruzione `@page` nel caso in cui il visitatore acceda alla pagina in corrispondenza della relativa route `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Si noti che l'URL generato per il collegamento **Contatto** nella pagina sottoposta a rendering riflette la route aggiornata:

![Collegamento alla pagina di contatto dell'app di esempio nella barra di spostamento](razor-pages-conventions/_static/contact-link.png)

![L'esame del collegamento alla pagina di contatto nell'HTML sottoposto a rendering indica che href è impostato su '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

Visitare la pagina di contatto nella route normale, `/Contact`, o nella route personalizzata `/TheContactPage`. Se si specifica un segmento di route `text` aggiuntivo, la pagina visualizza il segmento codificato in formato HTML specificato dall'utente:

![Esempio di browser Microsoft Edge in cui viene specificato un segmento di route facoltativo 'text' corrispondente a 'TextValue' nell'URL. La pagina sottoposta a rendering visualizza il valore del segmento 'text'.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Convenzioni per le azioni del modello di pagina

Il provider del modello di pagina predefinito <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> che implementa le convenzioni Invokes progettate per fornire punti di estendibilità per la configurazione dei modelli di pagina. Queste convenzioni sono utili durante la compilazione e la modifica degli scenari di individuazione ed elaborazione delle pagine.

Per gli esempi in questa sezione, l'app di esempio usa `AddHeaderAttribute` una classe, che è <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>un oggetto, che applica un'intestazione di risposta:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Tramite le convenzioni, nell'esempio viene illustrato come applicare l'attributo a tutte le pagine in una cartella e a una singola pagina.

**Convenzione per il modello di app cartella**

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oggetto che richiama un'azione sulle <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> istanze di per tutte le pagine nella cartella specificata.

Nell'esempio viene illustrato l'uso di `AddFolderApplicationModelConvention` aggiungendo un'intestazione, `OtherPagesHeader`, alle pagine all'interno della cartella *OtherPages* dell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Richiedere la pagina Page1 dell'esempio in `localhost:5000/OtherPages/Page1` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina OtherPages/Page1 indicano che è stato aggiunto l'elemento OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convenzione per il modello di app cartella**

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oggetto che richiama un'azione sull'oggetto <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> per la pagina con il nome specificato.

Nell'esempio viene illustrato l'uso di `AddPageApplicationModelConvention` aggiungendo un'intestazione, `AboutHeader`, alla pagina di informazioni:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina di informazioni indicano che è stato aggiunto l'elemento AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurare un filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Configura il filtro specificato da applicare. È possibile implementare una classe di filtro, ma l'app di esempio illustra come implementare un filtro in un'espressione lambda, che viene implementata in background come una factory che restituisce un filtro:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Viene usato il modello di app della pagina per verificare il percorso relativo per i segmenti che portano alla pagina Page2 nella cartella *OtherPages*. Se la condizione viene superata, viene aggiunta un'intestazione. In caso contrario, viene applicato `EmptyFilter`.

`EmptyFilter` è un [filtro azione](xref:mvc/controllers/filters#action-filters). Poiché i filtri azione vengono ignorati da Razor Pages `EmptyFilter` , non ha alcun effetto come previsto se il percorso `OtherPages/Page2`non contiene.

Richiedere la pagina Page2 dell'esempio in `localhost:5000/OtherPages/Page2` ed esaminare le intestazioni per visualizzare il risultato:

![L'elemento OtherPagesPage2Header viene aggiunto alla risposta per Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurare una factory di filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Configura la factory specificata per applicare i [filtri](xref:mvc/controllers/filters) a tutti i Razor Pages.

L'app di esempio illustra un esempio dell'uso di una [factory di filtro](xref:mvc/controllers/filters#ifilterfactory) aggiungendo un'intestazione, `FilterFactoryHeader`, con due valori per le pagine dell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina informazioni indicano che sono state aggiunte due intestazioni FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtri MVC e il filtro di pagina (IPageFilter)

I [filtri azione](xref:mvc/controllers/filters#action-filters) MVC vengono ignorati da Razor Pages, poiché vengono usati i metodi del gestore. Sono disponibili altri tipi di filtri MVC: [autorizzazione](xref:mvc/controllers/filters#authorization-filters), [eccezione](xref:mvc/controllers/filters#exception-filters), [risorse](xref:mvc/controllers/filters#resource-filters) e [risultati](xref:mvc/controllers/filters#result-filters). Per altre informazioni, vedere l'argomento [Filtri](xref:mvc/controllers/filters).

Il filtro di pagina<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>() è un filtro che si applica a Razor Pages. Per altre informazioni, vedere [Modalità di filtro per pagine Razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Informazioni su come usare le [convenzioni di route e del provider di modello di app](xref:mvc/controllers/application-model#conventions) della pagina per controllare routing, individuazione ed elaborazione nelle app Razor Pages.

Quando è necessario configurare la route di una pagina personalizzata per le singole pagine, configurare il routing alle pagine con la [convenzione AddPageRoute](#configure-a-page-route) descritta più avanti in questo argomento.

Per specificare una route di pagina, aggiungere segmenti di route o aggiungere parametri a una route, usare la `@page` direttiva della pagina. Per altre informazioni, vedere [route personalizzate](xref:razor-pages/index#custom-routes).

Sono disponibili parole riservate che non possono essere usate come segmenti di route o nomi di parametro. Per ulteriori informazioni, vedere [routing: nomi di routing riservati](xref:fundamentals/routing#reserved-routing-names).

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

| Scenario | L'esempio illustra come eseguire le seguenti operazioni: |
| -------- | --------------------------- |
| [Convenzioni del modello](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Aggiungere un modello e un'intestazione di route alle pagine di un'app. |
| [Convenzioni per le azioni di route di pagina](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Aggiungere un modello di route alle pagine in una cartella e a una pagina singola. |
| [Convenzioni per le azioni del modello di pagina](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe di filtro, espressione lambda o factory di filtro)</li></ul> | Aggiungere un'intestazione alle pagine in una cartella, aggiungere un'intestazione a una pagina singola e configurare una [factory di filtro](xref:mvc/controllers/filters#ifilterfactory) per aggiungere un'intestazione alle pagine di un'app. |

Le convenzioni Razor Pages vengono aggiunte e configurate <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> mediante <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> il metodo di estensione a nella `Startup` raccolta di servizi nella classe. Gli esempi di convenzione seguenti sono illustrati più avanti in questo argomento:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordine Route

Le route specificano un oggetto per l' <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> elaborazione (corrispondenza della route).

| JSON            | Comportamento |
| :--------------: | -------- |
| -1               | La route viene elaborata prima dell'elaborazione di altre route. |
| 0                | L'ordine non è specificato (valore predefinito). Non assegnando `Order` (`Order = null`) il percorso `Order` viene impostato su 0 (zero) per l'elaborazione. |
| 1, 2, &hellip; n | Specifica l'ordine di elaborazione della route. |

L'elaborazione delle route viene stabilita per convenzione:

* Le route vengono elaborate in ordine sequenziale (-1, 0, 1 &hellip; , 2, n).
* Quando le route hanno lo `Order`stesso, la route più specifica viene confrontata per prima, seguita da route meno specifiche.
* Quando le route con lo `Order` stesso numero di parametri corrispondono a un URL di richiesta, le route vengono elaborate nell'ordine in cui sono state aggiunte <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>a.

Se possibile, evitare di basarsi su un ordine di elaborazione di Route definito. In genere, il routing seleziona la route corretta con l'URL corrispondente. Se è necessario impostare le `Order` proprietà della route in modo che le richieste vengano indirizzate correttamente, lo schema di routing dell'app è probabilmente confuso per i client ed è fragile da mantenere. Cercare di semplificare lo schema di routing dell'app. L'app di esempio richiede un ordine esplicito di elaborazione della route per illustrare diversi scenari di routing usando una singola app. Tuttavia, è consigliabile provare a evitare la procedura di impostazione della `Order` route nelle app di produzione.

RazorIl routing di pagine e il routing del controller MVC condividono un'implementazione. Le informazioni sugli ordini di route negli argomenti di MVC sono disponibili in [routing alle azioni del controller: ordinamento delle route degli attributi](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Convenzioni del modello

Aggiungere un delegato per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> per aggiungere le [convenzioni](xref:mvc/controllers/application-model#conventions) del modello Razor che si applicano alle pagine.

### <a name="add-a-route-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di route a tutte le pagine

Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oggetto alla raccolta di <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> istanze di che vengono applicate durante la costruzione del modello di route della pagina.

L'app di esempio aggiunge un modello di route `{globalTemplate?}` a tutte le pagine nell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `1`. In questo modo si garantisce il comportamento di corrispondenza Route seguente nell'app di esempio:

* Un modello di route `TheContactPage/{text?}` per viene aggiunto più avanti nell'argomento. La route della pagina di contatto ha un ordine `null` predefinito`Order = 0`di (), quindi corrisponde prima `{globalTemplate?}` del modello di route.
* Un `{aboutTemplate?}` modello di route verrà aggiunto più avanti nell'argomento. Al modello `{aboutTemplate?}` viene assegnato un `Order` di `2`. Quando viene richiesta la pagina di informazioni in `/About/RouteDataValue`, "RouteDataValue" viene caricato in `RouteData.Values["globalTemplate"]` (`Order = 1`) e non in `RouteData.Values["aboutTemplate"]` (`Order = 2`) a causa dell'impostazione della proprietà `Order`.
* Un `{otherPagesTemplate?}` modello di route verrà aggiunto più avanti nell'argomento. Al modello `{otherPagesTemplate?}` viene assegnato un `Order` di `2`. Quando viene richiesta una pagina nella cartella *pages/OtherPages* con un parametro di route, ad esempio `/OtherPages/Page1/RouteDataValue`, "RouteDataValue" viene `RouteData.Values["globalTemplate"]` caricato in`Order = 1`() e non `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) a causa dell'impostazione della `Order` proprietà.

Laddove possibile, non impostare `Order`, che restituisce. `Order = 0` Basarsi sul routing per selezionare la route corretta.

RazorLe opzioni relative alle pagine, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>ad esempio l'aggiunta, vengono aggiunte quando MVC viene aggiunto alla `Startup.ConfigureServices`raccolta di servizi in. Per un esempio completo, vedere [l'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About/GlobalRouteValue` ed esaminare il risultato:

![La pagina di informazioni viene richiesta con un segmento di route di GlobalRouteValue. La pagina sottoposta a rendering indica che il valore di dati della route viene acquisito nel metodo OnGet della pagina.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di app a tutte le pagine

Usare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oggetto alla raccolta di <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> istanze che vengono applicate durante la costruzione del modello di app di pagina.

Per dimostrare questa e altre convenzioni più avanti in questo argomento, l'app di esempio include una classe `AddHeaderAttribute`. Il costruttore della classe accetta una stringa `name` e una matrice di stringhe `values`. Questi valori vengono usati nel relativo metodo `OnResultExecuting` per impostare un'intestazione di risposta. La classe completa è illustrata nella sezione [Convenzioni per le azioni del modello di pagina](#page-model-action-conventions) più avanti in questo argomento.

L'app di esempio usa la classe `AddHeaderAttribute` per aggiungere un'intestazione, `GlobalHeader`, a tutte le pagine nell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina di informazioni indicano che è stato aggiunto l'elemento GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Aggiungere una convenzione del modello di gestore a tutte le pagine

Utilizzare <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> oggetto alla raccolta di <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> istanze di che vengono applicate durante la costruzione del modello del gestore di pagina.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Convenzioni per le azioni di route di pagina

Il provider del modello di route predefinito che deriva <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> da richiama le convenzioni progettate per fornire punti di estendibilità per la configurazione delle route di pagina.

### <a name="folder-route-model-convention"></a>Convenzione per il modello di route cartella

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oggetto che richiama un'azione su <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> per tutte le pagine nella cartella specificata.

L'app di esempio usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> per aggiungere un modello di route `{otherPagesTemplate?}` alle pagine nella cartella *OtherPages*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `2`. In questo modo si garantisce che `{globalTemplate?}` al modello per (impostato in precedenza `1`nell'argomento a) venga assegnata la priorità per la prima posizione del valore di dati della route quando viene specificato un singolo valore di route. Se una pagina nella cartella *pages/OtherPages* è richiesta con un valore di parametro di route (ad `/OtherPages/Page1/RouteDataValue`esempio,), "RouteDataValue" viene `RouteData.Values["globalTemplate"]` caricato`Order = 1`in () `RouteData.Values["otherPagesTemplate"]` e`Order = 2`non () a causa `Order` dell'impostazione della proprietà.

Laddove possibile, non impostare `Order`, che restituisce. `Order = 0` Basarsi sul routing per selezionare la route corretta.

Richiedere la pagina Page1 dell'esempio in `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ed esaminare il risultato:

![La pagina Page1 nella cartella OtherPages viene richiesta con un segmento di route di GlobalRouteValue e OtherPagesRouteValue. La pagina sottoposta a rendering indica che i valori di dati della route vengono acquisiti nel metodo OnGet della pagina.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convenzione per il modello di route pagina

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> oggetto che richiama un'azione sull'oggetto <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> per la pagina con il nome specificato.

L'app di esempio usa `AddPageRouteModelConvention` per aggiungere un modello di route `{aboutTemplate?}` alla pagina di informazioni:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

La proprietà <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> per <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> è impostata su `2`. In questo modo si garantisce che `{globalTemplate?}` al modello per (impostato in precedenza `1`nell'argomento a) venga assegnata la priorità per la prima posizione del valore di dati della route quando viene specificato un singolo valore di route. Se la pagina about è richiesta con un valore di parametro di `/About/RouteDataValue`route in, "RouteDataValue" viene `RouteData.Values["globalTemplate"]` caricato`Order = 1`in () `RouteData.Values["aboutTemplate"]` e`Order = 2`non () a causa `Order` dell'impostazione della proprietà.

Laddove possibile, non impostare `Order`, che restituisce. `Order = 0` Basarsi sul routing per selezionare la route corretta.

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About/GlobalRouteValue/AboutRouteValue` ed esaminare il risultato:

![La pagina di informazioni viene richiesta con segmenti di route per GlobalRouteValue e AboutRouteValue. La pagina sottoposta a rendering indica che i valori di dati della route vengono acquisiti nel metodo OnGet della pagina.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Configurare una route di pagina

Utilizzare <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> per configurare una route a una pagina nel percorso di pagina specificato. I collegamenti generati alla pagina usano la route specificata. `AddPageRoute` usa `AddPageRouteModelConvention` per stabilire la route.

L'app di esempio crea una route a `/TheContactPage` per *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

La pagina di contatto può anche essere raggiunta in corrispondenza di `/Contact` tramite la route predefinita.

La route personalizzata dell'app di esempio alla pagina di contatto consente un segmento di route `text` facoltativo (`{text?}`). La pagina include anche tale segmento facoltativo nella relativa istruzione `@page` nel caso in cui il visitatore acceda alla pagina in corrispondenza della relativa route `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Si noti che l'URL generato per il collegamento **Contatto** nella pagina sottoposta a rendering riflette la route aggiornata:

![Collegamento alla pagina di contatto dell'app di esempio nella barra di spostamento](razor-pages-conventions/_static/contact-link.png)

![L'esame del collegamento alla pagina di contatto nell'HTML sottoposto a rendering indica che href è impostato su '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

Visitare la pagina di contatto nella route normale, `/Contact`, o nella route personalizzata `/TheContactPage`. Se si specifica un segmento di route `text` aggiuntivo, la pagina visualizza il segmento codificato in formato HTML specificato dall'utente:

![Esempio di browser Microsoft Edge in cui viene specificato un segmento di route facoltativo 'text' corrispondente a 'TextValue' nell'URL. La pagina sottoposta a rendering visualizza il valore del segmento 'text'.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Convenzioni per le azioni del modello di pagina

Il provider del modello di pagina predefinito <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> che implementa le convenzioni Invokes progettate per fornire punti di estendibilità per la configurazione dei modelli di pagina. Queste convenzioni sono utili durante la compilazione e la modifica degli scenari di individuazione ed elaborazione delle pagine.

Per gli esempi in questa sezione, l'app di esempio usa `AddHeaderAttribute` una classe, che è <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>un oggetto, che applica un'intestazione di risposta:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Tramite le convenzioni, nell'esempio viene illustrato come applicare l'attributo a tutte le pagine in una cartella e a una singola pagina.

**Convenzione per il modello di app cartella**

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oggetto che richiama un'azione sulle <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> istanze di per tutte le pagine nella cartella specificata.

Nell'esempio viene illustrato l'uso di `AddFolderApplicationModelConvention` aggiungendo un'intestazione, `OtherPagesHeader`, alle pagine all'interno della cartella *OtherPages* dell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Richiedere la pagina Page1 dell'esempio in `localhost:5000/OtherPages/Page1` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina OtherPages/Page1 indicano che è stato aggiunto l'elemento OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convenzione per il modello di app cartella**

Utilizzare <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> per creare e aggiungere un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> oggetto che richiama un'azione sull'oggetto <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> per la pagina con il nome specificato.

Nell'esempio viene illustrato l'uso di `AddPageApplicationModelConvention` aggiungendo un'intestazione, `AboutHeader`, alla pagina di informazioni:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina di informazioni indicano che è stato aggiunto l'elemento AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurare un filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Configura il filtro specificato da applicare. È possibile implementare una classe di filtro, ma l'app di esempio illustra come implementare un filtro in un'espressione lambda, che viene implementata in background come una factory che restituisce un filtro:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Viene usato il modello di app della pagina per verificare il percorso relativo per i segmenti che portano alla pagina Page2 nella cartella *OtherPages*. Se la condizione viene superata, viene aggiunta un'intestazione. In caso contrario, viene applicato `EmptyFilter`.

`EmptyFilter` è un [filtro azione](xref:mvc/controllers/filters#action-filters). Poiché i filtri azione vengono ignorati dalle Razor pagine `EmptyFilter` , non ha alcun effetto come previsto se il percorso `OtherPages/Page2`non contiene.

Richiedere la pagina Page2 dell'esempio in `localhost:5000/OtherPages/Page2` ed esaminare le intestazioni per visualizzare il risultato:

![L'elemento OtherPagesPage2Header viene aggiunto alla risposta per Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurare una factory di filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>Configura la factory specificata per applicare [filtri](xref:mvc/controllers/filters) a tutte le Razor pagine.

L'app di esempio illustra un esempio dell'uso di una [factory di filtro](xref:mvc/controllers/filters#ifilterfactory) aggiungendo un'intestazione, `FilterFactoryHeader`, con due valori per le pagine dell'app:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Richiedere la pagina di informazioni dell'esempio in `localhost:5000/About` ed esaminare le intestazioni per visualizzare il risultato:

![Le intestazioni di risposta della pagina informazioni indicano che sono state aggiunte due intestazioni FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtri MVC e il filtro di pagina (IPageFilter)

I [filtri azione](xref:mvc/controllers/filters#action-filters) MVC vengono ignorati dalle Razor pagine Razor , poiché le pagine utilizzano i metodi del gestore. Sono disponibili altri tipi di filtri MVC: [autorizzazione](xref:mvc/controllers/filters#authorization-filters), [eccezione](xref:mvc/controllers/filters#exception-filters), [risorse](xref:mvc/controllers/filters#resource-filters) e [risultati](xref:mvc/controllers/filters#result-filters). Per altre informazioni, vedere l'argomento [Filtri](xref:mvc/controllers/filters).

Il filtro di pagina<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>() è un filtro che si Razor applica alle pagine. Per ulteriori informazioni, vedere [metodi di filtro Razor per le pagine](xref:razor-pages/filter).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
