---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "66248934"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Добавление группы потребителей в Центр Интернета вещей

[Группы потребителей](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) предоставляют независимые представления в поток событий, что позволяет приложениям и службам Azure независимо использовать данные из одной конечной точки концентратора событий. В этом разделе вы добавите группу потребителей в встроенную конечную точку центра Интернета вещей, которая будет использоваться далее в этом руководстве для извлечения данных из конечной точки.

Чтобы добавить группу потребителей в Центр Интернета вещей, сделайте следующее:

1. Откройте Центр Интернета вещей на [портале Azure](https://portal.azure.com/).

2. На левой панели выберите **встроенные конечные точки**, щелкните **события** на правой панели и введите имя в поле **группы потребителей**. Щелкните **Сохранить**.

   ![Создание группы потребителей в Центре Интернета вещей](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
