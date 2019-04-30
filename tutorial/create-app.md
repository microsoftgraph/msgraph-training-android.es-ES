<!-- markdownlint-disable MD002 MD041 -->

Abra Android Studio y seleccione **iniciar un nuevo proyecto de Android Studio** en la pantalla de bienvenida. En el cuadro de diálogo **crear nuevo proyecto** , seleccione **vaciar actividad**y, a continuación, elija **siguiente**.

![Captura de pantalla del cuadro de diálogo Crear nuevo proyecto en Android Studio](./images/choose-project.png)

En el cuadro de diálogo **configurar el proyecto** , **** establezca el `Graph Tutorial`nombre en, asegúrese de que el campo `Java` **idioma** esté establecido en y asegúrese de que el **nivel mínimo de API** esté establecido en. `API 27: Android 8.1 (Oreo)` Modifique el **nombre del paquete** y la **Ubicación de guardado** según sea necesario. Seleccione **Finalizar**.

![Captura de pantalla del cuadro de diálogo Configurar el proyecto](./images/configure-project.png)

> [!IMPORTANT]
> Asegúrese de que escribe exactamente el mismo nombre para el proyecto que se especifica en estas instrucciones de la práctica. El nombre del proyecto se convierte en parte del espacio de nombres en el código. El código incluido en estas instrucciones depende del espacio de nombres que coincida con el nombre de proyecto especificado en estas instrucciones. Si usa un nombre de proyecto diferente, el código no se compilará a menos que ajuste todos los espacios de nombres para que se correspondan con el nombre del proyecto que ha especificado al crear el proyecto.

Antes de continuar, instale algunas dependencias adicionales que usará más adelante.

- `com.android.support:design`para que los diseños de cajón de navegación estén disponibles para la aplicación.
- [Biblioteca de autenticación de Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) para administrar la autenticación y la administración de tokens de Azure ad.
- [SDK de Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) para realizar llamadas a Microsoft Graph.

ExPanda los **scripts Gradle**y, a continuación, abra el archivo **Build. Gradle (Module: app)** . Agregue las siguientes líneas dentro del `dependencies` valor.

```Gradle
implementation 'com.android.support:design:28.0.0'
implementation 'com.microsoft.graph:microsoft-graph:1.1.+'
implementation 'com.microsoft.identity.client:msal:0.2.+'
```

> [!NOTE]
> Si usa una versión de SDK diferente, asegúrese de cambiar el `28.0.0` para que coincide con la versión de la `com.android.support:appcompat-v7` dependencia que ya está presente en **Build. Gradle**.

Agregue un `packagingOptions` dentro del `android` valor en el archivo **Build. Gradle (Module: app)** .

```Gradle
packagingOptions {
    pickFirst 'META-INF/jersey-module-version'
}
```

Guarde los cambios. En el menú **archivo** , seleccione **sincronizar proyecto con archivos Gradle**.

## <a name="design-the-app"></a>Diseñar la aplicación

La aplicación usará un [cajón de navegación](https://developer.android.com/training/implementing-navigation/nav-drawer) para navegar entre distintas vistas. En este paso, se actualizará la actividad para usar un diseño de cajón de navegación y agregar fragmentos para las vistas.

### <a name="create-a-navigation-drawer"></a>Crear un cajón de navegación

Empiece por crear iconos para el menú de navegación de la aplicación. Haga clic con el botón derecho en la carpeta **App/res/drawable** , seleccione **nuevo**y, a continuación, **activo vector**. Haga clic en el botón de icono situado junto a **imágenes**prediseñadas. En la ventana **seleccionar icono** , escriba `home` en la barra de búsqueda y, a continuación, seleccione el icono **Inicio** y elija **Aceptar**. Cambie el **nombre** a `ic_menu_home`.

![Una captura de pantalla de la ventana Configurar activo vectorial](./images/create-icon.png)

Elija **siguiente**y, a continuación, **Finalizar**. Repita este paso para crear dos iconos más.

- Nombre: `ic_menu_calendar`, icono:`event`
- Nombre: `ic_menu_signout`, icono:`exit to app`
- Nombre: `ic_menu_signin`, icono:`person add`

A continuación, cree un menú para la aplicación. Haga clic con el botón derecho en la carpeta **res** , seleccione **nuevo**y, a continuación, **Directorio de recursos de Android**. Cambie el **tipo** de recurso `menu` a y haga clic en **Aceptar**.

Haga clic con el botón derecho en la carpeta de **menú** nueva y elija **nuevo**, y **archivo de recursos de menú**. Asigne un nombre `drawer_menu` al archivo y elija **Aceptar**. Cuando se abra el archivo, elija la pestaña **texto** para ver el XML y, a continuación, reemplace todo el contenido por lo siguiente.

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

Ahora, actualice el tema de la aplicación para que sea compatible con un cajón de navegación. Abra el archivo **App/res/Values/Styles. XML** . Reemplazar `Theme.AppCompat.Light.DarkActionBar` por `Theme.AppCompat.Light.NoActionBar`. A continuación, agregue las siguientes líneas `style` dentro del elemento.

```xml
<item name="windowActionBar">false</item>
<item name="windowNoTitle">true</item>
<item name="android:statusBarColor">@android:color/transparent</item>
```

A continuación, cree un encabezado para el menú. Haga clic con el botón secundario en la carpeta **App/res/layout** . Seleccione **nuevo**y, a continuación, **archivo de recursos de diseño**. Asigne un nombre `nav_header` al archivo y cambie el **elemento raíz** a `LinearLayout`. Elija **Aceptar**.

Abra el archivo **nav_header. XML** y elija la pestaña **texto** . Reemplace todo el contenido por lo siguiente.

```xml
<?xml version="1.0" encoding="utf-8"?>
<<?xml version="1.0" encoding="utf-8"?>
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

Ahora, abra el archivo **App/res/layout/activity_main. XML** . Actualice el diseño a un `DrawerLayout` reemplazando el XML existente por lo siguiente.

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
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

        <android.support.v7.widget.Toolbar
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

    <android.support.design.widget.NavigationView
        android:id="@+id/nav_view"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        app:headerLayout="@layout/nav_header"
        app:menu="@menu/drawer_menu" />

</android.support.v4.widget.DrawerLayout>
```

A continuación, Abra **App/res/Values/Strings. XML**. Agregue los siguientes elementos dentro del `resources` elemento.

```xml
<string name="navigation_drawer_open">Open navigation drawer</string>
<string name="navigation_drawer_close">Close navigation drawer</string>
```

Por último, abra el archivo **app/java/com. example/graphtutorial/MainActivity** . Reemplace todo el contenido por lo siguiente.

```java
package com.example.graphtutorial;

import android.support.annotation.NonNull;
import android.support.design.widget.NavigationView;
import android.support.v4.view.GravityCompat;
import android.support.v4.widget.DrawerLayout;
import android.support.v7.app.ActionBarDrawerToggle;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.support.v7.widget.Toolbar;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.TextView;

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

Haga clic con el botón secundario en la carpeta **App/res/layout** , seleccione **nuevo**y, a continuación, **archivo de recursos de distribución**. Asigne un nombre `fragment_home` al archivo y cambie el **elemento raíz** a `RelativeLayout`. Elija **Aceptar**.

Abra el archivo **fragment_home. XML** y reemplace su contenido por lo siguiente.

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

A continuación, haga clic con el botón derecho en la carpeta **App/res/layout** , seleccione **nuevo**y, a continuación, **archivo de recursos de distribución**. Asigne un nombre `fragment_calendar` al archivo y cambie el **elemento raíz** a `RelativeLayout`. Elija **Aceptar**.

Abra el archivo **fragment_calendar. XML** y reemplace su contenido por lo siguiente.

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

Ahora, haga clic con el botón secundario en la carpeta **app/java/com. example. graphtutorial** y seleccione **nuevo**y, a continuación, **clase Java**. Asigne un nombre `HomeFragment` a la clase y establezca `android.support.v4.app.Fragment`la **superclase** en. Elija **Aceptar**. Abra el archivo **HomeFragment** y reemplace el contenido por lo siguiente.

```java
package com.example.graphtutorial;

import android.os.Bundle;
import android.support.annotation.NonNull;
import android.support.annotation.Nullable;
import android.support.v4.app.Fragment;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

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

A continuación, haga clic con el botón derecho en la carpeta **app/java/com. example. graphtutorial** y seleccione **nuevo**y, a continuación, **clase Java**. Asigne un nombre `CalendarFragment` a la clase y establezca `android.support.v4.app.Fragment`la **superclase** en. Elija **Aceptar**.

Abra el archivo **CalendarFragment** y agregue la siguiente función a la `CalendarFragment` clase.

```java
@Nullable
@Override
public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
    return inflater.inflate(R.layout.fragment_calendar, container, false);
}
```

Ahora que los fragmentos se han implementado, `MainActivity` actualice la clase para `onNavigationItemSelected` controlar el evento y use los fragmentos. En primer lugar, agregue las siguientes funciones a la clase.

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

A continuación, reemplace la `onNavigationItemSelected` función existente por lo siguiente.

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

Por último, agregue lo siguiente al final de la `onCreate` función para cargar el fragmento de inicio cuando se inicie la aplicación.

```java
// Load the home fragment by default on startup
if (savedInstanceState == null) {
    openHomeFragment(mUserName);
}
```

Guarde todos los cambios. En el menú **Ejecutar** , elija **Ejecutar "aplicación"**. El menú de la aplicación debe funcionar para navegar entre los dos fragmentos y cambiar cuando Puntee **en los botones iniciar sesión** o **Cerrar sesión** .

![Captura de pantalla de la aplicación](./images/welcome-page.png)