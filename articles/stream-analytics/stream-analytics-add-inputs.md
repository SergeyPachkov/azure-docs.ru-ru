---
title: Общие сведения о входных данных Azure Stream Analytics
description: В этой статье описываются концепции входных данных в задании Azure Stream Analytics, сравнение потоковых и справочных входных данных.
author: jseb225
ms.author: krishmam
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: b344e9e24d15189b805f586227c7253395e8448e
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348990"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Общие сведения о входных данных Azure Stream Analytics

Задания Azure Stream Analytics используют несколько видов входных данных. Все входные данные определяют подключение к имеющемуся источнику данных. Stream Analytics принимает входящие данные от нескольких типов источников событий, включая Центры событий, Центр Интернета вещей и хранилище BLOB-объектов. Входные данные подаются в имени потокового SQL-запроса, которое записывается для каждого задания. В запросе вы можете объединить несколько входных данных для смешивания данных или сравнения потоковых данных с помощью уточняющего запроса ссылочных данных и передачи результатов в выходные данные. 

Stream Analytics имеет интеграцию первого класса с четырьмя типами ресурсов в качестве входных данных:
- [Центры событий Azure](https://azure.microsoft.com/services/event-hubs/)
- [Центр Интернета вещей Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Хранилище BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-introduction.md) 

Эти входные ресурсы могут находиться в той же подписке Azure, что и ваше задание Stream Analytics, либо в другой подписке.

Для создания, изменения и проверки входных данных задания Stream Analytics можно использовать [портал Azure](stream-analytics-quick-create-portal.md#configure-job-input),  [Azure PowerShell](/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), [API .NET](/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [REST API](/rest/api/streamanalytics/2016-03-01/inputs)и [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) .

## <a name="stream-and-reference-inputs"></a>Потоковые и справочные входные данные
Данные, отправляемые в источник данных, принимаются заданием Stream Analytics и обрабатываются в режиме реального времени. Входные данные делятся на два типа: входные потоковые данные и входные справочные данные.

### <a name="data-stream-input"></a>Входные потоковые данные
Поток данных — это несвязанная последовательность событий в динамике по времени. Задания Stream Analytics должны включать хотя бы один входной поток данных. Концентраторы событий, центр Интернета вещей, Azure Data Lake Storage 2-го поколения и хранилище BLOB-объектов поддерживаются в качестве источников входных данных потока. Центры событий используются для сбора потоков событий из нескольких устройств и служб. Это могут быть ленты новостей социальных сетей, сведения о торговле акциями или данные датчиков. Центры Интернета вещей оптимизированы для сбора данных с подключенных устройств в сценариях Интернета вещей.  Хранилище больших двоичных объектов может использоваться в качестве источника входных данных при сборе массовых данных в виде потока, например файлов журналов.  

Дополнительные сведения о входных потоковых данных см. в статье [Подключение данных: узнайте о потоках входных данных из событий в Stream Analytics](stream-analytics-define-inputs.md).

### <a name="reference-data-input"></a>Входные справочные данные
Stream Analytics также поддерживает входные данные, называемые *ссылочными данными*. Справочные данные являются полностью статическими и изменяются крайне редко. Они обычно используются для осуществления корреляции и поисков. Например, можно соединить входные потоковые данные со ссылочными данными так же, как вы бы выполнили соединение SQL для поиска статических значений. В настоящее время хранилище BLOB-объектов Azure, Azure Data Lake Storage 2-го поколения и база данных SQL Azure поддерживаются в качестве входных источников для ссылочных данных. Размер BLOB-объектов источника эталонных данных ограничен до 300 МБ в зависимости от сложности запроса и выделенных единиц потоковой передачи (Дополнительные сведения см. в разделе " [ограничение размера](stream-analytics-use-reference-data.md#size-limitation) " документации по ссылочным данным).

Дополнительные сведения о входных справочных данных см. в статье [Использование эталонных данных для уточняющих запросов в Stream Analytics](stream-analytics-use-reference-data.md).

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Краткое руководство. по созданию задания Stream Analytics с помощью портала Azure](stream-analytics-quick-create-portal.md)