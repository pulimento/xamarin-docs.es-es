---
title: Mostrar elementos emergentes
description: 'Xamarin. Forms proporciona tres elementos de interfaz de usuario de tipo emergente: una alerta, una hoja de acción y un símbolo del sistema. En este artículo se muestra el uso de alertas, hojas de acción y API de mensajes para mostrar cuadros de diálogo que piden a los usuarios preguntas sencillas, guiar a los usuarios a través de tareas y mostrar mensajes.'
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/17/2020
ms.openlocfilehash: c71153cdaa94a7983b89968abc828011a648f2b1
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306630"
---
# <a name="display-pop-ups"></a>Mostrar elementos emergentes

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)

Mostrar una alerta, pedir al usuario que elija una opción o mostrar un mensaje es una tarea común de la interfaz de usuario. Xamarin. Forms tiene tres métodos en la clase [`Page`](xref:Xamarin.Forms.Page) para interactuar con el usuario a través de un elemento emergente: [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*), [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*)y `DisplayPromptAsync`. Se representan con controles nativos adecuados en cada plataforma.

## <a name="display-an-alert"></a>Visualización de una alerta

Todas las plataformas compatibles con Xamarin.Forms tienen una ventana emergente modal para mostrar alertas al usuario o realizar preguntas sencillas. Para mostrar estas alertas en Xamarin.Forms, use el método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) en cualquier elemento [`Page`](xref:Xamarin.Forms.Page). En la siguiente línea de código, se muestra un mensaje sencillo al usuario:

```csharp
await DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Alert Dialog with One Button")

En este ejemplo, no se recopila información del usuario. La alerta se muestra modalmente y, después de cerrarla, el usuario sigue interactuando con la aplicación.

El método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) también se puede usar para capturar la respuesta de un usuario mediante la presentación de dos botones y la devolución de un elemento `boolean`. Para obtener una respuesta de una alerta, agregue texto para los dos botones y use `await` para esperar el método. Cuando el usuario seleccione una de las opciones, se devolverá la respuesta al código. Observe las palabras clave `async` y `await` en el código de ejemplo siguiente:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "Cuadro de diálogo de alerta con dos botones")](pop-ups-images/alert2.png#lightbox "Cuadro de diálogo de alerta con dos botones")

## <a name="guide-users-through-tasks"></a>Guiar a los usuarios a través de tareas

El elemento [UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) es un elemento de interfaz de usuario común en iOS. El método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) de Xamarin.Forms le permite incluir este control en aplicaciones multiplataforma, lo que representa alternativas nativas en Android y UWP.

Para mostrar una hoja de acción, `await` [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) en cualquier [`Page`](xref:Xamarin.Forms.Page), pasando las etiquetas de mensaje y de botón como cadenas. Este método devuelve la etiqueta de cadena del botón en el que hizo clic el usuario. A continuación, se muestra un ejemplo sencillo:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "ActionSheet Dialog")

El botón `destroy` se representa de forma distinta y puede dejarse como `null`, o bien se puede especificar como el tercer parámetro de cadena. En el ejemplo siguiente, se usa el botón `destroy`:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "Cuadro de diálogo de hoja de acción con el botón destruir")](pop-ups-images/action2.png#lightbox "Cuadro de diálogo de hoja de acción con el botón destruir")

## <a name="display-a-prompt"></a>Mostrar un mensaje

Para mostrar un símbolo del sistema, llame al `DisplayPromptAsync` en cualquier [`Page`](xref:Xamarin.Forms.Page), pasando un título y un mensaje como argumentos `string`:

```csharp
string result = await DisplayPromptAsync("Question 1", "What's your name?");
```

El mensaje se muestra de forma modal:

[![Captura de pantalla de un símbolo del sistema modal, en iOS y Android](pop-ups-images/simple-prompt.png "Símbolo del sistema modal")](pop-ups-images/simple-prompt-large.png#lightbox "Símbolo del sistema modal")

Si se puntea el botón Aceptar, se devuelve la respuesta especificada como `string`. Si se puntea el botón Cancelar, se devuelve `null`.

La lista de argumentos completa para el método `DisplayPromptAsync` es:

- `title`, de tipo `string`, es el título que se va a mostrar en el símbolo del sistema.
- `message`, de tipo `string`, es el mensaje que se va a mostrar en el símbolo del sistema.
- `accept`, de tipo `string`, es el texto del botón Aceptar. Este argumento es opcional, cuyo valor predeterminado es OK.
- `cancel`, de tipo `string`, es el texto del botón Cancelar. Es un argumento opcional, cuyo valor predeterminado es cancelar.
- `placeholder`, de tipo `string`, es el texto del marcador de posición que se va a mostrar en el símbolo del sistema. Se trata de un argumento opcional, cuyo valor predeterminado es `null`.
- `maxLength`, de tipo `int`, es la longitud máxima de la respuesta del usuario. Este argumento es opcional, cuyo valor predeterminado es-1.
- `keyboard`, de tipo `Keyboard`, es el tipo de teclado que se va a utilizar para la respuesta del usuario. Se trata de un argumento opcional, cuyo valor predeterminado es `Keyboard.Default`.
- `initialValue`, de tipo `string`, es una respuesta predefinida que se mostrará y que se puede editar. Es un argumento opcional, cuyo valor predeterminado es un `string`vacío.

En el ejemplo siguiente se muestra cómo establecer algunos de los argumentos opcionales:

```csharp
string result = await DisplayPromptAsync("Question 2", "What's 5 + 5?", initialValue: "10", maxLength: 2, keyboard: Keyboard.Numeric);
```

Este código muestra una respuesta predefinida de 10, limita el número de caracteres que se pueden introducir en 2 y muestra el teclado numérico para la entrada del usuario:

[![Captura de pantalla de un símbolo del sistema modal, en iOS y Android](pop-ups-images/keyboard-prompt.png "Símbolo del sistema modal")](pop-ups-images/keyboard-prompt-large.png#lightbox "Símbolo del sistema modal")

> [!NOTE]
> El método de `DisplayPromptAsync` solo se implementa actualmente en iOS y Android.

## <a name="related-links"></a>Vínculos relacionados

- [PopupsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)
