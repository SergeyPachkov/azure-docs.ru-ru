---
title: включить файл
description: включить файл
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "75477152"
---
В этой справочной информации целевые показатели масштабируемости и производительности для службы хранилища Azure. Приведенные целевые показатели производительности и масштабируемости предельно высоки, но достижимы. В любом случае частота запросов, с которой успешно справляется учетная запись хранения, и ее пропускная способность зависят от размера хранимых объектов, используемых схем доступа и типа рабочей нагрузки приложения.

Обязательно протестируйте службу, чтобы определить, соответствует ли ее производительность вашим требованиям. По возможности избегайте внезапных пиковых нагрузок по трафику. Убедитесь в том, что трафик соответствующим образом распределяется по разделам.

Когда при работе приложения достигается предельная рабочая нагрузка на раздел, служба хранилища Azure начинает выдавать код ошибки 503 (сервер занят) или 500 (время ожидания операции истекло). При возникновении ошибок 503 попробуйте изменить приложение, чтобы при повторных попытках оно использовало политику экспоненциальной задержки. Экспоненциальное откладывание позволяет уменьшить нагрузку на раздел и облегчить обработку им пикового трафика. 
