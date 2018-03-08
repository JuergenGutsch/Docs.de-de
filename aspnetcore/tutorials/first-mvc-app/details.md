---
title: "Untersuchen der Methoden „Details“ und „Delete“"
author: rick-anderson
description: "Die Controllermethode „Details“ und Ansicht in einer einfachen ASP.NET Core MVC-App."
manager: wpickett
ms.author: riande
ms.date: 03/07/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 28ed7a7a56415d7eb675c06353fb9a8f65fb571f
ms.sourcegitcommit: c9658c0db446f7cb2e443f62b00cf773bed545fa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2017
---
# <a name="examining-the-details-and-delete-methods"></a>Untersuchen der Methoden „Details“ und „Delete“

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Öffnen Sie den „Movie“-Controller, und untersuchen Sie die `Details`-Methode.

[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]

Das MVC-Gerüstbaumodul, das diese Aktionsmethode erstellt hat, fügt einen Kommentar mit einer HTTP-Anforderung hinzu, die die Methode aufruft. In diesem Fall ist dies eine GET-Anforderung mit drei URL-Segmenten: dem Controller `Movies`, der Methode `Details` und dem Wert von `id`. Wie bereits erwähnt, werden diese Segmente in *Startup.cs* definiert.

[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

EF erleichtert das Suchen nach Daten mithilfe der `SingleOrDefaultAsync`-Methode. Ein wichtiges in die Methode integriertes Sicherheitsmerkmal ist, dass der Code überprüft, ob die Suchmethode einen Film gefunden hat, bevor sie versucht, etwas damit zu tun. Ein Hacker kann beispielsweise Fehler in die Website einschleusen, indem die von den Links erstellte URL von `http://localhost:xxxx/Movies/Details/1` in etwas wie `http://localhost:xxxx/Movies/Details/12345` geändert wird (oder einen anderen Wert, der keinen tatsächlichen Film darstellt). Wenn Sie nicht nach einem NULL-Movie gesucht haben, löst die App keine Ausnahme aus.

Untersuchen Sie die Methoden `Delete` und `DeleteConfirmed`.

[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete)]

Beachten Sie, dass die `HTTP GET Delete`-Methode den angegebenen Film nicht löscht, sondern eine Ansicht des Films zurückgibt, in der Sie den Löschvorgang (HttpPost) auslösen können. Das Ausführen eines Löschvorgangs als Reaktion auf eine GET-Anforderung (oder eigentlich eines Bearbeitungs-, Erstellungs- oder sonstigen Vorgangs, der Daten ändern) stellt eine Sicherheitslücke dar.

Die `[HttpPost]`-Methode, die die Daten löscht, heißt `DeleteConfirmed`, um der HTTP-POST-Methode eine eindeutige Signatur bzw. einen eindeutigen Namen zu geben. Die beiden Methodensignaturen werden nachstehend gezeigt:

[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]


Die Common Language Runtime (CLR) erfordert überladene Methoden, um eine eindeutige Parametersignatur zu erhalten (selber Methodenname, aber unterschiedliche Liste von Parametern). Hier benötigen Sie jedoch zwei `Delete`-Methoden (eine für GET und eine für POST), die beide die gleiche Parametersignatur haben. (Beide müssen eine einzelne ganze Zahl als Parameter akzeptieren.)

Es gibt zwei Ansätze zur Lösung dieses Problems. Eine besteht darin, die Methoden unterschiedlich zu benennen. Dafür das der Gerüstbaumechanismus im vorherigen Beispiel gesorgt. Dies bringt jedoch ein kleines Problem mit sich: ASP.NET ordnet Segmente einer URL anhand des Namens zu Aktionsmethoden zu. Wenn Sie die Methode umbenennen sollten, ist das Routing normalerweise nicht in der Lage, diese Methode zu finden. Die Lösung besteht (wie im Beispiel) im Hinzufügen des `ActionName("Delete")`-Attributs zur `DeleteConfirmed`-Methode. Dieses Attribut führt die Zuordnung für das Routingsystem durch, sodass eine URL, die „/Delete/“ für eine POST-Anforderung enthält, die `DeleteConfirmed`-Methode findet.

Eine weitere gängige Behelfslösung für Methoden mit identischen Namen und Signaturen ist das künstliche Ändern der POST-Methode durch Hinzufügen eines zusätzlichen (nicht verwendeten) Parameters. Siehe dazu die vorherige POST-Anforderung, der wir den `notUsed`-Parameter hinzugefügt haben. Sie können dasselbe hier für die `[HttpPost] Delete`-Methode ausführen:

```csharp
// POST: Movies/Delete/6
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Veröffentlichen in Azure

Anweisungen zum Veröffentlichen dieser App in Azure finden Sie unter [Veröffentlichen einer ASP.NET Core-Web-App in Azure App Service mit Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs).

Vielen Dank für Ihr Interesse an dieser Einführung in ASP.NET Core MVC. Wir freuen uns über Kommentare, die Sie hinterlassen. [Erste Schritte mit MVC und EF Core](xref:data/ef-mvc/intro) ist ein ausgezeichneter Anschlussartikel an dieses Tutorial.

>[!div class="step-by-step"]
[Vorherige](validation.md)
