---
title: Преобразование данных с помощью JAR-файла
description: Сведения об обработке или преобразовании данных с помощью Databricks Jar.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: shwang
ms.date: 03/15/2018
ms.openlocfilehash: 6b010000a674e351051c664dd5eeacd40e802439
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81414608"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Преобразование данных с помощью выполнения действий Jar в Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Действие Jar в Azure Databricks в [конвейере фабрики данных](concepts-pipelines-activities.md) позволяет запустить файл Spark Jar в кластере Azure Databricks. Эта статья посвящена [действиям преобразования данных](transform-data.md)   , в которых представлен общий обзор преобразования данных и поддерживаемых действий преобразования.Azure Databricks — это управляемая платформа для запуска Apache Spark.

Уделите 11 минут вашего времени, чтобы просмотреть следующее видео с кратким обзором и демонстрацией этой функции:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Определение действия Jar в Databricks

Пример определения JSON для действия Jar в Databricks:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Свойства действия Jar в Databricks

В следующей таблице приведено описание свойств, используемых в определении JSON.

|Свойство|Описание|Обязательно|
|:--|---|:-:|
|name|Имя действия в конвейере.|Да|
|description|Описание действия.|Нет|
|type|Тип действия Jar в Databricks — DatabricksSparkJar.|Да|
|linkedServiceName|Имя связанной службы Databricks, в которой выполняется действие Jar. Дополнительные сведения об этой связанной службе см. в статье [связанные службы вычислений](compute-linked-services.md)   .|Да|
|mainClassName|Полное имя класса, содержащего метод main, который будет выполнен. Этот класс должен содержаться в файле JAR, предоставляемом в виде библиотеки.|Да|
|параметры|Параметры, которые будут переданы в метод main.  Массив строк.|Нет|
|libraries|Список библиотек, которые должны быть установлены на кластере, на котором будет выполнено задание. Массив объектов <строка, объект>|Да (по крайней мере один метод, содержащий mainClassName)|

> [!NOTE]
> **Известная ошибка** . при использовании одного и того же [интерактивного кластера](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) для выполнения операций JAR-файла с параллельными модулями данных (без перезапуска кластера) существует известная ошибка в модулях данных, где в параметрах первого действия будут использоваться следующие действия. Поэтому в последующие задания передаются неверные параметры. Для устранения этой проблемы используйте вместо этого [кластер заданий](compute-linked-services.md#example---using-new-job-cluster-in-databricks) . 

## <a name="supported-libraries-for-databricks-activities"></a>Поддерживаемые библиотеки для действий Databricks

В приведенном выше определении действия Databricks необходимо указать следующие типы библиотек: *jar*, *egg*, *maven*, *pypi*, *cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Дополнительные сведения см. в [документации Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) по типам библиотек.

## <a name="how-to-upload-a-library-in-databricks"></a>Отправка библиотеки в Databricks

#### <a name="using-databricks-workspace-ui"></a>[С помощью пользовательского интерфейса рабочей области Databricks](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Чтобы получить путь к dbfs библиотеке, добавленной с помощью пользовательского интерфейса, можно использовать [интерфейс командной строки Databricks (установка)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Обычно библиотеки Jar, добавленные с помощью пользовательского интерфейса, хранятся в каталоге dbfs:/FileStore/jars. Вы можете получить список всех библиотек, выполнив следующую команду в интерфейсе командной строки: *databricks fs ls dbfs:/FileStore/job-jars*. 



#### <a name="copy-library-using-databricks-cli"></a>[Копирование библиотеки с помощью интерфейса командной строки Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Используйте интерфейс командной строки Databricks [(действия по установке)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Пример: копирование JAR-файлов в DBFS: *dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar*
