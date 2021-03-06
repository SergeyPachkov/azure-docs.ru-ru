---
title: Учебник. Настройка синхронизации получателей Кофенсе для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как автоматически подготавливать и отзывать учетные записи пользователей из Azure AD в службу синхронизации получателей Кофенсе.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 84fe20ef-0de0-4f7c-9b42-6385f3d834db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: Zhchia
ms.openlocfilehash: 69a9b9401f25893ec94b282f52730d92d372268d
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94355708"
---
# <a name="tutorial-configure-cofense-recipient-sync-for-automatic-user-provisioning"></a>Учебник. Настройка синхронизации получателей Кофенсе для автоматической подготовки пользователей

В этом учебнике описываются действия, которые необходимо выполнить в службе синхронизации получателей Кофенсе и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. После настройки Azure AD автоматически подготавливает и отменяет подготовку пользователей к [синхронизации получателей кофенсе](https://cofense.com/) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в Кофенсе синхронизации получателей
> * Удалять пользователей из Кофенсе получателей синхронизации, когда им больше не нужен доступ
> * Синхронизировать атрибуты пользователей между Azure AD и синхронизацией получателей Кофенсе

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](../develop/quickstart-create-new-tenant.md) 
* Учетная запись пользователя в Azure AD с [разрешением](../users-groups-roles/directory-assign-admin-roles.md) настраивать подготовку (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* Стандартная учетная запись оператора в Кофенсе Фишме.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](../app-provisioning/user-provisioning.md).
2. Определите, кто будет находиться в [области подготовки](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Определите, какие данные должны [сопоставляться между Azure AD и синхронизацией получателей кофенсе](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-cofense-recipient-sync-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка синхронизации получателей Кофенсе для поддержки подготовки с помощью Azure AD

1. Войдите в Кофенсе Фишме. Перейдите к **получателю > синхронизации получателей**. 
2. Примите условия и нажмите кнопку **начать работу**.

    ![Получателя Sync ТНК](media/cofense-provisioning-tutorial/recipient-sync-toc.png)

3. Скопируйте значения из полей **URL-адрес** и **токен** .

    ![Синхронизация получателя](media/cofense-provisioning-tutorial/recipient-sync-getting-started.png)


## <a name="step-3-add-cofense-recipient-sync-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление синхронизации получателей Кофенсе из коллекции приложений Azure AD

Добавьте синхронизацию получателей Кофенсе из коллекции приложений Azure AD, чтобы начать управление подготовкой к синхронизации получателей Кофенсе. Если вы ранее настроили синхронизацию получателей Кофенсе для единого входа, можно использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили указать, кто именно будет подготовлен, на основе одних только атрибутов пользователя или группы, можете использовать фильтр задания области, как описано [здесь](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* При назначении пользователей и групп для синхронизации получателей Кофенсе необходимо выбрать роль, отличную от **доступа по умолчанию**. Пользователи с ролью "Доступ по умолчанию" исключаются из подготовки и будут помечены в журналах подготовки как не назначенные явно. Кроме того, если эта роль является единственной, доступной в приложении, можно [изменить манифест приложения](../develop/howto-add-app-roles-in-azure-ad-apps.md), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-cofense-recipient-sync"></a>Шаг 5. Настройка автоматической подготовки пользователей для синхронизации получателей Кофенсе 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей в службе синхронизации получателей Кофенсе на основе пользователя в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cofense-recipient-sync-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей для синхронизации получателей Кофенсе в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения** , а затем **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Кофенсе получатель синхронизация**.

    ![Ссылка на Кофенсе в списке "приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "Подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Автоматическая вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите **базовый URL-адрес scim 2,0 и значение маркера проверки подлинности scim** , полученное ранее на шаге 2. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к службе синхронизации получателей кофенсе. Если подключение не выполняется, убедитесь, что у учетной записи синхронизации получателей Кофенсе есть разрешения администратора, и повторите попытку.

    ![Токен URL-адреса клиента](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое**.

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с синхронизацией получателей кофенсе**.

9. Изучите пользовательские атрибуты, которые синхронизированы из Azure AD, с помощью синхронизации получателей Кофенсе в разделе " **сопоставление атрибутов** ". Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в Кофенсе получателя синхронизации для операций обновления.  Нажмите кнопку **Сохранить** , чтобы зафиксировать все изменения.

   |attribute|Тип|
   |---|---|
   |userName|Строка|
   |externalId|Строка|
   |active|Логическое|
   |displayName|Строка|
   |name.formatted|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |имя. Хонорификсуффикс|Строка|
   |Фоненумберс [Type EQ "Рабочий"]. Value|Строка|
   |Фоненумберс [Type EQ "Home"]. значение|Строка|
   |Фоненумберс [Введите EQ "другое"]. Value|Строка|
   |Фоненумберс [Type EQ "пейджер"]. Value|Строка|
   |Фоненумберс [тип EQ "Мобильный"]. значение|Строка|
   |Фоненумберс [тип EQ "Факс"]. значение|Строка|
   |адреса [Введите EQ "Other"]. форматированный|Строка|
   |адреса [Type EQ "Рабочая"]. форматированный|Строка|
   |адреса [Type EQ "Рабочий"]. streetAddress|Строка|
   |адреса [Введите EQ "Рабочий"]. локальность|Строка|
   |адреса [Type EQ "Рабочий"]. регион|Строка|
   |адреса [Type EQ "Рабочий"]. postalCode|Строка|
   |адреса [Type EQ "Рабочая"]. Country|Строка|
   |title|Строка|
   |emails[type eq "work"].value|Строка|
   |сообщения электронной почты [Type EQ "Home"]. значение|Строка|
   |сообщения электронной почты [Введите EQ "другое"]. значение|Строка|
   |preferredLanguage|Строка|
   |nickName|Строка|
   |userType|Строка|
   |локаль|Строка|
   |timezone|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Справочник|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|Строка|

10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу подготовки Azure AD для синхронизации получателей Кофенсе, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

12. Определите пользователей и (или) группы, которые вы хотите подготавливать к синхронизации получателей Кофенсе, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется цикл начальной синхронизации всех пользователей и групп, определенных в поле **Область** в разделе **Параметры**. Начальный цикл занимает больше времени, чем последующие циклы. Пока служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

1. Используйте [журналы подготовки](../reports-monitoring/concept-provisioning-logs.md), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
2. Используйте [индикатор выполнения](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), чтобы узнать состояние цикла подготовки и приблизительное время до его завершения.
3. Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о режимах карантина см. [здесь](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)