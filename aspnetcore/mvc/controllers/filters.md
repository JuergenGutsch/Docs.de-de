---
title: Filter
author: ardalis
description: Erfahren Sie, wie *Filter* funktionieren und wie sie in ASP.NET Core MVC verwenden.
ms.author: tdykstra
manager: wpickett
ms.date: 12/12/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/filters
ms.openlocfilehash: 34a5be6e77f8558c9b3c257575272e167ed95ea4
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="filters"></a>Filter

Durch [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)

*Filter* in ASP.NET Core MVC ermöglichen es Ihnen, Code vor oder nach bestimmten Stufen in der Verarbeitungspipeline Anforderung auszuführen.

 Integrierte Filter Handle Aufgaben wie die Autorisierung (verhindert den Zugriff auf Ressourcen, denen für ein Benutzer nicht autorisiert ist), wird sichergestellt, dass alle Anforderungen HTTPS- und Antwort zwischenspeichern (verkürzte der Anforderungspipeline zum Zurückgeben einer zwischengespeicherten Antwort) verwenden. 

Sie können benutzerdefinierte Filter zum Behandeln von querschnittliche Sicherheitsrisiken für Ihre Anwendung erstellen. Jedes Mal, wenn Sie vermeiden, Code über Aktionen duplizieren möchten, die Lösung von Filter angewendet wurden. Fehler bei der Verarbeitung von Code in einen Ausnahmefilter kann z. B. konsolidiert werden.

[Anzeigen oder Herunterladen des Beispiels aus GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

## <a name="how-do-filters-work"></a>Wie funktionieren Filter?

Filter ausgeführt wird, innerhalb der *MVC-Aktion Aufruf Pipeline*, manchmal als gemäß der *Filterpipeline*.  Die Filterpipeline ausgeführt wird, nachdem MVC wählt die Aktion zum Ausführen aus.

![Die Anforderung wird durch andere Middleware, Middleware-Routing, Aktionsauswahl und der MVC-Aktion-Aufruf-Pipeline verarbeitet. Die Verarbeitung der Anforderung weiterhin zurückverfolgen Aktionsauswahl Middleware Routing und verschiedene andere Middleware vor immer eine Antwort an den Client gesendet.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Filtertypen

Jedes Filtertyps bestimmt, die zu einem anderen Zeitpunkt in der Filterpipeline ausgeführt wird.

* [Autorisierungsfilter](#authorization-filters) zuerst ausgeführt und werden verwendet, um zu bestimmen, ob der aktuelle Benutzer für die aktuelle Anforderung autorisiert ist. Sie können die Pipeline Kurzschluss, wenn eine Anforderung nicht autorisiert ist. 

* [Ressourcenfilter](#resource-filters) werden als erste eine Anfrage nach der Autorisierung behandelt.  Sie können Code vor dem Rest der Filterpipeline und nach Abschluss der Rest der Pipeline ausgeführt. Sie sind hilfreich beim Zwischenspeichern der Dienst implementieren oder andernfalls Kurzschluss Filterpipeline zur Verbesserung der Leistung. Da sie vor der modellbindung ausgeführt werden, sind sie nützlich für Elemente, die beeinflussen, wurden die modellbindung muss.

* [Aktionsfilter](#action-filters) Code ausführen können, unmittelbar vor und nach eine einzelnen Aktionsmethode aufgerufen wird. Sie können zum Bearbeiten von den Argumenten, die an eine Aktion übergeben und von der Aktion zurückgegebenen Ergebnisses verwendet werden.

* [Ausnahmefilter](#exception-filters) werden verwendet, um globale Richtlinien auf nicht behandelte Ausnahmen angewendet, die auftreten, bevor alle Elemente in den Antworttext geschrieben wurde.

* [Führen Sie die Filter](#result-filters) unmittelbar vor und nach der Ausführung der einzelnen Aktionsergebnisse Code ausführen können. Nur ausgeführt, wenn die Aktionsmethode erfolgreich ausgeführt wurde, und eignen sich für eine Logik, die Sicht oder Formatierer Ausführung umgeben muss.

Das folgende Diagramm zeigt, wie diese Filtertypen in der Filterpipeline interagieren.

![Die Anforderung wird über den Autorisierungsfilter, Ressourcenfilter Modell binden, Aktionsfilter, Ausführen von Aktionen und Aktion Ergebniskonvertierung, Ausnahmefilter, Ergebnisfilter und Ergebnis Ausführung verarbeitet. Auf dem Weg heraus wird die Anforderung nur durch Ergebnisfilter und Ressourcenfilter verarbeitet, bevor eine Antwort an den Client gesendet wird.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementierung

Filter unterstützen die synchrone und asynchrone Implementierungen über verschiedene Schnittstellendefinitionen. Wählen Sie die Synchronisierung oder die Async-Variante je nach Art der Aufgabe, die Sie ausführen müssen. 

Synchrone Filter, die ausgeführt werden können, code sowohl vor und nach ihren Pipelinestufe für definieren*Phase*ausführen und auf*Phase*Methoden ausgeführt. Beispielsweise `OnActionExecuting` wird aufgerufen, bevor die Aktionsmethode aufgerufen wird, und `OnActionExecuted` aufgerufen, nachdem die Aktionsmethode beendet.

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?highlight=6,8,13)]

Asynchrone Filter definieren eine einzelne auf*Phase*ExecutionAsync-Methode. Diese Methode nimmt einen *FilterType*ExecutionDelegate-Delegat, der den Filter Pipelinestufe "" ausgeführt wird. Beispielsweise `ActionExecutionDelegate` Aufrufe, die die Aktionsmethode, und Sie führen können code vor und nach dem Sie aufgerufen werden.

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleAsyncActionFilter.cs?highlight=6,8-10,13)]

Sie können für mehrere Filter Stufen in einer einzelnen Klasse Schnittstellen implementieren. Z. B. die [ActionFilterAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute) abstrakten Klasse implementiert beide `IActionFilter` und `IResultFilter`, sowie deren asynchrone Entsprechungen.

> [!NOTE]
> Implementieren **entweder** synchronen oder asynchronen-Version einer Filter-Schnittstelle, nicht beides. Das Framework überprüft zuerst, um festzustellen, ob der Filter die Async-Schnittstelle implementiert, und wenn dies der Fall ist, wird. Wenn dies nicht der Fall ist, ruft er die synchrone Schnittstelle Methode(n). Würden Sie beide Schnittstellen für eine Klasse implementieren, würde die Async-Methode aufgerufen werden. Bei Verwendung von abstrakten Klassen wie [ActionFilterAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute) würden Sie nur die synchronen Methoden oder die asynchrone Methode für jedes Filtertyps bestimmt.

### <a name="ifilterfactory"></a>IFilterFactory

`IFilterFactory` implementiert `IFilter`. Aus diesem Grund eine `IFilterFactory` Instanz kann verwendet werden, als ein `IFilter` Instanz an einer beliebigen Stelle in der Filterpipeline. Wenn das Framework vorbereitet, um den Filter aufzurufen, versucht es,-Typ umzuwandeln ein `IFilterFactory`. Wenn diese Umwandlung erfolgreich ist, die `CreateInstance` Methode wird aufgerufen, um das Erstellen der `IFilter` -Instanz, die aufgerufen wird. Dies bietet ein sehr flexibles Design, da die präzise Filterpipeline muss nicht explizit festgelegt werden, wenn die Anwendung gestartet wird.

Sie können implementieren `IFilterFactory` auf Ihren eigenen Implementierungen Attribut als ein weiteres Verfahren zum Erstellen von Filtern:

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

### <a name="built-in-filter-attributes"></a>Integrierter Filter-Attribute

Das Framework beinhaltet integrierte attributbasierte Filter, können Sie die Unterklasse und anpassen. Die folgenden Ergebnisfilter fügt z. B. einen Header in die Antwort an.

<a name="add-header-attribute"></a>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/AddHeaderAttribute.cs?highlight=5,16)]

Attribute können Filter aus, die Argumente akzeptieren, wie im obigen Beispiel gezeigt. Sie fügen Sie dieses Attribut auf einen Controller oder Aktion, und geben Sie den Namen und Wert des HTTP-Headers:

[!code-csharp[Main](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

Das Ergebnis der `Index` Aktion wird unten gezeigt – die Antwortheader werden auf der unteren rechten Ecke angezeigt.

![Developer Tools von Microsoft Edge mit Antwortheadern, einschließlich der Autor Steve Smith@ardalis](filters/_static/add-header.png)

Bei einigen filterschnittstellen können die entsprechenden Attribute, die als Basisklassen für benutzerdefinierte Implementierungen verwendet werden können.

Filtern Sie Attribute:

* `ActionFilterAttribute`
* `ExceptionFilterAttribute`
* `ResultFilterAttribute`
* `FormatFilterAttribute`
* `ServiceFilterAttribute`
* `TypeFilterAttribute`

`TypeFilterAttribute`und `ServiceFilterAttribute` werden erläutert [weiter unten in diesem Artikel](#dependency-injection).

## <a name="filter-scopes-and-order-of-execution"></a>Filtern Sie Bereiche und Reihenfolge der Ausführung

Filter kann hinzugefügt werden, an die Pipeline an einem von drei *Bereiche*. Sie können einen Filter auf eine bestimmte Aktion-Methode oder zu einer Controllerklasse mit einem Attribut hinzufügen. Oder Sie können einen Filter Global (für alle Controller und Aktionen) registrieren, durch Hinzufügen zu den `MvcOptions.Filters` Sammlung in der `ConfigureServices` Methode in der `Startup` Klasse:

[!code-csharp[Main](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

### <a name="default-order-of-execution"></a>Standardreihenfolge der Ausführung

Wenn mehrere Filter für eine bestimmte Phase der Pipeline sind, bestimmt der Bereich die Standardreihenfolge der filterausführung.  Globale Filter umschließen Klasse Filter, mit die-Methode filtert wiederum zu umschließen. Dies wird manchmal als bezeichnet "Russisch Puppe" Verschachteln, wie jede Erweiterung des Bereichs z. B. den vorherigen Bereich umbrochen wird eine [schachteln Puppe](https://wikipedia.org/wiki/Matryoshka_doll). Sie erhalten im Allgemeinen das gewünschte Verhalten für die überschreibende ohne explizit Reihenfolge zu bestimmen.

Als Ergebnis dieser Schachtelung der *nach* Code Filter wird in umgekehrter Reihenfolge ausgeführt der *vor* Code. Die Abfolge sieht wie folgt:

* Die *vor* Code Global angewendeten Filter
  * Die *vor* Code auf Domänencontrollern angewendeten Filter
    * Die *vor* Code auf Aktionsmethoden angewendeten Filter
    * Die *nach* Code auf Aktionsmethoden angewendeten Filter
  * Die *nach* Code auf Domänencontrollern angewendeten Filter
* Die *nach* Code Global angewendeten Filter
  
Hier ist ein Beispiel zur Veranschaulichung die Reihenfolge in den richtigen, die Filter Methoden für synchrone Aktionsfilter aufgerufen werden.

| Sequenz | Filterbereich | Filter-Methode |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | Controller | `OnActionExecuting` |
| 3 | Methode | `OnActionExecuting` |
| 4 | Methode | `OnActionExecuted` |
| 5 | Controller | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

Diese Sequenz zeigt, dass der Filter für die Methode innerhalb des Filters Controller geschachtelt ist und der Controller-Filter in der globalen Filter geschachtelt ist. Versetzen sie eine andere Weise, wenn Sie innerhalb einer Async-Filter sind des auf*Phase*ExecutionAsync-Methode, alle Filter mit einem engeren Bereich ausführen, während der Code auf dem Stapel ist.

> [!NOTE]
> Jeder Controller, die von erben die `Controller` Basisklasse enthält `OnActionExecuting` und `OnActionExecuted` Methoden. Diese Methoden umschließen die Filter für eine bestimmte Aktion ausführen: `OnActionExecuting` wird aufgerufen, bevor die Filter und `OnActionExecuted` wird aufgerufen, nachdem alle Filter.

### <a name="overriding-the-default-order"></a>Die Standardreihenfolge überschreiben

Sie können die Standardabfolge der Ausführung durch die Implementierung überschreiben `IOrderedFilter`. Diese Schnittstelle legt eine `Order` -Eigenschaft, die Vorrang vor Bereich, um die Reihenfolge der Ausführung zu ermitteln. Ein Filter mit einem niedrigeren `Order` liefern, dessen *vor* vor, einen Filter mit einem höheren Wert der ausgeführte Code `Order`. Ein Filter mit einem niedrigeren `Order` liefern, dessen *nach* Code ausgeführt, nachdem der Filter mit einer höheren `Order` Wert. Sie können festlegen, die `Order` Eigenschaft mit einem Konstruktorparameter:

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Ist gleich 3 gezeigt, in dem vorhergehenden Beispiel, aber Satz Aktionsfilter der `Order` -Eigenschaft des Controllers und globalen Filter auf 1 und 2, die Reihenfolge der Ausführung würde rückgängig gemacht werden kann.

| Sequenz | Filterbereich | `Order`-Eigenschaft | Filter-Methode |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Methode | 0 | `OnActionExecuting` |
| 2 | Controller | 1  | `OnActionExecuting` |
| 3 | Global | 2  | `OnActionExecuting` |
| 4 | Global | 2  | `OnActionExecuted` |
| 5 | Controller | 1  | `OnActionExecuted` |
| 6 | Methode | 0  | `OnActionExecuted` |

Die `Order` Eigenschaft sticht Bereich aus, wenn die Reihenfolge bestimmt, in dem Filter ausgeführt werden. Filter werden zuerst nach Reihenfolge sortiert, und Bereich wird verwendet, um Ties zu unterbrechen. Alle integrierten Filter implementieren `IOrderedFilter` und Festlegen der `Order` Wert auf 0, damit Bereich Reihenfolge bestimmt, es sei denn, Sie legen `Order` auf einen Wert ungleich 0 (null).

## <a name="cancellation-and-short-circuiting"></a>Abbruch und Kurzschlussoperationen

Sie können die Filterpipeline zu einem beliebigen Zeitpunkt Kurzschluss, durch Festlegen der `Result` Eigenschaft auf die `context` Parameter bereitgestellt, um die Filtermethode. Beispielsweise verhindert, dass die folgende Filter für die Ressource den Rest der Pipeline ausführen.

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?highlight=12,13,14,15)]

Im folgenden Code wird sowohl die `ShortCircuitingResourceFilter` und `AddHeader` Filterziel der `SomeResource` Aktionsmethode. Allerdings da der `ShortCircuitingResourceFilter` zuerst ausgeführt (da es sich um einen Filter für die Ressource ist und `AddHeader` ist ein Aktionsfilter) und kurzgeschlossen wird der Rest der Pipeline an, der `AddHeader` Filter nie ausgeführt wird, für die `SomeResource` Aktion. Dieses Verhalten wäre identisch, wenn beide Filter auf Methodenebene Aktion bereitgestellten angewendet wurden die `ShortCircuitingResourceFilter` zuerst ausgeführt (aufgrund seines Filtertyps für oder explizite Verwendung von `Order` -Eigenschaft, für die Instanz).

[!code-csharp[Main](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Abhängigkeitsinjektion

Filter können nach Typ oder nach Instanz hinzugefügt werden. Wenn Sie eine Instanz hinzufügen, wird diese Instanz für jede Anforderung verwendet werden. Wenn Sie einen Typ hinzufügen, werden sie Typ aktiviert, d. h. für jede Anforderung eine Instanz erstellt werden, und alle Abhängigkeiten Konstruktor werden von aufgefüllt [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md) (DI). Hinzufügen eines Filters nach Typ entspricht dem `filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`.

Filter, die als Attribute implementiert und direkt zu Controllerklassen oder Aktionsmethoden hinzugefügt sind keine Konstruktor Abhängigkeiten gebotenen [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md) (DI). Dies ist die Schreibberechtigung Attribute müssen ihre Konstruktorparameter bereitgestellt, in dem sie angewendet werden. Dies ist eine Einschränkung der Funktionsweise der Attribute.

Wenn die Filter Abhängigkeiten, die Sie von DI zugreifen müssen haben, sind mehrere Ansätze. Sie können den Filter auf eine Klasse oder eine Aktion-Methode, die mit einer der folgenden anwenden:

* `ServiceFilterAttribute`
* `TypeFilterAttribute`
* `IFilterFactory`auf Ihrem Attribut implementiert

> [!NOTE]
> Eine Abhängigkeit, die Sie möglicherweise aus DI abrufen möchten, ist eine Protokollierung. Allerdings zu vermeiden, erstellen und verwenden Filter ausschließlich für die Protokollierung, da der [integriertes Framework Protokollierungsfunktionen](xref:fundamentals/logging/index) müssen Sie möglicherweise bereits bereitstellen. Wenn Sie Protokollierung Ihre Filter hinzufügen möchten, sollten diese auf Geschäftsprobleme Domäne oder Verhalten, die spezifisch für Ihren Filter statt MVC-Aktionen oder andere Framework-Ereignisse konzentrieren.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Ein `ServiceFilter` Ruft eine Instanz des Filters aus DI ab. Sie den Filter hinzufügen, auf den Container in `ConfigureServices`, und verweisen sie in einem `ServiceFilter` Attribut

[!code-csharp[Main](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=11)]

[!code-csharp[Main](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Mithilfe von `ServiceFilter` ohne Registrierung die Filterergebnisse des Typs eine Ausnahme ausgelöst:

```
System.InvalidOperationException: No service for type
'FiltersSample.Filters.AddHeaderFilterWithDI' has been registered.
```

`ServiceFilterAttribute`implementiert `IFilterFactory`, das verfügbar macht einer einzelnen Methode zum Erstellen einer `IFilter` Instanz. Im Fall von `ServiceFilterAttribute`, `IFilterFactory` Schnittstelle `CreateInstance` Methode wird implementiert, um den angegebenen Typ aus der Dienstecontainer (DI) zu laden.

### <a name="typefilterattribute"></a>TypeFilterAttribute

`TypeFilterAttribute`vergleichbar mit `ServiceFilterAttribute` (und implementiert außerdem `IFilterFactory`), aber sein Typ nicht direkt aus dem Container DI aufgelöst. Stattdessen den Typ instanziiert, mithilfe von `Microsoft.Extensions.DependencyInjection.ObjectFactory`.

Wegen dieses Unterschieds, Typen, auf die verwiesen werden mithilfe der `TypeFilterAttribute` müssen mit dem Container erstmals registriert werden (aber dennoch haben Sie ihre Abhängigkeiten, die vom Container erfüllt). Darüber hinaus `TypeFilterAttribute` Konstruktorargumente für den betreffenden Typ kann optional akzeptieren. Im folgenden Beispiel wird veranschaulicht, wie Argumente übergeben werden, in einen Typ mit `TypeFilterAttribute`:

[!code-csharp[Main](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

Wenn Sie einen Filter, die keine Argumente erfordert, aber dem Konstruktor Abhängigkeiten aufweist, die mit DI ausgefüllt werden müssen, Sie Ihr eigenes benannten Attribut zu Klassen und Methoden anstelle der können `[TypeFilter(typeof(FilterType))]`). Die folgende Filter wird gezeigt, wie dies implementiert werden kann:

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Dieser Filter kann auf Klassen oder Methoden angewendet werden die `[SampleActionFilter]` -Syntax zu verwenden, anstatt `[TypeFilter]` oder `[ServiceFilter]`.

## <a name="authorization-filters"></a>Autorisierungsfilter

*Autorisierungsfilter* steuern den Zugriff auf Aktionsmethoden und sind von den ersten Filter in der Filterpipeline ausgeführt werden. Sie müssen nur eine vor der Methode, im Gegensatz zu den meisten Filter, die vor und nach Methoden unterstützen. Wenn Sie eine eigene Autorisierungsframework schreiben, sollten Sie nur einem benutzerdefinierten Autorisierungs-Filter schreiben. Bevorzugen Sie Ihre Autorisierungsrichtlinien konfigurieren, oder schreiben eine benutzerdefinierte Autorisierungsrichtlinie über das Schreiben eines benutzerdefinierten Filters. Die integrierten filterimplementierung ist nur für das Aufrufen der Autorisierungssystem zuständig.

Beachten Sie, dass Sie sollten nicht innerhalb Autorisierungsfilter, Ausnahmen da nichts wird die Ausnahme zu behandeln (Ausnahmefilter wird nicht behandelt werden). Stattdessen geben Sie eine neue Herausforderung dar oder finden Sie eine andere Möglichkeit.

Erfahren Sie mehr über [Autorisierung](../../security/authorization/index.md).

## <a name="resource-filters"></a>Ressource filtern

*Ressourcenfilter* implementieren entweder die `IResourceFilter` oder `IAsyncResourceFilter` -Schnittstelle, und ihre Ausführung dient als Wrapper für die meisten der Filterpipeline. (Nur [Autorisierungsfilter](#authorization-filters) vorgestellten ausführen.) Ressourcenfilter sind besonders hilfreich, wenn müssen Sie die meiste Arbeit Kurzschluss, die eine Anforderung ausführt. Beispielsweise kann ein Zwischenspeichern Filter den Rest der Pipeline vermeiden, wenn die Antwort bereits im Cache befindet.

Die [kurze circuiting Ressourcenfilter](#short-circuiting-resource-filter) weiter oben dargestellten ist ein Beispiel für einen Ressourcenfilter. Ein weiteres Beispiel ist [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs), was verhindert wurden die modellbindung Zugriff auf die Daten des Formulars. Es eignet sich für Fälle, in dem Sie wissen, dass Sie die offensichtlichen große Dateiuploads erhalten und um zu verhindern, dass das Formular in den Arbeitsspeicher gelesen werden sollen.

## <a name="action-filters"></a>Aktionsfilter

*Aktionsfilter* implementieren entweder die `IActionFilter` oder `IAsyncActionFilter` -Schnittstelle, und ihre Ausführung schließt die Ausführung von Aktionsmethoden.

So sieht ein Aktionsfilter Beispiel aus:

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet_ActionFilter)]

Die [ActionExecutingContext](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionexecutingcontext) stellt die folgenden Eigenschaften:

* `ActionArguments`-können Sie die Eingaben für die Aktion zu bearbeiten.
* `Controller`-können Sie die Controller-Instanz zu bearbeiten. 
* `Result`-Einstellung kurzgeschlossen Ausführung der Aktionsmethode und den nachfolgenden Aktionsfilter verwendet werden wird. Eine Ausnahme verhindert die Ausführung der Aktionsmethode und den nachfolgenden Filter auch wird als Fehler anstatt ein erfolgreiches Ergebnis behandelt.

Die [ActionExecutedContext](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.filters.actionexecutedcontext) bietet `Controller` und `Result` plus die folgenden Eigenschaften:

* `Canceled`-"true", wenn die aktionsausführung durch einen anderen Filter kurzgeschlossen wurde.
* `Exception`-ungleich Null ist, werden, wenn die Aktion oder einen Aktionsfilter für die nachfolgende eine Ausnahme ausgelöst hat. Durch Festlegen dieser Eigenschaft auf null effektiv eine Ausnahme "handles", und `Result` wird ausgeführt, als hätte es normalerweise von der Aktionsmethode zurückgegeben wurden.

Für eine `IAsyncActionFilter`, einen Aufruf der `ActionExecutionDelegate` führt alle nachfolgenden Aktionsfilter verwendet werden und die Aktionsmethode Zurückgeben einer `ActionExecutedContext`. Weisen Sie zum Circuit `ActionExecutingContext.Result` auf einige Ergebnis-Instanz und rufen Sie nicht die `ActionExecutionDelegate`.

Das Framework bietet eine abstrakte `ActionFilterAttribute` , können Sie die Unterklasse. 

Einen Aktionsfilter können Sie automatisch Modellstatus überprüfen und alle Fehler zurück, wenn der Status ungültig ist:

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/ValidateModelAttribute.cs)]

Die `OnActionExecuted` Methode ausgeführt wird, nachdem die Aktionsmethode und kann anzeigen und bearbeiten Sie die Ergebnisse der Aktion durch die `ActionExecutedContext.Result` Eigenschaft. `ActionExecutedContext.Canceled`wird auf "true", wenn die aktionsausführung durch einen anderen Filter kurzgeschlossen wurde festgelegt. `ActionExecutedContext.Exception`wird auf einen Wert ungleich Null festgelegt werden, wenn die Aktion oder einen Aktionsfilter für die nachfolgende eine Ausnahme ausgelöst hat. Festlegen von `ActionExecutedContext.Exception` effektiv NULL ' "eine Ausnahme behandelt, und `ActionExectedContext.Result` wird dann ausgeführt werden, als wäre es normalerweise von der Aktionsmethode zurückgegeben wurden.

## <a name="exception-filters"></a>Ausnahmefilter

*Ausnahmefilter* implementieren entweder die `IExceptionFilter` oder `IAsyncExceptionFilter` Schnittstelle. Sie können verwendet werden, um häufige Fehler bei der Verarbeitung von Richtlinien für eine app zu implementieren. 

Das folgende Beispiel Ausnahmefilter verwendet eine benutzerdefinierte Developer Fehleransicht zum Anzeigen von Details über Ausnahmen, die auftreten, wenn die Anwendung in der Entwicklung befindet:

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=1,14)]

Ausnahmefilter keine zwei Ereignisse (vor und nach) – nur implementierten hierarchieentwurfs `OnException` (oder `OnExceptionAsync`). 

Ausnahmefilter nicht behandelte Ausnahmen behandelt, die auftreten, im Controller-Erstellung [modellbindung](../models/model-binding.md), Aktionsfilter oder Aktionsmethoden. Sie wird nicht Abfangen von Ausnahmen, die in der Ressourcenfilter, Ergebnisfilter oder Ausführung von MVC-Ergebnis auftreten.

Um eine Ausnahme zu behandeln, legen Sie die `ExceptionContext.ExceptionHandled` Eigenschaft auf "true" oder eine Antwort zu schreiben. Dadurch wird die Weitergabe der Ausnahme beendet. Beachten Sie, dass ein Ausnahmefilter eine Ausnahme in "Erfolg" nicht aktivieren können. Nur ein Aktionsfilter kann dies tun.

> [!NOTE]
> In ASP.NET 1.1 ist nicht die Antwort gesendet, wenn Sie festlegen, `ExceptionHandled` auf "true" **und** eine Antwort zu schreiben. In diesem Szenario ASP.NET Core 1.0 sendet die Antwort und ASP.NET Core 1.1.2 1.0 Verhalten zurück. Weitere Informationen finden Sie unter [ausstellen #5594](https://github.com/aspnet/Mvc/issues/5594) im GitHub-Repository. 

Ausnahmefilter eignen sich für Auffangen von Ausnahmen, die in MVC Aktionen auftreten, aber sie sind nicht so flexibel wie Middleware für die Fehlerbehandlung. Middleware für die allgemeine Groß-/Kleinschreibung vorziehen, und verwenden Sie Filter, in denen Sie nur müssen Vorgehensweise Fehlerbehandlung *anders* basierend auf die MVC-Aktion ausgewählt wurde. Z. B. möglicherweise die app Aktionsmethoden für beide-API-Endpunkte und Ansichten/HTML. Die API-Endpunkte können Fehlerinformationen als JSON, zurückgeben, während die Ansicht basierende Aktionen als HTML-Fehlerseite zurückgeben können.

Das Framework bietet eine abstrakte `ExceptionFilterAttribute` , können Sie die Unterklasse. 

## <a name="result-filters"></a>Ergebnisfilter

*Führen Sie die Filter* implementieren entweder die `IResultFilter` oder `IAsyncResultFilter` -Schnittstelle, und ihre Ausführung schließt die Ausführung von Aktionsergebnisse. 

Hier ist ein Beispiel für einen Ergebnisfilter, der einen HTTP-Header hinzufügt.

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Die Art von Ergebnis, die gerade ausgeführt wird, hängt von der betreffenden Aktion ab. Eine MVC-Aktion, die eine Sicht zurückgeben zählen alle Razor, die im Rahmen der Verarbeitung der `ViewResult` ausgeführt wird. Eine API-Methode möglicherweise einige Serialisierung als Teil der Ausführung des Ergebnisses ausführen. Erfahren Sie mehr über [Aktionsergebnisse](actions.md)

Ergebnisfilter sind nur für erfolgreiche Ergebnisse - ausgeführt, wenn die Aktion oder ein Aktionsfilter ein Aktionsergebnis erzeugen. Ergebnisfilter werden nicht ausgeführt, wenn Ausnahmefilter eine Ausnahme zu behandeln.

Die `OnResultExecuting` Methode kann Ausführung der Aktionsergebnis und die nachfolgenden Ergebnisfilter durch Festlegen von Kurzschluss `ResultExecutingContext.Cancel` auf "true". Sie sollten im Allgemeinen auf dem Antwortobjekt schreiben, wenn Kurzschlussverhalten, um zu vermeiden, Erstellen einer leeren Antwort. Auslösen einer Ausnahme wird außerdem verhindert, dass Ausführung der Aktionsergebnis und die nachfolgenden Filter, jedoch wird als Fehler anstatt ein erfolgreiches Ergebnis behandelt werden.

Wenn die `OnResultExecuted` Methode ausgeführt wird, die Antwort wahrscheinlich an den Client gesendet wurde und nicht weiter geändert werden kann (es sei denn, eine Ausnahme ausgelöst wurde). `ResultExecutedContext.Canceled`wird auf "true", wenn die Ausführung der Aktion Ergebnis durch eine andere Filter kurzgeschlossen wurde festgelegt.

`ResultExecutedContext.Exception`wird auf einen Wert ungleich Null festgelegt werden, wenn das Aktionsergebnis oder einen nachfolgenden Ergebnisfilter eine Ausnahme ausgelöst hat. Festlegen von `Exception` auf Null effektiv "handles" eine Ausnahme und verhindert, dass die Ausnahme wird erneut ausgelöst von MVC weiter unten in der Pipeline. Wenn Sie eine Ausnahme in einen Ergebnisfilter verarbeiten, können Sie keine Daten in die Antwort geschrieben sein. Wenn das Aktionsergebnis wird durch die Ausführung eines ausgelöst und die Header an den Client bereits gelöscht wurden, ist keine zuverlässige Mechanismus zum Senden eines Fehlercode.

Für eine `IAsyncResultFilter` einen Aufruf von `await next()` auf die `ResultExecutionDelegate` führt alle nachfolgenden Ergebnisfilter und das Aktionsergebnis. Zu umgehen, legen Sie `ResultExecutingContext.Cancel` auf "true", und rufen Sie nicht die `ResultExectionDelegate`.

Das Framework bietet eine abstrakte `ResultFilterAttribute` , können Sie die Unterklasse. Die [AddHeaderAttribute](#add-header-attribute) weiter oben dargestellten Klasse ist ein Beispiel für ein Filterattribut Ergebnis.

## <a name="using-middleware-in-the-filter-pipeline"></a>Mithilfe der Middleware in der Filterpipeline

Ressourcenfilter funktionieren wie [Middleware](../../fundamentals/middleware.md) dahingehend, dass sie die Ausführung aller Elemente umschließen, der weiter unten in der Pipeline enthalten ist. Aber Filter unterscheiden sich von der Middleware, da Inhaltspakete Teil von MVC, sind dies bedeutet, dass sie Zugriff auf die MVC-Kontext und Konstrukte haben.

In ASP.NET Core 1.1 können Sie die Middleware in der Filterpipeline verwenden. Sie möchten nicht, wenn Sie eine middlewarekomponente verfügen, die benötigt Zugriff auf die MVC-Routendaten oder eine, die nur für bestimmte Domänencontroller oder Aktionen ausgeführt werden soll.

Um Middleware als Filter verwenden, erstellen Sie einen Typ mit einer `Configure` Methode, die die Middleware angibt, die Sie in der Filterpipeline einfügen möchten. Hier ist ein Beispiel, das die Middleware für die Lokalisierung verwendet wird, um die aktuelle Kultur für eine Anforderung einzurichten:

[!code-csharp[Main](./filters/sample/src/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

Anschließend können Sie die `MiddlewareFilterAttribute` die Middleware für einen ausgewählten Controller bzw. die Aktionsmethode ausgeführt oder global:

[!code-csharp[Main](./filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Führen Sie auf der gleichen Stufe der Filterpipeline als Ressourcenfilter vor der modellbindung und nach den Rest der Pipeline Middleware-Filter.

## <a name="next-actions"></a>Nächste Schritte

Experimentieren Sie mit Filtern, [herunterladen, testen und ändern Sie das Beispiel](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).
