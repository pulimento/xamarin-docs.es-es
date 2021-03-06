---
title: Uso de gráficos principales y animación básica en Xamarin. iOS
description: En este artículo se muestra paso a paso cómo crear una aplicación que usa gráficos principales y animaciones básicas. Muestra cómo dibujar en la pantalla en respuesta a la interacción con el usuario y cómo animar una imagen para viajar a lo largo de un trazado.
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: b35e88cfdc0bce321068951f1617885c90331c83
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032451"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>Uso de gráficos principales y animación básica en Xamarin. iOS

En este tutorial, vamos a dibujar una ruta de acceso con los gráficos principales en respuesta a la entrada táctil. A continuación, agregaremos una `CALayer` que contiene una imagen que se animará a lo largo del trazado.

En la captura de pantalla siguiente se muestra la aplicación completada:

![](graphics-animation-walkthrough-images/00-final-app.png "The completed application")

Antes de comenzar, descargue el ejemplo *GraphicsDemo* que acompaña a esta guía. Se puede descargar [aquí](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation) y se encuentra en el directorio **GraphicsWalkthrough** inicie el proyecto denominado **GraphicsDemo_starter** haciendo doble clic en él y abriendo la clase `DemoView`.

## <a name="drawing-a-path"></a>Dibujo de un trazado

1. En `DemoView` agregue una variable de `CGPath` a la clase y cree una instancia de ella en el constructor. Declare también dos `CGPoint` variables, `initialPoint` y `latestPoint`, que se van a usar para capturar el punto táctil desde el que se crea la ruta de acceso:

    ```csharp
    public class DemoView : UIView
    {
        CGPath path;
        CGPoint initialPoint;
        CGPoint latestPoint;

        public DemoView ()
        {
            BackgroundColor = UIColor.White;

            path = new CGPath ();
        }
    }
    ```

2. Agregue las siguientes directivas Using:

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. A continuación, invalide `TouchesBegan` y `TouchesMoved,` y agregue las siguientes implementaciones para capturar el punto táctil inicial y cada punto táctil posterior, respectivamente:

    ```csharp
    public override void TouchesBegan (NSSet touches, UIEvent evt){

        base.TouchesBegan (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt){

        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }
    ```

    se llamará a `SetNeedsDisplay` cada vez que toque el movimiento para que `Draw` se llame en el siguiente paso de bucle de ejecución.

4. Vamos a agregar líneas al trazado en el método `Draw` y usar una línea discontinua roja para dibujar con. [Implemente `Draw`](~/ios/platform/graphics-animation-ios/core-graphics.md) con el código que se muestra a continuación:

    ```csharp
    public override void Draw (CGRect rect){

        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            //get graphics context
            using(CGContext g = UIGraphics.GetCurrentContext ()){

                //set up drawing attributes
                g.SetLineWidth (2);
                UIColor.Red.SetStroke ();

                //add lines to the touch points
                if (path.IsEmpty) {
                    path.AddLines (new CGPoint[]{initialPoint, latestPoint});
                } else {
                    path.AddLineToPoint (latestPoint);
                }

                //use a dashed line
                g.SetLineDash (0, new nfloat[] { 5, 2 * (nfloat)Math.PI });

                //add geometry to graphics context and draw it
                g.AddPath (path);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
    ```

Si ejecutamos la aplicación ahora, podemos tocar para dibujar en la pantalla, tal como se muestra en la siguiente captura de pantalla:

![](graphics-animation-walkthrough-images/01-path.png "Drawing on the screen")

## <a name="animating-along-a-path"></a>Animar a lo largo de un trazado

Ahora que hemos implementado el código para permitir que los usuarios dibujen el trazado, vamos a agregar el código para animar una capa a lo largo del trazado dibujado.

1. En primer lugar, agregue una variable [`CALayer`](~/ios/platform/graphics-animation-ios/core-animation.md) a la clase y créela en el constructor:

    ```csharp
    public class DemoView : UIView
        {
            …

            CALayer layer;

            public DemoView (){
                …

                //create layer
                layer = new CALayer ();
                layer.Bounds = new CGRect (0, 0, 50, 50);
                layer.Position = new CGPoint (50, 50);
                layer.Contents = UIImage.FromFile ("monkey.png").CGImage;
                layer.ContentsGravity = CALayer.GravityResizeAspect;
                layer.BorderWidth = 1.5f;
                layer.CornerRadius = 5;
                layer.BorderColor = UIColor.Blue.CGColor;
                layer.BackgroundColor = UIColor.Purple.CGColor;
            }
    ```

2. A continuación, agregaremos la capa como una subcapa de la capa de la vista cuando el usuario Levante el dedo de la pantalla. A continuación, crearemos una animación de fotogramas clave mediante la ruta de acceso, animando la `Position`de la capa.

    Para ello, es necesario invalidar el `TouchesEnded` y agregar el código siguiente:

    ```csharp
    public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            base.TouchesEnded (touches, evt);

            //add layer with image and animate along path

            if (layer.SuperLayer == null)
                Layer.AddSublayer (layer);

            // create a keyframe animation for the position using the path
            layer.Position = latestPoint;
            CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
            animPosition.Path = path;
            animPosition.Duration = 3;
            layer.AddAnimation (animPosition, "position");
        }
    ```

3. Ejecutar la aplicación ahora y después de dibujar, se agrega una capa con una imagen y se desplaza a lo largo de la ruta de acceso dibujada:

![](graphics-animation-walkthrough-images/00-final-app.png "A layer with an image is added and travels along the drawn path")

## <a name="summary"></a>Resumen

En este artículo, se recorre un ejemplo en el que se relacionan los conceptos de gráficos y animaciones. En primer lugar, hemos mostrado cómo usar los gráficos principales para dibujar una ruta de acceso en una `UIView` en respuesta a la interacción del usuario. Después hemos mostrado cómo usar la animación básica para que una imagen viaje a lo largo de ese trazado.

## <a name="related-links"></a>Vínculos relacionados

- [Animación básica](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Gráficos básicos](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Recetas básicas de animación](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
