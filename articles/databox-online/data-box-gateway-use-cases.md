---
title: Microsoft Azure Шлюз Data Box вариантов использования | Документация Майкрософт
description: Описание вариантов использования Шлюз Azure Data Box, решения для хранения виртуальных устройств, которое позволяет передавать данные в Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/02/2019
ms.author: alkohli
ms.openlocfilehash: dde84f0973cc7e21e57574bbabe398b38581358f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82562420"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Сценарии использования для шлюза Azure Data Box

Шлюз Azure Data Box — это устройство шлюза для облачного хранилища, размещенное в локальной среде для отправки в Azure изображений, мультимедийных файлов и других данных. Устройство шлюза для облачного хранилища создается на виртуальной машине, подготовленной в гипервизоре. Вы можете сохранять данные на этом виртуальном устройстве по протоколам NFS и SMB, а затем они отправляются в Azure. В этой статье подробно описаны несколько сценариев, в которых целесообразно развертывать такое устройство.

Шлюз Data Box следует применять в следующих случаях:

- для постоянного приема очень больших объемов данных;
- для эффективной и защищенной архивации данных в облаке;
- для передачи добавочных данных по сети после начальной массовой передачи с помощью Data Box.

В следующих разделах подробно описаны все эти сценарии.


## <a name="continuous-data-ingestion"></a>Непрерывный прием данных

Одно из основных преимуществ Шлюза Data Box — возможность постоянно принимать на устройство данные для копирования в облако, независимо от их размера.

Устройство отправляет данные в службу хранилище Azure по мере их получения. Устройство автоматически управляет хранилищем, удаляя локальные копии файлов (но сохраняя метаданные) при достижении определенного порога. Сохранение локальной копии метаданных позволяет устройству шлюза передавать только изменения при обновлении файла. Отправляемые на устройство шлюза данные должны соответствовать требованиям, изложенным в разделе [Предупреждения, связанные с передачей данных](data-box-gateway-limits.md#data-upload-caveats).

По мере заполнения устройства данными оно применяет регулирование скорости для приема данных (по мере необходимости) в соответствии со скоростью передачи данных в облако. Для мониторинга непрерывного процесса приема применяются оповещения. Эти оповещения создаются при начале регулирования и удаляются при его прекращении.

## <a name="cloud-archival-of-data"></a>Архивация данных в облаке

Шлюз Data Box позволяет длительное время хранить данные в облаке. Для долгосрочного хранения можно использовать **архивный** уровень хранилища.

Архивный уровень оптимизирован для хранения редко используемых данных в течение 180 дней и более. **Архивный** уровень требует наименьших затрат на хранение, но наибольших — на доступ. Дополнительные сведения см. в разделе [Архивный уровень доступа](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier).

### <a name="move-data-to-archive-tier"></a>Перемещение данных на архивный уровень

Перед началом работы убедитесь, что у вас есть работающее устройство "Шлюз Data Box". Выполните действия, описанные в разделе [учебник. Подготовка к развертыванию шлюз Azure Data Box](data-box-gateway-deploy-prep.md) и продолжите переход к следующему руководству, пока не будет установлено рабочее устройство.

- Отправьте данные в Azure с помощью Шлюза Data Box, используя обычные процедуры передачи данных, как описано в [этом руководстве](data-box-gateway-deploy-add-shares.md).
- После отправки данных переведите хранилище на архивный уровень. Уровень больших двоичных объектов можно задать двумя способами: Azure PowerShell скрипт или политику управления жизненным циклом службы хранилища Azure.  
    - Если вы используете Azure PowerShell, выполните [эти действия](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier), чтобы переместить данные на архивный уровень.
    - Если вы используете управление жизненным циклом, выполните следующие действия, чтобы переместить данные на архивный уровень:
        - [Зарегистрируйтесь](/azure/storage/common/storage-lifecycle-management-concepts) для использования предварительной версии службы управления жизненным циклом BLOB-объектов, чтобы получить доступ к архивному уровню.
        - Используйте описанную ниже политику [архивации данных при приеме](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-after-ingest).
- Большие двоичные объекты, отмеченные как архивные, не могут быть изменены шлюзом, пока вы не переместите их на горячий или холодный уровень доступа. Если файл сохраняется в локальном хранилище, любые изменения в его локальной копии (в том числе удаление файла) не передаются на архивный уровень.
- Чтобы считать данные с архивного уровня, их нужно восстановить, переместив хранилище на горячий или холодный уровень. При [обновлении общей папки](data-box-gateway-manage-shares.md#refresh-shares) в шлюзе большой двоичный объект не восстанавливается.

Дополнительные сведения см. в статье [Управление жизненным циклом хранилища BLOB-объектов Azure (предварительная версия)](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Начальная массовая передача данных с последующей передачей добавочных данных

Если нужно выполнить массовую передачу большого объема данных с последующей передачей добавочных данных, Data Box и Шлюз Data Box следует использовать совместно. Data Box позволяет вне сети перенести большой объем данных (первоначальное заполнение), а Шлюз Data Box можно настроить для добавочной передачи текущих изменений по сети.

### <a name="seed-the-data-with-data-box"></a>Первоначальное заполнение с помощью Data Box

Выполните указанные ниже действия, чтобы скопировать данные в Data Box и отправить их в службу хранилища Azure.

1. [Заказ Data Box](/azure/databox/data-box-deploy-ordered).
2. [Настройка Data Box](/azure/databox/data-box-deploy-set-up).
3. [Копирование данных на Data Box по SMB](/azure/databox/data-box-deploy-copy-data).
4. [Передача Data Box и проверка отправки данных в Azure](/azure/databox/data-box-deploy-picked-up).
5. Когда передача данных в Azure завершится, все данные будут расположены в контейнерах хранилища Azure. В учетной записи хранения для Data Box перейдите к контейнеру больших двоичных объектов (и файлов) и убедитесь, что все данные успешно скопированы. Запомните или запишите имя контейнера, так как оно потребуется вам позже. Например, на следующем снимке экрана контейнер `databox` будет использоваться для добавочной передачи.

    ![Контейнер с данными в Data Box](media/data-box-gateway-use-cases/data-container1.png)

Эта массовая передача завершает этап начального заполнения.

### <a name="ongoing-feed-with-data-box-gateway"></a>Постоянный канал передачи через Шлюз Data Box

Выполните описанные ниже действия, чтобы настроить текущий прием данных в Шлюзе Data Box. 

1. Создайте общую облачную папку в Шлюзе Data Box. Все данные из этой папки будут автоматически отправляться в учетную запись хранения Azure. Перейдите к **списку общих папок** в ресурсе "Шлюз Data Box" и щелкните **+ Добавить общую папку**.

    ![Выбор элемента "+ Добавить общую папку"](media/data-box-gateway-use-cases/add-share1.png)

2. Убедитесь, что общая папка сопоставлена с контейнером, который содержит начальные данные. В разделе **Выберите контейнер BLOB-объектов** выберите вариант **Использовать существующий** и найдите в списке контейнер, в который ранее передали данные с помощью Data Box.

    ![Параметры общей папки](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. Завершив создание общей папки, обновите ее. С помощью этой операции локальная общая папка обновляется и заполняется содержимым из Azure.

    ![Обновление общей папки](media/data-box-gateway-use-cases/refresh-share1.png)

    При синхронизации общей папки Шлюз Data Box отправляет все добавочные изменения, которые внесены в файлы на клиентском компьютере.

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите [системные требования для Шлюза Data Box](data-box-gateway-system-requirements.md).
- Ознакомьтесь с общими сведениями об [ограничениях Шлюза Data Box](data-box-gateway-limits.md).
- Разверните [Шлюз Azure Data Box](data-box-gateway-deploy-prep.md) на портале Azure.