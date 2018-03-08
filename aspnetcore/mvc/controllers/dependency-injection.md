---
title: "Abhängigkeitsinjektion in Controllern"
author: ardalis
description: 
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: ff0a1a34ee6b025be6312a81f1a0bcdd07026adb
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="dependency-injection-into-controllers"></a>Abhängigkeitsinjektion in Controllern

<a name="dependency-injection-controllers"></a>

Durch [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC-Controller, sollte ihre Abhängigkeiten explizit über ihre Konstruktoren anfordern. In einigen Fällen einzelne Controlleraktionen erfordern möglicherweise einen Dienst und es möglicherweise nicht sinnvoll, um auf Controllerebene anzufordern. In diesem Fall können Sie auch auswählen, einen Dienst als Parameter auf die Aktionsmethode einzufügen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

## <a name="dependency-injection"></a>Abhängigkeitsinjektion

Abhängigkeitsinjektion ist eine Technik, die folgt die [Abhängigkeit Umkehrung Prinzip](http://deviq.com/dependency-inversion-principle/), sodass bei Anwendungen, die von lose verbundenen Modulen gebildet werden. ASP.NET Core verfügt über integrierte Unterstützung für [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md), wodurch Anwendungen einfacher zu testen und zu verwalten.

## <a name="constructor-injection"></a>Konstruktoreinfügung

ASP.NET Core der integrierte Unterstützung für Konstruktor basierende Abhängigkeitsinjektion erstreckt sich bis MVC-Controller. Durch einen Diensttyp einfach Ihre Domänencontroller als Konstruktorparameter hinzufügen, versucht ASP.NET Core, dieses Typs mit der integrierten Dienstcontainer aufzulösen. Dienste werden in der Regel, jedoch nicht immer mit Schnittstellen definiert. Wenn Ihre Anwendung von Geschäftslogik, die von der aktuellen Zeit abhängig ist, können Sie z. B. einen Dienst, der die Zeit (statt eine feste Programmierung ihn), ruft einfügen die ermöglichen würden die Tests Implementierungen zu übergeben, die einem bestimmten Zeitpunkt zu verwenden.

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]


Implementieren einer Schnittstelle wie dieses, sodass die Systemuhr zur Laufzeit verwendet, ist trivial:

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]


Mit diesem erfüllt können wir den Dienst in unser Controller verwenden. In diesem Fall haben wir die Programmlogik zum hinzugefügt der `HomeController` `Index` Methode, um dem Benutzer einen Gruß anzeigen auf Grundlage der Tageszeit.

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]

Wenn wir die Anwendung jetzt ausführen, wird es sehr wahrscheinlich einen Fehler auftreten:

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

Dieser Fehler tritt auf, wenn es einen Dienst in nicht konfiguriert haben die `ConfigureServices` -Methode in unserer `Startup` Klasse. Um anzugeben, die Anforderungen für `IDateTime` sollten korrigiert werden, mithilfe einer Instanz von `SystemDateTime`, fügen Sie die hervorgehobene Zeile in der Liste unten, um Ihre `ConfigureServices` Methode:

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]

> [!NOTE]
> Diese bestimmten Dienst mit einer von mehreren verschiedenen Lebensdauer Optionen implementiert werden kann (`Transient`, `Scoped`, oder `Singleton`). Finden Sie unter [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md) zu verstehen, wie jede dieser Bereichsoptionen das Verhalten des Diensts auswirken.

Sobald der Dienst konfiguriert wurde, sollte Ausführen der Anwendung, und navigieren zur Startseite die Nachricht einen zeitbasierten anzeigen, wie erwartet:

![Grußformel bei der Server](dependency-injection/_static/server-greeting.png)

>[!TIP]
> Finden Sie unter [Controllerlogik testen](testing.md) zu erfahren, wie Sie Abhängigkeiten explizit anfordern [http://deviq.com/explicit-dependencies-principle/](http://deviq.com/explicit-dependencies-principle/) im Controller wird der Code einfacher zu testen.

ASP.NET Core des integrierten Abhängigkeitsinjektion unterstützt das Vorhandensein nur eines einzelnen Konstruktors für Klassen, die Dienste anfordern. Wenn Sie mehr als einen Konstruktor verfügen, erhalten Sie möglicherweise eine Ausnahme, die besagt:

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

Wie in die Fehlermeldung angegeben, können Sie dieses Problem, dass nur einen einzelnen Konstruktor beheben. Sie können auch [ersetzen standardmäßig Dependency Injection-Unterstützung mit einer Implementierung von Drittanbietern](../../fundamentals/dependency-injection.md#replacing-the-default-services-container)viele mehrere Konstruktoren werden, unterstützen.

## <a name="action-injection-with-fromservices"></a>Aktion-Injection mit FromServices

In einigen Fällen benötigen Sie keinen Dienst für mehr als eine Aktion innerhalb Ihres Controllers. In diesem Fall kann es sinnvoll, den Dienst als Parameter an die Aktionsmethode einzufügen. Dies erfolgt durch den Parameter mit dem Attribut kennzeichnen `[FromServices]` wie hier gezeigt:

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]

## <a name="accessing-settings-from-a-controller"></a>Zugreifen auf Einstellungen von einem Controller

Zugriff auf Anwendungs- oder Einstellungen innerhalb eines Controllers ist ein allgemeines Muster. Dieser Zugriff sollte die in beschriebenen Optionen-Muster verwenden [Konfiguration](xref:fundamentals/configuration/index). Sie sollte nicht direkt aus Ihrem Controller mithilfe der Abhängigkeitsinjektion im Allgemeinen Einstellungen anfordern. Ein besserer Ansatz besteht darin Anforderung eine `IOptions<T>` Instanz, in denen `T` ist die Konfigurationsklasse, die Sie benötigen.

Um mit dem Muster Optionen zu arbeiten, müssen Sie eine Klasse erstellen, die Optionen, wie diese darstellt:

[!code-csharp[Main](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]

Dann müssen Sie so konfigurieren Sie die Anwendung verwenden Sie das Modell für die Optionen, und fügen Sie Ihrer Konfigurationsklasse auf die Auflistung der Dienste in `ConfigureServices`:

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]

> [!NOTE]
> In der obigen Liste konfigurieren wir die Anwendung, die Einstellungen aus einer JSON-formatierten Datei zu lesen. Sie können auch die Einstellungen vollständig im Code konfigurieren, wie im obigen kommentierten Code gezeigt wird. Finden Sie unter [Konfiguration](xref:fundamentals/configuration/index) für weitere Konfigurationsoptionen.

Nachdem Sie eine stark typisierte Konfigurationsobjekt angegeben haben (in diesem Fall `SampleWebSettings`) und wurde jedoch hinzugefügt, auf die Auflistung Dienste können fordern sie eine beliebige andere Methode Controller bzw. die Aktionsmethode Anfordern einer Instanz von `IOptions<T>` (in diesem Fall `IOptions<SampleWebSettings>`) . Der folgende Code zeigt, wie eine die Einstellungen von einem Controller anfordern möchten:

[!code-csharp[Main](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]

Nach dem Muster Optionen können Sie Einstellungen und Konfigurationen, um Sie voneinander entkoppelt werden, und stellt sicher, liegt im Anschluss an des Controllers [Trennung von Anliegen](http://deviq.com/separation-of-concerns/), da sie nicht wissen, wo oder wie benötigt auf Einstellungen für die gefunden Informationen. Es auch erleichtert den Controller Komponententest [Controllerlogik testen](testing.md), da es ist keine [Fensterdekorationen](http://deviq.com/static-cling/) oder direkte Instanziierung von für Einstellungenklassen innerhalb der Controllerklasse.
