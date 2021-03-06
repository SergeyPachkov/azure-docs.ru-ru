---
title: Присоединение виртуальной машины SLE к доменным службам Azure AD | Документация Майкрософт
description: Узнайте, как настроить и присоединить виртуальную машину SUSE Linux Enterprise к управляемому домену доменных служб Azure AD.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: joflore
ms.openlocfilehash: 607d3bc8eca3bd969f0f47ca95923040fb22591e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275862"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Присоединение виртуальной машины SUSE Linux Enterprise к управляемому домену доменных служб Azure Active Directory

Чтобы разрешить пользователям входить на виртуальные машины в Azure с помощью одного набора учетных данных, можно присоединить виртуальные машины к управляемому домену Azure Active Directory доменных служб (Azure AD DS). При присоединении виртуальной машины к управляемому домену AD DS Azure учетные записи пользователей и учетные данные из домена можно использовать для входа и управления серверами. Также применяются членства в группах из управляемого домена, позволяющие управлять доступом к файлам и службам на виртуальной машине.

В этой статье показано, как присоединить виртуальную машину SUSE Linux Enterprise (SLE) к управляемому домену.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуются следующие ресурсы и разрешения:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, [создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * Если потребуется, по инструкциям из первого руководства [создайте и настройте управляемый домен доменных служб Azure Active Directory][create-azure-ad-ds-instance].
* Учетная запись пользователя, входящая в управляемый домен.

## <a name="create-and-connect-to-a-sle-linux-vm"></a>Создание виртуальной машины SLE Linux и подключение к ней

Если у вас есть существующая виртуальная машина SLE Linux в Azure, подключитесь к ней с помощью SSH, а затем перейдите к следующему шагу, чтобы [приступить к настройке виртуальной машины](#configure-the-hosts-file).

Если необходимо создать ВИРТУАЛЬную машину SLE Linux или создать тестовую виртуальную машину для использования с этой статьей, можно использовать один из следующих методов.

* [Портал Azure](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

При создании виртуальной машины Обратите внимание на параметры виртуальной сети, чтобы убедиться, что виртуальная машина может обмениваться данными с управляемым доменом.

* Разверните виртуальную машину в той же или в одноранговой виртуальной сети, в которой включены доменные службы Azure AD.
* Разверните виртуальную машину в другой подсети, чем управляемый домен доменных служб Azure AD.

После развертывания виртуальной машины выполните действия по подключению к виртуальной машине по протоколу SSH.

## <a name="configure-the-hosts-file"></a>Настройка файла hosts

Чтобы убедиться, что имя узла виртуальной машины правильно настроено для управляемого домена, измените файл */etc/hosts* и задайте имя узла:

```console
sudo vi /etc/hosts
```

В файле *hosts* обновите адрес *localhost* . В следующем примере:

* *aaddscontoso.com* — это доменное имя DNS управляемого домена.
* *Linux-q2gr* — это имя узла ВИРТУАЛЬНОЙ машины SLE, присоединяемой к управляемому домену.

Обновите эти имена собственными значениями:

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

По завершении сохраните и закройте файл *hosts* с помощью `:wq` команды редактора.

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>Присоединение виртуальной машины к управляемому домену с помощью SSSD

Чтобы присоединиться к управляемому домену с помощью **SSSD** и модуля *управления входом пользователя* YaST, выполните следующие действия.

1. Установите модуль *управления входом пользователя* YaST:

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. Откройте YaST.

1. Чтобы успешно использовать автообнаружение DNS, настройте управляемые IP-адреса домена ( *Active Directory сервер*) в качестве сервера доменных имен для клиента.

    В YaST выберите **System > параметры сети**.

1. Выберите вкладку *HostName/DNS* , а затем введите IP-адреса управляемого домена в текстовое поле *имя сервера 1*. Эти IP-адреса отображаются в окне *Свойства* в портал Azure для управляемого домена, например *10.0.2.4* и *10.0.2.5*.

    Добавьте собственные IP-адреса управляемых доменов, а затем нажмите кнопку **ОК**.

1. В главном окне YaST выберите *Сетевые службы*  >  *управление входом пользователя*.

    Откроется модуль с обзором различных свойств сети компьютера и используемого метода проверки подлинности, как показано на следующем примере снимка экрана:

    ![Пример снимка экрана для окна управления входом пользователя в YaST](./media/join-suse-linux-vm/overview-window.png)

    Чтобы начать редактирование, выберите **изменить параметры**.

Чтобы присоединить виртуальную машину к управляемому домену, выполните следующие действия.

1. В диалоговом окне выберите **Добавить домен**.

1. Укажите правильное *доменное имя*, например *aaddscontoso.com*, а затем укажите службы, используемые для идентификации данных и проверки подлинности. Выберите *Microsoft Active Directory* для обоих.

    Убедитесь, что выбран параметр *включить домен* .

1. Когда все будет готово, щелкните **ОК**.

1. Примите параметры по умолчанию в следующем диалоговом окне, а затем нажмите кнопку **ОК**.

1. При необходимости виртуальная машина устанавливает дополнительное программное обеспечение, а затем проверяет, доступен ли управляемый домен.

    Если все правильно, в следующем примере отображается диалоговое окно, в котором указывается, что виртуальная машина обнаружила управляемый домен, но *еще не зарегистрирована*.

    ![Пример снимка экрана Active Directory окна регистрации в YaST](./media/join-suse-linux-vm/enroll-window.png)

1. В диалоговом окне укажите *имя* и *пароль* пользователя, который является частью управляемого домена. При необходимости [добавьте учетную запись пользователя в группу в Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Чтобы убедиться, что текущий домен включен для Samba, активируйте *перезапись конфигурации Samba для работы с этим AD*.

1. Для регистрации нажмите кнопку **ОК**.

1. Отобразится сообщение с подтверждением успешной регистрации. Для завершения нажмите кнопку **ОК**.

После регистрации виртуальной машины в управляемом домене настройте клиент с помощью команды *управление входом пользователя домена*, как показано на следующем примере снимка экрана:

![Пример снимка экрана: Управление окном входа пользователя домена в YaST](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. Чтобы разрешить вход с использованием данных, предоставленных управляемым доменом, установите флажок *Разрешить вход пользователя в домен*.

1. При необходимости в разделе *Включение источника данных домена*проверьте наличие дополнительных источников данных, необходимых для вашей среды. Эти параметры включают пользователей, которым разрешено использовать **sudo** или доступные сетевые диски.

1. Чтобы разрешить пользователям управляемого домена использовать домашние каталоги на виртуальной машине, установите флажок *создать домашние каталоги*.

1. На боковой панели выберите **Параметры службы › имя коммутатора**, а затем *Расширенные параметры*. В этом окне выберите либо *fallback_homedir* , либо *override_homedir*, а затем нажмите кнопку **добавить**.

1. Укажите значение для расположения домашнего каталога. Чтобы домашняя папка соблюдались в формате */хоме/user_name*, используйте */Хоме/%у*. Дополнительные сведения о возможных переменных см. на справочной странице SSSD. conf ( `man 5 sssd.conf` ) раздела *override_homedir*.

1. Щелкните **ОК**.

1. Чтобы сохранить изменения, нажмите кнопку **ОК**. Затем убедитесь, что значения отображаются правильно. Чтобы выйти из диалогового окна, нажмите **кнопку Отмена**.

1. Если планируется одновременное выполнение SSSD и Winbind (например, при соединении через SSSD, но при этом выполняется файловый сервер Samba), в *параметре* Samba следует задать *секреты и keytab* в SMB. conf. Параметр SSSD *ad_update_samba_machine_account_password* также должен иметь значение *true* в SSSD. conf. Эти параметры запрещают keytabу системы синхронизироваться.

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Присоединение виртуальной машины к управляемому домену с помощью Winbind

Чтобы присоединиться к управляемому домену с помощью **Winbind** и модуля *членства в домене Windows* YaST, выполните следующие действия.

1. В YaST выберите **Сетевые службы > членство в домене Windows**.

1. Введите домен для присоединение в *домене или рабочей группе* на экране *членства в домене Windows* . Введите имя управляемого домена, например *aaddscontoso.com*.

    ![Пример снимка экрана окна членства в домене Windows в YaST](./media/join-suse-linux-vm/samba-client-window.png)

1. Чтобы использовать источник SMB для проверки подлинности Linux, установите флажок *использовать сведения об SMB для проверки подлинности Linux*.

1. Чтобы автоматически создать локальный корневой каталог для пользователей управляемого домена на виртуальной машине, установите флажок *создать домашний каталог при входе*.

1. Установите флажок для *автономной проверки подлинности* , чтобы разрешить пользователям домена выполнять вход, даже если управляемый домен временно недоступен.

1. Если вы хотите изменить диапазоны UID и GID для пользователей и групп Samba, выберите *параметры эксперта*.

1. Настройте синхронизацию времени по протоколу NTP для управляемого домена, выбрав *Конфигурация NTP*. Введите IP-адреса управляемого домена. Эти IP-адреса отображаются в окне *Свойства* в портал Azure для управляемого домена, например *10.0.2.4* и *10.0.2.5*.

1. Нажмите кнопку **ОК** и подтвердите присоединение к домену при появлении соответствующего запроса.

1. Укажите пароль администратора в управляемом домене и нажмите кнопку **ОК**.

    ![Пример снимка экрана диалогового окна проверки подлинности при присоединении виртуальной машины SLE к управляемому домену](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

После присоединения к управляемому домену вы можете войти на него с рабочей станции, используя диспетчер экранов рабочего стола или консоли.

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-yast-command-line-interface"></a>Присоединение виртуальной машины к управляемому домену с помощью Winbind из интерфейса командной строки YaST

Чтобы присоединиться к управляемому домену с помощью **Winbind** и *интерфейса командной строки YaST*, выполните следующие действия.

* Присоединиться к домену:

  ```console
  sudo yast samba-client joindomain domain=aaddscontoso.com user=<admin> password=<admin password> machine=<(optional) machine account>
  ```

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-terminal"></a>Присоединение виртуальной машины к управляемому домену с помощью Winbind из терминала

Чтобы присоединиться к управляемому домену с помощью **Winbind** и выполните * `samba net` команду*:

1. Установите клиент Kerberos и Samba-winbind:

   ```console
   sudo zypper in krb5-client samba-winbind
   ```

2. Измените файлы конфигурации.

   * /етк/самба/смб.конф
   
     ```ini
     [global]
         workgroup = AADDSCONTOSO
         usershare allow guests = NO #disallow guests from sharing
         idmap config * : backend = tdb
         idmap config * : range = 1000000-1999999
         idmap config AADDSCONTOSO : backend = rid
         idmap config AADDSCONTOSO : range = 5000000-5999999
         kerberos method = secrets and keytab
         realm = AADDSCONTOSO.COM
         security = ADS
         template homedir = /home/%D/%U
         template shell = /bin/bash
         winbind offline logon = yes
         winbind refresh tickets = yes
     ```

   * /etc/krb5.conf
   
     ```ini
     [libdefaults]
         default_realm = AADDSCONTOSO.COM
         clockskew = 300
     [realms]
         AADDSCONTOSO.COM = {
             kdc = PDC.AADDSCONTOSO.COM
             default_domain = AADDSCONTOSO.COM
             admin_server = PDC.AADDSCONTOSO.COM
         }
     [domain_realm]
         .aaddscontoso.com = AADDSCONTOSO.COM
     [appdefaults]
         pam = {
             ticket_lifetime = 1d
             renew_lifetime = 1d
             forwardable = true
             proxiable = false
             minimum_uid = 1
         }
     ```

   * /ЕТК/секурити/pam_winbind. conf
   
     ```ini
     [global]
         cached_login = yes
         krb5_auth = yes
         krb5_ccache_type = FILE
         warn_pwd_expire = 14
     ```

   * /етк/нссвитч.конф
   
     ```ini
     passwd: compat winbind
     group: compat winbind
     ```

3. Убедитесь, что дата и время в Azure AD и Linux синхронизированы. Это можно сделать, добавив сервер Azure AD в службу NTP:
   
   1. Добавьте следующую строку в/ЕТК/НТП.конф:
     
      ```console
      server aaddscontoso.com
      ```

   1. Перезапустите службу NTP:
     
      ```console
      sudo systemctl restart ntpd
      ```

4. Присоединиться к домену:

   ```console
   sudo net ads join -U Administrator%Mypassword
   ```

5. Включите Winbind в качестве источника входа в модули подключаемого модуля проверки подлинности Linux (PAM):

   ```console
   pam-config --add --winbind
   ```

6. Включите автоматическое создание домашних каталогов, чтобы пользователи могли входить в систему:

   ```console
   pam-config -a --mkhomedir
   ```

7. Запуск и включение службы Winbind:

   ```console
   sudo systemctl enable winbind
   sudo systemctl start winbind
   ```

## <a name="allow-password-authentication-for-ssh"></a>Разрешить проверку пароля для SSH

По умолчанию пользователи могут входить в виртуальную машину только с помощью проверки подлинности на основе открытых ключей SSH. Проверка подлинности на основе пароля завершается сбоем. При присоединении виртуальной машины к управляемому домену эти учетные записи домена должны использовать проверку подлинности на основе пароля. Обновите конфигурацию SSH, чтобы разрешить проверку подлинности на основе пароля, как показано ниже.

1. Откройте файл *sshd_conf* с помощью редактора:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Обновите строку для *PasswordAuthentication* на *"Да"*:

    ```console
    PasswordAuthentication yes
    ```

    По завершении сохраните и закройте файл *sshd_conf* с помощью `:wq` команды редактора.

1. Чтобы применить изменения и позволить пользователям входить с помощью пароля, перезапустите службу SSH:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Предоставление привилегий суперпользователя группе "Администраторы контроллера домена AAD"

Чтобы предоставить членам группы *администраторов контроллера домена AAD* права администратора на ВИРТУАЛЬНОЙ машине SLE, добавьте запись в */etc/sudoers*. После добавления члены группы *администраторов контроллера домена AAD* могут использовать `sudo` команду на виртуальной машине SLE.

1. Откройте файл " *sudo* " для редактирования:

    ```console
    sudo visudo
    ```

1. Добавьте следующую запись в конец файла */etc/sudoers* . Группа *администраторов контроллера домена AAD* содержит пробелы в имени, поэтому включите escape-символ обратной косой черты в имя группы. Добавьте собственное доменное имя, например *aaddscontoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    По завершении сохраните и закройте редактор с помощью `:wq` команды редактора.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Вход в виртуальную машину с помощью учетной записи домена

Чтобы убедиться, что виртуальная машина успешно присоединена к управляемому домену, запустите новое SSH-подключение, используя учетную запись пользователя домена. Убедитесь, что был создан корневой каталог и применяется членство в группе из домена.

1. Создайте новое SSH-подключение из консоли. Используйте учетную запись домена, принадлежащую к управляемому домену, с помощью `ssh -l` команды, например, `contosoadmin@aaddscontoso.com` и введите адрес виртуальной машины, например *Linux-q2gr.aaddscontoso.com*. При использовании Azure Cloud Shell Используйте общедоступный IP-адрес виртуальной машины, а не внутреннее DNS-имя.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. После успешного подключения к виртуальной машине убедитесь, что корневой каталог был инициализирован правильно:

    ```console
    pwd
    ```

    Вы должны находиться в каталоге */Home* с собственным каталогом, совпадающим с учетной записью пользователя.

3. Теперь убедитесь, что членство в группе разрешается правильно:

    ```console
    id
    ```

    Вы должны увидеть членство в группе из управляемого домена.

4. Если вы вошли в виртуальную машину в качестве члена группы *администраторов контроллера домена AAD* , убедитесь, что вы можете правильно использовать `sudo` команду:

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>Дальнейшие действия

Если при подключении виртуальной машины к управляемому домену или при входе с помощью учетной записи домена возникли проблемы, см. раздел [Устранение неполадок при присоединении к домену](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
