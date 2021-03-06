---
title: Примеры кода для платформы удостоверений Майкрософт
description: Список доступных примеров кода для платформы удостоверений Майкрософт (конечная точка версии 2.0), упорядоченный по сценариям.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 10/21/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 49bba8054065123d7409f26b61d1dfc2580ef720
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636057"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Примеры кода для платформы удостоверений Майкрософт (конечная точка версии 2.0)

Возможности платформы удостоверений Майкрософт:

- добавление возможностей аутентификации и авторизации в веб-приложения и веб-API;
- требование маркера доступа для доступа к защищенным веб-API.

В этой статье приведены краткое описание и ссылки на примеры для конечной точки платформы удостоверений Майкрософт. В этих примерах показано, как работает эта платформа, и предложены фрагменты кода для использования в реальных приложениях. На странице примеров кода приведены ссылки на статьи с подробными сведениями о требованиях, установке и настройке. Код содержит комментарии, которые помогают понять наиболее важные разделы.

Чтобы понять основной сценарий для каждого типа примера, см. статью [Типы приложений для платформы удостоверений Майкрософт](v2-app-types.md).

Вы также можете поделиться своими примерами на сайте GitHub. Чтобы узнать, как это сделать, обратитесь к разделу [Примеры и документация Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Одностраничные приложения

В этих примерах показано, как создать одностраничное приложение, защищенное с помощью платформы удостоверений Майкрософт. В этих примерах используется одна из разновидностей MSAL.js.

| Платформа | Описание | Ссылка |
| -------- | --------------------- | -------- |
| ![На этом изображении показан логотип JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js).](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA вызывает Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![На этом изображении показан логотип JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js).](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA вызывает Microsoft Graph с помощью потока кода проверки подлинности с PKCE |[javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![На этом изображении показан логотип JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js).](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA вызывает B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![На этом изображении показан логотип JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js).](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA вызывает B2C с помощью потока кода проверки подлинности с PKCE |[b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) |
| ![На этом изображении показан логотип Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA вызывает Microsoft Graph  | [active-directory-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![На этом изображении показан логотип Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA вызывает пользовательский веб-API | [ms-identity-javascript-angular-spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![На этом изображении показан логотип Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA вызывает B2C |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![На этом изображении показан логотип React](media/sample-v2-code/logo_react.png) [React (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA вызывает пользовательский веб-API, который в свою очередь вызывает Microsoft Graph  | [ms-identity-javascript-react-spa-dotnetcore-webapi-obo](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![На этом изображении показан логотип JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js).](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA вызывает пользовательский веб-API, который в свою очередь вызывает Microsoft Graph  | [ms-identity-javascript-tutorial-chapter4-obo](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/4-1-call-api-graph) |
| ![На этом изображении показан логотип Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA вызывает пользовательский веб-API с ролями приложений и группами безопасности |[ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |

## <a name="web-applications"></a>Веб-приложения

В следующем примере показаны веб-приложения для входа пользователей. В некоторых примерах приложение вызывает Microsoft Graph или веб-API с использованием удостоверения пользователя.

| Платформа | Только вход пользователей | Выполняет вход пользователей и вызывает Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![На этом изображении показан логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [Руководство по входу пользователей в систему для веб-приложений ASP.NET Core](https://aka.ms/aspnetcore-webapp-sign-in) | Тот же пример на этапе [Веб-приложение ASP.NET Core вызывает Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![На этом изображении показан логотип ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Краткое руководство по ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![На этом изображении показан логотип Java](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![На этом изображении показан логотип Python](media/sample-v2-code/logo_python.png)  | [ms-identity-python-flask-webapp-authentication](https://github.com/Azure-Samples/ms-identity-python-flask-webapp-authentication) | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![На этом изображении показан логотип Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Общедоступные клиентские приложения — классические и мобильные

В приведенных ниже примерах показаны общедоступные клиентские приложения (классические или мобильные), которые обращаются к API Microsoft Graph или веб-API от имени пользователя. Кроме примера *классического (консольного) приложения с WAM* , все эти клиентские приложения используют библиотеку аутентификации Майкрософт (MSAL).

| Клиентское приложение | Платформа | Поток или способ предоставления | Вызывает Microsoft Graph | Вызывает веб-API ASP.NET Core |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Классическое приложение (WPF)      | ![На этом изображении показан логотип .NET/C#](media/sample-v2-code/logo_NET.png) | [Код авторизации](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Классическое приложение (консольное)   | ![На этом изображении показан логотип .NET/C# (для классического приложения)](media/sample-v2-code/logo_NET.png) | [Встроенная аутентификация Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Классическое приложение (консольное)   | ![На этом изображении показан логотип Java](media/sample-v2-code/logo_java.png) | [Встроенная аутентификация Windows](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Классическое приложение (консольное)   | ![На этом изображении показан логотип .NET/C# (для классического приложения)](media/sample-v2-code/logo_NETcore.png) | [Имя пользователя или пароль](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Классическое (консольное) приложение с WAM  | ![На этом изображении показан логотип .NET/C# (для классического приложения)](media/sample-v2-code/logo_NETcore.png) | Интерактивный с использованием [диспетчера учетных веб-записей](/windows/uwp/security/web-account-manager) (WAM) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Классическое приложение (консольное)   | ![На этом изображении показан логотип Java](media/sample-v2-code/logo_java.png) | [Имя пользователя или пароль](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Классическое приложение (консольное)   | ![На этом изображении показан логотип Python](media/sample-v2-code/logo_python.png) | [Имя пользователя или пароль](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Мобильное приложение (Android, iOS, UWP)   | ![На этом изображении показан логотип .NET/C# (для Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Код авторизации](msal-authentication-flows.md#authorization-code) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Мобильное приложение (iOS)       | ![На этом изображении показаны iOS/Objective-C или SWIFT](media/sample-v2-code/logo_iOS.png) | [Код авторизации](msal-authentication-flows.md#authorization-code) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Классическое приложение (для macOS)       | macOS | [Код авторизации](msal-authentication-flows.md#authorization-code) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Мобильное приложение (для Android и Java)   | ![На этом изображении показан логотип Android](media/sample-v2-code/logo_Android.png) | [Код авторизации](msal-authentication-flows.md#authorization-code) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Мобильное приложение (для Android и Kotlin)   | ![На этом изображении показан логотип Android](media/sample-v2-code/logo_Android.png) | [Код авторизации](msal-authentication-flows.md#authorization-code) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Управляющие программы

В следующем примере показано приложение, которое обращается к Microsoft Graph с использованием собственного удостоверения (без пользователя).

| Клиентское приложение | Платформа | Поток или предоставление | Вызывает Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Консоль | ![На этом изображении показан логотип .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Веб-приложение | ![На этом изображении показан логотип ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Консоль | ![На этом изображении показан логотип Java](media/sample-v2-code/logo_java.png) | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Консоль | ![На этом изображении показан логотип Python](media/sample-v2-code/logo_python.png) | [Учетные данные клиента](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Приложения удаленного управления

В следующем примере показано общедоступное клиентское приложение, запущенное на устройстве без веб-браузера. Это приложение может быть программой командной строки или приложением для Linux или Mac либо приложением Интернета вещей. В этом примере представлено приложение, которое обращается к API Microsoft Graph от имени пользователя, который в интерактивном режиме выполняет вход на другом устройстве (например, на мобильном телефоне). Это клиентское приложение использует библиотеку проверки подлинности Майкрософт (MSAL).

| Клиентское приложение | Платформа | Поток или предоставление | Вызывает Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Классическое приложение (консольное)   | ![На этом изображении показан логотип .NET/C# (для классического приложения)](media/sample-v2-code/logo_NETcore.png) | [Поток кода устройства](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Классическое приложение (консольное)   | ![На этом изображении показан логотип Java](media/sample-v2-code/logo_java.png) | [Поток кода устройства](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Классическое приложение (консольное)   | ![На этом изображении показан логотип Python](media/sample-v2-code/logo_python.png) | [Поток кода устройства](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>Мультитенантные приложения SaaS

В следующих примерах показано, как настроить приложение таким образом, чтобы оно поддерживало вход из любого клиента Azure AD (AD). Настройка приложения как *мультитенантного* означает, что вы можете предлагать **программное обеспечение как услугу** (SaaS) многим организациям, позволяя пользователям входить в приложение после предоставления согласия на использование своей учетной записи.

| Платформа | Описание | Ссылка |
| -------- | --------------------- | -------- |
| ![На этом изображении показан логотип JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js).](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | Мультитенантное одностраничное приложение вызывает API Graph |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![На этом изображении показан логотип Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | Мультитенантное SPA вызывает мультитенантный пользовательский веб-API |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![На этом изображении показан логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | Веб-приложение ASP.NET Core MVC вызывает API Graph |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![На этом изображении показан логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | Веб-приложение ASP.NET Core MVC вызывает веб-API ASP.NET Core |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Веб-API

В следующих примерах показано, как защитить веб-API с помощью конечной точки платформы удостоверений Майкрософт и как вызывать из веб-API подчиненный API.

| Платформа | Образец |
| -------- | ------------------- |
| ![На этом изображении показан логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | Веб-API (служба) ASP.NET Core [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![На этом изображении показан логотип ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC 3 | Веб-API (служба) для [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![На этом изображении показан логотип Java](media/sample-v2-code/logo_java.png) | Веб-API (служба) для [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![На этом изображении показан логотип Node.js](media/sample-v2-code/logo_nodejs.png) | Веб-API (служба) для [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![На этом изображении показан логотип Node.js](media/sample-v2-code/logo_nodejs.png) | B2C Веб-API (служба) для [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Функции Azure в качестве интерфейсов веб-API

В следующих примерах показано, как защитить функцию Azure с помощью HttpTrigger и веб-API в конечной точке платформы удостоверений Майкрософт и как вызывать из веб-API подчиненный API.

| Платформа | Образец |
| -------- | ------------------- |
| ![На этом изображении показан логотип ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | Веб-API (служба) ASP.NET Core Функций Azure для [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![На этом изображении показан логотип Python](media/sample-v2-code/logo_python.png)</p>Python | Веб-API (служба) для [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![На этом изображении показан логотип Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js | Веб-API (служба) для [Node.js и passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![На этом изображении показан логотип Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js | Веб-API (служба) для [NodeJS и passport-azure-ad с работой от имени](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Другие примеры Microsoft Graph

Дополнительные сведения о [примерах](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) и учебниках, в которых приводятся различные шаблоны использования API Microsoft Graph, включая аутентификацию в Azure AD, см. в статье [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples) (Примеры и учебники сообщества Microsoft Graph).

## <a name="see-also"></a>См. также раздел

[Основные понятия и справочные материалы по API Microsoft Graph](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta)
