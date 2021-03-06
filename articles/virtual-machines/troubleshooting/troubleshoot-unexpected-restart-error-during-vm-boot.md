---
title: Запуск ОС — компьютер неожиданно перезагружается или произошла непредвиденная ошибка
description: В этой статье приводятся инструкции по устранению проблем, когда при установке Windows происходит непредвиденная перезагрузка или Ошибка виртуальной машины.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: 186b1c46303be59e191a1754361e07a2003b997a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87036188"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Запуск ОС — компьютер неожиданно перезагружается или произошла непредвиденная ошибка

В этой статье приведены инструкции по устранению проблем, в которых виртуальная машина (VM) испытывает непредвиденную перезагрузку или ошибку при установке Windows.

## <a name="symptom"></a>Симптом

При использовании [диагностики загрузки](./boot-diagnostics.md) для просмотра снимка экрана виртуальной машины вы увидите, что на снимке экрана отображается сбой установки Windows со следующей ошибкой:

**Компьютер неожиданно перезапустился, или произошла непредвиденная ошибка. Установка Windows не может быть продолжена. Чтобы установить Windows, нажмите кнопку "ОК", чтобы перезагрузить компьютер, а затем перезапустите установку.**

![Ошибка во время установки Windows: компьютер был неожиданно перезагружен или произошла непредвиденная ошибка. Установка Windows не может быть продолжена. Чтобы установить Windows, нажмите кнопку "ОК", чтобы перезагрузить компьютер, а затем перезапустите установку.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Ошибка при запуске служб программой установки Windows: компьютер был неожиданно перезагружен или произошла непредвиденная ошибка. Установка Windows не может быть продолжена. Чтобы установить Windows, нажмите кнопку "ОК", чтобы перезагрузить компьютер, а затем перезапустите установку.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Причина

Компьютер пытается выполнить начальную загрузку [обобщенного образа](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation), но при этом возникают проблемы из-за обработки пользовательского файла ответов (unattend.xml). Пользовательские файлы ответов не поддерживаются в Azure. 

Файл ответов — это специальный XML-файл, в котором содержатся определения параметров и значения параметров конфигурации, которые необходимо автоматизировать во время установки операционной системы Windows Server. Параметры конфигурации включают в себя инструкции по разбивать диски на разделы, где можно найти устанавливаемый образ Windows, ключи продуктов для применения и другие команды, которые вы хотите запустить.

В Azure пользовательские файлы ответов не поддерживаются. Если вы указали пользовательский файл **Unattend.xml** с помощью `sysprep /unattend:<your file’s name>` параметра, эта ошибка может возникать.

В Azure используйте параметр входа в систему при первом включении **(OOBE)** в **Sysprep.exe**или используйте `sysprep /oobe` вместо файла Unattend.xml.

Эта проблема чаще всего создается при использовании **Sysprep.exe** с локальной виртуальной машиной для передачи ОБОБЩЕННОЙ виртуальной машины в Azure. В этом случае вам также может быть интересно, как правильно отправить обобщенную виртуальную машину.

## <a name="solution"></a>Решение

### <a name="replace-unattended-answer-file-option"></a>Параметр "заменить файл ответов автоматической установки"

Эта ситуация возникает, когда образ был подготовлен для использования в Azure, но он использовал пользовательский файл ответов, который не поддерживается в Azure, и вы использовали **Sysprep** с флагом, аналогичным следующей команде:

`sysprep /oobe /generalize /unattend:<NameOfYourAnswerFile.XML> /shutdown`

- В предыдущей команде замените на `<NameOfYourAnswerFile.XML>` имя файла.

Чтобы устранить эту проблему, следуйте [указаниям Azure по подготовке и записи образа](../windows/upload-generalized-managed.md) и подготовке нового обобщенного образа. Во время работы программы Sysprep не используйте `/unattend:<answerfile>` флаг. Вместо этого используйте только следующие флаги:

`sysprep /oobe /generalize /shutdown`

- Для виртуальных машин Azure поддерживается **встроенный** параметр (OOBE).

Вы также можете использовать **графический интерфейс средства подготовки системы** , чтобы выполнить ту же задачу, что и приведенная выше команда, выбрав приведенные ниже параметры.

- Ввод готовых к работе
- Generalize
- Shutdown
 
![Окно средства подготовки системы с выбранными параметрами OOBE, generalize и Shutdown.](./media/unexpected-restart-error-during-vm-boot/3.png)
