---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: Ciclo de vida de la aplicación de WPF y Xamarin. Forms
description: En este documento se comparan las similitudes y diferencias entre el ciclo de vida de la aplicación para las aplicaciones de Xamarin. Forms y WPF. También examina el árbol visual, los gráficos, los recursos y los estilos.
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: 725af8aebb111ac620a7d1ca5eebf73f31ee4a5e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016472"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>Ciclo de vida de la aplicación de WPF y Xamarin. Forms

Xamarin. Forms tiene una gran cantidad de instrucciones de diseño de los marcos de trabajo basados en XAML que venían antes, en especial WPF. Sin embargo, de otras maneras se desvía significativamente, lo que puede ser un punto de control para los usuarios que intentan migrar. En este documento se intenta identificar algunos de estos problemas y se proporcionan instrucciones siempre que sea posible para enlazar el conocimiento de WPF a Xamarin. Forms.

## <a name="app-lifecycle"></a>Ciclo de vida de la aplicación

El ciclo de vida de la aplicación entre WPF y Xamarin. Forms es similar. Ambos se inician en código externo (plataforma) e inician la interfaz de usuario a través de una llamada al método. La diferencia es que Xamarin. Forms siempre se inicia en un ensamblado específico de la plataforma que, a continuación, inicializa y crea la interfaz de usuario para la aplicación.

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> De forma predeterminada, el método `Main` es generado automáticamente y no es visible en el código.

**Xamarin.Forms**

- `Main method > AppDelegate > App > ContentPage` de &ndash; de **iOS**
- `MainActivity > App > ContentPage` de &ndash; de **Android**
- `Main method > App(UWP) > MainPage(UWP) > App > ContentPage` de &ndash; de **UWP**

### <a name="application-class"></a>Clase de aplicación

Tanto WPF como Xamarin. Forms tienen una clase `Application` que se crea como singleton. En la mayoría de los casos, las aplicaciones se derivarán de esta clase para proporcionar una aplicación personalizada, aunque esto no es estrictamente necesario en WPF. Ambos exponen una propiedad `Application.Current` para buscar el singleton creado.

### <a name="global-properties--persistence"></a>Propiedades globales + persistencia

Tanto WPF como Xamarin. Forms tienen un diccionario de `Application.Properties` disponible donde puede almacenar objetos globales de nivel de aplicación que son accesibles en cualquier parte de la aplicación. La diferencia clave es que Xamarin. Forms _conservará_ todos los tipos primitivos almacenados en la colección cuando la aplicación se suspenda y los recargará cuando se vuelva a iniciar. WPF no admite automáticamente ese comportamiento; en su lugar, la mayoría de los desarrolladores confiaban en el almacenamiento aislado o usaban la compatibilidad integrada de `Settings`.

## <a name="defining-pages-and-the-visual-tree"></a>Definir páginas y el árbol visual

WPF utiliza el `Window` como elemento raíz para cualquier elemento visual de nivel superior. Esto define un HWND en el mundo de Windows para mostrar información. Puede crear y mostrar tantas ventanas como desee en WPF.

En Xamarin. Forms, el visual de nivel superior siempre está definido por la plataforma; por ejemplo, en iOS, es un `UIWindow`. Xamarin. Forms representa su contenido en estas representaciones de plataforma nativas mediante una clase `Page`. Cada `Page` de Xamarin. Forms representa una "página" única de la aplicación, donde solo una es visible cada vez.

Tanto WPFs `Window` como Xamarin. Forms `Page` incluyen una propiedad `Title` para influir en el título mostrado y ambos tienen una propiedad `Icon` para mostrar un icono específico de la página (**tenga en cuenta** que el título y el icono no están siempre visibles en Xamarin. Forms). ). Además, puede cambiar las propiedades visuales comunes en, como el color de fondo o la imagen.

Es técnicamente posible representar dos vistas de plataforma independientes (por ejemplo, definir dos objetos de `UIWindow` y hacer que el segundo se represente en una pantalla externa o AirPlay), requiere código específico de la plataforma para hacerlo y no es una característica directamente admitida de Xamarin. Forms.

### <a name="views"></a>Vistas

La jerarquía visual de ambos marcos es similar. WPF es un poco más profundo debido a su compatibilidad con documentos WYSIWYG.

**WPF**

```
DependencyObject - base class for all bindable things
   Visual - rendering mechanics
      UIElement - common events + interactions
         FrameworkElement - adds layout
            Shape - 2D graphics
            Control - interactive controls
```

**Xamarin.Forms**

```
BindableObject - base class for all bindable things
   Element - basic parent/child support + resources + effects
      VisualElement - adds visual rendering properties (color, fonts, transforms, etc.)
         View - layout + gesture support
```

### <a name="view-lifecycle"></a>Ciclo de vida de las vistas

Xamarin. Forms está orientado principalmente a escenarios móviles. Como tal, las aplicaciones se _activan_, _suspenden_y _reactivan_ a medida que el usuario interactúa con ellas. Esto es similar a hacer clic fuera del `Window` en una aplicación WPF y hay un conjunto de métodos y eventos correspondientes que se pueden invalidar o enlazar para supervisar este comportamiento.

| Finalidad | WPF (método) | Xamarin. Forms (método) |
|--- |--- |--- |
|Activación inicial|ctor + Window. alcargado|ctor + Page. OnStart|
|Aparece|Window. IsVisibleChanged|Página. apareciendo|
|Hidden|Window. IsVisibleChanged|Página. desaparezca|
|Suspender o perder el foco|Window. OnDeactivated|Page. Sleep|
|Activado/recibido enfoque|Window. Onactivated|Page. alnude|
|Cerrado|Window. alclosing + Window. OnClose|no disponible|

Además, permiten ocultar o mostrar los controles secundarios, en WPF es una propiedad de tres Estados `IsVisible` (visible, Hidden y collapsed). En Xamarin. Forms, solo está visible u oculto a través de la propiedad `IsVisible`.

### <a name="layout"></a>Diseño

El diseño de página se produce en el mismo paso 2 (medida y organización) que ocurre en WPF. Puede enlazar el diseño de página invalidando los métodos siguientes en la clase `Page` de Xamarin. Forms:

| Método | Finalidad |
|--- |--- |
|OnChildMeasureInvalidated|Ha cambiado el tamaño preferido de un elemento secundario.|
|OnSizeAllocated|La página tiene asignado un ancho y un alto.|
|Evento LayoutChanged|El diseño o el tamaño de la página ha cambiado.|

No hay ningún evento de diseño global al que se llame hoy, ni tampoco hay un evento de `CompositionTarget.Rendering` global como se encuentra en WPF.

#### <a name="common-layout-properties"></a>Propiedades de diseño comunes

WPF y Xamarin. Forms admiten `Margin` para controlar el espaciado alrededor de un elemento y `Padding` para controlar el espaciado _dentro_ de un elemento. Además, la mayoría de las vistas de diseño de Xamarin. Forms tienen propiedades para controlar el espaciado (por ejemplo, fila o columna).

Además, la mayoría de los elementos tienen propiedades que influyen en cómo se colocan en el contenedor primario:

| WPF | Xamarin.Forms | Finalidad |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|Opciones Left/Center/Right/Stretch|
|VerticalAlignment|VerticalOptions|Opciones Top/Center/Bottom/Stretch|

> [!NOTE]
> La interpretación real de estas propiedades depende del contenedor principal.

#### <a name="layout-views"></a>Vistas de diseño

WPF y Xamarin. Forms usan controles de diseño para colocar los elementos secundarios. En la mayoría de los casos, se encuentran muy cercanos entre sí en cuanto a funcionalidad.

| WPF | Xamarin.Forms | Estilo de diseño |
|--- |--- |--- |
|StackPanel|StackLayout|Apilado infinito de izquierda a derecha o de arriba abajo|
|Cuadrícula|Cuadrícula|Formato tabular (filas y columnas)|
|DockPanel|no disponible|Acoplar a bordes de la ventana|
|Canvas|AbsoluteLayout|Posición de píxeles/coordenadas|
|WrapPanel|no disponible|Pila de ajuste|
|no disponible|RelativeLayout|Posicionamiento relativo basado en reglas|

> [!NOTE]
> Xamarin. Forms no admite un `GridSplitter`.

Ambas plataformas usan _propiedades adjuntas_ para ajustar los elementos secundarios.

### <a name="rendering"></a>Representación

La mecánica de representación de WPF y Xamarin. Forms es radicalmente diferente. En WPF, los controles que se crean representan directamente el contenido en píxeles en la pantalla. WPF mantiene dos gráficos de objetos (_árboles_) para representar este: el _árbol lógico_ representa los controles definidos en código o XAML, y el _árbol visual_ representa la representación real que se produce en la pantalla y que se realiza directamente mediante el elemento visual (a través de un método draw virtual) o a través de un `ControlTemplate` definido por XAML que se puede reemplazar o personalizar. Normalmente, el árbol visual es más complejo, ya que incluye elementos como los bordes alrededor de los controles, las etiquetas para el contenido implícito, etc. WPF incluye un conjunto de API (`LogicalTreeHelper` y `VisualTreeHelper`) para examinar estos dos gráficos de objetos.

En Xamarin. Forms, los controles que se definen en un `Page` son realmente simplemente objetos de datos simples. Son similares a la representación del árbol lógico, pero nunca representan el contenido por sí mismos. En su lugar, son el _modelo de datos_ que influye en la representación de los elementos. La representación real se realiza mediante un [conjunto independiente de _representadores visuales_ que se asignan a cada tipo de control](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Estos representadores se registran en cada uno de los proyectos específicos de la plataforma por ensamblados de Xamarin. Forms específicos de la plataforma. Puede ver una lista [aquí](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md). Además de reemplazar o extender el representador, Xamarin. Forms también admite [efectos](~/xamarin-forms/app-fundamentals/effects/index.md) que se pueden usar para influir en la representación nativa en cada plataforma.

#### <a name="the-logicalvisual-tree"></a>El árbol lógico/visual

No hay ninguna API expuesta para recorrer el árbol lógico de Xamarin. Forms, pero puede usar la reflexión para obtener la misma información. Por ejemplo, [este es un método que puede enumerar los elementos secundarios lógicos](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108) con reflexión.

## <a name="graphics"></a>Gráficos

Xamarin. Forms no incluye un sistema de gráficos para primitivas más allá de un rectángulo simple (`BoxView`). Puede incluir bibliotecas de terceros como [SkiaSharp](~/graphics-games/skiasharp/index.md) para obtener un dibujo 2D multiplataforma o [UrhoSharp](~/graphics-games/urhosharp/index.md) para 3D.

## <a name="resources"></a>Recursos

WPF y Xamarin. Forms tienen el concepto de recursos y diccionarios de recursos. Puede colocar cualquier tipo de objeto en una `ResourceDictionary` con una clave y, a continuación, buscarlo con `{StaticResource}` para ver las cosas que no cambiarán o `{DynamicResource}` para las cosas que pueden cambiar en el Diccionario en tiempo de ejecución. El uso y las mecánicas son los mismos con una diferencia: Xamarin. Forms requiere que se defina el `ResourceDictionary` que se va a asignar a la propiedad `Resources`, mientras que WPF crea uno previamente y lo asigna automáticamente.

Por ejemplo, consulte la definición siguiente:

**WPF**

```xaml
<Window.Resources>
   <Color x:Key="redColor">#ff0000</Color>
   ...
</Window.Resources>
```

**Xamarin.Forms**

```xaml
<ContentPage.Resources>
   <ResourceDictionary>
      <Color x:Key="redColor">#ff0000</Color>
      ...
   </ResourceDictionary>
</ContentPage.Resources>
```

Si no define el `ResourceDictionary`, se genera un error de tiempo de ejecución.

## <a name="styles"></a>Estilos

Los estilos también son totalmente compatibles con Xamarin. Forms y se pueden usar para crear un tema de los elementos de Xamarin. Forms que componen la interfaz de usuario. Admiten desencadenadores (propiedades, eventos y datos), herencia a través de `BasedOn`y búsquedas de recursos para valores. Los estilos se aplican a los elementos explícitamente a través de la propiedad `Style` o implícitamente al no proporcionar una clave de recurso, como WPF.

### <a name="device-styles"></a>Estilos de dispositivo

WPF tiene un conjunto de propiedades predefinidas (almacenadas como valores estáticos en un conjunto de clases estáticas como `SystemColors`) que dictan colores del sistema, fuentes y métricas en forma de valores y claves de recursos. Xamarin. Forms es similar, pero define un conjunto de [estilos de dispositivo](~/xamarin-forms/user-interface/styles/device.md) para representar las mismas cosas. El marco de trabajo proporciona estos estilos y se establecen en valores basados en el entorno de tiempo de ejecución (por ejemplo, accesibilidad).

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
