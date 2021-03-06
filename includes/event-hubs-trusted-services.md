---
title: включить файл
description: включить файл
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d828a0c3eb2582a833ee8ad07bdf4f18002c9dca
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427201"
---
## <a name="trusted-microsoft-services"></a>Доверенные службы Майкрософт
При включении параметра **разрешить доверенным службам Майкрософт обходить этот параметр брандмауэра** доступ к ресурсам концентраторов событий предоставляется следующим службам.

| Доверенная служба | Поддерживаемые сценарии использования | 
| --------------- | ------------------------- | 
| Сетка событий Azure | Разрешает службе "Сетка событий Azure" отправку событий в концентраторы событий в пространстве имен концентраторов событий. Также необходимо выполнить следующие действия. <ul><li>Включение назначенного системой удостоверения для раздела или домена</li><li>Добавление удостоверения в роль отправителя данных концентраторов событий Azure в пространстве имен концентраторов событий</li><li>Затем настройте подписку на события, которая использует концентратор событий в качестве конечной точки для использования назначенного системой удостоверения.</li></ul> <p>Дополнительные сведения см. в статье о [доставке событий с помощью управляемого удостоверения](../articles/event-grid/managed-service-identity.md) .</p>|
| Azure Monitor (параметры диагностики) | Позволяет Azure Monitor отправить диагностические данные в концентраторы событий в пространстве имен концентраторов событий. |
| Azure Stream Analytics | Позволяет Azure Stream Analyticsному заданию считывать данные из ([ввода](../articles/stream-analytics/stream-analytics-add-inputs.md)) или записывать их в концентраторы событий ([Output](../articles/stream-analytics/event-hubs-output.md)) в пространстве имен концентраторов событий. |
| Центр Интернета вещей Azure | Разрешает центру Интернета вещей отправку сообщений в концентраторы событий в пространстве имен концентратора событий. Также необходимо выполнить следующие действия. <ul><li>Включение назначенного системой удостоверения для центра Интернета вещей</li><li>Добавьте удостоверение в роль отправителя данных концентраторов событий Azure в пространстве имен концентраторов событий.</li><li>Затем настройте центр Интернета вещей, который использует концентратор событий в качестве пользовательской конечной точки для использования проверки подлинности на основе удостоверений.</li></ul>
