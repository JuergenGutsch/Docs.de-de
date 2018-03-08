---
title: "Setup für Microsoft-Account-externe Anmeldung"
author: rick-anderson
description: Dieses Lernprogramm veranschaulicht die Integration von Microsoft-Konto-Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
manager: wpickett
ms.author: riande
ms.date: 08/24/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 4e1512aa151a87f677b400f16dbf53ca22b61e9e
ms.sourcegitcommit: 93785b6b29a4996066fba05149348f1bdf881263
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2017
---
# <a name="configuring-microsoft-account-authentication"></a>Konfigurieren von Microsoft-Account-Authentifizierung

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Dieses Lernprogramm veranschaulicht das Aktivieren von Benutzern die Anmeldung mit seinem Microsoft-Konto mit einem Beispiel ASP.NET Core 2.0-Projekt erstellt haben, auf die [vorherige Seite](index.md).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Erstellen Sie die app im Microsoft Developer Portal

* Navigieren Sie zu [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) und erstellen oder ein Microsoft-Konto anmelden:

![Melden Sie sich im Dialogfeld "](index/_static/MicrosoftDevLogin.png)

Wenn Sie ein Microsoft-Konto noch nicht haben, tippen Sie auf  **[erstellen!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)** Nach der Anmeldung werden Sie umgeleitet **Meine Anwendungen** Seite:

![Microsoft Developer Portal in Microsoft Edge öffnen](index/_static/MicrosoftDev.png)

* Tippen Sie auf **app hinzufügen** in der oberen rechten Ecke, und geben Sie Ihre **Anwendungsname** und **Contact Email**:

![Dialogfeld "Neues Anwendungsregistrierung"](index/_static/MicrosoftDevAppCreate.png)

* Deaktivieren Sie für die Zwecke dieses Lernprogramms können die **geführtes Setup** Kontrollkästchen.

* Tippen Sie auf **erstellen** zur Weiterleitung an die **Registrierung** Seite. Geben Sie eine **Name** und notieren Sie den Wert der der **Anwendungs-Id**, die Verwendung als `ClientId` später im Lernprogramm:

![Seite "Registrierung"](index/_static/MicrosoftDevAppReg.png)

* Tippen Sie auf **Plattform hinzufügen** in der **Plattformen** Abschnitt, und wählen Sie die **Web** Plattform:

![Fügen Sie die Zielplattform (Dialogfeld)](index/_static/MicrosoftDevAppPlatform.png)

* In der neuen **Web** Plattform Abschnitt, geben Sie Ihre Entwicklung URL mit */signin-microsoft* angefügt, die in der **Umleitungs-URLs** Feld (z. B.: `https://localhost:44320/signin-microsoft`). Das Microsoft-Authentifizierungsschema konfiguriert, die weiter unten in diesem Lernprogramm behandelt automatisch Anforderungen, die bei */signin-microsoft* Route zum Implementieren des OAuth-Fluss:

![Webabschnitt-Plattform](index/_static/MicrosoftRedirectUri.png)

* Tippen Sie auf **URL hinzufügen** um sicherzustellen, dass die URL wurde hinzugefügt.

* Füllen Sie ggf. Weitere Anwendungseinstellungen vor bei Bedarf, und tippen Sie auf **speichern** am unteren Rand der Seite, um Änderungen an der app-Konfiguration zu speichern.

* Bei der Bereitstellung der Website müssen Sie rufen die **Registrierung** Seite, und legen Sie eine neue Öffentliche URL.

## <a name="store-microsoft-application-id-and-password"></a>Microsoft Anwendungs-Id und Kennwort speichern

* Beachten Sie die `Application Id` angezeigt, auf die **Registrierung** Seite.

* Tippen Sie auf **neues Kennwort generieren** in der **Anwendung geheime Schlüssel** Abschnitt. Dies zeigt ein Feld, in dem Sie das anwendungskennwort kopieren können:

![Dialogfeld "Neues Kennwort generiert"](index/_static/MicrosoftDevPassword.png)

Verknüpfen Sie die sensiblen Einstellungen wie Microsoft `Application ID` und `Password` zu Ihrer Anwendungskonfigurationsdatei mithilfe der [geheimen Manager](../../app-secrets.md). Für die Zwecke dieses Lernprogramms, benennen Sie die Token `Authentication:Microsoft:ApplicationId` und `Authentication:Microsoft:Password`.

## <a name="configure-microsoft-account-authentication"></a>Konfigurieren Sie die Microsoft-Konto-Authentifizierung

Die Projektvorlage verwendet, die in diesem Lernprogramm wird sichergestellt, dass [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) Paket ist bereits installiert.

* Zum Installieren dieses Pakets mit Visual Studio 2017 Maustaste auf das Projekt, und wählen **NuGet-Pakete verwalten**.
* Führen Sie die folgenden im Projektverzeichnis, um die mit .NET Core CLI installieren:

   `dotnet add package Microsoft.AspNetCore.Authentication.MicrosoftAccount`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Fügen Sie den Microsoft-Account-Dienst in der `ConfigureServices` Methode im *Startup.cs* Datei:

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddMicrosoftAccount(microsoftOptions =>
{
    microsoftOptions.ClientId = Configuration["Authentication:Microsoft:ApplicationId"];
    microsoftOptions.ClientSecret = Configuration["Authentication:Microsoft:Password"];
});
```

[!INCLUDE[default settings configuration](includes/default-settings.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Fügen Sie die Microsoft-Account-Middleware in der `Configure` Methode im *Startup.cs* Datei:

```csharp
app.UseMicrosoftAccountAuthentication(new MicrosoftAccountOptions()
{
    ClientId = Configuration["Authentication:Microsoft:ApplicationId"],
    ClientSecret = Configuration["Authentication:Microsoft:Password"]
});
```

---

Obwohl die Terminologie, die auf Microsoft-Entwicklerportal diese Token benennt `ApplicationId` und `Password`, sie sind als verfügbar gemacht `ClientId` und `ClientSecret` an der API-Konfiguration.

Finden Sie unter der [MicrosoftAccountOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API-Referenz für Weitere Informationen zu den Konfigurationsoptionen, die von Microsoft-Account-Authentifizierung unterstützt. Dies kann verwendet werden, um unterschiedliche Informationen über den Benutzer anzufordern.

## <a name="sign-in-with-microsoft-account"></a>Melden Sie sich mit Microsoft-Konto

Führen Sie die Anwendung, und klicken Sie auf **melden Sie sich**. Eine Option aus, um sich mit Microsoft anmelden wird angezeigt:

![-Webanwendung Protokoll auf der Seite: nicht authentifizierte Benutzer](index/_static/DoneMicrosoft.png)

Wenn Sie auf Microsoft klicken, werden Sie für die Authentifizierung an Microsoft weitergeleitet. Nachdem Sie sich mit Ihrem Microsoft-Account (sofern nicht bereits angemeldet) werden Sie aufgefordert, um die app Zugriff auf Ihre Infos zu ermöglichen:

![Dialogfeld für Microsoft-Authentifizierung](index/_static/MicrosoftLogin.png)

Tippen Sie auf **Ja** und Sie zurück zur Website, wo Sie Ihre e-Mail-Adresse festlegen können, umgeleitet werden.

Sie sind jetzt mit Ihrem Microsoft-Anmeldeinformationen angemeldet:

![-Webanwendung: Authentifizierte Benutzer](index/_static/Done.png)

## <a name="troubleshooting"></a>Problembehandlung

* Wenn der Microsoft-Account-Anbieter auf eine Fehler-Anmeldeseite umgeleitet, beachten Sie die Fehler Titel und Beschreibung Abfragezeichenfolgen-Parameter direkt nach der `#` (Hashtag) im Uri.

  Obwohl die Fehlermeldung an, dass ein Problem mit dem Microsoft-Authentifizierung scheint, ist die häufigste Ursache Ihrer Anwendung mit keiner der Uri der **Weiterleitungs-URIs** angegeben für die **Web** Plattform .
* **ASP.NET Core 2.x nur:** Wenn Identität ist nicht konfiguriert, durch den Aufruf `services.AddIdentity` in `ConfigureServices`, Authentifizierungsversuch führt zu *ArgumentException: die Option "SignInScheme" muss angegeben werden*. Die Projektvorlage, die in diesem Lernprogramm verwendete wird sichergestellt, dass dies geschehen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem der anfänglichen Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Tippen Sie auf **gelten Migrationen** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus zu fortfahren.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie mit Microsoft authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung bei anderen Anbietern aufgeführt, auf die [vorherige Seite](index.md).

* Nachdem Sie Ihre Website in Azure-Web-app veröffentlichen, sollten Sie ein neues erstellen `Password` im Microsoft-Entwicklerportal.

* Legen Sie die `Authentication:Microsoft:ApplicationId` und `Authentication:Microsoft:Password` als Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem wird beim Lesen von Schlüsseln von Umgebungsvariablen eingerichtet.
