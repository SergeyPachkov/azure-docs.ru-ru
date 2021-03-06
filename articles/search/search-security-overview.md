---
title: Общие сведения о безопасности
titleSuffix: Azure Cognitive Search
description: Azure Когнитивный поиск соответствует требованиям SOC 2, HIPAA и другим сертификатам. Подключение и шифрование данных, проверка подлинности и доступ к удостоверениям с помощью идентификаторов безопасности пользователей и групп в выражениях фильтра.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: f314394d3a0ac453d525079e096162d8739f67cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314751"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Безопасность в Azure Когнитивный поиск — обзор

В этой статье описаны основные функции безопасности в Azure Когнитивный поиск, которые могут защищать содержимое и операции.

+ На уровне хранилища встроено шифрование неактивных данных для всех управляемых службой данных, сохраненных на диск, включая индексы, карты синонимов и определения индексаторов, источников данных и навыков. Когнитивный поиск Azure также поддерживает добавление ключей, управляемых клиентом (CMK), для дополнительного шифрования индексированного содержимого. Для служб, созданных после августа 1 2020, шифрование CMK распространяется на данные на временных дисках для полного двойного шифрования индексированного содержимого.

+ Безопасность входящего трафика защищает конечную точку службы поиска по возрастанию уровней безопасности: от ключей API запроса до правил входящего трафика в брандмауэре до частных конечных точек, которые полностью защищают службу от общедоступного Интернета.

+ Безопасность исходящего трафика применяется к индексаторам, которые извлекают содержимое из внешних источников. Для исходящих запросов настройте управляемое удостоверение, чтобы сделать поиск доверенной службой при доступе к данным из службы хранилища Azure, SQL Azure, Cosmos DB или других источников данных Azure. Управляемое удостоверение — это замена учетных данных или ключей доступа в соединении. Безопасность исходящего трафика в этой статье не рассматривается. Дополнительные сведения об этой возможности см. в разделе [Подключение к источнику данных с помощью управляемого удостоверения](search-howto-managed-identities-data-sources.md).

Просмотрите это видео с краткими сведениями о архитектуре безопасности и каждой категории компонентов.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

<a name="encryption"></a>

## <a name="encrypted-transmissions-and-storage"></a>Зашифрованные передачи и хранение

В Когнитивный поиск Azure шифрование начинается с соединений и передач, а также дополняется содержимым, хранящимся на диске. Для служб поиска в общедоступном Интернете Когнитивный поиск Azure прослушивает HTTPS порт 443. Все подключения клиента к службе используют шифрование TLS 1,2. Более ранние версии (1,0 или 1,1) не поддерживаются.

:::image type="content" source="media/search-security-overview/encryption-at-rest-cmk.png" alt-text="Схема, на которой показаны различные типы безопасности на каждом уровне служб":::

Для внутренних данных, обрабатываемых службой поиска, в следующей таблице описаны [модели шифрования данных](../security/fundamentals/encryption-models.md). Некоторые функции, такие как хранилище знаний, добавочное дополнение и индексирование на основе индексаторов, читают или пишут структуры данных в других службах Azure. Эти службы имеют собственные уровни поддержки шифрования, отделенные от Когнитивный поиск Azure.

| Моделирование | Ключ&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Необходимость&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Ограничения | Применяется к |
|------------------|-------|-------------|--------------|------------|
| шифрование на стороне сервера | Ключи, управляемые Майкрософт | Нет (встроенная) | Нет, доступно на всех уровнях во всех регионах для содержимого, созданного после января 24 2018. | Содержимое (индексы и карты синонимов) и определения (индексаторы, источники данных, навыков) |
| шифрование на стороне сервера | ключи, управляемые клиентом | Хранилище ключей Azure; | Доступно на оплачиваемых уровнях во всех регионах для содержимого, созданного после января 2019. | Содержимое (индексы и карты синонимов) на дисках данных |
| двойное шифрование на стороне сервера | ключи, управляемые клиентом | Хранилище ключей Azure; | Доступно на оплачиваемых уровнях в выбранных регионах в службах поиска после августа 1 2020. | Содержимое (индексы и карты синонимов) на дисках данных и временных дисках |

### <a name="service-managed-keys"></a>Ключи, управляемые службой

Шифрование, управляемое службой, является внутренней операцией Майкрософт, основанной на [Шифрование службы хранилища Azure](../storage/common/storage-service-encryption.md), с использованием [шифрования AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)с 256-битным шифрованием. Она выполняется автоматически во всех индексированиях, в том числе при добавочных обновлениях индексов, которые не полностью зашифрованы (создаются до января 2018).

### <a name="customer-managed-keys-cmk"></a>Ключи, управляемые клиентом (CMK)

Для управляемых клиентом ключей требуется дополнительная оплачиваемая служба, Azure Key Vault, которая может находиться в другом регионе, но в рамках той же подписки, что и Azure Когнитивный поиск. Включение шифрования CMK увеличит размер индекса и снизит производительность запросов. Исходя из наблюдений на дату, можно увидеть увеличение 30%-60% времени выполнения запроса, хотя фактическая производительность зависит от определения индекса и типов запросов. В связи с этим влиянием на производительность рекомендуется включить эту функцию только для индексов, которые действительно необходимы. Дополнительные сведения см. [в статье Настройка ключей шифрования, управляемых клиентом, в когнитивный Поиск Azure](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Двойное шифрование

В Когнитивный поиск Azure двойное шифрование является расширением CMK. Он рассматривается как двухстороннее шифрование (один раз в CMK, а также через ключи, управляемые службой) и полный в области, охватывающий долгосрочное хранение, которое записывается на диск данных, и краткосрочное хранение, записанное на временные диски. Разница между CMK до 1 2020 августа и после нее, а также то, что делает CMK функцией двойного шифрования в Azure Когнитивный поиск, является дополнительным шифрованием неактивных данных на временных дисках.

В настоящее время в новых службах, созданных в этих регионах после 1 августа, доступно двойное шифрование.

+ западная часть США 2
+ Восточная часть США
+ Центрально-южная часть США
+ US Gov (Вирджиния)
+ US Gov (Аризона)

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>Безопасность входящего трафика и защита конечных точек

Функции безопасности для входящих подключений защищают конечную точку службы поиска, повышая уровень безопасности и сложности. Во-первых, для всех запросов требуется ключ API для доступа с проверкой подлинности. Во-вторых, при необходимости можно задать правила брандмауэра, ограничивающие доступ к конкретным IP адресам. Для расширенной защиты третий вариант — включить частную ссылку Azure, чтобы защитить конечную точку службы от всего трафика Интернета.

### <a name="public-access-using-api-keys"></a>Открытый доступ с использованием ключей API

По умолчанию доступ к службе поиска осуществляется через общедоступное облако с использованием проверки подлинности на основе ключей для администрирования или доступа к запросам к конечной точке службы поиска. Ключ API является строкой, состоящей из случайно сгенерированных букв и цифр. Тип ключа (администратор или запрос) определяет уровень доступа. Отправка допустимого ключа представляет собой доказательство того, что запрос исходит от доверенной сущности.

Существует два уровня доступа к службе поиска, которые включаются следующими ключами API:

+ Ключ администратора (разрешает доступ на чтение и запись для операций [создания, чтения, обновления и удаления](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) в службе поиска)

+ Ключ запроса (разрешает доступ только для чтения к коллекции документов индекса)

*Ключи администратора* создаются при подготовке службы. Существует два ключа администратора, обозначенные для упорядочения как *первичный* и *вторичный*, хотя в действительности они являются взаимозаменяемыми. У каждой службы есть два ключа администратора, чтобы вы могли сменять их, не теряя доступа к службе. Вы можете периодически [повторно создать ключ администратора](search-security-api-keys.md#regenerate-admin-keys) в соответствии с рекомендациями по обеспечению безопасности Azure, но нельзя добавить общее число ключей администратора. Для одной службы поиска существует не более двух ключей администрирования.

*Ключи запроса* создаются по мере необходимости и предназначены для клиентских приложений, которые выдают запросы. Вы можете создать до 50 ключей поисковых запросов. В коде приложения укажите URL-адрес поиска и ключ API запроса, чтобы разрешить доступ только для чтения к коллекции Documents определенного индекса. Используемые вместе конечная точка, ключ API с доступом только для чтения и целевой индекс определяют область применения и уровень доступа для подключения из клиентского приложения.

Проверка подлинности требуется для каждого запроса, который состоит из обязательного ключа, операции и объекта. Для обеспечения полной безопасности операций службы достаточно двух уровней разрешений (полные или только для чтения) и контекста (например, для операции запроса к индексу). Дополнительные сведения о ключах см. в статье о [создании ключей api и управлении ими](search-security-api-keys.md).

### <a name="ip-restricted-access"></a>Доступ с ограниченным IP-адресом

Для дальнейшего управления доступом к службе поиска можно создать правила брандмауэра для входящих подключений, которые разрешают доступ к определенному IP-адресу или диапазону IP-адресов. Все клиентские соединения должны быть выполнены через разрешенный IP-адрес, иначе соединение будет отклонено.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="Схема, на которой показаны различные типы безопасности на каждом уровне служб":::

Для [настройки входящего доступа](service-configure-firewall.md)можно использовать портал.

Кроме того, можно использовать API-интерфейсы RESTFUL для управления. Начиная с API версии 2020-03-13 с параметром [ипруле](/rest/api/searchmanagement/services/createorupdate#iprule) можно ограничить доступ к службе, определив IP-адреса, по отдельности или в пределах диапазона, который будет предоставлять доступ к службе поиска.

### <a name="private-endpoint-no-internet-traffic"></a>Частная конечная точка (без трафика Интернета)

[Частная конечная точка](../private-link/private-endpoint-overview.md) Azure когнитивный Поиск позволяет клиенту в [виртуальной сети](../virtual-network/virtual-networks-overview.md) безопасно получать доступ к данным в индексе поиска по [закрытому каналу](../private-link/private-link-overview.md).

Частная конечная точка использует IP-адрес из адресного пространства виртуальной сети для подключений к службе поиска. Сетевой трафик между клиентом и службой поиска проходит по виртуальной сети и частной связи в магистральной сети Майкрософт, что устраняет уязвимость в общедоступном Интернете. ВИРТУАЛЬная сеть обеспечивает безопасное взаимодействие между ресурсами с локальной сетью и Интернетом.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="Схема, на которой показаны различные типы безопасности на каждом уровне служб":::

Хотя это решение является наиболее безопасным, использование дополнительных служб является дополнительными затратами, поэтому убедитесь, что у вас есть четкое понимание преимуществ, прежде чем углубляться в. Дополнительные сведения о затратах см. на [странице с ценами](https://azure.microsoft.com/pricing/details/private-link/). Дополнительные сведения о совместной работе этих компонентов см. в видеоролике в верхней части этой статьи. Покрытие параметра закрытой конечной точки начинается с 5:48 в видео. Инструкции по настройке конечной точки см. в статье [Создание частной конечной точки для Azure когнитивный Поиск](service-create-private-endpoint.md).

## <a name="index-access"></a>Доступ к индексу

В Когнитивный поиск Azure отдельный индекс не является защищаемым объектом. Вместо этого доступ к индексу определяется на уровне служб (доступ на чтение или запись к службе) вместе с контекстом операции.

В случае с доступом пользователей можно структурировать запросы для подключения с использованием ключа запроса (после чего любой запрос становится доступным только для чтения) и добавить конкретный индекс, используемый приложением. В запросе невозможно объединить индексы или получить доступ к нескольким индексам одновременно, поэтому все запросы по определению направляются к одному индексу. Таким образом, структура самого запроса (ключ и один целевой индекс) определяет границы безопасности.

Доступ администратора и разработчика к индексам недифференцированный: для обоих типов доступа необходимо разрешение на запись для создания, удаления и обновления объектов, управляемых службой. Любой пользователь, имеющий ключ администратора к службе, может прочитать, изменить или удалить любой индекс в той же службе. Решением для защиты от случайного или злонамеренного удаления индексов являются внутренние средства управления исходным кодом для ресурсов кода, которые позволяют отменить нежелательное удаление или изменение индекса. Когнитивный поиск Azure отработка отказа в кластере для обеспечения доступности, но не сохраняет и не выполняет собственный код, используемый для создания или загрузки индексов.

Для мультитенантных решений, границы безопасности которых должны находиться на уровне индексов, такие решения, как правило, содержат средний уровень, на котором клиенты обрабатывают изоляцию индекса. Дополнительные сведения о варианте использования для нескольких клиентов см. в статье [шаблоны разработки для многоклиентских приложений SaaS и Azure когнитивный Поиск](search-modeling-multitenant-saas-applications.md).

## <a name="user-access"></a>Доступ пользователей

Как пользователь получает доступ к индексу, а другие объекты определяются по типу ключа API в запросе. Большинство разработчиков создают и назначают [*ключи запросов*](search-security-api-keys.md) для поисковых запросов на стороне клиента. Ключ запроса предоставляет доступ только для чтения к содержимому с возможностью поиска в индексе.

Если требуется детальный контроль над результатами поиска, можно создать фильтры безопасности для запросов, возвращая документы, связанные с данным удостоверением безопасности. Вместо предопределенных ролей и назначений ролей управление доступом на основе удостоверений реализуется как *фильтр*, обрезающий результаты поиска документов и содержимого на основе удостоверений. В следующей таблице описаны два подхода к обрезке результатов поиска несанкционированного содержимого.

| Подход | Описание |
|----------|-------------|
|[Security filters for trimming results in Azure Search](search-security-trimming-for-azure-search.md) (Фильтры безопасности для обрезки результатов Поиска Azure)  | Документирует базовый рабочий процесс для реализации управления доступом на основе удостоверений пользователей. Описывает добавление удостоверений безопасности в индекс, а также объясняет фильтрацию этого поля для усечения результатов запрещенного содержимого. |
|[Security filters for trimming Azure Search results using Active Directory identities](search-security-trimming-for-azure-search-with-aad.md) (Фильтры безопасности для обрезки результатов Поиска Azure с использованием удостоверений Active Directory)  | Эта статья дополняет предыдущую статью, предоставляя действия по извлечению удостоверений из Azure Active Directory (Azure AD), одной из [бесплатных служб](https://azure.microsoft.com/free/) на облачной платформе Azure. |

## <a name="administrative-rights"></a>Права администратора

[Управление доступом на основе ролей Azure (Azure RBAC)](../role-based-access-control/overview.md) — это система авторизации, основанная на [Azure Resource Manager](../azure-resource-manager/management/overview.md) для подготовки ресурсов Azure. В Когнитивный поиск Azure диспетчер ресурсов используется для создания или удаления службы, управления ключами API и масштабирования службы. Таким образом, назначения ролей Azure определяют, кто может выполнять эти задачи, независимо от того, используете ли они [портал](search-manage.md), [PowerShell](search-manage-powershell.md)или [API-интерфейсы других функций управления](/rest/api/searchmanagement/search-howto-management-rest-api).

Напротив, права администратора для содержимого, размещенного в службе, например возможность создания или удаления индекса, выводятся через ключи API, как описано в [предыдущем разделе](#index-access).

> [!TIP]
> Используя механизмы Azure, можно заблокировать подписку или ресурс, чтобы предотвратить случайное или несанкционированное удаление службы поиска пользователями с правами администратора. Дополнительные сведения см. [в разделе Блокировка ресурсов для предотвращения непредвиденного удаления](../azure-resource-manager/management/lock-resources.md).

## <a name="certifications-and-compliance"></a>Соответствие требованиям и сертификаты

Когнитивный поиск Azure сертифицирована для нескольких глобальных, региональных и отраслевых стандартов общедоступного облака и Azure для государственных организаций. Чтобы получить полный список, скачайте [технический документ о **предложениях соответствия Microsoft Azure** ](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) на официальной странице отчетов об аудите.

Для обеспечения соответствия требованиям вы можете использовать [политику Azure](../governance/policy/overview.md) для реализации рекомендаций по обеспечению [безопасности Azure](../security/benchmarks/introduction.md)с высоким уровнем безопасности. Производительность системы безопасности Azure — это набор рекомендаций по обеспечению безопасности, кодифицированные в средства управления безопасностью, которые соответствуют ключевым действиям, которые необходимо предпринять для уменьшения угроз для служб и данных. В настоящее время существует 11 элементов управления безопасностью, включая [безопасность сети](../security/benchmarks/security-control-network-security.md), [ведение журнала и мониторинг](../security/benchmarks/security-control-logging-monitoring.md), а также [защиту данных](../security/benchmarks/security-control-data-protection.md) .

Политика Azure — это возможность, встроенная в Azure, которая помогает управлять соответствием нескольким стандартам, включая показатели производительности системы безопасности Azure. Для хорошо известных тестов производительности политика Azure предоставляет встроенные определения, которые предоставляют как критерии, так и реагирование на действия, которые не соответствуют требованиям.

Для Когнитивный поиск Azure в настоящее время имеется встроенное определение. Он предназначен для ведения журнала диагностики. С помощью этого встроенного средства можно назначить политику, определяющую любую службу поиска, в которой отсутствует Диагностическое протоколирование, а затем включить ее. Дополнительные сведения см. в статье [Управление соответствием нормативным требованиям политики Azure для когнитивный Поиск Azure](security-controls-policy.md).

## <a name="see-also"></a>См. также

+ [Основы обеспечения безопасности в Azure](../security/fundamentals/index.yml)
+ [Безопасность в Azure](https://azure.microsoft.com/overview/security)
+ [Центр безопасности Azure](../security-center/index.yml)
