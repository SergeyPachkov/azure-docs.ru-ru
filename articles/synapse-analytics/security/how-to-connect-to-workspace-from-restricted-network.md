---
title: Подключение к ресурсам рабочей области в Azure синапсе Analytics Studio из ограниченной сети
description: Из этой статьи вы узнаете, как подключиться к ресурсам рабочей области из ограниченной сети.
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 7cff2d8245095489fbba3b7af24b416885995e4d
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637138"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>Подключение к ресурсам рабочей области из ограниченной сети

Предположим, вы являетесь ИТ-администратором, который управляет сетью, ограниченной в Организации. Необходимо включить сетевое подключение между Azure синапсе Analytics Studio и рабочей станцией в этой ограниченной сети. Эта статья поможет вам сделать это.

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure** : Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* **Рабочая область Azure синапсе Analytics**. Вы можете создать ее из Azure синапсе Analytics. Вам потребуется имя рабочей области на шаге 4.
* **Ограниченная сеть**. ИТ-администратор обслуживает ограниченную сеть для Организации и имеет разрешение на настройку политики сети. В шаге 3 вам потребуется имя виртуальной сети и ее подсеть.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Шаг 1. Добавление сетевых правил безопасности для исходящего трафика в сеть с ограниченным доступом

Необходимо добавить четыре правила сетевой безопасности для исходящего трафика с четырьмя тегами службы. 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* Азуремонитор (этот тип правила является необязательным. Добавьте его, только если вы хотите поделиться данными с Майкрософт.)

На следующем снимке экрана показаны подробные сведения о Azure Resource Manager правиле исходящего трафика.

![Снимок экрана со сведениями о теге службы Azure Resource Manager.](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

При создании других трех правил замените значение **тега службы назначения** на **азурефронтдур. интерфейс** , **AzureActiveDirectory** или **азуремонитор** из списка.

Дополнительные сведения см. в статье [Обзор тегов служб](/azure/virtual-network/service-tags-overview.md).

## <a name="step-2-create-private-link-hubs"></a>Шаг 2. Создание концентраторов частных ссылок

Затем создайте центры частных ссылок из портал Azure. Чтобы найти это на портале, выполните поиск по запросу *Azure синапсе Analytics (концентраторы частных ссылок)* , а затем введите необходимые сведения, чтобы создать его. 

> [!Note]
> Убедитесь, что значение **региона** совпадает с тем, в котором находится рабочая область Azure синапсе Analytics.

![Снимок экрана: создание центра частных ссылок для синапсе.](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-gateway"></a>Шаг 3. Создание частной конечной точки для шлюза

Чтобы получить доступ к шлюзу Azure синапсе Analytics Studio, необходимо создать частную конечную точку из портал Azure. Чтобы найти это на портале, выполните поиск по *ссылке частная ссылка*. В **частном центре ссылок** выберите **создать частную конечную точку** , а затем укажите необходимые сведения, чтобы создать ее. 

> [!Note]
> Убедитесь, что значение **региона** совпадает с тем, в котором находится рабочая область Azure синапсе Analytics.

![Снимок экрана: создание частной конечной точки, вкладка "основные".](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

На вкладке **ресурс** выберите концентратор частных ссылок, созданный на шаге 2.

![Снимок экрана создания частной конечной точки, вкладки ресурсов.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

На вкладке **Конфигурация** выполните следующие действия. 
* В поле **Виртуальная сеть** выберите ограниченное имя виртуальной сети.
* В поле **подсеть** выберите подсеть с ограниченной виртуальной сетью. 
* Для варианта **Интеграция с частной зоной DNS** выберите **Да**.

![Снимок экрана: создание частной конечной точки, вкладка "Конфигурация".](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

После создания конечной точки частной ссылки можно получить доступ к странице входа веб-инструмента для Azure синапсе Analytics Studio. Однако вы еще не можете получить доступ к ресурсам в рабочей области. Для этого необходимо выполнить следующий шаг.

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>Шаг 4. Создание частных конечных точек для ресурса рабочей области

Для доступа к ресурсам в ресурсе рабочей области Azure синапсе Analytics Studio необходимо создать следующее:

- По крайней мере одна конечная точка частной связи с типом **разработчика** **целевого подресурса**.
- Две другие необязательные конечные точки Private Link с типами **SQL** или **склондеманд** в зависимости от того, какие ресурсы в рабочей области необходимо получить.

Их создание аналогично созданию конечной точки на предыдущем шаге.  

На вкладке **ресурс** выполните следующие действия.

* В качестве **типа ресурса** выберите **Microsoft. синапсе/workspaces**.
* В поле **ресурс** выберите имя рабочей области, созданное ранее.
* В поле **целевой подресурс** выберите тип конечной точки:
  * **SQL** предназначен для выполнения запросов SQL в пуле SQL.
  * **Склондеманд** предназначен для встроенного выполнения запросов SQL.
  * **Разработка** предназначена для доступа ко всем остальным в рабочих областях Azure синапсе Analytics Studio. Необходимо создать хотя бы одну конечную точку частной связи этого типа.

![Снимок экрана создания частной конечной точки, вкладки ресурсов, рабочей области.](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>Шаг 5. Создание частных конечных точек для связанного хранилища рабочей области

Чтобы получить доступ к связанному хранилищу с помощью обозревателя хранилищ в рабочей области Azure синапсе Analytics Studio, необходимо создать одну частную конечную точку. Эти действия похожи на шаги, описанные в шаге 3. 

На вкладке **ресурс** выполните следующие действия.
* В качестве **типа ресурса** выберите **Microsoft. синапсе/storageAccounts**.
* В поле **ресурс** выберите имя учетной записи хранения, созданной ранее.
* В поле **целевой подресурс** выберите тип конечной точки:
  * **большой двоичный объект** предназначен для хранилища BLOB-объектов Azure.
  * **DFS** предназначен для Azure Data Lake Storage 2-го поколения.

![Снимок экрана создания частной конечной точки, вкладки ресурсов, хранилища.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

Теперь вы можете получить доступ к связанному ресурсу хранилища. В виртуальной сети в рабочей области Azure синапсе Analytics Studio можно использовать Обозреватель хранилищ для доступа к связанному ресурсу хранилища.

Вы можете включить управляемую виртуальную сеть для рабочей области, как показано на следующем снимке экрана:

![Снимок экрана создания рабочей области синапсе с выделенным параметром "включить управляемую виртуальную сеть".](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

Если вы хотите, чтобы Записная книжка была подключена к связанным ресурсам хранилища в определенной учетной записи хранения, добавьте управляемые частные конечные точки в Azure синапсе Analytics Studio. Необходимо указать имя учетной записи хранения, к которой должна иметь доступ ваша Записная книжка. Дополнительные сведения см. [в статье Создание управляемой частной конечной точки для источника данных](./how-to-create-managed-private-endpoints.md).

После создания этой конечной точки состояние утверждения показывает состояние **Ожидание**. Запросите утверждение от владельца этой учетной записи хранения на вкладке " **подключения частной конечной точки** " этой учетной записи хранения в портал Azure. После утверждения Записная книжка сможет получить доступ к связанным ресурсам хранилища в этой учетной записи хранения.

Теперь все готово. Вы можете получить доступ к ресурсу рабочей области Azure синапсе Analytics Studio.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об [управляемой виртуальной сети рабочей области](./synapse-workspace-managed-vnet.md).

Дополнительные сведения об [управляемых частных конечных точках](./synapse-workspace-managed-private-endpoints.md).
