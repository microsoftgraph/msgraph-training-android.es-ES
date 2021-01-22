<!-- markdownlint-disable MD002 MD041 -->

En esta sección agregará la capacidad de crear eventos en el calendario del usuario.

1. Abra **GraphHelper** y agregue las `import` siguientes instrucciones en la parte superior del archivo.

    ```java
    import com.microsoft.graph.models.extensions.Attendee;
    import com.microsoft.graph.models.extensions.DateTimeTimeZone;
    import com.microsoft.graph.models.extensions.EmailAddress;
    import com.microsoft.graph.models.extensions.ItemBody;
    import com.microsoft.graph.models.generated.AttendeeType;
    import com.microsoft.graph.models.generated.BodyType;
    ```

1. Agregue la siguiente función a la `GraphHelper` clase para crear un nuevo evento.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="CreateEventSnippet":::

## <a name="update-new-event-fragment"></a>Actualizar nuevo fragmento de evento

1. Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**. Asigne un nombre a `EditTextDateTimePicker` la clase y seleccione **Aceptar**.

1. Abra el nuevo archivo y reemplace su contenido por lo siguiente.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EditTextDateTimePicker.java" id="DateTimePickerSnippet":::

    Esta clase encapsula un control, que muestra un selector de fecha y hora cuando el usuario lo pulsa y actualiza el valor con la fecha y `EditText` hora seleccionados.

1. Abre **app/res/layout/fragment_new_event.xml** y reemplaza su contenido por lo siguiente.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_new_event.xml":::

1. Abra **NewEventFragment y** agregue las `import` siguientes instrucciones en la parte superior del archivo.

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

1. Agregue los siguientes miembros a la `NewEventFragment` clase.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="InputsSnippet":::

1. Agregue las siguientes funciones para mostrar y ocultar una barra de progreso.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="ProgressBarSnippet":::

1. Agregue las siguientes funciones para obtener los valores de los controles de entrada y llamar a la `GraphHelper.createEvent` función.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="CreateEventSnippet":::

1. Reemplace el existente `onCreateView` por lo siguiente.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="OnCreateViewSnippet":::

1. Guarde los cambios y reinicie la aplicación. Seleccione el **elemento de menú Nuevo** evento, rellene el formulario y seleccione **CREAR**.

    ![Captura de pantalla del formulario de creación de eventos en la aplicación](images/create-event.png)
