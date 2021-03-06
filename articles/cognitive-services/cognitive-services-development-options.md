---
title: Варианты разработки Cognitive Services Azure
description: Узнайте, как использовать Cognitive Services Azure с различными вариантами разработки и развертывания, такими как клиентские библиотеки, интерфейсы API, Logic Apps, Автоматизация питания, функции Azure, служба приложений Azure, Azure Databricks и многие другие.
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 4eaa33778287bfcda45547c24e6abe0606b6baa7
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368803"
---
# <a name="cognitive-services-development-options"></a>Варианты разработки для Cognitive Services

В этом документе представлен общий обзор вариантов разработки и развертывания, которые помогут вам приступить к работе с Azure Cognitive Services.

Cognitive Services Azure — это облачные службы искусственного интеллекта, позволяющие разработчикам создавать средства анализа приложений и продуктов без глубокого знания машинного обучения. С Cognitive Services можно получить доступ к возможностям и моделям искусственного интеллекта, которые созданы, обучены и обновлены корпорацией Майкрософт для использования в приложениях. Во многих случаях также имеется возможность настройки моделей для бизнес-нужд. 

Cognitive Services разбиты на четыре категории: решение, язык, речь и концепция. Обычно доступ к этим службам осуществляется через API-интерфейсы RESTFUL, клиентские библиотеки и пользовательские средства (например, интерфейсы командной строки), предоставляемые корпорацией Майкрософт. Однако это только один путь к успешному выполнению. С помощью Azure вы также можете получить доступ к нескольким вариантам разработки, таким как:

* средства автоматизации и интеграции, такие как Logic Apps и Power Automate;
* варианты развертывания, такие как "Функции Azure" и "Служба приложений"; 
* контейнеры Docker Cognitive Services для безопасного доступа;
* такие средства, как Apache Spark, Azure Databricks, Azure Synapse Analytics и "Служба Azure Kubernetes" для сценариев с большими данными. 

Прежде чем перейти к началу работы, важно понять, что Cognitive Services в основном используется для двух отдельных задач. В зависимости от задачи, которую вы хотите выполнить, вы можете выбрать различные варианты разработки и развертывания. 

* [Варианты разработки для прогнозирования и анализа](#development-options-for-prediction-and-analysis)
* [Средства для настройки и настройки моделей](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>Варианты разработки для прогнозирования и анализа 

Средства, которые будут использоваться для настройки и настройки моделей, отличаются от средств, используемых для вызова Cognitive Services. В большинстве случаев большинство Cognitive Services позволяют отправлять данные и получать ценные сведения без каких бы то ни было настроек. Пример: 

* Вы можете отправить изображение в службу Компьютерное зрение, чтобы обнаружить слова и фразы или подсчитать количество людей в кадре.
* Вы можете отправить звуковой файл в службу распознавания речи и получить транскрипции и преобразовать речь в текст в то же время
* Вы можете отправить документ PDF в службу распознавания форм и определить таблицы, ячейки и текст внутри этих ячеек, а также получить выходные данные JSON с координатами и подробными сведениями.

Azure предлагает широкий спектр средств, предназначенных для различных типов пользователей, многие из которых можно использовать с Cognitive Services. Средства, управляемые конструктором, проще всего использовать, и их можно быстро настроить и автоматизировать, но при этом они могут иметь ограничения по настройке. Наши API-интерфейсы и клиентские библиотеки RESTFUL предоставляют пользователям больший контроль и гибкость, но нуждаются в дополнительных усилиях, времени и опытах для создания решения. Если вы используете API-интерфейсы и клиентские библиотеки для RESTFUL, то предполагалось, что вы умеете работать с современными языками программирования, такими как C#, Java, Python, JavaScript или другой популярный язык программирования. 

Давайте взглянем на различные способы работы с Cognitive Services.

### <a name="client-libraries-and-rest-apis"></a>Клиентские библиотеки и интерфейсы REST API

Cognitive Services клиентские библиотеки и интерфейсы API-интерфейсов RESTFUL обеспечивают прямой доступ к службе. Эти средства предоставляют программный доступ к Cognitive Services, базовым моделям и во многих случаях позволяют программно настраивать модели и решения. 

* **Целевые пользователи** : разработчики и специалисты по обработке и анализу данных
* **Преимущества**. обеспечивает максимальную гибкость при вызове служб из любого языка и среды. 
* **Пользовательский интерфейс** : н/д — только код
* **Подписки** : учетная запись Azure + Cognitive Services ресурсы

Если вы хотите узнать больше о доступных клиентских библиотеках и API-интерфейсах RESTFUL, воспользуйтесь нашим [Cognitive Services обзором](index.yml) для выбора и обслуживания и начала работы с одним из наших кратких руководств по концепциям, решениям, языку и распознаванию речи.

### <a name="cognitive-services-for-big-data"></a>Cognitive Services для больших данных

Кроме того, вы можете внедрять постоянно улучшающиеся интеллектуальные модели непосредственно в Apache Spark&trade; и вычисления SQL. Эти инструменты избавляют разработчиков от необходимости вникать в сетевые конфигурации, чтобы они могли сосредоточиться на создании интеллектуальных распределенных приложений. Cognitive Services для больших данных поддерживает следующие платформы и соединители: Azure Databricks, Azure синапсе, служба Kubernetes Azure и соединители данных.

* **Целевые пользователи** : специалисты по обработке и анализу данных
* **Преимущества**. Cognitive Services Azure для больших данных позволяет пользователям получить доступ к терабайтам данных через Cognitive Services с помощью Apache Spark &trade; . Можно легко создавать крупномасштабные интеллектуальные приложения с любым хранилищем данных.
* **Пользовательский интерфейс** : н/д — только код
* **Подписки** : учетная запись Azure + Cognitive Services ресурсы

Если вы хотите узнать больше о больших данных для Cognitive Services, лучше всего начать с [обзора](./big-data/cognitive-services-for-big-data.md). Если вы готовы приступить к созданию, попробуйте наши примеры [Python](./big-data/samples-python.md) или [Scala](./big-data/samples-scala.md) .

### <a name="azure-functions-and-azure-service-web-jobs"></a>Функции Azure и веб-задания службы Azure

[Функции Azure](../azure-functions/index.yml) и [веб-задания службы приложений Azure](../app-service/index.yml) предоставляют службы интеграции, предназначенные для разработчиков, и созданы на основе [служб приложений Azure](../app-service/index.yml). Эти продукты предоставляют бессерверную инфраструктуру для написания кода. В этом коде вы можете вызывать наши службы, используя клиентские библиотеки и интерфейсы API-интерфейсов RESTFUL. 

* **Целевые пользователи** : разработчики и специалисты по обработке и анализу данных
* **Преимущества** : служба бессерверных вычислений, позволяющая запускать код, активируемый событием. 
* **Пользовательский интерфейс** : Да
* **Подписки** : учетная запись Azure + Cognitive Services ресурс + подписка на функции Azure

### <a name="azure-logic-apps"></a>Azure Logic Apps 

[Azure Logic Apps](../logic-apps/index.yml) совместно использовать один и тот же конструктор рабочих процессов и соединители, но обеспечивает более широкие возможности управления, включая интеграцию с Visual Studio и DevOps. Power автоматизиру упрощает интеграцию с ресурсами для работы со службами через соединители для конкретных служб, которые предоставляют прокси или оболочку для интерфейсов API. Это те же соединители, которые доступны в Power автоматизировать. 

* **Целевые пользователи** : разработчики, интеграторы, ИТ-специалисты, DevOps
* **Преимущества** : модель разработки First Designer (декларативная), предоставляющая расширенные возможности и интеграцию в решении с низким кодом
* **Пользовательский интерфейс** : Да
* **Подписки** : учетная запись Azure + Cognitive Services ресурс + развертывание Logic Apps

### <a name="power-automate"></a>Power Automate 

Power автоматизировать — это служба в [платформе питания](/power-platform/) , которая помогает создавать автоматизированные рабочие процессы между приложениями и службами без написания кода. Мы предлагаем несколько соединителей, которые облегчают взаимодействие с Cognitive Servicesным ресурсом в решении Power автоматизировать. Служба Power Automate создана на основе Logic Apps. 

* **Целевые** пользователи: бизнес-пользователей (аналитики) и администраторы SharePoint
* **Преимущества**. автоматизируйте повторяющиеся ручные задачи просто путем записи щелчков мыши, нажатий клавиш и копирования шагов вставки с рабочего стола!
* **Средства пользовательского интерфейса** : Да — только пользовательский интерфейс
* **Подписки** : учетная запись Azure + Cognitive Services ресурс + Power Автоматизация Подписка + подписка Office 365

### <a name="ai-builder"></a>AI Builder 

[AI Builder](/ai-builder/overview) — это функция платформы электропитания Майкрософт, которую можно использовать для повышения производительности бизнеса за счет автоматизации процессов и прогнозирования результатов. Построитель ИСКУССТВЕНного интеллекта предоставляет возможности искусственного интеллекта в своих решениях с помощью функций «точка-щелчок». Многие службы, такие как распознаватель форм, Анализ текста и Компьютерное зрение, были непосредственно интегрированы и вам не нужно создавать собственные Cognitive Services. 

* **Целевые** пользователи: бизнес-пользователей (аналитики) и администраторы SharePoint
* **Преимущества** : готовое решение, объединяющее возможности ИСКУССТВЕНного интеллекта. Нет необходимости в написании кода или навыков обработки и анализа данных.
* **Средства пользовательского интерфейса** : Да — только пользовательский интерфейс
* **Подписки** : построитель искусственного интеллекта

### <a name="continuous-integration-and-deployment"></a>Непрерывная интеграция и развертывание

Вы можете использовать действия Azure DevOps и GitHub для управления развертываниями. В [разделе ниже](#continuous-integration-and-delivery-with-devops-and-github-actions) представлены два примера интеграции CI/CD для обучения и развертывания пользовательских моделей для речи и службы Language UNDERSTANDING (Luis). 

* **Целевые пользователи** : разработчики, специалисты по обработке данных и инженеры по обработке данных
* **Преимущества** : позволяет постоянно настраивать, обновлять и развертывать приложения и модели программным способом. Существует значительное преимущество при регулярном использовании данных для улучшения и обновления моделей для распознавания речи, концепций, языка и принятия решений. 
* **Средства пользовательского интерфейса** : н/д — только код 
* **Подписки** : учетная запись Azure + Cognitive Services ресурс + учетная запись GitHub

## <a name="tools-to-customize-and-configure-models"></a>Средства для настройки и настройки моделей

При создании приложения или рабочего процесса с Cognitive Services может оказаться необходимым настроить модель для достижения желаемой производительности. Многие наши службы позволяют создавать поверх готовых моделей в соответствии с конкретными потребностями бизнеса. Для всех наших настраиваемых служб мы предоставляем как пользовательский интерфейс для прохода по процессу, так и интерфейсы API для обучения на основе кода. Пример:

* Вы хотите обучить Пользовательское распознавание речиную модель, чтобы правильно распознать медицинские термины со словом "Частота ошибок" (WER) ниже 3%
* Вы хотите создать классификатор изображений с Пользовательское визуальное распознавание, который может сообщить о различиях между хвойное и листопадное деревьями
* Вы хотите создать пользовательский нейронный счет с личными голосовыми данными для повышения качества автоматизированного взаимодействия с клиентами

Средства, которые будут использоваться для обучения и настройки моделей, отличаются от средств, используемых для вызова Cognitive Services. Во многих случаях Cognitive Services, поддерживающие настройку, предоставляют порталы и средства пользовательского интерфейса, предназначенные для обучения, вычисления и развертывания моделей. Давайте быстро рассмотрим несколько вариантов:<br><br>

| Аспект | Служба | Пользовательский интерфейс настройки | Краткое руководство |
|--------|---------|------------------|------------|
| Зрение | Пользовательское визуальное распознавание | https://www.customvision.ai/ | [Краткое руководство](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| Зрение | Распознаватель документов | Средство маркировки данных | [Краткое руководство](./form-recognizer/quickstarts/label-tool.md?tabs=v2-0) |
| Решение | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [Краткое руководство](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| Решение | Помощник по метрикам | https://metricsadvisor.azurewebsites.net/  | [Краткое руководство](./metrics-advisor/quickstarts/web-portal.md) |
| Решение | Персонализатор | Пользовательский интерфейс доступен в портал Azure в ресурсе персонализации. | [Краткое руководство](./personalizer/quickstart-personalizer-sdk.md) |
| Язык | Распознавание речи (LUIS) | https://www.luis.ai/ | |
| Язык | QnA Maker | https://www.qnamaker.ai/ | [Краткое руководство](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| Язык | Переводчик или настраиваемый переводчик | https://portal.customtranslator.azure.ai/ | [Краткое руководство](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| Речь | Пользовательские команды | https://speech.microsoft.com/ | [Краткое руководство](./speech-service/custom-commands.md) |
| Речь | Пользовательское распознавание речи | https://speech.microsoft.com/ | [Краткое руководство](./speech-service/how-to-custom-speech.md) |
| Речь | Custom Voice (Пользовательские голосовые модели) | https://speech.microsoft.com/ | [Краткое руководство](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>Непрерывная интеграция и доставка с помощью действий DevOps и GitHub

Language Understanding и служба распознавания речи предлагают решения для непрерывной интеграции и непрерывного развертывания на основе действий Azure DevOps и GitHub. Эти средства используются для автоматического обучения, тестирования и управления выпусками пользовательских моделей. 

* [CI/CD для набора средств "Пользовательское распознавание речи"](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [CI/CD для LUIS](./luis/luis-concept-devops-automation.md)

## <a name="on-prem-containers"></a>Локальные контейнеры 

Многие из Cognitive Services могут быть развернуты в контейнерах для локального доступа и использования. Используя эти контейнеры, вы получаете возможность Cognitive Services ближе к вашим данным для обеспечения соответствия требованиям, безопасности или других эксплуатационных причин. Полный список контейнеров Cognitive Services см. в разделе Локальные [контейнеры для Cognitive Services](./cognitive-services-container-support.md).

## <a name="next-steps"></a>Дальнейшие шаги
<!--
* Learn more about low code development options for Cognitive Services -->
* [Создание Cognitive Services ресурса и начало создания](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)