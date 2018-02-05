# <a name="aspnet-core-authorization-sample"></a>Beispiel für ASP.NET Core-Autorisierung

Dieses Beispiel veranschaulicht die Verwendung von Razor-Pages Autorisierung von Konventionen. In diesem Beispiel wird veranschaulicht, die in beschriebenen Funktionen der [Razor-Pages Autorisierung Konventionen](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization) Thema.

Benutzerautorisierung in diesem Beispiel verwendet die Cookieauthentifizierung Funktionen, in beschrieben der [Cookieauthentifizierung verwenden, ohne ASP.NET Core Identity](https://docs.microsoft.com/aspnet/core/security/authentication/cookie) Thema. Informationen zur Verwendung von ASP.NET Core Identity finden Sie unter den Themen in der [Authentifizierung](https://docs.microsoft.com/aspnet/core/security/authentication/index) Abschnitt der Dokumentation.

Beim Ausführen des Beispiels verwenden Sie die e-Mail-Adresse  **maria.rodriguez@contoso.com**  zur Authentifizierung des Benutzers.

## <a name="examples-in-this-sample"></a>Die Beispiele in diesem Beispiel

| Funktion | Beschreibung |
| ------- | ----------- |
| [AuthorizePage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) | Fügt eine [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) auf der Seite mit dem angegebenen Pfad. |
| [AuthorizeFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) | Fügt eine [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) für alle Seiten in einem Ordner mit dem angegebenen Pfad. |
| [AllowAnonymousToPage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) | Fügt eine [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) auf eine Seite mit dem angegebenen Pfad. |
| [AllowAnonymousToFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) | Fügt eine [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) für alle Seiten in einem Ordner mit dem angegebenen Pfad. |
