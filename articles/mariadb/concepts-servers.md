---
title: Серверы — база данных Azure для MariaDB
description: В этой статье приведены рекомендации и указания по работе с серверами базы данных Azure для MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4d8293258083ea3e8d0172f510e5b41e91328736
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541068"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Основные понятия работы с сервером в базе данных Azure для MariaDB
В этой статье приведены рекомендации и указания по работе с серверами базы данных Azure для MariaDB.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Что такое сервер базы данных Azure для MariaDB?

Сервер базы данных Azure для MariaDB является центральной точкой администрирования нескольких баз данных. Подобная серверная конструкция MariaDB может быть вам знакома по работе в локальной среде. В частности, служба базы данных Azure для MariaDB является управляемой, предоставляет гарантии производительности, обеспечивает доступ и функциональность на уровне сервера.

База данных Azure для сервера MariaDB:

- создается в подписке Azure;
- выступает в качестве родительского ресурса для баз данных;
- предоставляет пространство имен для баз данных;
- представляет собой контейнер со строгой семантикой времени существования (при удалении сервера удаляются также все расположенные на нем базы данных);
- выравнивает ресурсы в регионе;
- предоставляет конечную точку подключения к серверу и базе данных;
- обеспечивает область для политик управления, применяемых к базам данных: имена входа, брандмауэр, пользователи, роли, конфигурации и т. д.;
- доступна в версии ядра MariaDB 10.2. Дополнительные сведения см. в статье [Поддерживаемые версии в базе данных Azure для MariaDB](./concepts-supported-versions.md).

На сервере базы данных Azure для MariaDB можно создать одну или несколько баз данных. Можно создать по одной базе данных на каждом сервере, чтобы использовать все ресурсы, или несколько баз данных, чтобы предоставить общий доступ к ресурсам. Цена формируется для каждого сервера исходя из конфигурации ценовой категории, количества виртуальных ядер и объема хранилища (ГБ). Дополнительные сведения см. в разделе [Ценовые категории](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Как защитить сервер базы данных Azure для MariaDB?

Ниже перечислены элементы, которые помогают обеспечить безопасный доступ к базе данных.

|||
| :--| :--|
| **Аутентификация и авторизация** | Сервер базы данных Azure для MariaDB поддерживает собственную аутентификацию MySQL. Подключиться к серверу и выполнить аутентификацию можно с помощью учетных данных администратора сервера. |
| **Протокол** | Служба поддерживает протокол на основе сообщений, используемый MySQL. |
| **TCP/IP** | Протокол работает через TCP/IP, а также через сокеты домена Unix. |
| **Брандмауэр** | Для защиты данных правило брандмауэра запрещает любой доступ к серверу базы данных, пока не будут указаны компьютеры, которые имеют разрешение. См. статью [Правила брандмауэра сервера базы данных Azure для MariaDB](./concepts-firewall-rules.md). |
| **SSL** | Служба поддерживает применение SSL-соединений между приложениями и сервером базы данных. См. [Настройка SSL-подключений в приложении для безопасного подключения к базе данных Azure для MariaDB](./howto-configure-ssl.md). |

## <a name="stopstart-an-azure-database-for-mariadb-preview"></a>Завершение работы и запуск базы данных Azure для MariaDB (Предварительная версия)
База данных Azure для MariaDB позволяет **прекратить** работу сервера, когда он не используется, и **запустить** сервер при возобновлении действия. По сути, это делается для снижения затрат на серверы баз данных и оплаты только тех ресурсов, которые используются. Это еще важнее для рабочих нагрузок разработки и тестирования, а также при использовании сервера только для части дня. При прекращении сервера все активные подключения будут удалены. Позже, когда нужно перевести сервер обратно в режим «в сети», можно использовать [портал Azure](../mysql/how-to-stop-start-server.md) или [CLI](../mysql/how-to-stop-start-server.md).

Если сервер находится в **остановленном** состоянии, плата за вычисление сервера не взимается. Тем не менее, хранилище продолжает оплачиваться по мере того, как хранилище сервера остается доступным для обеспечения доступности файлов данных при повторном запуске сервера.

> [!IMPORTANT]
> При **остановлении** сервера он остается в этом состоянии в течение следующих 7 дней в растяжении. Если не **запустить** его в течение этого времени вручную, сервер будет автоматически запущен в конце 7 дней. Вы можете снова **Отключить** его, если вы не используете сервер.

Во время остановки сервера операции управления на сервере выполняться не могут. Чтобы изменить параметры конфигурации на сервере, необходимо [запустить сервер](../mysql/how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Ограничения для операции "завершение/начало"
- Не поддерживается с конфигурациями реплики чтения (как исходной, так и репликой).

## <a name="how-do-i-manage-a-server"></a>Как управлять сервером?
Управлять серверами базы данных Azure для MariaDB можно с помощью портала Azure или Azure CLI.

## <a name="next-steps"></a>Дальнейшие действия
- Общие сведения об этой службе см. в статье [Обзор базы данных Azure для MariaDB](./overview.md).
- Сведения о квотах и ограничениях для конкретных ресурсов, основанных на **уровне служб** , см. в разделе [уровни служб](./concepts-pricing-tiers.md) .

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
