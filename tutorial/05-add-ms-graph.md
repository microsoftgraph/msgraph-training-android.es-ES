<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="09b14-101">En este ejercicio incorporará Microsoft Graph en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="09b14-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="09b14-102">Para esta aplicación, usará el SDK de [Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="09b14-102">For this application, you will use the [Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="09b14-103">Obtener eventos de calendario de Outlook</span><span class="sxs-lookup"><span data-stu-id="09b14-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="09b14-104">En esta sección, ampliará la clase para agregar una función para obtener los eventos del usuario de la semana actual y actualizar para usar `GraphHelper` `CalendarFragment` estas nuevas funciones.</span><span class="sxs-lookup"><span data-stu-id="09b14-104">In this section you will extend the `GraphHelper` class to add a function to get the user's events for the current week and update `CalendarFragment` to use these new functions.</span></span>

1. <span data-ttu-id="09b14-105">Abra **GraphHelper** y agregue las `import` siguientes instrucciones en la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="09b14-105">Open **GraphHelper** and add the following `import` statements to the top of the file.</span></span>

    ```java
    import com.microsoft.graph.options.Option;
    import com.microsoft.graph.options.HeaderOption;
    import com.microsoft.graph.options.QueryOption;
    import com.microsoft.graph.requests.extensions.IEventCollectionPage;
    import com.microsoft.graph.requests.extensions.IEventCollectionRequestBuilder;
    import java.time.ZonedDateTime;
    import java.time.format.DateTimeFormatter;
    import java.util.LinkedList;
    import java.util.List;
    ```

1. <span data-ttu-id="09b14-106">Agregue las siguientes funciones a la `GraphHelper` clase.</span><span class="sxs-lookup"><span data-stu-id="09b14-106">Add the following functions to the `GraphHelper` class.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="GetEventsSnippet":::

    > [!NOTE]
    > <span data-ttu-id="09b14-107">Ten en cuenta lo que hace `getCalendarView` el código.</span><span class="sxs-lookup"><span data-stu-id="09b14-107">Consider what the code in `getCalendarView` is doing.</span></span>
    >
    > - <span data-ttu-id="09b14-108">La dirección URL a la que se llamará es `/v1.0/me/calendarview` .</span><span class="sxs-lookup"><span data-stu-id="09b14-108">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
    >   - <span data-ttu-id="09b14-109">Los `startDateTime` parámetros de consulta y de consulta definen el inicio y el final de la vista de `endDateTime` calendario.</span><span class="sxs-lookup"><span data-stu-id="09b14-109">The `startDateTime` and `endDateTime` query parameters define the start and end of the calendar view.</span></span>
    >   - <span data-ttu-id="09b14-110">el encabezado hace que Microsoft Graph devuelva las horas de inicio y finalización de cada evento en la `Prefer: outlook.timezone` zona horaria del usuario.</span><span class="sxs-lookup"><span data-stu-id="09b14-110">the `Prefer: outlook.timezone` header causes the Microsoft Graph to return the start and end times of each event in the user's time zone.</span></span>
    >   - <span data-ttu-id="09b14-111">La `select` función limita los campos devueltos para cada evento a solo aquellos que la vista usará realmente.</span><span class="sxs-lookup"><span data-stu-id="09b14-111">The `select` function limits the fields returned for each events to just those the view will actually use.</span></span>
    >   - <span data-ttu-id="09b14-112">La `orderby` función ordena los resultados por hora de inicio.</span><span class="sxs-lookup"><span data-stu-id="09b14-112">The `orderby` function sorts the results by start time.</span></span>
    >   - <span data-ttu-id="09b14-113">La `top` función solicita 25 resultados por página.</span><span class="sxs-lookup"><span data-stu-id="09b14-113">The `top` function requests 25 results per page.</span></span>
    > - <span data-ttu-id="09b14-114">Se define una devolución de llamada ( ) para comprobar si hay más resultados disponibles y para solicitar `pagingCallback` páginas adicionales si es necesario.</span><span class="sxs-lookup"><span data-stu-id="09b14-114">A callback is defined (`pagingCallback`) to check if there are more results available and to request additional pages if needed.</span></span>

1. <span data-ttu-id="09b14-115">Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**.</span><span class="sxs-lookup"><span data-stu-id="09b14-115">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="09b14-116">Asigne un nombre a `GraphToIana` la clase y seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="09b14-116">Name the class `GraphToIana` and select **OK**.</span></span>

1. <span data-ttu-id="09b14-117">Abra el nuevo archivo y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="09b14-117">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphToIana.java" id="GraphToIanaSnippet":::

1. <span data-ttu-id="09b14-118">Agregue las siguientes `import` instrucciones a la parte superior del archivo **CalendarFragment.**</span><span class="sxs-lookup"><span data-stu-id="09b14-118">Add the following `import` statements to the top of the **CalendarFragment** file.</span></span>

    ```java
    import android.util.Log;
    import android.widget.ListView;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.Event;
    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalException;
    import java.time.DayOfWeek;
    import java.time.ZoneId;
    import java.time.ZonedDateTime;
    import java.time.temporal.ChronoUnit;
    import java.time.temporal.TemporalAdjusters;
    import java.util.List;
    ```

1. <span data-ttu-id="09b14-119">Agregue el siguiente miembro a la `CalendarFragment` clase.</span><span class="sxs-lookup"><span data-stu-id="09b14-119">Add the following member to the `CalendarFragment` class.</span></span>

    ```java
    private List<Event> mEventList = null;
    ```

1. <span data-ttu-id="09b14-120">Agregue las siguientes funciones a la `CalendarFragment` clase para ocultar y mostrar la barra de progreso.</span><span class="sxs-lookup"><span data-stu-id="09b14-120">Add the following functions to the `CalendarFragment` class to hide and show the progress bar.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="ProgressBarSnippet":::

1. <span data-ttu-id="09b14-121">Agregue la siguiente función para proporcionar una devolución de llamada para `getCalendarView` la función en `GraphHelper` .</span><span class="sxs-lookup"><span data-stu-id="09b14-121">Add the following function to provide a callback for the `getCalendarView` function in `GraphHelper`.</span></span>

    ```java
    private ICallback<List<Event>> getCalendarViewCallback() {
        return new ICallback<List<Event>>() {
            @Override
            public void success(List<Event> eventList) {
                mEventList = eventList;

                // Temporary for debugging
                String jsonEvents = GraphHelper.getInstance().serializeObject(mEventList);
                Log.d("GRAPH", jsonEvents);

                hideProgressBar();
            }

            @Override
            public void failure(ClientException ex) {
                hideProgressBar();
                Log.e("GRAPH", "Error getting events", ex);
                Snackbar.make(getView(),
                    ex.getMessage(),
                    BaseTransientBottomBar.LENGTH_LONG).show();
            }
        };
    }
    ```

1. <span data-ttu-id="09b14-122">Reemplace la función `onCreateView` existente en la clase por lo `CalendarFragment` siguiente.</span><span class="sxs-lookup"><span data-stu-id="09b14-122">Replace the existing `onCreateView` function in the `CalendarFragment` class with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="OnCreateViewSnippet":::

    <span data-ttu-id="09b14-123">Observe lo que hace este código.</span><span class="sxs-lookup"><span data-stu-id="09b14-123">Notice what this code does.</span></span> <span data-ttu-id="09b14-124">En primer lugar, `acquireTokenSilently` llama para obtener el token de acceso.</span><span class="sxs-lookup"><span data-stu-id="09b14-124">First, it calls `acquireTokenSilently` to get the access token.</span></span> <span data-ttu-id="09b14-125">Llamar a este método cada vez que se necesita un token de acceso es un procedimiento recomendado porque aprovecha las capacidades de almacenamiento en caché y actualización de tokens de MSAL.</span><span class="sxs-lookup"><span data-stu-id="09b14-125">Calling this method every time an access token is needed is a best practice because it takes advantage of MSAL's caching and token refresh abilities.</span></span> <span data-ttu-id="09b14-126">Internamente, MSAL busca un token almacenado en caché y, a continuación, comprueba si ha expirado.</span><span class="sxs-lookup"><span data-stu-id="09b14-126">Internally, MSAL checks for a cached token, then checks if it is expired.</span></span> <span data-ttu-id="09b14-127">Si el token está presente y no ha expirado, solo devuelve el token almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="09b14-127">If the token is present and not expired, it just returns the cached token.</span></span> <span data-ttu-id="09b14-128">Si ha expirado, intenta actualizar el token antes de devolverlo.</span><span class="sxs-lookup"><span data-stu-id="09b14-128">If it is expired, it attempts to refresh the token before returning it.</span></span>

    <span data-ttu-id="09b14-129">Una vez recuperado el token, el código llama al `getCalendarView` método para obtener los eventos del usuario.</span><span class="sxs-lookup"><span data-stu-id="09b14-129">Once the token is retrieved, the code then calls the `getCalendarView` method to get the user's events.</span></span>

1. <span data-ttu-id="09b14-130">Ejecuta la aplicación, inicia sesión y pulsa el elemento **de** navegación Calendario en el menú.</span><span class="sxs-lookup"><span data-stu-id="09b14-130">Run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="09b14-131">Debería ver un volcado JSON de los eventos en el registro de depuración en Android Studio.</span><span class="sxs-lookup"><span data-stu-id="09b14-131">You should see a JSON dump of the events in the debug log in Android Studio.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="09b14-132">Mostrar los resultados</span><span class="sxs-lookup"><span data-stu-id="09b14-132">Display the results</span></span>

<span data-ttu-id="09b14-133">Ahora puede reemplazar el volcado json por algo para mostrar los resultados de forma fácil de usar.</span><span class="sxs-lookup"><span data-stu-id="09b14-133">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="09b14-134">En esta sección, agregará una al fragmento de calendario, creará un diseño para cada elemento de la lista y creará un adaptador de lista personalizado para el que asigna los campos de cada uno a la adecuada en la `ListView` `ListView` `ListView` `Event` `TextView` vista.</span><span class="sxs-lookup"><span data-stu-id="09b14-134">In this section, you will add a `ListView` to the calendar fragment, create a layout for each item in the `ListView`, and create a custom list adapter for the `ListView` that maps the fields of each `Event` to the appropriate `TextView` in the view.</span></span>

1. <span data-ttu-id="09b14-135">Reemplace el `TextView` archivo **en app/res/layout/fragment_calendar.xml** por un `ListView` archivo .</span><span class="sxs-lookup"><span data-stu-id="09b14-135">Replace the `TextView` in **app/res/layout/fragment_calendar.xml** with a `ListView`.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_calendar.xml" highlight="6-11":::

1. <span data-ttu-id="09b14-136">Haga clic con el botón secundario en **la carpeta app/res/layout** y seleccione **Nuevo** y, a continuación, archivo de recursos **de diseño.**</span><span class="sxs-lookup"><span data-stu-id="09b14-136">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="09b14-137">Asigne un nombre `event_list_item` al archivo, cambie el **elemento Raíz** a y seleccione `RelativeLayout` **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="09b14-137">Name the file `event_list_item`, change the **Root element** to `RelativeLayout`, and select **OK**.</span></span>

1. <span data-ttu-id="09b14-138">Abra el **event_list_item.xml** archivo y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="09b14-138">Open the **event_list_item.xml** file and replace its contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/event_list_item.xml":::

1. <span data-ttu-id="09b14-139">Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**.</span><span class="sxs-lookup"><span data-stu-id="09b14-139">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="09b14-140">Asigne un nombre a `EventListAdapter` la clase y seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="09b14-140">Name the class `EventListAdapter` and select **OK**.</span></span>

1. <span data-ttu-id="09b14-141">Abra el **archivo EventListAdapter** y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="09b14-141">Open the **EventListAdapter** file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EventListAdapter.java" id="EventListAdapterSnippet":::

1. <span data-ttu-id="09b14-142">Abra la **clase CalendarFragment** y agregue la siguiente función a la clase.</span><span class="sxs-lookup"><span data-stu-id="09b14-142">Open the **CalendarFragment** class and add the following function to the class.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="AddEventsToListSnippet":::

1. <span data-ttu-id="09b14-143">Reemplace el código de depuración temporal de la `success` invalidación por `addEventsToList();` .</span><span class="sxs-lookup"><span data-stu-id="09b14-143">Replace the temporary debugging code from the `success` override with `addEventsToList();`.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="SuccessSnippet" highlight="5":::

1. <span data-ttu-id="09b14-144">Ejecuta la aplicación, inicia sesión y pulsa en el **elemento de** navegación Calendario.</span><span class="sxs-lookup"><span data-stu-id="09b14-144">Run the app, sign in, and tap the **Calendar** navigation item.</span></span> <span data-ttu-id="09b14-145">Debería ver la lista de eventos.</span><span class="sxs-lookup"><span data-stu-id="09b14-145">You should see the list of events.</span></span>

    ![Captura de pantalla de la tabla de eventos](./images/calendar-list.png)
