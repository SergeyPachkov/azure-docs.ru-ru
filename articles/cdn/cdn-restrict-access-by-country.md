---
title: Ограничение Azure CDN содержимого по странам/регионам | Документация Майкрософт
description: Узнайте, как ограничить доступ по стране или региону к содержимому Azure CDN с помощью функции географической фильтрации.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/19/2018
ms.author: allensu
ms.openlocfilehash: ed82adcc1432bde27042d5775c454bfabcdb96ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358140"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Ограничение Azure CDN содержимого по странам или регионам

## <a name="overview"></a>Обзор
По умолчанию запрашиваемое содержимое возвращается пользователю независимо от расположения, из которого был сделан запрос. Однако в некоторых случаях может потребоваться ограничить доступ к содержимому по стране или региону. С помощью функции *географической фильтрации* можно создать правила для определенных путей в КОНЕЧНОЙ точке CDN, чтобы разрешить или заблокировать содержимое в выбранных странах или регионах.

> [!IMPORTANT]
> Профили **Azure CDN уровня "Стандартный" от Майкрософт** не поддерживают геофильтрацию на основе пути.
> 

## <a name="standard-profiles"></a>Профили (цен. категория "Стандартный")
Процедуры, описанные в этом разделе, относятся только к профилям **Azure CDN (цен. категория "Стандартный") от Akamai** и **Azure CDN (цен. категория "Стандартный") от Verizon**. 

Чтобы активировать геофильтрацию для профилей **Azure CDN (цен. категория "Премиум") от Verizon**, используйте портал **управления**. Дополнительные сведения см. в разделе о профилях [Azure CDN (цен. категория "Премиум") от Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Определение пути к каталогу
Чтобы получить доступ к функции геофильтрации, выберите свою конечную точку CDN на портале, а затем щелкните **Геофильтрация** в разделе параметров в меню слева. 

![Снимок экрана, на котором показана географическая фильтрация, выбранная в меню для конечной точки.](./media/cdn-filtering/cdn-geo-filtering-standard.png)

В поле **Путь** укажите относительный путь к каталогу, доступ к которому будет разрешен или запрещен. 

Геофильтрацию можно применить ко всем файлам в корневом каталоге, указав косую черту (/), или к выбранным каталогам, указав к ним пути, например */pictures/*. Геофильтрацию также можно применить к одному файлу, например */pictures/city.png*. Можете указать несколько правил. Когда вы введете первое правило, рядом появится пустая строка для ввода следующего.

Например, все следующие фильтры путей каталогов являются допустимыми:   
*/*                                 
*Изображений*     
*/фотос/страсбаург/*     
*/Photos/Strasbourg/city.png*.

### <a name="define-the-type-of-action"></a>Определение типа действия

В списке **Действие** выберите **Разрешить** или **Запретить**: 

- **Разрешить**: доступ к ресурсам, запрошенным из рекурсивного пути, разрешен только пользователям из указанных стран или регионов.

- **Блокировать**: пользователям из указанных стран или регионов отказано в доступе к ресурсам, запрошенным из рекурсивного пути. Если для этого расположения не настроены другие параметры фильтрации для страны или региона, доступ будет разрешен всем остальным пользователям.

Например, правило геофильтрации для блокировки путей */фотографии/Strasbourg/* фильтрует следующие файлы:     
*http: \/ / \<endpoint> . azureedge.NET/Photos/Strasbourg/1000.jpg* 
 *http: \/ / \<endpoint> . azureedge.NET/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Определение стран и регионов
В списке **коды стран** выберите страны или регионы, которые требуется заблокировать или разрешить для пути. 

Завершив выбор стран и регионов, нажмите кнопку **сохранить** , чтобы активировать новое правило географической фильтрации. 

![На снимке экрана показаны коды стран, используемые для блокирования или разрешения стран или регионов.](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Очистка ресурсов
Чтобы удалить правило, выберите его в списке на странице **Фильтрация по странам**, а затем щелкните **Удалить**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Профили Azure CDN (цен. категория "Премиум") от Verizon
Пользовательский интерфейс для создания правила геофильтрации отличается для профилей **Azure CDN (цен. категория "Премиум") от Verizon**.

1. В меню профиля Azure CDN вверху выберите **Управление**.

2. На портале Verizon выберите **HTTP Large** (Большой HTTP-объект) и **Country Filtering** (Фильтрация по странам).

    ![На снимке экрана показано, как выбрать фильтрацию по странам в Azure C D N.](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Нажмите кнопку **Add Country Filter** (Добавить фильтр по странам).

    Появится страница **Step One:** (Шаг 1).

4. Введите путь к каталогу, выберите **Block** (Блокировать) или **Add** (Добавить), а затем нажмите кнопку **Next** (Далее).

    Появится страница **Step Two:** (Шаг 2). 

5. Выберите одну или несколько стран или регионов из списка, а затем нажмите кнопку **Готово** , чтобы активировать правило. 
    
    Новое правило появится в таблице на странице **Country Filtering** (Фильтрация по странам).

    ![На снимке экрана показано, где правило отображается в поле Фильтрация по странам.](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Очистка ресурсов
В таблице правила фильтрации страны или региона щелкните значок удаления рядом с правилом, чтобы удалить его, или значок редактирования, чтобы изменить его.

## <a name="considerations"></a>Рекомендации
* Изменения в конфигурации геофильтрации для страны вступают в силу не сразу:
   * Для профилей **Azure CDN категории "Стандартный" от Майкрософт** распространение обычно выполняется в течение 10 минут. 
   * Для профилей **Azure CDN уровня "Стандартный" от Akamai** распространение обычно завершается в течение одной минуты. 
   * Для профилей **Azure CDN уровня "Стандартный" от Verizon** и **Azure CDN уровня "Премиум" от Verizon** распространение обычно выполняется в течение 10 минут. 
 
* Эта функция не поддерживает подстановочные знаки (например, *).

* Конфигурация геофильтрации, связанная с относительным путем, применяется рекурсивно к этому пути.

* Только одно правило может применяться к тому же относительному пути. То есть нельзя создать несколько фильтров стран или регионов, которые указывают на один и тот же относительный путь. Однако, так как фильтры страны и региона являются рекурсивными, папка может содержать несколько фильтров стран или регионов. Иными словами, вложенной папке ранее настроенной папки можно назначить другой фильтр страны или региона.

* Функция географической фильтрации использует коды стран для определения стран и регионов, из которых запрос разрешен или заблокирован для защищенного каталога. Хотя профили от Akamai и Verizon по большей части поддерживают одинаковые коды стран, существуют некоторые различия. Дополнительные сведения см. в разделе [Azure CDN Country Codes](/previous-versions/azure/mt761717(v=azure.100)). 

