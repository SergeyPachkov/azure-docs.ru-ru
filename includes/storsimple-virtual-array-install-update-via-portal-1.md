---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: e64db93347794ccfc85252205c6d92a68e904635
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376142"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Установка обновлений с помощью портала Azure

1. Откройте диспетчер устройств StorSimple и выберите **Устройства**. Из списка устройств, подключенных к службе, выберите то, которое требуется обновить.

    ![обновление устройства](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. В колонке **Параметры** щелкните **Обновления устройства**.

    ![Обновление устройства 2](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. При наличии доступных обновлений вы увидите сообщение об этом. Чтобы проверить наличие обновлений, можно щелкнуть элемент **Проверить**. Запишите версию программного обеспечения, которое выполняется в вашей системе. 

    ![Обновление устройства 3](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    Вы получите уведомление о начале и успешном завершении проверки.

    ![Обновление устройства 4](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. После проверки наличия обновлений щелкните **Скачать обновления**.

    ![Обновление устройства 5](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. В колонке **новых обновлений** просмотрите заметки о выпуске. Обратите внимание, что после скачивания обновлений необходимо подтвердить установку. Нажмите кнопку **ОК**.

    ![Обновление устройства 6](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. Вы получите уведомления о начале и успешном завершении загрузки.

     ![Обновление устройства 7](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. В колонке **Обновления устройства** щелкните **Установить**.

     ![Обновление устройства 8](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. В колонке **Свежие обновления** вы увидите предупреждение о том, что обновление нарушит работу. Так как виртуальный массив является устройством с одним узлом, после обновления он будет перезагружен. Все текущие операции ввода-вывода будут прерваны. Щелкните **ОК** , чтобы установить обновления.

    ![Обновление устройства 9](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. Вы получите уведомление о запуске задания установки.

    ![Обновление устройства 10](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  Когда задание установки успешно завершится, выберите ссылку **Просмотреть задание** в колонке **Обновление устройства** , чтобы проверить выполнение установки. 

    ![Обновление устройства 11](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Вы перейдете к колонке **Install Updates** (Установка обновлений). Здесь вы увидите подробные сведения о выполненном задании.

    ![Обновление устройства 12](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Если в начале у вас был виртуальный массив с установленным обновлением 0.6 (10.0.10293.0) для программного обеспечения, сейчас у вас установлено обновление 1. Оставшиеся действия можно пропустить. Если же у вас был виртуальный массив с более ранней версией программного обеспечения, чем 0.6 (10.0.10293.0), то теперь у вас установлена версия 0.6. Появится еще одно сообщение о том, что обновления доступны. Повторите шаги 4–8, чтобы установить обновление 1.

    ![Обновление устройства 13](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

