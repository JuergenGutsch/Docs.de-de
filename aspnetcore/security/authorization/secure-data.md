---
title: "Erstellen einer ASP.NET Core-app mit Benutzerdaten durch Autorisierung geschützt"
author: rick-anderson
keywords: ASP.NET Core, MVC, Autorisierung, Rollen, Sicherheit, administrator
ms.author: riande
manager: wpickett
ms.date: 05/22/2017
ms.topic: article
ms.assetid: abeb2f8e-dfbf-4398-a04c-338a613a65bc
ms.technology: aspnet
ms.prod: aspnet-core
uid: security/authorization/secure-data
ms.openlocfilehash: 000b14ddc1adb56c029d3da8ab0754215403ba79
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>Erstellen einer ASP.NET Core-app mit Benutzerdaten durch Autorisierung geschützt

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)

In diesem Lernprogramm wird gezeigt, wie zum Erstellen einer ASP.NET Core-Web-app mit Benutzerdaten durch Autorisierung geschützt wird. Es zeigt eine Liste von Kontakten, die authentifizierte Benutzer (registrierten) erstellt haben. Es gibt drei Sicherheitsgruppen:

* **Registrierte Benutzer** alle genehmigten Daten anzeigen und bearbeiten/löschen können ihre eigenen Daten.
* **Manager** genehmigen oder ablehnen von Kontaktdaten können. Nur genehmigte Kontakte werden für Benutzer angezeigt.
* **Administratoren** können ablehnen/genehmigen und Bearbeiten/Löschen aller Daten.

In der folgenden Abbildung, Benutzer Rick (`rick@example.com`) angemeldet ist. Rick kann nur genehmigte Kontakte anzeigen und **bearbeiten**/**löschen**/**neu erstellen** Links, um seine Kontakte. Nur der letzte Datensatz erstellt, von Rick zeigt **bearbeiten** und **löschen** Links. Andere Benutzer sehen den letzten Datensatz, erst, wenn ein Manager oder der Administrator ändert sich der Status "Genehmigt".

![Bild beschrieben vor](secure-data/_static/rick.png)

In der folgenden Abbildung `manager@contoso.com` in und in der Rolle "Manager" signiert ist:

![Bild beschrieben vor](secure-data/_static/manager1.png)

Die folgende Abbildung zeigt den Managern Detailansicht eines Kontakts an:

![Bild beschrieben vor](secure-data/_static/manager.png)

Die **genehmigen** und **ablehnen** Schaltflächen sind nur für Manager und Administratoren angezeigt.

In der folgenden Abbildung `admin@contoso.com` in und in der "Administratoren" angemeldet ist:

![Bild beschrieben vor](secure-data/_static/admin.png)

Der Administrator verfügt über alle Berechtigungen. Sie können eine beliebige kontaktieren, Lese-/bearbeiten/löschen und ändern Sie den Status von Kontakten.

Die app wurde erstellt, indem [Gerüstbau](xref:tutorials/first-mvc-app-xplat/adding-model#scaffold-the-moviecontroller) Folgendes `Contact` Modell:

[!code-csharp[Main](secure-data/samples/starter2/Models/Contact.cs?name=snippet1)]

Das Beispiel enthält die folgenden Autorisierung Handler:

* `ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass Benutzer nur ihre Daten bearbeiten kann.
* `ContactManagerAuthorizationHandler`: Ermöglicht es Managern zum Genehmigen oder ablehnen von Kontakten.
* `ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren zum Genehmigen oder ablehnen von Kontakten und Bearbeiten/Löschen von Kontakten.

## <a name="prerequisites"></a>Erforderliche Komponenten

In diesem Lernprogramm wird erweitert. Sie sollten mit vertraut sein:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Authentifizierung](xref:security/authentication/index)
* [Kontobestätigung und Kennwortwiederherstellung](xref:security/authentication/accconfirm)
* [Autorisierung](xref:security/authorization/index)
* [Entity Framework Core](xref:data/ef-mvc/intro)

Finden Sie unter [diese PDF-Datei](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/asp.net_repo_pdf_1-16-18.pdf) für ASP.NET Core MVC-Version. Die Version 1.1 von ASP.NET Core dieses Lernprogramm ist [dies](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data) Ordner. Die 1.1 ASP.NET Core-Beispiel in ist der [Beispiele](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).

## <a name="the-starter-and-completed-app"></a>Die Starter und abgeschlossene Anwendung

[Herunterladen](xref:tutorials/index#how-to-download-a-sample) der [abgeschlossen](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2) app. [Test](#test-the-completed-app) abgeschlossene Anwendung, damit Sie mit der Sicherheitsfunktionen vertraut machen.

### <a name="the-starter-app"></a>Die Starter-app

[Herunterladen](xref:tutorials/index#how-to-download-a-sample) der [Starter](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2) app.

Führen Sie die app, tippen Sie auf die **ContactManager** verknüpfen, und überprüfen Sie Sie erstellen, bearbeiten und Löschen eines Kontakts.

## <a name="secure-user-data"></a>Sichern von Benutzerdaten

In den folgenden Abschnitten sind die wichtigsten Schritte zum Erstellen der sicheren Benutzer Daten app. Sie können es zum Verweisen auf das abgeschlossene Projekt hilfreich sein.

### <a name="tie-the-contact-data-to-the-user"></a>Binden Sie die Kontaktdaten für den Benutzer

Verwenden Sie die ASP.NET [Identität](xref:security/authentication/identity) Benutzer-ID, um sicherzustellen, dass Benutzer ihre Daten, aber nicht für andere Benutzerdaten bearbeiten kann. Hinzufügen `OwnerID` und `ContactStatus` auf die `Contact` Modell:

[!code-csharp[Main](secure-data/samples/final2/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID`ist die ID des Benutzers aus der `AspNetUser` -Tabelle in der [Identität](xref:security/authentication/identity) Datenbank. Die `Status` Feld bestimmt, ob ein Kontakt allgemeine Benutzer sichtbar ist.

Erstellen Sie eine neue Migration und Aktualisierung der Datenbank:

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="require-ssl-and-authenticated-users"></a>Erfordern von SSL und authentifizierte Benutzer

Hinzufügen [IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) auf `Startup`:

[!code-csharp[Main](secure-data/samples/final2/Startup.cs?name=snippet_env)]

In der `ConfigureServices` Methode der *Startup.cs* hinzufügen. die [RequireHttpsAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute) Autorisierungsfilter:

[!code-csharp[Main](secure-data/samples/final2/Startup.cs?name=snippet_SSL&highlight=19-999)]

Wenn Sie Visual Studio verwenden, aktivieren Sie SSL.

Zum Umleiten von HTTP-Anforderungen zu HTTPS finden Sie unter [URL umschreiben Middleware](xref:fundamentals/url-rewriting). Wenn Sie mithilfe von Visual Studio-Code oder auf einer lokalen-Plattform, die ein Testzertifikat für SSL keine testen:

  Legen Sie `"LocalTest:skipSSL": true` in der *"appSettings". Developement.JSON* Datei.

### <a name="require-authenticated-users"></a>Erfordern Sie authentifizierte Benutzern

Legen Sie die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen. Sie können die Authentifizierung auf Methodenebene Razor-Page, Controller oder Aktionen mit abwählen der `[AllowAnonymous]` Attribut. Festlegen der Standardrichtlinie für die Authentifizierung Benutzer authentifiziert werden müssen schützt neu hinzugefügten Razor-Pages und Controllern aus. Mit Authentifizierung erforderlich, in der Standardeinstellung sicherer ist als der vertrauenden Seite auf das neue Domänencontroller und Razor-Pages enthalten die `[Authorize]` Attribut. Fügen Sie Folgendes an der `ConfigureServices` Methode der *Startup.cs* Datei:

[!code-csharp[Main](secure-data/samples/final2/Startup.cs?name=snippet_defaultPolicy&highlight=31-999)]

Hinzufügen [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) auf den Index zu, und wenden Sie sich an Seiten, sodass anonyme Benutzer Informationen über die Website abrufen können, bevor sie registriert werden. 

[!code-csharp[Main](secure-data/samples/final2/Pages/Index.cshtml.cs?name=snippet&highlight=2)]

Hinzufügen `[AllowAnonymous]` auf die [LoginModel und RegisterModel](https://github.com/aspnet/templating/issues/238).

### <a name="configure-the-test-account"></a>Konfigurieren Sie das Testkonto an

Die `SeedData` Klasse erstellt zwei Konten: Administrator und den Manager. Verwenden der [Secret-Manager-Tool](xref:security/app-secrets) zum Festlegen eines Kennworts für diese Konten. Legen Sie das Kennwort aus dem Projektverzeichnis (das Verzeichnis mit *"Program.cs"*):

```console
dotnet user-secrets set SeedUserPW <PW>
```

Update `Main` Testkennwort verwenden:

[!code-csharp[Main](secure-data/samples/final2/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Erstellen Sie die Testkonten und aktualisieren die Kontakte

Update der `Initialize` Methode in der `SeedData` Klasse, um die Testkonten zu erstellen:

[!code-csharp[Main](secure-data/samples/final2/Data/SeedData.cs?name=snippet_Initialize)]

Fügen Sie die Administrator-Benutzer-ID und `ContactStatus` für Kontakte. Stellen Sie einen der Kontakte "Übermittelt" und eine "abgelehnt". Fügen Sie die Benutzer-ID und den Status für alle Kontakte hinzu. Nur ein Kontakt wird angezeigt:

[!code-csharp[Main](secure-data/samples/final2/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Erstellen Sie, Besitzer, Manager und Administrator Autorisierung Handler

Erstellen einer `ContactIsOwnerAuthorizationHandler` -Klasse in der *Autorisierung* Ordner. Die `ContactIsOwnerAuthorizationHandler` stellt sicher, dass der Benutzer, die auf einer Ressource fungiert die Ressource besitzt.

[!code-csharp[Main](secure-data/samples/final2/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

Die `ContactIsOwnerAuthorizationHandler` Aufrufe [Kontext. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) ist der aktuelle authentifizierte Benutzer der Besitzer des Kontakts. Autorisierung Handler in der Regel:

* Zurückgeben `context.Succeed` Wenn die Anforderungen erfüllt sind.
* Zurückgeben `Task.CompletedTask` bei ist nicht erfüllt. `Task.CompletedTask`ist weder Erfolg oder Misserfolg&mdash;können andere Autorisierung Handler ausgeführt.

Wenn Sie explizit fehlschlägt müssen, zurückgeben [Kontext. Fehlschlagen](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Die app ermöglicht Kontakt Besitzer zu bearbeiten, löschen, erstellen ihre eigenen Daten an. `ContactIsOwnerAuthorizationHandler`Überprüfen Sie den Vorgang im Parameters Anforderung übergeben muss nicht.

### <a name="create-a-manager-authorization-handler"></a>Erstellen Sie einen Ereignishandler der Manager-Autorisierung

Erstellen einer `ContactManagerAuthorizationHandler` -Klasse in der *Autorisierung* Ordner. Die `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, die auf die Ressource ist ein Manager. Nur Manager können genehmigen oder Ablehnen der Änderungen am Inhalt (neue oder geänderte).

[!code-csharp[Main](secure-data/samples/final2/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Erstellen Sie einen Administrator-Autorisierung-Ereignishandler

Erstellen einer `ContactAdministratorsAuthorizationHandler` -Klasse in der *Autorisierung* Ordner. Die `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, die auf die Ressource ist ein Administrator. Administratoren kann alle Vorgänge tun.

[!code-csharp[Main](secure-data/samples/final2/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Registrieren Sie die Authorization-Handler

Verwendung von Entity Framework Core Services müssen registriert werden, für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mit [AddScoped](/aspnet/core/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). Die `ContactIsOwnerAuthorizationHandler` verwendet ASP.NET Core [Identität](xref:security/authentication/identity), die basiert auf Entity Framework Core. Damit sie verfügbar sind, registrieren Sie die Handler mit die Auflistung der `ContactsController` über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection). Fügen Sie den folgenden Code am Ende der `ConfigureServices`:

[!code-csharp[Main](secure-data/samples/final2/Startup.cs?name=ConfigureServices&highlight=41-999)]

`ContactAdministratorsAuthorizationHandler`und `ContactManagerAuthorizationHandler` als Singletons hinzugefügt werden sollen. Sie Singletons sind, da sie nicht EF verwenden und alle erforderlichen Informationen in den `Context` Parameter von der `HandleRequirementAsync` Methode.

## <a name="support-authorization"></a>Support-Autorisierung

In diesem Abschnitt Aktualisieren der Razor-Pages und fügen Sie eine Operations Anforderungen-Klasse.

### <a name="review-the-contact-operations-requirements-class"></a>Überprüfen Sie die Kontaktinformationen Vorgänge Anforderungen-Klasse

Überprüfen Sie die `ContactOperations` Klasse. Diese Klasse enthält die Anforderungen der app unterstützt:

[!code-csharp[Main](secure-data/samples/final2/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-razor-pages"></a>Erstellen Sie eine Basisklasse für die Razor-Pages

Erstellen Sie eine Basisklasse, die in den Kontakten Razor-Pages verwendeten Dienste enthält. Die Basisklasse setzt diese Initialisierungscode an einem zentralen Ort:

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/DI_BasePageModel.cs)]

Der vorangehende Code:

* Fügt der `IAuthorizationService` Dienst den Zugriff auf die Autorisierung Handler.
* Fügt die Identität `UserManager` Dienst.
* Fügen Sie die `ApplicationDbContext` hinzu.

### <a name="update-the-createmodel"></a>Aktualisieren der CreateModel

Aktualisieren den erstellen Seite Modell Konstruktor verwendet den `DI_BasePageModel` Basisklasse:

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Update der `CreateModel.OnPostAsync` aufzurufende Methode:

* Fügen Sie die Benutzer-ID an den `Contact` Modell.
* Rufen Sie den Handler Autorisierung, um sicherzustellen, dass der Benutzer die Berechtigung zum Erstellen von Kontakten verfügt.

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Aktualisieren Sie die IndexModel

Update der `OnGetAsync` Methode, sodass nur genehmigte Kontakte für allgemeine Benutzer angezeigt werden:

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Aktualisieren Sie die EditModel

Fügen Sie einen Handler Autorisierung, um sicherzustellen, dass der Benutzer im Besitz des Kontakts ist. Da Ressource Autorisierung überprüft wird, die `[Authorize]` Attribut ist nicht ausreichend. Die app hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden. Autorisierung ressourcenbasierte muss zwingend erforderlich. Überprüfungen müssen ausgeführt werden, sobald die app Zugriff auf die Ressource durch Laden in das Modell oder durch Laden in den Handler selbst hat. Durch Übergeben der Ressourcenschlüssel greifen Sie häufig die Ressource.

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Aktualisieren Sie die DeleteModel

Aktualisieren Sie das Modell löschen Seite, um die Autorisierung Handler zu verwenden, um sicherzustellen, dass der Benutzer über die Löschberechtigung für den Kontakt verfügt.

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Fügen Sie dem Prüfdienst in den Ansichten

Derzeit wird die Benutzeroberfläche zeigt bearbeiten und Löschen von Verknüpfungen für Daten, die der Benutzer ändern kann. Die Benutzeroberfläche ist durch Anwenden des Autorisierung Handlers, die den Ansichten fest.

Fügen Sie dem Prüfdienst in der *Views/_ViewImports.cshtml* Datei, damit er für alle Ansichten verfügbar ist:

[!code-cshtml[Main](secure-data/samples/final2/Pages/_ViewImports.cshtml?highlight=6-9)]

Das vorhergehende Markup fügt mehrere `using` Anweisungen.

Update der **bearbeiten** und **löschen** verknüpft *Pages/Contacts/Index.cshtml* , sodass sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:

[!code-cshtml[Main](secure-data/samples/final2/Pages/Contacts/Index.cshtml?highlight=34-36,64-999)]

> [!WARNING]
> Ausblenden von Links von Benutzern, die keine Berechtigung zum Ändern von Daten haben Sichern nicht die app. Durch das Ausblenden Links wird die app durch Anzeigen der einzige gültige Links Benutzerfreundlicher. Benutzer können die generierten URLs zum Aufrufen bearbeiten und Löschen von Operationen mit Daten, die sie besitzen keine hack. Der Razor-Page oder ein Domänencontroller muss zugriffsüberprüfungen zum Schutz der Daten erzwingen.

### <a name="update-details"></a>Updatedetails

Aktualisieren Sie die Detailansicht, damit Manager genehmigen oder ablehnen von Kontakten können:

[!code-cshtml[Main](secure-data/samples/final2/Pages/Contacts/Details.cshtml?range=48-999)]

Aktualisieren Sie das Modell Details:

[!code-csharp[Main](secure-data/samples/final2/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="test-the-completed-app"></a>Abgeschlossene Anwendung testen

Wenn Sie mithilfe von Visual Studio-Code oder auf einer lokalen-Plattform, die ein Testzertifikat für SSL keine testen:

* Legen Sie `"LocalTest:skipSSL": true` in der *"appSettings". Developement.JSON* Datei, um die SSL-Anforderung zu überspringen. Überspringen Sie SSL nur auf einem Entwicklungscomputer.

Wenn die app Kontakte besitzt:

* Löschen aller Datensätze in der `Contact` Tabelle.
* Starten Sie die app Ausgangswert für die Datenbank neu.

Registrieren Sie einen Benutzer zum Durchsuchen der Kontakte.

Eine einfache Möglichkeit zum Testen Sie die abgeschlossenen app wird auf drei verschiedenen Browsern (oder inkognito/InPrivate-Versionen) zu starten. In einem Browser eingeben, einen neuen Benutzer registrieren (z. B. `test@example.com`). Melden Sie sich mit einem anderen Benutzer jedem Browser an. Überprüfen Sie die folgenden Vorgänge aus:

* Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.
* Registrierte Benutzer können bearbeiten/ihre eigenen Daten löschen.
* Manager können genehmigen oder ablehnen Kontaktdaten. Die `Details` Ansicht zeigt **genehmigen** und **ablehnen** Schaltflächen.
* Administratoren können ablehnen/genehmigen und Bearbeiten/Löschen aller Daten.

| Benutzer| Optionen |
| ------------ | ---------|
| test@example.com | Können bearbeiten/eigene Daten löschen |
| manager@contoso.com | Können ablehnen/genehmigen und bearbeiten/löschen, die Daten besitzen |
| admin@contoso.com | Können bearbeiten/löschen und alle Daten genehmigen/ablehnen|

Erstellen eines Kontakts in der Administrator-Browser. Kopieren Sie die URL zum Löschen und Bearbeiten von den Administrator wenden Sie sich an. Fügen Sie diese Links in den Browser des Testbenutzers zu überprüfen, ob der Testbenutzer diese Vorgänge ausführen kann nicht aus.

## <a name="create-the-starter-app"></a>Erstellen der Starter-app

* Erstellen Sie eine Razor-Pages-app mit dem Namen "ContactManager"

  * Erstellen Sie die app mit **einzelne Benutzerkonten**.
  * Nennen Sie sie "ContactManager", sodass Ihr Namespace den Namespace, die im Beispiel verwendeten entspricht.

  ```console
  dotnet new razor -o ContactManager -au Individual -uld
  ```

  * `-uld`Gibt an, anstelle von SQLite LocalDB

* Fügen Sie die folgenden `Contact` Modell:

  [!code-csharp[Main](secure-data/samples/starter2/Models/Contact.cs?name=snippet1)]

* Gerüst der `Contact` Modell:

```console
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
```

* Update der **ContactManager** Verankern der *Pages/_Layout.cshtml* Datei:

```cshtml
<a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
```

* Erstellen der anfänglichen Migrations und die Datenbank zu aktualisieren:

```console
dotnet ef migrations add initial
dotnet ef database update
```

* Testen der app durch erstellen, bearbeiten und Löschen eines Kontakts

### <a name="seed-the-database"></a>Ausführen eines Seedings für die Datenbank

Hinzufügen der `SeedData` Klasse, um die *Daten* Ordner. Wenn Sie das Beispiel heruntergeladen haben, können Sie kopieren die *SeedData.cs* Datei wird in der *Daten* Ordner des Startprojekts.

Rufen Sie `SeedData.Initialize` aus `Main`:

[!code-csharp[Main](secure-data/samples/starter2/Program.cs?name=snippet)]

Testen Sie, ob die Anwendung die Datenbank mit Anfangsdaten gefüllt. Wenn alle Zeilen in den Kontakt DB vorhanden sind, nicht der Seed-Methode ausgeführt werden.

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Zusätzliche Ressourcen

* [ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop). Diese Übung wird ausführlicher auf den Sicherheitsfeatures, die in diesem Lernprogramm eingeführt.
* [Autorisierung in ASP.NET Core: einfach, anspruchsbasierte und benutzerdefinierten Rolle](xref:security/authorization/index)
* [Benutzerdefinierte, richtlinienbasierte Autorisierung](xref:security/authorization/policies)
