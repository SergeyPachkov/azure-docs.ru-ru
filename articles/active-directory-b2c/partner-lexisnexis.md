---
title: Руководство по настройке Azure Active Directory B2C с помощью Лексиснексис
titleSuffix: Azure AD B2C
description: Узнайте, как интегрировать проверку подлинности Azure AD B2C с Лексиснексис, которая является службой профилирования и проверки личности и используется для проверки идентификации пользователей и предоставления комплексных оценок рисков на основе устройства пользователя.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c753e9a18f9869e1bf11aa437fb60484f2553e17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259260"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Руководство по настройке Лексиснексис с помощью Azure Active Directory B2C

В этом примере учебника мы предоставляем рекомендации по интеграции Azure AD B2C с [лексиснексис](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). Лексиснексис предоставляет разнообразные решения, их можно найти [здесь](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). В этом учебном курсе мы расскажем о решении **среатметрикс** из лексиснексис. Среатметрикс — это служба для профилирования и проверки личности. Он используется для проверки идентификации пользователей и предоставления комплексных оценок рисков на основе устройства пользователя.

Эта интеграция выполняет профилирование на основе нескольких частей сведений о пользователях, которые предоставляются пользователем во время процесса регистрации. Среатметрикс определяет, должен ли пользователь продолжать входить в систему или нет. В анализе рисков Среатметрикс рассматриваются следующие атрибуты:

- Email
- номер телефона;
- Сведения о профилировании, собранные с компьютера пользователя

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, вам потребуется:

- Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).

- [Клиент Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , связанный с вашей подпиской Azure.

## <a name="scenario-description"></a>Описание сценария

Интеграция Среатметрикс включает следующие компоненты:

- Azure AD B2C — сервер авторизации, отвечающий за проверку учетных данных пользователя, также известный как поставщик удостоверений.

- Среатметрикс — служба Среатметрикс принимает входные данные, предоставленные пользователем, и объединяет ее с данными профилирования, собранными с компьютера пользователя, для проверки безопасности взаимодействия с пользователем.

- Пользовательский API-интерфейс службы "интерфейс пользователя" — Этот API реализует интеграцию между Azure AD B2C и службой Среатметрикс.

Реализация показана на следующей схеме архитектуры.

![снимок экрана для лексиснексис — схема архитектуры](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|Шаг | Описание |
|:--------------|:-------------|
|1. | Пользователь прибывает на странице входа. Пользователь нажимает кнопку Регистрация для создания новой учетной записи и ввода данных на странице. Azure AD B2C собирает атрибуты пользователя.
| 2. | Azure AD B2C вызывает API среднего уровня и передает атрибуты пользователя.
| 3. | API среднего уровня собирает атрибуты пользователя и преобразует их в формат, который может использовать API Лексиснексис. Затем отправляет его в Лексиснексис.  
| 4. | Лексиснексис потребляет информацию и обрабатывает ее для проверки идентификации пользователя на основе анализа рисков. Затем он возвращает результат в API среднего уровня.
| 5. | API среднего уровня обрабатывает сведения и отправляет в Azure AD B2C соответствующие сведения.
| 6. | Azure AD B2C получает сведения от API среднего уровня. Если отображается ответ на сбой, пользователю выдается сообщение об ошибке. Если отображается ответ об успешном выполнении, пользователь проходит проверку подлинности и получает доступ.

## <a name="onboard-with-lexisnexis"></a>Подключение с помощью Лексиснексис

1. Чтобы создать учетную запись Лексиснексис, обратитесь в [лексиснексис](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)

2. Создайте политику Лексиснексис, соответствующую вашим требованиям. Используйте документацию, доступную [здесь](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd).

>[!NOTE]
> Имя политики будет использоваться позже.

После создания учетной записи вы получите сведения, необходимые для настройки API. В следующих разделах описывается процесс.

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>Настройка Azure AD B2C с помощью Лексиснексис

### <a name="part-1---deploy-the-api"></a>Часть 1. Развертывание API

Разверните предоставленный [код API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api) в службе Azure. Код можно опубликовать из Visual Studio, следуя этим [инструкциям](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Вам потребуется URL-адрес развернутой службы, чтобы настроить Azure AD с использованием требуемых параметров.

### <a name="part-2---configure-the-api"></a>Часть 2. Настройка API

Параметры приложения можно [настроить в службе приложений в Azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings).  С помощью этого метода параметры можно безопасно настроить без их возврата в репозиторий. Необходимо предоставить следующие параметры для API-интерфейса RESTful:

| Параметры приложений | Источник | Примечания |
| :-------- | :------------| :-----------|
|Среатметрикс: URL-адрес | Конфигурация учетной записи Среатметрикс |     |
|Среатметрикс: OrgId | Конфигурация учетной записи Среатметрикс |     |
|Среатметрикс: ApiKey |Конфигурация учетной записи Среатметрикс|  |
|Среатметрикс: политика | Имя политики, созданной в Среатметрикс | |
| Басикаус: Апиусернаме |Определение имени пользователя для API| Имя пользователя будет использоваться в конфигурации Azure AD B2C
| Басикаус: Апипассворд | Определение пароля для API | Пароль будет использоваться в конфигурации Azure AD B2C

### <a name="part-3---deploy-the-ui"></a>Часть 3. Развертывание пользовательского интерфейса

В этом решении используются пользовательские шаблоны пользовательского интерфейса, загружаемые Azure AD B2C. Эти шаблоны пользовательского интерфейса выполняют профилирование, которое отправляется непосредственно в службу Среатметрикс.

Инструкции по развертыванию включенных [файлов пользовательского интерфейса](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template) в учетную запись хранения BLOB-объектов см. в этих [инструкциях](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#custom-page-content-walkthrough) . Инструкции включают настройку учетной записи хранения BLOB-объектов, настройку CORS и предоставление общего доступа.

Пользовательский интерфейс основан на [шаблоне «морской цвета](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue)». Все ссылки в пользовательском интерфейсе должны быть обновлены для ссылки на развернутое расположение. В папке пользовательского интерфейса найдите и замените на https://yourblobstorage/blobcontainer развернутое расположение.

### <a name="part-4---create-api-policy-keys"></a>Часть 4. Создание ключей политики API

Ознакомьтесь с этим [документом](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#add-rest-api-username-and-password-policy-keys) и создайте два ключа политики: один для имени пользователя API и один для пароля API, который вы определили выше.

В образце политики используются следующие имена ключей:

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>Часть 5. Обновление URL-адреса API

В указанной [политике TrustFrameworkExtensions](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)найдите технический профиль с именем `Rest-LexisNexus-SessionQuery` и обновите `ServiceUrl` элемент МЕТАДАННЫХ, указав расположение API, развернутого выше.

### <a name="part-6---update-ui-url"></a>Часть 6. Обновление URL-адреса пользовательского интерфейса

В указанной [политике TrustFrameworkExtensions](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)выполните поиск и замену, чтобы найти https://yourblobstorage/blobcontainer/ расположение, в котором развернуты файлы пользовательского интерфейса.

>[!NOTE]
> Рекомендуется, чтобы клиенты добавили уведомление о согласия на страницу коллекции атрибутов. Уведомлять пользователей о том, что сведения будут отправляться сторонним службам для проверки личности.

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>Часть 7. Настройка политики Azure AD B2C

Обратитесь к этому [документу](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) , чтобы скачать файл [Local Accounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) и настроить [политику](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy) для клиента Azure AD B2C.

>[!NOTE]
>Обновите указанные политики, чтобы они были связаны с конкретным клиентом.

## <a name="test-the-user-flow"></a>Тестирование потока пользователя

1. Откройте клиент Azure AD B2C и в разделе политики выберите **потоки пользователя**.

2. Выберите ранее созданный **поток пользователя**.

3. Выберите **запустить поток пользователя** и выберите параметры:

   a. **Приложение**: выберите зарегистрированное приложение (пример — JWT)

   b. **URL-адрес ответа**: выберите **URL-адрес перенаправления**

   c. Выберите **Выполнить поток пользователя**.

4. Последовательно выберите процесс регистрации и создайте учетную запись.

5. Выход из журнала

6. Последовательность входа  

7. После ввода **"продолжить"** появится окно головоломки среатметрикс.

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения см. в следующих статьях:

- [Пользовательские политики в Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Приступая к работе с пользовательскими политиками в Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
