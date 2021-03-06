---
title: Самостоятельный обмен и возмещение средств для резервирований Azure
description: Узнайте, как можно обменять Azure Reserved Virtual Machine Instances или вернуть деньги за них. Для обмена или возврата денег вам нужны права владельца в заказе на резервирование.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 32db8396a687428c668a9b8a4213b50986614083
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150129"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Самостоятельный обмен и возмещение средств для резервирований Azure

Azure Reserved Virtual Machine Instances обеспечивают гибкость, позволяющую удовлетворить ваши требования. Зарезервированный экземпляр можно обменять на другой зарезервированный экземпляр того же типа. Например, вы можете обменять резервирование виртуальной машины, чтобы приобрести другое резервирование для любого другого размера или региона виртуальной машины. Аналогичным образом, резервирования базы данных SQL PaaS можно обменять на приобретение другого резервирования базы данных SQL PaaS любого доступного типа в любом регионе. Вы также можете возместить средства за резервирование, но общая сумма всех отмененных обязательств по резервированию в области выставления счетов (таких как EA, Клиентское соглашение Майкрософт и Соглашение с партнером Майкрософт) не может превышать 50 000 долларов США за 12 месяцев (скользящий интервал). Зарезервированная емкость Azure Databricks, резервирование решений Azure VMware от CloudSimple, резервирование Open Shift в Azure Red Hat, планы Red Hat и планы SUSE Linux недоступны для возмещения.

Самостоятельный обмен и возможность отмены недоступны для клиентов с соглашением Enterprise для US Gov организаций. Другие типы подписок для государственных организаций США, включая подписку с оплатой по мере использования и подписку в рамках программы "Поставщик облачных решений" (CSP), поддерживаются.

> [!NOTE]
> - **Для обмена имеющегося резервирования или возврата денег за него вам понадобятся права владельца в соответствующем заказе на резервирование.** См. также раздел [Добавление или изменение пользователей, которые могут управлять резервированием](./manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
> - Сейчас корпорация Майкрософт не взимает плату за досрочную отмену при возмещениях за резервирования. Но мы можем установить такую плату за возмещения в будущем. Пока дата начала применения такой платы не определена.

## <a name="how-to-exchange-or-refund-an-existing-reservation"></a>Обмен или возмещение по существующему резервированию

Вы можете обменять резервирование на [портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Выберите резервирования, за которые нужно вернуть деньги, и щелкните **Обмен**.  
    [![Пример изображения, на котором показаны резервирования для возврата](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png#lightbox)
1. Выберите продукт в категории виртуальных машин, который вы хотите приобрести, и введите количество. Убедитесь, что сумма нового приобретения больше суммы возврата. [Определите правильный размер перед покупкой](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    [![Пример изображения, на котором показан продукт виртуальной машины для приобретения при обмене](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png#lightbox)
1. Проверьте и завершите транзакцию.  
    [![Пример изображения, на котором показан продукт виртуальной машины для приобретения при обмене после завершения возврата](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png#lightbox)

Чтобы вернуть деньги за резервирование, перейдите в раздел **Сведения о резервировании** и выберите **Возврат**.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Обмен хранилища класса, отличного от Premium, на хранилище класса Premium

Вы можете обменять приобретенное резервирование с размером виртуальной машины, который не поддерживает хранилище класса Premium, на размер виртуальной машины, поддерживающий хранилище класса Premium. Например, _F1_ на _F1s_. Чтобы выполнить обмен, перейдите в раздел сведений о резервировании и выберите **Обмен**. При обмене не сбрасывается срок действия зарезервированного экземпляра или не создается транзакция. 

## <a name="how-transactions-are-processed"></a>Обработка транзакций

Сначала корпорация Майкрософт отменяет имеющееся резервирование и возмещает пропорциональную сумму за это резервирование. При обмене происходит новая покупка. Корпорация Майкрософт проводит возмещение, используя один из следующих методов, в зависимости от типа учетной записи и метода оплаты:

### <a name="enterprise-agreement-customers"></a>Пользователи с соглашением Enterprise

Деньги добавляются в денежные обязательства по обмену и возмещению, если исходная покупка была выполнена с помощью этого метода. Если срок действия денежного обязательства, в рамках которого используется приобретенное резервирование, завершился, кредит будет добавлен к текущему сроку действия денежного обязательства в рамках соглашения Enterprise. Кредит действителен в течение 90 дней с момента возврата. Срок действия неиспользованного кредита истекает через 90 дней.

Если исходная покупка выполнялась как избыточная, то исходный счет, по которому было приобретено резервирование, и все последующие счета повторно открываются и корректируются соответствующим образом. Корпорация Майкрософт выдает кредит-ноту для возвратных платежей.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Оплата счетов по мере использования и программа CSP

Исходный счет на покупку резервирования отменяется, а затем для возмещения создается новый счет. При обмене в новом счете отображается возмещение и новая покупка. Сумма возмещения корректируется в соответствии с покупкой. Если вы осуществили возмещение средств за резервирование, то пропорциональная сумма удерживается корпорацией Майкрософт и используется для будущих покупок резервирования. Если вы ранее приобрели резервирование по тарифам с оплатой по мере использования, а потом перешли на CSP, резервирование можно вернуть и приобрести повторно без штрафа.

### <a name="pay-as-you-go-credit-card-customers"></a>Клиенты с кредитными картами с оплатой по мере использования

Исходный счет отменяется и создается новый. Деньги возмещаются на кредитную карту, которая использовалась для первоначальной покупки. Если вы изменили карточку, [обратитесь в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Политики отмены, обмена и возврата денег

В Azure имеются следующие политики для отмены, обмена и возврата денег.

**Политики обмена**

- Вы можете вернуть несколько существующих резервирований, чтобы приобрести одно новое резервирование того же типа. Нельзя обменять резервирования одного типа на другой. Например, нельзя вернуть резервирование виртуальной машины для приобретения резервирования SQL. Вы можете изменить свойство резервирования, например семейство, серию, версию, номер SKU, регион, количество и срок действия в рамках обмена.
- Только владельцы резервирования могут осуществлять обмен. [Узнайте, как добавлять или изменять пользователей, которые могут управлять резервированием](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Обмен обрабатывается как возврат и повторное приобретение: создаются разные транзакции для отмены и покупки нового резервирования. За резервирования, которые вы возвращаете, возмещается пропорциональная сумма резервирования. Вы полностью оплачиваете новую покупку. Пропорциональная сумма резервирования — это ежедневная пропорциональная остаточная сумма возвращаемого резервирования.
- Вы можете обменять или вернуть деньги за резервирование, даже если соглашение Enterprise, в рамках которого было приобретено резервирование, больше не действует и было изменено на новое.
- Суммарное обязательство за время существования для нового резервирования не может быть меньше, чем недоиспользованное обязательство по прежнему резервированию. Например, если было приобретено резервирование на три года с суммой 100 долларов США в месяц, то при его обмене после 18-го платежа новое резервирование должно иметь общую сумму не менее 1800 долларов США (с оплатой ежемесячно или по предоплате).
- Новое резервирование, приобретенное в рамках обмена, имеет новый срок действия, начиная с момента обмена.
- За обмен не предусмотрены штрафы или годовые ограничения.

**Политики возврата денег**

- Сейчас мы не взимаем плату за досрочную отмену, но в будущем может взиматься штраф в размере 12 %.
- Общая сумма отмененных обязательств не может превышать 50 000 долларов США за 12 месяцев (скользящий интервал) для профиля выставления счетов или однократной регистрации. Например, для резервирования сроком на три года с суммой обязательства 100 долларов США в месяц при возмещении в течение 18-го месяца отмененное обязательство составляет 1800 долларов США. После такого возмещения ваш доступный лимит возмещений составит 48 200 долларов США. Через 365 дней после даты этого возмещения доступный лимит (48 200 долларов США) увеличится на 1800 долларов США и снова составит 50 000 долларов США. При любой другой отмене резервирования для профиля выставления счетов или регистрации EA этот лимит точно так же уменьшается с той же логикой восстановления лимита.
- Azure не будет обрабатывать возврат средств, превышающий лимит в 50 000 долларов США за 12 месяцев для профиля выставления счетов или регистрации EA.
- Сумма возмещенных средств рассчитывается на основе наименьшей стоимости, например на стоимости покупки или текущей стоимости резервирования.
- Только владельцы заказа на резервирование могут осуществлять возмещение. [Узнайте, как добавлять или изменять пользователей, которые могут управлять резервированием](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Дальнейшие действия

- Сведения об управлении резервированием см. в разделе [Управление резервированиями в Azure](manage-reserved-vm-instance.md).
- Дополнительные сведения о резервировании в Azure см. по следующим ссылкам:
    - [Основные сведения о резервировании в Azure](save-compute-costs-reservations.md)
    - [Управление резервированиями в Azure](manage-reserved-vm-instance.md)
    - [Сведения о применении скидки на зарезервированный экземпляр виртуальной машины](../manage/understand-vm-reservation-charges.md)
    - [Общие сведения об использовании резервирования Azure для подписки с оплатой по мере использования](understand-reserved-instance-usage.md)
    - [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](understand-reserved-instance-usage-ea.md)
    - [Затраты на программное обеспечение Windows, которые не включены в стоимость зарезервированных экземпляров Azure](reserved-instance-windows-software-costs.md)
    - [Резервирования в Azure по программе CSP](/partner-center/azure-reservations)