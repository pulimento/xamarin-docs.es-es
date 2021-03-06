---
title: Controls Reference (Referencia de controles)
description: Una descripción de todos los elementos de la interfaz de usuario que se usan para crear una aplicación de Xamarin. Forms. En este artículo se enumera los grupos de control que conforman la interfaz de usuario de una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2019
ms.openlocfilehash: b9436603c17eb8008f470e75a52a93e8e122034f
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488120"
---
# <a name="controls-reference"></a>Controls Reference (Referencia de controles)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

La interfaz de usuario de una aplicación de Xamarin. Forms se construye a partir de objetos que se asignan a los controles nativos de cada plataforma de destino. Esto permite que las aplicaciones específicas de la plataforma para iOS, Android y el Plataforma universal de Windows usen el código de Xamarin. Forms incluido en una [biblioteca de .net Standard](~/cross-platform/app-fundamentals/net-standard.md).

Los cuatro grupos de control principales que se usan para crear la interfaz de usuario de una aplicación de Xamarin. Forms son los siguientes:

- [**Páginas**](pages.md)
- [**Diseños**](layouts.md)
- [**Vistas**](views.md)
- [**Celdas**](cells.md)

Por lo general, una página de Xamarin.Forms ocupa toda la pantalla. La página suele contener un diseño, que contiene las vistas y, posiblemente, otros diseños. Las celdas son componentes especializados utilizados en conexión con [ `TableView` ](views.md#tableview) y [ `ListView` ](views.md#listview). Un diagrama de clases que muestra la jerarquía de tipos que se suelen usar para compilar una interfaz de usuario en Xamarin. Forms se puede encontrar en la [jerarquía de clases de Xamarin. Forms Controls](~/xamarin-forms/internals/class-hierarchy.md).

En los artículos en cuatro [ **páginas**](pages.md), [ **diseños**](layouts.md), [ **vistas** ](views.md), y [ **celdas**](cells.md), se describe cada tipo de control con vínculos a uno o más programas de ejemplo, un artículo que describe su uso (si existe) y su documentación de API (si existen). Cada tipo de control también está acompañado por una captura de pantalla que muestra una página del ejemplo [**FormsGallery**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) que se ejecuta en dispositivos iOS y Android. Cada captura de pantalla siguiente se vincula al código de origen para la página de C#, la página XAML equivalente y (cuando corresponda) el archivo de código subyacente de C# para la página XAML.

> [!NOTE]
> Las páginas, los diseños y las vistas derivan de la clase `VisualElement`. La clase `VisualElement` proporciona una variedad de propiedades, métodos y eventos que son útiles para derivar clases. Para obtener más información, vea [propiedades, métodos y eventos de VisualElement](common-properties.md).

Además de los controles suministrados con Xamarin. Forms, los controles de terceros están disponibles. Para obtener más información, vea [controles de terceros](thirdparty.md).

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Xamarin.Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Jerarquía de clases de controles de Xamarin. Forms](~/xamarin-forms/internals/class-hierarchy.md)
- [Documentación de la API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
