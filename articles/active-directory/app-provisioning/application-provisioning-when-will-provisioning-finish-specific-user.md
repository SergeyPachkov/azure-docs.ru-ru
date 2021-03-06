---
title: Узнайте, когда конкретный пользователь сможет получить доступ к приложению
description: В этой статье описано, как определить, когда критически важный пользователь получит доступ к приложению, которое было настроено для подготовки пользователя в Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 307a97b71fe453c89617a86a88063e60fcf28fa3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88235066"
---
# <a name="check-the-status-of-user-provisioning"></a>Проверка состояния подготовки пользователей

Служба подготовки Azure AD выполняет первоначальный цикл подготовки к исходной системе и целевой системе, за которыми следуют периодические инкрементные циклы. При настройке подготовки для приложения можно проверить текущее состояние службы подготовки и узнать, когда пользователь сможет получить доступ к приложению.

## <a name="view-the-provisioning-progress-bar"></a>Просмотр индикатора выполнения подготовки

 На странице **Подготовка** для приложения вы можете просмотреть состояние службы подготовки Azure AD. В разделе **Current Status (текущее состояние** ) в нижней части страницы показано, начал ли цикл подготовки учетные записи пользователей. Вы можете отслеживать ход выполнения цикла, узнать, сколько пользователей и групп были подготовлены, и узнать, сколько ролей создано.

При первой настройке автоматической подготовки в разделе **Текущее состояние** в нижней части страницы отображается состояние начального цикла подготовки. Этот раздел обновляется каждый раз при выполнении инкрементного цикла. Отображаются следующие сведения:
- Тип цикла подготовки (начального или добавочного), который в данный момент выполняется или последний раз был завершен.
- **Индикатор выполнения** , показывающий процент завершенного цикла подготовки. Процент отражает количество подготовленных страниц. Обратите внимание, что каждая страница может содержать несколько пользователей или групп, поэтому процентная доля не сопоставляется напрямую с числом пользователей, групп или ролей, подготовленных к выполнению.
- Кнопка **обновления** , с помощью которой можно обновлять представление.
- Число **пользователей** и **групп** в хранилище данных соединителя. Число увеличивается каждый раз, когда объект добавляется в область подготовки. Счетчик не будет передвигаться, если пользователь обратимо удален или жестко удален, так как он не удаляет объект из хранилища данных соединителя. Число будет пересчитано при первой синхронизации после [сброса](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta) компакт-дисков 
- Ссылка **Просмотреть журналы аудита** , которая открывает журналы подготовки Azure AD для получения подробных сведений обо всех операциях, выполняемых службой подготовки пользователей, включая состояние подготовки для отдельных пользователей (см. раздел [Использование журналов подготовки](#use-provisioning-logs-to-check-a-users-provisioning-status) ниже).

После завершения цикла подготовки в разделе **Статистика по дате** отображается совокупное число пользователей и групп, которые были подготовлены к работе вместе с датой завершения и длительностью последнего цикла. **Идентификатор действия** однозначно определяет самый последний цикл подготовки. **Идентификатор задания** — это уникальный идентификатор задания подготовки, который относится к приложению в клиенте.

Ход подготовки можно просмотреть в портал Azure, на вкладке ** &gt; &gt; \[ \] &gt; Подготовка Azure Active Directory приложения корпоративные приложения** .

![Индикатор выполнения страницы подготовки](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Использование журналов подготовки для проверки состояния подготовки пользователя

Чтобы просмотреть состояние подготовки для выбранного пользователя, просмотрите [журналы подготовки (Предварительная версия)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) в Azure AD. Все операции, выполняемые службой подготовки пользователей, записываются в журналы подготовки Azure AD. Сюда входят все операции чтения и записи, выполненные в исходной и целевой системах, а также данные пользователя, которые были считаны или записаны во время каждой операции.

Вы можете получить доступ к журналам подготовки в портал Azure, выбрав **Azure Active Directory** &gt; **корпоративные приложения** &gt; **журналы подготовки (Предварительная версия)** в разделе **действие** . Данные подготовки можно искать на основе имени пользователя или идентификатора в исходной системе или в целевой системе. Дополнительные сведения см. в статье [Подготовка журналов (Предварительная версия)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Журналы подготовки записывают все операции, выполняемые службой подготовки, в том числе:

* Получение сведений о назначенных пользователях, которые находятся в области для подготовки, в Azure AD
* Получение сведений о наличии этих пользователей от целевого приложения
* Сравнение объектов пользователя в разных системах
* Добавление, обновление или отключение учетной записи пользователя в целевой системе на основе сравнения

Дополнительные сведения о том, как читать журналы подготовки в портал Azure, см. в разделе Руководство по [подготовке отчетов по обеспечению подготовки](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Сколько времени занимает подготовка пользователей?
При использовании автоматической подготовки пользователей в приложении Azure AD автоматически подготавливает и обновляет учетные записи пользователей в приложении на основе таких объектов, как [Назначение пользователей и групп](../manage-apps/assign-user-or-group-access-portal.md) в регулярно запланированном интервале времени, обычно каждые 40 минут.

Время, необходимое для подготовки данного пользователя, зависит главным образом от того, выполняет ли задание подготовки начальный цикл или добавочный цикл.

- Для **начального цикла**время задания зависит от многих факторов, включая количество пользователей и групп в области для подготовки, а также общее число пользователей и групп в исходной системе. Первая синхронизация приложения с Azure AD может занять от 20 минут до нескольких часов, в зависимости от размера каталога Azure AD и количества пользователей в области подготовки. Далее в этом разделе приводится полный список факторов, влияющих на производительность начального цикла.

- Для **инкрементных циклов** после начального цикла время выполнения заданий будет выше (например, в течение 10 минут), так как служба подготовки сохраняет водяные знаки, представляющие состояние обеих систем после начального цикла, повышая производительность последующих операций синхронизации. Время задания зависит от числа изменений, обнаруженных в этом цикле подготовки. При наличии менее 5 000 изменений в членстве пользователя или группы задание может завершиться в рамках одного цикла добавочной подготовки. 

В следующей таблице перечислены данные о времени синхронизации, необходимом для общих сценариев подготовки. В этих сценариях исходной системой является Azure AD, а целевой — приложение SaaS. Время синхронизации определяется статистическим анализом заданий синхронизации для приложений SaaS ServiceNow, рабочей области, Salesforce и G Suite.


| Конфигурация области | Пользователи, группы и участники в области | Начальное время цикла | Добавочное время цикла |
| -------- | -------- | -------- | -------- |
| Синхронизация только назначенных пользователей и групп |  < 1000 |  < 30 минут | < 30 минут |
| Синхронизация только назначенных пользователей и групп |  1000–10 000 | 142–708 минут | < 30 минут |
| Синхронизация только назначенных пользователей и групп |   10 000–100 000 | 1170–2,340 минут | < 30 минут |
| Синхронизация всех пользователей и групп в Azure AD |  < 1000 | < 30 минут  | < 30 минут |
| Синхронизация всех пользователей и групп в Azure AD |  1000–10 000 | 30–120 минут | < 30 минут |
| Синхронизация всех пользователей и групп в Azure AD |  10 000–100 000  | 713–1425 минут | < 30 минут |
| Синхронизация всех пользователей в Azure AD|  < 1000  | < 30 минут | < 30 минут |
| Синхронизация всех пользователей в Azure AD | 1000–10 000  | 43–86 минут | < 30 минут |

Для синхронизации конфигурации **только назначенные пользователи и группы**можно использовать следующие формулы для определения приблизительного минимального и максимального ожидаемого **начального времени цикла** :

- Минимальный минуты = 0,01 x [число назначенных пользователей, групп и членов групп]
- Максимальное количество минут = 0,08 x [число назначенных пользователей, групп и членов групп]

Сводка факторов, влияющих на время, затрачиваемое на выполнение **начального цикла**:

- Общее количество пользователей и групп, которые будут подготовлены.

- Общее количество пользователей, групп и участников группы, находящихся в исходной системе (Azure AD).

- Будут ли пользователи в области подготовки сопоставляться с существующими пользователями в целевом приложении или должны быть созданы в первый раз. Задания синхронизации, для которых все пользователи создаются в первый раз, выполняются в *два раза* дольше, чем задания синхронизации, для которых все пользователи сопоставляются с существующими пользователями.

- Количество ошибок в [журналах подготовки](check-status-user-account-provisioning.md). Производительность также снижается, если возникает большое количество ошибок и служба подготовки перешла в состояние карантина. 

- Ограничения частоты и регулирования запросов, реализованные целевой системой. Некоторые целевые системы реализуют ограничения скорости запросов и регулирование, что может повлиять на производительность во время больших операций синхронизации. В этих условиях приложение, получающее слишком много запросов за короткий промежуток времени, может снизить частоту реагирования или закрыть подключение. Чтобы повысить производительность, соединитель необходимо настроить таким образом, чтобы количество отправляемых запросов соответствовало количеству запросов, которые может обработать приложение. Соединители подготовки, созданные корпорацией Майкрософт, позволяют внести эту корректировку. 

- Количество и размеры назначенных групп. Синхронизация назначенных групп длится дольше, чем синхронизация пользователей. Число и размер назначенных групп оказывают влияние на производительность. Если в приложении [включена функция сопоставления для синхронизации объектов группы](customize-application-attributes.md#editing-group-attribute-mappings), свойства группы (например, названия групп и членства) синхронизируются в дополнение к пользователям. Эти дополнительные синхронизации занимают больше времени, чем синхронизация только объектов пользователей.

- Если производительность станет проблемой и вы пытаетесь подготавливать большинство пользователей и групп в клиенте, используйте фильтры области. Фильтры области позволяют точно настроить данные, которые служба подготовки получает из Azure AD, за счет фильтрации пользователей по значению конкретного атрибута. Дополнительные сведения о фильтрах области см. в статье [Подготовка приложений на основе атрибутов с использованием фильтров области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Дальнейшие шаги
[Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](user-provisioning.md)