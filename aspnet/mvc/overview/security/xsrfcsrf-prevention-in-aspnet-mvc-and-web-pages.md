---
uid: mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages
title: XSRF/vom CSRF-Schutz in ASP.NET MVC und Web Pages | Microsoft Docs
author: Rick-Anderson
description: "Websiteübergreifende anforderungsfälschung (auch bekannt als XSRF oder FORGERY) ist ein Angriff gegen Web gehostete Anwendungen, bei dem eine bösartige Website die Interakti beeinflussen können..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/14/2013
ms.topic: article
ms.assetid: aadc5fa4-8215-4fc7-afd5-bcd2ef879728
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages
msc.type: authoredcontent
ms.openlocfilehash: 6cf30daa7ed966b11405cec715c5bc803b567249
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2018
---
<a name="xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages"></a>XSRF/vom CSRF-Schutz in ASP.NET MVC und Web Pages
====================
Durch [Rick Anderson](https://github.com/Rick-Anderson)

> Cross-Site-Anforderung, dass (auch bekannt als XSRF oder FORGERY) auf einen Angriff auf Web gehostete Anwendungen ist bei dem eine bösartige Website für die Interaktion zwischen einem Clientbrowser und eine Website, die von diesen Browser als vertrauenswürdig eingestuft beeinflussen können. Diese Angriffe sind möglich, da Webbrowsern Authentifizierungstoken automatisch mit jeder Anforderung an eine Website gesendet werden. Kanonisches Beispiel ist ein Authentifizierungscookie, z. B. ASP. NET Forms-Authentifizierungsticket. Websites, die alle persistenten Authentifizierungsmechanismus (z. B. Windows-Authentifizierung, Basic, usw.) verwenden können jedoch durch diese Angriffe angesprochen werden.
> 
> Ein XSRF-Angriffen unterscheidet sich von einem Phishing-Attacke. Phishing-Angriffe erfordern Eingreifen durch das Opfer. In einer Phishing-Attacke eine bösartige Website wird die Zielwebsite imitieren und das Opfer erkennen Sie vertraulichen Informationen an den Angreifer bereitzustellen. Bei einem Angriff XSRF erfolgt häufig keine Interaktion erforderlich ist, über das Opfer. Stattdessen wird der Angreifer auf den Browser senden alle relevanten Cookies automatisch an den Zielstandort für die Web Vertrauensstellungen der vertrauenden Seite.
> 
> Weitere Informationen finden Sie unter der [öffnen Webanwendungsprojekt Sicherheit](https://www.owasp.org/index.php/Main_Page)(OWASP) [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)).


## <a name="anatomy-of-an-attack"></a>Anatomie eines Angriffs

Um eine XSRF-Angriffen zu durchlaufen, sollten Sie in ein Benutzer, einige Onlinebankingtransaktionen ausführen möchte. Dieser Benutzer besucht zuerst WoodgroveBank.com und Protokolle, an welchem, die Punkt der Antwortheader ihr Authentifizierungscookie enthalten wird:

[!code-console[Main](xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages/samples/sample1.cmd)]

Da das Authentifizierungscookie ein Sitzungscookie ist, wird es vom Browser automatisch zurückgesetzt, wenn der Browserprozess beendet wird. Allerdings wird der Browser bis zu diesem Zeitpunkt wird automatisch das Cookie mit jeder Anforderung zum WoodgroveBank.com enthalten. Der Benutzer möchte nun $1000 an ein anderes Konto übertragen werden, so dass sie ein Formular auf der Website Banking ausfüllt, und der Browser diese Anforderung an dem Server sendet:

[!code-console[Main](xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages/samples/sample2.cmd)]

Da dieser Vorgang einen Nebeneffekt hat (es initiiert eine finanzielle Transaktion), hat die Banking-Website ausgewählt, HTTP POST erforderlich ist, um diesen Vorgang zu initiieren. Der Server liest das Authentifizierungstoken aus der Anforderung, sucht der Kontonummer des aktuellen Benutzers, stellt sicher, dass Deckung vorhanden sind, und klicken Sie dann die Transaktion in das Zielkonto initiiert.

Ihr Onlinebanking abgeschlossen, der Benutzer navigiert Weg von der Website Banking und Besuche von anderen Speicherorten im Web. Einer dieser Websites – "fabrikam.com" – enthält das folgende Markup auf einer Seite eingebettet ein &lt;Iframe&gt;:

[!code-html[Main](xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages/samples/sample3.html)]

Die wiederum führt dazu, dass den Browser für diese Anforderung:

[!code-console[Main](xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages/samples/sample4.cmd)]

Der Angreifer missbraucht die Tatsache, dass der Benutzer möglicherweise ein gültiges Authentifizierungstoken für die Ziel-Website noch, und sie einen kleinen Ausschnitt von Javascript verwendet wird, um die dazu führen, dass den Browser HTTP POST an den Zielstandort automatisch. Wenn das Authentifizierungstoken noch gültig ist, wird die Banking-Website eine Übertragung der 250 US-Dollar berücksichtigt der Angreifer Wahl initiiert.

### <a name="ineffective-mitigations"></a>Ineffektiven Maßnahmen

Es ist interessant, beachten, dass im obigen Szenario ist die Tatsache, dass WoodgroveBank.com wurde über SSL zugegriffen, und es ein Cookie für die Authentifizierung nur über SSL wurde nicht ausreichend, um das Risiko eines Angriffs vereiteln wurde. Der Angreifer kann an die [URI-Schema](http://en.wikipedia.org/wiki/URI_scheme) (Https) in ihrem &lt;Formular&gt; Element und den Browser nicht abgelaufenen Cookies an den Zielstandort gesendet werden, solange diese Cookies konsistent mit dem URI sind weiterhin das vorgesehene Ziel-Schema.

Eine könnte argumentieren, dass der Benutzer nicht besuchen Sie einfach sollte nicht vertrauenswürdigen Sites besuchen nur vertrauenswürdigen Sites ist, hilft Ihnen bei der sicheren online bleiben. Es gibt einige Wahrheit dieser allerdings leider diesen Rat ist nicht immer praktisch. Vielleicht vertraut"der Benutzer" die Website für lokale Nachrichten ConsolidatedMessenger. ConsolidatedMessenger.com und kopiert wird, die stattdessen site besuchen, aber dieser Standort verfügt über eine XSS-Schwachstelle, wodurch Angreifern, den gleichen Codeausschnitt einzufügen, die auf "fabrikam.com" ausgeführt wurde.

Sie können sicherstellen, dass eingehende Anforderungen eine [referenzheader](http://www.w3.org/Protocols/HTTP/HTRQ_Headers.html#z14) verweisen auf Ihrer Domäne. Dies wird von einer Drittanbieter-Domäne unwissentlich übermittelte Anforderungen beendet. Allerdings einige Personen Deaktivieren des Browsers Referer-Header Gründen des Datenschutzes und Angreifer können manchmal diesen Header spoofen, wenn das Opfer bestimmte unsichere-Software installiert ist. Überprüfen der [referenzheader](http://www.w3.org/Protocols/HTTP/HTRQ_Headers.html#z14) einen sicheren Ansatz für die Verhinderung von XSRF-Angriffen wird nicht berücksichtigt.

## <a name="web-stack-runtime-xsrf-mitigations"></a>Web Stapel Runtime XSRF Maßnahmen

Der ASP.NET Web-Stack-Runtime verwendet eine Variante der [für die domänensynchronisierung tokenmuster](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)_Prevention_Cheat_Sheet#General_Recommendation:_Synchronizer_Token_Pattern) Verteidigung gegen XSRF-Angriffen. Das allgemeine Format des Musters token für die domänensynchronisierung ist, dass zwei Anti-XSRF-Token mit jeder HTTP-POST (zusätzlich zum das Authentifizierungstoken) an den Server übermittelt werden: eine als ein Cookie, und der andere als ein Formularwert-Token. Die Tokenwerte generiert, durch die ASP.NET-Laufzeit sind nicht deterministisch oder von einem Angreifer vorhersagbare. Wenn das Token übermittelt werden, lässt der Server die Anforderung fortgesetzt werden, nur dann, wenn beide Token eine Vergleich die Überprüfung bestanden.

Die Anforderung XSRF-Überprüfung *Sitzungstoken* wird als ein HTTP-Cookie gespeichert und derzeit die folgende Informationen in der Nutzlast enthält:

- Ein Sicherheitstoken, bestehend aus einer zufälligen 128-Bit-Bezeichner.   
 Die folgende Abbildung zeigt das XSRF-Überprüfung Sitzung Anforderungstoken angezeigt, mit der Internet Explorer F12-Entwicklungstools: (Hinweis Dies ist die aktuelle Implementierung und Betreff "," sogar wahrscheinlich ändern.)

![](xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages/_static/image1.png)

Die *Feldtoken* gespeichert ist, als ein `<input type="hidden" />` und enthält die folgenden Informationen in der Nutzlast:

- Benutzername des angemeldeten Benutzers, (wenn nicht authentifiziert).
- Alle zusätzlichen Daten, das durch ein [IAntiForgeryAdditionalDataProvider](https://msdn.microsoft.com/library/system.web.helpers.iantiforgeryadditionaldataprovider(v=vs.111).aspx).

Die Nutzlast der Anti-XSRF-Token werden verschlüsselt und signiert, damit Sie den Benutzernamen nicht anzeigen können, wenn Sie Tools verwenden, um die Token zu überprüfen. Wenn die Webanwendung ASP.NET 4.0 vorgesehen ist, werden von kryptografische Dienste bereitgestellt der [MachineKey.Encode](https://msdn.microsoft.com/library/system.web.security.machinekey.encode.aspx) Routine. Wenn die Webanwendung vorgesehen ist ASP.NET 4.5 oder höher, kryptografische Dienste über bereitgestellt werden die [MachineKey.Protect](https://msdn.microsoft.com/library/system.web.security.machinekey.protect(v=vs.110)) -Routine, die eine bessere Leistung, Erweiterbarkeit und Sicherheit bietet. Sehen Sie sich die folgenden Blogbeiträge Weitere Details:

- [Kryptografische Verbesserungen in ASP.NET 4.5, pt. 1](https://blogs.msdn.com/b/webdev/archive/2012/10/22/cryptographic-improvements-in-asp-net-4-5-pt-1.aspx)
- [Kryptografische Verbesserungen in ASP.NET 4.5, pt. 2](https://blogs.msdn.com/b/webdev/archive/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2.aspx)
- [Kryptografische Verbesserungen in ASP.NET 4.5, pt. 3](https://blogs.msdn.com/b/webdev/archive/2012/10/24/cryptographic-improvements-in-asp-net-4-5-pt-3.aspx)

## <a name="generating-the-tokens"></a>Generieren von Token

Um die Anti-XSRF-Token zu generieren, rufen die [ @Html.AntiForgeryToken ](https://msdn.microsoft.com/library/dd470175.aspx) Methode aus einer MVC-Ansicht oder @AntiForgery.GetHtml() aus einer Razor-Page. Die Common Language Runtime führt dann die folgenden Schritte aus:

1. Wenn die aktuelle HTTP-Anforderung bereits eine Sitzung Anti-XSRF-Token enthält (das Cookie Anti-XSRF \_ \_RequestVerificationToken), das Sicherheitstoken wird daraus extrahiert. Wenn die HTTP-Anforderung enthält kein Anti-XSRF-Token-Sitzung oder wenn Fehler bei der Extraktion des Sicherheitstokens, ein neues zufälliges Anti-XSRF-Token generiert werden.
2. Ein Feld Anti-XSRF-Token wird mithilfe des Sicherheitstokens aus vorigen Schritt (1) und die Identität des aktuellen angemeldeten Benutzers generiert. (Weitere Informationen zum Ermitteln der Benutzeridentität finden Sie unter der  **[Szenarien mit Unterstützung für spezielle](#_Scenarios_with_special)**  Abschnitt weiter unten.) Darüber hinaus Wenn ein [IAntiForgeryAdditionalDataProvider](https://msdn.microsoft.com/library/jj158328(v=vs.111).aspx) wird konfiguriert, die Common Language Runtime ruft seine [GetAdditionalData](https://msdn.microsoft.com/library/system.web.helpers.iantiforgeryadditionaldataprovider.getadditionaldata(v=vs.111).aspx) Methode und die zurückgegebene Zeichenfolge im Feldtoken enthalten. (Siehe die  **[Konfigurations- und Erweiterbarkeit](#_Configuration_and_extensibility)**  Abschnitt, um weitere Informationen.)
3. Neues Anti-XSRF-Token in Schritt (1) generiert wurde, wird ein Token für die neue Sitzung erstellt werden, enthalten und wird der ausgehenden HTTP-Cookies-Auflistung hinzugefügt werden. Wird das Feldtoken aus Schritt (2) umschlossen werden ein `<input type="hidden" />` -Element, und diese HTML-Markup wird der Rückgabewert der `Html.AntiForgeryToken()` oder `AntiForgery.GetHtml()`.

## <a name="validating-the-tokens"></a>Überprüfen die Token

Um die eingehenden Anti-XSRF-Token zu überprüfen, die Entwickler umfasst eine [ValidateAntiForgeryToken](https://msdn.microsoft.com/library/system.web.mvc.validateantiforgerytokenattribute(VS.108).aspx) Attribut für ihre MVC-Aktion oder des Controllers oder She Aufrufe `@AntiForgery.Validate()` aus ihrem Razor-Page. Die Common Language Runtime führt die folgenden Schritte aus:

1. Die eingehende Sitzungstoken und das Feldtoken werden gelesen, und die Anti-XSRF-Token extrahiert aus jedem. Die Anti-XSRF-Token müssen pro Schritt (2) in der Routine Generation identisch sein.
2. Wenn der aktuelle Benutzer authentifiziert ist, wird ihr Benutzername mit dem Benutzernamen in das Feldtoken gespeicherten verglichen. Die Benutzernamen übereinstimmen.
3. Wenn ein [IAntiForgeryAdditionalDataProvider](https://msdn.microsoft.com/library/system.web.helpers.iantiforgeryadditionaldataprovider(v=vs.111).aspx) konfiguriert ist, ruft die Laufzeit die *ValidateAdditionalData* Methode. Die Methode muss den booleschen Wert zurückgeben *"true"*.

Bei erfolgreicher Überprüfung wird die Anforderung zugelassen, um den Vorgang fortzusetzen. Wenn die Validierung fehlschlägt, löst das Framework eine *HttpAntiForgeryException*.

## <a name="failure-conditions"></a>Fehlerbedingungen

Beginnend mit der ASP.NET Web-Stapel Runtime v2, alle *HttpAntiForgeryException* ausgelöst, die während der Validierung enthält ausführliche Informationen zu den Einzelheiten. Die aktuell definierten fehlerbedingungen sind:

- Das Sitzungstoken oder formulartokens ist nicht in der Anforderung vorhanden.
- Das Sitzungstoken oder formulartokens ist nicht lesbar. Die wahrscheinlichste Ursache ist eine nicht übereinstimmende Versionen der ASP.NET Web-Stapel Runtime oder eine Farm-Farm, in dem die &lt;MachineKey&gt; Element in der Datei "Web.config" unterscheidet sich zwischen Computern. Sie können ein Tool wie Fiddler verwenden, diese Ausnahme zu erzwingen, indem Sie die Manipulation von entweder Anti-XSRF-Token.
- Das Sitzungstoken und Feldtoken wurden ausgetauscht.
- Das Sitzungstoken und Feldtoken enthalten, nicht übereinstimmende Sicherheitstoken.
- Der Benutzername, der in das Feldtoken eingebettet entspricht nicht den angemeldeten Benutzernamen des aktuellen Benutzers.
- Die  *[IAntiForgeryAdditionalDataProvider.ValidateAdditionalData](https://msdn.microsoft.com/library/system.web.helpers.iantiforgeryadditionaldataprovider.validateadditionaldata(v=vs.111).aspx)*  zurückgegebene Methode *"false"*.

Die Anti-XSRF-Funktionen möglicherweise auch zusätzliche wird die Überprüfung während der Generierung von Tokens oder Überprüfung durchgeführt, und Fehler während dieser Tests möglicherweise ausgelösten Ausnahmen. Finden Sie unter der [WIF / ACS / anspruchsbasierte Authentifizierung](#_WIF_ACS) und  **[Konfigurations- und Erweiterbarkeit](#_Configuration_and_extensibility)**  Abschnitten Weitere Informationen.

<a id="_Scenarios_with_special"></a>

## <a name="scenarios-with-special-support"></a>Szenarien mit spezielle Unterstützung

### <a name="anonymous-authentication"></a>Anonyme Authentifizierung

Die Anti-XSRF-System enthält spezielle Unterstützung für anonyme Benutzer, wobei "Anonym" als ein Benutzer definiert ist, in dem die *IIdentity.IsAuthenticated* -Eigenschaft gibt *"false"*. Szenarien zum Schutz der Anmeldeseite (bevor der Benutzer authentifiziert wurde) und benutzerdefinierte Authentifizierungsschemas, bei denen die Anwendung einen Mechanismus außer verwendet, XSRF *"IIdentity" wird* zur Identifizierung von Benutzern.

Um diese Szenarien zu unterstützen, denken Sie daran, dass die Sitzung und Feld-Token durch ein Sicherheitstoken verknüpft sind, also eine 128-Bit-zufällig generierte nicht transparenter Bezeichner. Dieses Sicherheitstoken wird verwendet, um eine einzelne benutzersitzung nachzuverfolgen, wie sie den Standort so effektiv den Zweck eines anonymen Bezeichners dient navigiert. Eine leere Zeichenfolge ist für die Generierung und Überprüfung Routinen, die oben beschriebenen anstelle des Benutzernamens verwendet.

<a id="_WIF_ACS"></a>

### <a name="wif--acs--claims-based-authentication"></a>WIF / ACS / anspruchsbasierter Authentifizierung

In der Regel wird die *"IIdentity" wird* Klassen, die in .NET Framework integriert haben, die Eigenschaft, die *IIdentity.Name* ist ausreichend, um einen bestimmten Benutzer innerhalb einer bestimmten Anwendung eindeutig identifizieren. Beispielsweise *FormsIdentity.Name* gibt den Benutzernamen, die in der Mitgliedschaftsdatenbank (die für alle Anwendungen, abhängig von der Datenbank eindeutig ist), gespeicherte *WindowsIdentity.Name* gibt die domänenqualifizierte Identität des Benutzers, und So weiter. Diese Systeme bieten nicht nur die Authentifizierung; Sie auch *identifizieren* Benutzer zu einer Anwendung.

Anspruchsbasierte Authentifizierung auf der anderen Seite erfordert nicht unbedingt einen bestimmten Benutzer zu identifizieren. Stattdessen die *ClaimsPrincipal* und *"ClaimsIdentity"* Typen werden mit einem Satz von zugeordneten *Anspruch* Instanzen, in denen die einzelnen Ansprüche möglicherweise "über 18 Jahre alt ist" oder " ist ein Administrator"zu einem anderen. Da der Benutzer noch nicht notwendigerweise identifiziert wurde, kann die Common Language Runtime nicht verwenden die *ClaimsIdentity.Name* Eigenschaft als eindeutiger Bezeichner für diesen bestimmten Benutzer. Das Team kann angezeigt werden, praktische Beispiele, in denen *ClaimsIdentity.Name* gibt *null*zurückgegeben einen angezeigten Namen, oder andernfalls gibt eine Zeichenfolge, die nicht für die Verwendung als eindeutiger Bezeichner geeignet ist für den Benutzer.

Viele Bereitstellungen, die anspruchsbasierte Authentifizierung verwendet verwenden [Azure Access Control Service](https://msdn.microsoft.com/library/windowsazure/gg429786.aspx) (ACS) insbesondere. ACS ermöglicht es dem Entwickler so konfigurieren Sie einzelne *Identitätsanbieter* (z. B. AD FS, die Microsoft-Account-Anbieter, OpenID-Anbietern wie Yahoo! usw.), und geben Sie die Identitätsanbieter zurück *Benennen von Bezeichnern*. Diese Namen Bezeichner können Daten mit persönlich identifizierbaren Informationen (PII) enthalten, wie eine e-Mail-Adresse, oder sie können z. B. einen privaten persönlichen Bezeichner (PPID) anonymisiert. Unabhängig davon, das Tupel (Identitätsanbieter, Namensbezeichner) ausreichend dient als entsprechende Überwachungsprofil Token für einen bestimmten Benutzer, während sie die Website durchsucht, damit der ASP.NET Web-Stapel Runtime Tupel anstelle von Benutzernamen verwenden können, während der Generierung und Überprüfen die Anti-XSRF-Feld-Token. Die bestimmte URIs für den Identitätsanbieter und der Namensbezeichner sind:

- `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`
- `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`

(finden Sie in diesem [ACS Doc Seite](https://msdn.microsoft.com/library/windowsazure/gg185971.aspx) für Weitere Informationen.)

Beim Generieren oder Überprüfen eines Tokens, wird der ASP.NET Web-Stapel Runtime Bindung zu den Typen zur Laufzeit versucht:

- `Microsoft.IdentityModel.Claims.IClaimsIdentity, Microsoft.IdentityModel, Version=3.5.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35`(Für die WIF-SDK).
- `System.Security.Claims.ClaimsIdentity`(Für .NET 4.5).

Wenn diese Typen vorhanden sind, und wenn des aktuellen Benutzers *IIIIdentity* implementiert oder Unterklassen eines der folgenden Typen ist, wird die Anti-XSRF-Funktion (Identitätsanbieter, Namensbezeichner) Tupel anstelle der Benutzername beim Generieren und Überprüfen die Token an. Wenn keine solche Tupel vorhanden ist, schlägt die Anforderung mit einem Fehler beschrieben, die Entwickler zum Konfigurieren des Anti-XSRF-Systems, um zu verstehen, den bestimmten anspruchsbasierte Authentifizierungsmechanismus verwendet fehl. Finden Sie unter der  **[Konfigurations- und Erweiterbarkeit](#_Configuration_and_extensibility)**  Abschnitt, um weitere Informationen.

### <a name="oauth--openid-authentication"></a>OAuth / OpenID-Authentifizierung

Schließlich verfügt die Anti-XSRF-Funktion spezielle Unterstützung für Anwendungen, die OAuth- oder OpenID-Authentifizierung verwenden. Diese Unterstützung basiert auf Heuristik: Wenn die aktuelle *IIdentity.Name* beginnt mit http:// oder https://, dann Benutzernamen Vergleiche erfolgen, werden der OrdinalIgnoreCase Standardvergleich, statt einen ordinalen Vergleich verwenden.

<a id="_Configuration_and_extensibility"></a>

## <a name="configuration-and-extensibility"></a>Konfiguration und Erweiterbarkeit

In einigen Fällen möchten Entwickler eine strengere Kontrolle über die Anti-XSRF-Generierung und Überprüfung Verhalten. Z. B. Hilfsprogramme MVC und Web Pages-Standardverhalten automatisch zum Hinzufügen von HTTP-Cookies, in die Antwort ist möglicherweise nicht wünschenswert, und der Entwickler die Token an anderer Stelle beibehalten möchten. Es gibt noch zwei APIs, die dabei helfen:

`AntiForgery.GetTokens(string oldCookieToken, out string newCookieToken, out string formToken);`  
`AntiForgery.Validate(string cookieToken, string formToken);`

Die *GetTokens* Methode nimmt als Eingabe eine vorhandenen XSRF-Überprüfung Sitzung Anforderungstoken (die null sein kann) und erzeugt als Ausgabe eine neue XSRF-Überprüfung Sitzung Anforderungstoken und Feldtoken. Die Token sind einfach opake Zeichenfolgen ohne Dekoration; die *FormToken* Wert wird für die Instanz nicht umschlossen werden ein &lt;input&gt; Tag. Die *NewCookieToken* Wert ist möglicherweise null; in diesem Fall wird das *OldCookieToken* Wert ist immer noch gültig und muss kein neues Antwortcookie festgelegt werden. Der Aufrufer *GetTokens* ist verantwortlich für alle erforderlichen Antwort Cookies beibehalten oder Generieren von Markup erforderlich; die *GetTokens* Methode selbst ändert sich nicht auf die Antwort als einen Nebeneffekt auslösen. Die *Validate* Methode akzeptiert die eingehende Sitzung und Feld-Token und die zuvor erwähnten Validierungslogik durchläuft.

### <a name="antiforgeryconfig"></a>AntiForgeryConfig

Der Entwickler kann die Anti-XSRF-System aus Anwendung konfigurieren\_starten. Programmgesteuerte ist konfiguriert. Die Eigenschaften des statischen *AntiForgeryConfig* Typ im folgenden beschrieben. Die meisten Benutzer mit Ansprüchen sollten die UniqueClaimTypeIdentifier-Eigenschaft festgelegt.

| **Property** | **Beschreibung** |
| --- | --- |
| **AdditionalDataProvider** | Ein [IAntiForgeryAdditionalDataProvider](https://msdn.microsoft.com/library/system.web.helpers.iantiforgeryadditionaldataprovider(v=vs.111).aspx) , die zusätzliche Daten während der Generierung von Tokens bereitstellt und verwendet zusätzliche Daten während der Überprüfung von token. Der Standardwert ist *null*. Weitere Informationen finden Sie unter der [IAntiForgeryAdditionalDataProvider](https://msdn.microsoft.com/library/system.web.helpers.iantiforgeryadditionaldataprovider(v=vs.111).aspx) Abschnitt. |
| **CookieName** | Eine Zeichenfolge, die den Namen des HTTP-Cookies bereitstellt, die zum Speichern der Sitzung Anti-XSRF-Token verwendet wird. Wenn dieser Wert nicht festgelegt ist, wird ein Name automatisch basierend auf der Anwendung bereitgestellten virtuellen Pfad generiert werden. Der Standardwert ist *null*. |
| **RequireSsl** | Ein boolescher Wert, der bestimmt, ob die Anti-XSRF-Token über einen SSL-gesicherte Kanal gesendet werden müssen. Wenn dieser Wert ist *"true"*Cookies automatisch generiert werden das "secure"-Flag festgelegt haben und die Anti-XSRF-APIs wird ausgelöst, wenn in einer Anforderung aufgerufen, die nicht über SSL übermittelt wird. Der Standardwert ist *FALSE*. |
| **SuppressIdentityHeuristicChecks** | Ein boolescher Wert, der bestimmt, ob die Anti-XSRF-System die Unterstützung für anspruchsbasierte Identitäten deaktiviert werden soll. Wenn dieser Wert ist *"true"*, wird das System davon ausgehen, dass *IIdentity.Name* eignet sich für die Verwendung als eine eindeutige pro-Benutzer-ID und versucht nicht, spezielle *IClaimsIdentity*oder *ClClaimsIdentity* wie beschrieben in der [WIF / ACS / anspruchsbasierte Authentifizierung](#_WIF_ACS) Abschnitt. Der Standardwert ist `false`. |
| **UniqueClaimTypeIdentifier** | Eine Zeichenfolge, die angibt, welcher Anspruchstyp eignet sich für die Verwendung als eine eindeutige pro-Benutzer-ID. Wenn dieser Wert festlegen und die aktuelle *"IIdentity" wird* angegebenen anspruchsbasierte, das System versucht, einen Anspruch des Typs extrahieren *UniqueClaimTypeIdentifier*, und der entsprechende Wert verwendet werden anstelle der Benutzername des Benutzers während der Generierung der Feld-Token. Wenn der Typ des Anspruchs nicht gefunden wird, wird das System die Anforderung fehl. Der Standardwert ist *null*, was bedeutet, dass das System verwenden soll, die (Identitätsanbieter, Namensbezeichner) Tupel anstelle der Benutzername des Benutzers wie zuvor beschrieben. |

<a id="_IAntiForgeryAdditionalDataProvider"></a>

### <a name="iantiforgeryadditionaldataprovider"></a>IAntiForgeryAdditionalDataProvider

Die  *[IAntiForgeryAdditionalDataProvider](https://msdn.microsoft.com/library/system.web.helpers.iantiforgeryadditionaldataprovider(v=vs.111).aspx)*  Typ ermöglicht Entwicklern das Verhalten des Anti-XSRF-Systems durch zusätzliche Round-Tripping-Daten in einzelnen Token zu erweitern. Die *GetAdditionalData* Methode wird jedes Mal aufgerufen, wenn ein Token wird generiert, und der Rückgabewert in das generierte Token eingebettet ist. Eine Implementierung konnte einen Zeitstempel, eine Nonce oder einen anderen Wert, den er möchte von dieser Methode zurückgeben.

Auf ähnliche Weise die *ValidateAdditionalData* Methode wird jedes Mal aufgerufen, wenn ein Token überprüft wird und die "zusätzliche Daten"-Zeichenfolge, die das Token eingebettet wurde an die Methode übergeben wird. Die Validierungsroutine kann einen Timeout (durch überprüfen die aktuelle Zeit mit dem Zeitpunkt, die gespeichert wurde, wenn das Token erstellt wurde) implementieren, eine Nonce überprüfen, Routine oder ein anderes gewünscht Logik.

## <a name="design-decisions-and-security-considerations"></a>Entwurfsentscheidungen und Überlegungen zur Sicherheit

Das Sicherheitstoken, das verknüpft und die Sitzung und Feld-Token ist technisch nur erforderlich, beim Versuch, eine anonyme / nicht authentifizierte Benutzer vor XSRF-Angriffen schützen. Wenn der Benutzer authentifiziert ist, konnte das Authentifizierungstoken selbst (vermutlich in Form eines Cookies übermittelt) verwendet werden, als die Hälfte der einer für die domänensynchronisierung Tokenpaars. Allerdings stehen gültige Szenarien für den Schutz von Anmeldeseiten, die durch nicht authentifizierte Benutzer erreicht, und die Anti-XSRF-Logik wurde durch immer generieren und überprüfen das Sicherheitstoken, dies gilt auch für authentifizierte Benutzer einfacher versucht. Es stellt auch einige zusätzlichen Schutz bereit, wenn ein Token von einem Angreifer, als festlegen oder zu erraten, dass das Sitzungstoken wäre, einen anderen Hürde für Angreifer zu überwinden je kompromittiert ist.

Entwickler sollten Vorsicht verwenden, wenn mehrere Anwendungen in einer einzigen Domäne gehostet werden. Beispielsweise, obwohl *example1.cloudapp.net* und *example2.cloudapp.net* sind verschiedene Hosts besteht ein implizites Vertrauensverhältnis zwischen allen Hosts unter der  *\*. cloudapp.net* Domäne. Diese implizite Vertrauensstellung [können potenziell nicht vertrauenswürdige Hosts Cookies gegenseitig beeinträchtigen](http://stackoverflow.com/questions/9636857/how-can-asp-net-or-asp-net-mvc-be-protected-from-related-domain-cookie-attacks) (die gleichen-Origin-Richtlinien, die AJAX-Anforderungen steuern unbedingt gelten nicht für HTTP-Cookies). Der ASP.NET Web-Stapel Runtime bietet einige Verringerung, der Benutzernamen in das Feldtoken eingebettet ist, selbst wenn eine böswillige Unterdomäne einen Sitzungstoken überschreiben kann zum Generieren eines gültigen Felds Tokens für den Benutzer werden. Allerdings können nicht beim Hosten in einer derartigen Umgebung die integrierte Anti-XSRF-Routinen weiterhin Sitzungsübernahme oder Anmeldung XSRF Verteidigung gegen.

Die Anti-XSRF-Routinen werden derzeit nicht für verteidigen [Clickjacking](https://www.owasp.org/index.php/Clickjacking). Anwendungen, die selbst Verteidigung gegen Clickjacking möchten können dies ganz einfach durch eine X-Frame-Options senden: SAMEORIGIN-Header mit jeder Antwort. Dieser Header wird von allen aktuellen Browsern unterstützt. Weitere Informationen finden Sie unter der [IE-Blog](https://blogs.msdn.com/b/ieinternals/archive/2010/03/30/combating-clickjacking-with-x-frame-options.aspx), [SDL-Blog](https://blogs.msdn.com/b/sdl/archive/2009/02/05/clickjacking-defense-in-ie8.aspx), und [OWASP](https://www.owasp.org/index.php/Clickjacking). Die Web-Stapel ASP.NET-Laufzeit möglicherweise sind einige der nächsten Releases MVC und Webseiten Anti-XSRF-Hilfsprogrammen diese Header automatisch festgelegt, damit Anwendungen automatisch vor diesen Angriffen geschützt sind.

Entwickler von Websites sollte weiterhin stellen Sie sicher, dass die Website nicht XSS-Angriffe anfällig ist. XSS-Angriffen sind sehr leistungsstark und einem erfolgreichen Angriff wird auch der ASP.NET Web-Stapel Runtime entsprechende Schutzmaßnahmen vor XSRF-Angriffen unterbrochen.

## <a name="acknowledgment"></a>Bestätigung

[@LeviBroderick](https://twitter.com/LeviBroderick), ein Großteil der ASP.NET-Code für die Sicherheit den Großteil dieser Informationen geschrieben.
