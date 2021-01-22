<!-- markdownlint-disable MD002 MD041 -->

Comience por crear un nuevo proyecto de Android Studio.

1. Abre Android Studio y selecciona **Iniciar un nuevo proyecto de Android Studio** en la pantalla de bienvenida.

1. En el **cuadro de diálogo Crear nuevo** proyecto, seleccione Actividad **vacía** y, a continuación, **seleccione Siguiente**.

    ![Captura de pantalla del cuadro de diálogo Crear nuevo proyecto en Android Studio](./images/choose-project.png)

1. En el **cuadro de** diálogo  Configurar el proyecto, establezca el nombre en , asegúrese de que el campo Idioma está establecido en y asegúrese de que el nivel mínimo de `Graph Tutorial` API está establecido en  `Java`  `API 29: Android 10.0 (Q)` . Modifique el **nombre del paquete y** la ubicación de guardar **según** sea necesario. Seleccione **Finalizar**.

    ![Captura de pantalla del cuadro de diálogo Configurar el proyecto](./images/configure-project.png)

> [!IMPORTANT]
> El código y las instrucciones de este tutorial usan el nombre del **paquete com.example.graphtutorial**. Si usas un nombre de paquete diferente al crear el proyecto, asegúrate de usar el nombre del paquete dondequiera que veas este valor.

## <a name="install-dependencies"></a>Instalar dependencias

Antes de pasar, instale algunas dependencias adicionales que usará más adelante.

- `com.google.android.material:material` para que la [vista de navegación](https://material.io/develop/android/components/navigation-view/) esté disponible para la aplicación.
- [Biblioteca de autenticación de Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) para administrar la autenticación de Azure AD y la administración de tokens.
- [SDK de Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) para realizar llamadas a Microsoft Graph.

1. Expanda **Scripts de Gradle** y, a **continuación, abra build.gradle (módulo: Graph_Tutorial.app).**

1. Agregue las siguientes líneas dentro del `dependencies` valor.

    :::code language="gradle" source="../demo/GraphTutorial/app/build.gradle" id="DependenciesSnippet":::

1. Agregue un `packagingOptions` valor dentro del valor en `android` **build.gradle (Módulo: Graph_Tutorial.app).**

    ```Gradle
    packagingOptions {
        pickFirst 'META-INF/jersey-module-version'
    }
    ```

1. Agregue el repositorio de Azure Maven para la biblioteca MicrosoftDeviceSDK, una dependencia de MSAL. Abra **build.gradle (Project: Graph_Tutorial)**. Agregue lo siguiente al `repositories` valor dentro del `allprojects` valor.

    ```Gradle
    maven {
        url 'https://pkgs.dev.azure.com/MicrosoftDeviceSDK/DuoSDK-Public/_packaging/Duo-SDK-Feed/maven/v1'
    }
    ```

1. Guarde los cambios. En el **menú** Archivo, seleccione **Sincronizar proyecto con archivos gradle**.

## <a name="design-the-app"></a>Diseñar la aplicación

La aplicación usará un cajón de navegación para navegar entre distintas vistas. En este paso actualizará la actividad para usar un diseño de cajón de navegación y agregará fragmentos para las vistas.

### <a name="create-a-navigation-drawer"></a>Crear un cajón de navegación

En esta sección creará iconos para el menú de navegación de la aplicación, creará un menú para la aplicación y actualizará el tema y el diseño de la aplicación para que sean compatibles con un cajón de navegación.

#### <a name="create-icons"></a>Crear iconos

1. Haga clic con el botón secundario en **la carpeta app/res/drawable** y seleccione **Nuevo** y, a continuación, **Vector Asset**.

1. Haga clic en el botón de icono junto **a Imágenes prediseñadas.**

1. En la **ventana Seleccionar** icono, escriba en la barra de búsqueda, seleccione el icono Inicio y `home` seleccione  **Aceptar**.

1. Cambie el **nombre** a `ic_menu_home` .

    ![Captura de pantalla de la ventana Configurar activos vectoriales](./images/create-icon.png)

1. Seleccione **Siguiente** y, a **continuación, Finalizar**.

1. Repita el paso anterior para crear cuatro iconos más.

    - Nombre: `ic_menu_calendar` , Icono: `event`
    - Nombre: `ic_menu_add_event` , Icono: `add box`
    - Nombre: `ic_menu_signout` , Icono: `exit to app`
    - Nombre: `ic_menu_signin` , Icono: `person add`

#### <a name="create-the-menu"></a>Crear el menú

1. Haga clic con el botón **secundario en la carpeta Res** y seleccione **Nuevo** y, a continuación, Directorio de recursos **de Android**.

1. Cambie el **tipo de recurso** a y seleccione `menu` **Aceptar**.

1. Haga clic con el botón secundario en la nueva **carpeta de menús** y **seleccione Nuevo** y, a continuación, archivo de recursos de **menú.**

1. Asigne un nombre al `drawer_menu` archivo y seleccione **Aceptar.**

1. Cuando se abra el archivo, seleccione la **pestaña** Código para ver el XML y, a continuación, reemplace todo el contenido por lo siguiente.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/menu/drawer_menu.xml":::

#### <a name="update-application-theme-and-layout"></a>Actualizar el tema y el diseño de la aplicación

1. Abra el **archivo app/res/values/styles.xml** y reemplace `Theme.AppCompat.Light.DarkActionBar` por `Theme.AppCompat.Light.NoActionBar` .

1. Agregue las siguientes líneas dentro del `style` elemento.

    ```xml
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>
    ```

1. Haz clic con el botón derecho **en la carpeta app/res/layout.**

1. Seleccione **Nuevo** y, a **continuación, archivo de recursos diseño**.

1. Asigne un nombre `nav_header` al archivo y cambie el elemento **Raíz** a `LinearLayout` y, a continuación, **seleccione Aceptar**.

1. Abra el **nav_header.xml** archivo y seleccione la **pestaña** Texto. Reemplace todo el contenido por lo siguiente.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/nav_header.xml":::

1. Abra el **archivo app/res/layout/activity_main.xml** y actualice el diseño a a reemplazando el `DrawerLayout` XML existente por lo siguiente.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/activity_main.xml":::

1. Abre **app/res/values/strings.xml** y agrega los siguientes elementos dentro del `resources` elemento.

    ```xml
    <string name="navigation_drawer_open">Open navigation drawer</string>
    <string name="navigation_drawer_close">Close navigation drawer</string>
    ```

1. Abra el **archivo app/java/com.example/graphtutorial/MainActivity** y reemplace todo el contenido por lo siguiente.

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

### <a name="add-fragments"></a>Agregar fragmentos

En esta sección creará fragmentos para las vistas principal y de calendario.

1. Haga clic con el botón secundario en **la carpeta app/res/layout** y seleccione **Nuevo** y, a continuación, archivo de recursos **de diseño.**

1. Asigne un nombre `fragment_home` al archivo y cambie el elemento **Raíz** a `RelativeLayout` y, a continuación, **seleccione Aceptar**.

1. Abra el **fragment_home.xml** archivo y reemplace su contenido por lo siguiente.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_home.xml":::

1. Haga clic con el botón secundario en **la carpeta app/res/layout** y seleccione **Nuevo** y, a continuación, archivo de recursos **de diseño.**

1. Asigne un nombre `fragment_calendar` al archivo y cambie el elemento **Raíz** a `RelativeLayout` y, a continuación, **seleccione Aceptar**.

1. Abra el **fragment_calendar.xml** archivo y reemplace su contenido por lo siguiente.

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

1. Haga clic con el botón secundario en **la carpeta app/res/layout** y seleccione **Nuevo** y, a continuación, archivo de recursos **de diseño.**

1. Asigne un nombre `fragment_new_event` al archivo y cambie el elemento **Raíz** a `RelativeLayout` y, a continuación, **seleccione Aceptar**.

1. Abra el **fragment_new_event.xml** archivo y reemplace su contenido por lo siguiente.

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

1. Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**.

1. Asigne un nombre a `HomeFragment` la clase y, a continuación, **seleccione Aceptar**.

1. Abra el **archivo HomeFragment** y reemplace su contenido por lo siguiente.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/HomeFragment.java" id="HomeSnippet":::

1. Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**.

1. Asigne un nombre a `CalendarFragment` la clase y, a continuación, **seleccione Aceptar**.

1. Abra el **archivo CalendarFragment** y reemplace su contenido por lo siguiente.

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

1. Haga clic con el botón secundario en la carpeta **app/java/com.example.graphtutorial** y seleccione Nuevo **y,** a continuación, **Java clase**.

1. Asigne un nombre a `NewEventFragment` la clase y, a continuación, **seleccione Aceptar**.

1. Abra el **archivo NewEventFragment** y reemplace su contenido por lo siguiente.

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

1. Abra el **archivo MainActivity.java** y agregue las siguientes funciones a la clase.

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

1. Reemplace la función `onNavigationItemSelected` existente por lo siguiente.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnNavItemSelectedSnippet":::

1. Guarde todos los cambios.

1. En el **menú Ejecutar,** seleccione **Ejecutar "aplicación".**

El menú de la aplicación debe funcionar para navegar entre  los dos fragmentos y cambiar cuando pulses los botones Iniciar sesión o **Cerrar** sesión.

![Captura de pantalla de la aplicación](./images/app-screens.png)
