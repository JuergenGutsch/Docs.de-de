---
title: "Erstellen einer Web-API mit ASP.NET Core und Visual Studio für Mac"
description: "Erstellen einer Web-API mit ASP.NET Core MVC und Visual Studio für Mac"
author: rick-anderson
ms.author: riande
ms.date: 09/15/2017
ms.topic: get-started-article
ms.prod: asp.net-core
uid: tutorials/first-web-api-mac
helpviewer_heywords: ASP.NET Core, WebAPI, Web API, REST, mac, macOS, HTTP, Service, HTTP Service
ms.technology: aspnet
manager: wpickett
ms.openlocfilehash: 6bbd5e332e395928d8f79888ecf190f7f59a4bbc
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2017
---
# <a name="create-a-web-api-with-aspnet-core-mvc-and-visual-studio-for-mac"></a>Erstellen einer Web-API mit ASP.NET Core MVC und Visual Studio für Mac

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Mike Wasson](https://github.com/mikewasson)

In diesem Tutorial erstellen Sie eine Web-API zum Verwalten einer Liste von „To-Do“-Elementen. Sie werden keine Benutzeroberfläche erstellen.

Es gibt drei Versionen dieses Tutorials:

* macOS: Web-API mit Visual Studio für Mac (dieses Tutorial)
* Windows: [Web-API mit Visual Studio für Windows](xref:tutorials/first-web-api)
* macOS, Linux, Windows: [Web-API mit Visual Studio Code](xref:tutorials/web-api-vsc)

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[template files](../includes/webApi/intro.md)]

* Ein Beispiel für die Verwendung einer beständigen Datenbank finden Sie unter [Einführung in ASP.NET Core MVC unter Mac oder Linux](xref:tutorials/first-mvc-app-xplat/index).

## <a name="prerequisites"></a>Erforderliche Komponenten

Installieren Sie Folgendes:

- [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) oder höher
- [Visual Studio für Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-the-project"></a>Erstellen eines Projekts

Klicken Sie in Visual Studio auf **Datei > Neue Projektmappe**.

![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

Klicken Sie auf **.NET Core-App > ASP.NET Core-Web-API > Weiter**.

![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)

Geben Sie **TodoApi** als **Projektnamen** ein, und klicken Sie dann auf „Erstellen“.

![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

### <a name="launch-the-app"></a>Starten der App

Klicken Sie in Visual Studio auf **Ausführen > Mit Debugging starten**, um die App zu starten. Visual Studio startet dann einen Browser und navigiert zu `http://localhost:5000`. Ihnen wird der HTTP-Fehler „404 (Not Found)“ angezeigt.  Ändern Sie die URL zu `http://localhost:port/api/values`. Die `ValuesController`-Daten werden angezeigt:

```
["value1","value2"]
```

### <a name="add-support-for-entity-framework-core"></a>Hinzufügen der Unterstützung für Entity Framework Core

Installieren Sie den Datenbankanbieter [Entity Framework Core InMemory](https://docs.microsoft.com/ef/core/providers/in-memory/). Dieser Datenbankanbieter ermöglicht, dass Entity Framework Core mit einer In-Memory Database verwendet wird.

* Klicken Sie im Menü **Projekt** auf **NuGet-Pakete hinzufügen**. 

  *  Sie können alternativ mit der rechten Maustaste auf **Abhängigkeiten** und dann auf **Pakete hinzufügen** klicken.

* Geben Sie `EntityFrameworkCore.InMemory` in das Suchfeld ein.
* Wählen Sie `Microsoft.EntityFrameworkCore.InMemory` aus, und klicken Sie dann auf **Pakete hinzufügen**.

### <a name="add-a-model-class"></a>Hinzufügen einer Modellklasse

Ein Modell ist ein Objekt, das die Daten in Ihrer Anwendung darstellt. In diesem Fall ist das einzige Modell ein To-do-Element.

Fügen Sie einen Ordner namens *Models* hinzu. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt. Klicken Sie auf **Hinzufügen** > **Neuer Ordner**. Geben Sie dem Ordner den Namen *Modelle*.

![Neuer Ordner](first-web-api-mac/_static/folder.png)

Hinweis: Sie können Modellklassen überall in Ihrem Projekt unterbringen, aber der Ordner *Modelle* wird gemäß der Konvention verwendet.

Fügen Sie eine `TodoItem`-Klasse hinzu. Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen > Neue Datei > Allgemein > Leere Klasse**. Benennen Sie die Klasse `TodoItem`, und klicken Sie dann auf **Neu**.

Ersetzen Sie den generierten Code mit Folgendem:

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

Die Datenbank generiert die `Id`, wenn ein `TodoItem` erstellt wird.

### <a name="create-the-database-context"></a>Erstellen des Datenbankkontexts

Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert. Sie können diese Klasse durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellen.

Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

## <a name="add-a-controller"></a>Hinzufügen eines Controllers

Fügen Sie im Ordner *Controller* im Projektmappen-Explorer die Klasse `TodoController` hinzu.

Ersetzen Sie den generierten Code mit Folgendem (und fügen Sie schließende Klammern hinzu):

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a>Starten der App

Klicken Sie in Visual Studio auf **Ausführen > Mit Debugging starten**, um die App zu starten. Visual Studio startet einen Browser und navigiert zu `http://localhost:port`, wobei *port* eine zufällig ausgewählte Portnummer ist. Ihnen wird der HTTP-Fehler „404 (Not Found)“ angezeigt.  Ändern Sie die URL zu `http://localhost:port/api/values`. Die `ValuesController`-Daten werden angezeigt:

```
["value1","value2"]
```

Navigieren Sie zum `Todo`-Controller auf `http://localhost:port/api/todo`:

```
[{"key":1,"name":"Item1","isComplete":false}]
```

## <a name="implement-the-other-crud-operations"></a>Implementieren der anderen CRUD-Vorgänge

Fügen Sie Methoden `Create`, `Update` und `Delete` zum Controller hinzu. Dabei handelt sich um Variationen eines Designs, darum wird der Code mit den hervorgehobenen Hauptunterschieden dargestellt. Erstellen Sie das Projekt, nachdem Sie Code hinzugefügt oder geändert haben.

### <a name="create"></a>Erstellen

[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Dies ist eine HTTP-POST-Methode, die vom [`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute)-Attribut angegeben wird. Das [`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute)-Attribut weist MVC an, den Wert des To-Do-Elements aus dem Text der HTTP-Anforderung abzurufen.

Die `CreatedAtRoute`-Methode gibt die Antwort „201“ zurück, bei der es sich um die Standardantwort für eine HTTP-POST-Methode handelt, die eine neue Ressource auf dem Server erstellt. `CreatedAtRoute` fügt der Antwort außerdem einen Adressheader hinzu. Der Adressheader gibt den URI des neu erstellten To-Do-Elements zurück. Siehe [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

### <a name="use-postman-to-send-a-create-request"></a>Verwenden von Postman zum Senden einer Erstellungsanforderung

* Starten Sie die App (**Ausführen > Mit Debugging starten**).
* Starten Sie Postman.

![Postman-Konsole](first-web-api/_static/pmc.png)

* Legen Sie die HTTP-Methode auf `POST` fest.
* Wählen Sie das Optionsfeld **Body** aus.
* Wählen Sie das Optionsfeld **raw** aus.
* Legen Sie den Typ auf „JSON“ fest.
* Geben Sie im Schlüsselwert-Editor zum Beispiel folgendes To-Do-Element ein

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* Klicken Sie auf **Send**.

* Klicken Sie auf die Registerkarte „Header“ im unteren Bereich, und kopieren Sie den Header **Location**:

![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmget.png)

Sie können den Adressheader-URI verwenden, um auf die Ressource zuzugreifen, die Sie gerade erstellt haben. Denken Sie daran, dass die `GetById`-Methode die benannte Route `"GetTodo"` erstellt hat:

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(string id)
```

### <a name="update"></a>Update

[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`Update` ähnelt `Create`, verwendet aber HTTP PUT. Die Antwort ist [204 (Kein Inhalt)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität und nicht nur die Deltas sendet. Verwenden Sie HTTP PATCH, um Teilupdates zu unterstützen.

```json
{
  "key": 1,
  "name": "walk dog",
  "isComplete": true
}
```

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

### <a name="delete"></a>Löschen

[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

Die Antwort ist [204 (Kein Inhalt)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmd.png)

## <a name="next-steps"></a>Nächste Schritte

* [Routing to Controller Actions (Routing zu Controlleraktionen)](xref:mvc/controllers/routing)
* Weitere Informationen zum Bereitstellen Ihrer API finden Sie unter [Publishing and Deployment (Veröffentlichung und Bereitstellung)](../publishing/index.md).
* [Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))
* [Postman](https://www.getpostman.com/)
* [Fiddler](https://www.telerik.com/download/fiddler)
