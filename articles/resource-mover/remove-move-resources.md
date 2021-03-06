---
title: Удаление ресурсов из коллекции перемещения в службе "перемещение ресурсов Azure"
description: Узнайте, как удалить ресурсы из коллекции перемещения в службе "перемещение ресурсов Azure".
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 38a633a7a11ac29271231679e7075920e1f33a70
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945949"
---
# <a name="manage-move-collections-and-resource-groups"></a>Управление перемещением коллекций и групп ресурсов

В этой статье описывается, как удалить ресурсы из коллекции перемещения или удалить коллекцию или группу ресурсов в службе "перемещение [ресурсов Azure](overview.md)". Перемещение коллекций используется при перемещении ресурсов Azure между регионами Azure.

## <a name="remove-a-resource-portal"></a>Удаление ресурса (портала)

Вы можете удалить ресурсы в коллекции перемещения на портале перемещения ресурсов следующим образом:

1. В **области в разных регионах**выберите все ресурсы, которые необходимо удалить из коллекции, и нажмите кнопку **Удалить**. 

    ![Кнопка для удаления](./media/remove-move-resources/portal-select-resources.png)

2. В окне **удаление ресурсов**нажмите кнопку **Удалить**.

    ![Кнопка, позволяющая выбрать удаление ресурсов из коллекции перемещения](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>Удаление коллекции перемещения или группы ресурсов (портал)

Вы можете удалить перемещаемую коллекцию или группу ресурсов на портале.

1. Выполните инструкции из описанной выше процедуры, чтобы удалить ресурсы из коллекции. Если вы удаляете группу ресурсов, убедитесь, что она не содержит ресурсов.
2. Удаление коллекции перемещения или группы ресурсов.  

## <a name="remove-a-resource-powershell"></a>Удаление ресурса (PowerShell)

Удалите ресурс (в нашем примере — Псдемовм Machines) из коллекции с помощью PowerShell, как показано ниже.

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus - Name PSDemoVM
```
**Ожидаемый результат** 
 ![ Вывод текста после удаления ресурса из коллекции перемещения](./media/remove-move-resources/remove-resource.png)



## <a name="remove-a-collection-powershell"></a>Удаление коллекции (PowerShell)

Удалите всю коллекцию перемещения с помощью PowerShell, как показано ниже.

1. Выполните приведенные выше инструкции, чтобы удалить ресурсы из коллекции с помощью PowerShell.
2. Выполните:

    ```azurepowershell-interactive
    # Remove a resource using the resource ID
    Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus 
    ```
    **Ожидаемый результат** ![ Вывод текста после удаления коллекции перемещения](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>Состояние ресурса виртуальной машины после удаления

Что происходит при удалении ресурса виртуальной машины из коллекции перемещения, зависит от состояния ресурса, как показано в таблице.

###  <a name="remove-vm-state"></a>Удалить состояние виртуальной машины
**Состояние ресурса** | **Виртуальная машина** | **Сеть**
--- | --- | --- 
**Добавлен для перемещения коллекции** | Удалить из коллекции Move. | Удалить из коллекции Move. 
**Разрешаемые или подготовленные зависимости** | Удалить из коллекции перемещения  | Удалить из коллекции Move. 
**Идет подготовка**<br/> (или любое другое состояние) | Операция удаления завершается с ошибкой.  | Операция удаления завершается с ошибкой.
**Сбой подготовки** | Удалите из коллекции перемещения.<br/>Удалите все, что создано в целевом регионе, включая диски реплики. <br/><br/> Ресурсы инфраструктуры, созданные во время перемещения, необходимо удалить вручную. | Удалите из коллекции перемещения.  
**Ожидание инициации перемещения** | Удалить из коллекции Move.<br/><br/> Удалите все, что создано в целевом регионе, включая виртуальные машины, диски реплики и т. д.  <br/><br/> Ресурсы инфраструктуры, созданные во время перемещения, необходимо удалить вручную. | Удалить из коллекции Move.
**Не удалось инициировать перемещение** | Удалить из коллекции Move.<br/><br/> Удалите все, что создано в целевом регионе, включая виртуальные машины, диски реплики и т. д.  <br/><br/> Ресурсы инфраструктуры, созданные во время перемещения, необходимо удалить вручную. | Удалить из коллекции Move.
**Ожидание фиксации** | Рекомендуется отменить перемещение, чтобы сначала удалить целевые ресурсы.<br/><br/> Ресурс возвращается в состояние **Ожидание запуска перемещения** , и вы можете продолжить отсюда. | Рекомендуется отменить перемещение, чтобы сначала удалить целевые ресурсы.<br/><br/> Ресурс возвращается в состояние **Ожидание запуска перемещения** , и вы можете продолжить отсюда. 
**Сбой фиксации** | Рекомендуется удалить, чтобы целевые ресурсы были удалены первыми.<br/><br/> Ресурс возвращается в состояние **Ожидание запуска перемещения** , и вы можете продолжить отсюда. | Рекомендуется отменить перемещение, чтобы сначала удалить целевые ресурсы.<br/><br/> Ресурс возвращается в состояние **Ожидание запуска перемещения** , и вы можете продолжить отсюда.
**Отмена завершена** | Ресурс возвращается в состояние **Ожидание запуска перемещения** .<br/><br/> Он удаляется из коллекции перемещения, а также все, что создается в целевой виртуальной машине, дисках реплики, хранилище и т. д.  <br/><br/> Ресурсы инфраструктуры, созданные во время перемещения, необходимо удалить вручную. <br/><br/> Ресурсы инфраструктуры, созданные во время перемещения, необходимо удалить вручную. |  Ресурс возвращается в состояние **Ожидание запуска перемещения** .<br/><br/> Он удаляется из коллекции перемещения.
**Сбой при отмене** | Рекомендуется отказаться от перемещений, чтобы сначала удалить целевые ресурсы.<br/><br/> После этого ресурс возвращается в состояние **Ожидание запуска перемещения** , и вы можете продолжить отсюда. | Рекомендуется отказаться от перемещений, чтобы сначала удалить целевые ресурсы.<br/><br/> После этого ресурс возвращается в состояние **Ожидание запуска перемещения** , и вы можете продолжить отсюда.
**Ожидается удаление источника** | Удаляется из коллекции перемещения.<br/><br/> Он не удаляет все, что было создано в целевом регионе.  | Удаляется из коллекции перемещения.<br/><br/> Он не удаляет все, что было создано в целевом регионе.
**Не удалось удалить источник** | Удаляется из коллекции перемещения.<br/><br/> Он не удаляет все, что было создано в целевом регионе. | Удаляется из коллекции перемещения.<br/><br/> Он не удаляет все, что было создано в целевом регионе.

## <a name="sql-resource-state-after-removing"></a>Состояние ресурсов SQL после удаления

Что происходит при удалении ресурса SQL Azure из коллекции перемещения, зависит от состояния ресурса, как показано в таблице.

**Состояние ресурса** | **SQL** 
--- | --- 
**Добавлен для перемещения коллекции** | Удалить из коллекции Move. 
**Разрешаемые или подготовленные зависимости** | Удалить из коллекции перемещения 
**Идет подготовка**<br/> (или любое другое состояние)  | Операция удаления завершается с ошибкой. 
**Сбой подготовки** | Удалить из коллекции перемещения<br/><br/>Удалите все, что было создано в целевом регионе. 
**Ожидание инициации перемещения** |  Удалить из коллекции перемещения<br/><br/>Удалите все, что было создано в целевом регионе. База данных SQL существует на этом этапе и будет удалена. 
**Не удалось инициировать перемещение** | Удалить из коллекции перемещения<br/><br/>Удалите все, что было создано в целевом регионе. База данных SQL существует на этом этапе и должна быть удалена. 
**Ожидание фиксации** | Рекомендуется отменить перемещение, чтобы сначала удалить целевые ресурсы.<br/><br/> Ресурс возвращается в состояние **Ожидание запуска перемещения** , и вы можете продолжить отсюда.
**Сбой фиксации** | Рекомендуется отменить перемещение, чтобы сначала удалить целевые ресурсы.<br/><br/> Ресурс возвращается в состояние **Ожидание запуска перемещения** , и вы можете продолжить отсюда. 
**Отмена завершена** |  Ресурс возвращается в состояние **Ожидание запуска перемещения** .<br/><br/> Он удаляется из коллекции перемещения, а также все, что создается на целевом объекте, включая базы данных SQL. 
**Сбой при отмене** | Рекомендуется отказаться от перемещений, чтобы сначала удалить целевые ресурсы.<br/><br/> После этого ресурс возвращается в состояние **Ожидание запуска перемещения** , и вы можете продолжить отсюда. 
**Ожидается удаление источника** | Удаляется из коллекции перемещения.<br/><br/> Он не удаляет все, что было создано в целевом регионе. 
**Не удалось удалить источник** | Удаляется из коллекции перемещения.<br/><br/> Он не удаляет все, что было создано в целевом регионе. 

## <a name="next-steps"></a>Дальнейшие шаги

Попробуйте [переместить виртуальную машину](tutorial-move-region-virtual-machines.md) в другой регион с помощью перемещения ресурсов.