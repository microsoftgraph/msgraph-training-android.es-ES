<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="6f7b8-101">Abra Android Studio y seleccione **iniciar un nuevo proyecto de Android Studio** en la pantalla de bienvenida.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-101">Open Android Studio, and select **Start a new Android Studio project** on the welcome screen.</span></span> <span data-ttu-id="6f7b8-102">En el cuadro de diálogo **crear nuevo proyecto** , seleccione **vaciar actividad**y, a continuación, elija **siguiente**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-102">In the **Create New Project** dialog, select **Empty Activity**, then choose **Next**.</span></span>

![Captura de pantalla del cuadro de diálogo Crear nuevo proyecto en Android Studio](./images/choose-project.png)

<span data-ttu-id="6f7b8-104">En el cuadro de diálogo **configurar el proyecto** , \*\*\*\* establezca el `Graph Tutorial`nombre en, asegúrese de que el campo `Java` **idioma** esté establecido en y asegúrese de que el **nivel mínimo de API** esté establecido en. `API 27: Android 8.1 (Oreo)`</span><span class="sxs-lookup"><span data-stu-id="6f7b8-104">In the **Configure your project** dialog, set the **Name** to `Graph Tutorial`, ensure the **Language** field is set to `Java`, and ensure the **Minimum API level** is set to `API 27: Android 8.1 (Oreo)`.</span></span> <span data-ttu-id="6f7b8-105">Modifique el **nombre del paquete** y la **Ubicación de guardado** según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-105">Modify the **Package name** and **Save location** as needed.</span></span> <span data-ttu-id="6f7b8-106">Seleccione **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-106">Select **Finish**.</span></span>

![Captura de pantalla del cuadro de diálogo Configurar el proyecto](./images/configure-project.png)

> [!IMPORTANT]
> <span data-ttu-id="6f7b8-108">Asegúrese de que escribe exactamente el mismo nombre para el proyecto que se especifica en estas instrucciones de la práctica.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-108">Ensure that you enter the exact same name for the project that is specified in these lab instructions.</span></span> <span data-ttu-id="6f7b8-109">El nombre del proyecto se convierte en parte del espacio de nombres en el código.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-109">The project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="6f7b8-110">El código incluido en estas instrucciones depende del espacio de nombres que coincida con el nombre de proyecto especificado en estas instrucciones.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-110">The code inside these instructions depends on the namespace matching the project name specified in these instructions.</span></span> <span data-ttu-id="6f7b8-111">Si usa un nombre de proyecto diferente, el código no se compilará a menos que ajuste todos los espacios de nombres para que se correspondan con el nombre del proyecto que ha especificado al crear el proyecto.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-111">If you use a different project name the code will not compile unless you adjust all the namespaces to match the project name you enter when you create the project.</span></span>

<span data-ttu-id="6f7b8-112">Antes de continuar, instale algunas dependencias adicionales que usará más adelante.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-112">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="6f7b8-113">`com.android.support:design`para que los diseños de cajón de navegación estén disponibles para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-113">`com.android.support:design` to make the navigation drawer layouts available to the app.</span></span>
- <span data-ttu-id="6f7b8-114">[Biblioteca de autenticación de Microsoft (MSAL) para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) para administrar la autenticación y la administración de tokens de Azure ad.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-114">[Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="6f7b8-115">[SDK de Microsoft Graph para Java](https://github.com/microsoftgraph/msgraph-sdk-java) para realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-115">[Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) for making calls to the Microsoft Graph.</span></span>

<span data-ttu-id="6f7b8-116">ExPanda los **scripts Gradle**y, a continuación, abra el archivo **Build. Gradle (Module: app)** .</span><span class="sxs-lookup"><span data-stu-id="6f7b8-116">Expand **Gradle Scripts**, then open the **build.gradle (Module: app)** file.</span></span> <span data-ttu-id="6f7b8-117">Agregue las siguientes líneas dentro del `dependencies` valor.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-117">Add the following lines inside the `dependencies` value.</span></span>

```Gradle
implementation 'com.android.support:design:28.0.0'
implementation 'com.microsoft.graph:microsoft-graph:1.1.+'
implementation 'com.microsoft.identity.client:msal:0.2.+'
```

> [!NOTE]
> <span data-ttu-id="6f7b8-118">Si usa una versión de SDK diferente, asegúrese de cambiar el `28.0.0` para que coincide con la versión de la `com.android.support:appcompat-v7` dependencia que ya está presente en **Build. Gradle**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-118">If you are using a different SDK version, make sure to change the `28.0.0` to match the version of the `com.android.support:appcompat-v7` dependency already present in **build.gradle**.</span></span>

<span data-ttu-id="6f7b8-119">Agregue un `packagingOptions` dentro del `android` valor en el archivo **Build. Gradle (Module: app)** .</span><span class="sxs-lookup"><span data-stu-id="6f7b8-119">Add a `packagingOptions` inside the `android` value in the **build.gradle (Module: app)** file.</span></span>

```Gradle
packagingOptions {
    pickFirst 'META-INF/jersey-module-version'
}
```

<span data-ttu-id="6f7b8-120">Guarde los cambios.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-120">Save your changes.</span></span> <span data-ttu-id="6f7b8-121">En el menú **archivo** , seleccione **sincronizar proyecto con archivos Gradle**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-121">On the **File** menu, select **Sync Project with Gradle Files**.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="6f7b8-122">Diseñar la aplicación</span><span class="sxs-lookup"><span data-stu-id="6f7b8-122">Design the app</span></span>

<span data-ttu-id="6f7b8-123">La aplicación usará un [cajón de navegación](https://developer.android.com/training/implementing-navigation/nav-drawer) para navegar entre distintas vistas.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-123">The application will use a [navigation drawer](https://developer.android.com/training/implementing-navigation/nav-drawer) to navigate between different views.</span></span> <span data-ttu-id="6f7b8-124">En este paso, se actualizará la actividad para usar un diseño de cajón de navegación y agregar fragmentos para las vistas.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-124">In this step you will update the activity to use a navigation drawer layout, and add fragments for the views.</span></span>

### <a name="create-a-navigation-drawer"></a><span data-ttu-id="6f7b8-125">Crear un cajón de navegación</span><span class="sxs-lookup"><span data-stu-id="6f7b8-125">Create a navigation drawer</span></span>

<span data-ttu-id="6f7b8-126">Empiece por crear iconos para el menú de navegación de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-126">Start by creating icons for the app's navigation menu.</span></span> <span data-ttu-id="6f7b8-127">Haga clic con el botón derecho en la carpeta **App/res/drawable** , seleccione **nuevo**y, a continuación, **activo vector**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-127">Right-click the **app/res/drawable** folder and select **New**, then **Vector Asset**.</span></span> <span data-ttu-id="6f7b8-128">Haga clic en el botón de icono situado junto a **imágenes**prediseñadas.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-128">Click the icon button next to **Clip Art**.</span></span> <span data-ttu-id="6f7b8-129">En la ventana **seleccionar icono** , escriba `home` en la barra de búsqueda y, a continuación, seleccione el icono **Inicio** y elija **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-129">In the **Select Icon** window, type `home` in the search bar, then select the **Home** icon and choose **OK**.</span></span> <span data-ttu-id="6f7b8-130">Cambie el **nombre** a `ic_menu_home`.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-130">Change the **Name** to `ic_menu_home`.</span></span>

![Una captura de pantalla de la ventana Configurar activo vectorial](./images/create-icon.png)

<span data-ttu-id="6f7b8-132">Elija **siguiente**y, a continuación, **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-132">Choose **Next**, then **Finish**.</span></span> <span data-ttu-id="6f7b8-133">Repita este paso para crear dos iconos más.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-133">Repeat this step to create two more icons.</span></span>

- <span data-ttu-id="6f7b8-134">Nombre: `ic_menu_calendar`, icono:`event`</span><span class="sxs-lookup"><span data-stu-id="6f7b8-134">Name: `ic_menu_calendar`, Icon: `event`</span></span>
- <span data-ttu-id="6f7b8-135">Nombre: `ic_menu_signout`, icono:`exit to app`</span><span class="sxs-lookup"><span data-stu-id="6f7b8-135">Name: `ic_menu_signout`, Icon: `exit to app`</span></span>
- <span data-ttu-id="6f7b8-136">Nombre: `ic_menu_signin`, icono:`person add`</span><span class="sxs-lookup"><span data-stu-id="6f7b8-136">Name: `ic_menu_signin`, Icon: `person add`</span></span>

<span data-ttu-id="6f7b8-137">A continuación, cree un menú para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-137">Next, create a menu for the application.</span></span> <span data-ttu-id="6f7b8-138">Haga clic con el botón derecho en la carpeta **res** , seleccione **nuevo**y, a continuación, **Directorio de recursos de Android**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-138">Right-click the **res** folder and choose **New**, then **Android Resource Directory**.</span></span> <span data-ttu-id="6f7b8-139">Cambie el **tipo** de recurso `menu` a y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-139">Change the **Resource type** to `menu` and choose **OK**.</span></span>

<span data-ttu-id="6f7b8-140">Haga clic con el botón derecho en la carpeta de **menú** nueva y elija **nuevo**, y **archivo de recursos de menú**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-140">Right-click the new **menu** folder and choose **New**, then **Menu resource file**.</span></span> <span data-ttu-id="6f7b8-141">Asigne un nombre `drawer_menu` al archivo y elija **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-141">Name the file `drawer_menu` and choose **OK**.</span></span> <span data-ttu-id="6f7b8-142">Cuando se abra el archivo, elija la pestaña **texto** para ver el XML y, a continuación, reemplace todo el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-142">When the file opens, choose the **Text** tab to view the XML, then replace the entire contents with the following.</span></span>

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

<span data-ttu-id="6f7b8-143">Ahora, actualice el tema de la aplicación para que sea compatible con un cajón de navegación.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-143">Now update the application's theme to be compatible with a navigation drawer.</span></span> <span data-ttu-id="6f7b8-144">Abra el archivo **App/res/Values/Styles. XML** .</span><span class="sxs-lookup"><span data-stu-id="6f7b8-144">Open the **app/res/values/styles.xml** file.</span></span> <span data-ttu-id="6f7b8-145">Reemplazar `Theme.AppCompat.Light.DarkActionBar` por `Theme.AppCompat.Light.NoActionBar`.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-145">Replace `Theme.AppCompat.Light.DarkActionBar` with `Theme.AppCompat.Light.NoActionBar`.</span></span> <span data-ttu-id="6f7b8-146">A continuación, agregue las siguientes líneas `style` dentro del elemento.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-146">Then add the following lines inside the `style` element.</span></span>

```xml
<item name="windowActionBar">false</item>
<item name="windowNoTitle">true</item>
<item name="android:statusBarColor">@android:color/transparent</item>
```

<span data-ttu-id="6f7b8-147">A continuación, cree un encabezado para el menú.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-147">Next, create a header for the menu.</span></span> <span data-ttu-id="6f7b8-148">Haga clic con el botón secundario en la carpeta **App/res/layout** .</span><span class="sxs-lookup"><span data-stu-id="6f7b8-148">Right-click the **app/res/layout** folder.</span></span> <span data-ttu-id="6f7b8-149">Seleccione **nuevo**y, a continuación, **archivo de recursos de diseño**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-149">Choose **New**, then **Layout resource file**.</span></span> <span data-ttu-id="6f7b8-150">Asigne un nombre `nav_header` al archivo y cambie el **elemento raíz** a `LinearLayout`.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-150">Name the file `nav_header` and change the **Root element** to `LinearLayout`.</span></span> <span data-ttu-id="6f7b8-151">Elija **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-151">Choose **OK**.</span></span>

<span data-ttu-id="6f7b8-152">Abra el archivo **nav_header. XML** y elija la pestaña **texto** . Reemplace todo el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-152">Open the **nav_header.xml** file and choose the **Text** tab. Replace the entire contents with the following.</span></span>

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

<span data-ttu-id="6f7b8-153">Ahora, abra el archivo **App/res/layout/activity_main. XML** .</span><span class="sxs-lookup"><span data-stu-id="6f7b8-153">Now, open the **app/res/layout/activity_main.xml** file.</span></span> <span data-ttu-id="6f7b8-154">Actualice el diseño a un `DrawerLayout` reemplazando el XML existente por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-154">Update the layout to a `DrawerLayout` by replacing the existing XML with the following.</span></span>

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

<span data-ttu-id="6f7b8-155">A continuación, Abra **App/res/Values/Strings. XML**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-155">Next, open **app/res/values/strings.xml**.</span></span> <span data-ttu-id="6f7b8-156">Agregue los siguientes elementos dentro del `resources` elemento.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-156">Add the following elements inside the `resources` element.</span></span>

```xml
<string name="navigation_drawer_open">Open navigation drawer</string>
<string name="navigation_drawer_close">Close navigation drawer</string>
```

<span data-ttu-id="6f7b8-157">Por último, abra el archivo **app/java/com. example/graphtutorial/MainActivity** .</span><span class="sxs-lookup"><span data-stu-id="6f7b8-157">Finally, open the **app/java/com.example/graphtutorial/MainActivity** file.</span></span> <span data-ttu-id="6f7b8-158">Reemplace todo el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-158">Replace the entire contents with the following.</span></span>

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

### <a name="add-fragments"></a><span data-ttu-id="6f7b8-159">Agregar fragmentos</span><span class="sxs-lookup"><span data-stu-id="6f7b8-159">Add fragments</span></span>

<span data-ttu-id="6f7b8-160">Haga clic con el botón secundario en la carpeta **App/res/layout** , seleccione **nuevo**y, a continuación, **archivo de recursos de distribución**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-160">Right-click the **app/res/layout** folder and choose **New**, then **Layout resource file**.</span></span> <span data-ttu-id="6f7b8-161">Asigne un nombre `fragment_home` al archivo y cambie el **elemento raíz** a `RelativeLayout`.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-161">Name the file `fragment_home` and change the **Root element** to `RelativeLayout`.</span></span> <span data-ttu-id="6f7b8-162">Elija **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-162">Choose **OK**.</span></span>

<span data-ttu-id="6f7b8-163">Abra el archivo **fragment_home. XML** y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-163">Open the **fragment_home.xml** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="6f7b8-164">A continuación, haga clic con el botón derecho en la carpeta **App/res/layout** , seleccione **nuevo**y, a continuación, **archivo de recursos de distribución**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-164">Next, right-click the **app/res/layout** folder and choose **New**, then **Layout resource file**.</span></span> <span data-ttu-id="6f7b8-165">Asigne un nombre `fragment_calendar` al archivo y cambie el **elemento raíz** a `RelativeLayout`.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-165">Name the file `fragment_calendar` and change the **Root element** to `RelativeLayout`.</span></span> <span data-ttu-id="6f7b8-166">Elija **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-166">Choose **OK**.</span></span>

<span data-ttu-id="6f7b8-167">Abra el archivo **fragment_calendar. XML** y reemplace su contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-167">Open the **fragment_calendar.xml** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="6f7b8-168">Ahora, haga clic con el botón secundario en la carpeta **app/java/com. example. graphtutorial** y seleccione **nuevo**y, a continuación, **clase Java**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-168">Now, right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="6f7b8-169">Asigne un nombre `HomeFragment` a la clase y establezca `android.support.v4.app.Fragment`la **superclase** en.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-169">Name the class `HomeFragment` and set the **Superclass** to `android.support.v4.app.Fragment`.</span></span> <span data-ttu-id="6f7b8-170">Elija **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-170">Choose **OK**.</span></span> <span data-ttu-id="6f7b8-171">Abra el archivo **HomeFragment** y reemplace el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-171">Open the **HomeFragment** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="6f7b8-172">A continuación, haga clic con el botón derecho en la carpeta **app/java/com. example. graphtutorial** y seleccione **nuevo**y, a continuación, **clase Java**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-172">Next, right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="6f7b8-173">Asigne un nombre `CalendarFragment` a la clase y establezca `android.support.v4.app.Fragment`la **superclase** en.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-173">Name the class `CalendarFragment` and set the **Superclass** to `android.support.v4.app.Fragment`.</span></span> <span data-ttu-id="6f7b8-174">Elija **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-174">Choose **OK**.</span></span>

<span data-ttu-id="6f7b8-175">Abra el archivo **CalendarFragment** y agregue la siguiente función a la `CalendarFragment` clase.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-175">Open the **CalendarFragment** file and add the following function to the `CalendarFragment` class.</span></span>

```java
@Nullable
@Override
public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
    return inflater.inflate(R.layout.fragment_calendar, container, false);
}
```

<span data-ttu-id="6f7b8-176">Ahora que los fragmentos se han implementado, `MainActivity` actualice la clase para `onNavigationItemSelected` controlar el evento y use los fragmentos.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-176">Now that the fragments are implemented, update the `MainActivity` class to handle the `onNavigationItemSelected` event and use the fragments.</span></span> <span data-ttu-id="6f7b8-177">En primer lugar, agregue las siguientes funciones a la clase.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-177">First, add the following functions to the class.</span></span>

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

<span data-ttu-id="6f7b8-178">A continuación, reemplace la `onNavigationItemSelected` función existente por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-178">Next, replace the existing `onNavigationItemSelected` function with the following.</span></span>

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

<span data-ttu-id="6f7b8-179">Por último, agregue lo siguiente al final de la `onCreate` función para cargar el fragmento de inicio cuando se inicie la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-179">Finally, add the following at the end of the `onCreate` function to load the home fragment when the app starts.</span></span>

```java
// Load the home fragment by default on startup
if (savedInstanceState == null) {
    openHomeFragment(mUserName);
}
```

<span data-ttu-id="6f7b8-180">Guarde todos los cambios.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-180">Save all of your changes.</span></span> <span data-ttu-id="6f7b8-181">En el menú **Ejecutar** , elija **Ejecutar "aplicación"**.</span><span class="sxs-lookup"><span data-stu-id="6f7b8-181">On the **Run** menu, choose **Run 'app'**.</span></span> <span data-ttu-id="6f7b8-182">El menú de la aplicación debe funcionar para navegar entre los dos fragmentos y cambiar cuando Puntee **en los botones iniciar sesión** o **Cerrar sesión** .</span><span class="sxs-lookup"><span data-stu-id="6f7b8-182">The app's menu should work to navigate between the two fragments and change when you tap the **Sign in** or **Sign out** buttons.</span></span>

![Captura de pantalla de la aplicación](./images/welcome-page.png)