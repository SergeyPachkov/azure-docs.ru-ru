---
title: Перенос больших ЭВМ на виртуальные машины Azure
description: Ресурсы вычислений Azure позволяют сравнить производительность мэйнфреймов, чтобы можно было перенести и модернизировать приложения IBM Z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 9c5941ec88cd793961ad66245d0dc0b5e0d7772f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86998941"
---
# <a name="move-mainframe-compute-to-azure"></a>Перемещение вычислений мэйнфреймов в Azure

Большие ЭВМ имеют репутацию для обеспечения высокой надежности и доступности и остаются доверенной магистралью многих предприятий. Как правило, они имеют практически неограниченную масштабируемость и вычислительные мощности. Однако некоторые предприятия выросли возможности самых крупных доступных мэйнфреймов. Если это кажется вам, Azure обеспечивает гибкость, доступность и экономию инфраструктуры.

Чтобы выполнять рабочие нагрузки мэйнфреймов на Microsoft Azure, необходимо знать, как возможности вычислений для мэйнфрейма сравниваются с Azure. На основе мэйнфрейма IBM Z14 (самая последняя модель на момент написания этой статьи) в этой статье рассказывается, как получить сравнимые результаты в Azure.

Чтобы приступить к работе, рассмотрим окружения параллельно. На следующем рисунке сравниваются среда мэйнфрейма для запуска приложений в среде размещения Azure.

![Службы и среды эмуляции Azure предлагают сравнимую поддержку и упрощают миграцию.](media/mainframe-compute-azure.png)

Возможности мэйнфреймов часто используются для систем оперативной обработки транзакций (OLTP), которые обрабатывают миллионы обновлений для тысяч пользователей. Эти приложения часто используют программное обеспечение для обработки транзакций, обработки экрана и записи формы. Они могут использовать систему управления сведениями о клиентах (CICS), систему управления данными (МГНОВЕНные данные) или пакет интерфейса транзакций (TIP).

Как показано на рисунке, эмулятор TPM в Azure может работать с рабочими нагрузками CICS и МГНОВЕНных сообщений. Эмулятор системы пакетной службы в Azure выполняет роль языка управления заданиями (ЖКЛ). Данные мэйнфреймов переносятся в базы данных Azure, такие как база данных SQL Azure. Службы Azure или другое программное обеспечение, размещенное на виртуальных машинах Azure, можно использовать для управления системой.

## <a name="mainframe-compute-at-a-glance"></a>Быстрый расчет больших ЭВМ

В мэйнфрейме Z14 процессоры располагаются в виде до четырех *нарисований*. *Ящик* — это просто кластер процессоров и наборов микросхем. Каждый ящик может иметь шесть микропроцессоров (CP), и каждый CP имеет 10 микросхем системного контроллера (SC). В терминологии Intel x86 имеется шесть сокетов на каждый, 10 ядер на сокет и четыре появляющихся лотка. Эта архитектура представляет собой приблизительный эквивалент 24-сокета и 240 ядер (максимум) для Z14.

В Fast Z14 CP установлена тактовая частота 5,2 ГГц. Как правило, Z14 поставляется со всеми CPs в поле. Они активируются по мере необходимости. Клиент обычно занимает не менее четырех часов вычислений в месяц, несмотря на фактическое использование.

Процессор мэйнфрейма можно настроить как один из следующих типов:

- Процессор общего назначения (GP)
- Интегрированный системный процессор z (Зиип)
- Интегрированное средство для процессора Linux (ИФЛ)
- Процессор системного помощника (SAP)
- Процессор интегрированного механизма подключения (ICF)

## <a name="scaling-mainframe-compute-up-and-out"></a>Масштабирование и увеличение масштаба мэйнфрейма

Мэйнфреймы IBM обеспечивают возможность масштабирования до 240 ядер (текущий размер Z14 для одной системы). Кроме того, мэйнфреймы IBM могут масштабироваться через функцию, называемую механизмом взаимосвязей (CF). CF позволяет нескольким системам мэйнфреймов одновременно получать доступ к одним и тем же данным. С помощью CF параллельно Сисплексная технология группирует процессоры мэйнфреймов в кластерах. При написании этого руководством функция Parallel Сисплекс поддерживала 32 групп процессоров 64. Таким образом можно сгруппировать до 2 048 процессоров, чтобы масштабировать производительность вычислений.

CF позволяет вычисленным кластерам совместно использовать данные с прямым доступом. Он используется для сведений о блокировке, сведений о кэше и списка общих ресурсов данных. Параллельный Сисплекс, использующий один или несколько CFs, можно рассматривать как масштабируемый кластер с общим доступом. Дополнительные сведения об этих функциях см. в разделе [Parallel сисплекс on IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) на веб-сайте IBM.

Приложения могут использовать эти функции для обеспечения масштабируемой производительности и высокой доступности. Сведения о том, как CICS может использовать Parallel Сисплекс с CF, можно скачать с [IBM CICS и механизма взаимосвязей: Помимо основ](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) редбук.

## <a name="azure-compute-at-a-glance"></a>Быстрый расчет Azure

Некоторые люди по ошибке считают, что серверы на базе Intel не так эффективны, как большие ЭВМ. Однако новые высокоплотные системы на базе технологии Intel имеют в качестве больших ЭВМ как большие объемы вычислений. В этом разделе описываются варианты инфраструктуры как услуги Azure (IaaS) для вычислений и хранения. Azure предоставляет также параметры платформы как услуги (PaaS), но в этой статье рассматриваются варианты, которые обеспечивают сравнимую емкость мэйнфреймов.

Виртуальные машины Azure обеспечивают мощность вычислений в диапазоне размеров и типов. В Azure виртуальный ЦП (виртуальных ЦП) примерно соответствует ядру на мэйнфрейме.

В настоящее время диапазон размеров виртуальных машин Azure — от 1 до 128 виртуальных ЦП. Типы виртуальных машин оптимизированы для конкретных рабочих нагрузок. Например, в следующем списке показаны типы виртуальных машин (Текущая версия на момент написания этой статьи) и рекомендуемые способы их использования:

| Размер     | Тип и описание                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Серия D | Общее назначение с 64 виртуальных ЦП и тактовой частотой 3,5 ГГц                           |
| Серия E | Оптимизированная память с размером до 64 виртуальных ЦП                                                 |
| Серия F | Оптимизированные для вычислений с тактовой частотой до 64 виртуальных ЦП и 3... 7 ГГц                       |
| Серия H | Оптимизировано для высокопроизводительных вычислительных приложений (HPC)                          |
| Серия L | Хранилище, оптимизированное для высокопроизводительных приложений, поддерживающих такие базы данных, как NoSQL |
| Серия M | Самые крупные виртуальные машины, оптимизированные для вычислений и в памяти, до 128 виртуальных ЦП                        |

Дополнительные сведения о доступных виртуальных машинах см. в статье [серия виртуальных машин](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Мэйнфрейм Z14 может иметь до 240 ядер. Однако большие ЭВМ Z14 практически никогда не используют все ядра для одного приложения или рабочей нагрузки. Вместо этого мэйнфрейм разделяет рабочие нагрузки на логические секции (Лпарс), а Лпарс имеет рейтинги — MIPS (миллионы инструкций в секунду) или MSU (миллион единиц обслуживания). При определении сравнимого размера виртуальной машины, необходимого для запуска рабочей нагрузки мэйнфрейма в Azure, учитывайте значение в рейтинге MIPS (или MSU).

Ниже приведены общие оценки.

-   150 MIPS на виртуальных ЦП

-   1 000 MIPS на процессор

Чтобы определить правильный размер виртуальной машины для конкретной рабочей нагрузки в ЛПАР, сначала Оптимизируйте виртуальную машину для рабочей нагрузки. Затем определите необходимое количество виртуальных ЦП. Консервативная оценка — 150 MIPS на виртуальных ЦП. Например, на основе этой оценки виртуальная машина серии F с 16 виртуальных ЦП может легко поддерживать рабочую нагрузку IBM DB2, поступающие от ЛПАР с 2 400 MIPS.

## <a name="azure-compute-scale-up"></a>Масштабирование вычислений Azure

Виртуальные машины серии M можно масштабировать до 128 виртуальных ЦП (на момент написания этой статьи). Если вы используете консервативную оценку 150 MIPS на виртуальных ЦП, виртуальная машина серии M соответствует примерно 19 000 MIPS. Общее правило для оценки MIPS для мэйнфреймов — 1 000 MIPS на процессор. Мэйнфрейм Z14 может иметь до 24 процессоров и обеспечивать около 24 000 MIPS для одной системы мэйнфреймов.

У самой крупной Z14 мэйнфрейма примерно 5 000 MIPS больше, чем максимальная виртуальная машина, доступная в Azure. Однако важно сравнить, как развертываются рабочие нагрузки. Если в системе мэйнфрейма есть как приложение, так и реляционная база данных, они обычно развертываются на одном физическом мэйнфрейме — каждый в своем собственном ЛПАР. Одно и то же решение в Azure часто развертывается с помощью одной виртуальной машины для приложения и отдельной виртуальной машины подходящего размера для базы данных.

Например, если M64 виртуальных ЦП система поддерживает приложение, а для базы данных используется M96 виртуальных ЦП, то требуется примерно 150 виртуальных ЦП — или примерно 24 000 MIPS, как показано на следующем рисунке.

![Сравнение развертываний рабочих нагрузок 24 000 MIPS](media/mainframe-compute-mips.png)

Подход заключается в переносе Лпарс на отдельные виртуальные машины. Затем Azure легко масштабируется до размера, необходимого для большинства приложений, развернутых в одной системе мэйнфреймов.

## <a name="azure-compute-scale-out"></a>Масштабное масштабирование вычислений Azure

Одним из преимуществ решения на основе Azure является возможность масштабирования. Масштабирование делает практически неограниченную доступную для приложения емкость вычислений. Azure поддерживает несколько методов масштабирования мощности вычислений:

- **Балансировка нагрузки в кластере.** В этом сценарии приложение может использовать [подсистему балансировки нагрузки](../../../../load-balancer/load-balancer-overview.md) или диспетчер ресурсов для распределения рабочей нагрузки между несколькими виртуальными машинами в кластере. Если требуется больше ресурсов вычислений, в кластер добавляются дополнительные виртуальные машины.

- **Масштабируемые наборы виртуальных машин.** В этом сценарии приложение может масштабироваться до дополнительных [ресурсов вычислений](../../../../virtual-machine-scale-sets/overview.md) на основе использования виртуальной машины. Когда спрос оказывается, количество виртуальных машин в масштабируемом наборе также может быть отключено, что обеспечивает эффективное использование мощности вычислений.

- **Масштабирование PaaS.** Предложения Azure PaaS позволяют масштабировать ресурсы вычислений. Например, [Azure Service Fabric](../../../../service-fabric/service-fabric-overview.md) выделяет ресурсы вычислений для удовлетворения роста объема запросов.

- **Кластеры Kubernetes.** Приложения в Azure могут использовать [кластеры Kubernetes](../../../../aks/concepts-clusters-workloads.md) для служб вычислений для указанных ресурсов. Служба Azure Kubernetes Service (AKS) — это управляемая служба, которая управляет узлами, пулами и кластерами Kubernetes в Azure.

Чтобы выбрать правильный метод масштабирования ресурсов вычислений, важно понимать, как в Azure и мэйнфреймах различаются. Ключом является то, как — или, если — данные совместно используются ресурсами вычислений. В Azure данные (по умолчанию) обычно не являются общими для нескольких виртуальных машин. Если для нескольких виртуальных машин в масштабируемом кластере требуется общий доступ к данным, общие данные должны находиться в ресурсе, поддерживающем эту функцию. В Azure для совместного использования данных требуется хранилище, как описано в следующем разделе.

## <a name="azure-compute-optimization"></a>Оптимизация вычислений Azure

Вы можете оптимизировать каждый уровень обработки в архитектуре Azure. Используйте наиболее подходящий тип виртуальных машин и функций для каждой среды. На следующем рисунке показана одна потенциальная схема развертывания виртуальных машин в Azure для поддержки приложения CICS, использующего DB2. На основном сайте виртуальные машины для производственной, подготовительной и тестовой версии приложения развертываются с высоким уровнем доступности. Дополнительный сайт используется для резервного копирования и аварийного восстановления.

Каждый уровень может также предоставлять соответствующие службы аварийного восстановления. Например, для виртуальных машин, используемых в рабочей среде, и виртуальных машин с базами данных, возможно, потребуется "горячее" или "теплое" восстановление. А виртуальные машины для разработки и тестирования поддерживают "холодное" восстановление.

![Развертывание с высоким уровнем доступности, поддерживающее аварийное восстановление](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Миграция мэйнфреймов](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Повторное размещение мэйнфреймов на виртуальных машинах Azure](../overview.md)
- [Перемещение хранилища мэйнфреймов в Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Ресурсы по IBM

- [Parallel Сисплекс в IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS и механизм взаимосвязей: Помимо основ](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Создание обязательных пользователей для установки Db2 pureScale Feature](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt: команда для создания экземпляра](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Решение для кластерной базы данных DB2 Пурескале](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure для государственных организаций

- [Microsoft Azure для государственных организаций облачные приложения для мэйнфреймов](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft и FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Дополнительные материалы по миграции

- [Виртуальный центр обработки данных Azure: руководство по миграции методом lift-and-shift](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [iSCSI GlusterFS](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
