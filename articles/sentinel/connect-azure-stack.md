---
title: Подключение виртуальных машин Azure Stack HUB к Azure Sentinel | Документация Майкрософт
description: В этой статье показано, как подготавливать расширение виртуальной машины управления Azure Monitor, Update и Configuration Management на виртуальных машинах Azure Stack HUB и запускать их мониторинг с помощью Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 9ff70e7c05ca8de49f560fba3d59f0609785b8c4
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636781"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-azure-sentinel"></a>Подключение виртуальных машин Azure Stack HUB к Azure Sentinel




С помощью Sentinel Azure можно отслеживать виртуальные машины, работающие в Azure и концентраторе Azure Stack, в одном месте. Чтобы подключить Azure Stack машины к Azure Sentinel, сначала необходимо добавить расширение виртуальной машины к существующим виртуальным машинам центра Azure Stack. 

После подключения к Azure Stackным машинам концентраторов выберите из коллекции панелей мониторинга, в которой отображаются данные на основе данных. Эти панели мониторинга можно легко настроить в своих нуждах.



## <a name="add-the-virtual-machine-extension"></a>Добавление расширения виртуальной машины 

Добавьте расширение виртуальной машины **Azure Monitor, Update и Configuration Management** в виртуальные машины, работающие в центре Azure Stack. 

1. На новой вкладке браузера Войдите на [портал центра Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Перейдите на страницу **виртуальные машины** , выберите виртуальную машину, которую необходимо защитить с помощью Azure Sentinel. Сведения о том, как создать виртуальную машину в центре Azure Stack, см. в статье Создание виртуальной машины [Windows Server с помощью портала Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) или [Создание виртуальной машины сервера Linux с помощью портала концентратора Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Выберите **Расширения**. Появится список расширений виртуальных машин, которые установлены на этой виртуальной машине.
4. Откройте вкладку **Добавить**. Откроется колонка меню **Новый ресурс** и отобразится список доступных расширений виртуальных машин. 
5. Выберите расширение **управления Azure Monitor, обновление и настройка** и нажмите кнопку **создать**. Откроется окно **Установка расширения** конфигурации.

   ![Параметры управления Azure Monitor, обновления и конфигурации](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Если вы не видите расширение **управления Azure Monitor, обновления и настройки** , указанное в Marketplace, обратитесь к оператору центра Azure Stack, чтобы сделать его доступным.

6. В меню Sentinel Azure выберите **параметры рабочей области** , а затем — **Дополнительно** и скопируйте **идентификатор рабочей области** и **ключ рабочей области (первичный ключ)**. 
1. В окне **Установка расширения** центра Azure Stack вставьте их в указанные поля и нажмите кнопку **ОК**.
1. После завершения установки расширения его состояние отображается как **Подготовка выполнена**. Для отображения виртуальной машины на портале Sentinel Azure может потребоваться до одного часа.

Дополнительные сведения об установке и настройке агента для Windows см. в разделе [Подключение компьютеров Windows](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).

Сведения об устранении неполадок с агентами Linux см. в статье [Устранение неполадок с агентом Log Analytics для Linux](../azure-monitor/platform/agent-linux-troubleshoot.md).

На портале Sentinel Azure в Azure в разделе **виртуальные машины** представлены общие сведения обо всех виртуальных машинах и компьютерах, а также их состояние. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Если расширение больше не требуется, его можно удалить с виртуальной машины с помощью портала концентратора Azure Stack.

Чтобы удалить расширение, сделайте следующее:

1. Откройте **портал центра Azure Stack**.
2. Перейдите к странице **Виртуальные машины** , выберите виртуальную машину, с которой нужно удалить расширение.
3. Выберите **Расширения** и щелкните расширение **Microsoft.EnterpriseCloud.Monitoring**.
4. Щелкните **Удалить** и подтвердите выбор.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- Узнайте, как выполнять потоковую передачу из [устройств Common Event Format](connect-common-event-format.md) в Azure Sentinel.
