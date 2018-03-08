---
title: Facebook externe Anmeldung Setup in ASP.NET Core
author: rick-anderson
description: Dieses Lernprogramm veranschaulicht die Integration von Facebook-Konto-Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
manager: wpickett
ms.author: riande
ms.date: 08/01/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authentication/facebook-logins
ms.openlocfilehash: 1eb563a5067fe4b063e5bd593d441e4e2a719b18
ms.sourcegitcommit: 93785b6b29a4996066fba05149348f1bdf881263
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2017
---
# <a name="configuring-facebook-authentication"></a>Konfigurieren von Facebook-Authentifizierung

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Dieses Lernprogramm veranschaulicht das Ihren Benutzern zur Anmeldung mit ihrem Facebook-Kontos mit einem Beispiel ASP.NET Core 2.0-Projekt erstellt haben, auf die [vorherige Seite](index.md). Erstellen eine Facebook-App-ID gemäß wir zunächst die [offizielle Schritte](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Erstellen Sie die app in Facebook

*  Navigieren Sie zu der [app von Facebook | Entwickler](https://developers.facebook.com/apps/) Seite, und melden Sie sich. Wenn Sie eine Facebook-Konto noch nicht haben, verwenden Sie die **registrieren Sie sich für Facebook** Link auf der Anmeldeseite um eines zu erstellen.

* Tippen Sie auf die **Hinzufügen einer neuen App** Schaltfläche in der oberen rechten Ecke zum Erstellen einer neuen App-ID

   ![Facebook für Entwicklerportal öffnen, in Microsoft Edge](index/_static/FBMyApps.png)

* Das Formular auszufüllen, und tippen Sie auf die **Erstellen von App-ID** Schaltfläche.

   ![Erstellen Sie ein Formular neue App-ID](index/_static/FBNewAppId.png)

* Auf der **Produkt auswählen** auf **Set Up** auf die **Facebook-Anmeldung** Karte.

   ![Setup-Produktseite](index/_static/FBProductSetup.png)
  
* Die **Schnellstart** Assistent startet mit **wählen Sie eine Plattform** als erste Seite. Der Assistent fürs zu umgehen, indem Sie auf die **Einstellungen** Link im Menü auf der linken Seite:

   ![Skip-Schnellstart](index/_static/FBSkipQuickStart.png)

* Clientanzahl der **OAuth-Clienteinstellungen** Seite:

![Seite "Client-OAuth-Einstellungen"](index/_static/FBOAuthSetup.png)

* Geben Sie die Entwicklung URI mit */signin-facebook* angefügt, die in der **gültige OAuth-Umleitungs-URIs** Feld (z. B.: `https://localhost:44320/signin-facebook`). Die Facebook-Authentifizierung konfiguriert, die weiter unten in diesem Lernprogramm behandelt automatisch Anforderungen, die bei */signin-facebook* Route zum Implementieren des OAuth-Fluss.

* Klicken Sie auf **Änderungen speichern**.

* Klicken Sie auf die **Dashboard** Link im linken Navigationsbereich. 

    Auf dieser Seite, notieren Sie sich Ihre `App ID` und Ihre `App Secret`. Sie werden sowohl in Ihre ASP.NET Core-Anwendung im nächsten Abschnitt hinzufügen:

   ![Facebook-Entwickler-Dashboard](index/_static/FBDashboard.png)

* Wenn Sie den Standort bereitstellen, müssen Sie wiederholen, die **Facebook-Anmeldung** Setupseite, und registrieren Sie einen neuen öffentliche URI.

## <a name="store-facebook-app-id-and-app-secret"></a>Facebook-App-ID und den geheimen speichern

Verknüpfen Sie die sensiblen Einstellungen wie Facebook `App ID` und `App Secret` zu Ihrer Anwendungskonfigurationsdatei mithilfe der [geheimen Manager](xref:security/app-secrets). Für die Zwecke dieses Lernprogramms, benennen Sie die Token `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret`.

Führen Sie die folgenden Befehle zum sicheren Speichern `App ID` und `App Secret` mit dem geheimen Schlüssel-Manager:

```console
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a>Konfigurieren von Facebook-Authentifizierung

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Hinzufügen den Facebook-Dienst in der `ConfigureServices` Methode in der *Startup.cs* Datei:

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE[default settings configuration](includes/default-settings.md)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Installieren der [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) Paket.

* Zum Installieren dieses Pakets mit Visual Studio 2017 Maustaste auf das Projekt, und wählen **NuGet-Pakete verwalten**.
* Führen Sie die folgenden im Projektverzeichnis, um die mit .NET Core CLI installieren:

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

Hinzufügen die Facebook-Middleware in der `Configure` Methode im *Startup.cs* Datei:

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

Finden Sie unter der [FacebookOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.facebookoptions) API-Referenz für Weitere Informationen zu den Konfigurationsoptionen von Facebook-Authentifizierung unterstützt. Konfigurationsoptionen verwendet werden können:

* Fordern Sie unterschiedliche Informationen über den Benutzer.
* Fügen Sie Abfragezeichenfolgenargumente, um das Anmeldeverfahren anzupassen.

## <a name="sign-in-with-facebook"></a>Mit Facebook anmelden

Führen Sie die Anwendung, und klicken Sie auf **melden Sie sich**. Sie sehen eine Option zur Anmeldung mit Facebook.

![-Webanwendung: nicht authentifizierte Benutzer](index/_static/DoneFacebook.png)

Wenn Sie klicken auf **Facebook**, Sie werden für die Authentifizierung mit Facebook weitergeleitet:

![Seite "Facebook-Authentifizierung"](index/_static/FBLogin.png)

Facebook-Authentifizierung anfordert öffentliche Profile und e-Mail-Adresse wird standardmäßig an:

![Seite "Facebook-Authentifizierung"](index/_static/FBLoginDone.png)

Sobald Sie Ihre Facebook-Anmeldeinformationen eingeben, werden Sie wieder auf Ihrer Website weitergeleitet, wo Sie Ihre e-Mail-Adresse festlegen können.

Sie sind jetzt angemeldet mit Ihren Facebook-Anmeldeinformationen:

![-Webanwendung: Authentifizierte Benutzer](index/_static/Done.png)

## <a name="troubleshooting"></a>Problembehandlung

* **ASP.NET Core 2.x nur:** Wenn Identität ist nicht konfiguriert, durch den Aufruf `services.AddIdentity` in `ConfigureServices`, Authentifizierungsversuch führt zu *ArgumentException: die Option "SignInScheme" muss angegeben werden*. Die Projektvorlage, die in diesem Lernprogramm verwendete wird sichergestellt, dass dies geschehen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem der anfänglichen Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Tippen Sie auf **gelten Migrationen** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus zu fortfahren.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie mit Facebook authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung bei anderen Anbietern aufgeführt, auf die [vorherige Seite](index.md).

* Nachdem Sie Ihre Website in Azure-Web-app veröffentlichen, sollten Sie Zurücksetzen der `AppSecret` im Facebook-Entwicklerportal.

* Legen Sie die `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret` als Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem wird beim Lesen von Schlüsseln von Umgebungsvariablen eingerichtet.
