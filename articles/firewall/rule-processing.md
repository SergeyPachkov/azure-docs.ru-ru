---
title: Логика обработки правил Брандмауэра Azure
description: В Брандмауэре Azure есть правила преобразования сетевых адресов (NAT), правила сети и правила приложений. Каждое из них обрабатывается в соответствии с типом.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 84110e749dac9267e994385aa5f6d05e3ba224a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87087549"
---
# <a name="configure-azure-firewall-rules"></a>Настройка правил брандмауэра Azure
Правила NAT, сетевые правила и правила приложений можно настроить в брандмауэре Azure. Коллекции правил обрабатываются в соответствии с типом правила в порядке приоритета, а более низкие числа — от 100 до 65 000. Имя коллекции правил может содержать только буквы, цифры, символы подчеркивания, точки или дефисы. Оно должно начинаться с буквы или цифры и заканчиваться буквой, цифрой или символом подчеркивания. Максимальная длина имени — 80 символов.

Рекомендуется первоначально разместить номера приоритетов наборов правил с шагом 100 (100, 200, 300 и т. д.), чтобы при необходимости можно было добавлять дополнительные коллекции правил.

> [!NOTE]
> Если включена фильтрация на основе аналитики угроз, эти правила имеют наивысший приоритет и всегда обрабатываются первыми. Фильтрация с использованием логики операций с угрозами может отклонять трафик перед обработкой настроенных правил. Дополнительные сведения см. в статье [Фильтрация на основе интеллектуального анализа угроз в Azure](threat-intel.md).

## <a name="outbound-connectivity"></a>Исходящее подключение

### <a name="network-rules-and-applications-rules"></a>Правила сети и правила приложений

При настройке правил сети и правил приложения правила сети применяются в порядке приоритета перед правилами приложения. Затем выполнение правил завершается. Поэтому при обнаружении совпадения в сетевом правиле никакие другие правила не обрабатываются.  Если правила сети не совпадают, а протоколом является HTTP, HTTPS или MSSQL, пакет затем оценивается правилами приложения в порядке приоритета. Если совпадений не найдено, пакет оценивается по [коллекции правил инфраструктуры](infrastructure-fqdns.md). Если правило по-прежнему не выполняется, пакет отклоняется по умолчанию.

## <a name="inbound-connectivity"></a>Входящее подключение

### <a name="nat-rules"></a>Правила преобразования сетевых адресов

Входящее подключение к Интернету можно включить, настроив преобразование сетевых адресов назначения (ДНАТ), как описано в разделе [руководство. Фильтрация входящего трафика с помощью ДНаТ брандмауэра Azure с использованием портал Azure](tutorial-firewall-dnat.md). Правила NAT применяются по приоритету перед правилами сети. Если совпадение найдено, добавляется неявно соответствующее правило сети, чтобы разрешить преобразованный трафик. Чтобы переопределить эту реакцию, явно добавьте коллекцию правил сети с запрещающими правилами, которые соответствуют преобразованному трафику.

Правила приложения не применяются к входящим подключениям. Поэтому, если требуется отфильтровать входящий трафик HTTP/S, следует использовать брандмауэр веб-приложения (WAF). Дополнительные сведения см. в статье [что такое брандмауэр веб-приложения Azure?](../web-application-firewall/overview.md)

## <a name="examples"></a>Примеры

В следующих примерах показаны результаты некоторых из этих сочетаний правил.

### <a name="example-1"></a>Пример 1

Подключение к google.com разрешено из-за соответствующего правила сети.

**Правило сети**

- Действие: Allow


|name  |Протокол  |Тип исходного значения  |Источник  |Тип назначения  |Адрес назначения  |Конечные порты|
|---------|---------|---------|---------|----------|----------|--------|
|Разрешить — Интернет     |TCP|IP-адрес|*|IP-адрес|*|80, 443

**Правило приложения**

- Действие: Deny

|name  |Тип исходного значения  |Источник  |Протокол: порт|Целевые полные доменные имена|
|---------|---------|---------|---------|----------|----------|
|Deny — Google     |IP-адрес|*|http: 80, HTTPS: 443|google.com

**Результат**

Подключение к google.com разрешено, так как пакет соответствует правилу *разрешенных веб* -сетей. На этом этапе обработка правил останавливается.

### <a name="example-2"></a>Пример 2

Трафик SSH запрещен, так как он блокирует коллекцию *правил сети с* более высоким приоритетом.

**Коллекция правил сети 1**

- Имя: Allow-Collection
- Приоритет: 200
- Действие: Allow

|name  |Протокол  |Тип исходного значения  |Источник  |Тип назначения  |Адрес назначения  |Конечные порты|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-SSH     |TCP|IP-адрес|*|IP-адрес|*|22

**Коллекция правил сети 2**

- Имя: Deny-Collection.
- Приоритет. 100
- Действие: Deny

|name  |Протокол  |Тип исходного значения  |Источник  |Тип назначения  |Адрес назначения  |Конечные порты|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH     |TCP|IP-адрес|*|IP-адрес|*|22

**Результат**

SSH-подключения запрещены, так как коллекция сетевых правил с более высоким приоритетом блокирует его. На этом этапе обработка правил останавливается.

## <a name="rule-changes"></a>Изменения правил

Если изменить правило, чтобы запретить ранее разрешенный трафик, все соответствующие существующие сеансы будут удалены.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [развернуть и настроить брандмауэр Azure](tutorial-firewall-deploy-portal.md).