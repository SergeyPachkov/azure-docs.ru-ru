---
title: Установка агента Azure AD Connect Health | Документация Майкрософт
description: Это страница Azure AD Connect Health, на которой описана процедура установки агента для AD FS и синхронизации.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: da3ae5e86833eb3e7eb71d7e47cb6f963d37b9cf
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410731"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Установка агента Azure AD Connect Health

В этом документе описываются этапы установки и настройки агентов Azure AD Connect Health. Загрузить агенты можно [отсюда](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent):

## <a name="requirements"></a>Требования

В таблице ниже приведен список предварительных требований для использования Azure AD Connect Health.

| Требование | Описание |
| --- | --- |
| Azure AD Premium |Служба Azure AD Connect Health относится к Azure AD Premium и требует наличия выпуска Azure AD Premium. <br /><br />Дополнительные сведения см. в статье [Приступая к работе с Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) <br />Чтобы получить бесплатную 30-дневную пробную версию, перейдите [по этой ссылке](https://azure.microsoft.com/trial/get-started-active-directory/). |
| Для начала работы с Azure AD Connect Health требуются права глобального администратора Azure AD  |По умолчанию только глобальный администратор может установить и настроить агенты работоспособности, чтобы приступить к работе, получить доступ к порталу и начать выполнять любые операции в Azure AD Connect Health. Дополнительные сведения см. в статье [Администрирование каталога Azure AD](../fundamentals/active-directory-whatis.md). <br /><br /> С помощью управления доступом на основе ролей Azure (Azure RBAC) можно разрешить доступ к Azure AD Connect Health другим пользователям в Организации. Дополнительные сведения см. в статье [Управление доступом на основе ролей Azure (Azure RBAC) для Azure AD Connect Health.](how-to-connect-health-operations.md#manage-access-with-azure-rbac) <br /><br />**Важно!** Учетная запись, которая используется во время установки агентов, должна быть рабочей или учебной. Учетную запись Майкрософт для этого использовать нельзя. Дополнительные сведения см. в статье [Подпишитесь на Azure как организация](../fundamentals/sign-up-organization.md). |
| Агент Azure AD Connect Health следует установить на всех целевых серверах | Для использования Azure AD Connect Health на целевых серверах необходимо установить и настроить агенты работоспособности, чтобы получать данные и предоставлять возможности мониторинга и аналитики. <br /><br />Например, для получения данных об инфраструктуре AD FS необходимо установить агент на серверы AD FS и прокси-серверы веб-приложений. Аналогично, для получения данных о локальной инфраструктуре AD DS агент необходимо установить на контроллеры домена. <br /><br /> |
| Исходящие подключения к конечным точкам службы Azure | Во время установки и выполнения агенту требуется подключение к конечным точкам службы Azure AD Connect Health. При блокировке исходящего подключения с помощью брандмауэров убедитесь, что в список разрешенных добавлены указанные ниже конечные точки. См. [здесь](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints). |
|Исходящие подключения на основе IP-адресов | Дополнительные сведения о фильтрации на основе IP-адресов в брандмауэрах см. в статье о [диапазонах IP-адресов Azure](https://www.microsoft.com/download/details.aspx?id=41653).|
| Проверка TLS для исходящего трафика фильтруется или отключается. | Выполнение этапа регистрации агента или операции передачи данных может завершиться ошибкой, если для исходящего трафика на сетевом уровне имеется проверка TLS или прерывание. Подробнее о [настройке проверки TLS](/previous-versions/tn-archive/ee796230(v=technet.10)) |
| Порты брандмауэра на сервере с агентом |Агент требует открытия следующих портов брандмауэра для обмена данными с конечными точками службы Azure AD Health.<br /><br /><li>TCP-порт 443</li><li>TCP-порт 5671</li> <br />Обратите внимание на то, что для последней версии агента больше не нужен порт 5671. Для обновления до последней версии требуется только порт 443. Ознакомьтесь с дополнительными сведениями о [включении портов брандмауэра](/previous-versions/sql/sql-server-2008/ms345310(v=sql.100)). |
| Внесите следующие веб-сайты в список разрешенных, если включена политика усиленной безопасности IE |Если на сервере, на котором будет установлен агент, включена конфигурация усиленной безопасности, потребуется открыть доступ для следующих веб-сайтов:<br /><br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>HTTPS: \/ /aadcdn.msftauth.NET</li><li>Сервер федерации вашей организации должен быть доверенным для Azure Active Directory. Например: https:\//sts.contoso.com.</li> Дополнительные сведения о [настройке IE см. в](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing)этой статье. Если у вас есть прокси-сервер в вашей сети, см. Примечание ниже.|
| Установлена служба PowerShell 4.0 или более поздней версии | <li>Windows Server 2012 поставляется вместе с PowerShell версии 3.0, что недостаточно для агента.</li><li>Windows Server 2012 R2 и более поздней версии поставляется с последней версией PowerShell.</li>|
|Отключение FIPS|FIPS не поддерживается агентами Azure Active Directory Connect Health.|

> [!IMPORTANT]
> Установка агента Azure AD Connect Health в Windows Server Core не поддерживается.


> [!NOTE]
> При наличии сильно заблокированной и очень ограниченной среды вам потребуется добавить URL-адреса, указанные в списках конечных точек службы, в дополнение к тем, которые перечислены в разрешенной выше конфигурации усиленной безопасности IE. 


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Исходящие подключения к конечным точкам службы Azure

 Во время установки и выполнения агенту требуется подключение к конечным точкам службы Azure AD Connect Health. При блокировке исходящего подключения с помощью брандмауэров убедитесь, что в список запрещенных не были по умолчанию добавлены указанные ниже URL-адреса. Не отключайте мониторинг безопасности или проверку URL-адресов, но предоставьте им разрешения такие же, как и интернет-трафику. Они разрешают обмен данными с конечными точками службы Azure AD Connect Health. Узнайте, как [проверить исходящие подключения с помощью Test-AzureADConnectHealthConnectivity](#test-connectivity-to-azure-ad-connect-health-service).

| Доменная среда | Требуемые конечные точки службы Azure |
| --- | --- |
| Общедоступная сеть | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;. servicebus.windows.net-Port: 5671 (не требуется в последней версии агента)</li><li>&#42;.adhybridhealth.azure.com/;</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com. *Эта конечная точка используется только для обнаружения во время регистрации.</li> |
| Azure для Германии | <li>*.blob.core.cloudapi.de; </li><li>*.servicebus.cloudapi.de; </li> <li>*.aadconnecthealth.microsoftazure.de; </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https:\//www.office.de. *Эта конечная точка используется только для обнаружения во время регистрации.</li> |
| Azure для государственных организаций | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com. *Эта конечная точка используется только для обнаружения во время регистрации.</li> |


## <a name="download-and-install-the-azure-ad-connect-health-agent"></a>Скачивание и установка агента Azure AD Connect Health

* Обязательно [выполните требования](how-to-connect-health-agent-install.md#requirements) для Azure AD Connect Health.
* Приступая к работе с Azure AD Connect Health для AD FS
    * [Скачайте агент Azure AD Connect Health для AD FS.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [См. инструкции по установке](#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Приступая к работе с Azure AD Connect Health для синхронизации
    * [Скачайте и установите последнюю версию Azure AD Connect.](https://go.microsoft.com/fwlink/?linkid=615771) Агент Azure AD Connect Health для синхронизации будет установлен вместе с Azure AD Connect (версии 1.0.9125.0 или более поздней).
* Приступая к работе с Azure AD Connect Health для AD DS
    * [Скачайте агент Azure AD Connect Health для AD DS](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [См. инструкции по установке](#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Установка агента Azure AD Connect Health для AD FS

> [!NOTE]
> Сервер AD FS должен отличаться от сервера синхронизации. Не устанавливайте агент AD FS на сервере синхронизации.
>

Перед установкой убедитесь, что имя узла сервера AD FS является уникальным и не существует в службе AD FS.
Дважды щелкните скачанный EXE-файл, чтобы начать установку агента. На первом экране щелкните «Установить».

![Запуск AD FS установки Azure AD Connect Health](./media/how-to-connect-health-agent-install/install1.png)

После завершения установки нажмите кнопку «Настроить сейчас».

![Завершение установки AD FS Azure AD Connect Health](./media/how-to-connect-health-agent-install/install2.png)

Откроется окно PowerShell, и начнется процесс регистрации агента. При появлении запроса войдите с помощью учетной записи Azure AD, которая имеет доступ на выполнение регистрации агента. По умолчанию учетная запись глобального администратора имеет такой доступ.

![Azure AD Connect Health AD FS настроить вход](./media/how-to-connect-health-agent-install/install3.png)

После входа PowerShell продолжит выполнять команду. После завершения можно закрыть PowerShell. Настройка завершена.

На этом этапе службы агента должны запуститься автоматически, благодаря чему агент сможет безопасно загрузить необходимые данные в облачную службу.

Если вы не выполнили все необходимые предварительные требования, которые были описаны в предыдущих разделах, в окне PowerShell будут отображаться предупреждения. Обязательно выполните эти [требования](how-to-connect-health-agent-install.md#requirements), прежде чем устанавливать агент. В качестве примера этих ошибок приведен следующий снимок экрана.

![Azure AD Connect Health AD FS настроить скрипт](./media/how-to-connect-health-agent-install/install4.png)

Чтобы проверить, установлен ли агент, найдите на сервере приведенные ниже службы. Если вы выполнили настройку, эти службы должны работать. Они не запустятся, пока не будет выполнена настройка.

* Служба диагностики AD FS Azure AD Connect Health
* Служба AD FS получения ценной информации из данных о работоспособности Azure AD Connect
* Служба наблюдения AD FS Azure AD Connect Health

![Службы AD FS Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>Включение аудита для AD FS

> [!NOTE]
> Этот раздел относится только к серверам AD FS. Эти действия не нужно выполнять на прокси-серверах веб-приложений.
>

Чтобы функция аналитики по использованию могла осуществлять сбор и анализ данных, агенту Azure AD Connect Health нужна информация из журналов аудита AD FS. По умолчанию эти журналы не включены. Используйте следующие процедуры для включения аудита AD FS и поиска журналов аудита AD FS на серверах AD FS.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Включение аудита для AD FS на Windows Server 2012 R2

1. Из раздела **Диспетчер сервера** на начальном экране или на панели задач откройте диалоговое окно **Локальная политика безопасности**. Затем выберите **Сервис/Локальная политика безопасности**.
2. Перейдите к папке **Параметры безопасности\Локальные политики\Назначение прав пользователей** , а затем дважды щелкните **Создание аудитов безопасности**.
3. На вкладке **Параметр локальной безопасности** убедитесь, что в списке указана учетная запись службы AD FS. Если она отсутствует, щелкните ссылку **Добавление пользователя или группы** , добавьте запись в список и нажмите кнопку **ОК**.
4. Чтобы включить аудит, откройте командную строку с повышенным уровнем привилегий и выполните следующую команду: ```auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable```.
5. Закройте вкладку **Параметры локальной безопасности**.
<br />   -- **Следующие шаги требуются только для основных серверов AD FS.** -- <br />
6. Откройте оснастку **Управление AD FS** (в разделе "Диспетчер сервера" выберите меню "Сервис" и щелкните "Управление AD FS").
7. В области **Действия** щелкните ссылку **Изменить свойства службы федерации**.
8. В диалоговом окне **Свойства службы федерации** перейдите на вкладку **События**.
9. Установите флажки для **успешных и неудачных событий аудита** и нажмите кнопку **ОК**.
10. Подробное ведение журнала можно включить с помощью PowerShell, выполнив команду: ```Set-AdfsProperties -LOGLevel Verbose``` .

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Включение аудита для AD FS на Windows Server 2016

1. Из раздела **Диспетчер сервера** на начальном экране или на панели задач откройте диалоговое окно **Локальная политика безопасности**. Затем выберите **Сервис/Локальная политика безопасности**.
2. Перейдите к папке **Параметры безопасности\Локальные политики\Назначение прав пользователей** , а затем дважды щелкните **Создание аудитов безопасности**.
3. На вкладке **Параметр локальной безопасности** убедитесь, что в списке указана учетная запись службы AD FS. Если она отсутствует, щелкните **Добавить пользователя или группу** , добавьте учетную запись службы AD FS в список и нажмите кнопку **ОК**.
4. Чтобы включить аудит, откройте командную строку с повышенным уровнем привилегий и выполните следующую команду: <code>auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable</code>.
5. Закройте вкладку **Параметры локальной безопасности**.
<br />   -- **Следующие шаги требуются только для основных серверов AD FS.** -- <br />
6. Откройте оснастку **Управление AD FS** (в разделе "Диспетчер сервера" выберите меню "Сервис" и щелкните "Управление AD FS").
7. В области **Действия** щелкните ссылку **Изменить свойства службы федерации**.
8. В диалоговом окне **Свойства службы федерации** перейдите на вкладку **События**.
9. Установите флажки для **успешных и неудачных событий аудита** и нажмите кнопку **ОК**. Этот параметр должен быть включен по умолчанию.
10. Откройте окно PowerShell и выполните следующую команду: ```Set-AdfsProperties -AuditLevel Verbose```.

Обратите внимание, что по умолчанию включен "базовый" уровень аудита. См. дополнительные сведения об [усовершенствовании аудита AD FS в Windows Server 2016](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server).


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Поиск журналов аудита AD FS

1. Откройте средство **просмотра событий**.
2. Перейдите к разделу "Журналы Windows" и выберите пункт **Безопасность**.
3. Справа щелкните **Фильтровать текущие журналы**.
4. В разделе «Источник события» выберите **Аудит AD FS**.

    Краткое [примечание из раздела часто задаваемых вопросов](reference-connect-health-faq.md#operations-questions) о журналах аудита.

![Журналы аудита AD FS](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> Групповая политика может отключить аудит AD FS. Если аудит AD FS отключен, аналитика по использованию в отношении действий входа недоступна. Убедитесь, что групповая политика, которая может отключить аудит AD FS, отсутствует.>
>


## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Установка агента Azure AD Connect Health для синхронизации

В последней сборке Azure AD Connect агент Azure AD Connect Health для синхронизации устанавливается автоматически. Чтобы использовать Azure AD Connect для синхронизации, скачайте и установите последнюю версию Azure AD Connect. Последнюю версию можно загрузить [здесь](https://www.microsoft.com/download/details.aspx?id=47594).

Чтобы проверить, установлен ли агент, найдите на сервере приведенные ниже службы. Если вы выполнили настройку, эти службы должны работать. Они не запустятся, пока не будет выполнена настройка.

* Служба Sync Insights Azure AD Connect Health
* Служба Sync Monitoring Azure AD Connect Health

![Проверка Azure AD Connect Health для синхронизации](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Помните, что для использования службы Azure AD Connect Health требуется Azure AD Premium. Если Azure AD Premium отсутствует, вы не сможете завершить конфигурацию на портале Azure. Дополнительные сведения о требованиях см. [здесь](how-to-connect-health-agent-install.md#requirements).
>
>

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Регистрация агента Azure AD Connect Health для синхронизации вручную

Если после успешной установки Azure AD Connect произойдет сбой регистрации агента Azure AD Connect Health для синхронизации, можно использовать следующую команду PowerShell, чтобы зарегистрировать агент вручную.

> [!IMPORTANT]
> Использование этой команды PowerShell необходимо только в том случае, если после установки Azure AD Connect произошел сбой регистрации агента.
>
>

Следующая команда PowerShell требуется, ТОЛЬКО если регистрация агента работоспособности завершается сбоем даже после успешной установки и настройки Azure AD Connect. Службы Azure AD Connect Health запустятся после того, как агент будет успешно зарегистрирован.

Агент Azure AD Connect Health для синхронизации можно зарегистрировать вручную с помощью следующей команды PowerShell:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Команда принимает следующие параметры:

* AttributeFiltering. Значение $true (по умолчанию) используется, если служба Azure AD Connect не синхронизирует набор атрибутов по умолчанию и была настроена для использования отфильтрованного набора атрибутов. В противном случае используется значение $false.
* StagingMode. Значение $false (по умолчанию) используется, если сервер Azure AD Connect НЕ находится в промежуточном режиме, $true — если сервер настроен на пребывание в промежуточном режиме.

При появлении запроса на аутентификацию следует использовать учетную запись глобального администратора (например, admin@domain.onmicrosoft.com) которая использовалась для настройки Azure AD Connect.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Установка агента Azure AD Connect Health для AD DS

Дважды щелкните скачанный EXE-файл, чтобы начать установку агента. На первом экране щелкните «Установить».

![Агент Azure AD Connect Health для AD DS запуска установки](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

После завершения установки нажмите кнопку «Настроить сейчас».

![Агент Azure AD Connect Health для AD DS завершения установки](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Это приведет к запуску командной строки и оболочки PowerShell, которая выполнит команду Register-AzureADConnectHealthADDSAgent. При появлении запроса на вход войдите в Azure.

![Агент Azure AD Connect Health для AD DS настройки входа](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

После входа PowerShell продолжит выполнять команду. После завершения можно закрыть PowerShell. Настройка завершена.

На этом этапе службы должны запускаться автоматически, а агент будет выполнять мониторинг и сбор данных. Если вы не выполнили все необходимые предварительные требования, которые были описаны в предыдущих разделах, в окне PowerShell будут отображаться предупреждения. Обязательно выполните эти [требования](how-to-connect-health-agent-install.md#requirements), прежде чем устанавливать агент. В качестве примера этих ошибок приведен следующий снимок экрана.

![Агент Azure AD Connect Health для AD DS настройки скрипта](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Чтобы проверить, установлен ли агент, найдите приведенные ниже службы в контроллере домена.

* служба AD DS для Azure AD Connect Health;
* служба наблюдения AD DS Azure AD Connect Health.

Если вы выполнили настройку, эти службы должны работать. Они не запустятся, пока не будет выполнена настройка.

![Агент Azure AD Connect Health для служб AD DS](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quick-agent-installation-in-multiple-servers"></a>Быстрая установка агентов на нескольких серверах

1. Создание учетной записи пользователя в Azure AD с паролем.
2. Назначьте роль **владельца** для этой локальной учетной записи AAD в Azure AD Connect Health через портал. Следуйте инструкциям, которые приводятся [здесь](how-to-connect-health-operations.md#manage-access-with-azure-rbac). Назначьте роль всем экземплярам службы. 
3. Скачайте exe MSI файл на локальном контроллере домена для установки.
4. Выполните следующий скрипт для регистрации. Замените параметры новой созданной учетной записью пользователя и паролем. 

```powershell
AdHealthAddsAgentSetup.exe /quiet
Start-Sleep 30
$userName = "NEWUSER@DOMAIN"
$secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
$myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
 
Register-AzureADConnectHealthADDSAgent -Credential $myCreds

```

1. После этого можно будет удалить доступ к локальной учетной записи, выполнив одно или несколько из следующих действий. 
    * Удаление назначения роли для локальной учетной записи для службы AAD Connect Health
    * Смена пароля для локальной учетной записи. 
    * Отключение локальной учетной записи AAD
    * Удаление локальной учетной записи AAD  

## <a name="agent-registration-using-powershell"></a>Регистрация агента с помощью PowerShell

После установки установочного файла для соответствующего агента в зависимости от роли вы можете зарегистрировать агент с помощью следующих команд PowerShell. Откройте окно PowerShell и выполните соответствующую команду.

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

Эти команды принимают учетные данные в качестве параметра, чтобы завершить регистрацию в неинтерактивном режиме или на основном серверном компьютере.
* Учетные данные можно указать в переменной PowerShell, которая передается в качестве параметра.
* Можно указать любое удостоверение Azure AD, которое имеет доступ на регистрацию агентов и в котором отключена Многофакторная идентификация.
* По умолчанию глобальные администраторы имеют доступ на выполнение регистрации агента. Можно также разрешить выполнять этот шаг другим, менее привилегированным пользователям. Узнайте больше об [управлении доступом на основе ролей в Azure (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac).

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Настройка агентов Azure AD Connect Health для использования HTTP-прокси

Вы можете настроить агенты Azure AD Connect Health на работу с HTTP-прокси.

> [!NOTE]
> * Команда Netsh WinHttp set ProxyServerAddress не поддерживается, потому что для создания веб-запросов агент применяет System.Net, а не службы Microsoft Windows HTTP.
> * Настроенный адрес прокси-сервера HTTP будет использоваться для передачи зашифрованных сообщений HTTPS.
> * Прокси-серверы с проверкой подлинности (с помощью HTTPBasic) не поддерживаются.
>
>

### <a name="change-health-agent-proxy-configuration"></a>Изменение конфигурации прокси-сервера агента работоспособности

Доступны следующие варианты настройки агента Azure AD Connect Health для использования HTTP-прокси.

> [!NOTE]
> Необходимо перезапустить все службы агента Azure AD Connect Health, чтобы обновить параметры прокси-сервера. Выполните следующую команду:<br />
> Restart-Service Азуреадконнексеалс *
>
>

#### <a name="import-existing-proxy-settings"></a>Импорт имеющихся параметров прокси-сервера

##### <a name="import-from-internet-explorer"></a>Импорт из Internet Explorer

Можно импортировать параметры прокси-сервера HTTP из Internet Explorer для использования агентами Azure AD Connect Health. Для этого на каждом сервере, где запущен агент работоспособности, выполните следующую команду PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

##### <a name="import-from-winhttp"></a>Импорт из WinHTTP

Можно импортировать параметры WinHTTP для использования агентами Azure AD Connect Health. Для этого на каждом сервере, где запущен агент работоспособности, выполните следующую команду PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>Задание адресов прокси-сервера вручную

Вы можете вручную задать адрес прокси-сервера, выполнив следующую команду PowerShell на каждом сервере, где запущен агент работоспособности.

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

Пример: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver:443*.

* Значение "address" может быть именем сервера, разрешимым в DNS, или адресом IPv4.
* Значение "port" можно опустить. Если это значение не указано, в качестве порта по умолчанию используется 443.

#### <a name="clear-existing-proxy-configuration"></a>Очистка существующей конфигурации прокси-сервера

Чтобы очистить текущую конфигурацию прокси-сервера, выполните следующую команду:

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>Считывание текущих параметров прокси-сервера

Вы можете считать текущие параметры прокси-сервера с помощью следующей команды:

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Проверка подключения к службе Azure AD Connect Health

Существуют проблемы, при которых агент Azure AD Connect Health может потерять соединение со службой Azure AD Connect Health. К ним относятся проблемы с сетью, с правами доступа и другие причины.

Если агент не может отправить данные службе Azure AD Connect Health более 2 часов, на портале появится следующее оповещение: "Данные службы работоспособности неактуальны". Чтобы проверить, может ли затронутый агент Azure AD Connect Health передавать данные в службу Azure AD Connect Health, выполните следующую команду PowerShell:

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

Параметр role в настоящее время принимает следующие значения:

* ADFS
* Синхронизация
* ADDS

> [!NOTE]
> Чтобы использовать средство подключения, необходимо сначала завершить регистрацию агента. Если зарегистрировать агент не получается, убедитесь, что соблюдены все [требования](how-to-connect-health-agent-install.md#requirements) для Azure AD Connect Health. Эта проверка подключения выполняется по умолчанию во время регистрации агента.
>
>

## <a name="related-links"></a>Связанные ссылки

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Операции Azure AD Connect Health](how-to-connect-health-operations.md)
* [Использование Azure AD Connect Health с AD FS](how-to-connect-health-adfs.md)
* [Использование Azure AD Connect Health для синхронизации](how-to-connect-health-sync.md)
* [Использование Azure AD Connect Health с AD DS](how-to-connect-health-adds.md)
* [Часто задаваемые вопросы об Azure AD Connect Health](reference-connect-health-faq.md)
* [Журнал версий Azure AD Connect Health](reference-connect-health-version-history.md)
