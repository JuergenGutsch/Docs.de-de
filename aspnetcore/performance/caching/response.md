---
title: Zwischenspeichern von Antworten in ASP.NET Core
author: rick-anderson
description: "Informationen Sie zum Verwenden von caching zu niedrigeren bandbreitenanforderungen Antwort und erhöhen Sie der Leistung von ASP.NET Core-apps."
manager: wpickett
ms.author: riande
ms.date: 09/20/2017
ms.prod: asp.net-core
ms.topic: article
uid: performance/caching/response
ms.openlocfilehash: c38f9b9a1bf1c523951e2cf1f3070858fe5daf04
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2018
---
# <a name="response-caching-in-aspnet-core"></a>Zwischenspeichern von Antworten in ASP.NET Core

Durch [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), und [Luke Latham](https://github.com/guardrex)

> [!NOTE]
> Zwischenspeichern von Antworten [wird nicht unterstützt in Razor-Pages mit ASP.NET Core 2.0](https://github.com/aspnet/Mvc/issues/6437). Diese Funktion wird in unterstützt die [Version 2.1 von ASP.NET Core](https://github.com/aspnet/Home/wiki/Roadmap).
  
[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/sample) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

Zwischenspeichern von Antworten verringert die Anzahl der Anforderungen, die ein Client oder Proxy auf einem Webserver vornimmt. Zwischenspeichern von Antworten auch reduziert die Menge der Arbeit der Webserver durchführt, um eine Antwort zu generieren. Zwischenspeichern von Antworten wird durch Header gesteuert, die angeben, wie der Client und Proxy-Middleware zum Zwischenspeichern von Antworten soll.

Die Webserver kann Antworten zwischenspeichern, beim Hinzufügen [Antwort zwischenspeichern Middleware](xref:performance/caching/middleware).

## <a name="http-based-response-caching"></a>Zwischenspeichern von Antworten HTTP-basierte

Die [Zwischenspeichern von HTTP 1.1-Spezifikation](https://tools.ietf.org/html/rfc7234) wird beschrieben, wie Internet Caches Verhalten soll. Ist der primäre HTTP-Header, die zum Zwischenspeichern verwendeten [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), der verwendet wird, geben Sie den Cache *Direktiven*. Die Direktiven steuern Verhalten beim Zwischenspeichern, wie Anforderungen wie von Clients an Server senden und eine Antwort von Servern an Clients die Möglichkeit. Anforderungen und Antworten verschieben, über Proxy-Server und Proxy-Server müssen auch das Zwischenspeichern von HTTP 1.1-Spezifikation entsprechen.

Allgemeine `Cache-Control` Direktiven sind in der folgenden Tabelle gezeigt.

| Direktive                                                       | Aktion |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Ein Cache möglicherweise die Antwort zu speichern. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | Die Antwort muss von einem geteilten Datencache nicht gespeichert werden. Ein privater Cache möglicherweise speichern und wiederverwenden die Antwort. |
| [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | Der Client wird nicht als eine Antwort nicht akzeptiert, deren Alter größer als die angegebene Anzahl von Sekunden ist. Beispiele: `max-age=60` (60 Sekunden), `max-age=2592000` (1 Monat) |
| [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **Für Anforderungen**: ein Caches muss nicht gespeicherte Antwort zum Erfüllen der Anforderung verwenden. Hinweis: Ursprungsservers wird erneut die Antwort für den Client generiert und die Middleware aktualisiert die gespeicherte Antwort in seinem Cache.<br><br>**Auf Antworten**: die Antwort dürfen nicht für eine nachfolgende Anforderung ohne Überprüfung auf dem Ursprungsserver verwendet werden. |
| [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **Für Anforderungen**: Speichern ein Caches muss nicht die Anforderung.<br><br>**Auf Antworten**: ein Caches muss einen beliebigen Teil der Antwort nicht speichern. |

Andere Cacheheader, die eine Rolle am caching spielen sind in der folgenden Tabelle gezeigt.

| Header                                                     | Funktion |
| ---------------------------------------------------------- | -------- |
| [ALTER](https://tools.ietf.org/html/rfc7234#section-5.1)     | Eine Schätzung der die Zeitdauer in Sekunden seit die Antwort generiert wurde, oder auf dem Ausgangsserver erfolgreich überprüft. |
| [Läuft ab](https://tools.ietf.org/html/rfc7234#section-5.3) | Das Datum/Uhrzeit, nach dem die Antwort als veraltet angesehen wird. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Vorhanden ist, für die Kompatibilität mit HTTP/1.0 Abwärtskompatibilität Einstellung zwischenspeichert `no-cache` Verhalten. Wenn die `Cache-Control` Header vorhanden ist, ist die `Pragma` -Header wird ignoriert. |
| [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Gibt an, dass eine zwischengespeicherte Antwort nicht, wenn alle gesendet werden muss von der `Vary` Headerfelder entsprechen, in die zwischengespeicherte Antwort ursprüngliche Anforderung und die neue Anforderung. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>HTTP-basierte Zwischenspeichern Hinsicht anfordern cachesteuerungsdirektiven

Die [Zwischenspeichern von HTTP 1.1-Spezifikation für den Cache-Control-Header](https://tools.ietf.org/html/rfc7234#section-5.2) erfordert einen Cache für eine gültige berücksichtigt `Cache-Control` vom Client gesendeten Header. Ein Client kann Anforderungen mit stellen eine `no-cache` Headerwert "und" Force "dem Server um eine neue Antwort für jede Anforderung zu generieren.

Berücksichtigt immer Client `Cache-Control` Anforderungsheader ist sinnvoll, wenn Sie das Ziel des HTTP-caching in Betracht ziehen. Unter die offizielle Spezifikation caching bedeutet, dass die Latenz und Netzwerkaufwand verringert der Erfüllung von Anforderungen in einem Netzwerk des Clients, Proxys und Servern zu verringern. Es ist nicht unbedingt eine Möglichkeit, um die Last auf eine Ursprungsserver zu steuern.

Keine aktuelle entwicklersteuerung dieses Verhalten beim Zwischenspeichern vorhanden ist, bei Verwendung der [Antwort zwischenspeichern Middleware](xref:performance/caching/middleware) , da die Middleware zu den offiziellen caching-Spezifikation entspricht. [Zukünftiger Verbesserungen der Middleware](https://github.com/aspnet/ResponseCaching/issues/96) gestattet, konfigurieren die Middleware, um einer Anforderung zu ignorieren `Cache-Control` Header, die bei der Entscheidung, die eine zwischengespeicherte Antwort dient. Dies wird Sie bieten eine Möglichkeit, eine bessere Steuerung der Last auf dem Server bei der Verwendung von der Middleware.

## <a name="other-caching-technology-in-aspnet-core"></a>Andere Zwischenspeichern Technologie in ASP.NET Core

### <a name="in-memory-caching"></a>Im Arbeitsspeicher Zwischenspeichern

Im Arbeitsspeicher Zwischenspeichern verwendet Serverarbeitsspeicher zum Speichern von zwischengespeicherter Daten. Diese Form des Cachings eignet sich für einen einzelnen oder mehrerer Server mithilfe von *persistente Sitzungen*. Persistente Sitzungen bedeutet, dass die Anfragen von einem Client immer mit dem gleichen Server zur Verarbeitung weitergeleitet werden.

Weitere Informationen finden Sie unter [Einführung in die im Arbeitsspeicher Zwischenspeichern in ASP.NET Core](xref:performance/caching/memory).

### <a name="distributed-cache"></a>Verteilter Cache

Verwenden Sie einen verteilten Cache zum Speichern von Daten im Arbeitsspeicher, wenn die app in einer Cloud oder Server-Farm gehostet wird. Der Cache wird auf den Servern gemeinsam genutzt, die Anforderungen zu verarbeiten. Ein Client kann senden, dass eine Anforderung, die von einem beliebigen Server in der Gruppe behandelt und zwischengespeicherte Daten für den Client verfügbar ist. ASP.NET Core bietet SQL Server und verteilt Redis-Caches.

Weitere Informationen finden Sie unter [arbeiten mit einem verteilten Cache](xref:performance/caching/distributed).

### <a name="cache-tag-helper"></a>Cache-Tag-Hilfsprogramm

Den Inhalt aus einer MVC-Ansicht oder Razor-Page können mit dem Tag-Helfer Cache zwischengespeichert werden. Der Cache-Tag-Hilfsmethode verwendet im Arbeitsspeicher zwischenspeichern, um Daten zu speichern.

Weitere Informationen finden Sie unter [Cache Tag Helper in ASP.NET Core MVC](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

### <a name="distributed-cache-tag-helper"></a>Verteilter Cache-Tag-Hilfsprogramm

Den Inhalt aus einer MVC-Ansicht oder Razor-Page in verteilten Cloud oder Webfarm-Szenarien können mit verteilten Cache-Tag-Hilfsprogramm zwischengespeichert werden. Das verteilte Cache-Tag-Hilfsobjekt verwendet SQL Server oder Redis zum Speichern von Daten an.

Weitere Informationen finden Sie unter [verteilten Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper).

## <a name="responsecache-attribute"></a>ResponseCache-Attribut

Die `ResponseCacheAttribute` gibt die Parameter zum Festlegen der entsprechenden Header in Zwischenspeichern von Antworten erforderlich sind. Finden Sie unter [ResponseCacheAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute) eine Beschreibung der Parameter.

> [!WARNING]
> Deaktivieren Sie das Zwischenspeichern für Inhalte, die Informationen für authentifizierte Clients enthält. Zwischenspeichern sollten nur aktiviert werden, für Inhalte, die nicht ändern, die auf Grundlage eines Benutzers Identität oder gibt an, ob ein Benutzer angemeldet ist.

`VaryByQueryKeys string[]`(erfordert ASP.NET Core 1.1 und höher): Wenn festgelegt ist, die Antwort zwischenspeichern Middleware gespeicherte Antwort durch die Werte der angegebenen Liste der Abfrageschlüssel variiert. Die Antwort zwischenspeichern Middleware muss aktiviert sein, zum Festlegen der `VaryByQueryKeys` Eigenschaft; andernfalls wird eine Laufzeitausnahme ausgelöst. Es ist keine entsprechende HTTP-Header für die `VaryByQueryKeys` Eigenschaft. Diese Eigenschaft ist eine HTTP-Funktion, die von der Antwort zwischenspeichern Middleware verarbeitet. Für die Middleware, die eine zwischengespeicherte Antwort dient müssen der Abfragezeichenfolge und der Wert der Abfragezeichenfolge eine frühere Anforderung wieder übereinstimmen. Betrachten Sie beispielsweise die Sequenz von Anforderungen und Ergebnissen, die in der folgenden Tabelle gezeigt.

| Anforderung                          | Ergebnis                   |
| -------------------------------- | ------------------------ |
| `http://example.com?key1=value1` | vom Server zurückgegebene     |
| `http://example.com?key1=value1` | von der Middleware zurückgegeben |
| `http://example.com?key1=value2` | vom Server zurückgegebene     |

Die erste Anforderung wird vom Server zurückgegebenen und Middleware zwischengespeichert. Die zweite Anforderung wird von Middleware zurückgegeben, weil die Abfragezeichenfolge die vorhergehenden Anforderung übereinstimmt. Die dritte Anforderung ist nicht im Cache Middleware, da der Wert der Abfragezeichenfolge nicht mit eine frühere Anforderung übereinstimmt. 

Die `ResponseCacheAttribute` dient zum Erstellen und konfigurieren Sie (über `IFilterFactory`) eine `ResponseCacheFilter`. Die `ResponseCacheFilter` führt die Arbeit aktualisieren die entsprechenden HTTP-Header und die Funktionen der Antwort. Der Filter:

* Entfernt alle vorhandenen Header für `Vary`, `Cache-Control`, und `Pragma`. 
* Schreibt die entsprechenden Header auf Grundlage der Eigenschaften legen Sie in der `ResponseCacheAttribute`. 
* Aktualisiert die Antwort zwischenspeichern HTTP-Funktion, wenn `VaryByQueryKeys` festgelegt ist.

### <a name="vary"></a>variieren

Dieser Header wird nur geschrieben, wenn die `VaryByHeader` festgelegt wird. Es wird festgelegt, um die `Vary` den Wert der Eigenschaft. Das folgende Beispiel verwendet die `VaryByHeader` Eigenschaft:

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

Sie können die Antwortheadern mit Ihrem Browser Netzwerktools anzeigen. Die folgende Abbildung zeigt die Ausgabe auf Edge F12 der **Netzwerk** Registerkarte, wenn die `About2` Aktionsmethode aktualisiert wird:

![Der Edge F12-Ausgabe auf der Registerkarte "Netzwerk" beim Aufrufen der Aktionsmethode About2](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a>NoStore und Location.None

`NoStore`überschreibt die meisten anderen Eigenschaften. Wenn diese Eigenschaft festgelegt wird, um `true`, `Cache-Control` Header wird festgelegt, um `no-store`. Wenn `Location` festgelegt ist, um `None`:

* Für `Cache-Control` ist `no-store,no-cache` festgelegt.
* Für `Pragma` ist `no-cache` festgelegt.

Wenn `NoStore` ist `false` und `Location` ist `None`, `Cache-Control` und `Pragma` festgelegt `no-cache`.

Legen Sie Sie in der Regel `NoStore` auf `true` auf Fehlerseiten. Zum Beispiel:

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

Daraus ergibt sich die folgenden Header:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Speicherort und die Dauer

So aktivieren Sie das Zwischenspeichern, `Duration` muss auf einen positiven Wert festgelegt werden und `Location` muss entweder `Any` (Standard) oder `Client`. In diesem Fall die `Cache-Control` Header festgelegt ist, auf den Speicherortwert, gefolgt von den `max-age` der Antwort.

> [!NOTE]
> `Location`die Optionen der `Any` und `Client` übersetzen in `Cache-Control` Headerwerte `public` und `private`bzw. Wie bereits erwähnt, festlegen `Location` auf `None` legt `Cache-Control` und `Pragma` Header `no-cache`.

Im folgenden ein Beispiel für die Header erstellt wird, durch Festlegen von `Duration` und lassen die Standardeinstellung `Location` Wert:

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

Dies erzeugt die folgende Kopfzeile:

```
Cache-Control: public,max-age=60
```

### <a name="cache-profiles"></a>Cacheprofile

Anstelle von duplizieren `ResponseCache` Einstellungen auf viele Aktion Attributen für Controller, CacheProfile können als Optionen konfiguriert werden, beim Einrichten von MVC in der `ConfigureServices` Methode in `Startup`. In einer referenzierten Cacheprofil, gefundenen Werte dienen als die standardmäßig von der `ResponseCache` Attribut, und werden nach beliebigen Eigenschaften für das Attribut angegebenen außer Kraft gesetzt.

Einrichten von ein Cacheprofil:

[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)] 

Verweisen auf ein Cacheprofil aus:

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

Die `ResponseCache` Attribut kann auf Aktionen (Methoden) und auf Domänencontrollern (Klassen) angewendet werden. Methodenebene Attribute überschreiben die Attribute auf Klassenebene festgelegten Einstellungen.

Im obigen Beispiel gibt ein Attribut auf Klassenebene während einer Methodenebene-Attribut ein Cacheprofil mit einer Dauer von 60 Sekunden festgelegt verweist auf eine Dauer von 30 Sekunden an.

Die resultierende Header:

```
Cache-Control: public,max-age=60
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Caching in HTTP aus der Spezifikation](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* [Zwischenspeicherung im Speicher](xref:performance/caching/memory)
* [Arbeiten mit einem verteilten Cache](xref:performance/caching/distributed)
* [Erkennen von Änderungen mit Änderungstoken](xref:fundamentals/primitives/change-tokens)
* [Antworten zwischenspeichernde Middleware](xref:performance/caching/middleware)
* [Cache-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)
* [Taghilfsprogramm für verteilten Cache](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)
