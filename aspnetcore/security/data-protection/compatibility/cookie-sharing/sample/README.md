# <a name="cookie-sharing-sample-app"></a>Cookie-Freigabe-Beispiel-App

Im Beispiel werden alle drei apps, die Cookieauthentifizierung gemeinsam Cookie veranschaulicht:

| Projekt                             | Beschreibung |
| ----------------------------------- | ----------- |
| CookieAuth.Core                     | ASP.NET 2.0-Razor-Pages Core app ohne Verwendung von ASP.NET Core Identity |
| CookieAuthWithIdentity.Core         | ASP.NET Core 2.0 MVC-Anwendung mit ASP.NET Core Identität |
| CookieAuthWithIdentity.NETFramework | ASP.NET Framework 4.6.1 MVC-Anwendung mit ASP.NET Identity |

Anweisungen:

1. Führen Sie die CookieAuth.Core-app. Registrieren eines Benutzers an. Die app authentifiziert den Benutzer auf, wenn der Benutzer registriert ist. Melden Sie sich des Benutzers ab.
1. Führen Sie in der gleichen Browsersitzung die CookieAuthWithIdentity.Core-app ein. Registrieren Sie denselben Benutzer wie die Core-App verwendet. Die app authentifiziert den Benutzer auf, wenn der Benutzer registriert ist. Melden Sie sich des Benutzers ab.
1. Führen Sie in der gleichen Browsersitzung die CookieAuthWithIdentity.NETFramework-app ein. Registrieren Sie den gleichen Benutzer als mit anderen apps verwendet. Die app authentifiziert den Benutzer auf, wenn der Benutzer registriert ist. Melden Sie sich des Benutzers ab.
1. Melden Sie sich der Benutzer auf den drei Apps. Das Authentifizierungscookie ist von den apps gemeinsam genutzt. Beachten Sie, dass der Benutzer automatisch in die anderen zwei apps angemeldet ist.
1. Melden Sie den Benutzer aus den Apps ein. Beachten Sie, dass der Benutzer von anderen apps automatisch abgemeldet wird.

In diesem Beispiel wird veranschaulicht, die in beschriebenen Funktionen der [Cookies zwischen apps freigeben](https://docs.microsoft.com/aspnet/core/security/data-protection/compatibility/cookie-sharing) Thema.
