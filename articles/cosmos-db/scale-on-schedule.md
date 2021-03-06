---
title: Масштабирование Azure Cosmos DB по расписанию с помощью таймера функций Azure
description: Узнайте, как масштабировать изменения в пропускной способности Azure Cosmos DB с помощью PowerShell и функций Azure.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: c60f3fc6b4ce4a1aead273fedb81e39de697f576
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339263"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Масштабирование Azure Cosmos DB пропускной способности с помощью триггера таймера для функций Azure
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Производительность учетной записи Azure Cosmos основана на объеме подготовленной пропускной способности, выраженной в единицах запросов в секунду (штук/с). Подготовка выполняется с второй гранулярностью и оплачивается на основе максимального количества единиц запросов в час. Данная модель подготовленной емкости позволяет службе обеспечивать предсказуемую и согласованную пропускную способность, гарантированную низкую задержку и высокий уровень доступности. Большинство рабочих нагрузок этих функций. Однако в средах разработки и тестирования, в которых Azure Cosmos DB используется только в рабочее время, вы можете увеличить пропускную способность в утром и уменьшить масштаб до вечером в конце рабочего времени.

Пропускную способность можно задать с помощью [шаблонов Azure Resource Manager](./templates-samples-sql.md), [Azure CLI](cli-samples.md)и [PowerShell](powershell-samples.md), для учетных записей API Core (SQL) или с помощью языковых пакетов Azure Cosmos DB для конкретного языка. Преимущество использования шаблонов диспетчер ресурсов, Azure CLI или PowerShell заключается в том, что они поддерживают все API-интерфейсы модели Azure Cosmos DB.

## <a name="throughput-scheduler-sample-project"></a>Пример проекта планировщика пропускной способности

Чтобы упростить процесс масштабирования Azure Cosmos DB по расписанию, мы создали пример проекта, именуемый [планировщиком пропускной способности Azure Cosmos](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler). Этот проект является приложением функций Azure с двумя триггерами таймера: "Скалеуптригжер" и "Скаледовнтригжер". Триггеры запускают сценарий PowerShell, который задает пропускную способность для каждого ресурса, как определено в `resources.json` файле каждого триггера. Скалеуптригжер настроен на запуск в течение 8 часов UTC, а Скаледовнтригжер настроен на запуск в 6 РМ UTC, и эти времена можно легко обновить в `function.json` файле для каждого триггера.

Вы можете клонировать этот проект локально, изменить его, указав Azure Cosmos DBные ресурсы для увеличения и уменьшения масштаба, а также расписание выполнения. Позже вы сможете развернуть его в подписке Azure и защитить его с помощью управляемого удостоверения службы с разрешениями [управления доступом на основе ролей Azure (Azure RBAC)](role-based-access-control.md) с ролью "Azure Cosmos DB оператор", чтобы настроить пропускную способность для учетных записей Azure Cosmos.

## <a name="next-steps"></a>Next Steps

- Узнайте больше и загрузите пример из [планировщика пропускной способности Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).