---
title: Подключение Решения Azure VMware к локальной среде
description: Узнайте, как подключить Решение Azure VMware к локальной среде.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 2a0cb641df00f3e580e87e38aff382d8e8101fc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578910"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Подключение Решения Azure VMware к локальной среде

В этой статье вы продолжите использовать [сведения, собранные при планировании](production-ready-deployment-steps.md), чтобы подключить Решение Azure VMware к локальной среде.

Прежде чем приступать к подключению Решения Azure VMware к локальной среде, необходимо иметь следующее:

- цепь ExpressRoute из локальной среды в Azure;
- блок неперекрывающихся сетевых адресов /29 для пиринга ExpressRoute Global Reach, который вы определили как часть [этапа планирования](production-ready-deployment-steps.md).

>[!NOTE]
> Вы можете подключиться через VPN, но это не рассматривается в этом кратком руководстве.

## <a name="establish-an-expressroute-global-reach-connection"></a>Установка подключения ExpressRoute Global Reach

Чтобы установить локальное подключение к частному облаку Решения Azure VMware с помощью ExpressRoute Global Reach, следуйте указаниям в [этом учебнике](tutorial-expressroute-global-reach-private-cloud.md).



## <a name="verify-on-premises-network-connectivity"></a>Проверка локального сетевого подключения

Теперь вы должны увидеть в **локальном пограничном маршрутизаторе**, в котором подключается ExpressRoute, сегменты сети NSX-T и сегменты управления Решением VMware Azure.

>[!NOTE]
>У всех разная среда, и некоторым нужно будет разрешить, чтобы эти маршруты распространялись обратно в локальную сеть.  

В некоторых средах брандмауэры защищают цепь ExpressRoute.  Если брандмауэры отсутствуют и удаление маршрутов не выполняется, можно проверить связь с сервером vCenter Решения Azure VMware или [виртуальной машиной](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) в сегменте NSX-T из локальной среды.

Кроме того, с виртуальной машины в сегменте NSX-T можно получить доступ к ресурсам в локальной среде.

## <a name="next-steps"></a>Дальнейшие действия

Перейдите к следующему разделу для развертывания и настройки VMware HCX.

> [!div class="nextstepaction"]
> [Развертывание и настройка VMware HCX](tutorial-deploy-vmware-hcx.md)