---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 437b4ab62cc8c4903af88ca2f9632e89b953c798
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "68669793"
---
Чтобы включить потоковую передачу журналов для приложения-функции в Azure, сделайте следующее:

1. Выберите F1, чтобы открыть палитру команд, а затем найдите и выполните команду **Azure Functions: Start Streaming Logs** (Функции Azure: запуск потоковой передачи журналов).

1. Выберите свое приложение-функцию в Azure, а затем выберите вариант **Да**, чтобы включить ведение журнала приложений для приложения-функции.

1. Активируйте свои функции в Azure. Обратите внимание, что данные журнала отображаются в окне выходных данных в Visual Studio Code.

1. Когда все будет готово, не забудьте выполнить команду **Azure Functions: Stop Streaming Logs** (Функции Azure: остановка потоковой передачи журналов), чтобы отключить ведение журнала для приложения-функции.