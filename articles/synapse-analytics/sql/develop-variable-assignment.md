---
title: Назначение переменных с помощью синапсе SQL
description: В этой статье вы найдете советы по назначению переменных T-SQL с помощью синапсе SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: abc0a9f6fa6baefc7cc4b29c84ff179f0851dc30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90028922"
---
# <a name="assign-variables-with-synapse-sql"></a>Назначение переменных с помощью синапсе SQL

В этой статье вы найдете советы по назначению переменных T-SQL с помощью синапсе SQL.

## <a name="set-variables-with-declare"></a>Задание переменных с помощью DECLARE

Переменные в синапсе SQL задаются с помощью `DECLARE` инструкции или `SET` инструкции. Инициализация переменных с помощью DECLARE является одним из наиболее гибких способов установки значения переменной в синапсе SQL.

```sql
DECLARE @v  int = 0
;
```

С помощью DECLARE можно задать одновременно несколько переменных. Нельзя использовать SELECT или UPDATE для следующих задач:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Нельзя инициализировать и использовать переменную в одной и той же инструкции DECLARE. Следующий пример не допускается, так как * \@ P1* инициализирован и используется в одной и той же инструкции DECLARE. Следующий пример приводит к возникновению ошибки.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Установка значений с помощью SET

SET — это очень распространенный метод задания одной переменной.

Ниже перечислены допустимые способы задания значения с помощью инструкции SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

С помощью SET можно одновременно задать только одну переменную. Тем не менее допускаются составные операторы.

## <a name="limitations"></a>Ограничения

Нельзя использовать UPDATE для назначения переменных.

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные советы по разработке см. в статье [Общие сведения о разработке СИНАПСЕ SQL](develop-overview.md) .
