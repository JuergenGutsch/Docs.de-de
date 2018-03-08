---
title: "Abhängigkeitsinjektion in Anforderung Handler"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 308951a45ee6576f096e1cdc792208b89e476e61
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2017
---
# <a name="dependency-injection-in-requirement-handlers"></a>Abhängigkeitsinjektion in Anforderung Handler

<a name="security-authorization-di"></a>

[Müssen Autorisierung Handler registriert werden](policies.md#handler-registration) in die Auflistung während der Konfiguration (mit [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)).

Angenommen, mussten Sie ein Repository mit Regeln innerhalb eines ereignishandlers Autorisierung auswerten möchten, und diesem Repository registriert wurde, in die Auflistung. Die Autorisierung wird beheben und einfügen, die in Ihrem Konstruktor.

Angenommen, Sie ASP verwenden möchten. NET der Infrastruktur, die Sie einfügen möchten Protokollierung `ILoggerFactory` in den Handler. Ein Handler möglicherweise formuliert werden:

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

Registrieren Sie den Handler mit `services.AddSingleton()`:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Eine Instanz der Ereignishandler wird beim Start der Anwendung erstellt werden und DI wird die registrierte einfügen `ILoggerFactory` in Ihrem Konstruktor.

> [!NOTE]
> Handler, die Verwendung von Entity Framework darf nicht als Singletons registriert werden.
