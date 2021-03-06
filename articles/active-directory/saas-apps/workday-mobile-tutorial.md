---
title: Руководство по интеграции единого входа Azure Active Directory с мобильным приложением Workday | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в мобильное приложение Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 256da169761da486d8ac064a2f58a59be43bb5df
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754185"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Руководство по интеграции единого входа Azure Active Directory с мобильным приложением Workday

В этом учебнике описано, как интегрировать Azure Active Directory (Azure AD), условный доступ и Intune с мобильными приложениями Workday. При интеграции мобильных приложений Workday с платформой Майкрософт можно выполнить следующие действия:

* Перед входом убедитесь, что устройства соответствуют политикам.
* Добавьте элементы управления в приложение Workday, чтобы пользователи могли безопасно получать доступ к корпоративным данным. 
* С помощью Azure AD вы можете контролировать доступ к Workday.
* Вы можете включить автоматический вход пользователей в Workday с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Интеграция Workday с Azure AD
* Руководство по [интеграции единого входа Azure Active Directory с Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите политики условного доступа Майкрософт и Intune с помощью мобильных приложений Workday.

* Теперь для федеративного приложения Workday можно настроить Azure AD для обеспечения единого входа. Дополнительные сведения о настройке см. по [этой ссылке](workday-tutorial.md).

> [!NOTE] 
> Workday не поддерживает политики Защиты приложений Intune. Чтобы применять условный доступ, необходимо использовать управление мобильными устройствами.


## <a name="ensure-users-have-access-to-the-workday-mobile-app"></a>Убедитесь, что пользователи имеют доступ к мобильному приложению Workday:

Настройте Workday, чтобы разрешить доступ к предложениям мобильных приложений. Необходимо настроить следующие политики для мобильных устройств.

Это можно сделать, выполнив следующие инструкции:

1. Получите доступ к отчету о политиках безопасности домена для функциональной области.
2. Выберите политику безопасности.
    * Использование мобильных устройств — Android
    * Использование мобильных устройств — iPad
    * Использование мобильных устройств — iPhone
3. Нажмите кнопку Изменить разрешения.
4. Установите флажок "Просмотреть" или "Изменить", чтобы предоставить группам безопасности доступ к защищаемым элементам отчета или задачи.
5. Установите флажок "Получить" или "Поместить", чтобы предоставить группам безопасности доступ к интеграции, а также к действиям, выполняемым в отчетах или задачах.

Активируйте ожидающие изменения политики безопасности, запустив **соответствующую** задачу.

## <a name="open-workday-login-page-in-mobile-browser"></a>Открытие страницы входа Workday в браузере мобильного устройства

Чтобы применить условный доступ к мобильному приложению Workday, необходимо открыть приложение во внешнем браузере. Это можно сделать, установив флажок **Enable Mobile Browser SSO for Native Apps** (Включить единый вход браузера мобильного устройства для собственных приложений) в разделе **Edit Tenant Setup - Security** (Изменение настройки клиента — безопасность). Для этого на устройстве iOS и в рабочем профиле для Android будет установлен браузер, утвержденный Intune.

![Вход в браузер мобильного устройства](./media/workday-tutorial/mobile-browser.png)

## <a name="setup-conditional-access-policy"></a>Настройка политики условного доступа

Эта политика влияет только на ведение журнала на устройстве iOS или Android. Если вы хотите расширить ее на всех платформах, просто выберите **Любое устройство**. Эта политика потребует, чтобы устройство соответствовало политике, и проверит это с помощью Microsoft Intune. Из-за наличия рабочих профилей Android все пользователи должны блокировать вход в Workday (веб-приложение или приложение), если только они не входят в систему через свой рабочий профиль и не установили приложение с помощью Корпоративного портала Intune. Есть еще один дополнительный шаг для iOS, чтобы убедиться в том, что будет применен тот же подход. Ниже приведены некоторые снимки экрана настройки условного доступа.

**Workday поддерживает следующие элементы управления доступом:**
* Требование многофакторной идентификации
* Требовать, чтобы устройство было отмечено как соответствующее

**Приложение Workday не поддерживает следующие действия:**
* Требование утвержденного клиентского приложения
* Требование политики защиты приложений (предварительная версия)

Чтобы настроить **Workday** в качестве **управляемого устройства** , выполните следующие действия.

![Настройка политики условного доступа](./media/workday-tutorial/managed-devices-only.png)

1. Последовательно выберите **Домашняя страница > Microsoft Intune > Политики условного доступа > Managed Devices Only (Только управляемые устройства)** . 

1. На странице **Managed Devices Only** (Только управляемые устройства) присвойте полю **Имя** значение `Managed Devices Only` и щелкните **Облачные приложения или действия**.

1. На странице **Облачные приложения или действия** выполните следующие действия.

    a. Для параметра **Выбрать объект, к которому будет применяться эта политика** выберите значение **Облачные приложения**.

    b. В списке "Включить" щелкните **Выбрать приложения**.

    c. Выберите **Workday** из списка выбора.

    d. Нажмите кнопку **Done** (Готово).

1. Выберите параметр **Включить политику**.

1. Выберите команду **Сохранить**.

В колонке **Предоставить** выполните следующие действия:

![Настройка политики условного доступа Workday](./media/workday-tutorial/managed-devices-only-2.png)

1. Последовательно выберите **Домашняя страница > Microsoft Intune > Политики условного доступа > Managed Devices Only (Только управляемые устройства)** . 

1. На странице **Managed Devices Only** (Только управляемые устройства) присвойте полю **Имя** значение `Managed Devices Only` и щелкните **Элементы управления доступом > Предоставить**.

1. На странице **Предоставить** выполните следующие действия:

    a. Выберите элементы управления, которые следует применять принудительно в качестве параметра **Предоставить доступ**.

    b. Установите флажок **Требовать, чтобы устройство было отмечено как соответствующее**.

    c. Выберите **Требовать один из выбранных элементов управления**.

    d. Нажмите кнопку **Выбрать**.

1. Выберите параметр **Включить политику**.

1. Нажмите кнопку **Сохранить**

## <a name="set-up-device-compliance-policy"></a>Настройка политики соответствия устройств

Чтобы устройства iOS могли входить только через приложение Workday, управляемое MDM, необходимо заблокировать приложение App Store, добавив **com.workday.workdayapp** в список ограниченных приложений. Это обеспечит доступ к Workday только для устройств, на которых установлено приложение Workday, скачанное с помощью корпоративного портала. При использовании браузера они смогут получить доступ к Workday только в том случае, если устройством управляет Intune и работа осуществляется через управляемый браузер.

![Настройка политики соответствия устройств Workday](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-microsoft-intune-app-configuration-policies"></a>Настройка политик Конфигурации приложений Microsoft Intune

| Сценарий | Пары "ключ-значение" |
|----------------------------------------------------------------------------------------   |-----------|
| Автоматически заполните поля "Клиент" и "Веб-адрес" для:<br>● Workday на Android при включении профилей Android for Work.<br>● Workday на iPad и iPhone.     | Для настройки клиента используйте следующие значения: <br>● Ключ конфигурации = UserGroupCode<br>● Тип значения = String <br>● Значение конфигурации = имя_клиента Пример: gms<br>Используйте эти значения для настройки веб-адреса:<br>● Ключ конфигурации = AppServiceHost<br>● Тип значения = String<br>● Значение конфигурации = базовый_URL-адрес_клиента Пример: https://www.myworkday.com                              |   |
| Отключите эти действия для Workday на iPad и iPhone:<br>● Команды вырезки, копирования и вставки.<br>● Печать.                       | Чтобы отключить эту функцию, установите для этих ключей значение false (логическое):<br>● AllowCutCopyPaste<br>● AllowPrint  |
| Отключите снимки экрана для Workday на Android. |Чтобы отключить функциональные возможности, установите значение false для ключа AllowScreenshots.|
| Отключите предлагаемые обновления для пользователей.|Чтобы отключить функциональные возможности, установите значение false для ключа AllowSuggestedUpdates.|
|Настройте URL-адрес магазина приложений, чтобы пользователи мобильных устройств могли выбрать магазин приложений.|Используйте эти значения, чтобы изменить URL-адрес магазина приложений:<br>● Ключ конфигурации = AppUpdateURL<br>● Тип значения = String<br> ● Значение конфигурации = URL-адрес_магазина_приложений|
|       |


## <a name="ios-configuration-policies"></a>Политики конфигурации iOS

1. Перейдите на сайт https://portal.azure.com/ и войдите в систему.
2. Найдите **Intune** или щелкните мини-приложение в списке.
3. Последовательно выберите **Клиентские приложения -> Приложения -> Политики конфигурации приложений -> + Добавить -> Управляемые устройства**.
4. Введите имя.
5. В разделе **Платформа** выберите **iOS/iPadOS**.
6. В разделе **Связанное приложение** выберите добавленное приложение Workday для iOS.
7. Щелкните **Параметры конфигурации** и в разделе **Формат параметров конфигурации** выберите **Введите данные XML**.
8. Ниже приведен пример XML-файла. Добавьте конфигурации, которые вы хотите применить. Замените **STRING_VALUE** строкой, которую вы хотите использовать. Замените `<true />` или `<false />` на `<true />` или `<false />`. Если не добавить конфигурацию, она будет работать так же, как если ей присвоено значение true.

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
9. Нажмите "Добавить"
10. Обновите страницу и щелкните только что созданную политику.
11. Щелкните "Назначения" и выберите к кому нужно применить приложение.
12. Нажмите кнопку «Сохранить».

## <a name="android-configuration-policies"></a>Политики конфигурации Android

1. Перейдите на сайт `https://portal.azure.com/` и войдите в систему.
2. Найдите **Intune** или щелкните мини-приложение в списке.
3. Последовательно выберите **Клиентские приложения -> Приложения -> Политики конфигурации приложений -> + Добавить -> Управляемые устройства**.
5. Введите имя. 
6. В разделе **Платформа** выберите **Android**.
7. В разделе **Связанное приложение** выберите добавленное приложение Workday для Android.
8. Щелкните **Параметры конфигурации** и в разделе **Формат параметров конфигурации** выберите **Ввод данных JSON**.

