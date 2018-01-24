---
title: "Aktivieren der Generierung von QR-Code für Authentifikator-apps in ASP.NET Core"
author: rick-anderson
description: "Aktivieren der Generierung von QR-Code für Authentifikator-apps in ASP.NET Core"
ms.author: riande
manager: wpickett
ms.date: 09/24/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: 87a6d3f17216625e0f7ce206dddd72cb2f371e9a
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/19/2018
---
# <a name="enabling-qr-code-generation-for-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="d5d6d-103">Aktivieren der Generierung von QR-Code für Authentifikator-apps in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5d6d-103">Enabling QR Code generation for authenticator apps in ASP.NET Core</span></span>

<span data-ttu-id="d5d6d-104">Hinweis: Dieses Thema bezieht sich auf ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="d5d6d-104">Note: This topic applies to ASP.NET Core 2.x</span></span>

<span data-ttu-id="d5d6d-105">Im Lieferumfang von ASP.NET Core ist Unterstützung für Anwendungen, für die einzelnen Authentifizierung Authentifikator.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="d5d6d-106">Zwei Faktor-Authentifizierung (2FA) Authentifikator-apps, mit der eine zeitbasierte zum einmaligen Kennwort Algorithmus (TOTP), sind die empfohlene Vorgehensweise für 2FA Branche.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="d5d6d-107">2FA TOTP mit SMS 2FA bevorzugt wird.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="d5d6d-108">Eine Authentifikator-app enthält einen 6 bis 8 Ziffern-Code, der die Benutzer nach der Bestätigung ihres Benutzernamens und Kennworts eingeben müssen.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="d5d6d-109">In der Regel wird eine Authentifikator-app auf einem Smartphone installiert.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="d5d6d-110">ASP.NET Core Web-app-Vorlagen unterstützen Authentifikatoren, aber Sie bieten keine Unterstützung für die Generierung von QRCode.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-110">The ASP.NET Core web app templates support authenticators, but do not provide support for QRCode generation.</span></span> <span data-ttu-id="d5d6d-111">QRCode Generatoren vereinfachen die Einrichtung des 2FA.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="d5d6d-112">Dieses Dokument führt Sie durch Hinzufügen von [QR-Code](https://wikipedia.org/wiki/QR_code) Generation auf der Konfigurationsseite 2FA.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="d5d6d-113">Hinzufügen von QR-Codes auf der Konfigurationsseite 2FA</span><span class="sxs-lookup"><span data-stu-id="d5d6d-113">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="d5d6d-114">Verwenden Sie diese Anweisungen *qrcode.js* über https://davidshimjs.github.io/qrcodejs/-Repository.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-114">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="d5d6d-115">Herunterladen der [qrcode.js Javascript-Bibliothek](https://davidshimjs.github.io/qrcodejs/) auf die `wwwroot\lib` Ordner des Projekts.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-115">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

* <span data-ttu-id="d5d6d-116">In *Pages\Account\Manage\EnableAuthenticator.cshtml* (Razor-Seiten) oder *Views\Manage\EnableAuthenticator.cshtml* (MVC), suchen Sie die `Scripts` Abschnitt am Ende der Datei:</span><span class="sxs-lookup"><span data-stu-id="d5d6d-116">In *Pages\Account\Manage\EnableAuthenticator.cshtml* (Razor Pages) or *Views\Manage\EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="d5d6d-117">Update der `Scripts` Abschnitt fügen einen Verweis auf die `qrcodejs` Bibliothek, die Sie hinzugefügt haben und ein Aufruf zum Generieren des QR-Codes.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-117">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="d5d6d-118">Es sollte wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="d5d6d-118">It should look as follows:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* <span data-ttu-id="d5d6d-119">Löschen Sie den Absatz Sie mit den vorliegenden Anleitungen verknüpft.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-119">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="d5d6d-120">Ausführen der app, und stellen Sie sicher, dass Sie den QR-Code Scannen und überprüfen den Code, den der Authentifikator beweist können.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-120">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="d5d6d-121">Ändern Sie den Namen der Website in den QR-Code</span><span class="sxs-lookup"><span data-stu-id="d5d6d-121">Change the site name in the QR Code</span></span>

<span data-ttu-id="d5d6d-122">Der Websitename in den QR-Code stammt aus den Namen des Projekts, die Sie auswählen, wenn das Projekt zunächst erstellen.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-122">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="d5d6d-123">Können Sie Sie ändern, indem Sie die Suche nach der `GenerateQrCodeUri(string email, string unformattedKey)` Methode in der *Pages\Account\Manage\EnableAuthenticator.cshtml.cs* (Razor-Seiten)-Datei oder das *Controllers\ManageController.cs* (MVC)-Datei.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-123">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages\Account\Manage\EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers\ManageController.cs* (MVC) file.</span></span> 

<span data-ttu-id="d5d6d-124">Der Standard-Code aus der Vorlage sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="d5d6d-124">The default code from the template looks as follows:</span></span>

```c#
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenicatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="d5d6d-125">Der zweite Parameter im Aufruf von `string.Format` der Standortname, der den Namen Ihrer Projektmappe entnommen wird.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-125">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="d5d6d-126">Kann auf einen beliebigen Wert geändert werden, aber es muss immer ein URL-codiert sein.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-126">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="d5d6d-127">Verwenden einer anderen QR-Code-Bibliothek</span><span class="sxs-lookup"><span data-stu-id="d5d6d-127">Using a different QR Code library</span></span>

<span data-ttu-id="d5d6d-128">Sie können den QR-Code-Bibliothek mit Ihrem bevorzugten Bibliothek ersetzen.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-128">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="d5d6d-129">Der HTML-Code enthält eine `qrCode` Element in die Sie einen QR-Code, indem Sie geeignete Mechanismus platzieren können Ihre Bibliothek enthält.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-129">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="d5d6d-130">Die korrekt formatierte URL für den QR-Code finden Sie in der:</span><span class="sxs-lookup"><span data-stu-id="d5d6d-130">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="d5d6d-131">`AuthenticatorUri`die Eigenschaft des Modells.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-131">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="d5d6d-132">`data-url`die Eigenschaft in der `qrCodeData` Element.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-132">`data-url` property in the `qrCodeData` element.</span></span> 

<span data-ttu-id="d5d6d-133">Verwendung `@Html.Raw` Zugriff auf die Modelleigenschaft in einer Ansicht (andernfalls die kaufmännische und-Zeichen im Url doppelt codiert und der Bezeichnungsparameter für den QR-Code werden ignoriert).</span><span class="sxs-lookup"><span data-stu-id="d5d6d-133">Use `@Html.Raw` to access the model property in a view (otherwise the ampersands in the url will be double encoded and the label parameter of the QR Code will be ignored).</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="d5d6d-134">TOTP Client- und Zeitversatz</span><span class="sxs-lookup"><span data-stu-id="d5d6d-134">TOTP client and server time skew</span></span>

<span data-ttu-id="d5d6d-135">TOTP Authentifizierung hängt sowohl die Server-als auch der Authentifikator Gerät müssen die genaue Uhrzeit aus.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-135">TOTP authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="d5d6d-136">Token werden nur für 30 Sekunden dauern.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-136">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="d5d6d-137">Wenn die TOTP 2FA Anmeldungen fehlschlägt, überprüfen Sie, ob die Serverzeit genau und vorzugsweise eine genaue NTP-Dienst synchronisiert.</span><span class="sxs-lookup"><span data-stu-id="d5d6d-137">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>