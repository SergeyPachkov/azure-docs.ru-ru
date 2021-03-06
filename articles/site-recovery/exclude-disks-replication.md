---
title: Исключение дисков из репликации с помощью Azure Site Recovery
description: Как исключить диски из репликации в Azure с помощью Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 15989fbfd65f758eb777c5170c217aba8707e0be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333670"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Исключение дисков из аварийного восстановления

В этой статье описывается, как исключить диски из репликации во время аварийного восстановления из локальной среды в Azure с помощью [Azure Site Recovery](site-recovery-overview.md). Исключить диски из репликации может потребоваться по ряду причин:

- неважные данные, находящиеся на исключенном диске, не реплицируются;
- оптимизация использования пропускной способности репликации или ресурсов на стороне целевого объекта;
- сохранение ресурсов хранилища и сети благодаря исключению из репликации данных, которые вам не нужны;
- на виртуальных машинах Azure достигнут предел репликаций Site Recovery.


## <a name="supported-scenarios"></a>Поддерживаемые сценарии

Можно исключать диски из репликации в случаях, указанных в таблице.

**Из Azure в Azure** | **VMware в VMware** | **Hyper-V в Azure** | **С физического сервера в Azure**
--- | --- | --- | ---
Да | Да | Да | Да

## <a name="exclude-limitations"></a>Ограничения исключения

**Ограничения** | **Виртуальные машины Azure** | **Виртуальные машины VMware** | **Виртуальные машины Hyper-V**
--- | --- | ---
**Типы дисков** | Можно исключать из репликации базовые диски.<br/><br/> Нельзя исключать диски операционной системы и динамические диски. Временные диски исключаются по умолчанию. | Можно исключать из репликации базовые диски.<br/><br/> Нельзя исключать диски операционной системы и динамические диски. | Можно исключать из репликации базовые диски.<br/><br/> Вы не можете исключать диски операционной системы. Мы не рекомендуем исключать динамические диски. Site Recovery не может определить, является ли виртуальный жесткий диск базовым или динамическим в гостевой виртуальной машине. Если все зависимые динамические диски не исключены, защищенный динамический диск на виртуальной машине, используемой для отработки отказа, будет считаться неисправным и данные на нем будут недоступны.
**Реплицируемый диск** | Нельзя исключить диск, который реплицируется.<br/><br/> Отключите и повторно включите репликацию для виртуальной машины. |  Нельзя исключить диск, который реплицируется. |  Нельзя исключить диск, который реплицируется.
**Служба "Мобильность" (VMware)** | Неприменимо | Можно исключать диски только на тех виртуальных машинах, на которых установлена служба "Мобильность".<br/><br/> Это означает, что необходимо вручную установить службу "Мобильность" на виртуальных машинах, для которых требуется исключить диски. Механизм принудительной установки использовать нельзя, так как он устанавливает службу "Мобильность" только после включения репликации. | Неприменимо
**Добавление и удаление** | Можно добавить управляемые диски на виртуальных машинах Azure с управляемыми дисками и с поддержкой репликации. Нельзя удалить диски из виртуальных машин Azure с поддержкой репликации. | Нельзя добавлять и удалять диски после включения репликации. Отключите и снова включите репликацию, чтобы добавить диск. | Нельзя добавлять и удалять диски после включения репликации. Отключите и снова включите репликацию.
**Тип отработки отказа** | Если приложению требуется исключаемый диск, после отработки отказа необходимо создать диск вручную, чтобы можно было запустить реплицируемое приложение.<br/><br/> Кроме того, можно создать диск во время отработки отказа виртуальной машины, интегрировав службу автоматизации Azure в план восстановления. | Если вы исключаете диск, который требуется приложению, создайте его вручную в Azure после отработки отказа. | Если вы исключаете диск, который требуется приложению, создайте его вручную в Azure после отработки отказа.
**Локальное восстановление размещения — диски, созданные вручную** | Неприменимо | **Для виртуальных машин Windows**: Диски, созданные в Azure вручную, не будут восстановлены в исходном расположении. Например, если вы выполните отработку отказа трех дисков и создадите два диска непосредственно на виртуальной машине Azure, то восстановление размещения будет выполнено только для трех дисков, задействованных при отработке отказа.<br/><br/> **Для виртуальных машин Linux**: Диски, создаваемые в Azure вручную, восстанавливаются в исходном расположении. Например, если для трех дисков выполняется отработка отказа, а еще два создаются в виртуальной машине Azure, восстановление будет выполнено для всех пяти дисков. Диски, созданные вручную при восстановлении расположения, исключать нельзя. | Диски, созданные в Azure вручную, не будут восстановлены в исходном расположении. Например, если вы выполните отработку отказа трех дисков и создадите два диска непосредственно на виртуальной машине Azure, то восстановление размещения будет выполнено только для трех дисков, задействованных при отработке отказа.
**Локальное восстановление размещения — исключенные диски** | Неприменимо | При восстановлении размещения на исходном компьютере в конфигурацию дисков виртуальной машины восстановления не будут включаться исключенные диски. Диски, которые были исключены из репликации VMware в Azure, не будут доступны на восстановленной виртуальной машине. | Если восстановление происходит в исходное расположение Hyper-V, конфигурация дисков восстановленной виртуальной машины остается такой же, как у диска исходной виртуальной машины. Диски, которые были исключены из репликации из сайта Hyper-V в Azure, доступны на восстановленной виртуальной машине.



## <a name="typical-scenarios"></a>Типичные сценарии

Хорошим примером потока данных для исключения могут являться записи в файл подкачки (pagefile.sys) и записи в файл tempdb Microsoft SQL Server. В зависимости от рабочей нагрузки и подсистемы хранилища файл подкачки и файл tempdb могут регистрировать значительный поток данных. Репликация этого типа данных в Azure требует больших ресурсов.

- Для оптимизации репликации виртуальной машины с одним виртуальным диском, содержащим как операционную систему, так и файл подкачки, можно выполнить следующие действия.
    1. Разделите один виртуальный диск на два диска. Один виртуальный диск будет содержать операционную систему, а второй — файл подкачки.
    2. Исключите диск с файлом подкачки из репликации.

- Для оптимизации репликации диска, содержащего файл tempdb Microsoft SQL Server и файл системной базы данных, можно сделать следующее.
    1. Файл системной базы данных и файл tempdb Microsoft SQL Server должны находиться на разных дисках.
    2. Исключите диск с файлом tempdb из репликации.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Пример 1: Исключение диска с файлом tempdb SQL Server

Рассмотрим процесс обработки исключений дисков, отработки отказа и отработки отказа для исходной виртуальной машины Windows с SQL Server **SalesDB***, для которой мы хотим исключить файл tempdb. 

### <a name="exclude-disks-from-replication"></a>Исключение дисков из репликации

На исходной виртуальной машине Windows SalesDB имеются следующие диски.

**Имя диска** | **Диск гостевой ОС** | **Буква диска** | **Тип данных диска**
--- | --- | --- | ---
DB-Disk0-OS | Диск 0 | C:\ | Диск операционной системы.
DB-Disk1| Диск 1 | D:\ | Системная база данных SQL и база данных пользователя Database1.
DB-Disk 2 (исключенный из репликации) | Диск 2 | E:\ | Временные файлы.
DB-Disk 3 (исключенный из репликации) | Диск 3 | F:\ | База данных tempdb SQL.<br/><br/> Путь к папке: F:\MSSQL\Data\. Запишите путь к папке до отработки отказа.
DB-Disk4 | Диск 4 |G:\ | База данных пользователя Database2

1. Мы включили репликацию для виртуальной машины SalesDB.
2. Мы исключили диски 2 и 3 из репликации, поскольку данные на этих дисках являются временными. 


### <a name="handle-disks-during-failover"></a>Обработка дисков во время отработки отказа

Так как диски не реплицируются, при отработке отказа в Azure они будут отсутствовать на виртуальной машине Azure, созданной после отработки отказа. В таблице ниже приведены диски виртуальной машины Azure.

**Диск гостевой ОС** | **Буква диска** | **Тип данных диска**
--- | --- | ---
Диск 0 | C:\ | Диск операционной системы.
Диск 1 | E:\ | Временное хранилище<br/><br/>Этот диск добавляет Azure. Так как диски 2 и 3 были исключены из репликации, "E:" является первой доступной буквой из списка. Azure назначает "E:" тому временного хранилища. Для всех реплицированных дисков буквы диска не изменятся.
Диск 2 | D:\ | Системная база данных SQL и база данных пользователя Database1
Диск 3 | G:\ | База данных пользователя Database2

В нашем примере, так как диск 3, то есть диск с tempdb SQL, был исключен из репликации и недоступен на виртуальной машине Azure, служба SQL находится в остановленном состоянии и ей требуется путь F:\MSSQL\Data. Этот путь можно создать несколькими способами. 

- После отработки отказа добавьте новый диск и назначьте путь к папке tempdb.
- Используйте существующий диск временного хранилища для пути к папке tempdb.

#### <a name="add-a-new-disk-after-failover"></a>Добавление нового диска после отработки отказа

1. Запишите пути к файлам tempdb.mdf и tempdb.ldf для базы данных SQL перед отработкой отказа.
2. На портале Azure добавьте новый диск в виртуальную машину Azure, используемую для отработки отказа. Диск должен иметь тот же размер (или больше), что и исходный диск базы данных tempdb SQL (Диск 3).
3. Войдите на виртуальную машину Azure.
4. В консоли управления (diskmgmt.msc) диском выполните инициализацию и форматирование добавленного диска.
5. Назначьте букву диска, используемую для диска базы данных tempdb SQL ("F:").
6. Создайте папку tempdb в томе "F:" (F:\MSSQL\Data).
7. Запустите службу SQL из консоли службы.

#### <a name="use-an-existing-temporary-storage-disk"></a>Использование существующего временного дискового хранилища 

1. Откройте командную строку.
2. Запустите SQL Server в режиме восстановления из командной строки.

    ```console
    Net start MSSQLSERVER /f / T3608
    ```

3. Запустите следующую команду sqlcmd, чтобы изменить путь к tempdb на новый.

    ```sql
    sqlcmd -A -S SalesDB        **Use your SQL DBname**
    USE master;     
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
    GO
    ```

4. Остановите службу Microsoft SQL Server.

    ```console
    Net stop MSSQLSERVER
    ```

5. Запустите службу Microsoft SQL Server.

    ```console
    Net start MSSQLSERVER
    ```

### <a name="vmware-vms-disks-during-failback-to-original-location"></a>Виртуальные машины VMware: диски во время восстановления размещения в исходное расположение

Теперь давайте посмотрим, как работать с дисками на виртуальных машинах VMware при восстановлении размещения в исходное локальное расположение.

- **Диски, созданные в Azure**. Так как в нашем примере используется виртуальная машина Windows, диски, созданные вручную в Azure, не реплицируются обратно в ваше расположение при восстановлении размещения или повторной защите виртуальной машины.
- **Диск временного хранилища в Azure**. Диски временного хранилища не реплицируются в локальные узлы.
- **Исключенные диски**. Диски, которые были исключены из репликации VMware в Azure, не будут доступны на локальной виртуальной машине после восстановления.

Перед восстановлением размещения виртуальных машин VMware в исходном расположении виртуальная машина Azure имеет следующую конфигурацию дисков.

**Диск гостевой ОС** | **Буква диска** | **Тип данных диска**
--- | --- | ---
Диск 0 | C:\ | Диск операционной системы.
Диск 1 | E:\ | Временное хранилище.
Диск 2 | D:\ | Системная база данных SQL и база данных пользователя Database1.
Диск 3 | G:\ | База данных пользователя Database2.

В таблице ниже приведены диски виртуальной машины VMware в исходном расположении после восстановления.

**Диск гостевой ОС** | **Буква диска** | **Тип данных диска**
--- | --- | ---
Диск 0 | C:\ | Диск операционной системы.
Диск 1 | D:\ | Системная база данных SQL и база данных пользователя Database1.
Диск 2 | G:\ | База данных пользователя Database2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Виртуальные машины Hyper-V: диски во время восстановления размещения в исходное расположение

Теперь давайте посмотрим, как работать с дисками на виртуальных машинах Hyper-V при восстановлении размещения в исходное локальное расположение.

- **Диски, созданные в Azure**. Диски, созданные вручную в Azure, не реплицируются обратно в ваше расположение при восстановлении размещения или повторной защите виртуальной машины.
- **Диск временного хранилища в Azure**. Диски временного хранилища не реплицируются в локальные узлы.
- **Исключенные диски**. После восстановления размещения конфигурация дисков виртуальной машины будет совпадать с конфигурацией дисков исходной виртуальной машины. Диски, которые были исключены из репликации из Hyper-V в Azure, будут доступны на восстановленной виртуальной машине.

Перед восстановлением размещения виртуальных машин Hyper-V в исходном расположении виртуальная машина Azure имеет следующую конфигурацию дисков.

**Диск гостевой ОС** | **Буква диска** | **Тип данных диска**
--- | --- | ---
Диск 0 | C:\ | Диск операционной системы.
Диск 1 | E:\ | Временное хранилище.
Диск 2 | D:\ | Системная база данных SQL и база данных пользователя Database1.
Диск 3 | G:\ | База данных пользователя Database2.

В таблице ниже представлены диски виртуальной машины Hyper-V в исходном расположении после плановой отработки отказа (восстановления размещения) из Azure в локальную службу Hyper-V.

**Имя диска** | **№ диска гостевой ОС** | **Буква диска** | **Тип данных диска**
 --- | --- | --- | ---
DB-Disk0-OS | Диск 0 |   C:\ | Диск операционной системы.
DB-Disk1 | Диск 1 | D:\ | Системная база данных SQL и база данных пользователя Database1.
DB-Disk2 (исключенный из репликации) | Диск 2 | E:\ | Временные файлы.
DB-Disk3 (исключенный из репликации) | Диск 3 | F:\ | База данных tempdb SQL.<br/><br/> Путь к папке: F:\MSSQL\Data\).
DB-Disk4 | Диск 4 | G:\ | База данных пользователя Database2


## <a name="example-2-exclude-the-paging-file-disk"></a>Пример 2. Исключение диска с файлом подкачки

Рассмотрим процесс обработки исключений дисков, отработки отказа и отработки отказа для исходной виртуальной машины Windows, для которой требуется исключить диск с файлом pagefile.sys как в случае его расположения на диске D, так и в случае расположения на альтернативном диске.


### <a name="paging-file-on-the-d-drive"></a>Файл подкачки на диске D

На исходной виртуальной машине имеются следующие диски.

**Имя диска** | **Диск гостевой ОС** | **Буква диска** | **Тип данных диска**
--- | --- | --- | ---
DB-Disk0-OS | Диск 0 | C:\ | Диск операционной системы
DB-Disk1 (исключен из репликации) | Диск 1 | D:\ | pagefile.sys
DB-Disk2 | Диск 2 | E:\ | Пользовательские данные 1
DB-Disk3 | Диск 3 | F:\ | Пользовательские данные 2

Ниже приведены параметры файла подкачки на исходной виртуальной машине.

![Снимок экрана: диалоговое окно виртуальной памяти с выделенной строкой D: диск [файл подкачки] с размером файла подкачки (МБ), равным 3000-7000.](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Мы включили репликацию для виртуальной машины.
2. Мы исключили диск DB-Disk1 из репликации.

#### <a name="disks-after-failover"></a>Диски после отработки отказа

В таблице ниже приведены диски виртуальной машины Azure после отработки отказа.

**Имя диска** | **№ диска операционной системы на виртуальной машине** | **Буква диска** | **Тип данных на диске**
--- | --- | --- | ---
DB-Disk0-OS | Диск 0 | C:\ | Диск операционной системы
DB-Disk1 | Диск 1 | D:\ | Временное хранилище (pagefile.sys) <br/><br/> Так как диск DB-Disk1 (D:) был исключен, "D:" является первой доступной буквой диска из списка.<br/><br/> Azure назначает "D:" тому временного хранилища.<br/><br/> Так как диск "D:" доступен, параметры файла подкачки виртуальной машины остаются неизменными.
DB-Disk2 | Диск 2 | E:\ | Пользовательские данные 1
DB-Disk3 | Диск 3 | F:\ | Пользовательские данные 2

Ниже приведены параметры файла подкачки на виртуальной машине Azure.

![Параметры файла подкачки на виртуальной машине Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Файл подкачки на другом диске (не "D:")

Рассмотрим пример, в котором файл подкачки находится не на диске "D".  

На исходной виртуальной машине имеются следующие диски.

**Имя диска** | **Диск гостевой ОС** | **Буква диска** | **Тип данных диска**
--- | --- | --- | ---
DB-Disk0-OS | Диск 0 | C:\ | Диск операционной системы
DB-Disk1 (исключен из репликации) | Диск 1 | G:\ | pagefile.sys
DB-Disk2 | Диск 2 | E:\ | Пользовательские данные 1
DB-Disk3 | Диск 3 | F:\ | Пользовательские данные 2

Ниже приведены параметры файла подкачки на локальной виртуальной машине.

![Параметры файла подкачки на локальной виртуальной машине](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Мы включили репликацию для виртуальной машины.
2. Мы исключили диск DB-Disk1 из репликации.

#### <a name="disks-after-failover"></a>Диски после отработки отказа

В таблице ниже приведены диски виртуальной машины Azure после отработки отказа.

**Имя диска** | **№ диска гостевой ОС** | **Буква диска** | **Тип данных диска**
--- | --- | --- | ---
DB-Disk0-OS | Диск 0  |C:\ | Диск операционной системы
DB-Disk1 | Диск 1 | D:\ | Временное хранилище<br/><br/> Так как "D:" является первой доступной буквой диска в списке, Azure назначит ее тому временного хранилища.<br/><br/> Для всех реплицированных дисков буква диска не изменится.<br/><br/> Так как диск "G:" недоступен, для файла подкачки система будет использовать диск "C:".
DB-Disk2 | Диск 2 | E:\ | Пользовательские данные 1
DB-Disk3 | Диск 3 | F:\ | Пользовательские данные 2

Ниже приведены параметры файла подкачки на виртуальной машине Azure.

![Снимок экрана: диалоговое окно виртуальной памяти с выделенной строкой диска C:, показывающая параметр размера файла подкачки "управляется системой".](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о рекомендациях для диска временного хранилища
    - [Сведения](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) об использовании SSD на виртуальных машинах Azure для хранения базы данных TempDB SQL Server и расширений буферного пула.
    - [Просмотрите](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) рекомендации по оптимизации производительности SQL Server на виртуальных машинах Azure.
- Настроив и запустив развертывание, вы можете ознакомиться с [дополнительными сведениями](failover-failback-overview.md) о различных типах обработки отказа.
