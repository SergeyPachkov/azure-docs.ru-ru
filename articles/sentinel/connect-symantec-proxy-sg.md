---
title: Подключение данных Symantec Проксисг к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Symantec Проксисг к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: fe49481357b542ce52de5fc9dab3059cb584c229
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083911"
---
# <a name="connect-your-symantec-proxysg-to-azure-sentinel"></a>Подключение Symantec Проксисг к Azure Sentinel

> [!IMPORTANT]
> Соединитель данных Symantec Проксисг в Azure Sentinel сейчас находится в общедоступной предварительной версии.
> Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этой статье объясняется, как подключить устройство [Symantec проксисг](https://www.broadcom.com/products/cyber-security/network/gateway/proxy-sg-and-advanced-secure-gateway) к Azure Sentinel. Соединитель данных Symantec Проксисг позволяет легко подключать журналы Symantec Проксисг с помощью Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследование. При интеграции между Symantec Проксисг и Sentinel a Azure используется системный журнал.

> [!NOTE]
> Данные будут храниться в географическом расположении рабочей области, на которой вы используете метку Azure.

## <a name="forward-symantec-proxysg-logs-to-the-syslog-agent"></a>Пересылка журналов Symantec Проксисг в агент системного журнала  

Настройте Symantec Проксисг для пересылки сообщений Syslog в рабочую область Azure с помощью агента системного журнала.

1. На портале Sentinel Azure щелкните **соединители данных** и выберите **Symantec проксисг** Connector.

1. Выберите **открыть страницу соединителя**.

1. Следуйте инструкциям на странице **Symantec проксисг** .

## <a name="find-your-data"></a>Поиск данных

После установки успешного подключения данные отображаются в Log Analytics в разделе syslog.

## <a name="validate-connectivity"></a>Проверка подключения

Если журналы начнут появляться в Log Analytics, это может занять до 20 минут. 

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как подключить Symantec Проксисг к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.

- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.