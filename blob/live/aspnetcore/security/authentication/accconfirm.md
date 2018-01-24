---
title: "Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core"
author: rick-anderson
description: "Veranschaulicht das Erstellen einer ASP.NET Core-Apps mit der e-Mail-Bestätigung und das Kennwort zurücksetzen."
ms.author: riande
manager: wpickett
ms.date: 12/1/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/accconfirm
ms.openlocfilehash: b004a8e7680b203416552e5a7a2809799e657759
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="7114f-103">Kontobestätigung und kennwortwiederherstellung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7114f-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="7114f-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="7114f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span> 

<span data-ttu-id="7114f-105">In diesem Lernprogramm wird gezeigt, wie zum Erstellen einer ASP.NET Core-Apps mit e-Mail-Bestätigung und das Kennwort zurücksetzen.</span><span class="sxs-lookup"><span data-stu-id="7114f-105">This tutorial shows you how to build an ASP.NET Core app with email confirmation and password reset.</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="7114f-106">Erstellen eines neuen ASP.NET Core-Projekts</span><span class="sxs-lookup"><span data-stu-id="7114f-106">Create a New ASP.NET Core Project</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7114f-107">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7114f-107">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="7114f-108">Dieser Schritt gilt für Visual Studio unter Windows.</span><span class="sxs-lookup"><span data-stu-id="7114f-108">This step applies to Visual Studio on Windows.</span></span> <span data-ttu-id="7114f-109">Finden Sie im nächsten Abschnitt CLI-Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="7114f-109">See the next section for CLI instructions.</span></span>

<span data-ttu-id="7114f-110">Das Lernprogramm ist Visual Studio 2017 Preview 2 oder höher erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7114f-110">The tutorial requires Visual Studio 2017 Preview 2 or later.</span></span>

* <span data-ttu-id="7114f-111">Erstellen Sie in Visual Studio eine neue Webanwendungsprojekt.</span><span class="sxs-lookup"><span data-stu-id="7114f-111">In Visual Studio, create a New Web Application Project.</span></span>
* <span data-ttu-id="7114f-112">Wählen Sie **ASP.NET Core 2.0**.</span><span class="sxs-lookup"><span data-stu-id="7114f-112">Select **ASP.NET Core 2.0**.</span></span> <span data-ttu-id="7114f-113">Die folgende Abbildung anzeigen **.NET Core** ausgewählt, aber Sie können auswählen, **.NET Framework**.</span><span class="sxs-lookup"><span data-stu-id="7114f-113">The following image show **.NET Core** selected, but you can select **.NET Framework**.</span></span>
* <span data-ttu-id="7114f-114">Wählen Sie **Authentifizierung ändern** und legen Sie auf **einzelne Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="7114f-114">Select **Change Authentication** and set to **Individual User Accounts**.</span></span>
* <span data-ttu-id="7114f-115">Behalten Sie den Standardwert **in app-Store Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="7114f-115">Keep the default **Store user accounts in-app**.</span></span>

![Dialogfeld "Neues Projekt" mit "Einzelne Benutzerkonten Radio" ausgewählt](accconfirm/_static/2.png)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7114f-117">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7114f-117">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="7114f-118">Das Lernprogramm ist Visual Studio 2017 oder höher erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7114f-118">The tutorial requires Visual Studio 2017 or later.</span></span>

* <span data-ttu-id="7114f-119">Erstellen Sie in Visual Studio eine neue Webanwendungsprojekt.</span><span class="sxs-lookup"><span data-stu-id="7114f-119">In Visual Studio, create a New Web Application Project.</span></span>
* <span data-ttu-id="7114f-120">Wählen Sie **Authentifizierung ändern** und legen Sie auf **einzelne Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="7114f-120">Select **Change Authentication** and set to **Individual User Accounts**.</span></span>

![Dialogfeld "Neues Projekt" mit "Einzelne Benutzerkonten Radio" ausgewählt](accconfirm/_static/indiv.png)

---

### <a name="net-core-cli-project-creation-for-macos-and-linux"></a><span data-ttu-id="7114f-122">.NET Core CLI projekterstellung für MacOS und Linux</span><span class="sxs-lookup"><span data-stu-id="7114f-122">.NET Core CLI project creation for macOS and Linux</span></span>

<span data-ttu-id="7114f-123">Wenn Sie die CLI oder SQLite verwenden, führen Sie in einem Befehlsfenster Folgendes:</span><span class="sxs-lookup"><span data-stu-id="7114f-123">If you're using the CLI or SQLite, run the following in a command window:</span></span>

```console
dotnet new mvc --auth Individual
```

* <span data-ttu-id="7114f-124">`--auth Individual`Gibt die Vorlage für die einzelnen Benutzerkonten an.</span><span class="sxs-lookup"><span data-stu-id="7114f-124">`--auth Individual` specifies the Individual User Accounts template.</span></span>
* <span data-ttu-id="7114f-125">Fügen Sie auf Windows, die `-uld` Option.</span><span class="sxs-lookup"><span data-stu-id="7114f-125">On Windows, add the `-uld` option.</span></span> <span data-ttu-id="7114f-126">Die `-uld` Option erstellt eine LocalDB-Verbindungszeichenfolge anstelle einer SQLite-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="7114f-126">The `-uld` option creates a LocalDB connection string rather than a SQLite DB.</span></span>
* <span data-ttu-id="7114f-127">Führen Sie `new mvc --help` um Hilfe zu diesem Befehl zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="7114f-127">Run `new mvc --help` to get help on this command.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="7114f-128">Testen Sie die Registrierung neuer Benutzer</span><span class="sxs-lookup"><span data-stu-id="7114f-128">Test new user registration</span></span>

<span data-ttu-id="7114f-129">Die app auszuführen, wählen Sie die **registrieren** verknüpfen, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="7114f-129">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="7114f-130">Befolgen Sie die Anweisungen zum Ausführen von Entity Framework Core-Migrationen aus.</span><span class="sxs-lookup"><span data-stu-id="7114f-130">Follow the instructions to run Entity Framework Core migrations.</span></span> <span data-ttu-id="7114f-131">An diesem Punkt wird die ausschließliche Überprüfung auf die e-Mail-Adresse mit der [[EmailAddress]](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) Attribut.</span><span class="sxs-lookup"><span data-stu-id="7114f-131">At this  point, the only validation on the email is with the [[EmailAddress]](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="7114f-132">Nachdem Sie die Registrierung senden, werden Sie in der app angemeldet.</span><span class="sxs-lookup"><span data-stu-id="7114f-132">After you submit the registration, you are logged into the app.</span></span> <span data-ttu-id="7114f-133">Später in diesem Lernprogramm werden wir dies ändern, damit neue Benutzer anmelden können, bis ihre e-Mails überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="7114f-133">Later in the tutorial, we'll change this so new users cannot log in until their email has been validated.</span></span>

## <a name="view-the-identity-database"></a><span data-ttu-id="7114f-134">Anzeigen der Identity-Datenbank</span><span class="sxs-lookup"><span data-stu-id="7114f-134">View the Identity database</span></span>

# <a name="sql-servertabsql-server"></a>[<span data-ttu-id="7114f-135">SQL Server</span><span class="sxs-lookup"><span data-stu-id="7114f-135">SQL Server</span></span>](#tab/sql-server)

* <span data-ttu-id="7114f-136">Aus der **Ansicht** klicken Sie im Menü **Objekt-Explorer von SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="7114f-136">From the **View** menu, select **SQL Server Object Explorer** (SSOX).</span></span> 
* <span data-ttu-id="7114f-137">Navigieren Sie zu **(Localdb) MSSQLLocalDB (SQLServer 13)**.</span><span class="sxs-lookup"><span data-stu-id="7114f-137">Navigate to **(localdb)MSSQLLocalDB(SQL Server 13)**.</span></span> <span data-ttu-id="7114f-138">Mit der rechten Maustaste auf **Dbo. AspNetUsers** > **zeigen Daten**:</span><span class="sxs-lookup"><span data-stu-id="7114f-138">Right-click on **dbo.AspNetUsers** > **View Data**:</span></span>

![Kontextmenü für AspNetUsers-Tabelle in SQL Server-Objekt-Explorer](accconfirm/_static/ssox.png)

<span data-ttu-id="7114f-140">Beachten Sie die `EmailConfirmed` Feld ist `False`.</span><span class="sxs-lookup"><span data-stu-id="7114f-140">Note the `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="7114f-141">Möglicherweise möchten diese e-Mail-Adresse verwenden erneut im nächsten Schritt, wenn die app eine e-Mail zur kaufbestätigung sendet.</span><span class="sxs-lookup"><span data-stu-id="7114f-141">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="7114f-142">Mit der rechten Maustaste auf die Zeile, und wählen **löschen**.</span><span class="sxs-lookup"><span data-stu-id="7114f-142">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="7114f-143">Löschen die e-Mail-Adresse alias wird nun erleichtert in den folgenden Schritten.</span><span class="sxs-lookup"><span data-stu-id="7114f-143">Deleting the email alias now will make it easier in the following steps.</span></span>

# <a name="sqlitetabsqlite"></a>[<span data-ttu-id="7114f-144">SQLite</span><span class="sxs-lookup"><span data-stu-id="7114f-144">SQLite</span></span>](#tab/sqlite)

<span data-ttu-id="7114f-145">Finden Sie unter [arbeiten mit SQLite in einem ASP.NET Core MVC-Projekt](xref:tutorials/first-mvc-app-xplat/working-with-sql) Anweisungen zum Anzeigen der SQLite-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="7114f-145">See [Working with SQLite in an ASP.NET Core MVC project](xref:tutorials/first-mvc-app-xplat/working-with-sql) for instructions on how to view the SQLite DB.</span></span> 

---

## <a name="require-ssl-and-setup-iis-express-for-ssl"></a><span data-ttu-id="7114f-146">Erfordern von SSL und Einrichten von IIS Express für SSL</span><span class="sxs-lookup"><span data-stu-id="7114f-146">Require SSL and setup IIS Express for SSL</span></span>

<span data-ttu-id="7114f-147">Finden Sie unter [Erzwingen von SSL](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="7114f-147">See [Enforcing SSL](xref:security/enforcing-ssl).</span></span>

<a name="prevent-login-at-registration"></a>
## <a name="require-email-confirmation"></a><span data-ttu-id="7114f-148">E-Mail-Bestätigung</span><span class="sxs-lookup"><span data-stu-id="7114f-148">Require email confirmation</span></span>

<span data-ttu-id="7114f-149">Es wird empfohlen, die e-Mail-Adresse eines eine neue benutzerregistrierung, um zu überprüfen, sind sie nicht eine andere Person Identität zu bestätigen (d. h., sie noch nicht registriert mit einer anderen Person e-Mail-Adresse).</span><span class="sxs-lookup"><span data-stu-id="7114f-149">It's a best practice to confirm the email of a new user registration to verify they are not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="7114f-150">Angenommen, ein Diskussionsforum mussten, und Sie möchten, um zu verhindern, dass "yli@example.com"aus der Registrierung als"nolivetto@contoso.com."</span><span class="sxs-lookup"><span data-stu-id="7114f-150">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com."</span></span> <span data-ttu-id="7114f-151">Ohne e-Mail-Bestätigung "nolivetto@contoso.com" könnte unerwünschte e-Mails aus Ihrer app abrufen.</span><span class="sxs-lookup"><span data-stu-id="7114f-151">Without email confirmation, "nolivetto@contoso.com" could get unwanted email from your app.</span></span> <span data-ttu-id="7114f-152">Angenommen, das der Benutzer versehentlich als registriert "ylo@example.com" hadn't Tippfehler nun der "Yli", wäre sie nicht kennwortwiederherstellung verwenden, da die app ihre korrekte e-Mail-Adresse hat.</span><span class="sxs-lookup"><span data-stu-id="7114f-152">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli," they wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="7114f-153">E-Mail-Bestätigung enthält nur begrenzt Schutz von Bots und stellt keinen Schutz vor bestimmt Spammern Domänenbenutzern viele funktionierenden e-Mail-Aliase, die sie verwenden können, um zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="7114f-153">Email confirmation provides only limited protection from bots and doesn't provide protection from determined spammers who have many working email aliases they can use to register.</span></span>

<span data-ttu-id="7114f-154">In der Regel möchten verhindern, dass neue Benutzer keine Daten zu Ihrer Website bereitstellen, bevor sie eine bestätigte e-Mail-Adresse aufweisen.</span><span class="sxs-lookup"><span data-stu-id="7114f-154">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span> 

<span data-ttu-id="7114f-155">Update `ConfigureServices` eine bestätigte e-Mail-Adresse erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="7114f-155">Update `ConfigureServices` to require a confirmed email:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7114f-156">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7114f-156">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](accconfirm/sample/WebPW/Startup.cs?name=snippet1&highlight=6-9)]


# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7114f-157">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7114f-157">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](accconfirm/sample/WebApp1/Startup.cs?name=snippet1&highlight=13-16)]

---

 
```csharp
config.SignIn.RequireConfirmedEmail = true;
```
<span data-ttu-id="7114f-158">Die vorangehende Zeile wird verhindert, dass registrierte Benutzern zur Verfügung protokolliert werden, bis ihre-e-Mail bestätigt ist.</span><span class="sxs-lookup"><span data-stu-id="7114f-158">The preceding line prevents registered users from being logged in until their email is confirmed.</span></span> <span data-ttu-id="7114f-159">Allerdings verhindert dieser Zeile nicht neue Benutzer im protokolliert werden, nachdem sie registriert werden.</span><span class="sxs-lookup"><span data-stu-id="7114f-159">However, that line does not prevent new users from being logged in after they register.</span></span> <span data-ttu-id="7114f-160">Der Standardcode meldet einen Benutzer auf, nachdem sie registriert werden.</span><span class="sxs-lookup"><span data-stu-id="7114f-160">The default code logs in a user after they register.</span></span> <span data-ttu-id="7114f-161">Nachdem sie sich abmelden, können sie nicht erneut anmelden, bis sie registriert werden.</span><span class="sxs-lookup"><span data-stu-id="7114f-161">Once they log out, they won't be able to log in again until they register.</span></span> <span data-ttu-id="7114f-162">Später in diesem Lernprogramm ändern wir der Code so neu registrierte Benutzer sind **nicht** angemeldet.</span><span class="sxs-lookup"><span data-stu-id="7114f-162">Later in the tutorial we'll change the code so newly registered user are **not** logged in.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="7114f-163">Konfigurieren von e-Mail-Anbieter</span><span class="sxs-lookup"><span data-stu-id="7114f-163">Configure email provider</span></span>

<span data-ttu-id="7114f-164">In diesem Lernprogramm wird SendGrid zum Senden von e-Mails.</span><span class="sxs-lookup"><span data-stu-id="7114f-164">In this tutorial, SendGrid is used to send email.</span></span> <span data-ttu-id="7114f-165">Sie benötigen ein SendGrid-Konto und ein Schlüssel zum Senden von e-Mail.</span><span class="sxs-lookup"><span data-stu-id="7114f-165">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="7114f-166">Sie können andere e-Mail-Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="7114f-166">You can use other email providers.</span></span> <span data-ttu-id="7114f-167">ASP.NET Core 2.x enthält `System.Net.Mail`, womit Sie das Senden von e-Mails aus Ihrer app.</span><span class="sxs-lookup"><span data-stu-id="7114f-167">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="7114f-168">Es wird empfohlen, dass Sie SendGrid oder eine andere e-Mail-Dienst verwenden, um e-Mail zu senden.</span><span class="sxs-lookup"><span data-stu-id="7114f-168">We recommend you use SendGrid or another email service to send email.</span></span>

<span data-ttu-id="7114f-169">Die [Optionen Muster](xref:fundamentals/configuration/options) wird verwendet, um die Benutzer Konto- und Schlüsselauthentifizierung Einstellungen zugreifen.</span><span class="sxs-lookup"><span data-stu-id="7114f-169">The [Options pattern](xref:fundamentals/configuration/options) is used to access the user account and key settings.</span></span> <span data-ttu-id="7114f-170">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7114f-170">For more information, see [configuration](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="7114f-171">Erstellen Sie eine Klasse, um den e-Mail-Schlüssel abzurufen.</span><span class="sxs-lookup"><span data-stu-id="7114f-171">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="7114f-172">Für dieses Beispiel die `AuthMessageSenderOptions` Klasse wird erstellt, der *Services/AuthMessageSenderOptions.cs* Datei.</span><span class="sxs-lookup"><span data-stu-id="7114f-172">For this sample, the `AuthMessageSenderOptions` class is created in the *Services/AuthMessageSenderOptions.cs* file.</span></span>

[!code-csharp[Main](accconfirm/sample/WebApp1/Services/AuthMessageSenderOptions.cs?name=snippet1)]

<span data-ttu-id="7114f-173">Legen Sie die `SendGridUser` und `SendGridKey` mit der [Secret-Manager-Tool](../app-secrets.md).</span><span class="sxs-lookup"><span data-stu-id="7114f-173">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](../app-secrets.md).</span></span> <span data-ttu-id="7114f-174">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7114f-174">For example:</span></span>

```none
C:\WebAppl\src\WebApp1>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="7114f-175">Unter Windows, geheimen-Manager speichert die Schlüssel/Wert-Paare in einem *secrets.json* Datei im Verzeichnis %APPDATA%/Microsoft/UserSecrets/ < WebAppName UserSecretsId >.</span><span class="sxs-lookup"><span data-stu-id="7114f-175">On Windows, Secret Manager stores your keys/value pairs in a *secrets.json* file in the %APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId> directory.</span></span>

<span data-ttu-id="7114f-176">Der Inhalt der *secrets.json* Datei sind nicht verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="7114f-176">The contents of the *secrets.json* file are not encrypted.</span></span> <span data-ttu-id="7114f-177">Die *secrets.json* Datei wird unten gezeigt (die `SendGridKey` Wert entfernt wurde.)</span><span class="sxs-lookup"><span data-stu-id="7114f-177">The *secrets.json* file is shown below (the `SendGridKey` value has been removed.)</span></span>

  ```json
  {
    "SendGridUser": "RickAndMSFT",
    "SendGridKey": "<key removed>"
  }
  ```

### <a name="configure-startup-to-use-authmessagesenderoptions"></a><span data-ttu-id="7114f-178">Konfigurieren Sie Start AuthMessageSenderOptions Verwendung</span><span class="sxs-lookup"><span data-stu-id="7114f-178">Configure startup to use AuthMessageSenderOptions</span></span>

<span data-ttu-id="7114f-179">Hinzufügen `AuthMessageSenderOptions` dem Dienstcontainer am Ende der `ConfigureServices` Methode in der *Startup.cs* Datei:</span><span class="sxs-lookup"><span data-stu-id="7114f-179">Add `AuthMessageSenderOptions` to the service container at the end of the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7114f-180">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7114f-180">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](accconfirm/sample/WebPW/Startup.cs?name=snippet1&highlight=18)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7114f-181">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7114f-181">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)
[!code-csharp[Main](accconfirm/sample/WebApp1/Startup.cs?name=snippet1&highlight=26)]

---

### <a name="configure-the-authmessagesender-class"></a><span data-ttu-id="7114f-182">Konfigurieren Sie die AuthMessageSender-Klasse</span><span class="sxs-lookup"><span data-stu-id="7114f-182">Configure the AuthMessageSender class</span></span>

<span data-ttu-id="7114f-183">Dieses Lernprogramm veranschaulicht das Hinzufügen von e-Mail-Benachrichtigungen über [SendGrid](https://sendgrid.com/), aber Sie können e-Mail-Nachrichten mithilfe von SMTP und andere Mechanismen senden.</span><span class="sxs-lookup"><span data-stu-id="7114f-183">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

* <span data-ttu-id="7114f-184">Installieren der `SendGrid` NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="7114f-184">Install the `SendGrid` NuGet package.</span></span> <span data-ttu-id="7114f-185">Aus der Paket-Manager-Konsole, geben Sie den folgenden den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="7114f-185">From the Package Manager Console,  enter the following the following command:</span></span>

  `Install-Package SendGrid`

* <span data-ttu-id="7114f-186">Finden Sie unter [erste Schritte mit SendGrid kostenlos](https://sendgrid.com/free/) für ein kostenloses SendGrid-Konto registrieren.</span><span class="sxs-lookup"><span data-stu-id="7114f-186">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

#### <a name="configure-sendgrid"></a><span data-ttu-id="7114f-187">Konfigurieren von SendGrid</span><span class="sxs-lookup"><span data-stu-id="7114f-187">Configure SendGrid</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7114f-188">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7114f-188">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

* <span data-ttu-id="7114f-189">Fügen Sie Code in *Services/EmailSender.cs* ähnlich der folgenden SendGrid konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="7114f-189">Add code in *Services/EmailSender.cs* similar to the following to configure SendGrid:</span></span>

[!code-csharp[Main](accconfirm/sample/WebPW/Services/EmailSender.cs)]


# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7114f-190">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7114f-190">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)
* <span data-ttu-id="7114f-191">Fügen Sie Code in *Services/MessageServices.cs* ähnlich der folgenden SendGrid konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="7114f-191">Add code in *Services/MessageServices.cs* similar to the following to configure SendGrid:</span></span>

[!code-csharp[Main](accconfirm/sample/WebApp1/Services/MessageServices.cs)]

---

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="7114f-192">Konto bestätigen und Kennwort Wiederherstellung aktivieren</span><span class="sxs-lookup"><span data-stu-id="7114f-192">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="7114f-193">Die Vorlage, den Code für die Wiederherstellung für Konto bestätigen und das Kennwort hat.</span><span class="sxs-lookup"><span data-stu-id="7114f-193">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="7114f-194">Suchen der `[HttpPost] Register` Methode in der *AccountController.cs* Datei.</span><span class="sxs-lookup"><span data-stu-id="7114f-194">Find the `[HttpPost] Register` method in the  *AccountController.cs* file.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7114f-195">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7114f-195">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="7114f-196">Verhindern Sie, dass neu registrierte Benutzern zur Verfügung wird automatisch durch die folgende Zeile auskommentiert angemeldet werden:</span><span class="sxs-lookup"><span data-stu-id="7114f-196">Prevent newly registered users from being automatically logged on by commenting out the following line:</span></span>

```csharp 
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="7114f-197">Die vollständige Methode ist mit der geänderten Zeile hervorgehoben dargestellt:</span><span class="sxs-lookup"><span data-stu-id="7114f-197">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[Main](accconfirm/sample/WebPW/Controllers/AccountController.cs?highlight=19&name=snippet_Register)]

<span data-ttu-id="7114f-198">Hinweis: Der vorherige Code schlägt fehl, wenn Sie implementieren `IEmailSender` und eine nur-Text-e-Mail zu senden.</span><span class="sxs-lookup"><span data-stu-id="7114f-198">Note: The previous code will fail if you implement `IEmailSender` and send a plain text email.</span></span> <span data-ttu-id="7114f-199">Finden Sie unter [dieses Problem](https://github.com/aspnet/Home/issues/2152) für Weitere Informationen und eine problemumgehung.</span><span class="sxs-lookup"><span data-stu-id="7114f-199">See [this issue](https://github.com/aspnet/Home/issues/2152) for more information and a workaround.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7114f-200">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7114f-200">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="7114f-201">Kommentieren Sie den Code zum Aktivieren der kontobestätigung.</span><span class="sxs-lookup"><span data-stu-id="7114f-201">Uncomment the code to enable account confirmation.</span></span>

[!code-csharp[Main](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=16-25&name=snippet_Register)]

<span data-ttu-id="7114f-202">Hinweis: Wir haben auch einen neu registrierter Benutzer verhindert, dass automatisch durch die folgende Zeile auskommentiert angemeldet sein:</span><span class="sxs-lookup"><span data-stu-id="7114f-202">Note: We're also preventing a newly-registered user from being automatically logged on by commenting out the following line:</span></span>

```csharp 
//await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="7114f-203">Kennwortwiederherstellung aktivieren, indem Sie den Code in Auskommentieren der `ForgotPassword` Aktion in der *Controllers/AccountController.cs* Datei.</span><span class="sxs-lookup"><span data-stu-id="7114f-203">Enable password recovery by uncommenting the code in the `ForgotPassword` action in the *Controllers/AccountController.cs* file.</span></span>

[!code-csharp[Main](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=17-23&name=snippet_ForgotPassword)]

<span data-ttu-id="7114f-204">Kommentieren Sie die Form-Elements im *Views/Account/ForgotPassword.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="7114f-204">Uncomment the form element in *Views/Account/ForgotPassword.cshtml*.</span></span> <span data-ttu-id="7114f-205">Möglicherweise möchten Sie entfernen die `<p> For more information on how to enable reset password ... </p>` Element, das einen Link zu dieser Artikel enthält.</span><span class="sxs-lookup"><span data-stu-id="7114f-205">You might want to remove the `<p> For more information on how to enable reset password ... </p>` element which contains a link to this article.</span></span>

[!code-html[Main](accconfirm/sample/WebApp1/Views/Account/ForgotPassword.cshtml?highlight=7-10,12,28)]

---

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="7114f-206">Registrieren, e-Mails zu bestätigen und Kennwort zurücksetzen</span><span class="sxs-lookup"><span data-stu-id="7114f-206">Register, confirm email, and reset password</span></span>

<span data-ttu-id="7114f-207">Führen Sie die Web-app, und Testen Sie die kontobestätigung und das Kennwort eine Wiederherstellung durchführen.</span><span class="sxs-lookup"><span data-stu-id="7114f-207">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="7114f-208">Die app auszuführen und einen neuen Benutzer registrieren</span><span class="sxs-lookup"><span data-stu-id="7114f-208">Run the app and register a new user</span></span>

 ![Webansicht Anwendung Konto registrieren](accconfirm/_static/loginaccconfirm1.png)

* <span data-ttu-id="7114f-210">Suchen Sie nach der Link für die Bestätigung Ihrer e-Mail.</span><span class="sxs-lookup"><span data-stu-id="7114f-210">Check your email for the account confirmation link.</span></span> <span data-ttu-id="7114f-211">Finden Sie unter [Debuggen e-Mail](#debug) , wenn Sie die e-Mail nicht erhalten.</span><span class="sxs-lookup"><span data-stu-id="7114f-211">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="7114f-212">Klicken Sie auf den Link, um Ihre e-Mail-Adresse zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="7114f-212">Click the link to confirm your email.</span></span>
* <span data-ttu-id="7114f-213">Melden Sie sich mit Ihren e-Mail- und das Kennwort an.</span><span class="sxs-lookup"><span data-stu-id="7114f-213">Log in with your email and password.</span></span>
* <span data-ttu-id="7114f-214">Melden Sie sich ab.</span><span class="sxs-lookup"><span data-stu-id="7114f-214">Log off.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="7114f-215">Anzeigen der Seite "verwalten"</span><span class="sxs-lookup"><span data-stu-id="7114f-215">View the manage page</span></span>

<span data-ttu-id="7114f-216">Wählen Sie Ihren Benutzernamen im Browser: ![Browserfenster mit Benutzernamen](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="7114f-216">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="7114f-217">Möglicherweise müssen Sie die Navigationsleiste, um Benutzername erweitern.</span><span class="sxs-lookup"><span data-stu-id="7114f-217">You might need to expand the navbar to see user name.</span></span>

![navbar](accconfirm/_static/x.png)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7114f-219">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7114f-219">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="7114f-220">Die Seite "verwalten" wird angezeigt, mit der **Profil** Registerkarte ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="7114f-220">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="7114f-221">Die **E-Mail** zeigt ein Kontrollkästchen, der angibt, der e-Mails bestätigt wurde.</span><span class="sxs-lookup"><span data-stu-id="7114f-221">The **Email** shows a check box indicating the email has been confirmed.</span></span> 

![Seite "verwalten"](accconfirm/_static/rick2.png)


# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7114f-223">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7114f-223">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="7114f-224">Dies wird auf dieser Seite später in diesem Lernprogramm geht.</span><span class="sxs-lookup"><span data-stu-id="7114f-224">We'll talk about this page later in the tutorial.</span></span>
<span data-ttu-id="7114f-225">![Seite "verwalten"](accconfirm/_static/rick2.png)</span><span class="sxs-lookup"><span data-stu-id="7114f-225">![manage page](accconfirm/_static/rick2.png)</span></span>

---

### <a name="test-password-reset"></a><span data-ttu-id="7114f-226">Test Zurücksetzen des Kennworts</span><span class="sxs-lookup"><span data-stu-id="7114f-226">Test password reset</span></span>

* <span data-ttu-id="7114f-227">Wenn Sie angemeldet sind, wählen Sie **Logout**.</span><span class="sxs-lookup"><span data-stu-id="7114f-227">If you're logged in, select **Logout**.</span></span>  
* <span data-ttu-id="7114f-228">Wählen Sie die **melden Sie sich** verknüpfen, und wählen Sie die **haben Sie Ihr Kennwort vergessen?** Link.</span><span class="sxs-lookup"><span data-stu-id="7114f-228">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="7114f-229">Geben Sie die e-Mail, die Sie verwendet, um das Konto zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="7114f-229">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="7114f-230">Eine e-Mail mit einem Link zum Zurücksetzen Ihres Kennworts werden gesendet.</span><span class="sxs-lookup"><span data-stu-id="7114f-230">An email with a link to reset your password will be sent.</span></span> <span data-ttu-id="7114f-231">Überprüfen Sie Ihre e-Mail-Adresse, und klicken Sie auf den Link, um Ihr Kennwort zurückzusetzen.</span><span class="sxs-lookup"><span data-stu-id="7114f-231">Check your email and click the link to reset your password.</span></span>  <span data-ttu-id="7114f-232">Nachdem Sie Ihr Kennwort erfolgreich zurückgesetzt wurde, können Sie mit Ihrem e-Mail- und das neue Kennwort anmelden.</span><span class="sxs-lookup"><span data-stu-id="7114f-232">After your password has been successfully reset, you can login with your email and new password.</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="7114f-233">Debuggen-e-Mail</span><span class="sxs-lookup"><span data-stu-id="7114f-233">Debug email</span></span>

<span data-ttu-id="7114f-234">Wenn Sie e-Mail-nicht funktioniert:</span><span class="sxs-lookup"><span data-stu-id="7114f-234">If you can't get email working:</span></span>

* <span data-ttu-id="7114f-235">Überprüfen Sie die [e-Mail-Aktivität](https://sendgrid.com/docs/User_Guide/email_activity.html) Seite.</span><span class="sxs-lookup"><span data-stu-id="7114f-235">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="7114f-236">Überprüfen Sie Ihre Spam-Ordner.</span><span class="sxs-lookup"><span data-stu-id="7114f-236">Check your spam folder.</span></span>
* <span data-ttu-id="7114f-237">Wiederholen Sie dann eine andere e-Mail-Alias für einen anderen e-Mail-Anbieter (Microsoft, Yahoo, Gmail, usw.).</span><span class="sxs-lookup"><span data-stu-id="7114f-237">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="7114f-238">Erstellen einer [Konsolen-app zum Senden von e-Mail](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html).</span><span class="sxs-lookup"><span data-stu-id="7114f-238">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html).</span></span>
* <span data-ttu-id="7114f-239">Wiederholen Sie den senden an andere e-Mail-Konten.</span><span class="sxs-lookup"><span data-stu-id="7114f-239">Try sending to different email accounts.</span></span>

<span data-ttu-id="7114f-240">**Hinweis:** eine bewährte Sicherheitsmethode Produktion geheime Schlüssel in Test- und nicht zu verwenden ist.</span><span class="sxs-lookup"><span data-stu-id="7114f-240">**Note:** A security best practice is to not use production secrets in test and development.</span></span> <span data-ttu-id="7114f-241">Wenn Sie die app in Azure veröffentlichen, können Sie die SendGrid-geheimen als Anwendungseinstellungen im Azure-Web-App-Portal festlegen.</span><span class="sxs-lookup"><span data-stu-id="7114f-241">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="7114f-242">Das Konfigurationssystem wird Setup beim Lesen von Schlüsseln von Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="7114f-242">The configuration system is setup to read keys from environment variables.</span></span>

## <a name="prevent-login-at-registration"></a><span data-ttu-id="7114f-243">Zu verhindern, dass die Anmeldung bei der Registrierung</span><span class="sxs-lookup"><span data-stu-id="7114f-243">Prevent login at registration</span></span>

<span data-ttu-id="7114f-244">Mit den aktuellen Vorlagen, sobald ein Benutzer das Registrierungsformular abgeschlossen ist. sie angemeldet sind (authentifiziert).</span><span class="sxs-lookup"><span data-stu-id="7114f-244">With the current templates, once a user completes the registration form, they are logged in (authenticated).</span></span> <span data-ttu-id="7114f-245">In der Regel möchten Sie ihre e-Mails zu überprüfen, bevor im Clientbrowser.</span><span class="sxs-lookup"><span data-stu-id="7114f-245">You generally want to confirm their email before logging them in.</span></span> <span data-ttu-id="7114f-246">Im folgenden Abschnitt werden wir ändern Sie den Code erfordern neue Benutzer haben eine bestätigte e-Mail-Adresse aus, bevor sie angemeldet sind.</span><span class="sxs-lookup"><span data-stu-id="7114f-246">In the section below, we will modify the code to require new users have a confirmed email before they are logged in.</span></span> <span data-ttu-id="7114f-247">Update der `[HttpPost] Login` Aktion in der *AccountController.cs* Datei mit den folgenden hervorgehobenen Änderungen.</span><span class="sxs-lookup"><span data-stu-id="7114f-247">Update the `[HttpPost] Login` action in the *AccountController.cs* file with the following highlighted changes.</span></span>

[!code-csharp[Main](accconfirm/sample/WebApp1/Controllers/AccountController.cs?highlight=11-21&name=snippet_Login)]

<span data-ttu-id="7114f-248">**Hinweis:** eine bewährte Sicherheitsmethode Produktion geheime Schlüssel in Test- und nicht zu verwenden ist.</span><span class="sxs-lookup"><span data-stu-id="7114f-248">**Note:** A security best practice is to not use production secrets in test and development.</span></span> <span data-ttu-id="7114f-249">Wenn Sie die app in Azure veröffentlichen, können Sie die SendGrid-geheimen als Anwendungseinstellungen im Azure-Web-App-Portal festlegen.</span><span class="sxs-lookup"><span data-stu-id="7114f-249">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="7114f-250">Das Konfigurationssystem wird Setup beim Lesen von Schlüsseln von Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="7114f-250">The configuration system is setup to read keys from environment variables.</span></span>


## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="7114f-251">Kombinieren von sozialen und lokalen Anmeldekonten</span><span class="sxs-lookup"><span data-stu-id="7114f-251">Combine social and local login accounts</span></span>

<span data-ttu-id="7114f-252">Hinweis: Dieser Abschnitt gilt nur für ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="7114f-252">Note: This section applies only to ASP.NET Core 1.x.</span></span> <span data-ttu-id="7114f-253">Für ASP.NET Core 2.x, finden Sie unter [dies](https://github.com/aspnet/Docs/issues/3753) Problem.</span><span class="sxs-lookup"><span data-stu-id="7114f-253">For ASP.NET Core 2.x, see [this](https://github.com/aspnet/Docs/issues/3753) issue.</span></span>

<span data-ttu-id="7114f-254">Um in diesem Abschnitt abzuschließen, müssen Sie zunächst ein externen Authentifizierungsanbieters aktivieren.</span><span class="sxs-lookup"><span data-stu-id="7114f-254">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="7114f-255">Finden Sie unter [Aktivieren der Authentifizierung mithilfe von Facebook, Google und anderen externen Anbietern](social/index.md).</span><span class="sxs-lookup"><span data-stu-id="7114f-255">See [Enabling authentication using Facebook, Google and other external providers](social/index.md).</span></span>

<span data-ttu-id="7114f-256">Lokale und soziale Konten können durch Klicken auf die e-Mail-Link kombiniert werden.</span><span class="sxs-lookup"><span data-stu-id="7114f-256">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="7114f-257">In der folgenden Reihenfolge "RickAndMSFT@gmail.com" wird als eine lokale Anmeldung; zuerst erstellt jedoch zunächst erstellen Sie das Konto als sozialen Anmeldung werden können, und fügen Sie eine lokale Anmeldung hinzu.</span><span class="sxs-lookup"><span data-stu-id="7114f-257">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![-Webanwendung: RickAndMSFT@gmail.com authentifizierte Benutzer](accconfirm/_static/rick.png)

<span data-ttu-id="7114f-259">Klicken Sie auf die **verwalten** Link.</span><span class="sxs-lookup"><span data-stu-id="7114f-259">Click on the **Manage** link.</span></span> <span data-ttu-id="7114f-260">Beachten Sie die externen 0 (social Anmeldungen) diesem Konto zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="7114f-260">Note the 0 external (social logins) associated with this account.</span></span>

![Verwalten von anzeigen](accconfirm/_static/manage.png)

<span data-ttu-id="7114f-262">Klicken Sie auf den Link, um einen anderen Anmeldenamen-Dienst, und akzeptieren Sie die app-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="7114f-262">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="7114f-263">In der folgenden Abbildung wird die Facebook dem externen Authentifizierungsanbieter:</span><span class="sxs-lookup"><span data-stu-id="7114f-263">In the image below, Facebook is the external authentication provider:</span></span>

![Verwalten Sie externer Anmeldungen Ansicht Facebook auflisten](accconfirm/_static/fb.png)

<span data-ttu-id="7114f-265">Die beiden Konten wurden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="7114f-265">The two accounts have been combined.</span></span> <span data-ttu-id="7114f-266">Sie werden können mit entweder Konto anmelden.</span><span class="sxs-lookup"><span data-stu-id="7114f-266">You will be able to log on with either account.</span></span> <span data-ttu-id="7114f-267">Sie sollten Ihren Benutzern lokale Konten hinzufügen, falls der Anmeldung sozialen Authentication-Dienst ausgefallen ist oder wahrscheinlicher haben sie Zugriff auf ihr Konto sozialen verloren.</span><span class="sxs-lookup"><span data-stu-id="7114f-267">You might want your users to add local accounts in case their social log in authentication service is down, or more likely they have lost access to their social account.</span></span>