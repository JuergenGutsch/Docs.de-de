---
title: Aktivieren der Authentifizierung mithilfe von Facebook, Google und anderen externen Anbietern
author: rick-anderson
description: Dieses Tutorial veranschaulicht, wie Sie eine ASP.NET Core 2.x-App mithilfe von OAuth 2.0 und externen Authentifizierungsanbietern entwickeln.
manager: wpickett
ms.author: riande
ms.date: 11/01/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authentication/social/index
ms.openlocfilehash: 7b72421f703db919daf640c54b725fc1497d2481
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="enabling-authentication-using-facebook-google-and-other-external-providers"></a>Aktivieren der Authentifizierung mithilfe von Facebook, Google und anderen externen Anbietern

<a name="security-authentication-social-logins"></a>

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Dieses Tutorial veranschaulicht, wie Sie eine ASP.NET Core 2.x-App erstellen, die Benutzern ermöglicht, sich mithilfe von OAuth 2.0 und Anmeldeinformationen von externen Authentifizierungsanbietern anzumelden.

Die Anbieter [Facebook](facebook-logins.md), [Twitter](twitter-logins.md), [Google](google-logins.md) und [Microsoft](microsoft-logins.md) werden in den folgenden Abschnitten behandelt. Andere Anbieter stehen in Paketen von Drittanbietern wie z.B. [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) und [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers) zur Verfügung.

![Symbole sozialer Medien wie Facebook, Twitter, Google Plus und Windows](index/_static/social.png)

Den Benutzern zu ermöglichen, sich mit ihren vorhandenen Anmeldeinformationen anzumelden, ist für diese komfortabel und verlagert einen Großteil der Komplexität der Verwaltung des Anmeldevorgangs an einen Drittanbieter. Beispiel dazu, wie Anmeldungen bei sozialen Medien für mehr Datenverkehr und gewonnene Kunden sorgen, finden Sie in Fallstudien von [Facebook](https://www.facebook.com/unsupportedbrowser) und [Twitter](https://dev.twitter.com/resources/case-studies).

Hinweis: Hier vorgestellte Pakete abstrahieren einen Großteil der Komplexität des OAuth-Authentifizierungsflusses, doch ein Verständnis der Details kann bei der Problembehandlung erforderlich sein. Viele Ressourcen sind verfügbar. Siehe z.B. [Einführung in OAuth 2](https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2) oder [Grundlegendes zu OAuth 2](http://www.bubblecode.net/2016/01/22/understanding-oauth2/). Einige Probleme können mithilfe einer Untersuchung des [ASP.NET Core-Quellcodes für die Anbieterpakete](https://github.com/aspnet/Security/tree/dev/src) behoben werden.

## <a name="create-a-new-aspnet-core-project"></a>Erstellen eines neuen ASP.NET Core-Projekts

* Erstellen Sie in Visual Studio 2017 ein neues Projekt auf der Startseite oder über **Datei > Neu > Projekt**.

* Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, die in der Kategorie **Visual C# > .NET Core** verfügbar ist:

![Dialogfeld "Neues Projekt"](index/_static/new-project.png)

* Tippen Sie auf **Webanwendung**, und vergewissern Sie sich, dass **Authentifizierung** auf **Einzelne Benutzerkonten** festgelegt ist:

![Dialogfeld „Neue Webanwendung“](index/_static/select-project.png)

Hinweis: Dieses Tutorial bezieht sich auf die ASP.NET Core 2.0 SDK-Version, die oben im Assistenten ausgewählt werden kann.

## <a name="apply-migrations"></a>Anwenden von Migrationen

* Führen Sie die App aus, und klicken Sie auf den Link **Anmelden**.
* Klicken Sie auf den Link **Als neuer Benutzer registrieren**.
* Geben Sie die E-Mail-Adresse und das Kennwort für das neue Konto ein, und wählen Sie dann **Registrieren** aus.
* Befolgen Sie die Anweisungen zum Anwenden von Migrationen.

## <a name="require-ssl"></a>Anfordern von SSL

OAuth 2.0 erfordert die Verwendung von SSL für die Authentifizierung über das HTTPS-Protokoll.

Hinweis: Mithilfe der Projektvorlagen **Webanwendung** oder **Web-API** für ASP.NET Core 2.x erstellte Projekte sind automatisch für das Aktivieren von SSL konfiguriert und werden mit einer HTTPS-URL gestartet, wenn **Einzelne Benutzerkonten** im Dialogfeld **Authentifizierung ändern** im Projekt-Assistenten, wie oben gezeigt, ausgewählt war.

* Machen Sie SSL für Ihre Website erforderlich, indem Sie die Schritte im Thema [Erzwingen von SSL in einer ASP.NET Core-App](xref:security/enforcing-ssl) befolgen.

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>Verwenden von SecretManager zum Speichern von Token, die von Anmeldeanbietern zugewiesen wurden

Anmeldeanbieter aus dem Bereich der sozialen Medien weisen während des Registrierungsvorgangs Token des Typs **Anwendungs-ID** und **Anwendungsgeheimnis** (die genaue Bezeichnung hängt vom Anbieter ab) zu.

Diese Werte sind gewissermaßen der *Benutzername* und das *Kennwort*, den/das Ihre Anwendung für den Zugriff auf die API des Anbieters verwendet. Diese Anmeldeinformationen bilden die „Geheimnisse“, die mithilfe von **Secret Manager** mit Ihrer Anwendungskonfiguration verknüpft werden können, anstatt diese direkt in Konfigurationsdateien zu speichern oder hartzucodieren.

Führen Sie die Schritte im Thema [Sichere Speicherung von App-Geheimnissen während der Entwicklung in ASP.NET Core](xref:security/app-secrets) aus, damit Sie Token speichern können, die von den nachstehenden Anmeldeanbietern zugewiesen werden.

## <a name="setup-login-providers-required-by-your-application"></a>Einrichten der für Ihre Anwendung erforderlichen Anmeldeanbietern

In den folgenden Themen erfahren Sie, wie Sie Ihre Anwendung für die entsprechenden Anbieter konfigurieren:

* Anweisungen für [Facebook](facebook-logins.md)
* Anweisungen für [Twitter](twitter-logins.md)
* Anweisungen für [Google](google-logins.md)
* Anweisungen für [Microsoft](microsoft-logins.md)
* Anweisungen für [andere Anbieter](other-logins.md)

## <a name="optionally-set-password"></a>Optionales Festlegen des Kennworts

Wenn Sie sich bei einem externen Anmeldeanbieter registrieren, wird kein Kennwort bei der App registriert. Dadurch entfällt für Sie Erstellen und Merken eines Kennworts für die Website. Sie werden allerdings auch vom externen Anmeldeanbieter abhängig. Wenn der externe Anmeldeanbieter nicht verfügbar ist, können Sie sich nicht bei der Website anmelden.

So erstellen Sie ein Kennwort und melden sich mithilfe Ihrer E-Mail-Adresse an, die Sie während des Anmeldevorgangs bei externen Anbietern festgelegt haben:

* Tippen Sie rechts oben auf den Link **Hallo<email alias>**, navigieren Sie zur Ansicht **Verwalten**.

![Ansicht „Verwalten“ der Webanwendung](index/_static/pass1a.png)

* Tippen Sie auf **Erstellen**.

![Seite zum Festlegen Ihres Kennworts](index/_static/pass2a.png)

* Legen Sie ein gültiges Kennwort fest, das Sie anschließend mit Ihrer E-Mail-Adresse zum Anmelden nutzen können.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde die externe Authentifizierung behandelt. Ferner wurden die Voraussetzungen für das Hinzufügen externer Anmeldungen zu Ihrer ASP.NET Core-App erläutert.

* Konsultieren Sie anbieterspezifische Seiten zum Konfigurieren von Anmeldungen für die von Ihrer App angeforderten Anbieter.
