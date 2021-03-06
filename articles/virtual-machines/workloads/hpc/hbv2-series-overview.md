---
title: Обзор виртуальных машин серии HBv2. виртуальные машины Azure | Документация Майкрософт
description: Сведения о размере виртуальной машины серии HBv2 в Azure.
services: virtual-machines
author: vermagit
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 48366f205ed8eb2d179bdc39c8da3d673f066a69
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332642"
---
# <a name="hbv2-series-virtual-machine-overview"></a>Обзор виртуальных машин серии HBv2 

 
Максимизация производительности приложений с высокой производительностью (HPC) в AMD ЕПИК требует продуманного подхода к локализации памяти и размещению процессов. Ниже мы рассмотрим архитектуру AMD ЕПИК и нашу реализацию ИТ в Azure для приложений HPC. Мы будем использовать термин **пнума** для ссылки на физический домен NUMA и **внума** для ссылки на виртуализированный домен NUMA. 

Физически сервер [серии HBv2](../../hbv2-series.md) — это 2 * 64-ядерные цп ЕПИК 7742, что составляет всего 128 физических ядер. Эти ядра 128 делятся на Пнумаые домены 32 (16 на сокет), каждый из которых имеет 4 ядра и называется "AMD" в качестве **ядра комплексной сложной** (или **КККС**). Каждый КККС имеет собственный кэш L3, что позволяет операционной системе видеть границу Пнума/Внума. Четыре смежных Ккксс имеют доступ к 2 каналам физической памяти DRAM. 

Чтобы предоставить службе низкоуровневой оболочки Azure возможность работы, не мешая работе виртуальной машины, мы резервируем физические домены Пнума 0 и 16 (т. е. Первый КККС каждого разъема ЦП). Все остальные 30 Пнума доменов назначаются виртуальной машине, где они становятся Внума. Таким образом, виртуальная машина будет видеть:

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` количество ядер на виртуальную машину 

Сама виртуальная машина не осведомлена о том, что Пнума 0 и 16 зарезервированы. Он перечислит Внума, который он видит как 0-29, с 15 Внума на сокет, Внума 0-14 на Всоккет 0 и Внума 15-29 на Всоккет 1. В следующем разделе приведены инструкции по оптимальному запуску приложений MPI на этом асимметричном макете NUMA. 

Закрепление процессов будет работать на виртуальных машинах серии HBv2, так как для гостевой виртуальной машины мы предоставляем базовую Кристалл "как есть". Мы настоятельно рекомендуем закреплять процесс для обеспечения оптимальной производительности и согласованности. 


## <a name="hardware-specifications"></a>Характеристики оборудования 

| Спецификации оборудования          | Виртуальная машина серии HBv2                   | 
|----------------------------------|----------------------------------|
| Ядра                            | 120 (SMT отключено)               | 
| ЦП                              | AMD ЕПИК 7742                    | 
| Частота ЦП (не AVX)          | ~ 3,1 ГГц (один + все ядра)    | 
| Память                           | 4 ГБ/ядро (всего 480 ГБ)         | 
| Локальный диск                       | 960 Гб NVMe (Block), SSD 480 ГБ (файл подкачки) | 
| Infiniband;                       | 200 Гбит/с ЕДР Mellanox ConnectX-6 | 
| Сеть                          | 50 ГБ/с Ethernet (40 ГБ/с) для использования во второй Смартник Azure | 


## <a name="software-specifications"></a>Спецификации программного обеспечения 

| Спецификации программного обеспечения     | Виртуальная машина серии HBv2                                            | 
|-----------------------------|-----------------------------------------------------------|
| Максимальный размер задания MPI            | 36000 ядер (300 ВМ в одном масштабируемом наборе виртуальных машин с singlePlacementGroup = true) |
| Поддержка MPI                 | HPC-X, Intel MPI, Опенмпи, MVAPICH2, МПИЧ, платформа MPI  |
| Дополнительные платформы       | Унифицированная связь X, либфабрик, ПГАС                  |
| Поддержка хранилища Azure       | Диски уровня "Стандартный" и "Премиум" (максимум 8 дисков)              |
| Поддержка ОС для SRIOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +           |
| Поддержка Orchestrator        | Циклеклауд, пакетная                                         | 


## <a name="next-steps"></a>Дальнейшие шаги

- Узнайте больше о [архитектуре AMD ЕПИК](https://bit.ly/2Epv3kC) и [архитектурах с несколькими микросхемами](https://bit.ly/2GpQIMb). Дополнительные сведения см. в описании [рекомендаций по настройке HPC для процессоров AMD ЕПИК](https://bit.ly/2T3AWZ9).
- Ознакомьтесь с последними объявлениями и некоторыми примерами HPC в [блогах сообщества разработчиков Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Сведения о более высоком уровне архитектурного представления выполнения рабочих нагрузок HPC см. в статье [Высокопроизводительные вычисления (HPC) в Azure](/azure/architecture/topics/high-performance-computing/).