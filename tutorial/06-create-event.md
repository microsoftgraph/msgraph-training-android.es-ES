<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="30f34-101">En esta sección agregará la capacidad de crear eventos en el calendario del usuario.</span><span class="sxs-lookup"><span data-stu-id="30f34-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="30f34-102">Abra **GraphHelper** y agregue las `import` siguientes instrucciones en la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="30f34-102">Open **GraphHelper** and add the following `import` statements to the top of the file.</span></span>

    ```java
    import com.microsoft.graph.models.extensions.Attendee;
    import com.microsoft.graph.models.extensions.DateTimeTimeZone;
    import com.microsoft.graph.models.extensions.EmailAddress;
    import com.microsoft.graph.models.extensions.ItemBody;
    import com.microsoft.graph.models.generated.AttendeeType;
    import com.microsoft.graph.models.generated.BodyType;
    ```

1. <span data-ttu-id="30f34-103">Agregue la siguiente función a la `GraphHelper` clase para crear un nuevo evento.</span><span class="sxs-lookup"><span data-stu-id="30f34-103">Add the following function to the `GraphHelper` class to create a new event.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="CreateEventSnippet":::

## <a name="update-new-event-fragment"></a><span data-ttu-id="30f34-104">Actualizar nuevo fragmento de evento</span><span class="sxs-lookup"><span data-stu-id="30f34-104">Update new event fragment</span></span>

1. <span data-ttu-id="30f34-105">Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**.</span><span class="sxs-lookup"><span data-stu-id="30f34-105">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="30f34-106">Asigne un nombre a `EditTextDateTimePicker` la clase y seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="30f34-106">Name the class `EditTextDateTimePicker` and select **OK**.</span></span>

1. <span data-ttu-id="30f34-107">Abra el nuevo archivo y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="30f34-107">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EditTextDateTimePicker.java" id="DateTimePickerSnippet":::

    <span data-ttu-id="30f34-108">Esta clase encapsula un control, que muestra un selector de fecha y hora cuando el usuario lo pulsa y actualiza el valor con la fecha y `EditText` hora seleccionados.</span><span class="sxs-lookup"><span data-stu-id="30f34-108">This class wraps an `EditText` control, showing a date and time picker when the user taps it, and updating the value with the date and time picked.</span></span>

1. <span data-ttu-id="30f34-109">Abre **app/res/layout/fragment_new_event.xml** y reemplaza su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="30f34-109">Open **app/res/layout/fragment_new_event.xml** and replace its contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_new_event.xml":::

1. <span data-ttu-id="30f34-110">Abra **NewEventFragment y** agregue las `import` siguientes instrucciones en la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="30f34-110">Open **NewEventFragment** and add the following `import` statements at the top of the file.</span></span>

    ```java
    import android.util.Log;
    import android.widget.Button;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.google.android.material.textfield.TextInputLayout;
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.Event;
    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalException;
    import java.time.ZoneId;
    import java.time.ZonedDateTime;
    ```

1. <span data-ttu-id="30f34-111">Agregue los siguientes miembros a la `NewEventFragment` clase.</span><span class="sxs-lookup"><span data-stu-id="30f34-111">Add the following members to the `NewEventFragment` class.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="InputsSnippet":::

1. <span data-ttu-id="30f34-112">Agregue las siguientes funciones para mostrar y ocultar una barra de progreso.</span><span class="sxs-lookup"><span data-stu-id="30f34-112">Add the following functions to show and hide a progress bar.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="ProgressBarSnippet":::

1. <span data-ttu-id="30f34-113">Agregue las siguientes funciones para obtener los valores de los controles de entrada y llamar a la `GraphHelper.createEvent` función.</span><span class="sxs-lookup"><span data-stu-id="30f34-113">Add the following functions to get the values from the input controls and call the `GraphHelper.createEvent` function.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="CreateEventSnippet":::

1. <span data-ttu-id="30f34-114">Reemplace el existente `onCreateView` por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="30f34-114">Replace the existing `onCreateView` with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="OnCreateViewSnippet":::

1. <span data-ttu-id="30f34-115">Guarde los cambios y reinicie la aplicación.</span><span class="sxs-lookup"><span data-stu-id="30f34-115">Save your changes and restart the app.</span></span> <span data-ttu-id="30f34-116">Seleccione el **elemento de menú Nuevo** evento, rellene el formulario y seleccione **CREAR**.</span><span class="sxs-lookup"><span data-stu-id="30f34-116">Select the **New Event** menu item, fill in the form, and select **CREATE**.</span></span>

    ![Captura de pantalla del formulario de creación de eventos en la aplicación](images/create-event.png)
