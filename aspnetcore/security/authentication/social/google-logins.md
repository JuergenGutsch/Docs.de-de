---
title: Google externe Anmeldung Setup in ASP.NET Core
author: rick-anderson
description: Dieses Lernprogramm veranschaulicht die Integration von Google-Konto-Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
manager: wpickett
ms.author: riande
ms.date: 08/02/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authentication/google-logins
ms.openlocfilehash: d7c5e1f1564c1bbf345e28fa76ce0b11ae65b0d4
ms.sourcegitcommit: 93785b6b29a4996066fba05149348f1bdf881263
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2017
---
# <a name="configuring-google-authentication-in-aspnet-core"></a>Konfigurieren von Google-Authentifizierung in ASP.NET Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Dieses Lernprogramm veranschaulicht das Ihren Benutzern zur Anmeldung mit ihrem Google +-Kontos mit einem Beispiel ASP.NET Core 2.0-Projekt erstellt haben, auf die [vorherige Seite](index.md). Wir beginnen, indem Sie die folgenden der [offizielle Schritte](https://developers.google.com/identity/sign-in/web/devconsole-project) zum Erstellen einer neuen app im Google-API-Konsole.

## <a name="create-the-app-in-google-api-console"></a>Erstellen der app im Google-API-Konsole

* Navigieren Sie zu [https://console.developers.google.com/projectselector/apis/library](https://console.developers.google.com/projectselector/apis/library) und melden Sie sich. Wenn Sie eine Google-Konto noch nicht haben, verwenden **Weitere Optionen** > **[Konto erstellen](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api)**  Link, um eines zu erstellen:

![Google-API-Konsole](index/_static/GoogleConsoleLogin.png)

* Sie werden zur umgeleitet **API-Manager-Bibliothek** Seite:

![Seite "API-Manager-Bibliothek"](index/_static/GoogleConsoleSwitchboard.png)

* Tippen Sie auf **erstellen** , und geben Sie Ihre **Projektname**:

![Dialogfeld "Neues Projekt"](index/_static/GoogleConsoleNewProj.png)

* Nach dem Akzeptieren des Dialogfeld ", werden Sie wieder auf der Seite" Bibliothek "können Sie Funktionen für Ihre neue app auswählen weitergeleitet. Suchen **Google + API** in der Liste aus und klicken Sie auf den zugehörigen Link der API-Funktion hinzu:

![Seite "API-Manager-Bibliothek"](index/_static/GoogleConsoleChooseApi.png)

* Die Seite für die neu hinzugefügte-API wird angezeigt. Tippen Sie auf **aktivieren** Google + Anmeldung Funktion in Ihrer app hinzufügen:

![API-Manager Google + API-Seite](index/_static/GoogleConsoleEnableApi.png)

* Nach dem Aktivieren der API, tippen Sie auf **Anmeldeinformationen erstellen** so konfigurieren Sie die geheimen Schlüssel:

![API-Manager Google + API-Seite](index/_static/GoogleConsoleGoCredentials.png)

* Wählen Sie Folgendes aus:
   * **Google + API**
   * **Webserver (z. B. node.js, Tomcat)**, und
   * **Benutzerdaten**:

![API-Manager anmeldeinformationsseite: Informieren Sie sich über welche Art von Anmeldeinformationen benötigen Bereich](index/_static/GoogleConsoleChooseCred.png)

* Tippen Sie auf **welche Anmeldeinformationen benötige ich?** die gelangen Sie zu der zweite Schritt des app-Konfiguration **Erstellen einer OAuth 2.0-Client-ID**:

![API-Manager anmeldeinformationsseite: Erstellen einer OAuth 2.0-Client-ID](index/_static/GoogleConsoleCreateClient.png)

* Da wir eine Google +-Projekt, mit nur einem Feature (Anmelden) erstellen, können wir geben Sie den gleichen **Namen** für die OAuth 2.0-Client-ID als diejenige, die wir für das Projekt verwendet.

* Geben Sie die Entwicklung URI mit */signin-google* angefügt, die in der **autorisierte umleitungs-URIs** Feld (z. B.: `https://localhost:44320/signin-google`). Die Google-Authentifizierung konfiguriert, die weiter unten in diesem Lernprogramm behandelt automatisch Anforderungen, die bei */signin-google* Route zum Implementieren des OAuth-Fluss.

* Drücken Sie TAB, um das Hinzufügen der **autorisierte umleitungs-URIs** Eintrag.

* Tippen Sie auf **Client-ID erstellen**, dem gelangen Sie mit dem dritten Schritt **richten Sie den Bildschirm der OAuth 2.0-Zustimmung**:

![API-Manager anmeldeinformationsseite: Richten Sie den Bildschirm der OAuth 2.0-Zustimmung](index/_static/GoogleConsoleAddCred.png)

* Geben Sie Ihre öffentlich **e-Mail-Adresse** und **Produktname** für Ihre app angezeigt wird, wenn Google + melden Sie sich der Benutzer aufgefordert wird. Weitere Optionen sind verfügbar unter **Weitere Optionen zur Anpassung der**.

* Tippen Sie auf **Fortfahren** bis zum letzten Schritt fortfahren **Anmeldeinformationen herunterladen**:

![API-Manager anmeldeinformationsseite: Herunterladen von Anmeldeinformationen](index/_static/GoogleConsoleFinish.png)

* Tippen Sie auf **herunterladen** eine JSON-Datei mit der Anwendung vertrauliche Informationen gespeichert und **Fertig** um die Erstellung der neuen app abzuschließen.

* Bei der Bereitstellung der Website müssen Sie rufen die **Konsole der Google-** und eine neue öffentliche Url zu registrieren.

## <a name="store-google-clientid-and-clientsecret"></a>Store Google ClientID und ClientSecret

Verknüpfen Sie die sensiblen Einstellungen wie Google `Client ID` und `Client Secret` zu Ihrer Anwendungskonfigurationsdatei mithilfe der [geheimen Manager](../../app-secrets.md). Für die Zwecke dieses Lernprogramms, benennen Sie die Token `Authentication:Google:ClientId` und `Authentication:Google:ClientSecret`.

Die Werte für diese Token finden Sie in der JSON-Datei heruntergeladen, die im vorherigen Schritt unter `web.client_id` und `web.client_secret`.

## <a name="configure-google-authentication"></a>Konfigurieren von Google-Authentifizierung

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Fügen Sie den Google-Dienst in der `ConfigureServices` Methode im *Startup.cs* Datei:

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddGoogle(googleOptions =>
{
    googleOptions.ClientId = Configuration["Authentication:Google:ClientId"];
    googleOptions.ClientSecret = Configuration["Authentication:Google:ClientSecret"];
});
```

[!INCLUDE[default settings configuration](includes/default-settings.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Die Projektvorlage verwendet, die in diesem Lernprogramm wird sichergestellt, dass [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) Paket installiert ist.

 * Zum Installieren dieses Pakets mit Visual Studio 2017 Maustaste auf das Projekt, und wählen **NuGet-Pakete verwalten**.
 * Führen Sie die folgenden im Projektverzeichnis, um die mit .NET Core CLI installieren:

   `dotnet add package Microsoft.AspNetCore.Authentication.Google`

Fügen Sie die Google-Middleware in der `Configure` Methode im *Startup.cs* Datei:

```csharp
app.UseGoogleAuthentication(new GoogleOptions()
{
    ClientId = Configuration["Authentication:Google:ClientId"],
    ClientSecret = Configuration["Authentication:Google:ClientSecret"]
});
```

---

Finden Sie unter der [GoogleOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.googleoptions) API-Referenz für Weitere Informationen zu den Konfigurationsoptionen, die von Google-Authentifizierung unterstützt. Dies kann verwendet werden, um unterschiedliche Informationen über den Benutzer anzufordern.

## <a name="sign-in-with-google"></a>Melden Sie sich mit Google

Führen Sie die Anwendung, und klicken Sie auf **melden Sie sich**. Eine Option zur Anmeldung mit Google angezeigt wird:

![In Microsoft Edge ausgeführte Webanwendung: nicht authentifizierte Benutzer](index/_static/DoneGoogle.png)

Wenn Sie die Google klicken, werden Sie Google für die Authentifizierung weitergeleitet:

![Dialogfeld für Google-Authentifizierung](index/_static/GoogleLogin.png)

Nach dem Eingeben Ihrer Google-Anmeldeinformationen, werden dann Sie zurück zur Website umgeleitet, wo Sie Ihre e-Mail-Adresse festlegen können.

Sie sind jetzt angemeldet mithilfe Ihrer Google-Anmeldeinformationen:

![In Microsoft Edge ausgeführte Webanwendung: Authentifizierte Benutzer](index/_static/Done.png)

## <a name="troubleshooting"></a>Problembehandlung

* Erhalten Sie eine `403 (Forbidden)` Fehlerseite aus Ihrer eigenen app, die beim Ausführen in den Entwicklungsmodus (oder Unterbrechung des Debuggers mit den gleichen Fehler) sicher, dass **Google + API** aktiviert wurde, der **-API-Manager-Bibliothek** anhand der aufgeführten Schritte [weiter oben auf dieser Seite](#create-the-app-in-google-api-console). Wenn die Anmeldung funktioniert nicht, und Sie werden keine Fehler erhalten, wechseln Sie zu den Entwicklungsmodus, um das Problem Debuggen zu vereinfachen.
* **ASP.NET Core 2.x nur:** Wenn Identität ist nicht konfiguriert, durch den Aufruf `services.AddIdentity` in `ConfigureServices`, Authentifizierungsversuch führt zu *ArgumentException: die Option "SignInScheme" muss angegeben werden*. Die Projektvorlage, die in diesem Lernprogramm verwendete wird sichergestellt, dass dies geschehen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem der anfänglichen Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Tippen Sie auf **gelten Migrationen** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus zu fortfahren.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie mit Google authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung bei anderen Anbietern aufgeführt, auf die [vorherige Seite](index.md).

* Nachdem Sie Ihre Website in Azure-Web-app veröffentlichen, sollten Sie Zurücksetzen der `ClientSecret` in der Google-API-Konsole.

* Legen Sie die `Authentication:Google:ClientId` und `Authentication:Google:ClientSecret` als Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem wird beim Lesen von Schlüsseln von Umgebungsvariablen eingerichtet.
