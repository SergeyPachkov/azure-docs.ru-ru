---
title: Руководство по интеграции единого входа Azure Active Directory с Darwinbox | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Darwinbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.openlocfilehash: 70c77caebfd8f9bfd36c7384255cf7b66416a379
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454989"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-darwinbox"></a>Руководство по интеграции единого входа Azure Active Directory с Darwinbox

В этом руководстве описано, как интегрировать Darwinbox с Azure Active Directory (Azure AD). Интеграция Darwinbox с Azure AD обеспечивает следующие возможности:

* Контроль доступа к Darwinbox с помощью Azure AD.
* Включение автоматического входа пользователей в Darwinbox с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.
Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее.

* Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Darwinbox с поддержкой единого входа.
> [!NOTE]
> Эту интеграцию также можно использовать в облачной среде Azure AD для государственных организаций США. Это приложение можно найти в коллекции облачных приложений с поддержкой Azure AD для государственных организаций США и настроить таким же образом, как и в общедоступном облаке.


## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Darwinbox поддерживает единый вход, инициированный **поставщиком услуг**.

## <a name="adding-darwinbox-from-the-gallery"></a>Добавление Darwinbox из коллекции

Чтобы настроить интеграцию Darwinbox с Azure AD, необходимо добавить Darwinbox из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Darwinbox**.
1. Выберите **Darwinbox** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on-for-darwinbox"></a>Настройка и проверка единого входа Azure AD в Darwinbox

Настройте и проверьте единый вход Azure AD в Darwinbox с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Darwinbox.

Чтобы настроить и проверить единый вход Azure AD в Darwinbox, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Darwinbox](#configure-darwinbox-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Darwinbox](#create-darwinbox-test-user)** требуется для того, чтобы в Darwinbox существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Darwinbox** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

   1. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<SUBDOMAIN>.darwinbox.in/`.

   1. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://<SUBDOMAIN>.darwinbox.in/adfs/module.php/saml/sp/metadata.php/<CUSTOMID>`.

      > [!NOTE]
      > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки Darwinbox](https://darwinbox.com/contact-us.php). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **XML метаданных федерации** и выберите **Скачать** , чтобы скачать сертификат и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

1. Скопируйте требуемые URL-адреса в разделе **Настройка Darwinbox**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как на портале Azure создать тестового пользователя с именем B.Simon.

1. На портале Azure в области слева выберите **Azure Active Directory** , **Пользователи** , а затем — **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `B.Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `B.Simon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя B.Simon, предоставив ему доступ к приложению Darwinbox.

1. На портале Azure выберите **Корпоративные приложения** , а затем — **Все приложения**.
1. В списке приложений выберите **Darwinbox**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя** , а в диалоговом окне **Добавление назначения**  выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать** , расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-darwinbox-sso"></a>Настройка единого входа в Darwinbox

Чтобы настроить единый вход на стороне **Darwinbox** , нужно отправить скачанный **XML-файл метаданных федерации** и соответствующие URL-адреса, скопированные на портале Azure, в [службу поддержки Darwinbox](https://darwinbox.com/contact-us.php). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-darwinbox-test-user"></a>Создание тестового пользователя Darwinbox

Из этого раздела вы узнаете, как создать пользователя B.Simon в приложении Darwinbox. Чтобы добавить пользователей на платформу Darwinbox, обратитесь в [службу поддержки Darwinbox](https://darwinbox.com/contact-us.php). Перед использованием единого входа необходимо создать и активировать пользователей.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Darwinbox на Панели доступа, вы автоматически войдете в приложение Darwinbox, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](../user-help/my-apps-portal-end-user-access.md)

## <a name="test-sso-for-darwinbox-mobile"></a>Проверка единого входа для Darwinbox (на мобильных устройствах)

1. Откройте мобильное приложение Darwinbox. Щелкните **Enter Organization URL** (Ввести URL-адрес организации), введите URL-адрес своей организации в текстовое поле и нажмите кнопку со стрелкой.

    ![Снимок экрана, на котором показано мобильное приложение Darwinbox с выбранным параметром "Enter Organization URL" (Ввести URL-адрес организации), а также пример организации и выбранная кнопка со стрелкой](media/darwinbox-tutorial/DarwinboxMobile01.jpg)

1. Если у вас несколько доменов, щелкните свой домен.

    ![Снимок экрана, на котором показан экран "Choose your domain" (Выбор домена) с выбранным примером домена](media/darwinbox-tutorial/DarwinboxMobile02.jpg)

1. Введите адрес электронной почты Azure AD в приложение Darwinbox и нажмите **Далее**.

    ![Снимок экрана, на котором показан экран "Sign in" (Вход) с выбранной кнопкой "Next" (Далее).](media/darwinbox-tutorial/DarwinboxMobile03.jpg)

1. Введите пароль Azure AD в приложение Darwinbox и нажмите **Войти**.

    ![Снимок экрана, на котором показан экран "Enter password" (Ввод пароля) с выбранной кнопкой "Next" (Далее)](media/darwinbox-tutorial/DarwinboxMobile04.jpg)

1. Наконец, после успешного входа на сайт отобразится домашняя страница приложения.

    ![Мобильное приложение Darwinbox](media/darwinbox-tutorial/DarwinboxMobile05.jpg)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](./tutorial-list.md)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Что представляет собой условный доступ в Azure Active Directory?](../conditional-access/overview.md)

- [Проверка работы Darwinbox с Azure AD](https://aad.portal.azure.com/)