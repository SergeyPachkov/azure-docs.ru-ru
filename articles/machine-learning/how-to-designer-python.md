---
title: Выполнение скрипта Python в конструкторе
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать модель выполнения скриптов Python в конструкторе Машинное обучение Azure для выполнения пользовательских операций, написанных на языке Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, designer, devx-track-python
ms.openlocfilehash: dcc28d98efbc82079586de8cfbecd35effc93d6e
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556239"
---
# <a name="run-python-code-in-azure-machine-learning-designer"></a>Выполнение кода Python в конструкторе Машинное обучение Azure

Из этой статьи вы узнаете, как использовать модуль [Выполнение сценария Python](algorithm-module-reference/execute-python-script.md) для добавления настраиваемой логики в конструктор Машинного обучения Azure. В следующем примере используется библиотека Pandas для простого проектирования признаков.

Можно использовать встроенный редактор кода для быстрого добавления простой логики Python. Если требуется добавить более сложный код или загрузить дополнительные библиотеки Python, следует использовать ZIP-файл.

Среда выполнения по умолчанию использует дистрибутив Python от Anaconda. Полный список предварительно установленных пакетов см. на странице [Справочник по модулю "Выполнение сценария Python"](algorithm-module-reference/execute-python-script.md).

![Схема ввода для выполнения сценария Python](media/how-to-designer-python/execute-python-map.png)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="execute-python-written-in-the-designer"></a>Выполнение Python, написанного в конструкторе

### <a name="add-the-execute-python-script-module"></a>Добавление модуля выполнения сценария Python

1. Найдите модуль **Выполнение сценария Python** в палитре конструктора. Он находится в разделе **Язык Python**.

1. Перетащите модуль на холст конвейера.

### <a name="connect-input-datasets"></a>Подключение входных наборов данных

В этой статье используется образец набора данных **Цены на автомобили (необработанные данные)** . 

1. Перетащите набор данных на холст конвейера.

1. Подключите выходной порт набора данных к верхнему левому порту ввода модуля **Выполнение сценария Python**. Конструктор предоставляет входные данные в качестве параметра для сценария точки входа.
    
    Порт ввода справа зарезервирован для библиотек Python в формате ZIP.

    ![Подключение наборов данных](media/how-to-designer-python/connect-dataset.png)
        

1. Запишите используемый порт ввода. Конструктор назначает для переменной `dataset1` входной порт слева, а для `dataset2` — средний входной порт. 

Входные модули являются необязательными, поскольку вы можете создавать или импортировать данные непосредственно в модуль **Выполнение сценария Python**.

### <a name="write-your-python-code"></a>Написание кода Python

Конструктор предоставляет сценарий начальной точки входа для редактирования и ввода собственного кода Python. 

В этом примере используется Pandas для объединения двух столбцов в наборе данных об автомобилях, **Price** и **Horsepower** , для создания нового столбца **Dollars per horsepower**. В этом столбце представлены сведения о стоимости в зависимости от мощности, что может быть полезным для принятия решения о том, стоит ли приобретать автомобиль. 

1. Выберите модуль **Выполнение сценария Python**.

1. В области, расположенной справа от холста, выберите текстовое поле **Сценарий Python**.

1. Скопируйте следующий код и вставьте его в текстовое поле.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    Конвейер должен выглядеть следующим образом:
    
    ![Выполнение конвейера Python](media/how-to-designer-python/execute-python-pipeline.png)

    Сценарий точки входа должен содержать функцию `azureml_main`. Существует два параметра функции, которые сопоставляются с двумя входными портами для модуля **Выполнение сценария Python**.

    Возвращаемое значение должно быть кадром данных Pandas. В выходных данных модуля можно вернуть до двух кадров данных.
    
1. Отправьте конвейер.

Теперь у вас есть набор данных с новой функцией **Dollars/HP** , который может быть полезен при обучении средства рекомендации автомобиля. Это пример извлечения признаков и уменьшения размерности. 

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [импортировать собственные данные](how-to-designer-import-data.md) в конструктор Машинного обучения Azure.
