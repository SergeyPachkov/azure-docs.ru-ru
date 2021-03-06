---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 2f490a5b12484a91e963d068810b292d7761521a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484309"
---
> [!NOTE]
> Службу Azure DNS можно использовать для настройки пользовательского DNS-имени для Службы приложений Azure. Дополнительные сведения см. в статье [Использование Azure DNS для указания параметров личного домена для службы Azure](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

1. Войдите на веб-сайт своего поставщика домена.

1. Найдите страницу управления записями DNS. Каждый поставщик домена имеет свой собственный интерфейс записей DNS, поэтому вам следует обратиться к документации поставщика. Найдите области сайта, обозначенные как **Имя домена**, **DNS** или **Name Server Management** (Управление сервером доменных имен).

   Часто страницу записей DNS можно найти, открыв раздел со сведениями о своей учетной записи и найдя такую ссылку, как **Мои домены**. Перейдите на эту страницу и найдите ссылку с именем наподобие **Файл зоны**, **Записи DNS** или **Расширенная конфигурация**.

   На снимке экрана ниже показан пример страницы с записями DNS:

   ![Снимок экрана, на котором показан пример страницы с записями DNS.](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. В примере снимка экрана нужно выбрать команду **Добавить** для создания записи. Некоторые поставщики имеют разные ссылки для добавления записей различных типов. Обратитесь к документации поставщика.

> [!NOTE]
> У некоторых поставщиков, например GoDaddy, изменения записей DNS не вступают в силу, пока вы не щелкнете ссылку **Сохранить изменения**.
