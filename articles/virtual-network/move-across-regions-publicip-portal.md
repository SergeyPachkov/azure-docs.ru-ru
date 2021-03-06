---
title: Перемещение конфигурации общедоступного IP-адреса Azure в другой портал Azure региона Azure
description: Используйте шаблон, чтобы переместить конфигурацию общедоступного IP-адреса Azure из одного региона Azure в другой с помощью портал Azure.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 23fe515ddfdecb9ef168dd662e3fa2d91ece688f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711482"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>Перемещение общедоступной IP-конфигурации Azure в другой регион с помощью портал Azure

Существуют разные причины, по которым может потребоваться перенос существующих конфигураций общедоступного IP-адреса Azure из одного региона в другой. Например, вы хотите создать общедоступный IP-адрес с такой же конфигурацией и SKU для тестирования. Или вы перемещаете конфигурацию в рамках планирования аварийного восстановления.

**Общедоступные IP-адреса Azure относятся к регионам и не могут перемещаться из одного региона в другой.** Однако можно использовать шаблон Azure Resource Manager для экспорта существующей конфигурации общедоступного IP-адреса.  Затем можно разместить ресурс в другом регионе, экспортировав общедоступный IP-адрес в шаблон, изменив параметры в соответствии с регионом назначения и развернув шаблон в новом регионе.  Дополнительные сведения о Resource Manager и шаблонах см. в [Кратком руководстве по созданию и развертыванию шаблонов Azure Resource Manager с помощью портала Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что общедоступный IP-адрес Azure расположен в регионе Azure, из которого вы намерены его перенести.

- Общедоступные IP-адреса Azure нельзя перемещать между регионами.  Необходимо связать новый общедоступный IP-адрес с ресурсами в целевом регионе.

- Чтобы экспортировать конфигурацию общедоступного IP-адреса и развернуть шаблон для создания общедоступного IP-адреса в другом регионе, вам потребуется роль "Участник сети" или выше.

- Определите структуру сети в исходном регионе и все ресурсы, которые вы сейчас используете. Сюда могут входить, среди прочего, подсистемы балансировки нагрузки, группы безопасности сети и виртуальные сети.

- Убедитесь, что используемая подписка Azure позволяет создавать общедоступные IP-адреса в целевом регионе. Свяжитесь со службой поддержки, чтобы включить необходимые квоты.

- Убедитесь, что у вашей подписки достаточно ресурсов для поддержки добавления общедоступных IP-адресов для этого процесса.  Ознакомьтесь со статьей [Подписка Azure, границы, квоты и ограничения службы](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Подготовка и перемещение
Ниже описано, как подготовить общедоступный IP-адрес для перемещения конфигурации с помощью шаблона диспетчер ресурсов и переместить общедоступную IP-конфигурацию в целевой регион с помощью портал Azure.

### <a name="export-the-template-and-deploy-from-a-script"></a>Экспорт шаблона и развертывание из скрипта

1. Войдите в [портал Azure](https://portal.azure.com)  >  **группы ресурсов**.
2. Выберите группу ресурсов, содержащую общедоступный исходный IP-адрес, и щелкните ее.
3. Выберите **Параметры**>  >  **Экспорт шаблона**.
4. Выберите **развернуть** в колонке **Экспорт шаблона** .
5. Щелкните **шаблон**  >  **изменить параметры** , чтобы открыть **parameters.jsв** файле в интерактивном редакторе.
8. Чтобы изменить параметр имени общедоступного IP-адреса, измените значение свойства в разделе **Параметры**  >  **value** из исходного общедоступного IP-адреса на имя целевого общедоступного IP-адреса и убедитесь, что имя указано в кавычках:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  Нажмите кнопку **сохранить** в редакторе.

9.  Щелкните **шаблон**  >  **изменить шаблон** , чтобы открыть **template.jsв** файле в интерактивном редакторе.

10. Чтобы изменить целевой регион, в который будет перемещен общедоступный IP-адрес, измените свойство **Location** в разделе **ресурсы**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. Чтобы получить коды расположения регионов, см. раздел [расположения Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Код для региона — это имя региона без пробелов, **Центральная американская**  =  **centralus**.

12. Кроме того, при необходимости можно изменить другие параметры в шаблоне:

    * **SKU** . Вы можете изменить номер SKU общедоступного IP-адреса в конфигурации с Standard на Basic или Basic на Standard, изменив **sku**  >  свойство**имя** SKU в **template.jsв** файле:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Дополнительные сведения о различиях между общедоступными IP-адресами уровня "базовый" и "Стандартный" см. в статье [Создание, изменение или удаление общедоступного адреса](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Метод выделения общедоступного IP-адреса** и **Время ожидания простоя**. Вы можете изменить оба этих параметра в шаблоне, изменив свойство **PublicIPAllocationMethod** со значения **Dynamic** на значение **Static** или со значения **Static** на значение **Dynamic**. Время ожидания при простое можно изменить, изменив свойство **IdleTimeoutInMinutes** на требуемый объем.  Значение по умолчанию — **4**:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Дополнительные сведения о методах выделения и значениях времени ожидания при простое см. в разделе [Создание, изменение или удаление общедоступного IP-адреса](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


13. Нажмите кнопку **сохранить** в интерактивном редакторе.

14. Щелкните **основные**  >  **подписки** , чтобы выбрать подписку, в которой будет развернут целевой общедоступный IP-адрес.

15. Щелкните **основные**  >  **ресурсы группа ресурсов** , чтобы выбрать группу ресурсов, в которой будет развернут целевой общедоступный IP-адрес.  Можно щелкнуть **создать** , чтобы создать новую группу ресурсов для целевого общедоступного IP-адреса.  Убедитесь, что имя не совпадает с исходной группой ресурсов существующего исходного общедоступного IP-адреса.

16. Убедитесь, что в качестве **основы**  >  **расположения** выбрано целевое расположение, в котором требуется развернуть общедоступный IP-адрес.

17. Проверьте в разделе **Параметры** , имя которых совпадает с именем, введенным в редакторе параметров выше.

18. Установите флажок в разделе **условия**.

19. Нажмите кнопку **приобрести** , чтобы развернуть целевой общедоступный IP-адрес.

## <a name="discard"></a>Игнорировать

Если вы хотите отменить целевой общедоступный IP-адрес, удалите группу ресурсов, содержащую целевой общедоступный IP-адрес.  Для этого выберите группу ресурсов на панели мониторинга на портале и щелкните **Удалить** в верхней части страницы обзора.

## <a name="clean-up"></a>Очистка

Чтобы зафиксировать изменения и завершить перемещение общедоступного IP-адреса, удалите исходный общедоступный IP-адрес или группу ресурсов. Для этого выберите общедоступный IP-адрес или группу ресурсов на панели мониторинга на портале и щелкните **Удалить** в верхней части каждой страницы.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы переместили общедоступный IP-адрес Azure из одного региона в другой и очистили исходные ресурсы.  Дополнительные сведения о перемещении ресурсов между регионами и аварийном восстановлении в Azure см. по следующей ссылке:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
