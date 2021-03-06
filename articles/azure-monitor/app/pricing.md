---
title: Управление использованием и затратами для Azure Application Insights | Документация Майкрософт
description: Управление объемом данных телеметрии и контроль расходов в Application Insights.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: DaleKoetke
ms.author: dalek
ms.date: 5/7/2020
ms.reviewer: mbullwin
ms.openlocfilehash: b695205c08f9039fbf91eaeddb7622b784d81d12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400593"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Управление использованием и затратами для Application Insights

> [!NOTE]
> В этой статье описывается, как оценить и контролировать затраты на Application Insights.  В статье [Мониторинг использования и ожидаемых затрат](../platform/usage-estimated-costs.md) описано, как можно просмотреть сведения об использовании и оценить затраты по нескольким функциям мониторинга Azure для разных моделей ценообразования.

Служба Application Insights предоставляет все, что нужно, для контроля доступности, производительности и использования веб-приложений независимо от того, размещены они в Azure или в локальной среде. Application Insights поддерживает популярные языки и платформы, например .NET, Java и Node.js, а также интегрируется с процессами и инструментами DevOps, такими как Azure DevOps, JIRA и PagerDuty. Важно понимать, как определяются затраты на мониторинг приложений. В этой статье мы рассмотрим, какие факторы влияют на затраты на мониторинг приложений, как их можно оперативно отслеживать и контролировать.

Если у вас возникли вопросы о принципах формирования цен на Application Insights, вы можете задать их на [странице вопросов и ответов на сайте Майкрософт](/answers/topics/azure-monitor.html).

## <a name="pricing-model"></a>Модель ценообразования

Цены на [Azure Application Insights][start] определяются по модели **с оплатой по мере использования**, то есть на основе объема полученных данных и более длительного хранения данных. Каждый ресурс Application Insights оплачивается как отдельная служба и включается в счет за подписку Azure. Объем данных оценивается по размеру несжатых пакетов данных в формате JSON, получаемых службой Application Insights от вашего приложения. При использовании [Live Metrics Stream](./live-stream.md) не взимается плата за объем данных.

За [многошаговые веб-тесты](./availability-multistep.md) взимается дополнительная плата. Это веб-тесты, в ходе которых выполняется несколько последовательных действий. За обычную *проверку связи* для одной страницы отдельная плата не взимается. Объем данных телеметрии, переданных в ходе проверки связи или многошагового теста, оплачивается так же, как другие данные телеметрии из вашего приложения.

Функция [Активация оповещений по измерениям пользовательских метрик](./pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) в Application Insights также может приводить к дополнительным затратам, поскольку может создавать дополнительные метрики до агрегирования. Изучите дополнительные сведения [о метриках на основе журналов и предварительно агрегированных метриках в Application Insights](./pre-aggregated-metrics-log-metrics.md), а также о [ценах](https://azure.microsoft.com/pricing/details/monitor/) на пользовательские метрики в Azure Monitor.

### <a name="workspace-based-application-insights"></a>Ресурсы рабочей области Application Insights

Для ресурсов Application Insights, которые отправляют данные в рабочую область Log Analytics ([ресурсы рабочей области Application Insights](create-workspace-resource.md)), выставление счетов за прием и хранение данных выполняется через рабочую область, в которой находятся эти данные Application Insights. Это позволяет клиентам использовать все варианты [модели ценообразования](../platform/manage-cost-storage.md#pricing-model) Log Analytics, включая резервирование мощности, а не только оплату по мере использования. Кроме того, в Log Analytics доступно больше вариантов для хранения данных, в том числе [хранения по типу данных](../platform/manage-cost-storage.md#retention-by-data-type). Типы данных Application Insights в рабочей области могут бесплатно храниться 90 дней. Использование веб-тестов и оповещений по пользовательским измерениям метрик по-прежнему выполняется через Application Insights. Узнайте, как отслеживать затраты на прием и хранение данных в Log Analytics, в разделах [об использовании и оценке затрат](../platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs), [о службе "Управление затратами Azure" и о выставлении счетов ](../platform/manage-cost-storage.md#viewing-log-analytics-usage-on-your-azure-bill), а также [о запросах Log Analytics](#data-volume-for-workspace-based-application-insights-resources). 

## <a name="estimating-the-costs-to-manage-your-application"></a>Оценка затрат на управление приложением

Если вы еще не используете Application Insights, для оценки затрат на Application Insights можно применить [калькулятор цен Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor). Сначала введите Azure Monitor в поле поиска и щелкните отобразившуюся плитку Azure Monitor. Прокрутите страницу вниз до Azure Monitor и выберите Application Insights в раскрывающемся списке типов.  Здесь можно ввести ожидаемый объем данных за месяц (в ГБ), но для этого вам нужно понимать, сколько данных соберет Application Insights при мониторинге приложения.

Существует два подхода к решению этой задачи: использование мониторинга по умолчанию и адаптивной выборки, которая доступна в пакете SDK для ASP.NET, или оценка предполагаемого объема данных на основе информации от других клиентов, использующих похожие системы.

### <a name="data-collection-when-using-sampling"></a>Сбор данных с использованием выборки

При использовании [адаптивной выборки](sampling.md#adaptive-sampling), предоставляемой пакетом SDK для ASP.NET, объем данных автоматически корректируется для соблюдения заданного лимита трафика для мониторинга Application Insights по умолчанию. Если приложение создает малый объем данных телеметрии, например при отладке или в период низкого потребления, обработчик выборки не удаляет элементы, пока объем данных не превышает заданное количество событий в секунду. Для приложения с большим объемом данных, если используется стандартное ограничение в пять событий в секунду, адаптивная выборка принимает ежедневно не более 432 000 событий. Если исходить из типичного среднего размера 1 КБ для одного события, за 31 день календарного месяца накапливается 13,4 ГБ данных телеметрии на каждый узел, где размещается приложение (поскольку выборка выполняется локально для каждого узла). 

Если пакеты SDK не поддерживают адаптивную выборку, примените [выборку при приеме](./sampling.md#ingestion-sampling), которая подразумевает отбрасывание данных при получении в Application Insights на основе заданного процента хранимых данных, или [выборку с фиксированной частотой для веб-сайтов ASP.NET, ASP.NET Core и Java](sampling.md#fixed-rate-sampling). Так вы сможете снизить объем трафика, отправляемого веб-сервером и веб-браузерами.

### <a name="learn-from-what-similar-customers-collect"></a>Узнайте, что собирают клиенты с аналогичными задачами

В калькуляторе цен Azure Monitoring для Application Insights, включив параметр "Оценка объема данных на основе действий приложений" и предоставив сведения о приложении (количество запросов и просмотров страниц в месяц, если вы намерены собирать данные телеметрии на стороне клиента), вы получите в калькуляторе значения медианы и 90-го процентиля по объему данных, собранных в аналогичных приложениях. Оценка выполняется по всему диапазону конфигураций Application Insights (например, для некоторых применяется [выборка](./sampling.md) по умолчанию, а для других не применяется и т. д.), поэтому у вас всегда будет возможность, используя выборку, добиться объема заметно ниже среднего уровня. Но это даст вам отправную точку для понимания того, что получают другие клиенты с похожими задачами.

## <a name="understand-your-usage-and-estimate-costs"></a>Анализ потребления и оценка затрат

В Applicaition Insights легко разобраться с затратами, которые, как правило, основаны на последних шаблонах использования. Чтобы приступить к работе, перейдите на страницу **Данные об использовании и предполагаемые расходы** для ресурса Application Insights на портале Azure.

![Выбор цен](./media/pricing/pricing-001.png)

A. Просмотрите сведения об объеме данных на месяц. К ним относятся все полученные и сохраненные данные (после любой [выборки](./sampling.md) из серверных и клиентских приложений, а также с тестов доступности).  
Б. За [многошаговые веб-тесты](./availability-multistep.md)взимается отдельная плата. (К ним не относятся простые тесты доступности, плата за которые взимается при выставлении счетов за используемый объем данных.)  
В. Просмотр тенденций объемов данных за последний месяц.  
Г. [Выборка](./sampling.md) при приеме данных.
Д. Настройка ежедневного ограничения объема данных.  

(Не забывайте, что все цены на снимках экранов в этой статье приведены только для примера. Текущие цены в валюте вашей страны для выбранного региона вы можете узнать на [странице цен на Application Insights][pricing].)

Для получения более детальных сведений об использовании Application Insights откройте страницу **Метрики**, добавьте метрику "Объем точки данных", а затем выберите *Применить разделение*, чтобы разделить данные по типу элемента телеметрии.

Оплата за использование Application Insights добавляется в счет Azure. Дополнительную информацию о счете за подписку на Azure можно просмотреть в разделе **Управление затратами и выставление счетов** портала Azure или на [портале выставления счетов Azure](https://account.windowsazure.com/Subscriptions).  Дополнительные сведения об использовании этих ресурсов для Application Insights см. [ниже](#viewing-application-insights-usage-on-your-azure-bill). 

![В меню слева выберите "Выставление счетов".](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Использование метрик объема данных
<a id="understanding-ingested-data-volume"></a>

Чтобы лучше понять реальные объемы ваших данных, выберите **Метрики** для ресурса Application Insights и добавьте новую диаграмму. В качестве метрики для диаграммы выберите в разделе **Метрики на основе журнала** параметр **Объем точки данных**. Щелкните **Применить разделение** и выберите группирование по **типу `Telemetryitem`** .

![Использование метрик для просмотра объема данных](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Запросы для получения сведений об объеме данных

Существует два подхода к изучению объемов данных для Application Insights. Первый основан на агрегированных данных из таблицы `systemEvents`, а второй использует свойство `_BilledSize`, которое доступно для каждого полученного события. `systemEvents` не содержит информации о размере данных для [ресурсов Application Insights на основе рабочей области](#data-volume-for-workspace-based-application-insights-resources).

#### <a name="using-aggregated-data-volume-information"></a>Использование агрегированных сведений об объеме данных

Например, в таблице `systemEvents` можно увидеть объем данных, полученных за последние 24 часа, с помощью такого запроса:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Также можно получить диаграмму объемов данных (в байтах) по типам данных за последние 30 дней, выполнив такой запрос:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Обратите внимание, что этот запрос можно использовать в [оповещениях по журналам Azure](../platform/alerts-unified-log.md), чтобы настроить генерацию оповещений по объемам данных.  

Чтобы узнать больше об изменениях в данных телеметрии, можно получить количество событий по типам с помощью такого запроса:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Использование сведений о размере данных для каждого события

Чтобы получить подробные данные об источниках полученных данных, можно использовать свойство `_BilledSize`, которое поступает вместе с каждым полученным событием.

Например, чтобы узнать, какие операции создали наибольший объем данных за последние 30 дней, можно выполнить суммирование по `_BilledSize` для всех событий зависимостей:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

#### <a name="data-volume-for-workspace-based-application-insights-resources"></a>Объем данных для ресурсов рабочей области Application Insights

Чтобы просмотреть тенденции изменения объема данных для всех [ресурсов рабочей области Application Insights](create-workspace-resource.md) в некоторой рабочей области за последнюю неделю, перейдите в эту рабочую область Log Analytics и выполните такой запрос:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d) and TimeGenerated < startofday(now())
| summarize sum(_BilledSize) by _ResourceId, bin(TimeGenerated, 1d)
| render areachart
```

Чтобы получить тенденции изменения объемов данных по типу для конкретного ресурса рабочей области Application Insights, выполните в рабочей области Log Analytics следующий запрос:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d) and TimeGenerated < startofday(now())
| where _ResourceId contains "<myAppInsightsResourceName>"
| summarize sum(_BilledSize) by Type, bin(TimeGenerated, 1d)
| render areachart
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Просмотр данных об использовании Application Insights в счете Azure

В Azure доступно большое количество полезных функций в центре Azure [Управление затратами и выставление счетов](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json). Например, функция "Анализ затрат" позволяет просматривать затраты на ресурсы Azure. Добавление фильтра по типу ресурсов (для Application Insights укажите microsoft.insights/components) позволит вам отслеживать расходы. Затем для параметра "Группировать по" выберите "Категория учета" или "Счетчик".  Для ресурсов Application Insights с действующими тарифными планами основная часть сведений об использовании будет отображаться в категории учета Log Analytics, так как для всех компонентов Azure Monitor используется общая серверная часть сбора журналов. 

Чтобы получить более полное представление об использовании, вы можете [скачать сведения об использовании на портале Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal).
В скачанной электронной таблице вы можете просмотреть сведения об использовании каждого ресурса Azure в день. В этой таблице Excel данные об использовании, связанные с ресурсами Application Insights, можно найти с помощью следующих фильтров: для столбца "Категория учета" нужно выбрать "Application Insights" и "Log Analytics", а для столбца "Идентификатор экземпляра" — значение "содержит microsoft.insights/components".  Большая часть данных об использовании для Application Insights отображается по счетчикам в категории "Счетчик" Log Analytics, так как для всех компонентов Azure Monitor используется общая серверная часть сбора журналов.  В категорию учета Application Insights включаются только ресурсы Application Insights с устаревшими ценовыми категориями и многошаговые веб-тесты.  Данные об использовании отображаются в столбце "Использованное количество", а единица измерения для каждой записи отображается в столбце "Единица измерения".  Дополнительные сведения о счете за использование Microsoft Azure см. в [этой статье](../../cost-management-billing/understand/review-individual-bill.md).

## <a name="managing-your-data-volume"></a>Управление объемом данных

Объем отправляемых данных можно контролировать с помощью следующих методов:

* **Выборка.** Позволяет уменьшить объем данных телеметрии, отправляемых из серверных и клиентских приложений, с минимальным искажением метрик. Это основное средство, с помощью которого можно настроить объем отправляемых данных. Дополнительные сведения см. в статье [Выборка в Application Insights](./sampling.md).

* **Ограничение числа вызовов AJAX.** Вы можете [ограничить число вызовов AJAX, которые могут выполняться](./javascript.md#configuration) на каждом представлении страницы, или отключите формирование отчетов AJAX.

* **Отключение ненужных модулей.** Отключите модули сбора, которые вы не используете, [отредактировав файл ApplicationInsights.config](./configuration-with-applicationinsights-config.md). Например, вы можете решить, что счетчики производительности или данные зависимостей не являются необходимыми.

* **Предварительная агрегация метрик.** Если в ваше приложение включены вызовы к TrackMetric, вы можете снизить объем трафика путем перегрузки, которая будет получать ваши вычисления среднего значения и стандартного отклонения пакета измерений. Кроме того, вы можете использовать [пакет предварительных статистических вычислений](https://www.myget.org/gallery/applicationinsights-sdk-labs).
 
* **Ежедневное ограничение.** При создании ресурса Application Insights на портале Azure устанавливается ограничение, которое составляет 100 ГБ в день. Ограничение по умолчанию при создании ресурса Application Insights в Visual Studio невелико (всего 32,3 МБ в день). Ежедневное ограничение по умолчанию задается для упрощения тестирования. Предполагается, что пользователь повысит ежедневное ограничение перед развертыванием приложения в рабочую среду. 

    Максимальное ограничение составляет 1000 ГБ в день. Вы можете запросить большее ограничение для приложения с большим объемом трафика.
    
    Сообщения с предупреждениями о ежедневном ограничении отправляются в те учетные записи, которые являются членами следующих ролей для ресурса Application Insights: "ServiceAdmin", "AccountAdmin", "CoAdmin", "Owner".

    Будьте внимательны при задании ежедневного ограничения. Следует предполагать, что вы *никогда не достигнете его*. При достижении ежедневного ограничения вы потеряете данные за остаток дня и не сможете наблюдать за приложением. Чтобы изменить ежедневное ограничение, используйте параметр **Ежедневное ограничение объема**. Этот параметр можно настроить в области **Usage and estimated costs** (Данные об использовании и предполагаемые расходы). Это описано более подробно далее в этой статье.
    
    Мы удалили ограничение для некоторых типов подписок с кредитом, который не может использоваться для Application Insights. Ранее, если для подписки была задана предельная сумма расходов, то в колонке ежедневного ограничения отображались инструкции по ее удалению, а также включению возможности повышения этого ограничения свыше 32,3 МБ в день.
    
* **Регулирование:** Ограничивает скорость передачи данных 32 000 событий в секунду на ключ инструментирования, с усреднением за периоды по 1 минуте. Объем данных, отправляемых приложением, оценивается каждую минуту. Если он превышает среднюю минутную частоту, сервер отклоняет часть запросов. Пакет SDK буферизует данные, а затем пытается повторно отправить их. Он распределяет резкое увеличение трафика на несколько минут. Если приложение постоянно отправляет данные с частотой, превышающей частоту регулирования, некоторые данные будут пропущены. (Пакеты SDK для ASP.NET, Java и JavaScript пытаются повторно отправлять данные таким способом; другие пакеты SDK могут просто пропускать отрегулированные данные.) Если произошло регулирование, вы увидите соответствующее предупреждение.

## <a name="manage-your-maximum-daily-data-volume"></a>Управление максимальным ежедневным объемом данных

Можно использовать ежедневное ограничение, чтобы ограничить объем собираемых данных. Тем не менее, если это ограничение достигнуто, теряются все данные телеметрии, отправленные из приложения за остаток дня. *Не рекомендуется* допускать достижения приложением ежедневного ограничения. После достижения ежедневного ограничения невозможно отслеживать работоспособность и производительность приложения.

Вместо ежедневного ограничения объема используйте [выборку](./sampling.md), чтобы настроить объем данных до требуемого уровня. Затем используйте ежедневное ограничение как "крайнюю меру" в случае, если приложение неожиданно начинает отправлять намного большие объемы данных телеметрии.

### <a name="identify-what-daily-data-limit-to-define"></a>Определение ежедневного ограничения по сбору данных

Сведения о тенденциях приема данных и определении ежедневного ограничения для объема см. на странице "Использование и ожидаемые затраты" для службы Application Insights. Тщательно изучите эти сведения, так как вы не сможете контролировать свои ресурсы после достижения предела.

### <a name="set-the-daily-cap"></a>Установка ежедневного ограничения

Чтобы изменить ежедневное ограничение, в разделе **Настройки** ресурса Application Insights на странице **Использование и ожидаемые затраты** выберите **Ежедневное ограничение**.

![Настройка ограничения ежедневного объема данных телеметрии](./media/pricing/pricing-003.png)

Чтобы [изменить ежедневное ограничение с помощью Azure Resource Manager](./powershell.md), нужно изменить свойство `dailyQuota`.  С помощью Azure Resource Manager можно также задать `dailyQuotaResetTime` и `warningThreshold` для ежедневного ограничения.

### <a name="create-alerts-for-the-daily-cap"></a>Генерация оповещений по ежедневному ограничению

Функция "Ежедневное ограничение" Application Insights создает событие в журнале действий Azure, когда объем принятых данных достигает порога предупреждений или ежедневного ограничения.  Вы можете [настроить генерацию оповещений по этим событиям журнала действий](../platform/alerts-activity-log.md#create-with-the-azure-portal). Ниже приведены названия сигналов для этих событий.

* Достигнуто пороговое значение предупреждения для ежедневного ограничения компонента Application Insights

* Достигнуто ежедневное ограничение для компонента Application Insights

## <a name="sampling"></a>Выборка
[выборка](./sampling.md) — это метод снижения скорости отправки данных телеметрии в приложение, сохраняя возможность поиска связанных событий во время поиска по журналу диагностики. Она также позволяет сохранить правильные значения числа событий.

Выборка — это эффективный способ сократить затраты и оставаться в пределах месячной квоты. Алгоритм выборки сохраняет связанные элементы телеметрии, поэтому, например, при использовании поиска можно найти запрос, относящийся к конкретному исключению. Алгоритм также сохраняет правильные количества, чтобы вы видели в обозревателе метрик правильные значения частоты запросов, частоты исключений и других счетчиков.

Существует несколько видов выборки.

* По умолчанию для пакета SDK для ASP.NET используется [адаптивная выборка](./sampling.md). Адаптивная выборка автоматически настраивается в соответствии с объемом данных телеметрии, отправляемых приложением. Она оперирует данными в пакете SDK в вашем веб-приложении, что позволяет уменьшить сетевой трафик телеметрии. 
* *Выборка приема* — это другая функция, которая работает в точке, где данные телеметрии из приложения поступают в службу Application Insights. Выборка приема не влияет на объем данных телеметрии, отправляемых из приложения, но уменьшает объем данных, сохраняемых службой. С помощью этой выборки можно уменьшить квоту, заданную для данных телеметрии, которые поступают из браузеров и других пакетов SDK.

Чтобы настроить выборку приема, перейдите в область **Цены**.

![В диалоговом окне "Quotas and pricing" (Квоты и цены) щелкните элемент "Выборки" и выберите долю выборки.](./media/pricing/pricing-004.png)

> [!WARNING]
> В области **Выборка данных** отражается только значение выборки приема. В ней не отражается частота выборки, которую применяет пакет SDK для Application Insights в приложении. Если по входящей телеметрии уже сделана выборка в пакете SDK, то выборка приема не применяется.
>

Чтобы узнать фактическую частоту выборки (где бы она ни применялась), выполните [запрос Analytics](../log-query/log-query-overview.md). Этот запрос выглядит следующим образом.

```kusto
requests | where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h)
| render areachart
```

Для каждой сохранившейся записи `itemCount` обозначает число исходных записей, которые эта запись представляет. Эта величина равна 1 + число предыдущих удаленных записей.

## <a name="change-the-data-retention-period"></a>Изменение срока хранения данных

По умолчанию для ресурсов Application Insights данные хранятся в течение 90 дней. Для каждого ресурса Application Insights можно выбрать собственный период хранения. Доступны следующие значения периодов хранения: 30, 60, 90, 120, 180, 270, 365, 550 или 730 дней. Дополнительные сведения о ценах на длительное хранение данных см. [здесь](https://azure.microsoft.com/pricing/details/monitor/). 

Чтобы изменить срок хранения, из своего ресурса Application Insights перейдите на страницу **Использование и ожидаемые затраты** и выберите параметр **Хранение данных**.

![Снимок экрана, на котором показано, где можно изменить срок хранения данных.](./media/pricing/pricing-005.png)

Если срок хранения данных снижен, то перед удалением самых старых данных предоставляется льготный период в несколько дней.

Срок хранения данных также можно [задать программным способом с помощью PowerShell](powershell.md#set-the-data-retention), указав параметр `retentionInDays`. Если вы настроили период хранения данных в 30 дней, можете активировать немедленную очистку старых данных с помощью параметра `immediatePurgeDataOn30Days`. Это может быть полезно при некоторых вариантах требований к соответствию. Возможность очистки данных доступна только через Azure Resource Manager и требует особой осторожности в использовании. Время ежедневного сброса ограничения по объему данных можно настроить с помощью Azure Resource Manager, указав параметр `dailyQuotaResetTime`.

## <a name="data-transfer-charges-using-application-insights"></a>Плата за передачу данных при использовании Application Insights

Отправка данных в Application Insights может повлечь за собой плату за пропускную способность при передаче данных. Как описано на [странице цен на пропускную способность Azure](https://azure.microsoft.com/pricing/details/bandwidth/), передача данных между службами Azure, расположенными в двух регионах, оплачивается как передача исходящих данных по стандартной цене. Передача входящих данных не подлежит оплате. Впрочем, эта плата очень мала (несколько %) по сравнению с затратами на прием данных журналов Application Insights. Следовательно, управление затратами на Log Analytics лучше сосредоточить на объеме принимаемых данных. Наши рекомендации по этому вопросу см. [здесь](#managing-your-data-volume).

## <a name="limits-summary"></a>Сводная таблица ограничений

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Отключение ежедневных электронных писем

Чтобы отключить ежедневное ограничение объема электронных писем, в разделе **Настройки** ресурса Application Insights в области **Usage and estimated costs** (Данные об использовании и предполагаемые расходы) выберите **Ежедневное ограничение**. Существуют параметры для отправки писем при достижении ограничения, а также при достижении порога предупреждений. Если вы хотите отключить отправку всех сообщений электронной почты, связанных с ежедневным ограничением объема, снимите флажки с обоих полей.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Устаревшая ценовая категория "Корпоративный" (за узел)

Для ранних пользователей Azure Application Insights по-прежнему существуют две ценовые категории: "Базовый" и "Корпоративный". По умолчанию используется ценовая категория "Базовый", которая соответствует описанию выше. Она включает в себя все возможности категории "Корпоративный", не требуя дополнительных затрат. В категории "Базовый" плата в основном взимается за объем принимаемых данных.

> [!NOTE]
> Эти устаревшие ценовые категории были переименованы. Ценовая категория "Корпоративный" теперь называется **За узел**, а категория "Базовый" называется **За ГБ**. Эти новые имена используются в тексте ниже и на портале Azure.  

В категории "За узел" (ранее — "Корпоративный") оплачивается каждый узел, и каждому узлу выделяется ежедневная квота данных. При использовании ценовой категории "За узел" плата взимается за данные, полученные сверх предоставленной квоты. Если вы используете Operations Management Suite, следует выбрать категорию "За узел".

Текущие цены в валюте вашей страны для выбранного региона вы можете узнать на [странице цен на Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> В апреле 2018 года была [представлена](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) новая модель ценообразования для служб мониторинга Azure. Эта модель использует простой принцип "с оплатой по мере использования" во всем портфеле служб мониторинга. Узнайте больше о [новой модели ценообразования](../platform/usage-estimated-costs.md), о том, как [оценить последствия перехода на новую модель](../platform/usage-estimated-costs.md#understanding-your-azure-monitor-costs), исходя из ваших шаблонов использования, и как [перейти на эту модель](../platform/usage-estimated-costs.md#azure-monitor-pricing-model).

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Категория "За узел" и права подписки Operations Management Suite

Как [недавно было объявлено](/archive/blogs/msoms/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription), клиенты, покупающие Operations Management Suite E1 и Operations Management Suite E2, могут бесплатно получить ценовую категорию Application Insights "За узел" в качестве дополнительного компонента. Каждая приобретенная единица пакета Operations Management Suite E1 и Operations Management Suite E2 предоставляет право на использование одного узла с ценовой категорией Application Insights "За узел". Каждый узел Application Insights включает до 200 МБ ежедневно обрабатываемых данных (отдельно от получаемых данных Log Analytics), которые хранятся в течение 90 дней. Дополнительная плата за это не взимается. Эта категория более подробно описана далее в этой статье.

Так как эта категория доступна только клиентам с подпиской Operations Management Suite, клиенты без такой подписки не будут видеть возможности для выбора этой категории.

> [!NOTE]
> Для получения этой возможности в ваших ресурсах Application Insights должна использоваться ценовая категория "За узел". Эта возможность применяется только к узлам. Ресурсы Application Insights в категории "За ГБ" не получают каких-либо преимуществ. Эта возможность не отображается в предполагаемых затратах в области **Usage and estimated costs** (Данные об использовании и предполагаемые расходы). Кроме того, при переводе подписки на новую модель ценообразования для мониторинга в Azure в апреле 2018 года будет доступна только категория "За ГБ". Перевод подписки на новую модель ценообразования для мониторинга Azure не рекомендуется, если у вас есть подписка Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Как работает ценовая категория "За узел"

* Используя категорию "За узел", вы платите только за количество узлов, с которых данные телеметрии отправляются в приложения.
  * *Узел* — это любой физический компьютер, виртуальная машина или экземпляр роли платформы как услуги, на котором размещено ваше приложение.
  * В число узлов не включаются компьютеры для разработки, клиентские браузеры и мобильные устройства.
  * Если приложение содержит несколько компонентов, отправляющих данные телеметрии, например веб-службу и рабочую роль сервера, такие компоненты учитываются отдельно.
  * Данные [динамического потока метрик](./live-stream.md) не учитываются при расчете стоимости. Сумма платы за подписку зависит от количества узлов, а не от количества приложений. Если вы используете пять узлов, которые отправляют телеметрию для 12 приложений, то плата взимается за пять узлов.
* Несмотря на то что суммы в тарифах указаны за целый месяц, плата взимается только за те часы, в течение которых узел отправлял данные телеметрии из приложения. Сумма почасовой платы вычисляется как указанная в тарифе ежемесячная плата, разделенная на 744 (то есть на количество часов в месяц длительностью 31 день).
* Для каждого обнаруженного узла на день выделяются данные объемом 200 МБ (с детализацией по часам). Выделенные, но не использованные объемы данных не переносятся на следующий день.
  * Если вы используете ценовую категорию "За узел", для каждой подписки ежедневно выделяется определенная квота на объем данных в зависимости от количества узлов, отправляющих телеметрию в ресурсы Application Insights в рамках соответствующей подписки. Например, если вы используете 5 узлов, которые круглые сутки отправляют данные, вы получите совокупную квоту в 1 ГБ на все ресурсы Application Insights в этой подписке. Неважно, какие узлы передают больший объем информации, так как включенные данные распределяются на все узлы. Если в определенный день на ресурсы Application Insights будут переданы данные в объеме, превышающем дневную квоту для этой подписки, будет взиматься плата за каждый гигабайт избыточных данных. 
  * Ежедневная квота на объем передаваемых данных вычисляется в зависимости от количества часов за соответствующий день (в формате UTC), на протяжении которых каждый узел отправлял данные телеметрии. Это количество часов делится на 24 и умножается на 200 МБ. Таким образом, если четыре узла отправляли данные телеметрии в течение 15 часов (из 24), объем данных рассчитывается по следующей формуле: ((4 × 15) / 24) × 200 МБ = 500 МБ. Если за этот день узлы отправят 1 ГБ данных, то, исходя из цены в 2,30 доллара США за каждый гигабайт данных сверх квоты, плата за этот день составит 1,15 доллара США.
  * Ежедневный лимит категории "За узел" не распространяется на приложения, для которых выбрана категория "За ГБ". Неиспользованный лимит на следующий день не переносится.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Примеры для определения количества уникальных узлов

| Сценарий                               | Общее количество узлов за день |
|:---------------------------------------|:----------------:|
| 1 приложение использует 3 экземпляра службы приложений Azure и 1 виртуальный сервер. | 4 |
| 3 приложения работают на 2 виртуальных машинах, ресурсы Application Insights для этих приложений находятся в одной подписке, и для них выбрана категория "За узел". | 2 | 
| Каждое из 4 приложений, ресурсы Application Insights которых находятся в одной подписке, запускает 2 экземпляра на протяжении 16 часов с наименьшей загрузкой и 4 экземпляра на протяжении 8 часов пиковой загрузки. | 13,33 |
| Облачные службы, у которых есть по 1 рабочей роли и 1 веб-роли, каждая их которых выполняется в 2 экземплярах. | 4 | 
| В кластере Azure Service Fabric с 5 узлами работают 50 микрослужб, для каждой из которых запущено по 3 экземпляра | 5|

* Точный подсчет количества узлов зависит от того, какой пакет SDK для Application Insights использует ваше приложение. 
  * В пакете SDK версии 2.2 и более новых версий пакет [SDK для Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) и [веб-пакет SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) Application Insights считают узлами каждый узел приложения. Например, имя компьютера для физического сервера или виртуальной машины или имя экземпляра для облачных служб.  Единственное исключение — приложение, использующее только [.NET Core](https://dotnet.github.io/) и пакет SDK для Core Application Insights. В этом случае для всех узлов регистрируется только один узел, так как имя узла недоступно.
  * В более ранних версиях пакета SDK [веб-пакет SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) ведет себя так же, как и в новых версиях, но пакет [SDK для Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) учитывает только один узел, независимо от количества узлов приложения.
  * Если приложение с помощью пакета SDK задает пользовательское значение для параметра **roleInstance** (Количество экземпляров роли), то по умолчанию это же значение будет использоваться и для определения количества узлов.
  * Если вы используете новую версию пакета SDK для приложения, которое запускается на клиентских компьютерах или мобильных устройствах, может возвращаться большое количество узлов (из-за большого числа клиентских компьютеров или мобильных устройств).

## <a name="automation"></a>Служба автоматизации

Можно создать скрипт для настройки ценовой категории с помощью службы управления ресурсами Azure. [Подробнее](powershell.md#price).

## <a name="next-steps"></a>Дальнейшие действия

* [ресамплинга](./sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ./app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/

