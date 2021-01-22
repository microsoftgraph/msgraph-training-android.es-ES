<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio incorporará Microsoft Graph en la aplicación. Para esta aplicación, usará el SDK de [Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) realizar llamadas a Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Obtener eventos de calendario de Outlook

En esta sección, ampliará la clase para agregar una función para obtener los eventos del usuario de la semana actual y actualizar para usar `GraphHelper` `CalendarFragment` estas nuevas funciones.

1. Abra **GraphHelper** y agregue las `import` siguientes instrucciones en la parte superior del archivo.

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

1. Agregue las siguientes funciones a la `GraphHelper` clase.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="GetEventsSnippet":::

    > [!NOTE]
    > Ten en cuenta lo que hace `getCalendarView` el código.
    >
    > - La dirección URL a la que se llamará es `/v1.0/me/calendarview` .
    >   - Los `startDateTime` parámetros de consulta y de consulta definen el inicio y el final de la vista de `endDateTime` calendario.
    >   - el encabezado hace que Microsoft Graph devuelva las horas de inicio y finalización de cada evento en la `Prefer: outlook.timezone` zona horaria del usuario.
    >   - La `select` función limita los campos devueltos para cada evento a solo aquellos que la vista usará realmente.
    >   - La `orderby` función ordena los resultados por hora de inicio.
    >   - La `top` función solicita 25 resultados por página.
    > - Se define una devolución de llamada ( ) para comprobar si hay más resultados disponibles y para solicitar `pagingCallback` páginas adicionales si es necesario.

1. Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**. Asigne un nombre a `GraphToIana` la clase y seleccione **Aceptar**.

1. Abra el nuevo archivo y reemplace su contenido por lo siguiente.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphToIana.java" id="GraphToIanaSnippet":::

1. Agregue las siguientes `import` instrucciones a la parte superior del archivo **CalendarFragment.**

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

1. Agregue el siguiente miembro a la `CalendarFragment` clase.

    ```java
    private List<Event> mEventList = null;
    ```

1. Agregue las siguientes funciones a la `CalendarFragment` clase para ocultar y mostrar la barra de progreso.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="ProgressBarSnippet":::

1. Agregue la siguiente función para proporcionar una devolución de llamada para `getCalendarView` la función en `GraphHelper` .

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

1. Reemplace la función `onCreateView` existente en la clase por lo `CalendarFragment` siguiente.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="OnCreateViewSnippet":::

    Observe lo que hace este código. En primer lugar, `acquireTokenSilently` llama para obtener el token de acceso. Llamar a este método cada vez que se necesita un token de acceso es un procedimiento recomendado porque aprovecha las capacidades de almacenamiento en caché y actualización de tokens de MSAL. Internamente, MSAL busca un token almacenado en caché y, a continuación, comprueba si ha expirado. Si el token está presente y no ha expirado, solo devuelve el token almacenado en caché. Si ha expirado, intenta actualizar el token antes de devolverlo.

    Una vez recuperado el token, el código llama al `getCalendarView` método para obtener los eventos del usuario.

1. Ejecuta la aplicación, inicia sesión y pulsa el elemento **de** navegación Calendario en el menú. Debería ver un volcado JSON de los eventos en el registro de depuración en Android Studio.

## <a name="display-the-results"></a>Mostrar los resultados

Ahora puede reemplazar el volcado json por algo para mostrar los resultados de forma fácil de usar. En esta sección, agregará una al fragmento de calendario, creará un diseño para cada elemento de la lista y creará un adaptador de lista personalizado para el que asigna los campos de cada uno a la adecuada en la `ListView` `ListView` `ListView` `Event` `TextView` vista.

1. Reemplace el `TextView` archivo **en app/res/layout/fragment_calendar.xml** por un `ListView` archivo .

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_calendar.xml" highlight="6-11":::

1. Haga clic con el botón secundario en **la carpeta app/res/layout** y seleccione **Nuevo** y, a continuación, archivo de recursos **de diseño.**

1. Asigne un nombre `event_list_item` al archivo, cambie el **elemento Raíz** a y seleccione `RelativeLayout` **Aceptar**.

1. Abra el **event_list_item.xml** archivo y reemplace su contenido por lo siguiente.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/event_list_item.xml":::

1. Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**.

1. Asigne un nombre a `EventListAdapter` la clase y seleccione **Aceptar**.

1. Abra el **archivo EventListAdapter** y reemplace su contenido por lo siguiente.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EventListAdapter.java" id="EventListAdapterSnippet":::

1. Abra la **clase CalendarFragment** y agregue la siguiente función a la clase.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="AddEventsToListSnippet":::

1. Reemplace el código de depuración temporal de la `success` invalidación por `addEventsToList();` .

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="SuccessSnippet" highlight="5":::

1. Ejecuta la aplicación, inicia sesión y pulsa en el **elemento de** navegación Calendario. Debería ver la lista de eventos.

    ![Captura de pantalla de la tabla de eventos](./images/calendar-list.png)
