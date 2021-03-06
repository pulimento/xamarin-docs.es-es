---
title: Personalización de la apariencia de una tabla en Xamarin. iOS
description: En este documento se describe cómo personalizar la apariencia de una tabla en Xamarin. iOS. Describe los estilos de celda, los accesorios, los separadores de celdas y los diseños de celda personalizados.
ms.prod: xamarin
ms.assetid: 8A83DE38-0028-CB61-66F9-0FB9DE552286
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 7b4042d9090823fbeff89face7c00e5753666c97
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021920"
---
# <a name="customizing-a-tables-appearance-in-xamarinios"></a>Personalización de la apariencia de una tabla en Xamarin. iOS

La manera más sencilla de cambiar la apariencia de una tabla es utilizar un estilo de celda diferente. Puede cambiar el estilo de celda que se utiliza al crear cada celda en el método `GetCell` del `UITableViewSource`.

## <a name="cell-styles"></a>Estilos de celda

Hay cuatro estilos integrados:

- **Valor predeterminado** : admite un `UIImageView`.
- **Subtítulo** : admite un `UIImageView` y un subtítulo.
- **Value1** : subtítulo alineado a la derecha, compatible con un `UIImageView`.
- **Valor2** : el título está alineado a la derecha y el subtítulo está alineado a la izquierda (pero no hay ninguna imagen).

Estas capturas de pantallas muestran cómo aparece cada estilo:

 [![](customizing-table-appearance-images/image7.png "These screenshots show how each style appears")](customizing-table-appearance-images/image7.png#lightbox)

El ejemplo **CellDefaultTable** contiene el código para generar estas pantallas. El estilo de celda se establece en el constructor `UITableViewCell`, de la siguiente manera:

```csharp
cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Subtitle, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value1, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value2, cellIdentifier);
```

A continuación, se pueden establecer [las propiedades admitidas](xref:UIKit.UITableViewCell) del estilo de celda:

```csharp
cell.TextLabel.Text = tableItems[indexPath.Row].Heading;
cell.DetailTextLabel.Text = tableItems[indexPath.Row].SubHeading;
cell.ImageView.Image = UIImage.FromFile("Images/" + tableItems[indexPath.Row].ImageName); // don't use for Value2
```

## <a name="accessories"></a>Accesorios

Las celdas pueden tener los siguientes accesorios agregados a la derecha de la vista:

- **Marca de verificación** : se puede utilizar para indicar la selección múltiple en una tabla.
- **DetailButton** : responde a la entrada táctil independientemente del resto de la celda, lo que le permite realizar una función diferente para tocar la celda en sí (por ejemplo, abrir una ventana emergente o nueva que no forma parte de una pila `UINavigationController`).
- **DisclosureIndicator** : se usa normalmente para indicar que al tocar la celda se abrirá otra vista.
- **DetailDisclosureButton** : una combinación de los `DetailButton` y `DisclosureIndicator`.

Este es el aspecto que tiene:

 [![](customizing-table-appearance-images/image8.png "Sample Accessories")](customizing-table-appearance-images/image8.png#lightbox)

Para mostrar uno de estos accesorios, puede establecer la propiedad `Accessory` en el método `GetCell`:

```csharp
cell.Accessory = UITableViewCellAccessory.Checkmark;
//cell.Accessory = UITableViewCellAccessory.DisclosureIndicator;
//cell.Accessory = UITableViewCellAccessory.DetailDisclosureButton; // implement AccessoryButtonTapped
//cell.Accessory = UITableViewCellAccessory.None; // to clear the accessory
```

Cuando se muestran los `DetailButton` o `DetailDisclosureButton`, también debe invalidar la `AccessoryButtonTapped` para realizar alguna acción cuando se toca.

```csharp
public override void AccessoryButtonTapped (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("DetailDisclosureButton Touched", tableItems[indexPath.Row].Heading, UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    owner.PresentViewController (okAlertController, true, null);

    tableView.DeselectRow (indexPath, true);
}
```

En el **CellAccessoryTable** de ejemplo se muestra un ejemplo de uso de accesorios.

## <a name="cell-separators"></a>Separadores de celdas

Los separadores de celdas son las celdas de la tabla que se utilizan para separar la tabla. Las propiedades se establecen en la tabla.

```csharp
TableView.SeparatorColor = UIColor.Blue;
TableView.SeparatorStyle = UITableViewCellSeparatorStyle.DoubleLineEtched;
```

También es posible agregar un efecto de desenfoque o vibrancy al separador:

```csharp
// blur effect
TableView.SeparatorEffect =
    UIBlurEffect.FromStyle(UIBlurEffectStyle.Dark);

//vibrancy effect
var effect = UIBlurEffect.FromStyle(UIBlurEffectStyle.Light);
TableView.SeparatorEffect = UIVibrancyEffect.FromBlurEffect(effect);
```

El separador también puede tener un bajorrelieve:

```csharp
TableView.SeparatorInset.InsetRect(new CGRect(4, 4, 150, 2));
```

## <a name="creating-custom-cell-layouts"></a>Crear diseños de celda personalizados

Para cambiar el estilo visual de una tabla, debe proporcionar las celdas personalizadas que se van a mostrar. La celda personalizada puede tener distintos colores y diseños de control.

En el ejemplo CellCustomTable se implementa una subclase `UITableViewCell` que define un diseño personalizado de `UILabel`s y un `UIImage` con diferentes fuentes y colores. Las celdas resultantes tienen el siguiente aspecto:

 [![](customizing-table-appearance-images/image9.png "Custom Cell Layouts")](customizing-table-appearance-images/image9.png#lightbox)

La clase de celda personalizada solo consta de tres métodos:

- **Constructor** : crea los controles de interfaz de usuario y establece las propiedades de estilo personalizadas (por ejemplo, fuente, tamaño y colores).
- **UpdateCell** : método para `UITableView.GetCell` que se va a usar para establecer las propiedades de la celda.
- **LayoutSubviews** : establezca la ubicación de los controles de la interfaz de usuario. En el ejemplo, cada celda tiene el mismo diseño, pero una celda más compleja (especialmente las que tienen tamaños variables) puede necesitar posiciones de diseño diferentes en función del contenido que se muestre.

A continuación se muestra el código de ejemplo completo de **CellCustomTable > CustomVegeCell.CS** :

```csharp
public class CustomVegeCell : UITableViewCell  {
    UILabel headingLabel, subheadingLabel;
    UIImageView imageView;
    public CustomVegeCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
    {
        SelectionStyle = UITableViewCellSelectionStyle.Gray;
        ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);
        imageView = new UIImageView();
        headingLabel = new UILabel () {
            Font = UIFont.FromName("Cochin-BoldItalic", 22f),
            TextColor = UIColor.FromRGB (127, 51, 0),
            BackgroundColor = UIColor.Clear
        };
        subheadingLabel = new UILabel () {
            Font = UIFont.FromName("AmericanTypewriter", 12f),
            TextColor = UIColor.FromRGB (38, 127, 0),
            TextAlignment = UITextAlignment.Center,
            BackgroundColor = UIColor.Clear
        };
        ContentView.AddSubviews(new UIView[] {headingLabel, subheadingLabel, imageView});

    }
    public void UpdateCell (string caption, string subtitle, UIImage image)
    {
        imageView.Image = image;
        headingLabel.Text = caption;
        subheadingLabel.Text = subtitle;
    }
    public override void LayoutSubviews ()
    {
        base.LayoutSubviews ();
        imageView.Frame = new CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
        headingLabel.Frame = new CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
        subheadingLabel.Frame = new CGRect (100, 18, 100, 20);
    }
}
```

El método `GetCell` del `UITableViewSource` debe modificarse para crear la celda personalizada:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (cellIdentifier) as CustomVegeCell;
    if (cell == null)
        cell = new CustomVegeCell (cellIdentifier);
    cell.UpdateCell (tableItems[indexPath.Row].Heading
            , tableItems[indexPath.Row].SubHeading
            , UIImage.FromFile ("Images/" + tableItems[indexPath.Row].ImageName) );
    return cell;
}
```

## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithTables (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
