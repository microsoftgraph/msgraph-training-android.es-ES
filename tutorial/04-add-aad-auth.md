<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="9ad04-101">En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD.</span><span class="sxs-lookup"><span data-stu-id="9ad04-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="9ad04-102">Esto es necesario para obtener el token de acceso OAuth necesario para llamar a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="9ad04-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="9ad04-103">Para ello, integrará la Biblioteca de autenticación de [Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9ad04-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

1. <span data-ttu-id="9ad04-104">Haga clic con el botón **secundario en la carpeta Res** y seleccione **Nuevo** y, a continuación, Directorio de recursos **de Android**.</span><span class="sxs-lookup"><span data-stu-id="9ad04-104">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="9ad04-105">Cambie el **tipo de recurso** a y seleccione `raw` **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="9ad04-105">Change the **Resource type** to `raw` and select **OK**.</span></span>

1. <span data-ttu-id="9ad04-106">Haga clic con el botón secundario en la nueva **carpeta sin** procesar y **seleccione Nuevo** y, a continuación, **Archivo**.</span><span class="sxs-lookup"><span data-stu-id="9ad04-106">Right-click the new **raw** folder and select **New**, then **File**.</span></span>

1. <span data-ttu-id="9ad04-107">Asigne un nombre al `msal_config.json` archivo y seleccione **Aceptar.**</span><span class="sxs-lookup"><span data-stu-id="9ad04-107">Name the file `msal_config.json` and select **OK**.</span></span>

1. <span data-ttu-id="9ad04-108">Agregue lo siguiente al archivo **msal_config.js** archivo.</span><span class="sxs-lookup"><span data-stu-id="9ad04-108">Add the following to the **msal_config.json** file.</span></span>

    :::code language="json" source="../demo/GraphTutorial/msal_config.json.example":::

1. <span data-ttu-id="9ad04-109">Reemplaza con el identificador de aplicación del registro `YOUR_APP_ID_HERE` de la aplicación y reemplaza por el nombre del paquete del `com.example.graphtutorial` proyecto.</span><span class="sxs-lookup"><span data-stu-id="9ad04-109">Replace `YOUR_APP_ID_HERE` with the app ID from your app registration, and replace `com.example.graphtutorial` with your project's package name.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="9ad04-110">Si usas el control de código fuente como Git, ahora sería un buen momento para excluir el archivo del control de código fuente para evitar la pérdida involuntaria del identificador `msal_config.json` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9ad04-110">If you're using source control such as git, now would be a good time to exclude the `msal_config.json` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="9ad04-111">Implementar el inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="9ad04-111">Implement sign-in</span></span>

<span data-ttu-id="9ad04-112">En esta sección actualizará el manifiesto para permitir que MSAL use un explorador para autenticar al usuario, registrar el URI de redireccionamiento como lo controla la aplicación, crear una clase auxiliar de autenticación y actualizar la aplicación para iniciar y cerrar sesión.</span><span class="sxs-lookup"><span data-stu-id="9ad04-112">In this section you will update the manifest to allow MSAL to use a browser to authenticate the user, register your redirect URI as being handled by the app, create an authentication helper class, and update the app to sign in and sign out.</span></span>

1. <span data-ttu-id="9ad04-113">Expanda la **carpeta app/manifests** y abra **AndroidManifest.xml**.</span><span class="sxs-lookup"><span data-stu-id="9ad04-113">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="9ad04-114">Agregue los siguientes elementos encima del `application` elemento.</span><span class="sxs-lookup"><span data-stu-id="9ad04-114">Add the following elements above the `application` element.</span></span>

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > <span data-ttu-id="9ad04-115">Estos permisos son necesarios para que la biblioteca de MSAL autentique al usuario.</span><span class="sxs-lookup"><span data-stu-id="9ad04-115">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

1. <span data-ttu-id="9ad04-116">Agrega el siguiente elemento dentro `application` del elemento, reemplazando la `YOUR_PACKAGE_NAME_HERE` cadena por el nombre del paquete.</span><span class="sxs-lookup"><span data-stu-id="9ad04-116">Add the following element inside the `application` element, replacing the `YOUR_PACKAGE_NAME_HERE` string with your package name.</span></span>

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

1. <span data-ttu-id="9ad04-117">Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**.</span><span class="sxs-lookup"><span data-stu-id="9ad04-117">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="9ad04-118">Cambiar el **tipo a** **interfaz**.</span><span class="sxs-lookup"><span data-stu-id="9ad04-118">Change the **Kind** to **Interface**.</span></span> <span data-ttu-id="9ad04-119">Asigne un nombre a la `IAuthenticationHelperCreatedListener` interfaz y seleccione **Aceptar.**</span><span class="sxs-lookup"><span data-stu-id="9ad04-119">Name the interface `IAuthenticationHelperCreatedListener` and select **OK**.</span></span>

1. <span data-ttu-id="9ad04-120">Abra el nuevo archivo y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="9ad04-120">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/IAuthenticationHelperCreatedListener.java" id="ListenerSnippet":::

1. <span data-ttu-id="9ad04-121">Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**.</span><span class="sxs-lookup"><span data-stu-id="9ad04-121">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="9ad04-122">Asigne un nombre a `AuthenticationHelper` la clase y seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="9ad04-122">Name the class `AuthenticationHelper` and select **OK**.</span></span>

1. <span data-ttu-id="9ad04-123">Abra el nuevo archivo y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="9ad04-123">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/AuthenticationHelper.java" id="AuthHelperSnippet":::

1. <span data-ttu-id="9ad04-124">Abra **MainActivity** y agregue las `import` siguientes instrucciones.</span><span class="sxs-lookup"><span data-stu-id="9ad04-124">Open **MainActivity** and add the following `import` statements.</span></span>

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. <span data-ttu-id="9ad04-125">Agregue las siguientes propiedades de miembro a la `MainActivity` clase.</span><span class="sxs-lookup"><span data-stu-id="9ad04-125">Add the following member properties to the `MainActivity` class.</span></span>

    ```java
    private AuthenticationHelper mAuthHelper = null;
    private boolean mAttemptInteractiveSignIn = false;
    ```

1. <span data-ttu-id="9ad04-126">Agregue el siguiente código al final de la función `onCreate`.</span><span class="sxs-lookup"><span data-stu-id="9ad04-126">Add the following code to the end of the `onCreate` function.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="InitialLoginSnippet":::

1. <span data-ttu-id="9ad04-127">Agregue las siguientes funciones a la `MainActivity` clase.</span><span class="sxs-lookup"><span data-stu-id="9ad04-127">Add the following functions to the `MainActivity` class.</span></span>

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

1. <span data-ttu-id="9ad04-128">Reemplace las funciones `signIn` y `signOut` existentes por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="9ad04-128">Replace the existing `signIn` and `signOut` functions with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="SignInAndOutSnippet":::

    > [!NOTE]
    > <span data-ttu-id="9ad04-129">Observe que `signIn` el método realiza un inicio de sesión silencioso (a través de `doSilentSignIn` ).</span><span class="sxs-lookup"><span data-stu-id="9ad04-129">Notice that the `signIn` method does a silent sign-in (via `doSilentSignIn`).</span></span> <span data-ttu-id="9ad04-130">La devolución de llamada de este método realizará un inicio de sesión interactivo si se produce un error en el silencioso.</span><span class="sxs-lookup"><span data-stu-id="9ad04-130">The callback for this method will do an interactive sign-in if the silent one fails.</span></span> <span data-ttu-id="9ad04-131">Esto evita tener que preguntar al usuario cada vez que inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9ad04-131">This avoids having to prompt the user every time they launch the app.</span></span>

1. <span data-ttu-id="9ad04-132">Guarde los cambios y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9ad04-132">Save your changes and run the app.</span></span>

1. <span data-ttu-id="9ad04-133">Al pulsar el elemento de menú **Iniciar** sesión, se abre un explorador en la página de inicio de sesión de Azure AD.</span><span class="sxs-lookup"><span data-stu-id="9ad04-133">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="9ad04-134">Inicie sesión con su cuenta.</span><span class="sxs-lookup"><span data-stu-id="9ad04-134">Sign in with your account.</span></span>

<span data-ttu-id="9ad04-135">Una vez que la aplicación se reanude, debería ver un token de acceso impreso en el registro de depuración de Android Studio.</span><span class="sxs-lookup"><span data-stu-id="9ad04-135">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Captura de pantalla de la ventana Logcat en Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="9ad04-137">Obtener detalles del usuario</span><span class="sxs-lookup"><span data-stu-id="9ad04-137">Get user details</span></span>

<span data-ttu-id="9ad04-138">En esta sección creará una clase auxiliar para contener todas las llamadas a Microsoft Graph y actualizará la clase para que use esta nueva clase para obtener el usuario que ha `MainActivity` iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="9ad04-138">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `MainActivity` class to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="9ad04-139">Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**.</span><span class="sxs-lookup"><span data-stu-id="9ad04-139">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="9ad04-140">Asigne un nombre a `GraphHelper` la clase y seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="9ad04-140">Name the class `GraphHelper` and select **OK**.</span></span>

1. <span data-ttu-id="9ad04-141">Abra el nuevo archivo y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="9ad04-141">Open the new file and replace its contents with the following.</span></span>

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
    > <span data-ttu-id="9ad04-142">Ten en cuenta lo que hace este código.</span><span class="sxs-lookup"><span data-stu-id="9ad04-142">Consider what this code does.</span></span>
    >
    > - <span data-ttu-id="9ad04-143">Implementa la interfaz para `IAuthenticationProvider` insertar el token de acceso en el encabezado en las solicitudes HTTP `Authorization` salientes.</span><span class="sxs-lookup"><span data-stu-id="9ad04-143">It implements the `IAuthenticationProvider` interface to insert the access token in the `Authorization` header on outgoing HTTP requests.</span></span>
    > - <span data-ttu-id="9ad04-144">Expone una función para obtener la información del usuario que ha iniciado sesión desde `getUser` el punto de conexión de `/me` Graph.</span><span class="sxs-lookup"><span data-stu-id="9ad04-144">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>

1. <span data-ttu-id="9ad04-145">Agregue las siguientes `import` instrucciones a la parte superior del archivo **MainActivity.**</span><span class="sxs-lookup"><span data-stu-id="9ad04-145">Add the following `import` statements to the top of the **MainActivity** file.</span></span>

    ```java
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.User;
    ```

1. <span data-ttu-id="9ad04-146">Agregue la siguiente función a la `MainActivity` clase para generar una para la llamada de `ICallback` Graph.</span><span class="sxs-lookup"><span data-stu-id="9ad04-146">Add the following function to the `MainActivity` class to generate an `ICallback` for the Graph call.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="GetUserCallbackSnippet":::

1. <span data-ttu-id="9ad04-147">Quite las siguientes líneas que establecen el nombre de usuario y el correo electrónico:</span><span class="sxs-lookup"><span data-stu-id="9ad04-147">Remove the following lines that set the user name and email:</span></span>

    ```java
    // For testing
    mUserName = "Megan Bowen";
    mUserEmail = "meganb@contoso.com";
    ```

1. <span data-ttu-id="9ad04-148">Reemplace el `onSuccess` reemplazo por `AuthenticationCallback` lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="9ad04-148">Replace the `onSuccess` override in the `AuthenticationCallback` with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnSuccessSnippet":::

1. <span data-ttu-id="9ad04-149">Guarde los cambios y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="9ad04-149">Save your changes and run the app.</span></span> <span data-ttu-id="9ad04-150">Después de iniciar sesión, la interfaz de usuario se actualiza con el nombre para mostrar y la dirección de correo electrónico del usuario.</span><span class="sxs-lookup"><span data-stu-id="9ad04-150">After sign-in the UI is updated with the user's display name and email address.</span></span>
