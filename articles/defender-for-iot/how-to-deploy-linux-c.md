---
title: Установка & развертывание агента Linux C
description: Сведения об установке и развертывании защитника безопасности на основе IoT C в Linux
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8c03f6b882c8b1a64c9f256493c5d586b5fa0f89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940054"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Развертывание агента безопасности на основе Azure IoT C для Linux

В этом руководство объясняется, как установить и развернуть защитник для агента безопасности на основе IoT C в Linux.

Из этого руководства вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * Установка
> * Проверка развертывания
> * Удаление агента
> * Диагностика

## <a name="prerequisites"></a>Предварительные требования

Сведения о других платформах и разновидностях агентов см. в разделе Выбор подходящего [агента безопасности](how-to-deploy-agent.md).

1. Для развертывания агента безопасности требуются права локального администратора на компьютере, который вы хотите установить (sudo).

1. [Создайте модуль безопасности](quickstart-create-security-twin.md) для устройства.

## <a name="installation"></a>Установка

Чтобы установить и развернуть агент безопасности, используйте следующий рабочий процесс:

1. Скачайте последнюю версию на компьютер с сайта [GitHub](https://aka.ms/iot-security-github-c).

1. Извлеките содержимое пакета и перейдите в папку _/СРК/инсталлатион_ .

1. Добавьте разрешения на выполнение **скрипта инсталлсекуритяжент** , выполнив следующую команду:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Затем выполните приведенную ниже команду:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Дополнительные сведения о параметрах проверки подлинности см. в [этой статье](concept-security-agent-authentication-methods.md).

Этот скрипт выполняет следующую функцию:

1. Установка необходимых компонентов.

1. Добавляет пользователя службы (с отключенным интерактивным входом).

1. Установка агента в качестве **управляющей программы** (предполагается, что устройство использует **systemd** для управления службой).

1. Настройка агента с предоставленными параметрами проверки подлинности.

Для получения дополнительной справки запустите скрипт с параметром –help:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Удаление агента

Чтобы удалить агент, запустите сценарий с параметром –-uninstall:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Устранение неполадок

Проверьте состояние развертывания, выполнив следующую команду:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Дальнейшие шаги

- Ознакомьтесь с [обзором](overview.md) службы "защитник для Интернета вещей"
- Дополнительные сведения о защитнике для [архитектуры](architecture.md) IOT
- Включите [службу](quickstart-onboard-iot-hub.md).
- Ознакомьтесь с [вопросами и](resources-frequently-asked-questions.md) ответами
- Общие сведения об [оповещениях системы безопасности](concept-security-alerts.md)
