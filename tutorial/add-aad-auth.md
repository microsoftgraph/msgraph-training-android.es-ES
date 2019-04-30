<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="2f963-101">En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD.</span><span class="sxs-lookup"><span data-stu-id="2f963-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="2f963-102">Esto es necesario para obtener el token de acceso de OAuth necesario para llamar a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="2f963-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="2f963-103">En este paso, integrará la [biblioteca de autenticación de Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f963-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

<span data-ttu-id="2f963-104">Haga clic con el botón derecho en la carpeta **App/res/Values** y elija **nuevo**, **archivo de recurso de valores**.</span><span class="sxs-lookup"><span data-stu-id="2f963-104">Right-click the **app/res/values** folder and choose **New**, then **Values resource file**.</span></span> <span data-ttu-id="2f963-105">Asigne un nombre `oauth_strings` al archivo y elija **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="2f963-105">Name the file `oauth_strings` and choose **OK**.</span></span> <span data-ttu-id="2f963-106">Agregue los siguientes valores al `resources` elemento.</span><span class="sxs-lookup"><span data-stu-id="2f963-106">Add the following values to the `resources` element.</span></span>

```xml
<string name="oauth_app_id">YOUR_APP_ID_HERE</string>
<string name="oauth_redirect_uri">msalYOUR_APP_ID_HERE</string>
<string-array name="oauth_scopes">
    <item>User.Read</item>
    <item>Calendars.Read</item>
</string-array>
```

> [!IMPORTANT]
> <span data-ttu-id="2f963-107">Si usa un control de código fuente como GIT, ahora sería un buen momento para excluir el `oauth_strings.xml` archivo del control de código fuente para evitar la pérdida inadvertida del identificador de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f963-107">If you're using source control such as git, now would be a good time to exclude the `oauth_strings.xml` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="2f963-108">Implementar el inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="2f963-108">Implement sign-in</span></span>

<span data-ttu-id="2f963-109">ExPanda la carpeta **App/Manifests** y Abra **AndroidManifest. XML**.</span><span class="sxs-lookup"><span data-stu-id="2f963-109">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="2f963-110">Agregue los siguientes elementos por encima `application` del elemento.</span><span class="sxs-lookup"><span data-stu-id="2f963-110">Add the following elements above the `application` element.</span></span>

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

<span data-ttu-id="2f963-111">Estos permisos son necesarios para que la biblioteca de MSAL autentique al usuario.</span><span class="sxs-lookup"><span data-stu-id="2f963-111">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

<span data-ttu-id="2f963-112">Ahora, agregue el siguiente elemento dentro `application` del elemento.</span><span class="sxs-lookup"><span data-stu-id="2f963-112">Now add the following element inside the `application` element.</span></span>

```xml
<activity android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />

        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <data
            android:host="auth"
            android:scheme="@string/oauth_redirect_uri" />
    </intent-filter>
</activity>
```

<span data-ttu-id="2f963-113">Esto permite que MSAL use un explorador para autenticar al usuario y registra el URI de redireccionamiento como controlado por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f963-113">This allows MSAL to use a browser to authenticate the user, and registers your redirect URI as being handled by the app.</span></span>

### <a name="implement-an-authentication-helper"></a><span data-ttu-id="2f963-114">Implementar una aplicación auxiliar de autenticación</span><span class="sxs-lookup"><span data-stu-id="2f963-114">Implement an authentication helper</span></span>

<span data-ttu-id="2f963-115">Haga clic con el botón derecho en la carpeta **app/java/com. example. graphtutorial** y seleccione **nuevo**y, a continuación, **clase Java**.</span><span class="sxs-lookup"><span data-stu-id="2f963-115">Right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="2f963-116">Asigne un nombre `AuthenticationHelper` a la clase y elija **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="2f963-116">Name the class `AuthenticationHelper` and choose **OK**.</span></span> <span data-ttu-id="2f963-117">Abra el nuevo archivo y reemplace el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="2f963-117">Open the new file and replace its contents with the following.</span></span>

```java
package com.example.graphtutorial;

import android.app.Activity;
import android.content.Context;
import android.content.Intent;

import com.microsoft.identity.client.AuthenticationCallback;
import com.microsoft.identity.client.IAccount;
import com.microsoft.identity.client.PublicClientApplication;

// Singleton class - the app only needs a single instance
// of PublicClientApplication
public class AuthenticationHelper {
    private static AuthenticationHelper INSTANCE = null;
    private PublicClientApplication mPCA = null;
    private String[] mScopes;

    private AuthenticationHelper(Context ctx) {
        String appId = ctx.getResources().getString(R.string.oauth_app_id);
        mScopes = ctx.getResources().getStringArray(R.array.oauth_scopes);
        mPCA = new PublicClientApplication(ctx, appId);
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

    public boolean hasAccount() {
        return !mPCA.getAccounts().isEmpty();
    }

    public void handleRedirect(int requestCode, int resultCode, Intent data) {
        mPCA.handleInteractiveRequestRedirect(requestCode, resultCode, data);
    }

    public void acquireTokenInteractively(Activity activity, AuthenticationCallback callback) {
        mPCA.acquireToken(activity, mScopes, callback);
    }

    public void acquireTokenSilently(AuthenticationCallback callback) {
        mPCA.acquireTokenSilentAsync(mScopes, mPCA.getAccounts().get(0), callback);
    }

    public void signOut() {
        for (IAccount account : mPCA.getAccounts()) {
            mPCA.removeAccount(account);
        }
    }
}
```

<span data-ttu-id="2f963-118">Ahora actualice `MainActivity` para usar esta nueva clase.</span><span class="sxs-lookup"><span data-stu-id="2f963-118">Now update `MainActivity` to use this new class.</span></span> <span data-ttu-id="2f963-119">Abra **MainActivity** y agregue las siguientes `import` instrucciones.</span><span class="sxs-lookup"><span data-stu-id="2f963-119">Open **MainActivity** and add the following `import` statements.</span></span>

```java
import android.content.Intent;
import android.support.annotation.Nullable;
import android.util.Log;

import com.microsoft.identity.client.AuthenticationCallback;
import com.microsoft.identity.client.AuthenticationResult;
import com.microsoft.identity.client.exception.MsalClientException;
import com.microsoft.identity.client.exception.MsalException;
import com.microsoft.identity.client.exception.MsalServiceException;
import com.microsoft.identity.client.exception.MsalUiRequiredException;
```

<span data-ttu-id="2f963-120">A continuación, agregue la siguiente propiedad de miembro `MainActivity` a la clase.</span><span class="sxs-lookup"><span data-stu-id="2f963-120">Next, add the following member property to the `MainActivity` class.</span></span>

```java
private AuthenticationHelper mAuthHelper = null;
```

<span data-ttu-id="2f963-121">Actualización `onCreate` que se `mAuthHelper`va a establecer.</span><span class="sxs-lookup"><span data-stu-id="2f963-121">Update `onCreate` to set `mAuthHelper`.</span></span> <span data-ttu-id="2f963-122">Agregue lo siguiente al final de la `onCreate` función.</span><span class="sxs-lookup"><span data-stu-id="2f963-122">Add the following to the end of the `onCreate` function.</span></span>

```java
// Get the authentication helper
mAuthHelper = AuthenticationHelper.getInstance(getApplicationContext());
```

<span data-ttu-id="2f963-123">Agregue un reemplazo para `onActivityResult` controlar las respuestas de autenticación.</span><span class="sxs-lookup"><span data-stu-id="2f963-123">Add an override for `onActivityResult` to handle authentication responses.</span></span>

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    mAuthHelper.handleRedirect(requestCode, resultCode, data);
}
```

<span data-ttu-id="2f963-124">Ahora, agregue las siguientes funciones a la `MainActivity` clase.</span><span class="sxs-lookup"><span data-stu-id="2f963-124">Now, add the following functions to the `MainActivity` class.</span></span>

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
        public void onSuccess(AuthenticationResult authenticationResult) {
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
                // Exception inside MSAL, more info inside MsalError.java
                Log.e("AUTH", "Client error authenticating", exception);
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

<span data-ttu-id="2f963-125">Por último, reemplace las `signIn` funciones `signOut` y existentes por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="2f963-125">Finally, replace the existing `signIn` and `signOut` functions with the following.</span></span>

```java
private void signIn() {
    showProgressBar();
    if (mAuthHelper.hasAccount()) {
        doSilentSignIn();
    } else {
        doInteractiveSignIn();
    }
}

private void signOut() {
    mAuthHelper.signOut();

    setSignedInState(false);
    openHomeFragment(mUserName);
}
```

<span data-ttu-id="2f963-126">Observe que el `signIn` método comprueba primero si hay una cuenta de usuario que ya está en la memoria caché de MSAL.</span><span class="sxs-lookup"><span data-stu-id="2f963-126">Notice that the `signIn` method first checks if there is a user account already in the MSAL cache.</span></span> <span data-ttu-id="2f963-127">Si es así, intenta actualizar sus tokens silenciosamente, evitando tener que preguntar al usuario cada vez que inician la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f963-127">If there is, it attempts to refresh its tokens silently, avoiding having to prompt the user every time they launch the app.</span></span>

<span data-ttu-id="2f963-128">Guarde los cambios y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2f963-128">Save your changes and run the app.</span></span> <span data-ttu-id="2f963-129">Al puntear en el elemento **de menú iniciar sesión** , se abre un explorador en la página de inicio de sesión de Azure ad.</span><span class="sxs-lookup"><span data-stu-id="2f963-129">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="2f963-130">Inicie sesión con su cuenta.</span><span class="sxs-lookup"><span data-stu-id="2f963-130">Sign in with your account.</span></span> <span data-ttu-id="2f963-131">Una vez que la aplicación se reanude, debería ver un token de acceso impreso en el registro de depuración en Android Studio.</span><span class="sxs-lookup"><span data-stu-id="2f963-131">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Captura de pantalla de la ventana de logcat en Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="2f963-133">Obtener detalles del usuario</span><span class="sxs-lookup"><span data-stu-id="2f963-133">Get user details</span></span>

<span data-ttu-id="2f963-134">Empiece por crear una clase auxiliar que contenga todas las llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="2f963-134">Start by creating a helper class to hold all of the calls to Microsoft Graph.</span></span> <span data-ttu-id="2f963-135">Haga clic con el botón derecho en la carpeta **app/java/com. example. graphtutorial** y seleccione **nuevo**y, a continuación, **clase Java**.</span><span class="sxs-lookup"><span data-stu-id="2f963-135">Right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="2f963-136">Asigne un nombre `GraphHelper` a la clase y elija **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="2f963-136">Name the class `GraphHelper` and choose **OK**.</span></span> <span data-ttu-id="2f963-137">Abra el nuevo archivo y reemplace el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="2f963-137">Open the new file and replace its contents with the following.</span></span>

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

<span data-ttu-id="2f963-138">Tenga en cuenta lo que hace el código.</span><span class="sxs-lookup"><span data-stu-id="2f963-138">Note what this code does.</span></span>

- <span data-ttu-id="2f963-139">Implementa la `IAuthenticationProvider` interfaz para insertar el token de acceso en el `Authorization` encabezado en las solicitudes HTTP salientes.</span><span class="sxs-lookup"><span data-stu-id="2f963-139">It implements the `IAuthenticationProvider` interface to insert the access token in the `Authorization` header on outgoing HTTP requests.</span></span>
- <span data-ttu-id="2f963-140">Expone una `getUser` función para obtener la información del usuario que ha iniciado sesión desde el `/me` punto de conexión del gráfico.</span><span class="sxs-lookup"><span data-stu-id="2f963-140">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>

<span data-ttu-id="2f963-141">Ahora, actualice `MainActivity` la clase para usar esta nueva clase para obtener el usuario que ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="2f963-141">Now update the `MainActivity` class to use this new class to get the logged-in user.</span></span> <span data-ttu-id="2f963-142">Agregue las siguientes `import` instrucciones en la parte superior del archivo **MainActivity** .</span><span class="sxs-lookup"><span data-stu-id="2f963-142">Add the following `import` statements to the top of the **MainActivity** file.</span></span>

```java
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.models.extensions.IGraphServiceClient;
import com.microsoft.graph.models.extensions.User;
```

<span data-ttu-id="2f963-143">A continuación, agregue la siguiente función a `MainActivity` la clase para generar `ICallback` un para la llamada de gráfico.</span><span class="sxs-lookup"><span data-stu-id="2f963-143">Next, add the following function to the `MainActivity` class to generate an `ICallback` for the Graph call.</span></span>

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

<span data-ttu-id="2f963-144">Quite las líneas siguientes que establecen el nombre de usuario y el correo electrónico:</span><span class="sxs-lookup"><span data-stu-id="2f963-144">Remove the following lines that set the user name and email:</span></span>

```java
// For testing
mUserName = "Megan Bowen";
mUserEmail = "meganb@contoso.com";
```

<span data-ttu-id="2f963-145">Por último, reemplace `onSuccess` la invalidación `AuthenticationCallback` en el por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="2f963-145">Finally, replace the `onSuccess` override in the `AuthenticationCallback` with the following.</span></span>

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

<span data-ttu-id="2f963-146">Si guarda los cambios y ejecuta la aplicación ahora, después de iniciar sesión, la interfaz de usuario se actualizará con el nombre para mostrar y la dirección de correo electrónico del usuario.</span><span class="sxs-lookup"><span data-stu-id="2f963-146">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
