---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "77471567"
---
Если вы хотите, чтобы пользователи запрашивают второй фактор проверки подлинности перед предоставлением доступа, можно настроить многофакторную идентификацию Azure (MFA). MFA можно настроить отдельно для каждого пользователя или использовать MFA с помощью [условного доступа](../articles/active-directory/conditional-access/overview.md).

* Многофакторную идентификацию на пользователя можно включить без дополнительных затрат. При включении многофакторной идентификации для каждого пользователя пользователю будет предложено выполнить вторую проверку подлинности для всех приложений, привязанных к клиенту Azure AD. Пошаговые инструкции см. в [варианте 1](#peruser) .
* Условный доступ обеспечивает более детализированный контроль над тем, как следует повысить уровень второго фактора. Он может разрешить назначение MFA только VPN и исключить другие приложения, привязанные к клиенту Azure AD. Инструкции см. в разделе [вариант 2](#conditional) .