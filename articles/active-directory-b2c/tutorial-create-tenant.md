---
title: Руководство по созданию клиента Azure Active Directory B2C
description: Из этого учебника вы узнаете, как подготовить приложения к регистрации, создав арендатор Azure Active Directory B2C с помощью портала Azure.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dce41f979a46ae2bda568b5db79f0e0304705dd8
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670211"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Руководство по Создание клиента Azure Active Directory B2C

Чтобы приложения могли взаимодействовать с Azure Active Directory B2C (Azure AD B2C), их нужно зарегистрировать в клиенте, которым вы управляете.

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание клиента Azure AD B2C
> * Привязка клиента к подписке
> * переход в каталог, где размещен клиент Azure AD B2C;
> * добавление ресурса Azure AD B2C в категорию **Избранное** на портале Azure.

Сведения о регистрации приложения приводятся в следующем руководстве.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-an-azure-ad-b2c-tenant"></a>Создание клиента Azure AD B2C

1. Войдите на [портал Azure](https://portal.azure.com/). Войдите в систему, используя учетную запись Azure, которой назначена по меньшей мере роль [Участник](../role-based-access-control/built-in-roles.md) в подписке или группе ресурсов в подписке.

1. Выберите каталог, который содержит нужную подписку.

    На панели инструментов портала Azure выберите значок **Directory + Subscription** (Каталог и подписка), а затем выберите каталог, содержащий нужную подписку. Это не тот каталог, в котором будет содержаться клиент Azure AD B2C.

    ![Клиент подписки, где установлен фильтр каталогов и подписки и выбран клиент подписки](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. На **домашней странице** или в меню портала Azure выберите **Создать ресурс**.
1. Выполните поиск по строке **Azure Active Directory B2C** и выберите **Создать**.
1. Выберите **Создать B2C-клиент Azure AD**.

    ![Портал Azure, где выбрана операция создания нового клиента Azure AD B2C](media/tutorial-create-tenant/portal-02-create-tenant.png)
    
1. Укажите **имя организации** и **первоначальное доменное имя**. Выберите **страну или регион** (эти сведения нельзя изменить позже), а затем выберите элемент **Создать**.

    Доменное имя используется как часть полного доменного имени клиента. В этом примере имя клиента — *contosob2c.onmicrosoft.com* :

1. После создания клиента выберите ссылку **Создание клиента B2C или связь с существующим клиентом** в верхней части страницы создания клиента.

    ![Строка навигации связывания с клиентом, выделенная на портале Azure](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Выберите элемент **Связывание существующего B2C-клиента Azure AD с вашей подпиской Azure**. Для выполнения этого шага необходимо войти в систему с ролью владельца.

   ![Выбор параметра связывания существующей подписки на портале Azure](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Выберите созданный **клиент Azure AD B2C** , а затем выберите свою **подписку**.

    Для параметра **Группа ресурсов** выберите **Создать**. Введите **имя** для группы ресурсов, которая будет содержать клиент, выберите **расположение группы ресурсов** и нажмите кнопку **Создать**.

    ![Форма привязывания параметров подписки на портале Azure](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)
    

Вы можете связать несколько клиентов Azure AD B2C с одной подпиской Azure для выставления счетов. Чтобы связать клиент, нужно иметь права администратора в клиенте Azure AD B2C и по меньшей мере роль "Участник" в подписке Azure. Дополнительные сведения см. в разделе [Связывание клиента Azure AD B2C с подпиской](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).

## <a name="select-your-b2c-tenant-directory"></a>Выбор каталога клиента B2C

Чтобы приступить к использованию нового клиента Azure AD B2C, нужно перейти в каталог, содержащий этот клиент.

Выберите фильтр **Directory + subscription** (Каталог и подписка) в верхнем меню на портале Azure, а затем выберите каталог, содержащий клиент Azure AD B2C.

Если вы не увидите новый клиент Azure B2C в этом списке, обновите окно браузера и снова выберите фильтр **Directory + subscription** (Каталог и подписка) в верхнем меню.

![Портал Azure, где выбран каталог с клиентом B2C](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Добавление Azure AD B2C в избранное (необязательно)

Этот необязательный шаг упрощает выбор клиента Azure AD B2C в рамках следующего и всех дальнейших руководств.

Чтобы не искать *Azure AD B2C* с помощью раздела **Все службы** каждый раз, когда потребуется работать с клиентом, вы можете внести этот ресурс в категорию "Избранное". После этого он появится в разделе **Избранное** в меню портала, где его можно выбрать для быстрого перехода к клиенту Azure AD B2C.

Эта операция выполняется только один раз. Перед выполнением этим убедитесь, что вы перешли в каталог, который содержит клиент Azure AD B2C, как описано в предыдущем разделе [Выбор каталога клиента B2C](#select-your-b2c-tenant-directory).

1. Войдите на [портал Azure](https://portal.azure.com).
1. В меню портала Azure выберите **Все службы**.
1. В поле поиска **Все службы** введите строку **Azure AD B2C** , наведите указатель мыши на результат поиска и щелкните значок звездочки в подсказке. Теперь **Azure AD B2C** появится в разделе **Избранное** на портале Azure.
1. Если вы хотите изменить расположение нового элемента в списке избранного, откройте меню портала Azure, выберите **Azure AD B2C** и перетащите этот элемент вверх или вниз в нужное расположение.

    ![Azure AD B2C, меню "Избранное", портал Microsoft Azure](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание клиента Azure AD B2C
> * Привязка клиента к подписке
> * переход в каталог, где размещен клиент Azure AD B2C;
> * добавление ресурса Azure AD B2C в категорию **Избранное** на портале Azure.

Теперь вы узнаете, как зарегистрировать веб-приложение в новом клиенте.

> [!div class="nextstepaction"]
> [Зарегистрировать приложения >](tutorial-register-applications.md)
