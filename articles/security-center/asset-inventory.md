---
title: Инвентаризация активов центра безопасности Azure
description: Узнайте о средствах управления активами в центре безопасности Azure, обеспечивая полную видимость всех отслеживаемых ресурсов центра безопасности.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 09/22/2020
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: d15d73b0f2b87b8e6f66c7bd4e7fb34f6b06e1a0
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341929"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>Исследование ресурсов и управление ими с помощью инвентаризации ресурсов и средств управления

Страница инвентаризации ресурсов в Центре безопасности Azure позволяет просматривать состояние безопасности всех ресурсов, подключенных к Центру безопасности. 

Центр безопасности периодически анализирует состояние безопасности ресурсов Azure, чтобы выявить потенциальные уязвимости. Затем он предоставляет рекомендации по устранению этих уязвимостей.

Если для каких-то ресурсов есть необработанные рекомендации, они будут отображаться в данных инвентаризации.

Используйте это представление и его фильтры для решения следующих вопросов:

- Какие из моих подписок с включенным защитником Azure имеют непревзойденные рекомендации?
- На каких компьютерах с тегом "Production" отсутствует Log Analytics агент?
- На скольких компьютерах, помеченных конкретным тегом, имеются необработанные рекомендации?
- Сколько ресурсов в определенной группе ресурсов имеет результаты безопасности от службы оценки уязвимостей?

Возможности управления ресурсами для этого средства значительно увеличиваются и продолжают расти. 

> [!TIP]
> Рекомендации по безопасности на странице инвентаризации активов те же, что и на странице **рекомендации** , но здесь они отображаются в соответствии с затронутым ресурсом. Сведения о том, как разрешить рекомендации, см. [в статье реализация рекомендаций по безопасности в центре безопасности Azure](security-center-recommendations.md).


## <a name="availability"></a>Доступность

|Аспект|Сведения|
|----|:----|
|Состояние выпуска:|Общедоступная версия (GA)|
|Цены|Free|
|Требуемые роли и разрешения|все пользователи|
|Облако.|![Да](./media/icons/yes-icon.png) Коммерческие облака<br>![Нет](./media/icons/no-icon.png) Национальные и независимые (US Gov, China Gov, другие правительственные облака)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Каковы основные возможности инвентаризации активов?

На странице «Инвентаризация» представлены следующие средства:

- **Сводки** — перед определением каких-либо фильтров в верхней части представления инвентаризации отображается заметная полоса значений:

    - **Всего ресурсов**: общее число ресурсов, подключенных к центру безопасности.
    - **Неработоспособные ресурсы**: ресурсы с активными рекомендациями по безопасности. [Дополнительные сведения о рекомендациях по безопасности](security-center-recommendations.md).
    - **Неотслеживаемые ресурсы**: ресурсы с проблемами мониторинга агентов — они имеют развернутый агент log Analytics, но агент не отправляет данные или не имеет других проблем с работоспособностью.

- **Фильтры** . несколько фильтров в верхней части страницы позволяют быстро уточнить список ресурсов в соответствии с вопросом, на который вы пытаетесь ответить. Например, если вы хотите ответить на вопрос о том, *что у моих компьютеров с тегом "Production" отсутствует агент log Analytics?* фильтр **мониторинга агента** можно объединить с фильтром **тегов** , как показано на следующем рисунке:

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="Фильтрация рабочих ресурсов, которые не отслеживаются":::

    После применения фильтров сводные значения обновляются для связи с результатами запроса. 

- **Параметры экспорта** — Инвентаризация предоставляет возможность экспортировать результаты выбранных параметров фильтра в CSV-файл. Кроме того, вы можете экспортировать сам запрос в обозреватель графа ресурсов Azure для дальнейшего уточнения, сохранения или изменения запроса ККЛ.

    ![Параметры экспорта запасов](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > Документация по ККЛ предоставляет базу данных с некоторыми примерами данных, а также некоторые простые запросы, позволяющие получить «впечатление» для языка. [Дополнительные сведения см. в этом руководстве по ККЛ](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

- **Параметры управления активами** — Инвентаризация позволяет выполнять сложные запросы на обнаружение. Когда вы нашли ресурсы, которые соответствуют запросам, Инвентаризация предоставляет следующие сочетания клавиш для таких операций, как:

    - Назначение тегов фильтруемым ресурсам. Установите флажки рядом с ресурсами, которые нужно пометить.
    - Подключение новых серверов к центру безопасности — используйте кнопку Добавить на панели инструментов **серверы, не относящиеся к Azure** .
    - Автоматизируйте рабочие нагрузки с Azure Logic Apps — используйте кнопку **триггер приложения логики** для запуска приложения логики на одном или нескольких ресурсах. Приложения логики должны быть подготовлены заранее и принимать соответствующий тип триггера (HTTP-запрос). Дополнительные [сведения о Logic Apps](../logic-apps/logic-apps-overview.md).


## <a name="how-does-asset-inventory-work"></a>Как работает Инвентаризация активов?

Инвентаризация активов использует [граф ресурсов Azure (ARG)](../governance/resource-graph/index.yml), службу Azure, которая предоставляет возможность запрашивать данные о безопасности в нескольких подписках в центре безопасности.

АРГУМЕНТ служит для эффективного исследования ресурсов с возможностью выполнения запросов в масштабе.

С помощью [языка запросов Kusto (ККЛ)](/azure/data-explorer/kusto/query/)Инвентаризация активов может быстро создавать подробные сведения, перекрестно ссылающиеся на ASC-данные с другими свойствами ресурсов.


## <a name="how-to-use-asset-inventory"></a>Использование инвентаризации активов

1. На боковой панели центра безопасности выберите **Инвентаризация**.

1. Используйте поле **Фильтровать по имени** , чтобы отобразить конкретный ресурс, или используйте фильтры, как описано ниже.

1. Выберите соответствующие параметры в фильтрах, чтобы создать конкретный запрос, который необходимо выполнить.

    :::image type="content" source="./media/asset-inventory/inventory-filters.png" alt-text="Фильтрация рабочих ресурсов, которые не отслеживаются" lightbox="./media/asset-inventory/inventory-filters.png":::

    По умолчанию ресурсы сортируются по количеству активных рекомендаций по безопасности.

    > [!IMPORTANT]
    > Параметры в каждом фильтре относятся к ресурсам в выбранных подписках **и** выбранным в других фильтрах.
    >
    > Например, если вы выбрали только одну подписку, а у подписки нет ресурсов с необработанными рекомендациями по безопасности (0 неработоспособных ресурсов), фильтр **рекомендаций** не будет иметь параметров. 

1. Чтобы использовать **Результаты безопасности, содержащие** фильтр, введите бесплатный текст из идентификатора, проверки безопасности или CVE имя уязвимости, в которой можно найти фильтр для затронутых ресурсов:

    ![Фильтр "результаты безопасности содержат"](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > **Фильтры с** результатами **безопасности содержат** только одно значение. Чтобы выполнить фильтрацию по нескольким, используйте **Добавление фильтров**.

1. Чтобы использовать фильтр **защитника Azure** , выберите один или несколько параметров (OFF, on или partial):

    - **Off** — ресурсы, не защищенные планом защитника Azure. Вы можете щелкнуть правой кнопкой мыши любую из них и обновить их:

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="Фильтрация рабочих ресурсов, которые не отслеживаются" lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - **В** ресурсах, защищенных планом защитника Azure;
    - **Частично** . Это относится к **подпискам** , в которых отключены некоторые планы защитника Azure. Например, в следующей подписке отключены пять планов защитника Azure. 

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="Фильтрация рабочих ресурсов, которые не отслеживаются":::

1. Чтобы проанализировать результаты запроса, выберите интересующие вас ресурсы.

1. Чтобы просмотреть текущие выбранные параметры фильтра в виде запроса в обозревателе графа ресурсов, выберите **вид в обозревателе ресурсов**.

    ![Запрос на инвентаризацию в ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Запуск ранее определенного приложения логики с помощью 

1. Если вы определили некоторые фильтры и открыли страницу, центр безопасности не обновит результаты автоматически. Любые изменения в ресурсах не будут влиять на отображаемые результаты, если вы не перезагрузите страницу вручную или выберите **Обновить**.


## <a name="faq---inventory"></a>Вопросы и ответы — Инвентаризация

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Почему не отображаются все подписки, компьютеры, учетные записи хранения и т. д.?

В представлении "Инвентаризация" перечислены ресурсы центра безопасности, подключенные с точки зрения Cloud Security Management (КСПМ). Фильтры не возвращают каждый ресурс в вашей среде; только те, у которых есть необработанные (или "активные") рекомендации. 

Например, на следующем снимке экрана показан пользователь с доступом к подпискам 38, но в настоящее время есть рекомендации только по 10. Поэтому при фильтрации по **типу ресурса = подписки**в инвентаризации отображаются только 10 подписок с активными рекомендациями.

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="Фильтрация рабочих ресурсов, которые не отслеживаются":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-azure-defender-or-agent-monitoring-columns"></a>Почему некоторые ресурсы показывают пустые значения в столбцах "защитник Azure" или "Мониторинг агента"?

Не все отслеживаемые ресурсы центра безопасности имеют агенты. Например, учетные записи хранения Azure или ресурсы PaaS, такие как диски, Logic Apps, Data Lakeный анализ и концентратор событий.

Если цены или мониторинг агентов не относятся к ресурсу, в этих столбцах инвентаризации ничего не отображается.

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="Фильтрация рабочих ресурсов, которые не отслеживаются":::

## <a name="next-steps"></a>Дальнейшие шаги

В этой статье описывается страница "Инвентаризация активов" центра безопасности Azure.

Дополнительные сведения о связанных средствах см. на следующих страницах:

- [Граф ресурсов Azure (ARG)](../governance/resource-graph/index.yml)
- [Язык запросов Kusto (KQL)](/azure/data-explorer/kusto/query/)