---
title: Подключение данных Zscaler к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Zscaler к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: ef6677b6f1103c26bd719a3585800765a029f7fb
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056844"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Подключение Zscaler Internet Access к Azure Sentinel

В этой статье объясняется, как подключить устройство Zscaler Internet Access к Azure Sentinel. Соединитель данных Zscaler позволяет легко подключать журналы Zscalerного доступа к Интернету (Зиа) с помощью Sentinel Azure, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследование. Использование Zscaler в Azure Sentinel предоставит вам более подробные сведения об использовании Интернета в вашей организации и улучшит возможности ее работы. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Настройка Zscaler для отправки сообщений CEF

1. На устройстве Zscaler необходимо задать эти значения, чтобы устройство отправляло необходимые журналы в нужном формате в агент syslog-агента Azure на основе агента Log Analytics. Вы можете изменить эти параметры на своем устройстве при условии, что они также будут изменены в управляющей программе syslog в агенте Sentinel Azure.
    - Протокол = TCP
    - Порт = 514
    - Format = CEF
    - IP-адрес. не забудьте отправить сообщения CEF по IP-адресу виртуальной машины, выделенной для этой цели.
 Дополнительные сведения см. в разделе [Zscaler и Azure Sentinel Deployment Guide](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Это решение поддерживает syslog RFC 3164 или RFC 5424.


1. Чтобы использовать соответствующую схему в Log Analytics для событий CEF, выполните поиск по запросу `CommonSecurityLog` .
1. Перейдите к процедуре [Шаг 3. Проверка подключения](connect-cef-verify.md).


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить Zscaler Internet Access к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.


