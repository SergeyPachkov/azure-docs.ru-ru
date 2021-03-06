---
title: Политика поддержки кластеров Azure Red Hat OpenShift 4
description: Общие сведения о требованиях политики поддержки для Red Hat OpenShift 4.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: e396cfa032a3030467b2e2318d61393713894cd4
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628226"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Политика поддержки Azure Red Hat OpenShift

Некоторые конфигурации кластеров Azure Red Hat OpenShift 4 могут влиять на поддержку вашего кластера. Azure Red Hat OpenShift 4 позволяет администраторам кластеров вносить изменения во внутренние компоненты кластеров, однако поддерживаются не все изменения. Представленная ниже политика поддержки объясняет, какие изменения нарушают политику и ведут к прекращению поддержки от корпорации Майкрософт и Red Hat.

> [!NOTE]
> Функции, представленные на платформе контейнеров OpenShift как предварительные версии технологий, в Azure Red Hat OpenShift не поддерживаются.

## <a name="cluster-configuration-requirements"></a>Требования к настройке кластеров

* Все операторы кластеров OpenShift должны оставаться в управляемом состоянии. Список операторов кластеров можно получить, выполнив команду `oc get clusteroperators`.
* Кластер должен иметь минимум один рабочий узел. Не масштабировать рабочие процессы кластера в ноль.
* Не удаляйте и не изменяйте службы кластеров Prometheus и Alertmanager.
* Не удаляйте правила службы Alertmanager.
* Не удаляйте и не изменяйте журналы службы OpenShift Azure Red Hat (pod-объекты mdsd).
* Не удаляйте и не изменяйте секретный код для извлечения кластера arosvc.azurecr.io.
* Все виртуальные машины кластера должны иметь прямой исходящий доступ к Интернету по крайней мере для конечных точек Azure Resource Manager (ARM) и Service Logging (Geneva).  Ни одна из форм прокси-сервера HTTPS не поддерживается.
* Не изменяйте конфигурацию DNS виртуальной сети кластера. Необходимо использовать сопоставитель Azure DNS по умолчанию.
* Не переопределяйте ни один из объектов MachineConfig кластера (например, конфигурацию kubelet) никаким образом.
* Не устанавливайте параметры Унсуппортедконфиговерридес. Установка этих параметров предотвращает обновление дополнительных версий.
* Служба Azure Red Hat OpenShift обращается к кластеру через службу приватных каналов.  Не удаляйте и не изменяйте доступ к службам.
* Вычислительные узлы, кроме RHCOS, не поддерживаются. Например, вычислительный узел RHEL использовать нельзя.

## <a name="supported-virtual-machine-sizes"></a>Поддерживаемые размеры виртуальных машин

Azure Red Hat OpenShift 4 поддерживает экземпляры рабочих узлов на виртуальных машинах следующих размеров:

### <a name="general-purpose"></a>Общего назначения

|Series|Размер|vCPU|Память: ГиБ|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>Оптимизированные для операций в памяти

|Series|Размер|vCPU|Память: ГиБ|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>Оптимизированные для вычислений

|Series|Размер|vCPU|Память: ГиБ|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### <a name="master-nodes"></a>Главные узлы

|Series|Размер|vCPU|Память: ГиБ|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
