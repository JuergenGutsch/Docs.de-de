---
title: Behandlung von Anforderungen mit Controller in ASP.NET Core MVC
author: ardalis
description: 
ms.author: riande
manager: wpickett
ms.date: 07/03/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/actions
ms.openlocfilehash: 99dcf1bd4f0dc4fcb6169f48bd398c9e40c21a35
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/12/2017
---
# <a name="handling-requests-with-controllers-in-aspnet-core-mvc"></a>Behandlung von Anforderungen mit Controllern in ASP.NET Core MVC

Durch [Steve Smith](https://ardalis.com/) und [Scott Addie](https://github.com/scottaddie)

Domänencontroller, Aktionen und Aktionsergebnisse sind grundlegende Bestandteile der Entwicklung von Apps mithilfe von ASP.NET Core MVC.

## <a name="what-is-a-controller"></a>Was ist ein Domänencontroller?

Ein Controller wird verwendet, um eine Reihe von Aktionen zu definieren und zu gruppieren. Eine Aktion (oder *Aktionsmethode*) ist eine Methode auf einem Domänencontroller der Anforderungen verarbeitet. Controller nehmen eine logische Gruppierung von ähnlichen Aktionen vor. Diese Aggregation von Aktionen kann ein allgemeines Regelwerk für, z. B. Routing, Zwischenspeichern und Autorisierung, global ermöglichen. Anforderungen werden mittels Routing Aktionen zugeordnet [routing](xref:mvc/controllers/routing).

Gemäß Konvention gilt für Controllerklassen Folgendes:
* Controllerklassen befinden sich im Ordner *Controllers* auf der Stammebene des Projekts.
*Controllerklassen erben von `Microsoft.AspNetCore.Mvc.Controller`.

Ein Controller ist eine instanziierbare Klasse, für die mindestens eine der folgenden Bedingungen "true" ist:
* Der Name der Klasse enthält das Suffix "Controller" 
* Die Klasse erbt von einer Klasse, die das Suffix "Controller" hat
* Die Klasse wurde mit dem `[Controller]`-Attribut definiert.

Einer Controllerklasse darf kein `[NonController]`-Attribut zugeordnet sein.

Domänencontroller sollten dem [expliziten Abhängigkeiten Prinzip](http://deviq.com/explicit-dependencies-principle/) folgen. Es gibt einige Ansätze für die Implementierung dieses Prinzips. Wenn mehrere Controlleraktionen denselben Dienst benötigen, sollten Sie [Konstruktor-Injection](xref:mvc/controllers/dependency-injection#constructor-injection) in Erwägung ziehen, um den Dienst anzufordern. Wenn ein Dienst nur für eine Aktions-Methode erforderlich ist, wäre [Aktion-Injection](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) das Mittel der Wahl, zur Anforderung des Dienstes.

Innerhalb des **M**odel -**V**iew -**C**ontroller-Patterns, ist ein Controller verantwortlich für die initiale Verarbeitung der Anforderung und die Instanziierung des Modells. Im Allgemeinen sollten inhaltliche Entscheidungen innerhalb des Modells vorgenommen werden.

Der Controller nutzt das Ergebnis der Verarbeitung des Modells, (sofern vorhanden) und gibt die richtige Ansicht und deren zugeordneten Daten oder das Ergebnis des API-Aufrufs. Weitere Informationen zu [Übersicht über ASP.NET Core MVC](xref:mvc/overview) und [erste Schritte mit ASP.NET Core MVC und Visual Studio](xref:tutorials/first-mvc-app/start-mvc).

Der Controller ist ein *-Benutzeroberflächenebene* Abstraktion. Ihren Aufgaben sind, um sicherzustellen, dass Anforderungsdaten gültig ist und auswählen, welche Ansicht (oder das Ergebnis für eine API) zurückgegeben werden sollen. In gut ausgearbeitete apps ist es nicht direkt Daten zugreifen oder die Geschäftslogik enthalten. Stattdessen werden der Controller an Diensten behandeln diese Aufgaben delegiert.

## <a name="defining-actions"></a>Definieren von Aktionen

Öffentliche Methoden auf einem Domänencontroller, außer denen mit ergänzt die `[NonAction]` -Attribut angegeben wird, werden die Aktionen. Parameter für Aktionen Anfordern von Daten gebunden werden und werden überprüft mithilfe von [modellbindung](xref:mvc/models/model-binding). Eine modellvalidierung erfolgt für alle Elemente, das Modell gebunden ist. Die `ModelState.IsValid` Eigenschaftswert angibt, ob die modellbindung und Überprüfung war erfolgreich.

Aktionsmethoden sollte die Logik für die Zuordnung von einer Anforderungs zu Besorgnis Business enthalten. Geschäftsprobleme sollte als Dienste, die der Controller über zugreift i. d. r. dargestellt werden [Abhängigkeitsinjektion](xref:mvc/controllers/dependency-injection). Aktionen werden Anwendungsstatus ist dann das Ergebnis der Aktion Business zuordnen.

Aktionen können nichts zurück, aber häufig zurückgeben eine Instanz von `IActionResult` (oder `Task<IActionResult>` für asynchrone Methoden), die eine Antwort erzeugt. Die Aktionsmethode ist verantwortlich für die Auswahl *welche Art von Antwort*. Das Aktionsergebnis *ist der reagiert*.

### <a name="controller-helper-methods"></a>Controller-Hilfsmethoden

Domänencontroller in der Regel erben [Controller](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller), obwohl dies nicht erforderlich ist. Ableiten von `Controller` ermöglicht den Zugriff auf drei Kategorien von Hilfsmethoden:

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. Methoden, was zu einer leeren Antworttext

Nicht `Content-Type` HTTP-Antwortheader enthalten, ist, seit der Antworttext verfügt nicht über die Inhalte zu beschreiben.

Es gibt zwei Typen in dieser Kategorie: Umleitung und HTTP-Statuscode.

* **HTTP-Statuscode**

    Dieser Typ gibt einen HTTP-Statuscode zurück. Einige Hilfsmethoden dieses Typs sind `BadRequest`, `NotFound`, und `Ok`. Beispielsweise `return BadRequest();` erzeugt einen Statuscode "400" bei der Ausführung. Bei Methoden, z. B. `BadRequest`, `NotFound`, und `Ok` sind überladen sind, sollten sie nicht mehr gelten als Responder Statuscode "HTTP", da inhaltsaushandlung stattfindet.

* **Umleiten**

    Dieser Typ gibt eine Umleitung an eine Aktion oder ein Ziel zurück (mit `Redirect`, `LocalRedirect`, `RedirectToAction`, oder `RedirectToRoute`). Beispielsweise `return RedirectToAction("Complete", new {id = 123});` leitet an `Complete`, ein anonymes Objekt übergeben.

    Der Ergebnistyp für die Umleitung unterscheidet sich von der HTTP-Statuscode-Typ in erster Linie in das Hinzufügen einer `Location` HTTP-Antwortheader.

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. Methoden, die in einen nicht leeren Antworttext durch einen vordefinierten Inhaltstyp resultierende

Die meisten Hilfsmethoden in dieser Kategorie gehören eine `ContentType` Eigenschaft, die Sie festlegen, sodass die `Content-Type` Antwortheader zum Beschreiben des Antworttexts.

Es gibt zwei Typen in dieser Kategorie: [Ansicht](xref:mvc/views/overview) und [Antwort formatiert](xref:mvc/models/formatting).

* **Ansicht**

    Dieser Typ zurückgibt, eine Sicht, die ein Modell verwendet wird, um das Rendering von HTML. Beispielsweise `return View(customer);` übergibt ein Modell zur Ansicht für die Datenbindung.

* **Formatierte Antwort**

    Dieser Typ zurückgibt, JSON oder eine ähnliche Datenaustauschformat, um ein Objekt in einer bestimmten Weise darzustellen. Beispielsweise `return Json(customer);` serialisiert das angegebene Objekt in JSON-Format.
    
    Dieses Typs andere übliche Methoden umfassen `File`, `PhysicalFile`, und `VirtualFile`. Beispielsweise `return PhysicalFile(customerFilePath, "text/xml");` gibt eine XML-Datei beschrieben durch einen `Content-Type` Wert des Antwortheaders der "Text/Xml".

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. Methoden, die in einen nicht leeren Antworttext resultierenden in einen Inhaltstyp, der mit dem Client ausgehandelt formatiert

Diese Kategorie ist eine bessere Leistung als **Inhaltsaushandlung**. [Inhalts-Aushandlung](xref:mvc/models/formatting#content-negotiation) gilt immer, wenn eine Aktion gibt eine [ObjectResult](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.objectresult) Typ oder einen anderen Wert als eine [IActionResult](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.iactionresult) Implementierung. Eine Aktion, die ein nicht-gibt`IActionResult` Implementierung (z. B. `object`) gibt auch eine Antwort formatiert.

Einige Hilfsmethoden dieses Typs enthalten `BadRequest`, `CreatedAtRoute`, und `Ok`. Beispiele für diese Methoden sind `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);`, und `return Ok(value);`zugeordnet. Beachten Sie, dass `BadRequest` und `Ok` führen Sie die inhaltsaushandlung nur, wenn ein Wert übergeben, ohne einen Wert, sie stattdessen dienen als HTTP-Statuscode: Ergebnistypen. Die `CreatedAtRoute` -Methode, andererseits, immer inhaltsaushandlungen seit seiner Überladungen, die alle erfordern, dass ein Wert übergeben werden.

### <a name="cross-cutting-concerns"></a>Querschnittliche Sicherheitsrisiken

Anwendungen gemeinsam in der Regel Teile ihrer Workflows verwenden. Beispiele sind eine app, die eine Authentifizierung auf dem Einkaufswagen erforderlich ist, oder eine app, die Daten auf einige Seiten zwischengespeichert. Um die Logik vor oder nach einer Aktionsmethode ausführen, verwenden Sie eine *Filter*. Mit [Filter](xref:mvc/controllers/filters) querschnittliche Bedenken können Duplikate, die ihnen ermöglicht, führen reduziert den [Don't wiederholen selbst (trocken)-Prinzip](http://deviq.com/don-t-repeat-yourself/).

Die meisten Attribute, z. B. filtern `[Authorize]`, auf der Ebene Controller bzw. die Aktionsmethode, die je nach der gewünschten Ebene der Granularität angewendet werden können.

Fehlerbehandlung und Zwischenspeichern von Antworten sind häufig querschnittliche bedenken:
   * [Fehlerbehandlung](xref:mvc/controllers/filters#exception-filters)
   * [Zwischenspeichern von Antworten](xref:performance/caching/response)

Viele Aspekte der querschnittliche können verarbeitet werden, mithilfe von Filtern oder benutzerdefinierte [Middleware](xref:fundamentals/middleware).
