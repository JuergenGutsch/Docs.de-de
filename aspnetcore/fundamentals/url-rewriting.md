---
title: "Überschreiben von URLs in ASP.NET Core Middleware"
author: guardrex
description: Informationen Sie zur URL umschreiben und Umleitung mit URL umschreiben Middleware in ASP.NET Core-Anwendungen.
ms.author: riande
manager: wpickett
ms.date: 08/17/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/url-rewriting
ms.openlocfilehash: dde0b5673c9885db2fecbb24b384752e5ddf70eb
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>Überschreiben von URLs in ASP.NET Core Middleware

Durch [Luke Latham](https://github.com/guardrex) und [Mikael Mengistu](https://github.com/mikaelm12)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Vorgehensweise zum Herunterladen](xref:tutorials/index#how-to-download-a-sample))

URLs dient der Anforderung, die eine oder mehrere vordefinierte Regeln URLs anhand ändern. Eine Abstraktion zwischen Ressourcenpfade und ihre Adressen URLs erstellt werden, sodass die Standorte und die Adressen nicht eng miteinander verknüpft sind. Es gibt mehrere Szenarien, in denen URLs nützlich ist:
* Verschieben oder ersetzen Serverressourcen temporär oder dauerhaft Beibehaltung stabil Locators für diese Ressourcen
* Teilen bei der anforderungsverarbeitung für andere apps oder Bereiche von einer app
* Entfernen von, hinzufügen oder Neuorganisieren von URL-Segmente für eingehende Anforderungen
* Optimieren der öffentlichen URLs für Search Engine Optimization (SEO)
* Die Verwendung von benutzerfreundlichen Öffentliche URLs können Personen, die den Inhalt Vorhersagen sie erwarten können, indem Sie einen Link zulassen
* Umleiten von unsichere Anforderungen zum Sichern von Endpunkten
* Verhindern von Image hotlinking

Sie können Regeln für die Änderung der URL auf verschiedene Arten, einschließlich Regex, Apache Mod_rewrite Modul Regeln, Rewrite-Modul für IIS-Regeln und verwenden benutzerdefinierte Logik definieren. Dieses Dokument führt URLs mit Anweisungen zum Neuerstellen von URL-Middleware in ASP.NET Core-apps verwenden.

> [!NOTE]
> URLs kann die Leistung einer App reduzieren. Sofern möglich, sollten Sie die Anzahl und Komplexität der Regeln beschränken.

## <a name="url-redirect-and-url-rewrite"></a>Schreiben Sie die URL-Umleitung "und"-URL
Der Unterschied in die Formulierung "zwischen" *URL-Umleitung* und *URL Rewrite* mag feine am ersten hat wichtige Auswirkungen auf die Ressourcen für Clients bereitstellt. ASP.NET Core URL umschreiben Middleware ist in der Besprechung müssen für beide.

Ein *URL-Umleitung* ist ein Vorgang die clientseitige, in dem der Client den Zugriff auf eine Ressource an eine andere Adresse angewiesen ist. Dies ist einen Roundtrip zum Server erforderlich. Die umleitungs-URL an den Client zurückgegeben wird in der Adressleiste des Browsers angezeigt, wenn der Client eine neue Anforderung für die Ressource sendet. 

Wenn `/resource` ist *umgeleitet* auf `/different-resource`, der Clientanforderungen `/resource`. Der Server antwortet, dass der Client die Ressource unter Abrufen sollte `/different-resource` mit einem Status Code gibt an, dass der Umleitung entweder vorübergehend oder dauerhaft ist. Der Client führt eine neue Anforderung für die Ressource an die umleitungs-URL.

![Ein Dienstendpunkt WebAPI wurde vorübergehend von Version 1 (v1) auf Version 2 (v2) auf dem Server geändert. Ein Client sendet eine Anforderung an den Dienst an der Version 1 Pfad /v1/api. Der Server sendet eine 302 (gefunden)-Antwort mit der neue, temporäre Pfad für den Dienst wieder auf Version 2 /v2/api. Der Client stellt eine zweite Anforderung an den Dienst an die umleitungs-URL an. Der Server antwortet mit einem Statuscode "200 (OK)".](url-rewriting/_static/url_redirect.png)

Wenn Anforderungen zu einer anderen URL umgeleitet, geben Sie an, ob die Umleitung permanente oder temporäre ist. Der 301 (Permanent verschoben) Statuscode wird, in dem die Ressource besitzt, eine neue, permanente-URL, und Sie möchten verwendet, um dem Client anzuweisen, dass alle zukünftige Anforderungen für die Ressource mit die neue URL verwenden soll. *Der Client möglicherweise die Antwort zwischenspeichern, wenn ein Statuscode "301" empfangen wird.* Der Statuscode "302 (gefunden)" wird verwendet, in denen die Umleitung temporäre oder in der Regel der Betreffzeile um zu ändern, dass der Client sollte nicht speichern und, die umleitungs-URL in der Zukunft wiederverwenden. Weitere Informationen finden Sie unter [RFC 2616: Statuscodedefinitionen](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

Ein *URL Rewrite* ist ein serverseitiger Vorgang, um eine Ressource von einer anderen Ressource Adresse bereitzustellen. Eine URL umschreiben erfordern keinen Roundtrip zum Server. Die umgeschriebene URL ist nicht an den Client zurückgegeben und wird nicht in der Adressleiste des Browsers angezeigt. Wenn `/resource` ist *umgeschrieben* auf `/different-resource`, der Clientanforderungen `/resource`, und der Server *intern* Ruft die Ressource auf `/different-resource`. Obwohl der Client die Ressource auf die umgeschriebene URL abrufen sein kann, wird nicht der Client informiert werden, dass die Ressource an der umgeschriebene URL vorhanden ist, wenn er die Anforderung sendet und die Antwort empfängt.

![Ein Dienstendpunkt WebAPI wurde von Version 1 (v1) auf Version 2 (v2) auf dem Server geändert. Ein Client sendet eine Anforderung an den Dienst an der Version 1 Pfad /v1/api. Die Anforderungs-URL ist für den Zugriff auf den Dienst an der Version 2 Pfad /v2/api umgeschrieben. Der Dienst antwortet dem Client mit einem Statuscode "200 (OK)".](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Umschreiben von URL-Beispiel-app
Untersuchen Sie die Funktionen von der URL umschreiben Middleware mit der [Umschreiben von URL-Beispiel-app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/). Die app gilt, schreiben und Umleitung Regeln und zeigt die URL umgeschriebene oder umgeleitet erfolgen.

## <a name="when-to-use-url-rewriting-middleware"></a>URL umschreiben Middleware verwenden
URL umschreiben Middleware verwenden, wenn Sie nicht verwenden, können die [URL Rewrite-Modul](https://www.iis.net/downloads/microsoft/url-rewrite) mit IIS unter Windows Server, die [Apache Mod_rewrite-Modul](https://httpd.apache.org/docs/2.4/rewrite/) auf Apache-Server [URLs auf Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/), oder auf Ihrer app gehostet wird [HTTP.sys Server](xref:fundamentals/servers/httpsys) (ehemals [WebListener](xref:fundamentals/servers/weblistener)). Der Hauptgründe, warum die Server-basierte URL Umschreiben von Technologien in IIS, Apache oder Nginx verwendet werden, dass die Middleware nicht die vollständigen Funktionen von diesen Modulen nicht unterstützt und die Leistung der Middleware wahrscheinlich nicht, die von den Modulen überein. Es gibt jedoch einige Funktionen von Servermodule, die mit ASP.NET Core-Projekten, wie z. B. nicht die `IsFile` und `IsDirectory` Einschränkungen des IIS-Rewrite-Modul. Verwenden Sie in diesen Szenarien stattdessen die Middleware.

## <a name="package"></a>Package
Um die Middleware in Ihrem Projekt einzuschließen, fügen Sie einen Verweis auf die [ `Microsoft.AspNetCore.Rewrite` ](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite/) Paket. Diese Funktion ist für apps für ASP.NET Core 1.1 oder höher verfügbar.

## <a name="extension-and-options"></a>Erweiterung und Optionen
Die URL-Rewrite herzustellen und leiten Sie Regeln durch das Erstellen einer Instanz von der `RewriteOptions` Klasse mit Erweiterungsmethoden für die einzelnen Regeln. Verkettet mehrere Regeln in der Reihenfolge, in der Sie verarbeitet sollen. Die `RewriteOptions` in der URL umschreiben Middleware übergeben werden, während sie mit der Anforderungspipeline hinzugefügt werden `app.UseRewriter(options);`.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1)]

---

### <a name="url-redirect"></a>URL-Umleitung
Verwendung `AddRedirect` Anforderungen. Der erste Parameter enthält die Regex für den Abgleich für den Pfad der eingehenden URL an. Der zweite Parameter ist die Ersatzzeichenfolge. Der dritte Parameter, gibt falls vorhanden, den Statuscode. Wenn Sie den Statuscode nicht angeben, wird standardmäßig 302 (Found), der angibt, dass die Ressource vorübergehend verschoben oder ersetzt wird.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=2)]

---

In einem Browser mit den Entwicklertools aktiviert, nehmen Sie eine Anforderung an die Beispiel-app mit dem Pfad `/redirect-rule/1234/5678`. Der Regex-Ausdruck entspricht den Anforderungspfad auf `redirect-rule/(.*)`, und der Pfad wird durch ersetzt `/redirected/1234/5678`. Die Umleitung wird zurück an den Client mit Statuscode 302 (gefunden) URL gesendet. Der Browser sendet eine neue Anforderung an die umleitungs-URL, die in der Adressleiste des Browsers angezeigt wird. Da keine Regeln in der Beispiel-app auf der umleitungs-URL übereinstimmen, die zweite Anforderung empfängt eine 200 (OK)-Antwort von der app, und der Text der Antwort zeigt der umleitungs-URL. Ein Roundtrip wird mit dem Server ausgelöst, wenn eine URL ist *umgeleitet*.

> [!WARNING]
> Seien Sie vorsichtig beim Einrichten der umleitungs-Regeln. Die umleitungs-Regeln werden bei jeder Anforderung an die app, die nach einer Umleitung einschließlich ausgewertet. Es ist einfach, versehentlich eine Schleife von unendliche umleitungen erstellen.

Ursprüngliche Anforderung:`/redirect-rule/1234/5678`

![Browser-Fenster mit den Entwicklertools Nachverfolgen von Anforderungen und Antworten](url-rewriting/_static/add_redirect.png)

Wird aufgerufen, der Teil des Ausdrucks innerhalb der Klammern eine *Erfassungsgruppe*. Der Punkt (`.`) des Ausdrucks bedeutet *Übereinstimmung mit beliebigem Zeichen*. Das Sternchen (`*`) gibt an, *entspricht dem vorangehende Zeichen keine oder mehrmalige*. Aus diesem Grund die letzten beiden Pfadsegmente der URL, `1234/5678`, werden von der Erfassungsgruppe erfasst `(.*)`. Jeder Wert, der Sie in der Anforderungs-URL nach dem Bereitstellen `redirect-rule/` als dieser einzelnen Erfassungsgruppe erfasst wird.

In der Ersetzungszeichenfolge erfasste Gruppen werden in der Zeichenfolge mit einem Dollarzeichen eingefügt (`$`) gefolgt von die Sequenznummer der Erfassung. Der erste Wert der Capture-Gruppe mit abgerufen wird `$1`, wobei der zweite mit `$2`, in Reihenfolge für die Erfassungsgruppen in Ihre Regex weiter. Ist nur bei einer erfasste Gruppe der Umleitung Regel Regex in der Beispiel-app, daher besteht nur eine eingefügte Gruppe in der Ersetzungszeichenfolge ein, also `$1`. Wenn die Regel angewendet wird, wird die URL `/redirected/1234/5678`.

<a name="url-redirect-to-secure-endpoint"></a>
### <a name="url-redirect-to-a-secure-endpoint"></a>URL-Umleitung an einen sicheren Endpunkt
Verwendung `AddRedirectToHttps` zum Umleiten von HTTP-Anforderungen auf dem gleichen Host und Pfad, die über HTTPS (`https://`). Wenn der Statuscode ist nicht angegeben wird, standardmäßig die Middleware 302 (gefunden). Wenn der Port angegeben ist, wird die Middleware standardmäßig `null`, was bedeutet, dass das Protokoll ändert sich in `https://` und der Client greift auf die Ressource über Port 443. Im Beispiel veranschaulicht das Festlegen des Statuscodes 301 (Permanent verschoben) und den Port zu 5001 ändern.
```csharp
var options = new RewriteOptions()
    .AddRedirectToHttps(301, 5001);

app.UseRewriter(options);
```
Verwendung `AddRedirectToHttpsPermanent` umleiten unsichere Anforderungen auf dem Host und Pfad mit sicheren HTTPS-Protokoll (`https://` über Port 443). Die Middleware legt den Statuscode 301 (Permanent verschoben).

Die Beispiel-app ist in der Lage ist, demonstrieren, wie Sie `AddRedirectToHttps` oder `AddRedirectToHttpsPermanent`. Fügen Sie die Erweiterungsmethode, um die `RewriteOptions`. Stellen Sie eine unsichere Anforderung an die app auf eine beliebige URL ein. Schließen Sie den Browser "sicherheitswarnung", dass das selbstsignierte Zertifikat nicht vertrauenswürdig ist.

Ursprüngliche Anforderung mit `AddRedirectToHttps(301, 5001)`:`/secure`

![Browser-Fenster mit den Entwicklertools Nachverfolgen von Anforderungen und Antworten](url-rewriting/_static/add_redirect_to_https.png)

Ursprüngliche Anforderung mit `AddRedirectToHttpsPermanent`:`/secure`

![Browser-Fenster mit den Entwicklertools Nachverfolgen von Anforderungen und Antworten](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>URL rewrite
Verwendung `AddRewrite` zum Erstellen einer Regel zum Umschreiben von URLs. Der erste Parameter enthält die Regex für den Abgleich für die eingehende URL-Pfad. Der zweite Parameter ist die Ersatzzeichenfolge. Der dritte Parameter `skipRemainingRules: {true|false}`, gibt an, um die Middleware ob zusätzliche neuschreibungsregeln überspringen, wenn die aktuelle Regel angewendet wird.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=6)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=3)]

---

Ursprüngliche Anforderung:`/rewrite-rule/1234/5678`

![Browser-Fenster mit den Entwicklertools nachverfolgen, die Anforderung und Antwort](url-rewriting/_static/add_rewrite.png)

Beachten Sie in den Regex als Erstes wird das Caretzeichen (`^`) am Anfang des Ausdrucks. Dies bedeutet, dass die Übereinstimmung am Anfang des URL-Pfads beginnt.

Im vorangehenden Beispiel mit der umleitungsregel `redirect-rule/(.*)`, gibt es keine Caretzeichen am Anfang der Regex; deshalb keine Zeichen vorangestellt möglicherweise `redirect-rule/` in den Pfad für eine erfolgreiche Übereinstimmung.

| Pfad                               | Match |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Ja   |
| `/my-cool-redirect-rule/1234/5678` | Ja   |
| `/anotherredirect-rule/1234/5678`  | Ja   |

Die neuschreibungsregel `^rewrite-rule/(\d+)/(\d+)`, nur Pfade entspricht, wenn sie mit dem `rewrite-rule/`. Beachten Sie den Unterschied zwischen den folgenden neuschreibungsregel und der oben genannten umleitungsregel abgeglichen.

| Pfad                              | Match |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Ja   |
| `/my-cool-rewrite-rule/1234/5678` | Nein    |
| `/anotherrewrite-rule/1234/5678`  | Nein    |

Nach der `^rewrite-rule/` Teil des Ausdrucks, es gibt zwei Erfassungsgruppen, `(\d+)/(\d+)`. Die `\d` , bedeutet dies *eine Ziffer (Anzahl) entsprechen*. Das Pluszeichen (`+`) bedeutet, dass *abgleichen mit einem oder mehreren das vorherige Zeichen*. Aus diesem Grund muss die URL eine Zahl, gefolgt von einem Schrägstrich, gefolgt von einer anderen Zahl enthalten. Diese Gruppen werden in der umgeschriebene URL eingefügt Capture `$1` und `$2`. Die Ersetzungszeichenfolge der Rewrite-Regel platziert der erfassten Gruppe in die Abfragezeichenfolge. Der angeforderte Pfad der `/rewrite-rule/1234/5678` wird erneut geschrieben, um die Ressource auf erhalten `/rewritten?var1=1234&var2=5678`. Wenn eine Abfragezeichenfolge für die ursprüngliche Anforderung vorhanden ist, wird es beim URL umgeschrieben wird beibehalten.

Es gibt keine Roundtrip zum Server, der die Ressource zu erhalten. Wenn die Ressource vorhanden ist, hat es abgerufen und an den Client mit einem Statuscode "200 (OK)" zurückgegeben. Da der Client umgeleitet wird, wird die URL in die Adressleiste des Browsers nicht geändert. Soweit der Client ist, ist der URL-Rewrite Vorgang nie aufgetreten.

> [!NOTE]
> Verwendung `skipRemainingRules: true` wann immer möglich, da Abgleichsregeln ein kostengünstiger Prozess ist und reduziert die Antwortzeit für die app. Für die schnellste app-Antwort:
> * Sortieren Sie die neuschreibungsregeln am häufigsten übereinstimmende Regel aus, die am seltensten übereinstimmende Regel.
> * Überspringen Sie die Verarbeitung der verbleibenden Regeln aus, wenn eine Übereinstimmung auftritt und keine weitere regelverarbeitung erforderlich ist.

### <a name="apache-modrewrite"></a>Apache mod_rewrite
Wenden Sie Apache Mod_rewrite Regeln mit `AddApacheModRewrite`. Stellen Sie sicher, dass die Rules-Datei mit der app bereitgestellt wird. Weitere Informationen und Beispiele für Mod_rewrite-Regeln finden Sie unter [Apache Mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Ein `StreamReader` wird verwendet, lesen Sie die Regeln aus der *ApacheModRewrite.txt* Rules-Datei.

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=1,7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Der erste Parameter erwartet ein `IFileProvider`, weiter über [Abhängigkeitsinjektion](dependency-injection.md). Die `IHostingEnvironment` eingefügt wird, zum Bereitstellen der `ContentRootFileProvider`. Der zweite Parameter ist der Pfad zu der Rules-Datei, also *ApacheModRewrite.txt* in der Beispiel-app.

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=4)]

---

Die Beispiel-app leitet Anforderungen von `/apache-mod-rules-redirect/(.\*)` auf `/redirected?id=$1`. Statuscode der Antwort ist 302 (gefunden).

[!code[Main](url-rewriting/samples/2.x/ApacheModRewrite.txt)]

Ursprüngliche Anforderung:`/apache-mod-rules-redirect/1234`

![Browser-Fenster mit den Entwicklertools Nachverfolgen von Anforderungen und Antworten](url-rewriting/_static/add_apache_mod_redirect.png)

##### <a name="supported-server-variables"></a>Unterstützte Servervariablen
Die Middleware unterstützt die folgenden Apache Mod_rewrite Servervariablen:
* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* ZEIT
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>IIS URL Rewrite-Modul-Regeln
Verwenden, um Regeln zu verwenden, die für das IIS URL Rewrite-Modul gelten `AddIISUrlRewrite`. Stellen Sie sicher, dass die Rules-Datei mit der app bereitgestellt wird. Nicht weiterleiten die Middleware verwenden Ihre *"Web.config"* bei Ausführung auf Windows Server IIS-Datei. Bei IIS diese Regeln gespeichert werden sollen, außerhalb von Ihrem *"Web.config"* zur Vermeidung von Konflikten mit der IIS-Rewrite-Modul. Weitere Informationen und Beispiele für IIS URL Rewrite-Modul-Regeln finden Sie unter [mithilfe von Url Rewrite-Modul 2.0](https://docs.microsoft.com/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) und [Konfiguration des URL schreiben Modulverweis](https://docs.microsoft.com/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Ein `StreamReader` wird verwendet, lesen Sie die Regeln aus der *IISUrlRewrite.xml* Rules-Datei.

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=2,8)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Der erste Parameter erwartet ein `IFileProvider`, während der zweite Parameter den Pfad zu den Regeln der XML-Datei wird also *IISUrlRewrite.xml* in der Beispiel-app.

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=5)]

---

Die Beispiel-app ändert die Anforderungen von `/iis-rules-rewrite/(.*)` auf `/rewritten?id=$1`. Die Antwort wird an den Client mit Statuscode 200 (OK) gesendet.

[!code-xml[Main](url-rewriting/samples/2.x/IISUrlRewrite.xml)]

Ursprüngliche Anforderung:`/iis-rules-rewrite/1234`

![Browser-Fenster mit den Entwicklertools nachverfolgen, die Anforderung und Antwort](url-rewriting/_static/add_iis_url_rewrite.png)

Wenn Sie eine aktive IIS Rewrite-Modul auf Serverebene Regeln konfiguriert, die Ihre app auf unerwünschten Weise beeinträchtigen würde haben, können Sie das IIS Rewrite-Modul für eine app deaktivieren. Weitere Informationen finden Sie unter [Deaktivieren von IIS-Module](xref:host-and-deploy/iis/modules#disabling-iis-modules).

#### <a name="unsupported-features"></a>Nicht unterstützte Funktionen

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Die Middleware freigegeben mit ASP.NET Core 2.x unterstützt die folgenden Funktionen von IIS URL Rewrite-Modul:
* Ausgehende Regeln
* Benutzerdefinierte Servervariablen
* Platzhalter
* LogRewrittenUrl

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Die Middleware freigegeben mit ASP.NET Core 1.x unterstützt die folgenden Funktionen von IIS URL Rewrite-Modul:
* Globale Regeln
* Ausgehende Regeln
* Schreiben von Zuordnungen
* CustomResponse Aktion
* Benutzerdefinierte Servervariablen
* Platzhalter
* Action:CustomResponse
* LogRewrittenUrl

---

#### <a name="supported-server-variables"></a>Unterstützte Servervariablen
Die Middleware unterstützt die folgenden Servervariablen von IIS URL Rewrite-Modul:
* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> Sie erhalten außerdem einen `IFileProvider` über eine `PhysicalFileProvider`. Dieser Ansatz kann größere Flexibilität für den Speicherort, der die Umgestaltung Rules-Dateien bereitstellen. Stellen Sie sicher, dass Ihre Rewrite Rules-Dateien in den Pfad auf dem Server bereitgestellt werden, die Sie bereitstellen.
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Methodenbasierte Regel
Verwendung `Add(Action<RewriteContext> applyRule)` Ihre eigene Regellogik in einer Methode zu implementieren. Die `RewriteContext` macht die `HttpContext` für die Verwendung in der Methode. Die `context.Result` bestimmt, wie zusätzliche Pipeline Verarbeitung erfolgt.

| context.Result                       | Aktion                                                          |
| ------------------------------------ | --------------------------------------------------------------- |
| `RuleResult.ContinueRules` (Standardwert) | Anwenden von Regeln wird fortgesetzt                                         |
| `RuleResult.EndResponse`             | Beenden Sie die Regeln angewendet werden, und Senden der Antwort                       |
| `RuleResult.SkipRemainingRules`      | Beenden Sie die Regeln angewendet werden, und senden Sie den Kontext an die nächste middleware |

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=9)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=6)]

---

Die Beispiel-app zeigt eine Methode, die leitet Anforderungen für Pfade, die mit enden *XML*. Wenn Sie eine Anforderung für vornehmen `/file.xml`, wird er an umgeleitet `/xmlfiles/file.xml`. Der Statuscode 301 (Permanent verschoben) festgelegt. Für eine Umleitung zu können, müssen Sie explizit den Statuscode der Antwort festlegen. Andernfalls wird ein Statuscode "200 (OK)" zurückgegeben, und die Umleitung wird nicht auf dem Client auftreten.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/RewriteRules.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet2)]

---

Ursprüngliche Anforderung:`/file.xml`

![Browser-Fenster mit den Entwicklertools Nachverfolgen von Anforderungen und Antworten für file.xml](url-rewriting/_static/add_redirect_xml_requests.png)

### <a name="irule-based-rule"></a>IRule basierende Regel
Verwendung `Add(IRule)` Ihre eigene Regellogik in einer Klasse zu implementieren, die abgeleitet `IRule`. Mithilfe einer `IRule` bietet größere Flexibilität gegenüber des methodenbasierten Regel Ansatzes. Die abgeleitete Klasse eventuell einen Konstruktor, in dem Sie im Parameter für übergeben können die `ApplyRule` Methode.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=10-11)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=7-8)]

---

Die Werte der Parameter in der Beispiel-app für die `extension` und `newPath` werden überprüft, um mehrere Bedingungen erfüllen. Die `extension` muss einen Wert enthalten, und der Wert muss *PNG*, *jpg*, oder *GIF*. Wenn die `newPath` ist ungültig, ein `ArgumentException` ausgelöst wird. Wenn Sie eine Anforderung für vornehmen *image.png*, wird er an umgeleitet `/png-images/image.png`. Wenn Sie eine Anforderung für vornehmen *image.jpg*, wird er an umgeleitet `/jpg-images/image.jpg`. Der Statuscode 301 (Permanent verschoben), festgelegt ist und die `context.Result` Regeln für die Verarbeitung zu beenden, und Senden der Antwort festgelegt ist.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/RewriteRules.cs?name=snippet2)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/RewriteRule.cs?name=snippet1)]

---

Ursprüngliche Anforderung:`/image.png`

![Browser-Fenster mit den Entwicklertools Nachverfolgen von Anforderungen und Antworten für image.png](url-rewriting/_static/add_redirect_png_requests.png)

Ursprüngliche Anforderung:`/image.jpg`

![Browser-Fenster mit den Entwicklertools Nachverfolgen von Anforderungen und Antworten für image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Regex-Beispiele

| Ziel | Regex-Zeichenfolge &<br>Match-Beispiel | Ersetzungszeichenfolge &<br>Ausgabebeispiel |
| ---- | :-----------------------------: | :------------------------------------: |
| Schreiben Sie Pfad in der Abfragezeichenfolge | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Bereichsstreifen nachgestellten Schrägstrich | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Erzwingen Sie die nachstehenden Schrägstrich | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Vermeiden Sie umschreiben bestimmte Anforderungen | `^(.*)(?<!\.axd)$` oder `^(?!.*\.axd$)(.*)$`<br>"Ja":`/resource.htm`<br>Nein:`/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Neuanordnen von URL-Segmente | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Ersetzen Sie ein URL-segment | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Application Startup (Starten von Anwendungen)](startup.md)
* [Middleware](middleware.md)
* [Reguläre Ausdrücke in .NET](/dotnet/articles/standard/base-types/regular-expressions)
* [Sprachelemente für reguläre Ausdrücke – Kurzübersicht](/dotnet/articles/standard/base-types/quick-ref)
* [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)
* [Verwenden von Url-Rewrite-Modul 2.0 (für IIS)](https://docs.microsoft.com/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [URL Rewrite-Modul Konfigurationsverweis](https://docs.microsoft.com/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [IIS URL Rewrite-Modul-Forum](https://forums.iis.net/1152.aspx)
* [Behalten Sie eine einfache URL-Struktur](https://support.google.com/webmasters/answer/76329?hl=en)
* [Tipps und Tricks 10 URLs](http://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [Schrägstrich oder nicht Schrägstrich](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
