---
title: ASP.NET Core-Web-API-Hilfeseiten mit Swagger
author: spboyer
description: "Dieses Tutorial enthält eine exemplarische Vorgehensweise für das Hinzufügen von Swagger, um Dokumentationen und Hilfeseiten für eine Web-API-Anwendung zu generieren."
manager: wpickett
ms.author: spboyer
ms.date: 09/01/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 8a87972a7394246ece2af3485d93739975ba5383
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="aspnet-core-web-api-help-pages-using-swagger"></a>ASP.NET Core-Web-API-Hilfeseiten mit Swagger

<a name="web-api-help-pages-using-swagger"></a>

Von [Shayne Boyer](https://twitter.com/spboyer) und [Scott Addie](https://twitter.com/Scott_Addie)

Das Verstehen der verschiedenen Methoden einer API kann beim Erstellen einer verarbeitenden Anwendung eine Herausforderung für einen Entwickler sein.

Das Generieren von guten Dokumentationen und Hilfeseiten für Ihre Web-API ist mithilfe von [Swagger](https://swagger.io/) mit der .NET Core-Implementierung [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) genauso leicht wie das Hinzufügen von NuGet-Paketen und das Ändern von *Startup.cs*.

* [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) ist ein Open Source-Projekt zum Generieren von Swagger-Dokumenten für Web-APIs von ASP.NET Core.

* [Swagger](https://swagger.io/) ist eine maschinenlesbare Darstellung einer RESTful-API, die Unterstützung für die interaktive Dokumentation, das Generieren von Client SDK und die Ermittelbarkeit ermöglicht.

In diesem Tutorial wird das Beispiel von [Building Your First Web API with ASP.NET Core MVC and Visual Studio (Erstellen Ihrer ersten Web-API mit ASP.NET Core MVC und Visual Studio)](xref:tutorials/first-web-api) erstellt. Für weitere Informationen laden Sie das Beispiel unter [https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample) herunter.

## <a name="getting-started"></a>Erste Schritte

Es gibt drei Hauptkomponenten von Swashbuckle:

* `Swashbuckle.AspNetCore.Swagger`: Ein Swagger-Objektmodell bzw. eine Swagger-Middleware, um `SwaggerDocument`-Objekte als JSON-Endpunkte verfügbar zu machen.

* `Swashbuckle.AspNetCore.SwaggerGen`: Ein Swagger-Generator, der `SwaggerDocument`-Objekte direkt aus Ihren Routen, Controllern und Modellen erstellt. Dieser wird üblicherweise mit der Middleware für den Swagger-Endpunkt kombiniert, um Swagger-JSONs automatisch verfügbar zu machen.

* `Swashbuckle.AspNetCore.SwaggerUI`: Eine eingebettete Version des Swagger-UI-Tools, das Swagger-JSONs interpretiert, um die Web-API-Funktionen umfassend und anpassbar zu beschreiben. Es enthält integrierte Testumgebungen für die öffentlichen Methoden.

## <a name="nuget-packages"></a>NuGet-Pakete

Swashbuckle kann mit folgenden Vorgehensweisen hinzugefügt werden:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Aus dem Fenster **Paket-Manager-Konsole**:

    ```powershell
    Install-Package Swashbuckle.AspNetCore
    ```

* Aus dem Dialogfeld **NuGet-Pakete verwalten**:

     * Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.
     * Legen Sie die **Paketquelle** auf „nuget.org“ fest.
     * Geben Sie „Swashbuckle.AspNetCore“ in das Suchfeld ein.
     * Wählen Sie das Paket „Swashbuckle.AspNetCore“ auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie mit der rechten Maustaste auf den Ordner *Pakete* unter **Lösungspad** > **Pakete hinzufügen...**.
* Legen Sie im Fenster **Pakete hinzufügen** das Dropdownmenü **Quelle** auf „nuget.org“ fest.
* Geben Sie „Swashbuckle.AspNetCore“ in das Suchfeld ein.
* Wählen Sie das Paket „Swashbuckle.AspNetCore“ aus dem Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie folgenden Befehl aus dem **integrierten Terminal** aus:

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie den folgenden Befehl aus:

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

---

## <a name="add-and-configure-swagger-to-the-middleware"></a>Hinzufügen und Konfigurieren von Swagger für die Middleware

Fügen Sie den Swagger-Generator zu der Services-Sammlung in der `ConfigureServices`-Methode von *Startup.cs* hinzu:

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_ConfigureServices&highlight=7-10)]

Fügen Sie die folgende Using-Anweisung für die `Info`-Klasse hinzu:

```csharp
using Swashbuckle.AspNetCore.Swagger;
```

Aktivieren Sie die Middleware in der `Configure`-Methode von *Startup.cs*, um das generierte JSON-Dokument und die Swagger-Benutzeroberfläche zu verarbeiten:

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_Configure&highlight=4,7-10)]

Starten Sie die App, und navigieren Sie zu `http://localhost:<random_port>/swagger/v1/swagger.json`. Das generierte Dokument, das den Endpunkt beschreibt, wird angezeigt.

**Hinweis:** Microsoft Edge, Google Chrome und Firefox zeigen JSON-Dokumente nativ an. Es gibt Erweiterungen für Chrome, die das Dokument für eine verbesserte Lesbarkeit formatieren. *Das folgende Beispiel wird aus Gründen der Übersichtlichkeit reduziert.*

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

Dieses Dokument steuert die Swagger-Benutzeroberfläche, die durch Navigieren zu `http://localhost:<random_port>/swagger` angezeigt werden kann:

![Swagger-Benutzeroberfläche](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Jede öffentliche Aktionsmethode in `TodoController` kann über die Benutzeroberfläche getestet werden. Klicken Sie auf einen Methodennamen, um den Abschnitt zu erweitern. Fügen Sie die erforderlichen Parameter hinzu, und klicken Sie auf „Probieren Sie es aus!“.

![Beispiel für einen Swagger-GET-Test](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

## <a name="customization--extensibility"></a>Anpassung und Erweiterbarkeit

Swagger stellt Optionen für das Dokumentieren des Objektmodells und das Anpassen der Benutzeroberfläche bereit, damit diese mit Ihrem Design übereinstimmt.

### <a name="api-info-and-description"></a>API-Informationen und -Beschreibung

Die Konfigurationsaktion, die an die `AddSwaggerGen`-Methode übergeben wurde, kann zum Hinzufügen von Informationen, z.B. Autor, Lizenz und Beschreibung, verwendet werden:

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?range=20-30,36)]

Die folgende Abbildung zeigt die Swagger-Benutzeroberfläche, die die Versionsinformationen anzeigt:

![Swagger-Benutzeroberfläche mit Versionsinformationen: Beschreibung, Autor, Link „Mehr anzeigen“](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>XML-Kommentare

XML-Kommentare können mithilfe der folgenden Ansätze aktiviert werden:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus.
* Überprüfen Sie das Feld **XML-Dokumentationsdatei** unter dem Abschnitt **Ausgabe** der Registerkarte **Erstellen**:

![Projekteigenschaften auf der Registerkarte „Erstellen“](web-api-help-pages-using-swagger/_static/swagger-xml-comments.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Öffnen Sie das Dialogfeld **Projektoptionen** > **Erstellen** > **Compiler**.
* Überprüfen Sie das Feld **XML-Dokumentation generieren** im Abschnitt **Allgemeine Optionen**:

![Abschnitt „Allgemeine Optionen“ der Projektoptionen](web-api-help-pages-using-swagger/_static/swagger-xml-comments-mac.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Fügen Sie den folgenden Codeausschnitt manuell zu der *CSPROJ*-Datei hinzu:

[!code-xml[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/TodoApi.csproj?range=7-9)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Zeigen Sie Visual Studio Code an.

---

Das Aktivieren von XML-Kommentaren stellt Debuginformationen zu nicht-dokumentierten öffentlichen Typen und Members bereit. Nicht-dokumentierte Typen und Members werden durch die Warnmeldung *Fehlender XML-Kommentar für öffentlich sichtbaren Typ oder Element* angegeben.

Konfigurieren Sie Swagger, um die generierte XML-Datei verwenden. Bei Linux oder anderen Betriebssystemen als Windows kann bei Dateinamen und -pfaden die Groß- und Kleinschreibung berücksichtigt werden. Die Datei *ToDoApi.XML* könnte beispielsweise unter Windows gefunden werden, nicht aber unter CentOS.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_ConfigureServices&highlight=20-22)]

Im vorangehenden Code ruft `ApplicationBasePath` den Basispfad der App ab. Der Basispfad wird verwendet, um die XML-Kommentardatei zu suchen. *TodoApi.xml* funktioniert nur für dieses Beispiel, da der Name für die generierte XML-Kommentardatei auf dem Anwendungsnamen basiert.

Das Hinzufügen von Kommentaren mit drei Schrägstrichen zur Methode verbessert die Swagger-Benutzeroberfläche, indem die Beschreibung zum Header des Abschnitts hinzugefügt wird:

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete&highlight=2)]

![Swagger-Benutzeroberfläche mit dem XML-Kommentar „Löscht ein bestimmtes TodoItem.“ für die DELETE-Methode](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

Die Benutzeroberfläche wird von der generierten JSON-Datei gesteuert, die auch diese Kommentare enthält:

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

Fügen Sie einen [<remarks>](https://docs.microsoft.com/dotnet/csharp/programming-guide/xmldoc/remarks)-Tag zu der Dokumentation der `Create`-Aktionsmethode hinzu. Dieser ergänzt die Informationen, die im `<summary>`-Tag angegeben wurden und bietet eine robustere Swagger-Benutzeroberfläche. Der Inhalt des `<remarks>`-Tags kann aus Text, JSON oder XML bestehen.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

Beachten Sie die Verbesserungen in der Benutzeroberfläche durch diese zusätzlichen Kommentare.

![Swagger-Benutzeroberfläche mit zusätzlichen Kommentaren](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>Datenanmerkungen

Ergänzen Sie das Modell mit Attributen, die in `System.ComponentModel.DataAnnotations` gefunden werden können, um das Steuern der Swagger-Benutzeroberflächenkomponenten zu vereinfachen.

Fügen Sie das `[Required]`-Attribut der `Name`-Eigenschaft der `TodoItem`-Klasse hinzu:

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Models/TodoItem.cs?highlight=10)]

Das Vorhandensein dieses Attributs ändert das Verhalten der Benutzeroberfläche und des zugrunde liegenden JSON-Schemas:

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

Fügen Sie das `[Produces("application/json")]`-Attribut zum API-Controller hinzu. Dadurch kann deklariert werden, dass die Aktionen des Controllers die Rückgabe eines Inhaltstypen von *application/json* unterstützen:

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_TodoController&highlight=3)]

Im Dropdownmenü des **Anforderungsinhaltstyps** ist dieser Inhaltstyp als Standard für die GET-Aktionen des Controllers ausgewählt:

![Swagger UI mit Standardinhaltstyp für die Antwort](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

Mit zunehmender Verwendung von Datenanmerkungen in der Web-API werden die UI- und API-Hilfeseiten beschreibender und nützlicher.

### <a name="describing-response-types"></a>Beschreiben von Antworttypen

Für verarbeitende Entwickler ist es am wichtigsten, was zurückgegeben wird &mdash; besonders Antworttypen und Fehlercodes (wenn diese nicht dem Standard entsprechen). Diese werden in XML-Kommentaren und Datenanmerkungen verarbeitet.

Die `Create`-Aktion gibt bei Erfolg `201 Created` zurück oder `400 Bad Request`, wenn der bereitgestellte Anforderungstext NULL ist. Ohne richtige Dokumentation in der Swagger-Benutzeroberfläche fehlt dem Consumer das Wissen über diese erwarteten Ergebnisse. Das Problem kann behoben werden, indem die hervorgehobenen Zeilen im folgenden Beispiel hinzugefügt werden:

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

Die Swagger-Benutzeroberfläche dokumentiert nun deutlich die erwarteten HTTP-Antwortcodes:

![Swagger-Benutzeroberfläche, die die Klassenbeschreibung „Gibt das neu erstellte Todo-Element zurück“ nach der Antwort anzeigt sowie „400“ für den Statuscode und „Wenn das Element NULL ist“ als Grund unter den Antwortnachrichten](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

### <a name="customizing-the-ui"></a>Anpassen der Benutzeroberfläche

Die Stock UI ist funktionsfähig und vorzeigbar, wenn Sie jedoch Dokumentationsseiten für Ihre API erstellen, möchten Sie eventuell, dass diese Ihre Marke oder Ihr Design darstellt. Das Ausführen dieser Aufgabe mit Swashbuckle-Komponenten erfordert das Hinzufügen der Ressourcen, um statische Dateien zu verarbeiten und dann die Ordnerstruktur zu erstellen, um diese Dateien zu hosten.

Wenn Sie Anwendungen für .NET Framework entwickeln, fügen Sie das NuGet-Paket `Microsoft.AspNetCore.StaticFiles` zum Projekt hinzu:

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

Aktivieren Sie die Middleware für statische Dateien:

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_Configure&highlight=3)]

Rufen Sie die Inhalte des Ordners *dist* aus dem [GitHub-Repository für die Swagger-Benutzeroberfläche](https://github.com/swagger-api/swagger-ui/tree/2.x/dist) ab. Dieser Ordner enthält die erforderlichen Ressourcen für die Seite der Swagger-Benutzeroberfläche.

Erstellen Sie einen *wwwroot/swagger/ui*-Ordner, und kopieren Sie die Inhalte des Ordners *dist* in ihn.

Erstellen Sie die Datei *wwwroot/swagger/ui/css/custom.css* mit dem folgenden CSS, um den Seitenheader anzupassen:

[!code-css[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/css/custom.css)]

Verweisen Sie in der Datei *index.html* auf *custom.css*:

[!code-html[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/index.html?range=14)]

Navigieren Sie zur Seite *index.html* auf `http://localhost:<random_port>/swagger/ui/index.html`. Geben Sie `http://localhost:<random_port>/swagger/v1/swagger.json` in das Textfeld des Headers ein, und klicken Sie dann auf die Schaltfläche **Durchsuchen**. Die Ergebnisseite sieht folgendermaßen aus:

![Swagger-Benutzeroberfläche mit benutzerdefiniertem Headertitel](web-api-help-pages-using-swagger/_static/custom-header.png)

Es gibt noch viele weitere Verwendungsmöglichkeiten für diese Seite. Weitere Informationen zum vollen Funktionsumgang der UI-Ressourcen finden Sie im [GitHub-Repository für die Swagger-Benutzeroberfläche](https://github.com/swagger-api/swagger-ui).
