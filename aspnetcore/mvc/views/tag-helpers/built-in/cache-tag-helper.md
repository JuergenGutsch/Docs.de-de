---
title: Cache-Tag-Hilfsprogramm im Kern der ASP.NET MVC
author: pkellner
description: Zeigt die zum Arbeiten mit Cache-Tag-Hilfsprogramm
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: 10aa1b493dbd0672cac789f6e48ddf2f14ba35dc
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2018
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>Cache-Tag-Hilfsprogramm im Kern der ASP.NET MVC

Von [Peter Kellner](http://peterkellner.net) 

Die Cache-Tag-Hilfsprogramm bietet die Möglichkeit, die Leistung Ihrer App ASP.NET Core erheblich zu verbessern, indem Sie dessen Inhalt auf den internen Cache-Anbieter von ASP.NET Core Zwischenspeichern.

Das Razor-Ansichtsmodul legt den Standardwert `expires-after` auf 20 Minuten.

Das folgende Markup für den Razor werden zwischengespeichert, Datum/Uhrzeit:

```cshtml
<cache>@DateTime.Now</cache>
```

Die erste Anforderung an die Seite mit `CacheTagHelper` wird das aktuelle Datum/Uhrzeit angezeigt. Zusätzliche Anforderungen werden den zwischengespeicherten Wert angezeigt, bis der Cache abläuft (standardmäßig 20 Minuten) oder durch ungenügenden Arbeitsspeicher bedingt entfernt wird.

Sie können die Cachedauer mit der folgenden Attribute festlegen:

## <a name="cache-tag-helper-attributes"></a>Zwischenspeichern von Tagattribute-Hilfsprogramm

- - -

### <a name="enabled"></a>enabled    


| Attributtyp    | Gültige Werte      |
|----------------   |----------------   |
| boolean           | "True" (Standard)  |
|                   | "false"   |


Bestimmt, ob der Inhalt durch die Cache-Tag-Hilfsprogramm eingeschlossen zwischengespeichert wird. Die Standardeinstellung ist `true`.  Wenn auf festgelegt `false` dieser Cache Tag Helper unbeachtet Zwischenspeichern auf der gerenderten Ausgabe.

Beispiel:

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="expires-on"></a>expires-on 

| Attributtyp    | Beispielwert     |
|----------------   |----------------   |
| DateTimeOffset    | "@new DateTime(2025,1,29,17,02,0)"    |


Legt ein absolutes Ablaufdatum fest. Im folgende Beispiel werden der Inhalt des Cache Tag bis 17:02 Uhr auf 29 Januar 2025 zwischengespeichert.

Beispiel:

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="expires-after"></a>läuft ab nach

| Attributtyp    | Beispielwert     |
|----------------   |----------------   |
| TimeSpan    | " @TimeSpan.FromSeconds (120)"    |


Legt die Zeitspanne seit der ersten Anforderung, zum Zwischenspeichern des Inhalts. 

Beispiel:

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="expires-sliding"></a>Ablauf-gleitende

| Attributtyp    | Beispielwert     |
|----------------   |----------------   |
| TimeSpan    | " @TimeSpan.FromSeconds (60)"     |


Legt die Zeit, die ein Eintrag im Cache entfernt werden soll, wenn er nicht zugegriffen wurde.

Beispiel:

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="vary-by-header"></a>variieren-by-header

| Attributtyp    | Beispielwerte                |
|----------------   |----------------               |
| Zeichenfolge            | "User-Agent"                  |
|                   | "Benutzer-Agent, Content-encoding" |

Nimmt einen einzelnen Header-Wert oder eine durch Trennzeichen getrennte Liste von Headerwerten, die eine Aktualisierung des Cache auslösen, wenn sich diese ändern. Im folgende Beispiel überwacht den Headerwert `User-Agent`. Im Beispiel speichert den Inhalt für alle anderen `User-Agent` dargestellt, mit dem Webserver.

Beispiel:

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="vary-by-query"></a>durch eine Abfrage variieren

| Attributtyp    | Beispielwerte                |
|----------------   |----------------               |
| Zeichenfolge            | "Erstellen"                |
|                   | "Marke, Modell" |

Nimmt einen einzelnen Header-Wert oder eine durch Trennzeichen getrennte Liste von Headerwerten, die eine Aktualisierung des Cache auslösen, wenn der Headerwert geändert wird. Im folgende Beispiel prüft die Werte der `Make` und `Model`.

Beispiel:

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="vary-by-route"></a>Verändern von route

| Attributtyp    | Beispielwerte                |
|----------------   |----------------               |
| Zeichenfolge            | "Erstellen"                |
|                   | "Marke, Modell" |

Nimmt einen einzelnen Header-Wert oder eine durch Trennzeichen getrennte Liste von Headerwerten, die eine Aktualisierung des Cache auslösen, wenn Daten routenparameters Änderung Wert(e) an. Beispiel:

*Startup.cs* 

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```
  
*Index.cshtml*

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="vary-by-cookie"></a>Verändern von Cookies

| Attributtyp    | Beispielwerte                |
|----------------   |----------------               |
| Zeichenfolge            | ".AspNetCore.Identity.Application"                |
|                   | ".AspNetCore.Identity.Application,HairColor" |

Nimmt einen einzelnen Header-Wert oder eine durch Trennzeichen getrennte Liste von Headerwerten, die eine Aktualisierung des Cache auslösen, wenn die Headerwerte (s) ändern. Im folgende Beispiel prüft die ASP.NET Identity zugeordneten Cookies ab. Wenn ein Benutzer authentifiziert wird der Anforderungscookies festgelegt werden, die eine Aktualisierung des Cache auslöst.

Beispiel:

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="vary-by-user"></a>variieren von Benutzer

| Attributtyp    | Beispielwerte                |
|----------------   |----------------               |
| Boolesch             | "true"                  |
|                     | "False" (Standard) |

Gibt an, und zwar unabhängig davon, ob der Cache zurückgesetzt werden soll, wenn der angemeldete Benutzer (oder Kontext Prinzipal) ändert. Der aktuelle Benutzer ist auch bekannt als der Prinzipal für die Anforderung Kontext und können in einer Razor-Ansicht angezeigt werden, durch Verweisen auf `@User.Identity.Name`.

Im folgende Beispiel prüft den aktuell angemeldeten Benutzers ab.  

Beispiel:

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Mit diesem Attribut behält den Inhalt im Cache für eine Anmeldung und Abmeldung Aktualisierungszyklus durchläuft.  Bei Verwendung `vary-by-user="true"`, eine Anmeldung und Abmeldung Aktion erklärt den Cache für den authentifizierten Benutzer.  Der Cache wird ungültig, da ein neue eindeutige Cookiewert bei der Anmeldung generiert wurde. Cache wird für den anonymen Zustand beibehalten, wenn kein Cookie vorhanden ist, oder ist abgelaufen. Dies bedeutet, wenn kein Benutzer angemeldet ist, wird der Cache beibehalten werden.

- - -

### <a name="vary-by"></a>vary-by

| Attributtyp    | Beispielwerte                |
|----------------   |----------------               |
| Zeichenfolge             | " @Model "                 |


Ermöglicht die Anpassung der Daten zwischengespeichert, ruft. Wenn das Objekt verweist auf das Attribut Zeichenfolge geändert wird, den Inhalt des Hilfsprogramms Tag Cache aktualisiert wurde. Eine zeichenfolgenverkettung aus Modellwerte werden häufig mit diesem Attribut zugewiesen.  Praktisch bedeutet dies, dass ein Update für die verketteten Werte des Caches erklärt wird.

Im folgende Beispiel wird davon ausgegangen, das Rendern der Ansicht Sums des ganzzahlige Wert, der die zwei Routenparameter Controllermethode `myParam1` und `myParam2`, und gibt zurück, die als einzelnes Modell-Eigenschaft. Bei dieser Summe geändert wird, wird der Inhalt des Cache-Tag-Hilfsprogramms gerendert und erneut zwischengespeichert.  

Beispiel:

Aktion:

```csharp
public IActionResult Index(string myParam1,string myParam2,string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

*Index.cshtml*

```cshtml
<cache vary-by="@Model"">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

- - -

### <a name="priority"></a>priority

| Attributtyp    | Beispielwerte                |
|----------------   |----------------               |
| CacheItemPriority  | "High"                   |
|                    | "Low" |
|                    | "NeverRemove" |
|                    | "Normal" |

Stellt die integrierten Cacheanbieter Cache Entfernung Anleitungen bereit. Der Webserver entfernen `Low` Einträge zuerst zwischengespeichert, wenn es nicht genügend Arbeitsspeicher vorhanden ist.

Beispiel:

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Die `priority` Attribut eine bestimmte Ebene des Cache Aufbewahrung garantiert nicht. `CacheItemPriority`ist nur ein Vorschlag. Wenn dieses Attribut auf `NeverRemove` garantiert nicht, dass der Cache immer beibehalten werden. Finden Sie unter [zusätzliche Ressourcen](#additional-resources) für Weitere Informationen.

Das Cache-Tag-Hilfsprogramm ist abhängig von der [Memory-Cachedienst](xref:performance/caching/memory). Die Cache-Tag-Hilfsprogramm wird der Dienst auf, wenn er nicht hinzugefügt wurde.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
