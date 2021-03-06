---
title: Настройка задач анализа кода безопасности Майкрософт
titleSuffix: Azure
description: В этой статье описывается настройка задач в расширении Microsoft Security Code Analysis
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 4016e1dd055b45f9cd59a172d0e71ef95fec1c40
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517212"
---
# <a name="configure-and-customize-the-build-tasks"></a>Настройка и настройка задач сборки

В этой статье подробно описаны параметры конфигурации, доступные в каждой из задач сборки. Статья начинается с задач для средств анализа кода безопасности. Он завершается задачами, выполняемыми после обработки.

## <a name="anti-malware-scanner-task"></a>Задача сканера защиты от вредоносных программ

>[!NOTE]
> Для задачи сборки сканера защиты от вредоносных программ требуется агент сборки с включенным защитником Windows. В размещении Visual Studio 2017 и более поздних версий предоставляется такой агент. Задача сборки не будет выполняться в размещенном агенте Visual Studio 2015.
>
> Хотя подписи не могут быть обновлены на этих агентах, подписи всегда должны быть менее трех часов назад.

Сведения о конфигурации задачи показаны на следующем снимке экрана и в тексте.

![Настройка задачи сборки сканера защиты от вредоносных программ](./media/security-tools/5-add-anti-malware-task600.png)

В поле со списком **тип** на снимке экрана выбран **базовый** . Выберите **Пользовательский** , чтобы указать аргументы командной строки для настройки проверки.

Защитник Windows использует клиент Центр обновления Windows для скачивания и установки сигнатур. Если обновление сигнатуры для агента сборки завершается сбоем, код ошибки **HRESULT** , скорее всего, поступает из центр обновления Windows.

Дополнительные сведения об Центр обновления Windows ошибках и их устранении см. в разделе [Центр обновления Windows коды ошибок по компонентам](/windows/deployment/update/windows-update-error-reference) и в статье TechNet, посвященной [Центр обновления Windowsу коды ошибок агента](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

Сведения о настройке YAML для этой задачи см. в статье [Параметры YAML для защиты от вредоносных программ](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>Задача Бинским

> [!NOTE]
> Перед выполнением задачи Бинским сборка должна соответствовать одному из следующих условий:
>  - Сборка создает двоичные артефакты из управляемого кода.
>  - Имеются зафиксированные двоичные артефакты, которые необходимо проанализировать с помощью Бинским.

Сведения о конфигурации задач показаны на следующем снимке экрана и в списке.

![Настройка задачи сборки Бинским](./media/security-tools/7-binskim-task-details.png)

- Установите для конфигурации сборки значение Отладка, чтобы были созданы PDB-файлы отладки. Бинским использует эти файлы для отображения проблем в выходных двоичных файлах обратно в исходный код.
- Чтобы избежать повторного поиска и создания собственной командной строки:
     - В списке **тип** выберите **базовый**.
     - В списке **функция** выберите **анализ**.
- В списке **целевой объект** введите один или несколько описателей для файла, каталога или шаблона фильтра. Эти описатели разрешаются в один или несколько анализируемых двоичных файлов:
    - Несколько указанных целевых объектов должны быть разделены точкой с запятой (;).
    - Спецификатор может быть отдельным файлом или содержать подстановочные знаки.
    - Спецификации каталогов всегда должны оканчиваться на \\ *.
    - Примеры:

```binskim-targets
           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
```

- Если в списке **тип** выбрать пункт **Командная строка** , необходимо запустить binskim.exe:
     - Убедитесь, что первые аргументы для binskim.exe являются командой **Analyze** , за которой следует одна или несколько спецификаций пути. Каждый путь может быть либо полным путем, либо путем относительно исходного каталога.
     - Несколько целевых путей должны быть разделены пробелом.
     - Параметр **/o** или **/Output** можно опустить. Выходное значение добавляется для вас или заменено.
     - Ниже приведены стандартные конфигурации командной строки.

```binskim-line-args
           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
```

> [!NOTE]
> \\При указании каталогов для целевого объекта * важно использовать символ *.

Дополнительные сведения об аргументах командной строки Бинским, правилах по ИДЕНТИФИКАТОРу или кодах выхода см. в разделе [Бинским User Guide](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Дополнительные сведения о настройке YAML для этой задачи см. в статье [Параметры БИНСКИМ YAML](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Задача сканера учетных данных

Сведения о конфигурации задач показаны на следующем снимке экрана и в списке.

![Настройка задачи сборки средства проверки учетных данных](./media/security-tools/3-taskdetails.png)

Доступны следующие варианты:
  - **Отображаемое имя** : имя задачи Azure DevOps. Значение по умолчанию — запустить средство проверки учетных данных.
  - **Основной номер версии инструмента** : доступные значения включают **кредскан v2** , **кредскан v1**. Мы рекомендуем клиентам использовать версию **Кредскан v2** .
  - **Формат вывода**. доступные значения: **TSV** , **CSV** , **сариф** и **fast**.
  - **Версия инструмента** : Мы рекомендуем выбрать **последнюю** версию.
  - **Папка проверки** : папка репозитория, которая должна быть проверена.
  - **Тип файла поиска** : параметры поиска файла поиска, используемого для проверки.
  - **Файл подавлений** : [JSON](https://json.org/) -файл может подавлять проблемы в выходном журнале. Дополнительные сведения о сценариях подавления см. в разделе часто задаваемых вопросов этой статьи.
  - **Подробные выходные данные** : говорят сами о себе.
  - **Размер пакета** : количество параллельных потоков, используемых для запуска средства проверки учетных данных. Значение по умолчанию — 20. Возможные значения находятся в диапазоне от 1 до 2 147 483 647.
  - **Время ожидания соответствия** : время в секундах, затрачиваемое на проверку соответствия поисковому запросу перед отменой проверки.
  - **Размер буфера чтения файла Просмотр** : размер в байтах буфера, используемого при считывании содержимого. Значение по умолчанию — 524 288.  
  - **Максимальное число просмотров файлов, прочитанных в байтах** : максимальное количество байтов для чтения из файла во время анализа содержимого. Значение по умолчанию — 104 857 600.
  - **Параметры управления**  >  **Выполнить эту задачу** : указывает, когда будет выполняться задача. Выберите **пользовательские условия** , чтобы указать более сложные условия.
  - **Версия** : версия задачи сборки в Azure DevOps. Этот параметр часто не используется.

Дополнительные сведения о настройке YAML для этой задачи см. в статье [Параметры YAML средства проверки учетных данных](yaml-configuration.md#credential-scanner-task) .

## <a name="roslyn-analyzers-task"></a>Задача «Roslyn Analyzer»

> [!NOTE]
> Перед запуском задачи Roslyn Analyzers ваша сборка должна удовлетворять следующим условиям.
>
> - Определение сборки включает встроенную задачу сборки MSBuild или Всбуилд для компиляции кода C# или Visual Basic. Задача «анализаторы» полагается на входные и выходные данные встроенной задачи для запуска компиляции MSBuild с включенными анализаторами Roslyn.
> - В агенте сборки, выполняющем эту задачу сборки, установлен Visual Studio 2017 версии 15,5 или более поздней, поэтому она использует компилятор версии 2,6 или более поздней.

Сведения о конфигурации задачи показаны в следующем списке и в заметке.

Доступны следующие варианты:

- **RuleSet** : значения **обязательны для SDL** , **рекомендуется использовать SDL** или собственный настраиваемый набор правил.
- **Анализаторы версия** : Мы рекомендуем выбрать **последнюю** версию.
- **Файл подавления предупреждений компилятора** : текстовый файл со списком идентификаторов предупреждений, которые подавляются.
- **Параметры управления**  >  **Выполнить эту задачу** : указывает, когда будет выполняться задача. Выберите **пользовательские условия** , чтобы указать более сложные условия.

> [!NOTE]
>
> - Анализаторы Roslyn интегрированы с компилятором и могут выполняться только в рамках компиляции csc.exe. Таким образом, для выполнения этой задачи требуется команда компилятора, которая выполнялась ранее в сборке для воспроизведения или повторного запуска. Это воспроизведение или запуск выполняется путем запроса к Azure DevOps (ранее Visual Studio Team Services) для журналов задач сборки MSBuild.
>
>   Нет других способов надежной работы задачи при получении командной строки компиляции MSBuild из определения сборки. Мы рассматриваем Добавление текстового поля полилинии, чтобы пользователи могли вводить свои командные строки. Но после этого было бы трудно поддерживать эти строки в актуальном состоянии и синхронизироваться с главной сборкой.
>
>   Пользовательские сборки должны воспроизводить весь набор команд, а не только команды компилятора. В таких случаях включение анализаторов Roslyn не является тривиальным или надежным.
>
> - Анализаторы Roslyn интегрированы с компилятором. Для вызова анализаторы Roslyn нуждаются в компиляции.
>
>   Эта новая задача сборки реализуется путем повторной компиляции уже построенных проектов C#. Новая задача использует только задачи сборки MSBuild и Всбуилд в той же сборке или в определении сборки, что и исходная задача. Однако в этом случае новая задача использует их с включенными анализаторами Roslyn.
>
>   Если новая задача выполняется на том же агенте, что и исходная задача, то выходные данные новой задачи перезапишут выход исходной задачи в папке источников *s* . Несмотря на то что выходные данные сборки одинаковы, мы советуем запустить MSBuild, скопировать выходные данные в каталог промежуточных артефактов, а затем запустить анализаторы Roslyn.

Дополнительные ресурсы для задачи «анализаторы Roslyn» см. на Документация Майкрософтх [анализаторов на основе Roslyn](/dotnet/standard/analyzers/api-analyzer) .

Пакет анализатора, установленный и используемый этой задачей сборки, можно найти на странице NuGet [Microsoft. CodeAnalysis. фкскопанализерс](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Дополнительные сведения о настройке YAML для этой задачи см. в статье [Параметры YAML Roslyn Analyzers](yaml-configuration.md#roslyn-analyzers-task) .

## <a name="tslint-task"></a>Задача TSLint

Дополнительные сведения о TSLint см. в [репозитории TSLint GitHub](https://github.com/palantir/tslint).

>[!NOTE] 
>Как вы, возможно, знаете, на домашней странице [репозитория TSLint GitHub](https://github.com/palantir/tslint) говорится, что TSLint будет считаться устаревшим в 2019. Корпорация Майкрософт изучает [ESLint](https://github.com/eslint/eslint) как альтернативную задачу.

Дополнительные сведения о настройке YAML для этой задачи см. в статье [Параметры TSLINT YAML](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Задача публикации журналов анализа безопасности

Сведения о конфигурации задач показаны на следующем снимке экрана и в списке.

![Настройка задачи «опубликовать журналы анализа безопасности» задача построения](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Имя артефакта** — любой строковый идентификатор.
- **Тип артефакта**. в зависимости от выбранного варианта можно публиковать журналы в Azure DevOps Server или в общем файле, доступном для агента сборки.
- **Средства**. Вы можете сохранить журналы для конкретных средств или выбрать **все инструменты** , чтобы сохранить все журналы.

Сведения о настройке YAML для этой задачи см. в статье [Публикация журналов безопасности YAML](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Задача "отчет о безопасности"

Сведения о конфигурации отчета о безопасности показаны на следующем снимке экрана и в списке.

![Настройка задачи «построение отчета о безопасности»](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Отчеты** : выберите любую **консоль конвейера** , **TSV-файл** и формат **файлов HTML** . Для каждого выбранного формата создается один файл отчета.
- **Инструменты** : выберите инструменты в определении сборки, для которых требуется сводка обнаруженных проблем. Для каждого выбранного инструмента можно выбрать отображение только ошибок или Просмотр ошибок и предупреждений в сводном отчете.
- **Дополнительные параметры** : Если нет журналов для одного из выбранных средств, можно выбрать запись в журнал предупреждения или ошибки. Если регистрируется ошибка, задача завершается ошибкой.
- **Папка базовых журналов**. можно настроить папку базовых журналов, в которой будут найдены журналы. Но этот параметр обычно не используется.

Сведения о настройке YAML для этой задачи см. в статье [Параметры YAML отчета о безопасности](yaml-configuration.md#security-report-task) .

## <a name="post-analysis-task"></a>Задача после анализа

Сведения о конфигурации задач показаны на следующем снимке экрана и в списке.

![Настройка задачи построения после анализа](./media/security-tools/a-post-analysis600.png)

- **Инструменты** : выберите инструменты в определении сборки, для которых необходимо условно внедрить разрыв сборки. Для каждого выбранного инструмента может быть выбрано, следует ли прерывать только ошибки или как ошибки, так и предупреждения.
- **Отчет**. при необходимости можно записать результаты, вызывающие разрыв сборки. Результаты записываются в окно консоли Azure DevOps и в файл журнала.
- **Дополнительные параметры** : Если нет журналов для одного из выбранных средств, можно выбрать запись в журнал предупреждения или ошибки. Если регистрируется ошибка, задача завершается ошибкой.

Дополнительные сведения о настройке YAML для этой задачи см. в статье [параметры после анализа YAML](yaml-configuration.md#post-analysis-task) .

## <a name="next-steps"></a>Дальнейшие действия

Сведения о конфигурации на основе YAML см. в нашем [руководстве по настройке YAML](yaml-configuration.md).

Если у вас есть дополнительные вопросы о расширении анализа кода безопасности и предлагаемых средствах, ознакомьтесь со [страницей часто задаваемых вопросов](security-code-analysis-faq.md).