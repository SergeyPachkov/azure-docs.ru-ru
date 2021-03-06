---
title: Учебник. Настройка автомасштабирования приложений для микрослужб
description: В этой статье описывается, как настроить автомасштабирование для приложений с помощью портала Microsoft Azure или Azure CLI.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/22/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 6023d1ebd27ebaccacfce85ce0f49b7ef87e17ad
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742661"
---
# <a name="tutorial-set-up-autoscale-for-microservice-applications"></a>Руководство по Настройка автомасштабирования для приложений, состоящих из микрослужб

**Эта статья применима к:** ✔️ Java ✔️ C#

Автомасштабирование — это встроенная функция Azure Spring Cloud, которая обеспечивает оптимальную работу приложений для микрослужб при изменении нагрузки. Например, при изменении числа виртуальных ЦП, памяти и экземпляров приложений. В этой статье описывается, как настроить автомасштабирование для приложений с помощью портала Microsoft Azure или Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этих процедур требуется:

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
* Развернутый экземпляр службы Azure Spring Cloud. Чтобы приступить к работе, следуйте инструкциям из статьи [Краткое руководство. Запуск приложения Java Spring с помощью Azure CLI](./spring-cloud-quickstart.md).
* По крайней мере одно приложение, ранее созданное в этом экземпляре службы.

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Переход на страницу масштабирования на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Перейдите на страницу **Обзор** Azure Spring Cloud.
3. Выберите группу ресурсов, которая содержит службу.
4. В разделе **Параметры** выберите вкладку **Приложения** в меню в области навигации слева.
5. Выберите приложение, для которого необходимо настроить автомасштабирование. В этом примере выберите приложение **demo** . После этого отобразится страница **обзора** приложения.
6. В разделе **Параметры** выберите вкладку **Горизонтальное масштабирование** в меню в области навигации слева.
7. Выберите развертывание, для которого необходимо настроить автомасштабирование. Вы увидите параметры для автомасштабирования, показанные в следующем разделе.


![Меню автомасштабирования](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Настройка параметров автомасштабирования для приложения на портале Azure

Существует два параметра для управления потребностями автомасштабирования:

* Ручное масштабирование. Поддерживает фиксированное число экземпляров. На уровне "Стандартный" можно горизонтально увеличивать масштаб не более чем до 500 экземпляров. Это значение изменяет количество отдельных работающих экземпляров приложений для микрослужб.
* Пользовательское автомасштабирование. Масштабирование по любому расписанию и на основе метрик.

На портале Azure выберите способ масштабирования.  На следующем рисунке показан параметр **Пользовательское автомасштабирование** и настройки режима.

![Пользовательское автомасштабирование](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Настройка параметров автомасштабирования для приложения в Azure CLI
Вы также можете задавать режимы автомасштабирования с помощью Azure CLI.  Следующие команды создают конфигурацию автомасштабирования и правило автомасштабирования.

* Создание параметров автомасштабирования
  ```
  az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
  ```
* Создание правила автомасштабирования
  ```
  az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
  ```

## <a name="upgrade-to-the-standard-tier"></a>Повышение ценовой категории до уровня "Стандартный"

Если вы используете уровень "Базовый" и одно или несколько таких ограничений препятствуют вашей работе, можете перейти на уровень "Стандартный". Для этого перейдите в меню **Ценовая категория** , выберите столбец уровня *Стандартный* и нажмите кнопку **Обновить** .

## <a name="next-steps"></a>Дальнейшие действия

* [Общие сведения об автомасштабировании в Microsoft Azure](../azure-monitor/platform/autoscale-overview.md)
* [Автомасштабирование Azure Monitor](/cli/azure/monitor/autoscale?preserve-view=true&view=azure-cli-latest)
