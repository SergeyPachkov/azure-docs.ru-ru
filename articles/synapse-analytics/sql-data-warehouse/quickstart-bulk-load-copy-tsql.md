---
title: Краткое руководство. Выполнение массовой загрузки данных с помощью одной инструкции T-SQL
description: Массовая загрузка данных с помощью инструкции COPY
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 06/18/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: f82bedc6ef638714b2641003e8274c2024a86c2e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "85213012"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Краткое руководство. Массовая загрузка данных с помощью инструкции COPY

В этом кратком руководстве вы выполните массовую загрузку данных в пул SQL с помощью простой и гибкой [инструкции COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) для приема данных с высокой пропускной способностью. Инструкция COPY является рекомендуемой служебной программой загрузки, поскольку она позволяет легко и гибко загружать данные, предоставляя им функциональные возможности, которые позволяют:

- Выполнять загрузку пользователям с более низкими привилегиями, без ограничения разрешений на управление хранилищем данных
- Использовать только одну инструкцию T-SQL без необходимости создавать дополнительные объекты базы данных.
- Использовать более детализированную модель разрешений без предоставления ключей учетной записи хранения с помощью подписанных URL-адресов (SAS).
- Указать другую учетную запись хранения для расположения ERRORFILE (REJECTED_ROW_LOCATION).
- Настроить значения по умолчанию для каждого целевого столбца и указать поля исходных данных для загрузки в определенные целевые столбцы.
- Указать пользовательский символ конца строки для CSV-файлов.
- Отменить разделители строк, полей и строк для CSV-файлов.
- Использовать форматы даты SQL Server для CSV-файлов.
- Указать подстановочные знаки и несколько файлов в пути места хранения.

## <a name="prerequisites"></a>Предварительные требования

В этом кратком руководстве предполагается, что у вас уже есть пул SQL. Если пул SQL не создан, используйте краткое руководство [Создание пула SQL Synapse и отправка в него запросов, используя портал Azure](create-data-warehouse-portal.md).

## <a name="set-up-the-required-permissions"></a>Настройка необходимых разрешений

```sql
-- List the permissions for your user
select  princ.name
,       princ.type_desc
,       perm.permission_name
,       perm.state_desc
,       perm.class_desc
,       object_name(perm.major_id)
from    sys.database_principals princ
left join
        sys.database_permissions perm
on      perm.grantee_principal_id = princ.principal_id
where name = '<yourusername>';

--Make sure your user has the permissions to CREATE tables in the [dbo] schema
GRANT CREATE TABLE TO <yourusername>;
GRANT ALTER ON SCHEMA::dbo TO <yourusername>;

--Make sure your user has ADMINISTER DATABASE BULK OPERATIONS permissions
GRANT ADMINISTER DATABASE BULK OPERATIONS TO <yourusername>

--Make sure your user has INSERT permissions on the target table
GRANT INSERT ON <yourtable> TO <yourusername>

```

## <a name="create-the-target-table"></a>Создание целевой таблицы

В этом примере мы будем загружать данные из базы данных такси в Нью-Йорке. Мы загрузим таблицу под названием "Поездка", которая представляет собой поездки на такси, осуществленные в течение одного года. Для создания таблицы выполните следующие действия.

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>Выполнение инструкции COPY

Выполните следующую инструкцию COPY, которая будет загружать данные из учетной записи хранилища BLOB-объектов Azure в таблицу "Поездка".

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Мониторинг загрузки

Проверьте, выполняется ли ваша загрузка, периодически выполняя следующий запрос:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Дальнейшие действия

- Рекомендации по загрузке данных см. в статье [Рекомендации по загрузке данных с помощью пула Synapse SQL](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data).
- Сведения об управлении ресурсами для загрузки данных см. в кратком руководстве [Настройка изоляции рабочих нагрузок с помощью T-SQL](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql). 
