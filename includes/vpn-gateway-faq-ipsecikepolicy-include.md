---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/05/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 754a47b3692847957de7f3d666f4dc09dc309d25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025287"
---
### <a name="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus"></a>Поддерживается ли политика IPsec/IKE во всех номерах SKU VPN-шлюзов Azure?
Пользовательская политика IPsec/IKE поддерживается во всех SKU Azure, за исключением SKU "Базовый".

### <a name="how-many-policies-can-i-specify-on-a-connection"></a>Сколько политик можно указать для подключения?
Можно указать только ***одну*** комбинацию политик для каждого подключения.

### <a name="can-i-specify-a-partial-policy-on-a-connection-for-example-only-ike-algorithms-but-not-ipsec"></a>Можно ли указать частичную политику для подключения (например, только алгоритмы IKE без IPsec)?
Нет, следует указать все алгоритмы и параметры для IKE (основной режим) и IPsec (быстрый режим). Указать частичную политику нельзя.

### <a name="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy"></a>Какие алгоритмы и уровни стойкости ключей поддерживает настраиваемая политика?
В таблице ниже перечислены поддерживаемые алгоритмы шифрования и уровни стойкости ключей, которые могут настроить клиенты. Необходимо выбрать один вариант для каждого поля.

| **IPsec/IKEv2**  | **Параметры**                                                                   |
| ---              | ---                                                                           |
| Шифрование IKEv2 | AES256, AES192, AES128, DES3, DES                                             |
| Проверка целостности IKEv2  | SHA384, SHA256, SHA1, MD5                                                     |
| Группа DH         | DHGroup24, ECP384, ECP256, DHGroup14 (DHGroup2048), DHGroup2, DHGroup1, нет  |
| Шифрование IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, нет      |
| Целостность IPsec  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                            |
| Группа PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, нет                              |
| Время существования QM SA   | Секунды (целое число, **минимум 300**, по умолчанию — 27 000 с)<br>Килобайты (целое число, **минимум 1024**, по умолчанию — 102 400 000 КБ)           |
| Селектор трафика | UsePolicyBasedTrafficSelectors ($True/$False; по умолчанию — $False)                 |
|                  |                                                                               |

> [!IMPORTANT]
> 1. DHGroup2048 и PFS2048 — это такие же группы, как и группа Диффи-Хелмана **14** в протоколе IKE и IPsec PFS. Полное сопоставление см. в разделе о [группах Диффи — Хелмана](#DH).
> 2. В алгоритмах GCMAES необходимо указать одинаковую длину ключа и алгоритма для шифрования и целостности данных IPsec.
> 3. Время существования SA основного режима IKEv2 составляет 28 800 секунд на VPN-шлюзах Azure.
> 4. Время существования QM SA указывать необязательно. Если эти значения не указаны, по умолчанию используются значения 27 000 с (7,5 ч) и 102 400 000 КБ (102 ГБ).
> 5. UsePolicyBasedTrafficSelector — это необязательный параметр при подключении. Чтобы получить информацию о параметре UsePolicyBasedTrafficSelectors, см. следующий вопрос и ответ.

### <a name="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations"></a>Должны ли политика VPN-шлюза Azure и мои конфигурации локальных VPN-устройств полностью совпадать?
Ваша конфигурация локальных VPN-устройств должна совпадать со следующими алгоритмами и параметрами, указанными в политике Azure IPsec/IKE, или содержать их.

* Алгоритм шифрования IKE
* Алгоритм проверки целостности IKE
* Группа DH
* Алгоритм шифрования IPsec
* Алгоритм проверки целостности IPsec
* Группа PFS
* Селектор трафика ( * )

Время существования SA указывается исключительно в локальных спецификациях, эти значения не обязательно должны совпадать.

Если вы включили параметр **UsePolicyBasedTrafficSelectors**, необходимо обеспечить соответствующие селекторы трафика для VPN-устройства, которые определены с помощью всех комбинаций префиксов локальной сети (шлюза локальной сети) с префиксами виртуальной сети Azure, а не разрешать совпадение любого префикса с любым. Например, если префиксы локальной сети — 10.1.0.0/16 и 10.2.0.0/16, а префиксы виртуальной сети — 192.168.0.0/16 и 172.16.0.0/16, необходимо указать следующие селекторы трафика:
* 10.1.0.0/16 <====> 192.168.0.0/16;
* 10.1.0.0/16 <====> 172.16.0.0/16;
* 10.2.0.0/16 <====> 192.168.0.0/16;
* 10.2.0.0/16 <====> 172.16.0.0/16.

Дополнительные сведения см. в статье [Подключение VPN-шлюзов Azure к нескольким локальным VPN-устройствам на основе политики с помощью PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

### <a name="which-diffie-hellman-groups-are-supported"></a><a name ="DH"></a>Поддерживаемые группы Диффи-Хелмана
В таблице ниже перечислены поддерживаемые группы Диффи-Хелмана для протокола IKE (DHGroup) и IPsec (PFSGroup).

| **Группа Диффи-Хелмана**  | **DHGroup**              | **PFSGroup** | **Длина ключа** |
| ---                       | ---                      | ---          | ---            |
| 1                         | DHGroup1                 | PFS1         | MODP (768 бит)   |
| 2                         | DHGroup2                 | PFS2         | MODP (1024 бит)  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP (2048 бит)  |
| 19                        | ECP256                   | ECP256       | ECP (256 бит)    |
| 20                        | ECP384                   | ECP384       | ECP (384 бит)    |
| 24                        | DHGroup24                | PFS24        | MODP (2048 бит)  |
|                           |                          |              |                |

Дополнительные сведения см. на страницах [RFC 3526](https://tools.ietf.org/html/rfc3526) и [RFC 5114](https://tools.ietf.org/html/rfc5114).

### <a name="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways"></a>Заменяет ли настраиваемая политика стандартные наборы политик IPsec/IKE для VPN-шлюзов Azure?
Да, когда настраиваемая политика будет указана для подключения, VPN-шлюз Azure будет использовать только политику для подключения как инициатор IKE и отвечающее устройство IKE.

### <a name="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected"></a>Если удалить настраиваемую политику IPsec/IKE, подключение становится незащищенным?
Нет, подключение будет по-прежнему защищено с помощью IPsec/IKE. После удаления настраиваемой политики из подключения VPN-шлюз Azure вернется к [списку предложений IPsec/IKE по умолчанию](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) и возобновит подтверждение IKE для локального VPN-устройства.

### <a name="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection"></a>Прерывается ли VPN-подключение при добавлении или обновлении политики IPsec/IKE?
Да, это может привести к небольшому прерыванию работы (на несколько секунд), так как VPN-шлюз Azure будет прерывать существующее подключение и повторно запускать подтверждение IKE, чтобы повторно настроить туннель IPsec с новыми алгоритмами и параметрами шифрования. Чтобы минимизировать длительность прерывания, настройте для локального VPN-устройства совпадающие алгоритмы и уровни стойкости ключей.

### <a name="can-i-use-different-policies-on-different-connections"></a>Можно ли использовать разные политики для разных подключений?
Да. Настраиваемая политика применяется на уровне подключения. Можно создавать и применять разные политики IPsec/IKE для разных подключений. Можно также применять настраиваемые политики к подмножеству подключений. Оставшиеся подключения используют стандартные наборы политик IPsec/IKE Azure.

### <a name="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well"></a>Можно ли также использовать настраиваемую политику для подключения между виртуальными сетями?
Да, настраиваемую политику можно применять для подключений IPsec между локальными или виртуальными сетями.

### <a name="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources"></a>Нужно ли указывать одну и ту же политику для обоих ресурсов при подключении между виртуальными сетями?
Да. Туннель подключения между виртуальными сетями состоит из двух ресурсов Azure: для каждого направления используется один ресурс. Обоим ресурсам подключения следует назначить одну и ту же политику, иначе подключение между виртуальными сетями не будет установлено.

### <a name="what-is-the-default-dpd-timeout-value-can-i-specify-a-different-dpd-timeout"></a>Какое значение времени ожидания DPD по умолчанию? Можно ли указать другое время ожидания DPD?
Время ожидания DPD по умолчанию составляет 45 секунд. Можно указать другое значение времени ожидания DPD для каждого подключения IPsec или виртуальной сети в диапазоне от 9 до 3600 секунд.

### <a name="does-custom-ipsecike-policy-work-on-expressroute-connection"></a>Работает ли настраиваемая политика IPsec/IKE для подключения ExpressRoute?
Нет. Политика IPsec/IKE работает только для VPN-подключений типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть" через VPN-шлюзы Azure.

### <a name="how-do-i-create-connections-with-ikev1-or-ikev2-protocol-type"></a>Как создавать подключения с типом протокола IKEv1 или IKEv2?
Подключения IKEv1 можно создавать во всех SKU с VPN типа RouteBased, кроме SKU уровня "Базовый" и "Стандартный" и других [устаревших SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-skus-legacy#gwsku). При создании подключения можно указать тип протокола IKEv1 или IKEv2. Если тип протокола не указан, по умолчанию используется тип IKEv2 (там, где это применимо). Дополнительные сведения см. в документации по [командлетам PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?). Сведения о типах SKU и поддержке протоколов IKEv1 и IKEv2 см. в статье о [подключении шлюзов к VPN-устройствам на базе политик](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

### <a name="is-transit-between-between-ikev1-and-ikev2-connections-allowed"></a>Можно ли перейти с подключения типа IKEv1 на IKEv2 и обратно?
Да. Переход между типами подключений IKEv1 и IKEv2 поддерживается.

### <a name="can-i-have-ikev1-site-to-site-connections-on-basic-skus-of-routebased-vpn-type"></a>Можно ли использовать межсайтовые подключения IKEv1 на SKU категории "Базовый" с VPN типа RouteBased?
Нет. SKU категории "Базовый" не поддерживает такой возможности.

### <a name="can-i-change-the-connection-protocol-type-after-the-connection-is-created-ikev1-to-ikev2-and-vice-versa"></a>Можно ли сменить тип протокола после создания подключения (с IKEv1 на IKEv2 или обратно)?
Нет. Изменить тип протокола IKEv1 или IKEv2 после создания подключения невозможно. Вам потребуется удалить и снова создать подключение с протоколом нужного типа.

### <a name="where-can-i-find-more-configuration-information-for-ipsec"></a>Где можно найти дополнительные сведения о конфигурации для IPsec?
См. раздел [Настройка политики IPsec/IKE для подключений типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть"](../articles/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md).
