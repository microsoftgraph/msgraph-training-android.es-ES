# <a name="how-to-run-the-completed-project"></a>Cómo ejecutar el proyecto completado

## <a name="prerequisites"></a>Requisitos previos

Para ejecutar el proyecto completado en esta carpeta, necesita lo siguiente:

- [Android Studio](https://developer.android.com/studio/) instalado en el equipo de desarrollo. (**Nota:** este tutorial se ha redactado con Android Studio versión 3.3.1 con 1.8.0 JRE y el SDK de Android 9,0. Los pasos de esta guía pueden funcionar con otras versiones, pero no se han probado.
- Una cuenta de Microsoft personal con un buzón de correo en Outlook.com o una cuenta profesional o educativa de Microsoft.

Si no tiene una cuenta de Microsoft, hay un par de opciones para obtener una cuenta gratuita:

- Puede [registrarse para obtener una nueva cuenta Microsoft personal](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Puede [registrarse para el programa de desarrolladores de office 365](https://developer.microsoft.com/office/dev-program) para obtener una suscripción gratuita a Office 365.

## <a name="register-an-application-with-the-azure-portal"></a>Registrar una aplicación con Azure portal

1. Abra un explorador y vaya al [centro de administración de Azure Active Directory](https://aad.portal.azure.com) e inicie sesión con una **cuenta personal** (también conocida como: cuenta Microsoft) o una **cuenta profesional o educativa**.

1. Seleccione **Azure Active Directory** en el panel de navegación de la izquierda y, después, seleccione **registros de aplicaciones** en **administrar**.

    ![Una captura de pantalla de los registros de la aplicación ](../../tutorial/images/aad-portal-app-registrations.png)

1. Seleccione **Nuevo registro**. En la página **Registrar una aplicación**, establezca los valores siguientes.

    - Establezca **Nombre** como `Android Graph Tutorial`.
    - Establezca **Tipos de cuenta admitidos** en **Cuentas en cualquier directorio de organización y cuentas personales de Microsoft**.
    - Deje **URI de redireccionamiento** vacía.

    ![Captura de pantalla de la página registrar una aplicación](../../tutorial/images/aad-register-an-app.png)

1. Elija **Registrar**. En la página **tutorial de Xamarin Graph** , copie el valor del **identificador de la aplicación (cliente)** y guárdelo, lo necesitará en el paso siguiente.

    ![Captura de pantalla del identificador de la aplicación del nuevo registro de la aplicación](../../tutorial/images/aad-application-id.png)

1. Seleccione el vínculo **Agregar un URI de** redireccionamiento. En la página **URI** de redireccionamiento, busque la sección **URI de redireccionamiento sugeridos para clientes públicos (móvil, escritorio)** . Seleccione el URI que comience con `msal` y cópielo y, después, elija **Guardar**. Guarde el URI de redireccionamiento copiado y lo necesitará en el paso siguiente.

    ![Captura de pantalla de la página URI de redireccionamiento](../../tutorial/images/aad-redirect-uris.png)

## <a name="run-the-sample"></a>Ejecutar el ejemplo

En Android Studio, elija **Ejecutar "aplicación"** en el menú **Ejecutar** .