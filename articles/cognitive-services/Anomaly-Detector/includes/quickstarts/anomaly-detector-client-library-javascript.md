---
title: Краткое руководство по использованию клиентской библиотеки Детектора аномалий (JavaScript)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.custom: devx-track-js
ms.openlocfilehash: b970b099d87148d169b2be3b7e72d32c159f5046
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371786"
---
Приступите к работе с клиентской библиотекой Детектора аномалий для JavaScript. Выполните следующие действия, чтобы установить пакет и приступить к использованию алгоритмов, предоставляемых службой. Служба Детектора аномалий позволяет находить аномалии в данных временных рядов, автоматически применяя для них наиболее подходящие модели, независимо от отрасли, сценария или объема данных.

Клиентскую библиотеку Детектора аномалий для JavaScript можно использовать для таких задач:

* обнаружение аномалий в наборе данных временных рядов с использованием пакетного запроса;
* Обнаружение состояния аномалии последней точки данных во временном ряду.
* обнаружение точек изменения тенденций в наборе данных.

[Справочная документация по библиотеке](https://go.microsoft.com/fwlink/?linkid=2090788) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Пакет (npm)](https://www.npmjs.com/package/%40azure/ai-anomaly-detector) | [Поиск кода на GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services).
* Текущая версия [Node.js](https://nodejs.org/)
* Получив подписку Azure, перейдите к <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Создание ресурса Детектора аномалий"  target="_blank">созданию ресурса Детектора аномалий <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. Дождитесь, пока закончится развертывание, и нажмите кнопку **Перейти к ресурсу**.
    * Для подключения приложения к API "Детектор аномалий" потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.

## <a name="setting-up"></a>Настройка

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp && cd myapp
```

Выполните команду `npm init`, чтобы создать приложение узла с помощью файла `package.json`. 

```console
npm init
```

Создайте файл с именем `index.js` и импортируйте следующие библиотеки:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Создайте переменные конечной точки и ключа ресурса Azure. Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена. Создайте другую переменную для примера файла данных, который будет скачан позже, и пустой список для точек данных. Затем создайте объект `ApiKeyCredentials`, в котором будет содержаться ключ.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

Установите пакеты NPM `ms-rest-azure` и `azure-cognitiveservices-anomalydetector`. В этом кратком руководстве также используется библиотека анализа CSV-файла:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

Файл `package.json` этого приложения будет дополнен зависимостями.

## <a name="object-model"></a>Объектная модель

Клиент Детектора аномалий — это объект [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest), который выполняет проверку подлинности в Azure с помощью вашего ключа. Клиент может выполнять обнаружение аномалий с использованием [entireDetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--) для всего набора данных и [LastDetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) для последней точки данных. Метод [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090788) обнаруживает точки, которые отмечают изменения тенденции. 

Данные временного ряда отправляются в виде ряда [точек](/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) в объекте [запроса](/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest). Объект `Request` содержит свойства для описания данных (например, [степень детализации](/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity)) и параметры для обнаружения аномалий. 

Ответ Детектора аномалий — это объект [LastDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest), [EntireDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) или [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090788) (в зависимости от используемого метода). 

## <a name="code-examples"></a>Примеры кода 

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки Детектора аномалий для Node.js:

* [аутентификация клиента](#authenticate-the-client);
* [загрузка набора данных временного ряда из файла](#load-time-series-data-from-a-file);
* [обнаружение аномалий во всем наборе данных](#detect-anomalies-in-the-entire-data-set); 
* [обнаружение состояния аномалии последней точки данных](#detect-the-anomaly-status-of-the-latest-data-point).
* [Обнаружение точек изменения в наборе данных](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Аутентификация клиента

Создайте экземпляр объекта [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) с конечной точкой и учетными данными.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Загрузка данных временного ряда из файла

Скачайте пример данных для этого краткого руководства с сайта [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. В браузере щелкните правой кнопкой мыши пункт **Без обработки**.
2. Выберите **Сохранить ссылку как**.
3. Сохраните файл в формате CSV в каталоге вашего приложения.

Данные временных рядов форматируются как CSV-файл и будут отправлены в API Детектора аномалий.

Прочтите файл данных с помощью `readFileSync()` метода библиотеки анализа CSV-файла и проанализируйте файл с помощью `parse()`. Для каждой строки отправьте объект [Точка](/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest), содержащий метку времени, и числовое значение.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Обнаружение аномалий во всем наборе данных 

Вызовите API для обнаружения аномалий в виде пакета временного ряда с помощью метода [entireDetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) клиента. Сохраните возвращенный объект [EntireDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest). Выполните итерацию списка ответов `isAnomaly` и выведите индексы любых значений `true`. Эти значения соответствуют индексу аномальных точек данных, если они были найдены.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Обнаружение состояний аномалии последней точки данных

Вызовите API Детектора аномалий, чтобы определить, является ли последняя точка данных аномальной, с помощью метода [lastDetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) клиента и сохраните возвращенный объект [LastDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest). Значение `isAnomaly` в ответе представляет собой логическое значение, указывающее состояние аномалий этой точки.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="detect-change-points-in-the-data-set"></a>Обнаружение точек изменения в наборе данных

Вызовите API для обнаружения точек изменения во временном ряде с помощью метода [detectChangePoint()](https://go.microsoft.com/fwlink/?linkid=2090788) клиента. Сохраните полученный объект [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090788). Выполните итерацию списка ответов `isChangePoint` и выведите индексы любых значений `true`. Эти значения соответствуют индексам точек изменения тенденции, если они были обнаружены.

[!code-javascript[detect change points](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=changePointDetection)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение, выполнив команду `node` для файла quickstart.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]