---
uid: web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
title: "Verhindern von Cross-Site Request Websiteübergreifender Anforderungsfälschung-Angriffen in ASP.NET Web-API | Microsoft Docs"
author: MikeWasson
description: "Beschreibt die siteübergreifende Anforderung Websiteübergreifender anforderungsfälschung Angriff und wie Anti-CSRF-Measures in ASP.NET Web-API implementiert."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/12/2012
ms.topic: article
ms.assetid: 81d46f14-8f48-4d8c-830d-cc8d594dc11b
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
msc.type: authoredcontent
ms.openlocfilehash: 1cd03f3b396cc2ece1d8dbe6820f6277c02d8e62
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2017
---
<a name="preventing-cross-site-request-forgery-csrf-attacks-in-aspnet-web-api"></a>Verhindern von Cross-Site Request Websiteübergreifender Anforderungsfälschung-Angriffen in ASP.NET Web-API
====================
durch [Mike Wasson](https://github.com/MikeWasson)

Cross-Site Request Fälschung Websiteübergreifender ist ein Angriff, in denen eine bösartige Website sendet eine Anforderung mit einem gefährdeten Standort, in dem der Benutzer zurzeit angemeldet ist

Hier ist ein Beispiel von CSRF-Angriffen:

1. Ein Benutzer sich anmeldet www.example.com, Formularauthentifizierung verwenden.
2. Der Server authentifiziert den Benutzer. Die Antwort vom Server enthält ein Authentifizierungscookie.
3. Ohne Abmelden, wechselt der Benutzer eine bösartige Website. Diese bösartige Website enthält die folgenden HTML-Formular: 

    [!code-html[Main](preventing-cross-site-request-forgery-csrf-attacks/samples/sample1.html)]

    Beachten Sie, dass die formaktion an den Standort anfällig für nicht auf die bösartige Website sendet. Dies ist der "Cross-Site" Teil CSRF.
4. Der Benutzer klickt auf die Schaltfläche "Absenden". Der Browser umfasst das Authentifizierungscookie mit der Anforderung.
5. Die Anforderung auf dem Server mit dem Kontext des Benutzers Authentifizierung ausgeführt und Aktionen möglich, die ein authentifizierter Benutzer tun darf.

Obwohl in diesem Beispiel wird den Benutzer auf die Formularschaltfläche erforderlich sind, konnte die böswillige Seite so fest, wie Sie problemlos ein Skript ausführen, das das Formular automatisch sendet. Darüber hinaus verhindert mit SSL eine CSRF-Angriffen nicht, da die bösartige Website "https://"-Anforderung senden kann.

In der Regel sind CSRF-Angriffen möglich für Websites, die Cookies für die Authentifizierung verwenden, da Browser alle relevanten Cookies an die Ziel-Website senden. CSRF-Angriffen sind jedoch nicht beschränkt auf Cookies ausnutzen. Beispielsweise Basis-und Digestauthentifizierung sind auch anfällig. Nach der Anmeldung eines Benutzers mit Standard-oder Digestauthentifizierung. der Browser sendet automatisch die Anmeldeinformationen an, bis die Sitzung beendet.

## <a name="anti-forgery-tokens"></a>Fälschungssicherheitstoken

Zum Vermeiden von CSRF-Angriffen verwendet ASP.NET MVC fälschungssicherheitstoken, so genannte *Überprüfung Token anfordern*.

1. Der Client fordert eine HTML-Seite, die ein Formular enthält.
2. Der Server enthält zwei Token in der Antwort. Ein Token wird als Cookie gesendet. Der andere wird in ein ausgeblendetes Formularfeld eingefügt. Die Token werden nach dem Zufallsprinzip generiert, so, dass Angreifer die Werte ermittelt werden kann.
3. Wenn der Client das Formular sendet, muss er beide Token an den Server senden. Der Client sendet das cookietoken als Cookie, und er sendet das Token Form innerhalb der Formulardaten. (Ein Browserclient geschieht automatisch, wenn der Benutzer das Formular sendet.)
4. Wenn eine Anforderung nicht beide Token enthält, lässt der Server die Anforderung an.

Hier ist ein Beispiel eines HTML-Formulars mit einem ausgeblendeten Formular-Token:

[!code-html[Main](preventing-cross-site-request-forgery-csrf-attacks/samples/sample2.html)]

Fälschungssicherheitstoken verwendet werden, da die böswillige Seite das Benutzertoken aufgrund von Richtlinien für denselben Ursprung nicht lesen kann. ([Same-Origin-Richtlinien](http://www.w3.org/Security/wiki/Same_Origin_Policy) verhindern, dass Dokumente auf zwei verschiedenen Standorten aus zugreifen auf Inhalte des jeweils anderen gehostet. Damit der oben aufgeführten Beispiel kann die bösartige Seite Anforderungen an example.com senden, aber die Antwort nicht gelesen.)

Zum Verhindern von CSRF-Angriffen fälschungssicherheitstoken mit Beliebiges Authentifizierungsprotokoll verwenden, in dem im Hintergrund sendet der Browser die Anmeldeinformationen nach der Anmeldung des Benutzers aus. Dies umfasst Cookie-basierte Authentifizierung-Protokolle, z. B. Formularauthentifizierung, als auch Protokolle wie z. B. die Standard- und Digest-Authentifizierung.

Sie müssen das fälschungssicherheitstoken nonsafe Methoden (POST, PUT, DELETE). Stellen Sie außerdem sicher, dass die sichere Methoden (GET, HEAD) nicht keine Nebeneffekte haben. Darüber hinaus, wenn Sie die domänenübergreifende-Unterstützung, z. B. CORS oder JSONP, aktivieren sind sogar sichere Methoden wie z. B. GET potenziell anfällig für CSRF-Angriffen der Angreifer sensible Daten zu lesen.

## <a name="anti-forgery-tokens-in-aspnet-mvc"></a>Fälschungssicherheitstoken in ASP.NET MVC

Verwenden Sie zum Hinzufügen der fälschungssicherheitstoken zu einer Razor-Page der **HtmlHelper.AntiForgeryToken** Hilfsmethode:

[!code-cshtml[Main](preventing-cross-site-request-forgery-csrf-attacks/samples/sample3.cshtml)]

Diese Methode fügt die ausgeblendetes Formularfeld und zudem wird das cookietoken.

## <a name="anti-csrf-and-ajax"></a>Anti-CSRF und AJAX

Des formulartokens kann ein Problem für AJAX-Anforderungen, da eine AJAX-Anforderung, JSON-Daten nicht HTML-Formulardaten senden kann. Eine Lösung besteht darin, die Token in einem benutzerdefinierten HTTP-Header zu senden. Der folgende Code Razor-Syntax verwendet, um die Token zu generieren und fügt dann die Token eine AJAX-Anforderung hinzu. Die Token werden durch den Aufruf auf dem Server generierte **AntiForgery.GetTokens**.

[!code-html[Main](preventing-cross-site-request-forgery-csrf-attacks/samples/sample4.html)]

Beim Verarbeiten der Anforderung extrahieren Sie das Token aus dem Anforderungsheader. Rufen Sie anschließend die **AntiForgery.Validate** Methode zum Überprüfen der Token. Die **Validate** Methode löst eine Ausnahme aus, wenn das Token nicht gültig sind.

[!code-csharp[Main](preventing-cross-site-request-forgery-csrf-attacks/samples/sample5.cs)]
