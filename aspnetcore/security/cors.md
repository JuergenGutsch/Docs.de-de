---
title: Aktivieren von Cross-Origin-Anforderungen (CORS)
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.author: riande
ms.date: 05/17/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/cors
ms.openlocfilehash: 0d1c34f5c82fe1a28a405617ea77084db899139b
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="enabling-cross-origin-requests-cors"></a>Aktivieren von Cross-Origin-Anforderungen (CORS)

Durch [Mike Wasson](https://github.com/mikewasson), [Shayne Boyer](https://twitter.com/spboyer), und [Tom Dykstra](https://github.com/tdykstra)

Browsersicherheit wird verhindert, dass eine Webseite, die AJAX-Anforderungen in eine andere Domäne. Diese Einschränkung wird aufgerufen, die *gleichen Origin-Richtlinie*, und verhindert, dass eine bösartige Website vertrauliche Daten von einem anderen Standort lesen. Allerdings sollten Sie in einigen Fällen an anderen Standorten Cross-Origin-Anforderungen für Ihre Web-API ausführen können.

[Cross Origin Resource Sharing](http://www.w3.org/TR/cors/) (CORS) ist ein W3C-Standard, der einem Server ermöglicht, die gleichen-Origin-Richtlinie weniger streng gehandhabt. Verwenden CORS, können ein Servers explizit einige Cross-Origin-Anforderungen beim Ablehnen von anderen Benutzern. CORS ist eine sicherere und flexibler als die früheren Methoden wie z. B. [JSONP](https://wikipedia.org/wiki/JSONP). Dieses Thema veranschaulicht das Aktivieren von CORS in einer ASP.NET Core-Anwendung.

## <a name="what-is-same-origin"></a>Was ist "gleichen Origin"?

Zwei URLs müssen denselben Ursprung aus, wenn sie identische Schemas, Hosts und Ports verfügen. ([RFC 6454](http://tools.ietf.org/html/rfc6454))

Diese beiden URLs haben die gleichen Ursprungs:

* `http://example.com/foo.html`

* `http://example.com/bar.html`

Diese URLs haben unterschiedliche Ursprünge als den vorherigen zwei:

* `http://example.net`-Der anderen Domäne

* `http://www.example.com/foo.html`-Andere Unterdomäne

* `https://example.com/foo.html`-Anderes Schema

* `http://example.com:9000/foo.html`-Anschluss

> [!NOTE]
> Internet Explorer berücksichtigen nicht den Port Ursprünge zu vergleichen.

## <a name="setting-up-cors"></a>CORS einrichten

Richten Sie CORS für Ihre Anwendung hinzufügen der `Microsoft.AspNetCore.Cors` Paket Ihrem Projekt.

Fügen Sie die CORS-Dienste im Startup.cs hinzu:

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?name=snippet_addcors)]

## <a name="enabling-cors-with-middleware"></a>Aktivieren von CORS mit middleware

So aktivieren Sie CORS für die gesamte Anwendung fügen Sie die CORS-Middleware mit Ihrer Anforderung Pipeline die `UseCors` Erweiterungsmethode. Beachten Sie, dass die CORS-Middleware definierten Endpunkte in Ihrer app vorausgehen muss, die Cross-Origin-Anforderungen (z.B. unterstützt werden soll vor jeder Aufruf von `UseMvc`).

Sie können eine Cross-Origin-Richtlinie angeben, beim Hinzufügen von den CORS-Middleware mithilfe der `CorsPolicyBuilder` Klasse. Hierfür gibt es zwei Möglichkeiten. Der erste Schritt besteht UseCors mit einem Lambda-Ausdruck aufrufen:

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?highlight=11,12&range=22-38)]

**Hinweis:** die URL muss angegeben werden, ohne einen nachstehenden Schrägstrich (`/`). Wenn die URL mit beendet `/`, der Vergleich zurück `false` und keine Header zurückgegeben werden.

Der Lambda-Ausdruck akzeptiert eine `CorsPolicyBuilder` Objekt. Sie finden eine Liste mit den [Konfigurationsoptionen](#cors-policy-options) weiter unten in diesem Thema. In diesem Beispiel lässt die Richtlinie Cross-Origin-Anforderungen von `http://example.com` und keine anderen Quellen.

Beachten Sie, dass CorsPolicyBuilder eine fluent-API verfügt, damit Sie die Methodenaufrufe verketten können:

[!code-csharp[Main](../security/cors/sample/CorsExample3/Startup.cs?highlight=3&range=29-32)]

Der zweite Ansatz ist, definieren ein oder mehrere benannte CORS-Richtlinien, und wählen Sie die Richtlinie anhand des Namens zur Laufzeit.

[!code-csharp[Main](cors/sample/CorsExample2/Startup.cs?name=snippet_begin)]

In diesem Beispiel wird eine CORS-Richtlinie mit dem Namen "AllowSpecificOrigin". Um die Richtlinie auswählen, übergeben Sie den Namen zu `UseCors`.

## <a name="enabling-cors-in-mvc"></a>Aktivieren von CORS in MVC

Sie können alternativ MVC verwenden, um bestimmte CORS pro Aktion, die pro Controller oder global für alle Controller anzuwenden. Verwendung von MVC CORS aktivieren, werden die gleichen CORS-Dienste verwendet, jedoch nicht von den CORS-Middleware.

### <a name="per-action"></a>Pro Aktion

Geben Sie eine CORS-Richtlinie für eine bestimmte Aktion hinzufügen der `[EnableCors]` -Attribut auf die Aktion. Geben Sie den Richtliniennamen an.

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnAction)]

### <a name="per-controller"></a>Pro controller

Geben Sie die CORS-Richtlinie für einen bestimmten Controller Hinzufügen der `[EnableCors]` -Attribut auf die Controllerklasse. Geben Sie den Richtliniennamen an.

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnController)]

### <a name="globally"></a>Global

Sie können CORS global für alle Domänencontroller durch Hinzufügen der `CorsAuthorizationFilterFactory` Filter, um die Auflistung der globalen Filter:

[!code-csharp[Main](cors/sample/CorsMVC/Startup2.cs?name=snippet_configureservices)]

Die Rangfolge wird: Aktion, Controller, global. Sicherheitsrichtlinien auf Unternehmensebene Aktion haben Vorrang vor Controllerebene Richtlinien sowie Sicherheitsrichtlinien auf Unternehmensebene Controller haben Vorrang vor globalen Richtlinien.

### <a name="disable-cors"></a>Deaktivieren Sie CORS

Verwenden Sie zum Deaktivieren von CORS für einen Controller oder die Aktion der `[DisableCors]` Attribut.

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=DisableOnAction)]

## <a name="cors-policy-options"></a>Optionen für CORS-Richtlinie

Dieser Abschnitt beschreibt die verschiedenen Optionen, die Sie in einer CORS-Richtlinie festlegen können.

* [Legen Sie die zulässigen Ursprünge](#set-the-allowed-origins)

* [Legen Sie die zulässigen HTTP-Methoden](#set-the-allowed-http-methods)

* [Legt die zulässige Anforderungsheader fest.](#set-the-allowed-request-headers)

* [Legen Sie die verfügbar gemachten Antwortheader](#set-the-exposed-response-headers)

* [Anmeldeinformationen in Cross-Origin-Anforderungen](#credentials-in-cross-origin-requests)

* [Legen Sie die Preflight-Ablaufzeit](#set-the-preflight-expiration-time)

Für einige Optionen kann es hilfreich, zu lesen sein [funktioniert wie CORS](#how-cors-works) erste.

### <a name="set-the-allowed-origins"></a>Legen Sie die zulässigen Ursprünge

Um eine oder mehrere bestimmte Ursprünge zuzulassen:

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=19-23)]

Alle Ursprünge erlaubt:

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs??range=27-31)]

Sollten Sie sorgfältig überlegen, bevor Anforderungen über einen beliebigen Ursprung zugelassen. Dies bedeutet, dass es sich bei jeder beliebige Website AJAX-Aufrufe auf Ihre API vornehmen kann.

### <a name="set-the-allowed-http-methods"></a>Legen Sie die zulässigen HTTP-Methoden

Um alle HTTP-Methoden zu ermöglichen:

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=44-49)]

Dies wirkt sich auf die Preflight-Anforderungen und Access-Control-Allow-Methods-Header.

### <a name="set-the-allowed-request-headers"></a>Legt die zulässige Anforderungsheader fest.

Eine CORS-preflight-Anforderung kann einen Access-Control-Request-Headers-Header, die HTTP-Header, die von der Anwendung festgelegtes auflisten enthalten (den so genannten "author Anforderungsheader").

Auf bestimmte Header der Positivliste:

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=53-58)]

Damit können erstellen alle Anforderungsheader:

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=62-67)]

Browser sind nicht vollständig in diese Festlegung von Access-Control-Request-Headers konsistent. Wenn der Header zu beliebiegen Dokumentbestandteilen außer festlegen "*", Sie sollten berücksichtigen mindestens "Annehmen", "Content-Type" und "Origin" sowie alle benutzerdefinierten Header, die Sie unterstützen möchten.

### <a name="set-the-exposed-response-headers"></a>Legen Sie die verfügbar gemachten Antwortheader

Standardmäßig nicht im Browser aller die Antwortheader für die Anwendung verfügbar machen. (Siehe [http://www.w3.org/TR/cors/#simple-response-header](http://www.w3.org/TR/cors/#simple-response-header).) Die Antwortheader, die standardmäßig verfügbar sind:

* Cache-Control

* Content-Language

* Content-Type

* Läuft ab

* Zuletzt geändert

* Pragma

Die CORS-Spezifikation ruft diese *einfache Antwortheader*. Um weitere Header für die Anwendung verfügbar zu machen:

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=71-76)]

### <a name="credentials-in-cross-origin-requests"></a>Anmeldeinformationen in Cross-Origin-Anforderungen

Anmeldeinformationen erfordern eine besondere Behandlung in einer CORS-Anforderung. Standardmäßig senden nicht im Browser keine Anmeldeinformationen mit einem Cross-Origin-Anforderung. Anmeldeinformationen werden Cookies sowie HTTP-Authentifizierungsschemas einschließen. Der Client muss zum Senden von Anmeldeinformationen mit einem Cross-Origin-Anforderung XMLHttpRequest.withCredentials auf "true" festlegen.

Mithilfe von XMLHttpRequest direkt:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'http://www.example.com/api/test');
xhr.withCredentials = true;
```

In jQuery:

```jQuery
$.ajax({
  type: 'get',
  url: 'http://www.example.com/home',
  xhrFields: {
    withCredentials: true
}
```

Darüber hinaus muss der Server die Anmeldeinformationen zulassen. Um Cross-Origin-Anmeldeinformationen zu ermöglichen:

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=80-85)]

Die HTTP-Antwort enthalten nun einen Access-Control-Allow-Credentials-Header, der wodurch dem Browser angewiesen wird, dass der Server die Anmeldeinformationen für eine Anforderung Cross-Origin zulässt.

Wenn der Browser Anmeldeinformationen sendet, aber die Antwort keinen gültigen Access-Control-Allow-Credentials-Header, der Browser wird nicht die Antwort an die Anwendung verfügbar machen, und die AJAX-Anforderung ein Fehler auftritt.

Seien Sie vorsichtig bei Cross-Origin-Anmeldeinformationen zulassen. Eine Website finden Sie unter einer anderen Domäne kann Anmeldeinformationen eines angemeldeten Benutzers an die app im Auftrag des Benutzers, ohne Wissen des Benutzers gesendet. CORS-Spezifikation besagt ebenfalls diese Einstellung Ursprünge auf "*" (alle Ursprünge) ist ungültig. wenn die `Access-Control-Allow-Credentials` Header vorhanden ist.

### <a name="set-the-preflight-expiration-time"></a>Legen Sie die Preflight-Ablaufzeit

Der Access-Control-Max-Age-Header gibt an, wie lange die Antwort auf die preflight-Anforderung zwischengespeichert werden kann. Auf diese Header festgelegt werden:

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=89-94)]

<a name="cors-how-cors-works"></a>

## <a name="how-cors-works"></a>Funktionsweise von CORS

In diesem Abschnitt wird beschrieben, was geschieht, in einer CORS-Anforderung auf der Ebene der HTTP-Nachrichten. Es ist wichtig zu verstehen, wie die CORS zusammen, sodass die CORS-Richtlinie ordnungsgemäß konfiguriert werden kann und eine beim zu unerwarteten Ergebnissen führen.

CORS-Spezifikation führt mehrere neue HTTP-Header, die Cross-Origin-Anforderungen zu ermöglichen. Wenn ein Browser CORS unterstützt, wird diese Header automatisch für Cross-Origin-Anforderungen festgelegt. Benutzerdefinierte JavaScript-Code ist nicht erforderlich, um CORS zu aktivieren.

Hier ist ein Beispiel einer Cross-Origin-Anforderung. Die `Origin` Header bietet die Domäne des Standorts, der die Anforderung gesendet wird:

```
GET http://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: http://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: http://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Wenn der Server die Anforderung zulässt, wird die Access-Control-Allow-Origin-Header in der Antwort festgelegt. Der Wert dieses Headers entspricht den Origin-Header in der Anforderung oder Wert für die Platzhalterzeichen "*", Bedeutung, die einen beliebigen Ursprung zulässig ist:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: http://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Wenn die Antwort den Access-Control-Allow-Origin-Header enthält, schlägt die AJAX-Anforderung. Der Browser lässt, die die Anforderung. Auch wenn der Server eine erfolgreiche Antwort zurückgibt, machen nicht im Browser die Antwort an die Clientanwendung verfügbar.

### <a name="preflight-requests"></a>Preflight-Anforderungen

Für einige CORS-Anforderungen sendet der Browser eine zusätzliche Anforderung, eine "preflight-Anforderung", aufgerufen, bevor die tatsächliche Anforderung für die Ressource gesendet. Der Browser kann die preflight-Anforderung überspringen, wenn Folgendes zutrifft:

* Die Anforderungsmethode ist GET, HEAD oder POST, und

* Die Anwendung keine Anforderungsheader als Accept, Accept-Language-Content-Language, festzulegen, die Content-Type oder letzten-Ereignis-ID und

* Der Content-Type-Header (falls festgelegt) ist eines der folgenden:

  * application/x-www-form-urlencoded

  * Multipart/Form-data

  * Text/plain

Die Regel zu Anforderungsheader gilt für Header, die Anwendung wird durch Aufrufen von SetRequestHeader für das XMLHttpRequest-Objekt. (Die CORS-Spezifikation ruft diese "Autor Anforderungsheader".) Die Regel gilt nicht für Header, die der Browser "Benutzer-Agent-Host" oder "Content-Length festlegen kann.

Hier ist ein Beispiel für eine preflight-Anforderung:

```
OPTIONS http://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: http://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

Die Preflight-Anforderung mithilfe der HTTP OPTIONS-Methode. Sie schließt zwei spezielle Header:

* Access-Control-Request-Method: Die HTTP-Methode, die für die tatsächliche Anforderung verwendet werden.

* Access-Control-Request-Headers: Eine Liste der Anforderungsheader, die die Anwendung für die tatsächliche Anforderung festgelegt. (Erneut nicht dazu gehören Header, die der Browser legt sie fest.)

Hier ist eine Beispielantwort, vorausgesetzt, dass der Server die Anforderung zulässt:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: http://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

Die Antwort enthält eine Access-Control-Allow-Methods-Header, der die zulässigen Methoden aufgelistet, und optional eine Access-Control-Allow-Headers-Header, der Listet die erlaubten Header. Wenn die preflight-Anforderung erfolgreich ist, sendet der Browser die tatsächliche Anforderung an, wie zuvor beschrieben.
