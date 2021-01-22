<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="375c0-101">Comience por crear un nuevo proyecto de Android Studio.</span><span class="sxs-lookup"><span data-stu-id="375c0-101">Begin by creating a new Android Studio project.</span></span>

1. <span data-ttu-id="375c0-102">Abre Android Studio y selecciona **Iniciar un nuevo proyecto de Android Studio** en la pantalla de bienvenida.</span><span class="sxs-lookup"><span data-stu-id="375c0-102">Open Android Studio, and select **Start a new Android Studio project** on the welcome screen.</span></span>

1. <span data-ttu-id="375c0-103">En el **cuadro de diálogo Crear nuevo** proyecto, seleccione Actividad **vacía** y, a continuación, **seleccione Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="375c0-103">In the **Create New Project** dialog, select **Empty Activity**, then select **Next**.</span></span>

    ![Captura de pantalla del cuadro de diálogo Crear nuevo proyecto en Android Studio](./images/choose-project.png)

1. <span data-ttu-id="375c0-105">En el **cuadro de** diálogo  Configurar el proyecto, establezca el nombre en , asegúrese de que el campo Idioma está establecido en y asegúrese de que el nivel mínimo de `Graph Tutorial` API está establecido en  `Java`  `API 29: Android 10.0 (Q)` .</span><span class="sxs-lookup"><span data-stu-id="375c0-105">In the **Configure your project** dialog, set the **Name** to `Graph Tutorial`, ensure the **Language** field is set to `Java`, and ensure the **Minimum API level** is set to `API 29: Android 10.0 (Q)`.</span></span> <span data-ttu-id="375c0-106">Modifique el **nombre del paquete y** la ubicación de guardar **según** sea necesario.</span><span class="sxs-lookup"><span data-stu-id="375c0-106">Modify the **Package name** and **Save location** as needed.</span></span> <span data-ttu-id="375c0-107">Seleccione **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="375c0-107">Select **Finish**.</span></span>

    ![Captura de pantalla del cuadro de diálogo Configurar el proyecto](./images/configure-project.png)

> [!IMPORTANT]
> <span data-ttu-id="375c0-109">El código y las instrucciones de este tutorial usan el nombre del **paquete com.example.graphtutorial**.</span><span class="sxs-lookup"><span data-stu-id="375c0-109">The code and instructions in this tutorial use the package name **com.example.graphtutorial**.</span></span> <span data-ttu-id="375c0-110">Si usas un nombre de paquete diferente al crear el proyecto, asegúrate de usar el nombre del paquete dondequiera que veas este valor.</span><span class="sxs-lookup"><span data-stu-id="375c0-110">If you use a different package name when creating the project, be sure to use your package name wherever you see this value.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="375c0-111">Instalar dependencias</span><span class="sxs-lookup"><span data-stu-id="375c0-111">Install dependencies</span></span>

<span data-ttu-id="375c0-112">Antes de pasar, instale algunas dependencias adicionales que usará más adelante.</span><span class="sxs-lookup"><span data-stu-id="375c0-112">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="375c0-113">`com.google.android.material:material` para que la [vista de navegación](https://material.io/develop/android/components/navigation-view/) esté disponible para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="375c0-113">`com.google.android.material:material` to make the [navigation view](https://material.io/develop/android/components/navigation-view/) available to the app.</span></span>
- <span data-ttu-id="375c0-114">[Biblioteca de autenticación de Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) para administrar la autenticación de Azure AD y la administración de tokens.</span><span class="sxs-lookup"><span data-stu-id="375c0-114">[Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="375c0-115">[SDK de Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) para realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="375c0-115">[Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) for making calls to the Microsoft Graph.</span></span>

1. <span data-ttu-id="375c0-116">Expanda **Scripts de Gradle** y, a **continuación, abra build.gradle (módulo: Graph_Tutorial.app).**</span><span class="sxs-lookup"><span data-stu-id="375c0-116">Expand **Gradle Scripts**, then open **build.gradle (Module: Graph_Tutorial.app)**.</span></span>

1. <span data-ttu-id="375c0-117">Agregue las siguientes líneas dentro del `dependencies` valor.</span><span class="sxs-lookup"><span data-stu-id="375c0-117">Add the following lines inside the `dependencies` value.</span></span>

    :::code language="gradle" source="../demo/GraphTutorial/app/build.gradle" id="DependenciesSnippet":::

1. <span data-ttu-id="375c0-118">Agregue un `packagingOptions` valor dentro del valor en `android` **build.gradle (Módulo: Graph_Tutorial.app).**</span><span class="sxs-lookup"><span data-stu-id="375c0-118">Add a `packagingOptions` value inside the `android` value in **build.gradle (Module: Graph_Tutorial.app)**.</span></span>

    ```Gradle
    packagingOptions {
        pickFirst 'META-INF/jersey-module-version'
    }
    ```

1. <span data-ttu-id="375c0-119">Agregue el repositorio de Azure Maven para la biblioteca MicrosoftDeviceSDK, una dependencia de MSAL.</span><span class="sxs-lookup"><span data-stu-id="375c0-119">Add the Azure Maven repository for the MicrosoftDeviceSDK library, a dependency of MSAL.</span></span> <span data-ttu-id="375c0-120">Abra **build.gradle (Project: Graph_Tutorial)**.</span><span class="sxs-lookup"><span data-stu-id="375c0-120">Open **build.gradle (Project: Graph_Tutorial)**.</span></span> <span data-ttu-id="375c0-121">Agregue lo siguiente al `repositories` valor dentro del `allprojects` valor.</span><span class="sxs-lookup"><span data-stu-id="375c0-121">Add the following to the `repositories` value inside the `allprojects` value.</span></span>

    ```Gradle
    maven {
        url 'https://pkgs.dev.azure.com/MicrosoftDeviceSDK/DuoSDK-Public/_packaging/Duo-SDK-Feed/maven/v1'
    }
    ```

1. <span data-ttu-id="375c0-122">Guarde los cambios.</span><span class="sxs-lookup"><span data-stu-id="375c0-122">Save your changes.</span></span> <span data-ttu-id="375c0-123">En el **menú** Archivo, seleccione **Sincronizar proyecto con archivos gradle**.</span><span class="sxs-lookup"><span data-stu-id="375c0-123">On the **File** menu, select **Sync Project with Gradle Files**.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="375c0-124">Diseñar la aplicación</span><span class="sxs-lookup"><span data-stu-id="375c0-124">Design the app</span></span>

<span data-ttu-id="375c0-125">La aplicación usará un cajón de navegación para navegar entre distintas vistas.</span><span class="sxs-lookup"><span data-stu-id="375c0-125">The application will use a navigation drawer to navigate between different views.</span></span> <span data-ttu-id="375c0-126">En este paso actualizará la actividad para usar un diseño de cajón de navegación y agregará fragmentos para las vistas.</span><span class="sxs-lookup"><span data-stu-id="375c0-126">In this step you will update the activity to use a navigation drawer layout, and add fragments for the views.</span></span>

### <a name="create-a-navigation-drawer"></a><span data-ttu-id="375c0-127">Crear un cajón de navegación</span><span class="sxs-lookup"><span data-stu-id="375c0-127">Create a navigation drawer</span></span>

<span data-ttu-id="375c0-128">En esta sección creará iconos para el menú de navegación de la aplicación, creará un menú para la aplicación y actualizará el tema y el diseño de la aplicación para que sean compatibles con un cajón de navegación.</span><span class="sxs-lookup"><span data-stu-id="375c0-128">In this section you will create icons for the app's navigation menu, create a menu for the application, and update the application's theme and layout to be compatible with a navigation drawer.</span></span>

#### <a name="create-icons"></a><span data-ttu-id="375c0-129">Crear iconos</span><span class="sxs-lookup"><span data-stu-id="375c0-129">Create icons</span></span>

1. <span data-ttu-id="375c0-130">Haga clic con el botón secundario en **la carpeta app/res/drawable** y seleccione **Nuevo** y, a continuación, **Vector Asset**.</span><span class="sxs-lookup"><span data-stu-id="375c0-130">Right-click the **app/res/drawable** folder and select **New**, then **Vector Asset**.</span></span>

1. <span data-ttu-id="375c0-131">Haga clic en el botón de icono junto **a Imágenes prediseñadas.**</span><span class="sxs-lookup"><span data-stu-id="375c0-131">Click the icon button next to **Clip Art**.</span></span>

1. <span data-ttu-id="375c0-132">En la **ventana Seleccionar** icono, escriba en la barra de búsqueda, seleccione el icono Inicio y `home` seleccione  **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="375c0-132">In the **Select Icon** window, type `home` in the search bar, then select the **Home** icon and select **OK**.</span></span>

1. <span data-ttu-id="375c0-133">Cambie el **nombre** a `ic_menu_home` .</span><span class="sxs-lookup"><span data-stu-id="375c0-133">Change the **Name** to `ic_menu_home`.</span></span>

    ![Captura de pantalla de la ventana Configurar activos vectoriales](./images/create-icon.png)

1. <span data-ttu-id="375c0-135">Seleccione **Siguiente** y, a **continuación, Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="375c0-135">Select **Next**, then **Finish**.</span></span>

1. <span data-ttu-id="375c0-136">Repita el paso anterior para crear cuatro iconos más.</span><span class="sxs-lookup"><span data-stu-id="375c0-136">Repeat the previous step to create four more icons.</span></span>

    - <span data-ttu-id="375c0-137">Nombre: `ic_menu_calendar` , Icono: `event`</span><span class="sxs-lookup"><span data-stu-id="375c0-137">Name: `ic_menu_calendar`, Icon: `event`</span></span>
    - <span data-ttu-id="375c0-138">Nombre: `ic_menu_add_event` , Icono: `add box`</span><span class="sxs-lookup"><span data-stu-id="375c0-138">Name: `ic_menu_add_event`, Icon: `add box`</span></span>
    - <span data-ttu-id="375c0-139">Nombre: `ic_menu_signout` , Icono: `exit to app`</span><span class="sxs-lookup"><span data-stu-id="375c0-139">Name: `ic_menu_signout`, Icon: `exit to app`</span></span>
    - <span data-ttu-id="375c0-140">Nombre: `ic_menu_signin` , Icono: `person add`</span><span class="sxs-lookup"><span data-stu-id="375c0-140">Name: `ic_menu_signin`, Icon: `person add`</span></span>

#### <a name="create-the-menu"></a><span data-ttu-id="375c0-141">Crear el menú</span><span class="sxs-lookup"><span data-stu-id="375c0-141">Create the menu</span></span>

1. <span data-ttu-id="375c0-142">Haga clic con el botón **secundario en la carpeta Res** y seleccione **Nuevo** y, a continuación, Directorio de recursos **de Android**.</span><span class="sxs-lookup"><span data-stu-id="375c0-142">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="375c0-143">Cambie el **tipo de recurso** a y seleccione `menu` **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="375c0-143">Change the **Resource type** to `menu` and select **OK**.</span></span>

1. <span data-ttu-id="375c0-144">Haga clic con el botón secundario en la nueva **carpeta de menús** y **seleccione Nuevo** y, a continuación, archivo de recursos de **menú.**</span><span class="sxs-lookup"><span data-stu-id="375c0-144">Right-click the new **menu** folder and select **New**, then **Menu resource file**.</span></span>

1. <span data-ttu-id="375c0-145">Asigne un nombre al `drawer_menu` archivo y seleccione **Aceptar.**</span><span class="sxs-lookup"><span data-stu-id="375c0-145">Name the file `drawer_menu` and select **OK**.</span></span>

1. <span data-ttu-id="375c0-146">Cuando se abra el archivo, seleccione la **pestaña** Código para ver el XML y, a continuación, reemplace todo el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="375c0-146">When the file opens, select the **Code** tab to view the XML, then replace the entire contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/menu/drawer_menu.xml":::

#### <a name="update-application-theme-and-layout"></a><span data-ttu-id="375c0-147">Actualizar el tema y el diseño de la aplicación</span><span class="sxs-lookup"><span data-stu-id="375c0-147">Update application theme and layout</span></span>

1. <span data-ttu-id="375c0-148">Abra el **archivo app/res/values/styles.xml** y reemplace `Theme.AppCompat.Light.DarkActionBar` por `Theme.AppCompat.Light.NoActionBar` .</span><span class="sxs-lookup"><span data-stu-id="375c0-148">Open the **app/res/values/styles.xml** file and replace `Theme.AppCompat.Light.DarkActionBar` with `Theme.AppCompat.Light.NoActionBar`.</span></span>

1. <span data-ttu-id="375c0-149">Agregue las siguientes líneas dentro del `style` elemento.</span><span class="sxs-lookup"><span data-stu-id="375c0-149">Add the following lines inside the `style` element.</span></span>

    ```xml
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>
    ```

1. <span data-ttu-id="375c0-150">Haz clic con el botón derecho **en la carpeta app/res/layout.**</span><span class="sxs-lookup"><span data-stu-id="375c0-150">Right-click the **app/res/layout** folder.</span></span>

1. <span data-ttu-id="375c0-151">Seleccione **Nuevo** y, a **continuación, archivo de recursos diseño**.</span><span class="sxs-lookup"><span data-stu-id="375c0-151">Select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="375c0-152">Asigne un nombre `nav_header` al archivo y cambie el elemento **Raíz** a `LinearLayout` y, a continuación, **seleccione Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="375c0-152">Name the file `nav_header` and change the **Root element** to `LinearLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="375c0-153">Abra el **nav_header.xml** archivo y seleccione la **pestaña** Texto. Reemplace todo el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="375c0-153">Open the **nav_header.xml** file and select the **Text** tab. Replace the entire contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/nav_header.xml":::

1. <span data-ttu-id="375c0-154">Abra el **archivo app/res/layout/activity_main.xml** y actualice el diseño a a reemplazando el `DrawerLayout` XML existente por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="375c0-154">Open the **app/res/layout/activity_main.xml** file and update the layout to a `DrawerLayout` by replacing the existing XML with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/activity_main.xml":::

1. <span data-ttu-id="375c0-155">Abre **app/res/values/strings.xml** y agrega los siguientes elementos dentro del `resources` elemento.</span><span class="sxs-lookup"><span data-stu-id="375c0-155">Open **app/res/values/strings.xml** and add the following elements inside the `resources` element.</span></span>

    ```xml
    <string name="navigation_drawer_open">Open navigation drawer</string>
    <string name="navigation_drawer_close">Close navigation drawer</string>
    ```

1. <span data-ttu-id="375c0-156">Abra el **archivo app/java/com.example/graphtutorial/MainActivity** y reemplace todo el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="375c0-156">Open the **app/java/com.example/graphtutorial/MainActivity** file and replace the entire contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.Menu;
    import android.view.MenuItem;
    import android.view.View;
    import android.widget.FrameLayout;
    import android.widget.ProgressBar;
    import android.widget.TextView;
    import androidx.annotation.NonNull;
    import androidx.appcompat.app.ActionBarDrawerToggle;
    import androidx.appcompat.app.AppCompatActivity;
    import androidx.appcompat.widget.Toolbar;
    import androidx.core.view.GravityCompat;
    import androidx.drawerlayout.widget.DrawerLayout;
    import com.google.android.material.navigation.NavigationView;

    public class MainActivity extends AppCompatActivity implements NavigationView.OnNavigationItemSelectedListener {
        private static final String SAVED_IS_SIGNED_IN = "isSignedIn";
        private static final String SAVED_USER_NAME = "userName";
        private static final String SAVED_USER_EMAIL = "userEmail";
        private static final String SAVED_USER_TIMEZONE = "userTimeZone";

        private DrawerLayout mDrawer;
        private NavigationView mNavigationView;
        private View mHeaderView;
        private boolean mIsSignedIn = false;
        private String mUserName = null;
        private String mUserEmail = null;
        private String mUserTimeZone = null;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            // Set the toolbar
            Toolbar toolbar = findViewById(R.id.toolbar);
            setSupportActionBar(toolbar);

            mDrawer = findViewById(R.id.drawer_layout);

            // Add the hamburger menu icon
            ActionBarDrawerToggle toggle = new ActionBarDrawerToggle(this, mDrawer, toolbar,
                    R.string.navigation_drawer_open, R.string.navigation_drawer_close);
            mDrawer.addDrawerListener(toggle);
            toggle.syncState();

            mNavigationView = findViewById(R.id.nav_view);

            // Set user name and email
            mHeaderView = mNavigationView.getHeaderView(0);
            setSignedInState(mIsSignedIn);

            // Listen for item select events on menu
            mNavigationView.setNavigationItemSelectedListener(this);

            if (savedInstanceState == null) {
                // Load the home fragment by default on startup
                openHomeFragment(mUserName);
            } else {
                // Restore state
                mIsSignedIn = savedInstanceState.getBoolean(SAVED_IS_SIGNED_IN);
                mUserName = savedInstanceState.getString(SAVED_USER_NAME);
                mUserEmail = savedInstanceState.getString(SAVED_USER_EMAIL);
                mUserTimeZone = savedInstanceState.getString(SAVED_USER_TIMEZONE);
                setSignedInState(mIsSignedIn);
            }
        }

        @Override
        protected void onSaveInstanceState(@NonNull Bundle outState) {
            super.onSaveInstanceState(outState);
            outState.putBoolean(SAVED_IS_SIGNED_IN, mIsSignedIn);
            outState.putString(SAVED_USER_NAME, mUserName);
            outState.putString(SAVED_USER_EMAIL, mUserEmail);
            outState.putString(SAVED_USER_TIMEZONE, mUserTimeZone);
        }

        @Override
        public boolean onNavigationItemSelected(@NonNull MenuItem menuItem) {
            // TEMPORARY
            return false;
        }

        @Override
        public void onBackPressed() {
            if (mDrawer.isDrawerOpen(GravityCompat.START)) {
                mDrawer.closeDrawer(GravityCompat.START);
            } else {
                super.onBackPressed();
            }
        }

        public void showProgressBar()
        {
            FrameLayout container = findViewById(R.id.fragment_container);
            ProgressBar progressBar = findViewById(R.id.progressbar);
            container.setVisibility(View.GONE);
            progressBar.setVisibility(View.VISIBLE);
        }

        public void hideProgressBar()
        {
            FrameLayout container = findViewById(R.id.fragment_container);
            ProgressBar progressBar = findViewById(R.id.progressbar);
            progressBar.setVisibility(View.GONE);
            container.setVisibility(View.VISIBLE);
        }

        // Update the menu and get the user's name and email
        private void setSignedInState(boolean isSignedIn) {
            mIsSignedIn = isSignedIn;

            mNavigationView.getMenu().clear();
            mNavigationView.inflateMenu(R.menu.drawer_menu);

            Menu menu = mNavigationView.getMenu();

            // Hide/show the Sign in, Calendar, and Sign Out buttons
            if (isSignedIn) {
                menu.removeItem(R.id.nav_signin);
            } else {
                menu.removeItem(R.id.nav_home);
                menu.removeItem(R.id.nav_calendar);
                menu.removeItem(R.id.nav_create_event);
                menu.removeItem(R.id.nav_signout);
            }

            // Set the user name and email in the nav drawer
            TextView userName = mHeaderView.findViewById(R.id.user_name);
            TextView userEmail = mHeaderView.findViewById(R.id.user_email);

            if (isSignedIn) {
                // For testing
                mUserName = "Lynne Robbins";
                mUserEmail = "lynner@contoso.com";
                mUserTimeZone = "Pacific Standard Time";

                userName.setText(mUserName);
                userEmail.setText(mUserEmail);
            } else {
                mUserName = null;
                mUserEmail = null;
                mUserTimeZone = null;

                userName.setText("Please sign in");
                userEmail.setText("");
            }
        }
    }
    ```

### <a name="add-fragments"></a><span data-ttu-id="375c0-157">Agregar fragmentos</span><span class="sxs-lookup"><span data-stu-id="375c0-157">Add fragments</span></span>

<span data-ttu-id="375c0-158">En esta sección creará fragmentos para las vistas principal y de calendario.</span><span class="sxs-lookup"><span data-stu-id="375c0-158">In this section you will create fragments for the home and calendar views.</span></span>

1. <span data-ttu-id="375c0-159">Haga clic con el botón secundario en **la carpeta app/res/layout** y seleccione **Nuevo** y, a continuación, archivo de recursos **de diseño.**</span><span class="sxs-lookup"><span data-stu-id="375c0-159">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="375c0-160">Asigne un nombre `fragment_home` al archivo y cambie el elemento **Raíz** a `RelativeLayout` y, a continuación, **seleccione Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="375c0-160">Name the file `fragment_home` and change the **Root element** to `RelativeLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="375c0-161">Abra el **fragment_home.xml** archivo y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="375c0-161">Open the **fragment_home.xml** file and replace its contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_home.xml":::

1. <span data-ttu-id="375c0-162">Haga clic con el botón secundario en **la carpeta app/res/layout** y seleccione **Nuevo** y, a continuación, archivo de recursos **de diseño.**</span><span class="sxs-lookup"><span data-stu-id="375c0-162">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="375c0-163">Asigne un nombre `fragment_calendar` al archivo y cambie el elemento **Raíz** a `RelativeLayout` y, a continuación, **seleccione Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="375c0-163">Name the file `fragment_calendar` and change the **Root element** to `RelativeLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="375c0-164">Abra el **fragment_calendar.xml** archivo y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="375c0-164">Open the **fragment_calendar.xml** file and replace its contents with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:text="Calendar"
            android:textSize="30sp" />

    </RelativeLayout>
    ```

1. <span data-ttu-id="375c0-165">Haga clic con el botón secundario en **la carpeta app/res/layout** y seleccione **Nuevo** y, a continuación, archivo de recursos **de diseño.**</span><span class="sxs-lookup"><span data-stu-id="375c0-165">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="375c0-166">Asigne un nombre `fragment_new_event` al archivo y cambie el elemento **Raíz** a `RelativeLayout` y, a continuación, **seleccione Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="375c0-166">Name the file `fragment_new_event` and change the **Root element** to `RelativeLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="375c0-167">Abra el **fragment_new_event.xml** archivo y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="375c0-167">Open the **fragment_new_event.xml** file and replace its contents with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:text="New Event"
            android:textSize="30sp" />

    </RelativeLayout>
    ```

1. <span data-ttu-id="375c0-168">Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**.</span><span class="sxs-lookup"><span data-stu-id="375c0-168">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="375c0-169">Asigne un nombre a `HomeFragment` la clase y, a continuación, **seleccione Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="375c0-169">Name the class `HomeFragment`, then select **OK**.</span></span>

1. <span data-ttu-id="375c0-170">Abra el **archivo HomeFragment** y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="375c0-170">Open the **HomeFragment** file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/HomeFragment.java" id="HomeSnippet":::

1. <span data-ttu-id="375c0-171">Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**.</span><span class="sxs-lookup"><span data-stu-id="375c0-171">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="375c0-172">Asigne un nombre a `CalendarFragment` la clase y, a continuación, **seleccione Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="375c0-172">Name the class `CalendarFragment`, then select **OK**.</span></span>

1. <span data-ttu-id="375c0-173">Abra el **archivo CalendarFragment** y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="375c0-173">Open the **CalendarFragment** file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;
    import androidx.fragment.app.Fragment;

    public class CalendarFragment extends Fragment {
        private static final String TIME_ZONE = "timeZone";

        private String mTimeZone;

        public CalendarFragment() {}

        public static CalendarFragment createInstance(String timeZone) {
            CalendarFragment fragment = new CalendarFragment();

            // Add the provided time zone to the fragment's arguments
            Bundle args = new Bundle();
            args.putString(TIME_ZONE, timeZone);
            fragment.setArguments(args);
            return fragment;
        }

        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            if (getArguments() != null) {
                mTimeZone = getArguments().getString(TIME_ZONE);
            }
        }

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            return inflater.inflate(R.layout.fragment_calendar, container, false);
        }
    }
    ```

1. <span data-ttu-id="375c0-174">Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**.</span><span class="sxs-lookup"><span data-stu-id="375c0-174">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="375c0-175">Asigne un nombre a `NewEventFragment` la clase y, a continuación, **seleccione Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="375c0-175">Name the class `NewEventFragment`, then select **OK**.</span></span>

1. <span data-ttu-id="375c0-176">Abra el **archivo NewEventFragment** y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="375c0-176">Open the **NewEventFragment** file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;
    import androidx.fragment.app.Fragment;

    public class NewEventFragment extends Fragment {
        private static final String TIME_ZONE = "timeZone";

        private String mTimeZone;

        public NewEventFragment() {}

        public static NewEventFragment createInstance(String timeZone) {
            NewEventFragment fragment = new NewEventFragment();

            // Add the provided time zone to the fragment's arguments
            Bundle args = new Bundle();
            args.putString(TIME_ZONE, timeZone);
            fragment.setArguments(args);
            return fragment;
        }

        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            if (getArguments() != null) {
                mTimeZone = getArguments().getString(TIME_ZONE);
            }
        }

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            return inflater.inflate(R.layout.fragment_new_event, container, false);
        }
    }
    ```

1. <span data-ttu-id="375c0-177">Abra el **archivo MainActivity.java** y agregue las siguientes funciones a la clase.</span><span class="sxs-lookup"><span data-stu-id="375c0-177">Open the **MainActivity.java** file and add the the following functions to the class.</span></span>

    ```java
    // Load the "Home" fragment
    public void openHomeFragment(String userName) {
        HomeFragment fragment = HomeFragment.createInstance(userName);
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, fragment)
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_home);
    }

    // Load the "Calendar" fragment
    private void openCalendarFragment(String timeZone) {
        CalendarFragment fragment = CalendarFragment.createInstance(timeZone);
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, fragment)
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_calendar);
    }

    // Load the "New Event" fragment
    private void openNewEventFragment(String timeZone) {
        NewEventFragment fragment = NewEventFragment.createInstance(timeZone);
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, fragment)
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_create_event);
    }

    private void signIn() {
        setSignedInState(true);
        openHomeFragment(mUserName);
    }

    private void signOut() {
        setSignedInState(false);
        openHomeFragment(mUserName);
    }
    ```

1. <span data-ttu-id="375c0-178">Reemplace la función `onNavigationItemSelected` existente por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="375c0-178">Replace the existing `onNavigationItemSelected` function with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnNavItemSelectedSnippet":::

1. <span data-ttu-id="375c0-179">Guarde todos los cambios.</span><span class="sxs-lookup"><span data-stu-id="375c0-179">Save all of your changes.</span></span>

1. <span data-ttu-id="375c0-180">En el **menú Ejecutar,** seleccione **Ejecutar "aplicación".**</span><span class="sxs-lookup"><span data-stu-id="375c0-180">On the **Run** menu, select **Run 'app'**.</span></span>

<span data-ttu-id="375c0-181">El menú de la aplicación debe funcionar para navegar entre  los dos fragmentos y cambiar cuando pulses los botones Iniciar sesión o **Cerrar** sesión.</span><span class="sxs-lookup"><span data-stu-id="375c0-181">The app's menu should work to navigate between the two fragments and change when you tap the **Sign in** or **Sign out** buttons.</span></span>

![Captura de pantalla de la aplicación](./images/app-screens.png)
