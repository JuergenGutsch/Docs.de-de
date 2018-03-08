---
title: "Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/05/2017
ms.topic: get-started-article
ms.assetid: 78571e4a-a143-452d-9cf2-0860f85972e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: df22852d2daddb2a3faef8404d0d250a6a1697a5
ms.sourcegitcommit: e987c950caae7af9c4ece8a82228caa364e0a5df
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2017
---
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a>Veröffentlichen einer ASP.NET Core-Web-App in Azure App Service mit Visual Studio

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs) und [Rachel Appel](https://twitter.com/rachelappel)

## <a name="set-up"></a>Einrichten

* Öffnen Sie ein [kostenloses Azure-Konto](https://aka.ms/K5y5yh), wenn Sie noch keines haben. 

## <a name="create-a-web-app"></a>Erstellen einer Web-App

Klicken Sie auf der Startseite von Visual Studio auf **Datei > Neu > Projekt...**.

![Datei (Menü)](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Vervollständigen Sie das Dialogfeld **Neues Projekt**:

* Wählen Sie im linken Bereich **.NET Core** aus.
* Wählen Sie im mittleren Bereich **ASP.NET Core-Webanwendung** aus.
* Klicken Sie auf **OK**.

![Dialogfeld "Neues Projekt"](publish-to-azure-webapp-using-vs/_static/new_prj.png)

Gehen Sie im Dialogfeld **ASP.NET Core-Webanwendung** folgendermaßen vor:

* Wählen Sie **Webanwendung** aus.
* Wählen Sie **Authentifizierung ändern** aus.

![Dialogfeld "Neues Projekt"](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Das Dialogfeld **Authentifizierung ändern** wird angezeigt. 

* Wählen Sie **Einzelne Benutzerkonten** aus.
* Klicken Sie auf **OK**, um zur Seite **Neue ASP.NET Core-Webanwendung** zurückzukehren, und klicken Sie anschließend noch einmal auf **OK**.

![Dialogfeld „New ASP.NET Core Web Authentication“ (Neue ASP.NET Core-Webauthentifizierung)](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio erstellt die Projektmappe.

## <a name="run-the-app"></a>Ausführen der App

* Drücken Sie STRG+F5, um das Projekt auszuführen.
* Testen Sie die Links **About** (Info) und **Contact** (Kontakt).

![Auf „localhost“ in Microsoft Edge geöffnete Webanwendung](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrieren eines Benutzers

* Wählen Sie **Registrieren**, und registrieren Sie einen neuen Benutzer. Sie können eine fiktive E-Mail-Adresse verwenden. Sobald Sie diese übermitteln, zeigt die Seite die folgende Fehlermeldung an:

    *„Interner Serverfehler: Fehler bei einem Datenbankvorgang beim Verarbeiten der Anforderung. SQL-Ausnahme: Die Datenbank kann nicht geöffnet werden. Das Anwenden vorhandener Migrationen für den Datenbankkontext der Anwendung kann dieses Problem beheben.“*
* Wählen Sie **Migrationen anwenden** aus, und aktualisieren Sie die Seite.

![Interner Serverfehler: Fehler bei einem Datenbankvorgang beim Verarbeiten der Anforderung. SQL-Ausnahme: Die Datenbank kann nicht geöffnet werden. Das Anwenden vorhandener Migrationen für den Datenbankkontext der Anwendung kann dieses Problem beheben.](publish-to-azure-webapp-using-vs/_static/mig.png)

Die App zeigt die zum Registrieren des neuen Benutzers verwendete E-Mail-Adresse und den Link **Abmelden**.

![In Microsoft Edge geöffnete Webanwendung Der Link „Register“ wird durch den Text „Hello email@domain.com!“ ersetzt.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Bereitstellen der App in Azure

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen...** aus.

![Geöffnetes Kontextmenü mit hervorgehobenem Link „Veröffentlichen“](publish-to-azure-webapp-using-vs/_static/pub.png)

Führen Sie im Dialogfeld **Veröffentlichen** folgende Schritte aus:

* Wählen Sie **Microsoft Azure App Service** aus.
* Klicken Sie auf das Zahnradsymbol, und wählen Sie anschließend **Profil erstellen** aus.
* Klicken Sie auf **Profil erstellen**.

![Dialogfeld „Veröffentlichen“](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Das Dialogfeld **App Service erstellen** wird angezeigt:

* Geben Sie Ihr Abonnement ein.
* Die Eingabefelder **App-Name**, **Ressourcengruppe** und **App Service-Plan** werden aufgefüllt. Sie können diese Namen beibehalten oder ändern.

![Dialogfeld „App Service“](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Wählen Sie die Registerkarte **Dienste** aus, um eine neue Datenbank zu erstellen.

* Klicken Sie auf das grüne Symbol **+**, um eine neue SQL-Datenbank zu erstellen.

![Neue SQL-Datenbank](publish-to-azure-webapp-using-vs/_static/sql.png)

* Wählen Sie im Dialogfeld **SQL-Datenbank konfigurieren** die Option **Neu...** aus, um eine neue Datenbank zu erstellen.

![Neue SQL-Datenbank und -Server](publish-to-azure-webapp-using-vs/_static/conf.png)

Das Dialogfeld **SQL Server konfigurieren** wird angezeigt.

* Geben Sie einen Administratorbenutzernamen und ein Kennwort ein, und klicken Sie dann auf **OK**. Sie können den standardmäßigen **Servernamen** beibehalten. 

> [!NOTE]
> „admin“ ist als Benutzername des Administrators nicht zulässig.

![Dialogfeld „SQL Server konfigurieren“](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Klicken Sie auf **OK**.

Visual Studio kehrt zum Dialogfeld **App Service erstellen** zurück.

* Wählen Sie im Dialogfeld **App Service erstellen** die Option **Erstellen** aus.

![Dialogfeld „SQL-Datenbank konfigurieren“](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio erstellt die Web-App und SQL Server in Azure. Dies kann einige Minuten in Anspruch nehmen. Weitere Informationen zu den erstellten Ressourcen finden Sie unter [Zusätzliche Ressourcen](#additonal-resources).

Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf **Einstellungen**:

![Dialogfeld „SQL Server konfigurieren“](publish-to-azure-webapp-using-vs/_static/set.png)

Gehen Sie im Bereich **Einstellungen** im Dialogfeld **Veröffentlichen** so vor:

  * Erweitern Sie **Datenbanken**, und aktivieren Sie **Diese Verbindungszeichenfolge zur Laufzeit verwenden**.
  * Erweitern Sie **Entity Framework-Migrationen**, und aktivieren Sie **Diese Migration auf Veröffentlichung anwenden**.

* Klicken Sie auf **Speichern**. Visual Studio kehrt zum Dialogfeld **Veröffentlichen** zurück. 

![Dialogfeld „Veröffentlichen“: Bereich „Einstellungen“](publish-to-azure-webapp-using-vs/_static/pubs.png)

Klicken Sie auf **Veröffentlichen**. Visual Studio veröffentlicht Ihre App in Azure. Wenn die Bereitstellung abgeschlossen ist, wird die App im Browser geöffnet.

### <a name="test-your-app-in-azure"></a>Testen der App in Azure

* Testen Sie die Links **About** und **Contact**.

* Registrieren eines neuen Benutzers

![In Azure App Service in Microsoft Edge geöffnete Webanwendung](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Aktualisieren der App

* Bearbeiten Sie die Razor-Page *Pages/About.cshtml*, und verändern Sie deren Inhalte. Sie können beispielsweise den Absatz so verändern, dass er „Hallo ASP.NET Core!“ anzeigt: [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie erneut **Veröffentlichen...** aus.

![Geöffnetes Kontextmenü mit hervorgehobenem Link „Veröffentlichen“](publish-to-azure-webapp-using-vs/_static/pub.png)

* Nachdem die Anwendung veröffentlicht wurde, vergewissern Sie sich, dass die vorgenommenen Änderungen in Azure verfügbar sind.

![Der Überprüfungstask ist abgeschlossen.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Bereinigen

Sobald Sie das Testen der App abgeschlossen haben, wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und löschen Sie die App.

* Wählen Sie **Ressourcengruppen** aus, und wählen Sie dann die Ressourcengruppe aus, die Sie erstellt haben.

![Azure-Portal: Ressourcengruppen im Menü auf der Randleiste](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Wählen Sie auf der Seite **Ressourcengruppen** **Löschen** aus.

![Azure-Portal: Seite „Ressourcengruppen“](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Geben Sie den Namen der Ressourcengruppe ein, und klicken Sie auf **Löschen**. Ihre App und alle anderen in diesem Tutorial erstellten Ressourcen werden nun aus Azure gelöscht.

### <a name="next-steps"></a>Nächste Schritte

* [Continuous Deployment in Azure mit Visual Studio und Git](../publishing/azure-continuous-deployment.md)
