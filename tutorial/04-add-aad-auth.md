<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="96e3b-101">En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD.</span><span class="sxs-lookup"><span data-stu-id="96e3b-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="96e3b-102">Esto es necesario para obtener el token de acceso de OAuth necesario para llamar a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="96e3b-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="96e3b-103">Para ello, integrará la [biblioteca de autenticación de Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="96e3b-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

1. <span data-ttu-id="96e3b-104">Haga clic con el botón derecho en la carpeta **res** y seleccione **nuevo**y, a continuación, **Directorio de recursos de Android**.</span><span class="sxs-lookup"><span data-stu-id="96e3b-104">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="96e3b-105">Cambie el **tipo** de recurso `raw` a y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="96e3b-105">Change the **Resource type** to `raw` and select **OK**.</span></span>

1. <span data-ttu-id="96e3b-106">Haga clic con el botón secundario en la nueva carpeta **raw** , seleccione **nuevo**y, a continuación, **archivo**.</span><span class="sxs-lookup"><span data-stu-id="96e3b-106">Right-click the new **raw** folder and select **New**, then **File**.</span></span>

1. <span data-ttu-id="96e3b-107">Asigne un nombre `msal_config.json` al archivo y seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="96e3b-107">Name the file `msal_config.json` and select **OK**.</span></span>

1. <span data-ttu-id="96e3b-108">Agregue lo siguiente al archivo **msal_config. JSON** .</span><span class="sxs-lookup"><span data-stu-id="96e3b-108">Add the following to the **msal_config.json** file.</span></span>

    ```json
    {
      "client_id" : "YOUR_APP_ID_HERE",
      "redirect_uri" : "msauth://YOUR_PACKAGE_NAME_HERE/callback",
      "broker_redirect_uri_registered": false,
      "account_mode": "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount"
          },
          "default": true
        }
      ]
    }
    ```

    <span data-ttu-id="96e3b-109">Reemplace `YOUR_APP_ID_HERE` por el identificador de la aplicación del registro de la aplicación `YOUR_PACKAGE_NAME_HERE` y reemplace por el nombre del paquete del proyecto.</span><span class="sxs-lookup"><span data-stu-id="96e3b-109">Replace `YOUR_APP_ID_HERE` with the app ID from your app registration, and replace `YOUR_PACKAGE_NAME_HERE` with your project's package name.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="96e3b-110">Si usa un control de código fuente como GIT, ahora sería un buen momento para excluir el `msal_config.json` archivo del control de código fuente para evitar la pérdida inadvertida del identificador de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="96e3b-110">If you're using source control such as git, now would be a good time to exclude the `msal_config.json` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="96e3b-111">Implementar el inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="96e3b-111">Implement sign-in</span></span>

<span data-ttu-id="96e3b-112">En esta sección, actualizará el manifiesto para permitir que MSAL use un explorador para autenticar al usuario, registrar el URI de redireccionamiento como controlado por la aplicación, crear una clase auxiliar de autenticación y actualizar la aplicación para iniciar y cerrar sesión.</span><span class="sxs-lookup"><span data-stu-id="96e3b-112">In this section you will update the manifest to allow MSAL to use a browser to authenticate the user, register your redirect URI as being handled by the app, create an authentication helper class, and update the app to sign in and sign out.</span></span>

1. <span data-ttu-id="96e3b-113">Expanda la carpeta **App/Manifests** y Abra **AndroidManifest. XML**.</span><span class="sxs-lookup"><span data-stu-id="96e3b-113">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="96e3b-114">Agregue los siguientes elementos por encima `application` del elemento.</span><span class="sxs-lookup"><span data-stu-id="96e3b-114">Add the following elements above the `application` element.</span></span>

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > <span data-ttu-id="96e3b-115">Estos permisos son necesarios para que la biblioteca de MSAL autentique al usuario.</span><span class="sxs-lookup"><span data-stu-id="96e3b-115">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

1. <span data-ttu-id="96e3b-116">Agregue el siguiente elemento dentro del `application` elemento, reemplazando `YOUR_PACKAGE_NAME_HERE` la cadena con el nombre del paquete.</span><span class="sxs-lookup"><span data-stu-id="96e3b-116">Add the following element inside the `application` element, replacing the `YOUR_PACKAGE_NAME_HERE` string with your package name.</span></span>

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

1. <span data-ttu-id="96e3b-117">Haga clic con el botón secundario en la carpeta **app/java/com. example. graphtutorial** y seleccione **nuevo**y, a continuación, **clase Java**.</span><span class="sxs-lookup"><span data-stu-id="96e3b-117">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="96e3b-118">Asigne un nombre `AuthenticationHelper` a la clase y seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="96e3b-118">Name the class `AuthenticationHelper` and select **OK**.</span></span>

1. <span data-ttu-id="96e3b-119">Abra el nuevo archivo y reemplace el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="96e3b-119">Open the new file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.app.Activity;
    import android.content.Context;
    import android.util.Log;
    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IPublicClientApplication;
    import com.microsoft.identity.client.ISingleAccountPublicClientApplication;
    import com.microsoft.identity.client.PublicClientApplication;
    import com.microsoft.identity.client.exception.MsalException;

    // Singleton class - the app only needs a single instance
    // of PublicClientApplication
    public class AuthenticationHelper {
        private static AuthenticationHelper INSTANCE = null;
        private ISingleAccountPublicClientApplication mPCA = null;
        private String[] mScopes = { "User.Read", "Calendars.Read" };

        private AuthenticationHelper(Context ctx) {
            PublicClientApplication.createSingleAccountPublicClientApplication(ctx, R.raw.msal_config,
                new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                    @Override
                    public void onCreated(ISingleAccountPublicClientApplication application) {
                        mPCA = application;
                    }

                    @Override
                    public void onError(MsalException exception) {
                        Log.e("AUTHHELPER", "Error creating MSAL application", exception);
                    }
                });
        }

        public static synchronized AuthenticationHelper getInstance(Context ctx) {
            if (INSTANCE == null) {
                INSTANCE = new AuthenticationHelper(ctx);
            }

            return INSTANCE;
        }

        // Version called from fragments. Does not create an
        // instance if one doesn't exist
        public static synchronized AuthenticationHelper getInstance() {
            if (INSTANCE == null) {
                throw new IllegalStateException(
                    "AuthenticationHelper has not been initialized from MainActivity");
            }

            return INSTANCE;
        }

        public void acquireTokenInteractively(Activity activity, AuthenticationCallback callback) {
            mPCA.signIn(activity, null, mScopes, callback);
        }

        public void acquireTokenSilently(AuthenticationCallback callback) {
            // Get the authority from MSAL config
            String authority = mPCA.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();
            mPCA.acquireTokenSilentAsync(mScopes, authority, callback);
        }

        public void signOut() {
            mPCA.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                @Override
                public void onSignOut() {
                    Log.d("AUTHHELPER", "Signed out");
                }

                @Override
                public void onError(@NonNull MsalException exception) {
                    Log.d("AUTHHELPER", "MSAL error signing out", exception);
                }
            });
        }
    }
    ```

1. <span data-ttu-id="96e3b-120">Abra **MainActivity** y agregue las siguientes `import` instrucciones.</span><span class="sxs-lookup"><span data-stu-id="96e3b-120">Open **MainActivity** and add the following `import` statements.</span></span>

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. <span data-ttu-id="96e3b-121">Agregue la siguiente propiedad de miembro a `MainActivity` la clase.</span><span class="sxs-lookup"><span data-stu-id="96e3b-121">Add the following member property to the `MainActivity` class.</span></span>

    ```java
    private AuthenticationHelper mAuthHelper = null;
    ```

1. <span data-ttu-id="96e3b-122">Agregue lo siguiente al final de la `onCreate` función.</span><span class="sxs-lookup"><span data-stu-id="96e3b-122">Add the following to the end of the `onCreate` function.</span></span>

    ```java
    // Get the authentication helper
    mAuthHelper = AuthenticationHelper.getInstance(getApplicationContext());
    ```

1. <span data-ttu-id="96e3b-123">Agregue las siguientes funciones a la `MainActivity` clase.</span><span class="sxs-lookup"><span data-stu-id="96e3b-123">Add the following functions to the `MainActivity` class.</span></span>

    ```java
    // Silently sign in - used if there is already a
    // user account in the MSAL cache
    private void doSilentSignIn() {
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
                    doInteractiveSignIn();

                } else if (exception instanceof MsalClientException) {
                    if (exception.getErrorCode() == "no_current_account") {
                        Log.d("AUTH", "No current account, interactive login required");
                        doInteractiveSignIn();
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

1. <span data-ttu-id="96e3b-124">Reemplace las funciones `signIn` y `signOut` existentes por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="96e3b-124">Replace the existing `signIn` and `signOut` functions with the following.</span></span>

    ```java
    private void signIn() {
        showProgressBar();
        // Attempt silent sign in first
        // if this fails, the callback will handle doing
        // interactive sign in
        doSilentSignIn();
    }

    private void signOut() {
        mAuthHelper.signOut();

        setSignedInState(false);
        openHomeFragment(mUserName);
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="96e3b-125">Observe que el `signIn` método comprueba primero si hay una cuenta de usuario que ya está en la memoria caché de MSAL.</span><span class="sxs-lookup"><span data-stu-id="96e3b-125">Notice that the `signIn` method first checks if there is a user account already in the MSAL cache.</span></span> <span data-ttu-id="96e3b-126">Si es así, intenta actualizar sus tokens silenciosamente, evitando tener que preguntar al usuario cada vez que inician la aplicación.</span><span class="sxs-lookup"><span data-stu-id="96e3b-126">If there is, it attempts to refresh its tokens silently, avoiding having to prompt the user every time they launch the app.</span></span>

1. <span data-ttu-id="96e3b-127">Guarde los cambios y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="96e3b-127">Save your changes and run the app.</span></span>

1. <span data-ttu-id="96e3b-128">Al puntear en el elemento **de menú iniciar sesión** , se abre un explorador en la página de inicio de sesión de Azure ad.</span><span class="sxs-lookup"><span data-stu-id="96e3b-128">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="96e3b-129">Inicie sesión con su cuenta.</span><span class="sxs-lookup"><span data-stu-id="96e3b-129">Sign in with your account.</span></span>

<span data-ttu-id="96e3b-130">Una vez que la aplicación se reanude, debería ver un token de acceso impreso en el registro de depuración en Android Studio.</span><span class="sxs-lookup"><span data-stu-id="96e3b-130">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Captura de pantalla de la ventana de logcat en Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="96e3b-132">Obtener detalles del usuario</span><span class="sxs-lookup"><span data-stu-id="96e3b-132">Get user details</span></span>

<span data-ttu-id="96e3b-133">En esta sección, creará una clase auxiliar que contendrá todas las llamadas a Microsoft Graph y actualizará la `MainActivity` clase para que use esta nueva clase para obtener el usuario que ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="96e3b-133">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `MainActivity` class to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="96e3b-134">Haga clic con el botón secundario en la carpeta **app/java/com. example. graphtutorial** y seleccione **nuevo**y, a continuación, **clase Java**.</span><span class="sxs-lookup"><span data-stu-id="96e3b-134">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="96e3b-135">Asigne un nombre `GraphHelper` a la clase y seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="96e3b-135">Name the class `GraphHelper` and select **OK**.</span></span>

1. <span data-ttu-id="96e3b-136">Abra el nuevo archivo y reemplace el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="96e3b-136">Open the new file and replace its contents with the following.</span></span>

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
            mClient.me().buildRequest().get(callback);
        }
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="96e3b-137">Considere lo que hace este código.</span><span class="sxs-lookup"><span data-stu-id="96e3b-137">Consider what this code does.</span></span>
    >
    > - <span data-ttu-id="96e3b-138">Implementa la `IAuthenticationProvider` interfaz para insertar el token de acceso en el `Authorization` encabezado en las solicitudes HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="96e3b-138">It implements the `IAuthenticationProvider` interface to insert the access token in the `Authorization` header on outgoing HTTP requests.</span></span>
    > - <span data-ttu-id="96e3b-139">Expone una `getUser` función para obtener la información del usuario que ha iniciado sesión desde el `/me` punto de conexión del gráfico.</span><span class="sxs-lookup"><span data-stu-id="96e3b-139">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>

1. <span data-ttu-id="96e3b-140">Agregue las siguientes `import` instrucciones en la parte superior del archivo **MainActivity** .</span><span class="sxs-lookup"><span data-stu-id="96e3b-140">Add the following `import` statements to the top of the **MainActivity** file.</span></span>

    ```java
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.IGraphServiceClient;
    import com.microsoft.graph.models.extensions.User;
    ```

1. <span data-ttu-id="96e3b-141">Agregue la siguiente función a la `MainActivity` clase para generar un `ICallback` para la llamada de gráfico.</span><span class="sxs-lookup"><span data-stu-id="96e3b-141">Add the following function to the `MainActivity` class to generate an `ICallback` for the Graph call.</span></span>

    ```java
    private ICallback<User> getUserCallback() {
        return new ICallback<User>() {
            @Override
            public void success(User user) {
                Log.d("AUTH", "User: " + user.displayName);

                mUserName = user.displayName;
                mUserEmail = user.mail == null ? user.userPrincipalName : user.mail;

                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        hideProgressBar();

                        setSignedInState(true);
                        openHomeFragment(mUserName);
                    }
                });

            }

            @Override
            public void failure(ClientException ex) {
                Log.e("AUTH", "Error getting /me", ex);
                mUserName = "ERROR";
                mUserEmail = "ERROR";

                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        hideProgressBar();

                        setSignedInState(true);
                        openHomeFragment(mUserName);
                    }
                });
            }
        };
    }
    ```

1. <span data-ttu-id="96e3b-142">Quite las líneas siguientes que establecen el nombre de usuario y el correo electrónico:</span><span class="sxs-lookup"><span data-stu-id="96e3b-142">Remove the following lines that set the user name and email:</span></span>

    ```java
    // For testing
    mUserName = "Megan Bowen";
    mUserEmail = "meganb@contoso.com";
    ```

1. <span data-ttu-id="96e3b-143">Reemplace la `onSuccess` invalidación en `AuthenticationCallback` el por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="96e3b-143">Replace the `onSuccess` override in the `AuthenticationCallback` with the following.</span></span>

    ```java
    @Override
    public void onSuccess(AuthenticationResult authenticationResult) {
        // Log the token for debug purposes
        String accessToken = authenticationResult.getAccessToken();
        Log.d("AUTH", String.format("Access token: %s", accessToken));

        // Get Graph client and get user
        GraphHelper graphHelper = GraphHelper.getInstance();
        graphHelper.getUser(accessToken, getUserCallback());
    }
    ```

<span data-ttu-id="96e3b-144">Si guarda los cambios y ejecuta la aplicación ahora, después de iniciar sesión, la interfaz de usuario se actualizará con el nombre para mostrar y la dirección de correo electrónico del usuario.</span><span class="sxs-lookup"><span data-stu-id="96e3b-144">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
