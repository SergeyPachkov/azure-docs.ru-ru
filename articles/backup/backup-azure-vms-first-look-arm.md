---
title: Создание резервной копии виртуальной машины Azure на основе параметров виртуальной машины
description: Из этой статьи вы узнаете, как выполнить резервное копирование отдельной виртуальной машины Azure или нескольких виртуальных машин Azure с помощью службы Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 55b71d2a2901cdde984df3ebfd68a2a643b78b74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89667522"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Создание резервной копии виртуальной машины Azure на основе параметров виртуальной машины

В этой статье описывается, как выполнять резервное копирование виртуальных машин Azure с помощью службы [Azure Backup](backup-overview.md). Вы можете создать резервную копию виртуальной машины Azure двумя способами:

- Одна виртуальная машина Azure. инструкции в этой статье описывают создание резервной копии виртуальной машины Azure непосредственно из параметров виртуальной машины.
- Несколько виртуальных машин Azure. Вы можете настроить хранилище служб восстановления и настроить резервное копирование для нескольких виртуальных машин Azure. Для этого сценария следуйте инструкциям [из этой статьи](backup-azure-arm-vms-prepare.md).

## <a name="before-you-start"></a>Перед началом работы

1. [Узнайте](backup-architecture.md#how-does-azure-backup-work) принцип работы резервного копирования и [проверьте](backup-support-matrix.md#azure-vm-backup-support) требования к поддержке.
2. Получите [общие сведения](backup-azure-vms-introduction.md) о резервном копировании виртуальной машины Azure.

### <a name="azure-vm-agent-installation"></a>Установка агента виртуальной машины Azure

Для резервного копирования виртуальных машин Azure Azure Backup устанавливает расширение на агенте виртуальной машины, работающем на компьютере. Если виртуальная машина была создана из образа Azure Marketplace, агент будет запущен. В некоторых случаях, например, при создании пользовательской виртуальной машины или миграции компьютера из локальной среды, может потребоваться установить агент вручную.

- Если необходимо устанавливать агент виртуальной машины, следуйте инструкциям для виртуальных машин [Windows](../virtual-machines/extensions/agent-windows.md) или [Linux](../virtual-machines/extensions/agent-linux.md).
- После установки агента при включении резервного копирования Azure Backup устанавливает расширение резервного копирования для агента. Служба обновляет и применяет исправления к расширению без вмешательства пользователя.

## <a name="back-up-from-azure-vm-settings"></a>Создание резервной копии на основе параметров виртуальной машины Azure

1. Войдите на [портал Azure](https://portal.azure.com/).
2. Выберите **все службы** и в поле Фильтр введите **виртуальные машины**, а затем выберите **виртуальные машины**.
3. В списке виртуальных машин выберите виртуальную машину, для которой нужно создать резервную копию.
4. В меню Виртуальная машина выберите пункт **резервное копирование**.
5. В области **Хранилище служб восстановления** сделайте следующее:
   - Если у вас уже есть хранилище, выберите **выбрать существующий**и выберите хранилище.
   - Если у вас нет хранилища, выберите **создать**. Укажите имя для хранилища. Оно создается в том же регионе и в той же группе ресурсов, что и виртуальная машина. Эти параметры нельзя изменить при включении резервного копирования непосредственно из параметров виртуальной машины.

        ![Мастер включения резервного копирования](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. В области **Выбор политики резервного копирования**выполните одно из следующих действий.

   - Оставьте политику по умолчанию. Она создает резервную копию виртуальной машины один раз в день в указанное время и сохраняет резервные копии в хранилище в течение 30 дней.
   - Выберите существующую политику архивации, если она есть.
   - Создайте политику и определите ее параметры.  

       ![Выбор политики резервного копирования](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Выберите **Включить резервное копирование**. Это связывает политику резервного копирования с виртуальной машиной.

    ![Кнопка "Включить резервное копирование"](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Вы можете отслеживать ход настройки в уведомлениях на портале.
9. После завершения задания в меню Виртуальная машина выберите пункт **резервное копирование**. На странице отображается состояние резервного копирования для виртуальной машины, а также сведения о точках восстановления, выполняемых заданиях и выдаваемых предупреждениях.

   ![Статус резервного копирования](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. После включения резервного копирования выполняется начальное резервное копирование. Вы можете немедленно запустить начальное резервное копирование или дождаться его запуска в соответствии с расписанием резервного копирования.
    - Пока начальное резервное копирование не будет выполнено, для параметра **Состояние последней архивации** будет отображаться значение **Warning (Initial backup pending)** (Предупреждение (ожидание начального резервного копирования)).
    - Чтобы узнать, когда будет выполняться Следующая запланированная архивация, выберите имя политики архивации.

## <a name="run-a-backup-immediately"></a>Немедленное выполнение резервного копирования

1. Чтобы немедленно запустить резервное копирование, в меню Виртуальная машина **выберите Архивация резервная копия**  >  **Backup now**.

    ![Выполнение резервного копирования](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. В поле **создать резервную копию**используйте элемент управления Calendar, чтобы выбрать, пока точка восстановления не будет храниться > и **ОК**.

    ![День, до которого сохраняется резервная копия](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Уведомления портала сообщают о том, что было запущено задание резервного копирования. Чтобы отслеживать ход архивации, выберите **Просмотреть все задания**.

## <a name="back-up-from-the-recovery-services-vault"></a>Создание резервной копии из хранилища Служб восстановления

Следуйте инструкциям в [этой статье](backup-azure-arm-vms-prepare.md) , чтобы включить резервное копирование для виртуальных машин Azure, настроив Azure Backup хранилище служб восстановления и включив резервное копирование в хранилище.

## <a name="next-steps"></a>Дальнейшие шаги

- Если у вас есть проблемы при работе с любой из процедур, описанных в этой статье, обратитесь к [руководству по устранению неполадок](backup-azure-vms-troubleshoot.md).
- [Дополнительные сведения об](backup-azure-manage-vms.md) управлении резервными копиями.
