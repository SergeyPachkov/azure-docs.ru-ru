---
title: Управление версиями набора данных
titleSuffix: Azure Machine Learning
description: Узнайте, как лучше использовать свои наборы данных и как работает управление версиями с помощью конвейеров машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 496a38e43c7bd624c42f5c7a43ad9cf16f85d166
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579578"
---
# <a name="version-and-track-datasets-in-experiments"></a>Версии и отслеживание наборов данных в экспериментах

В этой статье вы узнаете, как выполнять версию и отслеживание Машинное обучение Azure наборов данных для воспроизводимость. Управление версиями набора данных — это способ закладки состояния данных, чтобы можно было применить определенную версию набора данных для будущих экспериментов.

Типичные сценарии управления версиями:

* Когда новые данные доступны для повторного обучения
* При применении различных подходов к подготовке данных или проектированию компонентов

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством необходимы указанные ниже компоненты.

- [Установлен пакет SDK для машинное обучение Azure для Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py). Этот пакет SDK включает пакет [azureml-DataSets](/python/api/azureml-core/azureml.core.dataset?preserve-view=true&view=azure-ml-py) .
    
- [Рабочая область машинное обучение Azure](concept-workspace.md). Получите существующий, выполнив следующий код или [создав новую рабочую область](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Набор данных машинное обучение Azure](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Регистрация и получение версий наборов данных

Зарегистрировав набор данных, вы можете использовать его версию, повторное использование и совместное использование в экспериментах и с коллегами. Можно зарегистрировать несколько наборов данных с одним и тем же именем и получить определенную версию по имени и номеру версии.

### <a name="register-a-dataset-version"></a>Регистрация версии набора данных

Следующий код регистрирует новую версию `titanic_ds` набора данных, присвоив `create_new_version` параметру значение `True` . Если в `titanic_ds` рабочей области нет уже зарегистрированного набора данных, код создает новый набор данных с именем `titanic_ds` и устанавливает для его версии значение 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Получение набора данных по имени

По умолчанию метод [get_by_name ()](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) в `Dataset` классе возвращает последнюю версию набора данных, зарегистрированную в рабочей области. 

Следующий код возвращает версию 1 `titanic_ds` набора данных.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Рекомендации по управлении версиями

При создании версии набора данных вы *не* создаете дополнительные копии данных с рабочей областью. Поскольку наборы данных являются ссылками на данные в службе хранилища, у вас есть один источник истинности, управляемый службой хранилища.

>[!IMPORTANT]
> Если данные, на которые ссылается набор данных, перезаписываются или удаляются, вызов определенной версии набора данных *не* приводит к отмене изменений.

При загрузке данных из набора данных всегда загружается текущее содержимое данных, на которое ссылается набор данных. Если необходимо обеспечить воспроизводимость каждой версии набора данных, рекомендуется не изменять содержимое данных, на которое ссылается версия набора данных. Когда новые данные поступают в, сохраните новые файлы данных в отдельную папку данных, а затем создайте новую версию набора данных, чтобы включить данные из этой новой папки.

На следующем рисунке и образце кода показан рекомендуемый способ структурирования папок данных и создания версий наборов данных, ссылающихся на эти папки:

![Структура папок](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-an-ml-pipeline-output-dataset"></a>Версия выходного набора данных конвейера машинного обучения

В качестве входных и выходных данных каждого этапа [конвейера машинного обучения](concept-ml-pipelines.md) можно использовать набор данных. При повторном запуске конвейеров выходные данные каждого шага конвейера регистрируются в качестве новой версии набора данных.

Конвейеры машинного обучения заполняют выходные данные каждого шага в новую папку при каждом повторном выполнении конвейера. Такое поведение позволяет воспроизводить выходные наборы данных с управлением версиями. Дополнительные сведения о [наборах данных в конвейерах](how-to-create-your-first-pipeline.md#steps).

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datas-in-your-experiments"></a>Следите за данными в экспериментах

Машинное обучение Azure отслеживает данные во время эксперимента в качестве входных и выходных наборов данных.  

Ниже приведены сценарии, в которых данные отправляются в качестве **входного набора данных**. 

* Как `DatasetConsumptionConfig` объект с помощью `inputs` `arguments` параметра или `ScriptRunConfig` объекта при отправке запуска эксперимента. 

* При вызове таких методов, как, get_by_name () или get_by_id () в скрипте. В этом сценарии имя, присвоенное набору данных при его регистрации в рабочей области, является отображаемым именем. 

Ниже приведены сценарии, в которых данные отправляются в виде **выходного набора** данных.  

* Передайте `OutputFileDatasetConfig` объект с помощью `outputs` параметра или `arguments` при отправке запуска эксперимента. `OutputFileDatasetConfig` объекты также можно использовать для сохранения данных между этапами конвейера. См [. раздел Перемещение данных между этапами конвейера ml.](how-to-move-data-in-out-of-pipelines.md)
    > [!TIP]
    > [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) — Это открытый класс предварительной версии, содержащий [экспериментальные](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=truestable-vs-experimental) функции, которые могут измениться в любое время.

* Зарегистрируйте набор данных в скрипте. В этом сценарии имя, присвоенное набору данных при его регистрации в рабочей области, является отображаемым именем. В следующем примере `training_ds` — это имя, которое будет отображаться.

    ```Python
   training_ds = unregistered_ds.register(workspace = workspace,
                                     name = 'training_ds',
                                     description = 'training data'
                                     )
    ```

* Отправка дочернего элемента с незарегистрированным набором данных в скрипте. В результате получается анонимный сохраненный набор данных.

### <a name="trace-datasets-in-experiment-runs"></a>Наборы данных трассировки в запусках экспериментов

Для каждого Машинное обучение эксперимента можно легко отслеживать наборы данных, используемые в качестве входных данных, с помощью объекта эксперимента `Run` .

В следующем коде метод используется [`get_details()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--) для мониторинга того, какие входные наборы данных использовались при выполнении эксперимента:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Вы также можете найти `input_datasets` эксперименты с помощью [машинное обучение Azure Studio](). 

На следующем рисунке показано, где найти входной набор данных эксперимента в Машинное обучение Azure Studio. В этом примере перейдите в область **эксперименты** и откройте вкладку **свойства** для конкретного запуска эксперимента `keras-mnist` .

![Входные наборы данных](./media/how-to-version-track-datasets/input-datasets.png)

Используйте следующий код для регистрации моделей с наборами данных:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

После регистрации можно просмотреть список моделей, зарегистрированных в наборе данных, с помощью Python или перейдите к [студии](https://ml.azure.com/).

Следующее представление находится на панели **наборы данных** в разделе **активы**. Выберите набор данных и перейдите на вкладку **модели** для списка моделей, зарегистрированных в наборе данных. 

![Модели входных наборов данных](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Обучение с наборами данных](how-to-train-with-datasets.md)
* [Дополнительные образцы набора данных записные книжки](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)