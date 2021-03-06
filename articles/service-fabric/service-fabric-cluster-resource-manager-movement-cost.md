---
title: 'Диспетчер ресурсов кластера Service Fabric: затраты на перемещение'
description: Узнайте о затратах на перемещение для служб Service Fabric и о том, как ее можно указать в соответствии с любой архитектурой, включая динамическую конфигурацию.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 0fdcfb02851d56ed996ae4bf32671ab545782733
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89005349"
---
# <a name="service-movement-cost"></a>Затраты на перемещение служб
Фактором, который диспетчер кластерных ресурсов Service Fabric принимает во внимание при попытке определить, какие изменения вносить в кластер, являются общие затраты на эти изменения. Понятие "затраты" соотносится со степенью улучшения кластера. Затраты учитываются при перемещении служб для балансировки, дефрагментации и выполнения других требований. Цель — выполнить требования с наименьшим вмешательством или затратами.

Как минимум перемещение служб требует затрат процессорного времени и пропускной способности сети. Для служб с отслеживанием состояния требуется копировать их состояние, что приводит к использованию дополнительных ресурсов памяти и дисков. Сведение к минимуму затрат на решения, получаемые диспетчером кластерных ресурсов Azure Service Fabric, гарантирует, что ресурсы кластера не будут использоваться без необходимости. Однако при этом не хотелось бы игнорировать решения, которые могли бы значительно улучшить выделение ресурсов кластера.

В диспетчере кластерных ресурсов есть два способа вычисления затрат и их ограничения, применяемые в управлении кластером. Первый механизм — это просто подсчет каждого производимого перемещения. Если созданы два решения примерно с одинаковым распределением нагрузки (оценкой), то диспетчер кластерных ресурсов выберет решение с меньшими затратами (общим числом перемещений).

Эта стратегия хорошо работает. Но, как и в случае нагрузок по умолчанию или статических нагрузок, маловероятно, что в любой сложной системе все будут перемещения равнозначны. Некоторые могут быть намного более затратными.

## <a name="setting-move-costs"></a>Настройка затрат на перемещение 
Можно задать затраты на перемещение по умолчанию для службы при ее создании.

PowerShell.

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Можно также динамически указать или обновить затраты на перемещение для службы после ее создания. 

PowerShell. 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Динамическое указание затрат на перемещение на уровне реплики

Все фрагменты кода, приведенные выше, указывают значение затрат на перемещение сразу для всей службы, причем извне этой службы. Тем не менее, затраты на перемещение удобнее всего использовать, когда их значение для определенного объекта службы изменяется на протяжении времени его существования. Так как сами службы, вероятно, лучше всего определяют затраты на свое перемещение на определенный момент времени, предусмотрен API, с помощью которого службы могут сообщать значение собственных отдельных затрат на перемещение во время выполнения. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Влияние затрат на перемещение
MoveCost имеет пять уровней: ноль, низкий, средний, высокий и Верихигх. Применяются следующие правила.

* Мовекостс задаются относительно друг друга, за исключением нулей и Верихигх. 
* Стоимость перемещения Zero означает, что перемещение является бесплатным и не должно учитываться при оценке решения.
* Установка высокого или *верихигх не гарантирует* , что реплика *не будет* перемещена.
* Реплики с затратами на перемещение Верихигх будут перемещены только в том случае, если в кластере нарушено ограничение, которое не может быть исправлено каким-либо другим способом (даже если для устранения нарушения требуется переместить много других реплик).



<center>

![Влияние стоимости перемещения на выбор реплик для перемещения][Image1]
</center>

Параметр MoveCost помогает найти решения, которые в целом приводят к меньшему прерыванию работы и позволяют легче достичь равноценного баланса. Понятие стоимости службы может зависеть от многих факторов. Ниже перечислены наиболее распространенные факторы, учитываемые при вычислении стоимости перемещения.

- Объем данных состояния или информации, перемещаемой для службы.
- Стоимость отключения клиентов. Как правило, затраты на перемещение первичной реплики выше, чем затраты на перемещение вторичной реплики.
- Стоимость прерывания текущих операций. Некоторые операции уровня хранилища данных или операции, выполняемые по вызову клиента, являются дорогостоящими. После определенного момента вы не станете останавливать их, если только это не вынужденная мера. Поэтому пока происходит операция, увеличьте стоимость перемещения этого объекта службы, чтобы снизить вероятность его перемещения. А после завершения операции восстановите обычное значение стоимости перемещения.

> [!IMPORTANT]
> Следует тщательно обдумать использование стоимости перемещения Верихигх, так как она значительно ограничит способность кластера диспетчер ресурсов найти глобально оптимальное решение размещения в кластере. Реплики с затратами на перемещение Верихигх будут перемещены только в том случае, если в кластере нарушено ограничение, которое не может быть исправлено каким-либо другим способом (даже если для устранения нарушения требуется переместить много других реплик).

## <a name="enabling-move-cost-in-your-cluster"></a>Включение учета затрат на перемещение в кластере
Чтобы обеспечить учет более детализированных затрат на перемещение, в кластере нужно включить использование затрат на перемещение. Если это не настроить, то для расчета затрат на перемещение используется стандартный режим подсчета перемещений и отчеты о затратах на перемещение игнорируются.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

Для автономных развертываний используется ClusterConfig.json, а для размещенных в Azure кластеров — Template.json.

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Дальнейшие действия
- С помощью метрик диспетчер кластерных ресурсов Service Fabric управляет потреблением и емкостью в кластере. Чтобы узнать больше о метрик и их настройке, ознакомьтесь с разделом [Управление потреблением ресурсов и нагрузкой в Service Fabric с помощью метрик](service-fabric-cluster-resource-manager-metrics.md).
- Чтобы узнать, как диспетчер кластерных ресурсов управляет нагрузкой кластера и балансирует ее, ознакомьтесь со статьей [Балансировка кластера Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
