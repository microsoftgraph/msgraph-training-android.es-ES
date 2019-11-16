<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="0f679-101">En este ejercicio, incorporará Microsoft Graph a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0f679-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="0f679-102">Para esta aplicación, usará el [SDK de Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) para realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0f679-102">For this application, you will use the [Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="0f679-103">Obtener eventos de calendario de Outlook</span><span class="sxs-lookup"><span data-stu-id="0f679-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="0f679-104">En esta sección, ampliará la `GraphHelper` clase para agregar una función para obtener los eventos del usuario y actualizar `CalendarFragment` para usar estas funciones nuevas.</span><span class="sxs-lookup"><span data-stu-id="0f679-104">In this section you will extend the `GraphHelper` class to add a function to get the user's events and update `CalendarFragment` to use these new functions.</span></span>

1. <span data-ttu-id="0f679-105">Abra el archivo **GraphHelper** y agregue las siguientes `import` instrucciones en la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="0f679-105">Open the **GraphHelper** file and add the following `import` statements to the top of the file.</span></span>

    ```java
    import com.microsoft.graph.options.Option;
    import com.microsoft.graph.options.QueryOption;
    import com.microsoft.graph.requests.extensions.IEventCollectionPage;
    import java.util.LinkedList;
    import java.util.List;
    ```

1. <span data-ttu-id="0f679-106">Agregue las siguientes funciones a la `GraphHelper` clase.</span><span class="sxs-lookup"><span data-stu-id="0f679-106">Add the following functions to the `GraphHelper` class.</span></span>

    ```java
    public void getEvents(String accessToken, ICallback<IEventCollectionPage> callback) {
        mAccessToken = accessToken;

        // Use query options to sort by created time
        final List<Option> options = new LinkedList<Option>();
        options.add(new QueryOption("orderby", "createdDateTime DESC"));


        // GET /me/events
        mClient.me().events().buildRequest(options)
                .select("subject,organizer,start,end")
                .get(callback);

    }

    // Debug function to get the JSON representation of a Graph
    // object
    public String serializeObject(Object object) {
        return mClient.getSerializer().serializeObject(object);
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="0f679-107">Tenga en cuenta lo que `getEvents` hace el código.</span><span class="sxs-lookup"><span data-stu-id="0f679-107">Consider what the code in `getEvents` is doing.</span></span>
    >
    > - <span data-ttu-id="0f679-108">La dirección URL a la que se `/v1.0/me/events`llamará es.</span><span class="sxs-lookup"><span data-stu-id="0f679-108">The URL that will be called is `/v1.0/me/events`.</span></span>
    > - <span data-ttu-id="0f679-109">La `select` función limita los campos devueltos para cada evento a simplemente > que la vista usará realmente.</span><span class="sxs-lookup"><span data-stu-id="0f679-109">The `select` function limits the fields returned for each events to just > those the view will actually use.</span></span>
    > - <span data-ttu-id="0f679-110">El `QueryOption` nombre `orderby` se usa para ordenar los resultados por la fecha y la hora de creación, con el elemento más reciente en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="0f679-110">The `QueryOption` named `orderby` is used to sort the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="0f679-111">Agregue las siguientes `import` instrucciones en la parte superior del archivo **CalendarFragment** .</span><span class="sxs-lookup"><span data-stu-id="0f679-111">Add the following `import` statements to the top of the **CalendarFragment** file.</span></span>

    ```java
    import android.util.Log;
    import android.widget.ListView;
    import android.widget.ProgressBar;
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.Event;
    import com.microsoft.graph.requests.extensions.IEventCollectionPage;
    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalException;
    import java.util.List;
    ```

1. <span data-ttu-id="0f679-112">Agregue los siguientes miembros a la `CalendarFragment` clase.</span><span class="sxs-lookup"><span data-stu-id="0f679-112">Add the following members to the `CalendarFragment` class.</span></span>

    ```java
    private List<Event> mEventList = null;
    private ProgressBar mProgress = null;
    ```

1. <span data-ttu-id="0f679-113">Agregue las siguientes funciones a la `CalendarFragment` clase para ocultar y mostrar la barra de progreso, y para proporcionar una devolución de `getEvents` llamada para `GraphHelper`la función en.</span><span class="sxs-lookup"><span data-stu-id="0f679-113">Add the following functions to the `CalendarFragment` class to hide and show the progress bar, and to provide a callback for the `getEvents` function in `GraphHelper`.</span></span>

    ```java
    private void showProgressBar() {
        getActivity().runOnUiThread(new Runnable() {
            @Override
            public void run() {
                mProgress.setVisibility(View.VISIBLE);
            }
        });
    }

    private void hideProgressBar() {
        getActivity().runOnUiThread(new Runnable() {
            @Override
            public void run() {
                mProgress.setVisibility(View.GONE);
            }
        });
    }

    private ICallback<IEventCollectionPage> getEventsCallback() {
        return new ICallback<IEventCollectionPage>() {
            @Override
            public void success(IEventCollectionPage iEventCollectionPage) {
                mEventList = iEventCollectionPage.getCurrentPage();

                // Temporary for debugging
                String jsonEvents = GraphHelper.getInstance().serializeObject(mEventList);
                Log.d("GRAPH", jsonEvents);

                hideProgressBar();
            }

            @Override
            public void failure(ClientException ex) {
                Log.e("GRAPH", "Error getting events", ex);
                hideProgressBar();
            }
        };
    }
    ```

1. <span data-ttu-id="0f679-114">Reemplace la `onCreate` función de la `CalendarFragment` clase para obtener los eventos del usuario de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0f679-114">Override the `onCreate` function in the `CalendarFragment` class to get the user's events from Microsoft Graph.</span></span>

    ```java
    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mProgress = getActivity().findViewById(R.id.progressbar);
        showProgressBar();

        // Get a current access token
        AuthenticationHelper.getInstance()
                .acquireTokenSilently(new AuthenticationCallback() {
                    @Override
                    public void onSuccess(IAuthenticationResult authenticationResult) {
                        final GraphHelper graphHelper = GraphHelper.getInstance();

                        // Get the user's events
                        graphHelper.getEvents(authenticationResult.getAccessToken(),
                                getEventsCallback());
                    }

                    @Override
                    public void onError(MsalException exception) {
                        Log.e("AUTH", "Could not get token silently", exception);
                        hideProgressBar();
                    }

                    @Override
                    public void onCancel() {
                        hideProgressBar();
                    }
                });
    }
    ```

<span data-ttu-id="0f679-115">Observe lo que hace el código.</span><span class="sxs-lookup"><span data-stu-id="0f679-115">Notice what this code does.</span></span> <span data-ttu-id="0f679-116">En primer lugar, `acquireTokenSilently` llama a para obtener el token de acceso.</span><span class="sxs-lookup"><span data-stu-id="0f679-116">First, it calls `acquireTokenSilently` to get the access token.</span></span> <span data-ttu-id="0f679-117">Llamar a este método cada vez que se necesita un token de acceso es un procedimiento recomendado porque aprovecha las capacidades de almacenamiento en caché y actualización de los tokens de MSAL.</span><span class="sxs-lookup"><span data-stu-id="0f679-117">Calling this method every time an access token is needed is a best practice because it takes advantage of MSAL's caching and token refresh abilities.</span></span> <span data-ttu-id="0f679-118">Internamente, MSAL comprueba un token almacenado en caché y, a continuación, comprueba si ha expirado.</span><span class="sxs-lookup"><span data-stu-id="0f679-118">Internally, MSAL checks for a cached token, then checks if it is expired.</span></span> <span data-ttu-id="0f679-119">Si el token está presente y no ha expirado, simplemente devuelve el token almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="0f679-119">If the token is present and not expired, it just returns the cached token.</span></span> <span data-ttu-id="0f679-120">Si ha expirado, intenta actualizar el token antes de devolverlo.</span><span class="sxs-lookup"><span data-stu-id="0f679-120">If it is expired, it attempts to refresh the token before returning it.</span></span>

<span data-ttu-id="0f679-121">Una vez que se recupera el token, el código llama `getEvents` al método para obtener los eventos del usuario.</span><span class="sxs-lookup"><span data-stu-id="0f679-121">Once the token is retrieved, the code then calls the `getEvents` method to get the user's events.</span></span>

<span data-ttu-id="0f679-122">Ahora puede ejecutar la aplicación, iniciar sesión y puntear en el elemento de navegación **calendario** del menú.</span><span class="sxs-lookup"><span data-stu-id="0f679-122">You can now run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="0f679-123">Debe ver un volcado JSON de los eventos en el registro de depuración en Android Studio.</span><span class="sxs-lookup"><span data-stu-id="0f679-123">You should see a JSON dump of the events in the debug log in Android Studio.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="0f679-124">Mostrar los resultados</span><span class="sxs-lookup"><span data-stu-id="0f679-124">Display the results</span></span>

<span data-ttu-id="0f679-125">Ahora puede reemplazar el volcado de JSON con algo para mostrar los resultados de forma fácil de uso.</span><span class="sxs-lookup"><span data-stu-id="0f679-125">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="0f679-126">En `ListView` esta sección, agregará un al fragmento de calendario, creará un diseño para cada elemento en el `ListView`y creará un adaptador de lista personalizado para `ListView` el que asigna los campos de `Event` cada uno de `TextView` ellos al correspondiente en la vista.</span><span class="sxs-lookup"><span data-stu-id="0f679-126">In this section, you will add a `ListView` to the calendar fragment, create a layout for each item in the `ListView`, and create a custom list adapter for the `ListView` that maps the fields of each `Event` to the appropriate `TextView` in the view.</span></span>

1. <span data-ttu-id="0f679-127">Reemplace `TextView` en **App/res/layout/fragment_calendar. XML** por un `ListView`.</span><span class="sxs-lookup"><span data-stu-id="0f679-127">Replace the `TextView` in **app/res/layout/fragment_calendar.xml** with a `ListView`.</span></span>

    ```xml
    <ListView
        android:id="@+id/eventlist"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:divider="@color/colorPrimary"
        android:dividerHeight="1dp" />
    ```

1. <span data-ttu-id="0f679-128">Haga clic con el botón derecho en la carpeta **App/res/layout** y seleccione **nuevo**y, a continuación, **archivo de recursos de distribución**.</span><span class="sxs-lookup"><span data-stu-id="0f679-128">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="0f679-129">Asigne un nombre `event_list_item`al archivo, cambie el **elemento raíz** a `RelativeLayout`y seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="0f679-129">Name the file `event_list_item`, change the **Root element** to `RelativeLayout`, and select **OK**.</span></span>

1. <span data-ttu-id="0f679-130">Abra el archivo **event_list_item. XML** y reemplace el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="0f679-130">Open the **event_list_item.xml** file and replace its contents with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:padding="10dp">

        <TextView
            android:id="@+id/eventsubject"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Subject"
            android:textSize="20sp" />

        <TextView
            android:id="@+id/eventorganizer"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_below="@id/eventsubject"
            android:text="Adele Vance"
            android:textSize="15sp" />

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_below="@id/eventorganizer"
            android:orientation="horizontal">

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:paddingEnd="2sp"
                android:text="Start:"
                android:textSize="15sp"
                android:textStyle="bold" />

            <TextView
                android:id="@+id/eventstart"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="1:30 PM 2/19/2019"
                android:textSize="15sp" />

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:paddingStart="5sp"
                android:paddingEnd="2sp"
                android:text="End:"
                android:textSize="15sp"
                android:textStyle="bold" />

            <TextView
                android:id="@+id/eventend"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="1:30 PM 2/19/2019"
                android:textSize="15sp" />
        </LinearLayout>
    </RelativeLayout>
    ```

1. <span data-ttu-id="0f679-131">Haga clic con el botón secundario en la carpeta **app/java/com. example. graphtutorial** y seleccione **nuevo**y, a continuación, **clase Java**.</span><span class="sxs-lookup"><span data-stu-id="0f679-131">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="0f679-132">Asigne un nombre `EventListAdapter` a la clase y seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="0f679-132">Name the class `EventListAdapter` and select **OK**.</span></span>

1. <span data-ttu-id="0f679-133">Abra el archivo **EventListAdapter** y reemplace el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="0f679-133">Open the **EventListAdapter** file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.content.Context;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import android.widget.ArrayAdapter;
    import android.widget.TextView;
    import androidx.annotation.NonNull;
    import com.microsoft.graph.models.extensions.DateTimeTimeZone;
    import com.microsoft.graph.models.extensions.Event;
    import java.time.LocalDateTime;
    import java.time.ZoneId;
    import java.time.ZonedDateTime;
    import java.time.format.DateTimeFormatter;
    import java.time.format.FormatStyle;
    import java.util.List;
    import java.util.TimeZone;

    public class EventListAdapter extends ArrayAdapter<Event> {
        private Context mContext;
        private int mResource;
        private ZoneId mLocalTimeZoneId;

        // Used for the ViewHolder pattern
        // https://developer.android.com/training/improving-layouts/smooth-scrolling
        static class ViewHolder {
            TextView subject;
            TextView organizer;
            TextView start;
            TextView end;
        }

        public EventListAdapter(Context context, int resource, List<Event> events) {
            super(context, resource, events);
            mContext = context;
            mResource = resource;
            mLocalTimeZoneId = TimeZone.getDefault().toZoneId();
        }

        @NonNull
        @Override
        public View getView(int position, View convertView, ViewGroup parent) {
            Event event = getItem(position);

            ViewHolder holder;

            if (convertView == null) {
                LayoutInflater inflater = LayoutInflater.from(mContext);
                convertView = inflater.inflate(mResource, parent, false);

                holder = new ViewHolder();
                holder.subject = convertView.findViewById(R.id.eventsubject);
                holder.organizer = convertView.findViewById(R.id.eventorganizer);
                holder.start = convertView.findViewById(R.id.eventstart);
                holder.end = convertView.findViewById(R.id.eventend);

                convertView.setTag(holder);
            } else {
                holder = (ViewHolder) convertView.getTag();
            }

            holder.subject.setText(event.subject);
            holder.organizer.setText(event.organizer.emailAddress.name);
            holder.start.setText(getLocalDateTimeString(event.start));
            holder.end.setText(getLocalDateTimeString(event.end));

            return convertView;
        }

        // Convert Graph's DateTimeTimeZone format to
        // a LocalDateTime, then return a formatted string
        private String getLocalDateTimeString(DateTimeTimeZone dateTime) {
            ZonedDateTime localDateTime = LocalDateTime.parse(dateTime.dateTime)
                    .atZone(ZoneId.of(dateTime.timeZone))
                    .withZoneSameInstant(mLocalTimeZoneId);

            return String.format("%s %s",
                    localDateTime.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM)),
                    localDateTime.format(DateTimeFormatter.ofLocalizedTime(FormatStyle.SHORT)));
        }
    }
    ```

1. <span data-ttu-id="0f679-134">Abra la clase **CalendarFragment** y agregue la siguiente función a la clase.</span><span class="sxs-lookup"><span data-stu-id="0f679-134">Open the **CalendarFragment** class and add the following function to the class.</span></span>

    ```java
    private void addEventsToList() {
        getActivity().runOnUiThread(new Runnable() {
            @Override
            public void run() {
                ListView eventListView = getView().findViewById(R.id.eventlist);

                EventListAdapter listAdapter = new EventListAdapter(getActivity(),
                        R.layout.event_list_item, mEventList);

                eventListView.setAdapter(listAdapter);
            }
        });
    }
    ```

1. <span data-ttu-id="0f679-135">Agregue la siguiente línea de código en la `success` invalidación después `mEventList = iEventCollectionPage.getCurrentPage();` de la línea.</span><span class="sxs-lookup"><span data-stu-id="0f679-135">Add the following line of code in the `success` override after the `mEventList = iEventCollectionPage.getCurrentPage();` line.</span></span>

    ```java
    addEventsToList();
    ```

1. <span data-ttu-id="0f679-136">Ejecute la aplicación, inicie sesión y pulse el elemento de navegación **calendario** .</span><span class="sxs-lookup"><span data-stu-id="0f679-136">Run the app, sign in, and tap the **Calendar** navigation item.</span></span> <span data-ttu-id="0f679-137">Debe ver la lista de eventos.</span><span class="sxs-lookup"><span data-stu-id="0f679-137">You should see the list of events.</span></span>

    ![Captura de pantalla de la tabla de eventos](./images/calendar-list.png)
