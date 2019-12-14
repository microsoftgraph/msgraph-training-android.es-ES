<!-- markdownlint-disable MD002 MD041 -->

Para empezar, cree un nuevo proyecto de Android Studio.

1. Abra Android Studio y seleccione **iniciar un nuevo proyecto de Android Studio** en la pantalla de bienvenida.

1. En el cuadro de diálogo **crear nuevo proyecto** , seleccione **actividad vacía**y, a continuación, seleccione **siguiente**.

    ![Captura de pantalla del cuadro de diálogo Crear nuevo proyecto en Android Studio](./images/choose-project.png)

1. En el cuadro de diálogo **configurar el proyecto** , **** establezca el `Graph Tutorial`nombre en, asegúrese de que el campo `Java` **idioma** esté establecido en y asegúrese de que el **nivel mínimo de API** esté establecido en. `API 29: Android 10.0 (Q)` Modifique el **nombre del paquete** y la **Ubicación de guardado** según sea necesario. Seleccione **Finalizar**.

    ![Captura de pantalla del cuadro de diálogo Configurar el proyecto](./images/configure-project.png)

> [!IMPORTANT]
> El código y las instrucciones de este tutorial usan el nombre de paquete **com. example. graphtutorial**. Si usa un nombre de paquete diferente al crear el proyecto, asegúrese de usar el nombre del paquete en cualquier lugar en el que vea este valor.

## <a name="install-dependencies"></a>Instalar dependencias

Antes de continuar, instale algunas dependencias adicionales que usará más adelante.

- `com.google.android.material:material`para que la [vista de navegación](https://material.io/develop/android/components/navigation-view/) esté disponible para la aplicación.
- [Biblioteca de autenticación de Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) para administrar la autenticación y la administración de tokens de Azure ad.
- [SDK de Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) para realizar llamadas a Microsoft Graph.

1. Expanda los **scripts Gradle**y, a continuación, abra el archivo **Build. Gradle (Module: app)** .

1. Agregue las siguientes líneas dentro del `dependencies` valor.

    ```Gradle
    implementation 'com.google.android.material:material:1.0.0'
    implementation 'com.microsoft.identity.client:msal:1.0.0'
    implementation 'com.microsoft.graph:microsoft-graph:1.6.0'
    ```

1. Agregue un `packagingOptions` valor dentro del `android` valor en el archivo **Build. Gradle (Module: app)** .

    ```Gradle
    packagingOptions {
        pickFirst 'META-INF/jersey-module-version'
    }
    ```

1. Guarde los cambios. En el menú **archivo** , seleccione **sincronizar proyecto con archivos Gradle**.

## <a name="design-the-app"></a>Diseñar la aplicación

La aplicación usará un cajón de navegación para navegar entre distintas vistas. En este paso, se actualizará la actividad para usar un diseño de cajón de navegación y agregar fragmentos para las vistas.

### <a name="create-a-navigation-drawer"></a>Crear un cajón de navegación

En esta sección se crean iconos para el menú de navegación de la aplicación, se crea un menú para la aplicación y se actualiza el tema y el diseño de la aplicación para que sea compatible con un cajón de navegación.

#### <a name="create-icons"></a>Crear iconos

1. Haga clic con el botón derecho en la carpeta **App/res/drawable** , seleccione **nuevo**y, a continuación, **activo vector**.

1. Haga clic en el botón de icono situado junto a **imágenes prediseñadas**.

1. En la ventana **seleccionar icono** , escriba `home` en la barra de búsqueda, seleccione el icono **Inicio** y haga clic en **Aceptar**.

1. Cambie el **nombre** a `ic_menu_home`.

    ![Una captura de pantalla de la ventana Configurar activo vectorial](./images/create-icon.png)

1. Seleccione **siguiente**y, a continuación, **Finalizar**.

1. Repita el paso anterior para crear tres iconos más.

    - Nombre: `ic_menu_calendar`, icono:`event`
    - Nombre: `ic_menu_signout`, icono:`exit to app`
    - Nombre: `ic_menu_signin`, icono:`person add`

#### <a name="create-the-menu"></a>Crear el menú

1. Haga clic con el botón derecho en la carpeta **res** y seleccione **nuevo**y, a continuación, **Directorio de recursos de Android**.

1. Cambie el **tipo** de recurso `menu` a y haga clic en **Aceptar**.

1. Haga clic con el botón derecho en la carpeta de **menú** nueva y seleccione **nuevo**, **archivo de recurso de menú**.

1. Asigne un nombre `drawer_menu` al archivo y seleccione **Aceptar**.

1. Cuando se abra el archivo, seleccione la pestaña **texto** para ver el XML y, a continuación, reemplace todo el contenido por lo siguiente.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <menu xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        tools:showIn="navigation_view">

        <group android:checkableBehavior="single">
            <item
                android:id="@+id/nav_home"
                android:icon="@drawable/ic_menu_home"
                android:title="Home" />

            <item
                android:id="@+id/nav_calendar"
                android:icon="@drawable/ic_menu_calendar"
                android:title="Calendar" />
        </group>

        <item android:title="Account">
            <menu>
                <item
                    android:id="@+id/nav_signin"
                    android:icon="@drawable/ic_menu_signin"
                    android:title="Sign In" />

                <item
                    android:id="@+id/nav_signout"
                    android:icon="@drawable/ic_menu_signout"
                    android:title="Sign Out" />
            </menu>
        </item>

    </menu>
    ```

#### <a name="update-application-theme-and-layout"></a>Actualizar el tema y el diseño de la aplicación

1. Abra el archivo **App/res/Values/Styles. XML** y reemplace `Theme.AppCompat.Light.DarkActionBar` por `Theme.AppCompat.Light.NoActionBar`.

1. Agregue las siguientes líneas dentro del `style` elemento.

    ```xml
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>
    ```

1. Haga clic con el botón secundario en la carpeta **App/res/layout** .

1. Seleccione **nuevo**y, a continuación, **archivo de recursos de diseño**.

1. Asigne un nombre `nav_header` al archivo y cambie el **elemento raíz** a `LinearLayout`y, a continuación, seleccione **Aceptar**.

1. Abra el archivo **nav_header. XML** y seleccione la pestaña **texto** . Reemplace todo el contenido por lo siguiente.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="176dp"
        android:background="@color/colorPrimary"
        android:gravity="bottom"
        android:orientation="vertical"
        android:padding="16dp"
        android:theme="@style/ThemeOverlay.AppCompat.Dark">

        <ImageView
            android:id="@+id/user_profile_pic"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@mipmap/ic_launcher" />

        <TextView
            android:id="@+id/user_name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:paddingTop="8dp"
            android:text="Test User"
            android:textAppearance="@style/TextAppearance.AppCompat.Body1" />

        <TextView
            android:id="@+id/user_email"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="test@contoso.com" />

    </LinearLayout>
    ```

1. Abra el archivo **App/res/layout/activity_main. XML** y actualice el diseño a un `DrawerLayout` reemplazando el XML existente por lo siguiente.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <androidx.drawerlayout.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:id="@+id/drawer_layout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:fitsSystemWindows="true"
        tools:context=".MainActivity"
        tools:openDrawer="start">

        <RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">

            <ProgressBar
                android:id="@+id/progressbar"
                android:layout_width="75dp"
                android:layout_height="75dp"
                android:layout_centerInParent="true"
                android:visibility="gone"/>

            <androidx.appcompat.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                android:background="@color/colorPrimary"
                android:elevation="4dp"
                android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar" />

            <FrameLayout
                android:id="@+id/fragment_container"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:layout_below="@+id/toolbar" />
        </RelativeLayout>

        <com.google.android.material.navigation.NavigationView
            android:id="@+id/nav_view"
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_gravity="start"
            app:headerLayout="@layout/nav_header"
            app:menu="@menu/drawer_menu" />

    </androidx.drawerlayout.widget.DrawerLayout>
    ```

1. Abra **App/res/Values/Strings. XML** y agregue los siguientes elementos dentro `resources` del elemento.

    ```xml
    <string name="navigation_drawer_open">Open navigation drawer</string>
    <string name="navigation_drawer_close">Close navigation drawer</string>
    ```

1. Abra el archivo **app/java/com. example/graphtutorial/MainActivity** y reemplace todo el contenido por lo siguiente.

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
        private DrawerLayout mDrawer;
        private NavigationView mNavigationView;
        private View mHeaderView;
        private boolean mIsSignedIn = false;
        private String mUserName = null;
        private String mUserEmail = null;

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

            Menu menu = mNavigationView.getMenu();

            // Hide/show the Sign in, Calendar, and Sign Out buttons
            menu.findItem(R.id.nav_signin).setVisible(!isSignedIn);
            menu.findItem(R.id.nav_calendar).setVisible(isSignedIn);
            menu.findItem(R.id.nav_signout).setVisible(isSignedIn);

            // Set the user name and email in the nav drawer
            TextView userName = mHeaderView.findViewById(R.id.user_name);
            TextView userEmail = mHeaderView.findViewById(R.id.user_email);

            if (isSignedIn) {
                // For testing
                mUserName = "Megan Bowen";
                mUserEmail = "meganb@contoso.com";

                userName.setText(mUserName);
                userEmail.setText(mUserEmail);
            } else {
                mUserName = null;
                mUserEmail = null;

                userName.setText("Please sign in");
                userEmail.setText("");
            }
        }
    }
    ```

### <a name="add-fragments"></a>Agregar fragmentos

En esta sección, creará fragmentos para las vistas de inicio y calendario.

1. Haga clic con el botón derecho en la carpeta **App/res/layout** y seleccione **nuevo**y, a continuación, **archivo de recursos de distribución**.

1. Asigne un nombre `fragment_home` al archivo y cambie el **elemento raíz** a `RelativeLayout`y, a continuación, seleccione **Aceptar**.

1. Abra el archivo **fragment_home. XML** y reemplace el contenido por lo siguiente.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <LinearLayout
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:orientation="vertical">

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center_horizontal"
                android:text="Welcome!"
                android:textSize="30sp" />

            <TextView
                android:id="@+id/home_page_username"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center_horizontal"
                android:paddingTop="8dp"
                android:text="Please sign in"
                android:textSize="20sp" />
        </LinearLayout>

    </RelativeLayout>
    ```

1. Haga clic con el botón derecho en la carpeta **App/res/layout** y seleccione **nuevo**y, a continuación, **archivo de recursos de distribución**.

1. Asigne un nombre `fragment_calendar` al archivo y cambie el **elemento raíz** a `RelativeLayout`y, a continuación, seleccione **Aceptar**.

1. Abra el archivo **fragment_calendar. XML** y reemplace el contenido por lo siguiente.

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

1. Haga clic con el botón secundario en la carpeta **app/java/com. example. graphtutorial** y seleccione **nuevo**y, a continuación, **clase Java**.

1. Asigne un nombre `HomeFragment` a la clase y establezca `androidx.fragment.app.Fragment`la **superclase** en y, a continuación, seleccione **Aceptar**.

1. Abra el archivo **HomeFragment** y reemplace el contenido por lo siguiente.

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import android.widget.TextView;
    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;
    import androidx.fragment.app.Fragment;

    public class HomeFragment extends Fragment {
        private static final String USER_NAME = "userName";

        private String mUserName;

        public HomeFragment() {

        }

        public static HomeFragment createInstance(String userName) {
            HomeFragment fragment = new HomeFragment();

            // Add the provided username to the fragment's arguments
            Bundle args = new Bundle();
            args.putString(USER_NAME, userName);
            fragment.setArguments(args);
            return fragment;
        }

        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            if (getArguments() != null) {
                mUserName = getArguments().getString(USER_NAME);
            }
        }

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            View homeView = inflater.inflate(R.layout.fragment_home, container, false);

            // If there is a username, replace the "Please sign in" with the username
            if (mUserName != null) {
                TextView userName = homeView.findViewById(R.id.home_page_username);
                userName.setText(mUserName);
            }

            return homeView;
        }
    }
    ```

1. Haga clic con el botón secundario en la carpeta **app/java/com. example. graphtutorial** y seleccione **nuevo**y, a continuación, **clase Java**.

1. Asigne un nombre `CalendarFragment` a la clase y establezca `androidx.fragment.app.Fragment`la **superclase** en y, a continuación, seleccione **Aceptar**.

1. Abra el archivo **CalendarFragment** y reemplace el contenido por lo siguiente.

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

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            return inflater.inflate(R.layout.fragment_calendar, container, false);
        }
    }
    ```

1. Abra el archivo **MainActivity. Java** y agregue las siguientes funciones a la clase.

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
    private void openCalendarFragment() {
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, new CalendarFragment())
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_calendar);
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

    ```java
    @Override
    public boolean onNavigationItemSelected(@NonNull MenuItem menuItem) {
        // Load the fragment that corresponds to the selected item
        switch (menuItem.getItemId()) {
            case R.id.nav_home:
                openHomeFragment(mUserName);
                break;
            case R.id.nav_calendar:
                openCalendarFragment();
                break;
            case R.id.nav_signin:
                signIn();
                break;
            case R.id.nav_signout:
                signOut();
                break;
        }

        mDrawer.closeDrawer(GravityCompat.START);

        return true;
    }
    ```

1. Agregue lo siguiente al final de la `onCreate` función para cargar el fragmento de inicio cuando se inicie la aplicación.

    ```java
    // Load the home fragment by default on startup
    if (savedInstanceState == null) {
        openHomeFragment(mUserName);
    }
    ```

1. Guarde todos los cambios.

1. En el menú **Ejecutar** , seleccione **Ejecutar ' aplicación '**.

El menú de la aplicación debe funcionar para navegar entre los dos fragmentos y cambiar cuando Puntee **en los botones iniciar sesión** o **Cerrar sesión** .

![Captura de pantalla de la aplicación](./images/app-screens.png)
