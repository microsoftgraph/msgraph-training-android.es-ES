# <a name="how-to-run-the-completed-project"></a>Cómo ejecutar el proyecto completado

## <a name="prerequisites"></a>Requisitos previos

Para ejecutar el proyecto completado en esta carpeta, necesita lo siguiente:

- [Android Studio instalado](https://developer.android.com/studio/) en el equipo de desarrollo. (**Nota:** Este tutorial se escribió con Android Studio versión 3.6.2 y el SDK de Android 10.0. Los pasos de esta guía pueden funcionar con otras versiones, pero no se han probado).
- Ya sea una cuenta personal de Microsoft con un buzón en Outlook.com o una cuenta de Microsoft para trabajo o escuela.

Si no tienes una cuenta de Microsoft, hay un par de opciones para obtener una cuenta gratuita:

- Puede registrarse [para obtener una nueva cuenta personal de Microsoft.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)
- Puede registrarse en el Programa de desarrolladores de [Office 365](https://developer.microsoft.com/office/dev-program) para obtener una suscripción gratuita a Office 365.

## <a name="register-an-application-with-the-azure-portal"></a>Registrar una aplicación con Azure Portal

1. Abra un explorador y vaya al [centro de administración de Azure Active Directory](https://aad.portal.azure.com) e inicie sesión con una **cuenta personal** (también conocida como: cuenta Microsoft) o una **cuenta profesional o educativa**.

1. Seleccione **Azure Active Directory** en el panel de navegación izquierdo y, a continuación, seleccione **Registros de aplicaciones** en **Administrar**.

    ![Captura de pantalla de los registros de aplicaciones ](../../tutorial/images/aad-portal-app-registrations.png)

1. Seleccione **Nuevo registro**. En la página **Registrar una aplicación**, establezca los valores siguientes.

    - Establezca **Nombre** como `Android Graph Tutorial`.
    - Establezca **Tipos de cuenta admitidos** en **Cuentas en cualquier directorio de organización y cuentas personales de Microsoft**.
    - En **URI de** redireccionamiento, establezca el desplegable en Cliente **público/nativo (escritorio móvil &)** y establezca el valor en , reemplazando con el nombre del paquete del `msauth://YOUR_PACKAGE_NAME/callback` `YOUR_PACKAGE_NAME` proyecto.

    ![Captura de pantalla de la página Registrar una aplicación](../../tutorial/images/aad-register-an-app.png)

1. Seleccione **Registrar**. En la **página tutorial de Android Graph,** copie el valor del identificador de aplicación **(cliente)** y guárdelo, lo necesitará en el paso siguiente.

    ![Captura de pantalla del identificador de aplicación del nuevo registro de la aplicación](../../tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a>Configuración del ejemplo

1. Cambie el `msal_config.json.example` nombre del archivo y `msal_config.json` muévelo al `GraphTutorial/app/src/main/res/raw` directorio.
1. Edite `msal_config.json` el archivo y realice los siguientes cambios.
    1. Reemplace `YOUR_APP_ID_HERE` por el **id. de** aplicación que obtuvo de Azure Portal.
    1. Reemplaza `com.example.graphtutorial` por el nombre del paquete.

## <a name="run-the-sample"></a>Ejecutar el ejemplo

En Android Studio, selecciona **Ejecutar "aplicación"** en el **menú** Ejecutar.
