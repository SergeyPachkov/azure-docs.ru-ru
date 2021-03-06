---
title: Модель адаптации службы пиринга Azure
description: Дополнительные сведения о подключении службы пиринга Azure
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871404"
---
# <a name="onboarding-peering-service-model"></a>Модель службы пиринга по подключению

Процесс адаптации службы пиринга состоит из двух моделей, перечисленных ниже.

 - Подключение к службе пиринга для адаптации

 - Сбор данных телеметрии подключения службы пиринга

Планы действий для перечисленных выше моделей описаны ниже.

| **Step** | **Действие**| **Что вы получаете**|
|-----------|---------|---------|---------|
|1|Клиент для предоставления подключения от партнера по подключению (без взаимодействия с Майкрософт). |Поставщик услуг Интернета, который хорошо подключается к корпорации Майкрософт и отвечает техническим требованиям для обеспечения работоспособности и надежного подключения к Майкрософт.  |
|2 (необязательно)|Клиент регистрирует местоположения в портал Azure. Расположение определяется: ISP/ИКСП Name, физическое расположение сайта клиента (уровень состояния), префикс IP-адреса, предоставленный поставщику услуг или корпоративному расположению.  |Данные телеметрии: мониторинг маршрута через Интернет, приоритет трафика от корпорации Майкрософт к ближайшему расположению POP пользователя. |



## <a name="onboarding-peering-service-connection"></a>Подключение к службе пиринга для адаптации

Чтобы подключить подключение службы пиринга, выполните следующие действия.

Обратитесь к партнеру поставщика услуг Интернета (ISP) или к Интернету Exchange (IX), чтобы получить службу пиринга для подключения сети к сети Майкрософт.

Убедитесь, что [поставщики услуг подключения](location-partners.md) взаимосвязаны с корпорацией Майкрософт для службы пиринга. 

## <a name="onboarding-peering-service-connection-telemetry"></a>Сбор данных телеметрии подключения службы пиринга

Клиенты могут использовать данные телеметрии, такие как аналитика маршрутов BGP, для наблюдения за задержкой и производительностью сети при доступе к сети Microsoft. Это можно сделать, зарегистрировав подключение в портал Azure.

Чтобы подключить данные телеметрии подключения службы пиринга, клиент должен зарегистрировать подключение службы пиринга в портал Azure. Сведения о процедуре см. в [портал Azure службе пиринга регистрации](azure-portal.md) .

После этого можно измерять данные телеметрии, ссылаясь на [эту ссылку](measure-connection-telemetry.md).

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать пошаговые инструкции по регистрации подключения службы пиринга, см. раздел [Регистрация службы пиринга — портал Azure](azure-portal.md).

Дополнительные сведения об измерении телеметрии подключения см. в разделе [мера подключения телеметрии](measure-connection-telemetry.md).
