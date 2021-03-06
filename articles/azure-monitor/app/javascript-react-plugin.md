---
title: Подключаемый модуль реагирования для Application Insights SDK JavaScript
description: Установка и использование подключаемого модуля реагирования для Application Insights пакета SDK для JavaScript.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3a11f77384c520bed9824841269be4ad998adba4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056206"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Подключаемый модуль реагирования для Application Insights SDK JavaScript

Подключаемый модуль, реагирующий на Application Insights SDK для JavaScript, включает:

- Отслеживание изменений маршрута
- Статистика использования компонентов для реагирования

## <a name="getting-started"></a>Начало работы

Установить пакет NPM:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Основное использование

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>Конфигурация

| Имя    | По умолчанию | Описание                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | null    | Отреагировать на журнал маршрутизатора. Дополнительные сведения см. в [документации по пакету реагирования маршрутизатора](https://reactrouter.com/web/api/history). Сведения о том, как получить доступ к объекту журнала за пределами компонентов, см. в разделе [часто задаваемые вопросы о маршрутизаторе](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>Отслеживание использования компонентов отклика

Для инструментирования различных отслеживаний использования компонентов отклика примените `withAITracking` функцию компонента более высокого порядка.

Оно будет измерять время от `ComponentDidMount` события через `ComponentWillUnmount` событие. Однако, чтобы сделать это более точным, будет вычтено время бездействия пользователя `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Чтобы увидеть эту метрику в портал Azure необходимо перейти к ресурсу Application Insights, перейдите на вкладку "метрики" и настройте пустые диаграммы для вывода имени пользовательской метрики "отреагировать на время отклика компонента (секунды)", выберите агрегирование (SUM, AVG и т. д.) метрики и примените Split — "имя компонента".

![Снимок экрана с диаграммой, отображающей пользовательскую метрику "отклики на время обработки компонента" (в секундах) "разделение по" имени компонента "](./media/javascript-react-plugin/chart.png)

Можно также выполнять пользовательские запросы, чтобы разделить Application Insights данные для создания отчетов и визуализаций в соответствии с вашими требованиями. В портал Azure перейдите к ресурсу Application Insights, выберите "аналитика" в верхнем меню вкладки "Обзор" и выполните запрос.

![Снимок экрана результатов пользовательского запроса метрик.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Чтобы новые пользовательские метрики отображались на портале Azure, может потребоваться до 10 минут.

## <a name="sample-app"></a>Пример приложения

Ознакомьтесь с [демонстрацией Application Insights реагировать](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о пакете SDK для JavaScript см. в [документации по Application Insights JavaScript SDK](javascript.md).
- Дополнительные сведения о языке запросов Kusto и запросе данных в Log Analytics см. в разделе [Общие сведения о запросе журнала](../../azure-monitor/log-query/log-query-overview.md).
