---
title: Introducción a la interfaz de usuario de iOS 7
description: iOS 7 introduce una gran cantidad de cambios en la interfaz de usuario. En este artículo se resaltan algunos de los cambios más grandes, tanto en la apariencia visual de los controles como en las API que admiten el nuevo diseño.
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 4731be58c1fadae0bba6768570ecfd181b071dd2
ms.sourcegitcommit: ce4670de51e24116a944c778ee64585bd0aae0e1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2020
ms.locfileid: "79088962"
---
# <a name="ios-7-user-interface-overview"></a>Introducción a la interfaz de usuario de iOS 7

_iOS 7 introduce una gran cantidad de cambios en la interfaz de usuario. En este artículo se resaltan algunos de los cambios más grandes, tanto en la apariencia visual de los controles como en las API que admiten el nuevo diseño._

iOS 7 se centra en el contenido sobre Chrome. Los elementos de la interfaz de usuario de iOS 7 desenfatizan Chrome quitando atributos como bordes extraños, barras de estado y barras de navegación, lo que reduce la cantidad de espacio de pantalla que usan las vistas de contenido. En iOS 7, el contenido está diseñado para usar toda la pantalla.

iOS 7 introduce otros cambios: el color se usa para distinguir los elementos de la interfaz de usuario, en lugar de los atributos, como los bordes de los botones. Muchos elementos, como las barras de navegación y las barras de estado, ahora están desenfocados, translúcidos o transparentes, con vistas de contenido que tienen áreas debajo. Estas vistas de contenido se representan a través de barras borrosas, lo que transmite una sensación de profundidad en la interfaz de usuario.

En este artículo se tratan algunos de los cambios en los elementos de la interfaz de usuario de iOS 7, así como varias API relacionadas con el nuevo diseño de la interfaz de usuario.

## <a name="view-and-control-changes"></a>Ver y controlar los cambios

Todas las vistas de UIKit se ajustan a la nueva apariencia de iOS 7. En esta sección se resaltan algunos de los cambios en estas vistas, así como las API relacionadas que han cambiado para admitir la nueva interfaz de usuario.

### <a name="uibutton"></a>UIButton

Los botones creados a partir de la clase `UIButton` ahora no tienen bordes, sin fondo de forma predeterminada, como se muestra a continuación:

 ![](ios7-ui-images/button.png "Sample UIButton")

El estilo de `UIButtonType.RoundedRect` está desusado. Si se usa en iOS 7, `UIButtonType.RoundedRect` producirá `UIButtonType.System` se usará, lo que genera el estilo de botón predeterminado sin bordes de fondo o visibles, como se muestra anteriormente.

### <a name="uibarbuttonitem"></a>UIBarButtonItem

De forma similar a `UIButton`, los botones de barra también tienen bordes y tienen como valor predeterminado el nuevo estilo de `UIBarButtonItemStyle.Plain` que se muestra a continuación:

 ![](ios7-ui-images/barbuttonplain.png "Sample UIBarButtonItem")

Además, el estilo de `UIBarButtonItemStyle.Bordered` está en desuso. Si se establece `UIBarButtonItemStyle.Bordered` en iOS 7, se usará el estilo de `UIBarButtonItemStyle.Plain`.

El estilo de `UIBarButtonItemStyle.Done` no está en desuso. No obstante, también creará un botón sin borde, solo con un estilo de texto en negrita, como se muestra a continuación:

 ![](ios7-ui-images/barbuttondone.png "Sample UIBarButtonItem in the Done style")

### <a name="uialertview"></a>UIAlertView

Además del cambio de estilo para la nueva apariencia de iOS 7, las vistas de alertas ya no admiten la personalización a través de la subvista. Aunque `UIAlertView` hereda de `UIView`, llamar a `AddSubview` en un `UIAlertView` no tiene ningún efecto. Por ejemplo, considere el siguiente código:

```csharp
UIBarButtonItem button = new UIBarButtonItem ("Bar Button", UIBarButtonItemStyle.Plain, (s,e) =>
{
    UIAlertView alert = new UIAlertView ("Title", "Message", null, "Cancel", "OK");

    alert.AddSubview (new UIView () {
        Frame = new CGRect(50, 50,100, 100),
        BackgroundColor = UIColor.Green
    });

    alert.Show ();
});
```

Esto genera una vista de alertas estándar, con la subvista omitida, como se muestra a continuación:

 ![](ios7-ui-images/alert.png "Sample UIAlertView")

 Nota: UIAlertView quedó en desuso en iOS 8. Vea la receta del [controlador de alertas](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller) sobre el uso de una vista de alertas en iOS 8 y versiones posteriores.

### <a name="uisegmentedcontrol"></a>UISegmentedControl

Los controles segmentados en iOS 7 son transparentes y admiten el color de matiz. El color del matiz se usa para el color del texto y del borde. Cuando se selecciona un segmento, el color se intercambia entre el fondo y el texto, con el color de matiz usado para resaltar el segmento seleccionado, como se muestra a continuación:

 ![](ios7-ui-images/segmentedcontrol.png "Sample UISegmentedControl")

Además, el `UISegmentedControlStyle` ha quedado en desuso en iOS 7.

### <a name="picker-views"></a>Vistas del selector

La API para las vistas de selector es, en gran medida, sin cambios. sin embargo, las instrucciones de diseño de iOS 7 ahora las vistas de selector de estado deben presentarse en línea en lugar de como vistas de entrada animadas desde la parte inferior de la pantalla o a través de un nuevo controlador insertado en la pila de un controlador de navegación, como en versiones anteriores de iOS. Esto puede verse en la aplicación de calendario del sistema:

 ![](ios7-ui-images/inlinepicker.png "This can be seen in the system calendar app")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

La barra de búsqueda se muestra ahora dentro de la barra de navegación cuando la propiedad `UISearchDisplayController.DisplaysSearchBarInNavigationBar` está establecida en true. Cuando se establece en false (valor predeterminado), la barra de navegación se oculta cuando se muestra el controlador de búsqueda.

En la captura de pantalla siguiente se muestra la barra de búsqueda dentro de un `UISearchDisplayController`:

 ![](ios7-ui-images/searchbar.png "Sample UISearchDisplayController")

### <a name="uitableview"></a>UITableView

Las API en torno a `UITableView` son principalmente inalteradas; sin embargo, el estilo ha cambiado drásticamente para ajustarse al nuevo diseño de la interfaz de usuario. La jerarquía de vistas interna también es algo diferente. Este cambio no afectará a la mayoría de las aplicaciones, pero es algo que debe tener en cuenta.

#### <a name="grouped-table-style"></a>Estilo de tabla agrupada

El estilo agrupado cambiado se ha actualizado y ahora el contenido se extiende a los bordes de la pantalla, como se muestra a continuación:

 ![](ios7-ui-images/table1.png "Sample Grouped Table Style")

#### <a name="separatorinset"></a>SeparatorInset

Ahora se puede aplicar sangría a los separadores de fila estableciendo la propiedad `UITableVIewCell.SeparatorInset`. Por ejemplo, el código siguiente se utilizaría para aplicar sangría a las celdas del borde izquierdo:

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

Esto genera en la vista de tabla con celdas con sangría, como se muestra a continuación:

 ![](ios7-ui-images/separatorinset.png "Sample UITableView SeparatorInset")

#### <a name="table-button-styles"></a>Estilos de botón de tabla

Los distintos botones utilizados en las vistas de tabla han cambiado. En la captura de pantalla siguiente se muestra una vista de tabla en el modo de edición:

 ![](ios7-ui-images/table2.png "This screenshot presents a table view in editing mode")

### <a name="additional-control-changes"></a>Cambios de control adicionales

También han cambiado otros controles UIKit, incluidos los controles deslizantes, los modificadores y los steppers. Estos cambios son meramente visuales. Para más información, consulte la guía de [transición de la interfaz de usuario de iOS 7](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html)de Apple.

## <a name="general-user-interface-changes"></a>Cambios generales de la interfaz de usuario

Además de los cambios en UIKit, iOS 7 introduce una variedad de cambios visuales en la interfaz de usuario, entre los que se incluyen:

- Contenido de pantalla completa
- Apariencia de la barra
- Color de matiz

<a name="fullscreen" />

### <a name="full-screen-content"></a>Contenido de pantalla completa

iOS 7 está diseñado para permitir que las aplicaciones aprovechen toda la pantalla. Los controladores de vista ahora aparecen superpuestos por una barra de estado y una barra de navegación (si existe), en lugar de aparecer debajo de las barras de estado y de navegación.

Cuando prepare la aplicación para iOS 7, puede realinear las subvistas visualmente con *Interface Builder* o con *Xamarin iOS Designer*. También puede usar una de las nuevas API para ayudar a controlar mediante programación el contenido de pantalla completa. Estas API se presentan a continuación.

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide y BottomLayoutGuide

 `TopLayoutGuide` y `BottomLayoutGuide` sirven como referencia para la ubicación de inicio o finalización de las vistas, de modo que el contenido no se superponga con una barra de `UIKit` translúcida, como en el ejemplo siguiente:

 [![](ios7-ui-images/clipped.png "Sample content not overlapped by a translucent UIKit bar")](ios7-ui-images/clipped.png#lightbox)

Estas API se pueden usar para calcular el desplazamiento de una vista desde la parte superior o inferior de la pantalla y ajustar la ubicación del contenido según corresponda:

```csharp
public override void ViewDidLayoutSubviews ()
{
    base.ViewDidLayoutSubviews ();

    if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
        nfloat displacement_y = this.TopLayoutGuide.Length;

        //load subviews with displacement
    }

}
```

Podemos usar el valor calculado anteriormente para establecer el desplazamiento de `ImageView`desde la parte superior de la pantalla, por lo que toda la imagen es visible:

 [![](ios7-ui-images/good2.png "Example ImageViews displacement from the top of the screen")](ios7-ui-images/good2.png#lightbox)

Consulte [ImageViewer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates/) para obtener un ejemplo práctico.

El valor de desplazamiento se genera dinámicamente después de que se haya agregado la vista a la jerarquía, por lo que si se intenta leer `TopLayoutGuide` y `BottomLayoutGuide` valores en `ViewDidLoad`, devolverá 0. Calcule el valor después de que se haya cargado la vista, por ejemplo, en el `ViewDidLayoutSubviews`.

> [!IMPORTANT]
> `TopLayoutGuide` y `BottomLayoutGuide` están en desuso en iOS 11 en favor del nuevo diseño de área segura. Apple ha indicado que el uso del área segura es compatible con la versión de iOS anterior a iOS 11. Para obtener más información, consulte la guía [actualización de la aplicación para iOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen) .

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

Esta API especifica qué bordes de una vista se deben extender a la pantalla completa, independientemente de la barra translucidez. En iOS 7, las barras de navegación y las barras de herramientas aparecen superpuestas por encima de la vista del controlador, a diferencia de las versiones anteriores de iOS, donde no ocupaban el mismo espacio. La aplicación iOS 7 photos muestra el valor predeterminado de `UIViewController.EdgesForExtendedLayout`, `UIRectEdge.All`. Esta configuración rellena los cuatro bordes de la vista con contenido, creando el efecto de superposición y de pantalla completa:

 [![](ios7-ui-images/photos.png "Sample EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

Al puntear en la imagen se quitan las barras y se muestra la pantalla completa de la imagen:

 [![](ios7-ui-images/photos2.png "EdgesForExtendedLayout with the bars removed")](ios7-ui-images/photos2.png#lightbox)

Dado que el contenido de pantalla completa es el predeterminado, las aplicaciones configuradas para iOS 6 tendrán parte de la vista recortada, como se muestra en la captura de pantalla siguiente:

 [![](ios7-ui-images/clipped.png "Apps configured for iOS 6 will have part of the view clipped, as in this screenshot")](ios7-ui-images/clipped.png#lightbox)

La modificación de la propiedad `UIViewController.EdgesForExtendedLayout` se ajusta para este comportamiento. Podemos especificar que la vista no rellene los bordes, por lo que nuestra vista evitará mostrar el contenido en el espacio ocupado por la navegación o las barras de herramientas (en cada orientación):

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

En nuestra aplicación, veremos que la vista se vuelve a colocar, por lo que toda la imagen está visible:

 [![](ios7-ui-images/good.png "Example with whole image visible")](ios7-ui-images/good.png#lightbox)

Tenga en cuenta que, aunque los efectos de las API `TopLayoutGuide/BottomLayoutGuide` y `EdgesForExtendedLayout` son similares, están diseñados para rellenar diferentes objetivos. Cambiar el valor de `EdgesForExtendedLayout` del valor predeterminado puede corregir vistas recortadas en las aplicaciones diseñadas para iOS 6, pero un buen diseño de iOS 7 debe respetar el aspecto de la pantalla completa y ofrecer una experiencia de visualización de pantalla completa, confiando en `TopLayoutGuide` y `BottomLayoutGuide` para colocar correctamente el contenido que está diseñado para ser manipulado en un lugar cómodo para el usuario.

Consulte [ImageViewer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates/) para obtener un ejemplo práctico.

### <a name="status-and-navigation-bars"></a>Barras de estado y navegación

La barra de estado y las barras de navegación se representan con transparencia. Las barras de estado son transparentes, mientras que las barras de herramientas y las barras de navegación son translúcidas y borrosas para transmitir la sensación de profundidad en la interfaz de usuario. En la captura de pantalla siguiente se muestra este desenfoque y transparencia, donde el color de fondo azul de la vista de colección se muestra a través de las barras de estado y de navegación, lo que les proporciona una apariencia de azul claro:

 ![](ios7-ui-images/transparent-navbar.png "Sample Status and Navigation Bar blurring")

#### <a name="status-bar-styles"></a>Estilos de barra de estado

Junto con el desenfoque y la transparencia, el primer plano de una barra de estado puede ser claro o oscuro (el valor predeterminado es oscuro). El estilo de la barra de estado se puede establecer desde el controlador de vista. Un controlador de vista también puede establecer si la barra de estado está oculta o se muestra.

Por ejemplo, el código siguiente invalida el método `PreferredStatusBarStyle` de un controlador de vista para que la barra de estado muestre un primer plano claro:

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

Esto hace que la barra de estado aparezca como se muestra a continuación:

 ![](ios7-ui-images/light-status-bar.png "Sample Status Bar")

Para ocultar la barra de estado del código del controlador de vista, invalide `PrefersStatusBarHidden`, como se muestra a continuación:

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

Esto oculta la barra de estado:

 ![](ios7-ui-images/status-bar-hidden.png "Status Bar hidden")

### <a name="tint-color"></a>Color de matiz

Los botones ahora se muestran como texto sin Chrome. El color del texto se puede controlar mediante la nueva propiedad `TintColor` en `UIView`. Al establecer el `TintColor` se aplica el color a toda la jerarquía de vistas para la vista que lo establece. Para aplicar un `TintColor`a toda una aplicación, establézcalo en el `Window`. También puede detectar cuándo cambia el color del matiz a través del método `UIView.TintColorDidChange`.

Por ejemplo, en la siguiente captura de pantalla se muestra el efecto de cambiar el color del matiz en la vista de un controlador de navegación a púrpura:

 ![](ios7-ui-images/tint-color.png "Purple tint color on a navigation controllers view")

También se puede aplicar el color de matiz a las imágenes cuando el `RenderingMode` está establecido en `UIImageRenderingMode.AlwaysTemplate`.

> [!IMPORTANT]
> No se puede establecer el color del matiz mediante `UIAppearance`.

### <a name="dynamic-type"></a>Tipo dinámico

En iOS 7, el usuario puede especificar el tamaño del texto en la configuración del sistema. Con el tipo dinámico, la fuente se ajusta dinámicamente para que tenga un aspecto correcto independientemente del tamaño. `UIFont.PreferredFontForTextStyle` debe usarse para obtener una fuente optimizada para el tamaño controlado por el usuario.

## <a name="summary"></a>Resumen

En este artículo se describen los cambios en los elementos de la interfaz de usuario de iOS 7. Examina algunos de los cambios realizados en las vistas y los controles en UIKit, resaltando tanto los cambios visuales como los cambios en las API relacionadas. Por último, presenta nuevas API para trabajar con el contenido de pantalla completa, la nueva compatibilidad con el color de matiz y el tipo dinámico.

## <a name="related-links"></a>Vínculos relacionados

- [ImageViewer (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates)
