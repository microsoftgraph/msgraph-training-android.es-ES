# <a name="completed-module-add-azure-ad-authentication"></a>Módulo completado: agregar autenticación de Azure AD

La versión del proyecto en este directorio refleja cómo completar el tutorial hasta [Agregar autenticación de Azure ad](https://docs.microsoft.com/graph/tutorials/android?tutorial-step=3). Si usa esta versión del proyecto, deberá completar el resto del tutorial a partir de [obtener datos de calendario](https://docs.microsoft.com/graph/tutorials/android?tutorial-step=4).

> **Nota:** Se supone que ya ha registrado una aplicación en Azure portal como se especifica en [registrar la aplicación en el portal](https://docs.microsoft.com/graph/tutorials/android?tutorial-step=2). Debe configurar esta versión del ejemplo de la siguiente manera:
>
> 1. Cambie el nombre `./GraphTutorial/oauth_strings.xml.example` del archivo `oauth_strings.xml`a.
> 1. Mueva el `oauth_strings.xml` archivo al `./GraphTutorial/app/src/main/res/values` directorio.
> 1. Edite `oauth_strings.xml` el archivo y realice los cambios siguientes.
>     1. Reemplace `YOUR_APP_ID_HERE` por el **identificador de aplicación** que obtuvo desde el portal de Azure.