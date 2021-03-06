---
title: Настройка Azure Monitor для сбора данных агента контейнеров | Документация Майкрософт
description: В этой статье описывается настройка Azure Monitor для агента контейнеров для управления набором журналов stdout/stderr и переменных среды.
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: f21b841bc129012b684d2a1c59eb72989fe9e0e0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890502"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Настройка сбора данных агента для компонента "Azure Monitor для контейнеров"

Azure Monitor для контейнеров собирает потоки stdout, stderr и переменные среды из рабочих нагрузок контейнера, развернутых в управляемых кластерах Kubernetes из контейнерного агента. Параметры сбора данных агента можно настроить, создав настраиваемый Конфигмапс Kubernetes для управления этим интерфейсом. 

В этой статье показано, как создать ConfigMap и настроить сбор данных в соответствии с вашими требованиями.

>[!NOTE]
>Для Azure Red Hat OpenShift в пространстве имен *OpenShift-Azure-Logging* создается файл шаблона ConfigMap. 
>

## <a name="configmap-file-settings-overview"></a>Общие сведения о параметрах файлов ConfigMap

Предоставляется файл шаблона ConfigMap, который позволяет легко редактировать его с помощью настроек без необходимости создавать его с нуля. Прежде чем начать, ознакомьтесь с документацией по Kubernetes о [конфигмапс](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) и ознакомьтесь с созданием, настройкой и развертыванием конфигмапс. Это позволит вам фильтровать stderr и stdout на пространство имен или во всем кластере, а также переменные среды для любого контейнера, работающего во всех модулях Pod и узлах в кластере.

>[!IMPORTANT]
>Минимальная версия агента, поддерживаемая для получения переменных среды stdout, stderr и Variable из рабочих нагрузок контейнера, — ciprod06142019 или более поздней версии. Чтобы проверить версию агента, на вкладке **узел** выберите узел и в области свойства запишите значение свойства **тег образа агента** . Дополнительные сведения о версиях агентов и о том, что входит в каждый выпуск, см. в разделе [заметки о выпуске агента](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Параметры сбора данных

В следующей таблице описаны параметры, которые можно настроить для управления сбором данных.

| Клавиши | Тип данных | Значение | Описание |
|--|--|--|--|
| `schema-version` | Строка (с учетом регистра) | Версия 1 | Это версия схемы, используемая агентом<br> При анализе этого ConfigMap.<br> Текущая поддерживаемая версия схемы — v1.<br> Изменение этого значения не поддерживается и будет<br> отклонено при вычислении ConfigMap. |
| `config-version` | Строка |  | Поддерживает возможность отслеживания версии этого файла конфигурации в системе управления версиями или в репозитории.<br> Максимально допустимое количество символов равно 10, а все остальные символы усекаются. |
| `[log_collection_settings.stdout] enabled =` | Логическое | true или false | Определяет, включен ли сбор журналов контейнеров stdout. Если задано значение `true` и никакие пространства имен не исключаются для сбора журналов stdout<br> ( `log_collection_settings.stdout.exclude_namespaces` значение ниже). журналы stdout будут собираться из всех контейнеров во всех модулях Pod и узлах в кластере. Если не указано в Конфигмапс,<br> значение по умолчанию — `enabled = true` . |
| `[log_collection_settings.stdout] exclude_namespaces =` | Строка | Массив с разделителями-запятыми | Массив пространств имен Kubernetes, для которых не будут собираться журналы stdout. Этот параметр эффективен, только если<br> `log_collection_settings.stdout.enabled`<br> присвоено значение `true`.<br> Если параметр не указан в ConfigMap, по умолчанию используется значение<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | Логическое | true или false | Определяет, включен ли сбор журналов контейнеров stderr.<br> Если задано значение `true` и никакие пространства имен не исключаются для сбора журналов stdout<br> ( `log_collection_settings.stderr.exclude_namespaces` параметр). журналы stderr будут собираться из всех контейнеров во всех модулях Pod и узлах в кластере.<br> Если параметр не указан в Конфигмапс, по умолчанию используется значение<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | Строка | Массив с разделителями-запятыми | Массив пространств имен Kubernetes, для которых не будут собираться журналы stderr.<br> Этот параметр эффективен, только если<br> Параметру `log_collection_settings.stdout.enabled` задается значение `true`.<br> Если параметр не указан в ConfigMap, по умолчанию используется значение<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | Логическое | true или false | Этот параметр управляет коллекцией переменных среды<br> во всех модулях (Pod) и узлах кластера<br> и по умолчанию равно, `enabled = true` если не указано<br> в Конфигмапс.<br> Если коллекция переменных среды включена глобально, ее можно отключить для определенного контейнера.<br> путем установки переменной среды<br> `AZMON_COLLECT_ENV`**значение false** либо с параметром Dockerfile, либо в [файле конфигурации для Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) в разделе **env:** .<br> Если коллекция переменных среды глобально отключена, то нельзя включить сбор для определенного контейнера (то есть единственным переопределением, которое можно применить на уровне контейнера, является отключение коллекции, если она уже включена глобально). |
| `[log_collection_settings.enrich_container_logs] enabled =` | Логическое | true или false | Этот параметр управляет расширением журнала контейнера для заполнения значений свойств Name и Image.<br> для каждой записи журнала, записанной в таблицу Контаинерлог для всех журналов контейнера в кластере.<br> По умолчанию используется значение, `enabled = false` если не указано в ConfigMap. |
| `[log_collection_settings.collect_all_kube_events]` | Логическое | true или false | Этот параметр позволяет коллекции событий KUBE всех типов.<br> По умолчанию события Kube с типом " *нормальный* " не собираются. Если для этого параметра задано значение `true` , *обычные* события больше не фильтруются, а все события собираются.<br> По умолчанию устанавливается значение `false`. |

### <a name="metric-collection-settings"></a>Параметры сбора метрик

В следующей таблице описаны параметры, которые можно настроить для управления сбором метрик.

| Клавиши | Тип данных | Значение | Описание |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | Логическое | true или false | Этот параметр позволяет собирать метрики использования постоянного тома (ПС) в пространстве имен KUBE-System. По умолчанию метрики использования постоянных томов с утверждениями постоянного тома в пространстве имен KUBE-System не собираются. Если этот параметр имеет значение `true` , то собираются метрики использования PV для всех пространств имен. По умолчанию устанавливается значение `false`. |

Конфигмапс является глобальным списком, и к нему может быть применен только один ConfigMap. У вас не может быть другой Конфигмапс.

## <a name="configure-and-deploy-configmaps"></a>Настройка и развертывание Конфигмапс

Выполните следующие действия, чтобы настроить и развернуть файл конфигурации ConfigMap в кластере.

1. Скачайте [файл шаблона CONFIGMAP YAML](https://aka.ms/container-azm-ms-agentconfig) и сохраните его как Container-АЗМ-MS-ажентконфиг. YAML. 

   > [!NOTE]
   > Этот шаг не требуется при работе с Azure Red Hat OpenShift, так как шаблон ConfigMap уже существует в кластере.

2. Измените файл YAML ConfigMap, используя настройки для получения переменных среды stdout, stderr и (или). Если вы редактируете файл YAML ConfigMap для Azure Red Hat OpenShift, сначала выполните команду, `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` чтобы открыть файл в текстовом редакторе.

    - Чтобы исключить определенные пространства имен для сбора журналов stdout, необходимо настроить ключ или значение, используя следующий пример: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]` .
    
    - Чтобы отключить коллекцию переменных среды для определенного контейнера, установите ключ/значение, `[log_collection_settings.env_var] enabled = true` чтобы включить коллекцию переменных глобально, а затем выполните действия, описанные [здесь](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) , чтобы завершить настройку для конкретного контейнера.
    
    - Чтобы отключить кластер для сбора журналов stderr, настройте ключ/значение, используя следующий пример: `[log_collection_settings.stderr] enabled = false` .

3. Для кластеров, отличных от Azure Red Hat OpenShift, создайте ConfigMap, выполнив следующую команду kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` на кластерах, отличных от Azure Red Hat OpenShift. 
    
    Например, `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Для Azure Red Hat OpenShift сохраните изменения в редакторе.

До вступления в силу изменение конфигурации может занять несколько минут, и все omsagent Pod в кластере будут перезапущены. Перезагрузка является пошаговым перезапуском для всех модулей omsagent Pod, а не всех перезапусков одновременно. После завершения перезагрузки отображается сообщение, похожее на следующее и содержащее результат: `configmap "container-azm-ms-agentconfig" created` .

## <a name="verify-configuration"></a>Проверка конфигурации

Чтобы убедиться, что конфигурация была успешно применена к кластеру, отличному от Azure Red Hat OpenShift, используйте следующую команду для просмотра журналов из модуля "агент": `kubectl logs omsagent-fdf58 -n kube-system` . При наличии ошибок конфигурации из модулей Pod omsagent в выходных данных отобразятся ошибки, аналогичные приведенным ниже.

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Ошибки, связанные с применением изменений конфигурации, также доступны для проверки. Для выполнения дополнительных действий по устранению изменений конфигурации можно использовать следующие параметры.

- Из журналов Pod агента с помощью той же `kubectl logs` команды. 

    >[!NOTE]
    >Эта команда неприменима к кластеру Azure Red Hat OpenShift.
    > 

- Из активных журналов. В журналах в реальном времени отображаются ошибки, аналогичные приведенным ниже.

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Из таблицы **кубемонажентевентс** в рабочей области log Analytics. Данные отправляются каждый час с серьезностью *ошибки* конфигурации. Если ошибок нет, запись в таблице будет содержать данные со *сведениями об* уровне серьезности, которые не сообщают об ошибках. Свойство **Tags** содержит дополнительные сведения о Pod и идентификаторе контейнера, в которых произошла ошибка, а также первое вхождение, Последнее повторение и количество за последний час.

- С помощью Azure Red Hat OpenShift Проверьте журналы omsagent, выполнив поиск в таблице **контаинерлог** , чтобы проверить, включено ли ведение журнала OpenShift-Azure-Logging.

После исправления ошибок в ConfigMap для кластеров, отличных от Azure Red Hat OpenShift, сохраните файл YAML и примените обновленную Конфигмапс, выполнив команду: `kubectl apply -f <configmap_yaml_file.yaml` . Для Azure Red Hat OpenShift измените и сохраните обновленный Конфигмапс, выполнив команду:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Применение обновленных ConfigMap

Если вы уже развернули ConfigMap в кластерах, отличных от Azure Red Hat OpenShift, и хотите обновить его с помощью более новой конфигурации, вы можете изменить ранее использовавшийся файл ConfigMap, а затем применить его с помощью той же команды, что и раньше, `kubectl apply -f <configmap_yaml_file.yaml` . Для Azure Red Hat OpenShift измените и сохраните обновленный Конфигмапс, выполнив команду:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

До вступления в силу изменение конфигурации может занять несколько минут, и все omsagent Pod в кластере будут перезапущены. Перезагрузка является пошаговым перезапуском для всех модулей omsagent Pod, а не всех перезапусков одновременно. После завершения перезагрузки отображается сообщение, похожее на следующее и содержащее результат: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verifying-schema-version"></a>Проверка версии схемы

Поддерживаемые версии схемы конфигурации доступны в виде заметки к Pod (версии схемы) в модуле omsagent. Их можно увидеть с помощью следующей команды kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

Выходные данные будут выглядеть так, как показано ниже, с помощью схемы аннотации — версии:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Дальнейшие действия

- Azure Monitor для контейнеров не включает предопределенный набор предупреждений. Ознакомьтесь с разработкой [оповещений о производительности с помощью Azure Monitor для контейнеров](./container-insights-log-alerts.md) , чтобы узнать, как создавать Рекомендуемые оповещения для высокой загрузки ЦП и памяти для поддержки DevOps или рабочих процессов и процедур.

- С включенным мониторингом для получения сведений о работоспособности и использовании ресурсов в AKS или гибридном кластере и рабочих нагрузках Вы узнаете, [как использовать](container-insights-analyze.md) Azure Monitor для контейнеров.

- Просмотрите [примеры запросов журналов](container-insights-log-search.md#search-logs-to-analyze-data) , чтобы просмотреть предварительно определенные запросы и примеры для проверки или настройки предупреждений, визуализации или анализа кластеров.