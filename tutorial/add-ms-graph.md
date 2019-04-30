<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="24227-101">En este ejercicio, incorporará Microsoft Graph a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="24227-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="24227-102">Para esta aplicación, usará el [SDK de Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) para realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="24227-102">For this application, you will use the [Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="24227-103">Obtener eventos de calendario de Outlook</span><span class="sxs-lookup"><span data-stu-id="24227-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="24227-104">Empiece extendiendo la `GraphHelper` clase para agregar una función para obtener los eventos del usuario.</span><span class="sxs-lookup"><span data-stu-id="24227-104">Start by extending the `GraphHelper` class to add a function to get the user's events.</span></span> <span data-ttu-id="24227-105">Abra el archivo **GraphHelper** y agregue las siguientes `import` instrucciones en la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="24227-105">Open the **GraphHelper** file and add the following `import` statements to the top of the file.</span></span>

```java
import com.microsoft.graph.options.Option;
import com.microsoft.graph.options.QueryOption;
import com.microsoft.graph.requests.extensions.IEventCollectionPage;
import java.util.LinkedList;
import java.util.List;
```

<span data-ttu-id="24227-106">Agregue las siguientes funciones a la `GraphHelper` clase.</span><span class="sxs-lookup"><span data-stu-id="24227-106">Add the following functions to the `GraphHelper` class.</span></span>

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

<span data-ttu-id="24227-107">Tenga en cuenta lo que `getEvents` hace el código.</span><span class="sxs-lookup"><span data-stu-id="24227-107">Consider what the code in `getEvents` is doing.</span></span>

- <span data-ttu-id="24227-108">La dirección URL a la que se `/v1.0/me/events`llamará es.</span><span class="sxs-lookup"><span data-stu-id="24227-108">The URL that will be called is `/v1.0/me/events`.</span></span>
- <span data-ttu-id="24227-109">La `select` función limita los campos devueltos para cada evento a solo aquellos que la vista usará realmente.</span><span class="sxs-lookup"><span data-stu-id="24227-109">The `select` function limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="24227-110">El `QueryOption` nombre `orderby` se usa para ordenar los resultados por la fecha y la hora de creación, con el elemento más reciente en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="24227-110">The `QueryOption` named `orderby` is used to sort the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="24227-111">Ahora actualice `CalendarFragment` para usar estas nuevas funciones.</span><span class="sxs-lookup"><span data-stu-id="24227-111">Now update `CalendarFragment` to use these new functions.</span></span> <span data-ttu-id="24227-112">Agregue las siguientes `import` instrucciones en la parte superior del archivo **CalendarFragment** .</span><span class="sxs-lookup"><span data-stu-id="24227-112">Add the following `import` statements to the top of the **CalendarFragment** file.</span></span>

```java
import android.util.Log;
import android.widget.ListView;
import android.widget.ProgressBar;
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.models.extensions.Event;
import com.microsoft.graph.requests.extensions.IEventCollectionPage;
import com.microsoft.identity.client.AuthenticationCallback;
import com.microsoft.identity.client.AuthenticationResult;
import com.microsoft.identity.client.exception.MsalException;
import java.util.List;
```

<span data-ttu-id="24227-113">Agregue los siguientes miembros a la `CalendarFragment` clase.</span><span class="sxs-lookup"><span data-stu-id="24227-113">Add the following members to the `CalendarFragment` class.</span></span>

```java
private List<Event> mEventList = null;
private ProgressBar mProgress = null;
```

<span data-ttu-id="24227-114">Ahora, agregue las siguientes funciones a `CalendarFragment` la clase para ocultar y mostrar la barra de progreso, y para proporcionar una devolución `getEvents` de llamada `GraphHelper`para la función en.</span><span class="sxs-lookup"><span data-stu-id="24227-114">Now add the following functions to the `CalendarFragment` class to hide and show the progress bar, and to provide a callback for the `getEvents` function in `GraphHelper`.</span></span>

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

<span data-ttu-id="24227-115">Por último, reemplace `onCreate` la función de `GraphHelper` la clase para obtener los eventos del usuario de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="24227-115">Finally, override the `onCreate` function in the `GraphHelper` class to get the user's events from Microsoft Graph.</span></span>

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
                public void onSuccess(AuthenticationResult authenticationResult) {
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

<span data-ttu-id="24227-116">Observe lo que hace el código.</span><span class="sxs-lookup"><span data-stu-id="24227-116">Notice what this code does.</span></span> <span data-ttu-id="24227-117">En primer lugar, `acquireTokenSilently` llama a para obtener el token de acceso.</span><span class="sxs-lookup"><span data-stu-id="24227-117">First, it calls `acquireTokenSilently` to get the access token.</span></span> <span data-ttu-id="24227-118">Llamar a este método cada vez que se necesita un token de acceso es un procedimiento recomendado porque aprovecha las capacidades de almacenamiento en caché y actualización de los tokens de MSAL.</span><span class="sxs-lookup"><span data-stu-id="24227-118">Calling this method every time an access token is needed is a best practice because it takes advantage of MSAL's caching and token refresh abilities.</span></span> <span data-ttu-id="24227-119">Internamente, MSAL comprueba un token almacenado en caché y, a continuación, comprueba si ha expirado.</span><span class="sxs-lookup"><span data-stu-id="24227-119">Internally, MSAL checks for a cached token, then checks if it is expired.</span></span> <span data-ttu-id="24227-120">Si el token está presente y no ha expirado, simplemente devuelve el token almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="24227-120">If the token is present and not expired, it just returns the cached token.</span></span> <span data-ttu-id="24227-121">Si ha expirado, intenta actualizar el token antes de devolverlo.</span><span class="sxs-lookup"><span data-stu-id="24227-121">If it is expired, it attempts to refresh the token before returning it.</span></span>

<span data-ttu-id="24227-122">Una vez que se recupera el token, el código llama `getEvents` al método para obtener los eventos del usuario.</span><span class="sxs-lookup"><span data-stu-id="24227-122">Once the token is retrieved, the code then calls the `getEvents` method to get the user's events.</span></span>

<span data-ttu-id="24227-123">Ahora puede ejecutar la aplicación, iniciar sesión y puntear en el elemento de navegación **calendario** del menú.</span><span class="sxs-lookup"><span data-stu-id="24227-123">You can now run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="24227-124">Debe ver un volcado JSON de los eventos en el registro de depuración en Android Studio.</span><span class="sxs-lookup"><span data-stu-id="24227-124">You should see a JSON dump of the events in the debug log in Android Studio.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="24227-125">Mostrar los resultados</span><span class="sxs-lookup"><span data-stu-id="24227-125">Display the results</span></span>

<span data-ttu-id="24227-126">Ahora puede reemplazar el volcado de JSON con algo para mostrar los resultados de forma fácil de uso.</span><span class="sxs-lookup"><span data-stu-id="24227-126">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="24227-127">Empiece por reemplazar `TextView` en **App/res/layout/fragment_calendar. XML** por un. `ListView`</span><span class="sxs-lookup"><span data-stu-id="24227-127">Start by replacing the `TextView` in **app/res/layout/fragment_calendar.xml** with a `ListView`.</span></span>

```xml
<ListView
    android:id="@+id/eventlist"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:divider="@color/colorPrimary"
    android:dividerHeight="1dp" />
```

<span data-ttu-id="24227-128">A continuación, cree un diseño para cada elemento en `ListView`el.</span><span class="sxs-lookup"><span data-stu-id="24227-128">Next, create a layout for each item in the `ListView`.</span></span> <span data-ttu-id="24227-129">Haga clic con el botón secundario en la carpeta **App/res/layout** , seleccione **nuevo**y, a continuación, **archivo de recursos de distribución**.</span><span class="sxs-lookup"><span data-stu-id="24227-129">Right-click the **app/res/layout** folder and choose **New**, then **Layout resource file**.</span></span> <span data-ttu-id="24227-130">Asigne un nombre `event_list_item`al archivo, cambie el **elemento raíz** a `RelativeLayout`y elija **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="24227-130">Name the file `event_list_item`, change the **Root element** to `RelativeLayout`, and choose **OK**.</span></span> <span data-ttu-id="24227-131">Abra el archivo **event_list_item. XML** y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="24227-131">Open the **event_list_item.xml** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="24227-132">Ahora, cree un adaptador de lista personalizado `ListView`para el.</span><span class="sxs-lookup"><span data-stu-id="24227-132">Now create a custom list adapter for the `ListView`.</span></span> <span data-ttu-id="24227-133">Esto es necesario para crear las vistas para los elementos de la lista y para asignar los campos de cada uno `Event` de ellos al `TextView` correspondiente en la vista.</span><span class="sxs-lookup"><span data-stu-id="24227-133">This is necessary to create the views for the items in the list, and to map the fields of each `Event` to the appropriate `TextView` in the view.</span></span>

<span data-ttu-id="24227-134">Haga clic con el botón derecho en la carpeta **app/java/com. example. graphtutorial** y seleccione **nuevo**y, a continuación, **clase Java**.</span><span class="sxs-lookup"><span data-stu-id="24227-134">Right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="24227-135">Asigne un nombre `EventListAdapter` a la clase y elija **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="24227-135">Name the class `EventListAdapter` and choose **OK**.</span></span> <span data-ttu-id="24227-136">Abra el archivo **EventListAdapter** y reemplace el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="24227-136">Open the **EventListAdapter** file and replace its contents with the following.</span></span>

```java
package com.example.graphtutorial;

import android.content.Context;
import android.support.annotation.NonNull;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ArrayAdapter;
import android.widget.TextView;

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

<span data-ttu-id="24227-137">Por último, actualice `CalendarFragment` la clase para que `EventListAdapter` use el para `ListView`inicializar.</span><span class="sxs-lookup"><span data-stu-id="24227-137">Finally, update the `CalendarFragment` class to use the `EventListAdapter` to initialize the `ListView`.</span></span> <span data-ttu-id="24227-138">Abra la clase **CalendarFragment** y agregue la siguiente función a la clase.</span><span class="sxs-lookup"><span data-stu-id="24227-138">Open the **CalendarFragment** class and add the following function to the class.</span></span>

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

<span data-ttu-id="24227-139">Agregue la siguiente línea de código en la `success` invalidación después `mEventList = iEventCollectionPage.getCurrentPage();` de la línea.</span><span class="sxs-lookup"><span data-stu-id="24227-139">Add the following line of code in the `success` override after the `mEventList = iEventCollectionPage.getCurrentPage();` line.</span></span>

```java
addEventsToList();
```

<span data-ttu-id="24227-140">Ejecute la aplicación, inicie sesión y pulse el elemento de navegación **calendario** .</span><span class="sxs-lookup"><span data-stu-id="24227-140">Run the app, sign in, and tap the **Calendar** navigation item.</span></span> <span data-ttu-id="24227-141">Debe ver la lista de eventos.</span><span class="sxs-lookup"><span data-stu-id="24227-141">You should see the list of events.</span></span>

![Captura de pantalla de la tabla de eventos](./images/calendar-list.png)
