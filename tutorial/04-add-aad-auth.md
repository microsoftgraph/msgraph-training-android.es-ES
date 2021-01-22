<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD. Esto es necesario para obtener el token de acceso OAuth necesario para llamar a Microsoft Graph. Para ello, integrará la Biblioteca de autenticación de [Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) en la aplicación.

1. Haga clic con el botón **secundario en la carpeta Res** y seleccione **Nuevo** y, a continuación, Directorio de recursos **de Android**.

1. Cambie el **tipo de recurso** a y seleccione `raw` **Aceptar**.

1. Haga clic con el botón secundario en la nueva **carpeta sin** procesar y **seleccione Nuevo** y, a continuación, **Archivo**.

1. Asigne un nombre al `msal_config.json` archivo y seleccione **Aceptar.**

1. Agregue lo siguiente al archivo **msal_config.js** archivo.

    :::code language="json" source="../demo/GraphTutorial/msal_config.json.example":::

1. Reemplaza con el identificador de aplicación del registro `YOUR_APP_ID_HERE` de la aplicación y reemplaza por el nombre del paquete del `com.example.graphtutorial` proyecto.

    > [!IMPORTANT]
    > Si usas el control de código fuente como Git, ahora sería un buen momento para excluir el archivo del control de código fuente para evitar la pérdida involuntaria del identificador `msal_config.json` de la aplicación.

## <a name="implement-sign-in"></a>Implementar el inicio de sesión

En esta sección actualizará el manifiesto para permitir que MSAL use un explorador para autenticar al usuario, registrar el URI de redireccionamiento como lo controla la aplicación, crear una clase auxiliar de autenticación y actualizar la aplicación para iniciar y cerrar sesión.

1. Expanda la **carpeta app/manifests** y abra **AndroidManifest.xml**. Agregue los siguientes elementos encima del `application` elemento.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > Estos permisos son necesarios para que la biblioteca de MSAL autentique al usuario.

1. Agrega el siguiente elemento dentro `application` del elemento, reemplazando la `YOUR_PACKAGE_NAME_HERE` cadena por el nombre del paquete.

    ```xml
    <!--Intent filter to capture authorization code response from the default browser on the
        device calling back to the app after interactive sign in -->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data
                android:scheme="msauth"
                android:host="YOUR_PACKAGE_NAME_HERE"
                android:path="/callback" />
        </intent-filter>
    </activity>
    ```

1. Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**. Cambiar el **tipo a** **interfaz**. Asigne un nombre a la `IAuthenticationHelperCreatedListener` interfaz y seleccione **Aceptar.**

1. Abra el nuevo archivo y reemplace su contenido por lo siguiente.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/IAuthenticationHelperCreatedListener.java" id="ListenerSnippet":::

1. Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**. Asigne un nombre a `AuthenticationHelper` la clase y seleccione **Aceptar**.

1. Abra el nuevo archivo y reemplace su contenido por lo siguiente.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/AuthenticationHelper.java" id="AuthHelperSnippet":::

1. Abra **MainActivity** y agregue las `import` siguientes instrucciones.

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. Agregue las siguientes propiedades de miembro a la `MainActivity` clase.

    ```java
    private AuthenticationHelper mAuthHelper = null;
    private boolean mAttemptInteractiveSignIn = false;
    ```

1. Agregue el siguiente código al final de la función `onCreate`.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="InitialLoginSnippet":::

1. Agregue las siguientes funciones a la `MainActivity` clase.

    ```java
    // Silently sign in - used if there is already a
    // user account in the MSAL cache
    private void doSilentSignIn(boolean shouldAttemptInteractive) {
        mAttemptInteractiveSignIn = shouldAttemptInteractive;
        mAuthHelper.acquireTokenSilently(getAuthCallback());
    }

    // Prompt the user to sign in
    private void doInteractiveSignIn() {
        mAuthHelper.acquireTokenInteractively(this, getAuthCallback());
    }

    // Handles the authentication result
    public AuthenticationCallback getAuthCallback() {
        return new AuthenticationCallback() {

            @Override
            public void onSuccess(IAuthenticationResult authenticationResult) {
                // Log the token for debug purposes
                String accessToken = authenticationResult.getAccessToken();
                Log.d("AUTH", String.format("Access token: %s", accessToken));

                hideProgressBar();

                setSignedInState(true);
                openHomeFragment(mUserName);
            }

            @Override
            public void onError(MsalException exception) {
                // Check the type of exception and handle appropriately
                if (exception instanceof MsalUiRequiredException) {
                    Log.d("AUTH", "Interactive login required");
                    if (mAttemptInteractiveSignIn) {
                        doInteractiveSignIn();
                    }
                } else if (exception instanceof MsalClientException) {
                    if (exception.getErrorCode() == "no_current_account" ||
                        exception.getErrorCode() == "no_account_found") {
                        Log.d("AUTH", "No current account, interactive login required");
                        if (mAttemptInteractiveSignIn) {
                            doInteractiveSignIn();
                        }
                    } else {
                        // Exception inside MSAL, more info inside MsalError.java
                        Log.e("AUTH", "Client error authenticating", exception);
                    }
                } else if (exception instanceof MsalServiceException) {
                    // Exception when communicating with the auth server, likely config issue
                    Log.e("AUTH", "Service error authenticating", exception);
                }
                hideProgressBar();
            }

            @Override
            public void onCancel() {
                // User canceled the authentication
                Log.d("AUTH", "Authentication canceled");
                hideProgressBar();
            }
        };
    }
    ```

1. Reemplace las funciones `signIn` y `signOut` existentes por lo siguiente.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="SignInAndOutSnippet":::

    > [!NOTE]
    > Observe que `signIn` el método realiza un inicio de sesión silencioso (a través de `doSilentSignIn` ). La devolución de llamada de este método realizará un inicio de sesión interactivo si se produce un error en el silencioso. Esto evita tener que preguntar al usuario cada vez que inicia la aplicación.

1. Guarde los cambios y ejecute la aplicación.

1. Al pulsar el elemento de menú **Iniciar** sesión, se abre un explorador en la página de inicio de sesión de Azure AD. Inicie sesión con su cuenta.

Una vez que la aplicación se reanude, debería ver un token de acceso impreso en el registro de depuración de Android Studio.

![Captura de pantalla de la ventana Logcat en Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Obtener detalles del usuario

En esta sección creará una clase auxiliar para contener todas las llamadas a Microsoft Graph y actualizará la clase para que use esta nueva clase para obtener el usuario que ha `MainActivity` iniciado sesión.

1. Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**. Asigne un nombre a `GraphHelper` la clase y seleccione **Aceptar**.

1. Abra el nuevo archivo y reemplace su contenido por lo siguiente.

    ```java
    package com.example.graphtutorial;

    import com.microsoft.graph.authentication.IAuthenticationProvider;
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.http.IHttpRequest;
    import com.microsoft.graph.models.extensions.IGraphServiceClient;
    import com.microsoft.graph.models.extensions.User;
    import com.microsoft.graph.requests.extensions.GraphServiceClient;

    // Singleton class - the app only needs a single instance
    // of the Graph client
    public class GraphHelper implements IAuthenticationProvider {
        private static GraphHelper INSTANCE = null;
        private IGraphServiceClient mClient = null;
        private String mAccessToken = null;

        private GraphHelper() {
            mClient = GraphServiceClient.builder()
                    .authenticationProvider(this).buildClient();
        }

        public static synchronized GraphHelper getInstance() {
            if (INSTANCE == null) {
                INSTANCE = new GraphHelper();
            }

            return INSTANCE;
        }

        // Part of the Graph IAuthenticationProvider interface
        // This method is called before sending the HTTP request
        @Override
        public void authenticateRequest(IHttpRequest request) {
            // Add the access token in the Authorization header
            request.addHeader("Authorization", "Bearer " + mAccessToken);
        }

        public void getUser(String accessToken, ICallback<User> callback) {
            mAccessToken = accessToken;

            // GET /me (logged in user)
            mClient.me().buildRequest()
                    .select("displayName,mail,mailboxSettings,userPrincipalName")
                    .get(callback);
        }
    }
    ```

    > [!NOTE]
    > Ten en cuenta lo que hace este código.
    >
    > - Implementa la interfaz para `IAuthenticationProvider` insertar el token de acceso en el encabezado en las solicitudes HTTP `Authorization` salientes.
    > - Expone una función para obtener la información del usuario que ha iniciado sesión desde `getUser` el punto de conexión de `/me` Graph.

1. Agregue las siguientes `import` instrucciones a la parte superior del archivo **MainActivity.**

    ```java
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.User;
    ```

1. Agregue la siguiente función a la `MainActivity` clase para generar una para la llamada de `ICallback` Graph.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="GetUserCallbackSnippet":::

1. Quite las siguientes líneas que establecen el nombre de usuario y el correo electrónico:

    ```java
    // For testing
    mUserName = "Megan Bowen";
    mUserEmail = "meganb@contoso.com";
    ```

1. Reemplace el `onSuccess` reemplazo por `AuthenticationCallback` lo siguiente.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnSuccessSnippet":::

1. Guarde los cambios y ejecute la aplicación. Después de iniciar sesión, la interfaz de usuario se actualiza con el nombre para mostrar y la dirección de correo electrónico del usuario.
