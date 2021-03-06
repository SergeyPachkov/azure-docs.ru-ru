---
title: Поиск по зашифрованному содержимому хранилища BLOB-объектов Azure
titleSuffix: Azure Cognitive Search
description: Узнайте, как индексировать и извлекать текст из зашифрованных документов в хранилище BLOB-объектов Azure с помощью Azure Когнитивный поиск.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.openlocfilehash: f0295c27f1d193b0dcd7829a11b4aabe0edb659b
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286342"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Индексирование зашифрованных больших двоичных объектов с помощью индексаторов больших двоичных объектов и навыков в Azure Когнитивный поиск

В этой статье показано, как использовать [когнитивный Поиск Azure](search-what-is-azure-search.md) для индексирования документов, которые ранее были зашифрованы в [хранилище BLOB-объектов Azure](../storage/blobs/storage-blobs-introduction.md) с помощью [Azure Key Vault](../key-vault/general/overview.md). Как правило, индексатор не может извлекать содержимое из зашифрованных файлов, так как у него нет доступа к ключу шифрования. Однако, используя пользовательский навык [декриптблобфиле](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) , за которым следует [документекстрактионскилл](cognitive-search-skill-document-extraction.md), вы можете предоставить управляемый доступ к ключу для расшифровки файлов, а затем извлечь из них содержимое. Это разблокирует возможность индексировать эти документы без ущерба для состояния шифрования сохраненных документов.

Начиная с ранее зашифрованных целых документов (неструктурированный текст), таких как PDF, HTML, DOCX и PPTX в хранилище BLOB-объектов Azure, в этом руководство используются процедуры POST и API-интерфейсы RESTFUL поиска для выполнения следующих задач.

> [!div class="checklist"]
> * Определите конвейер, который расшифровывает документы и извлекает из них текст.
> * Определите индекс для хранения выходных данных.
> * Выполните этот конвейер, чтобы создать и заполнить индекс.
> * Изучите результаты с помощью полнотекстового поиска и расширенного синтаксиса запросов.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

В этом примере предполагается, что вы уже отправили файлы в хранилище BLOB-объектов Azure и выполнили их шифрование в процессе. Если вам нужна помощь с начальной отправкой и шифрованием файлов, ознакомьтесь с [этим руководством](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md) , чтобы получить сведения о том, как это сделать.

+ [Хранилище Azure](https://azure.microsoft.com/services/storage/)
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) в той же подписке, что и когнитивный Поиск Azure. Для хранилища ключей должна быть включена **Защита от** **обратимого удаления** и очистки.
+ [Когнитивный Поиск Azure](search-create-service-portal.md) на [уровне "оплачиваемый](search-sku-tier.md#tiers) " (базовый или выше в любом регионе)
+ [Функция Azure](https://azure.microsoft.com/services/functions/)
+ [Классическое приложение Postman](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1. Создание служб и получение учетных данных

### <a name="set-up-the-custom-skill"></a>Настройка пользовательского навыка

В этом примере используется пример проекта [декриптблобфиле](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) из репозитория [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) GitHub. В этом разделе вы развернете навык для функции Azure, чтобы его можно было использовать в наборе навыков. Встроенный скрипт развертывания создает ресурс функции Azure с именем Start с **псдбф-Function-App-** and, загружает навык. Вам будет предложено предоставить подписку и группу ресурсов. Не забудьте выбрать ту же подписку, в которой находится ваш экземпляр Azure Key Vault.

При работе Декриптблобфиле навык принимает URL-адрес и маркер SAS для каждого большого двоичного объекта в качестве входных данных и выводит скачанный зашифрованный файл, используя контракт ссылки на файл, который ожидает Azure Когнитивный поиск. Помните, что Декриптблобфиле требует ключ шифрования для выполнения расшифровки. В рамках настройки вы также создадите политику доступа, которая предоставляет Декриптблобфиле функции доступ к ключу шифрования в Azure Key Vault.

1. Нажмите кнопку **развернуть в Azure** на [целевой странице декриптблобфиле](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment), которая откроет указанный шаблон диспетчер ресурсов в портал Azure.

1. Выберите **подписку, в которой существует ваш экземпляр Azure Key Vault** (это не будет работать, если выбрать другую подписку) и либо выбрать существующую группу ресурсов, либо создать новую (если вы создадите новую), а также выбрать регион для развертывания.

1. Выберите **Проверка + создать** , убедитесь, что вы соглашаетесь с условиями, а затем щелкните **создать** , чтобы развернуть функцию Azure.

    ![Шаблон ARM на портале](media/indexing-encrypted-blob-files/arm-template.jpg "Шаблон ARM на портале")

1. Дождитесь завершения развертывания.

1. Перейдите к экземпляру Azure Key Vault на портале. [Создайте политику доступа](../key-vault/general/assign-access-policy-portal.md) в Azure Key Vault, которая предоставляет ключ для доступа к пользовательским навыкам.
 
    1. В разделе **Параметры** выберите **политики доступа** , а затем щелкните **Добавить политику доступа** .
     
       ![Keyvault добавить политику доступа](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Политики доступа Keyvault")

    1. В разделе **Настройка из шаблона** выберите **Azure Data Lake Storage или служба хранилища Azure**.

    1. В качестве участника выберите развернутый экземпляр функции Azure. Его можно найти с помощью префикса ресурса, который использовался для его создания на шаге 2, который имеет значение префикса по умолчанию **псдбф-Function-App**.

    1. Не выбирайте ничего для параметра "Авторизация приложения".
     
        ![Keyvault добавить шаблон политики доступа](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Шаблон политики доступа Keyvault")

    1. Не забудьте нажать кнопку **сохранить** на странице политики доступа, прежде чем добавлять политику доступа.
     
         ![Keyvault сохранить политику доступа](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Сохранить политику доступа Keyvault")

1. Перейдите к функции **псдбф-Function-App** на портале и обратите внимание на следующие свойства, которые понадобятся вам позже.

    1. URL-адрес функции, который можно найти в разделе **Essentials** на главной странице функции.
    
        ![URL-адрес функции](media/indexing-encrypted-blob-files/function-uri.jpg "Где найти URL-адрес функции Azure")

    1. Код ключа узла, который можно найти, перейдя к **разделу ключи приложения** , щелкнув элемент, чтобы отобразить ключ **по умолчанию** и скопировать значение.
     
        ![Код ключа узла функции](media/indexing-encrypted-blob-files/function-host-key.jpg "Где найти код ключа узла функции Azure")

### <a name="cognitive-services"></a>Службы Cognitive Services

Процесс обогащения и подготовки к ИСКУССТВЕНному выполнению осуществляется с помощью Cognitive Services, в том числе Анализ текста и Компьютерное зрение для обработки естественного языка и изображений. Если бы вы создавали реальный прототип или проект, на этом этапе нужно было бы создать Cognitive Services (в том же регионе, что и Когнитивный поиск Azure) для связывания с операциями индексирования.

Но для нашего примера подготовку ресурсов можно пропустить, так как Когнитивный поиск Azure может подключаться к Cognitive Services в фоновом режиме и предоставляет 20 бесплатных транзакций для каждого выполнения индексатора. После обработки 20 документов индексатор завершится ошибкой, если к набору навыков не присоединен ключ Cognitive Services. Для крупных проектов вам, скорее всего, нужно будет подготовить Cognitive Services на уровне S0 с оплатой по мере использования. См. сведения о [подключении Cognitive Services](cognitive-search-attach-cognitive-services.md). Обратите внимание, что для запуска набора знаний с более чем 20 документами требуется ключ Cognitive Services, даже если ни один из выбранных автоподбора не подключается к Cognitive Services (например, при условии, что к нему не добавлены навыки).

### <a name="azure-cognitive-search"></a>Когнитивный поиск Azure

Последний компонент — Azure Когнитивный поиск, который можно [создать на портале](search-create-service-portal.md). Для выполнения этого руководством можно использовать уровень Free. 

Как и в случае с функцией Azure, собирайте ключ администратора. Позже, когда вы начнете структурировать запросы, адрес конечной точки и административный ключ интерфейса нужно будет указывать в каждом запросе для проверки подлинности.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Получение ключа API и URL-адреса конечной точки для администрирования Когнитивного поиска Azure

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **Обзор** для службы поиска получите ее имя. Имя службы можно проверить, просмотрев URL-адрес конечной точки. Если URL-адрес конечной точки имеет вид `https://mydemo.search.windows.net`, значит служба называется `mydemo`.

2. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

   ![Получение имени службы, ключей запросов и администратора](media/search-get-started-javascript/service-name-and-keys.png)

Для выполнения любого запроса к службе нужно включить ключ API в заголовок. Действительный ключ устанавливает для каждого запроса отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="2---set-up-postman"></a>2\. Настройка Postman

Установите и настройте Postman.

### <a name="download-and-install-postman"></a>Скачивание и установка Postman

1. Скачайте [исходный код коллекции Postman](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json).
1. Щелкните **File** > **Import** (Файл > Импорт), чтобы импортировать этот исходный код в Postman.
1. Выберите вкладку **Collections** (Коллекции) и нажмите кнопку **...** (многоточие).
1. Выберите команду **Изменить**. 
   
   ![Приложение Postman, демонстрирующее навигацию](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Переход в меню Правка в Postman")
1. В диалоговом окне **Edit** (Правка) выберите вкладку **Variables** (Переменные). 

На вкладке **Переменные** вы можете добавить значения, которые Postman будет заменять во всех местах, где они указаны в двойных скобках. Например, Postman заменит символ `{{admin-key}}` текущим значением, которое вы указали для `admin-key`. Postman выполняет такие замены в URL-адресах, заголовках, текстах запросов и так далее. 

Чтобы получить значение для `admin-key` , используйте ранее записанный ключ API администратора когнитивный Поиск Azure. Укажите `search-service-name` имя используемой службы когнитивный Поиск Azure. Задайте с `storage-connection-string` помощью значения на вкладке **ключи доступа** учетной записи хранения и задайте `storage-container-name` имя контейнера больших двоичных объектов в этой учетной записи хранения, в которой хранятся зашифрованные файлы. Укажите `function-uri` URL-адрес функции Azure, записанный ранее, и задайте `function-code` для него код ключа узла функции Azure, который вы заметили ранее. Для остальных параметров можно сохранить значения по умолчанию.

![Вкладка переменных приложения Postman](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Окно переменных Postman")


| Переменная    | Как получить |
|-------------|-----------------|
| `admin-key` | На странице **Ключи** службы "Когнитивный поиск Azure".  |
| `search-service-name` | Имя службы "Когнитивный поиск Azure". Введите URL-адрес `https://{{search-service-name}}.search.windows.net`. | 
| `storage-connection-string` | На вкладке **Ключи доступа** для учетной записи хранения выберите **key1** > **Строка подключения**. | 
| `storage-container-name` | Имя контейнера больших двоичных объектов, в котором находятся зашифрованные файлы для индексации. | 
| `function-uri` |  В функции Azure в разделе **Essentials** на главной странице. | 
| `function-code` | В функции Azure перейдите к **разделу ключи приложения** , щелкните, чтобы отобразить ключ **по умолчанию** и скопировать значение. | 
| `api-version` | Сохраните значение **2020-06-30**. |
| `datasource-name` | Оставьте в качестве **зашифрованных BLOB-объектов — DS**. | 
| `index-name` | Оставьте **зашифрованные BLOB-объекты — idx**. | 
| `skillset-name` | Оставьте **зашифрованные BLOB-объекты — СС**. | 
| `indexer-name` | Оставьте в виде **зашифрованных BLOB-объектов — икср**. | 

### <a name="review-the-request-collection-in-postman"></a>Обзор коллекции запросов в Postman

При запуске этого руководство необходимо выполнить четыре HTTP-запроса: 

- **Запрос PUT для создания индекса.** Этот индекс содержит данные, которые служба "Когнитивный поиск Azure" использует и возвращает.
- **Запрос POST для создания источника данных** : этот источник данных подключает службу когнитивный Поиск Azure к вашей учетной записи хранения и, следовательно, зашифрованные файлы больших двоичных объектов. 
- **Разместить запрос на создание набора навыков** : набор навыков определяет пользовательское определение навыка для функции Azure, которая будет расшифровывать данные файла большого двоичного объекта, и [документекстрактионскилл](cognitive-search-skill-document-extraction.md) для извлечения текста из каждого документа после расшифровки.
- **Запрос PUT для создания индексатора.** При выполнении индексатора считываются данные, применяется набор навыков и сохраняются результаты. Этот запрос необходимо выполнить последним.

[Исходный код](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) содержит коллекцию POST, содержащую четыре запроса, а также некоторые полезные дальнейшие запросы. Чтобы выдать запросы, в Post перейдите на вкладку запросов и выберите **Отправить** для каждого из них.

## <a name="3---monitor-indexing"></a>3. Мониторинг индексирования

Индексирование и обогащение начинаются сразу же после отправки запроса на создание индексатора. В зависимости от количества документов в учетной записи хранения индексирование может занять некоторое время. Чтобы узнать, выполняется ли индексатор, используйте запрос **о состоянии индексатора** , предоставленный в составе коллекции POST, и просмотрите ответ, чтобы узнать, работает ли индексатор, или просмотрите сведения об ошибках и предупреждениях.  

Если используется уровень Free, ожидается следующее сообщение: `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"` . Это сообщение появляется, так как индексирование больших двоичных объектов на уровне Free имеет [ограничение 32 КБ на извлечение символов](search-limits-quotas-capacity.md#indexer-limits). Это сообщение не будет отображаться для этого набора данных на более высоких уровнях. 

## <a name="4---search"></a>4. Поиск

После завершения выполнения индексатора можно выполнить некоторые запросы, чтобы убедиться, что данные успешно расшифрованы и проиндексированы. Перейдите к службе Когнитивный поиск Azure на портале и используйте [Обозреватель поиска](search-explorer.md) для выполнения запросов по индексированным данным.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы успешно проиндексировани зашифрованные файлы, вы можете выполнить [итерацию по этому конвейеру, добавив дополнительные профессиональные навыки](cognitive-search-defining-skillset.md). Это позволит расширить возможности и получить дополнительные сведения о данных.

При работе с удвоенными зашифрованными данными может потребоваться изучить функции шифрования индекса, доступные в Когнитивный поиск Azure. Хотя индексатору требуется расшифровать данные для целей индексирования, после того, как индекс существует, он может быть зашифрован с помощью ключа, управляемого клиентом. Это обеспечит постоянное шифрование данных при хранении. Дополнительные сведения см. [в статье Настройка ключей, управляемых клиентом, для шифрования данных в Azure когнитивный Поиск](search-security-manage-encryption-keys.md).