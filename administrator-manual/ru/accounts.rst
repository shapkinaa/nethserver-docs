.. _users_and_groups-section:

================
Пользователи и группы
================

Пользователи
=====

Системный пользователь должен иметь доступ к большинству сервисов, предоставляемых 
|product| (email, shared folders, etc..).

Каждый пользователь характеризуется парой - имя пользователя и пароль. Вновь создаваемая 
учетная запись пользователя остается заблокированой, до момент установки пароля. Заблокированный
пользователь не может использовать сервисы серверы требующие аутентификации.

При создании пользователя следующие поля учетной записи обязательны к заполнению:

* Username (имя пользователя)
* Name (Имя)
* Surname (Фамилия)

Не обязательные поля:

* Company (Компания)
* Office (Комната)
* Address (Улица)
* City (Город)
* Phone (Телефон)


После создания, учетная запись пользователя заблокирована. Для ее разблокировки необходимо установить пароль с помощью кнопки
:guilabel:`Change password`.
Для смены собственного пароля, пользователь может воспользоваться разделом :ref:`user_profile-section` веб интерфейса Server Manager.
Смену пароля может осуществить только разблокированный пользователь.

Для добавления пользователя в одну или несколько группу необходимо воспользоваться разделом :guilabel:`Users` или :guilabel:`Groups`.

Иногда требуется заблокировать доступ пользователя к сервисам сервера без удаления учетной записи.
Для этого служат кнопки :guilabel:`Lock` и :guilabel:`Unlock`.


.. note:: При удалении пользователя, все данные пользователя будут также удалены.

.. _users_services-section:

Доступ к сервисам
------------------

После создания пользователя, можно указать, к каким сервис он будет иметь доступ.
Это производиться на странице :guilabel:`Services`.

.. _groups-section:

Группы
======

Пользовательские группы могут использоваться для присвоения специальных прав доступа пользователям, а также для создания листов рассылки (с помощью электронной почты).

Как и для пользователей, группам возможно разрешить использование как нескольких, так и всех сервисов.

.. tip:: Для назначения прав доступа в Server Manager, необходимо воспользоваться группами ``managers`` или ``administrators``.

Для организации доступа к панелям веб интерфейса Server Manager следует создать две специальные группы - ``managers`` или ``administrators``.

* :dfn:`administrators`: Пользователям этой группы будут предоставлены такие же права как и пользователя root или пользователю admin.
* :dfn:`managers`: Пользователям, входящим в эту группу предоставляется доступ к функциям управления.


.. _admin_user-section:

Учетная запись администратора
=============

Страница :guilabel:`Users` по умолчанию имеет одну запись: :dfn:`admin`. Эта 
учетная запись имеет доступ к веб интерфейсу Server Manager с такими же правами, что и пользователь
:dfn:`root`. Первоначально учетная запись :dfn:`admin` заблокирована и не имеет доступа к консоли.

.. tip:: Для разблокировки ``admin`` необходимо ей установить пароль.

Эта учетная запись ``admin`` используется для предоставления прав в специфичных сервисах,
таких как :ref:`Подключение рабочей станции к домену Samba <samba_pdc>`

Управление паролями
===================

На пароль в система накладываются следующие ограничения - сложность (:dfn:`complexity`) и время жизни (:dfn:`expiration`).

Политику, накладываемую на пароли, можно изменить в веб интерфейсе модуля ``nethserver-password``, сразу после установки.

Сложность
-----------

Сложность :index:`password` - это минимальный набор условий, удовлетворяя которым он будет принят системой: 
Имеется возможность выбора сложности пароли между двумя различными политиками:

* :dfn:`none`: пароль должен состоять минимум из 7 символов, остальные условия не предъявляются
* :dfn:`strong`

Политика :index:`strong` состоит из следующего набора правил:

* Минимальная длина пароля - 7 символов
* Пароль должен содержать как минимум 1 цифру
* Пароль должен содержать как минимум 1 символ в нижнем регистре
* Пароль должен содержать как минимум 1 символ в верхнем регистре
* Пароль должен содержать как минимум 1 специальный символ
* Пароль должен содержать как минимум 5 различных символ
* Пароль должен отсутствовать в словаре частоупотребляемых слов
* Пароль должен отличаться от имени пользователя
* Пароль не может повторять рисунка для трёх и более символов (на пример, пароль - As1.$ AS1. $ системой принят не будет)

Политика по умолчанию - :dfn:`strong`.

.. warning:: Изменение политики по умолчанию не крайне рекомендовано. Использование простого пароля часто ведет к уязвимости сервера для внешних атак.

Установка политики none ::

  config setprop passwordstrength Users none

Установка политики strong ::

  config setprop passwordstrength Users strong

Проверка текущей установленной политики на сервере ::

  config getprop passwordstrength Users

Время жизни пароля
----------

The  :index:`password expiration` is enabled by default to 6 months from the time when the password is set.
The system will send an e-mail to inform the users when their password is about to expire.

.. note:: The system will refer to the date of the last password change, 
   whichever is the earlier more than 6 months, the server will send an email to indicate that password has expired. 
   In this case you need to change the user password.
   For example, if the last password change was made in January, and the activation of the deadline in October, 
   the system will assume the password changed in January is expired, and notify the user.

If you wish to bypass the password expiration globally (also allow access for users with expired password) ::

  config setprop passwordstrength PassExpires no
  signal-event password-policy-update

To disable password expiration for a single user (replace username with the user) ::

  db accounts setprop <username> PassExpires no
  signal event password-policy-update


Below are the commands to view enabled policies.

Maximum number of days for which you can keep the same password (default: 180) ::

  config getprop passwordstrength MaxPassAge


Minimum number of days for which you are forced to keep the same password (default 0) ::

  config getprop passwordstrength MinPassAge


Number of days on which the warning is sent by email (default: 7) ::

  config getprop passwordstrength PassWarning


To change the parameters replace the :command:`getprop` command with :command:`setprop`,  
then add the desired value at end of the line. Finally apply new configurations::

  signal-event password-policy-update



For example, to change to 5 "Number of days on which the warning is sent by email" ::

 config setprop passwordstrength PassWarning 5
 signal-event password-policy-update



Effects of expired password
^^^^^^^^^^^^^^^^^^^^^^^^^^^

After password expiration, the user will be able to read and send mails but can no longer access the shared folders and printers (Samba) or other computer if the machine is part of the domain. 


Domain password
----------------

If the system is configured as a domain controller, users can change their password using the Windows tools.

In the latter case you can not set passwords shorter than 6 *characters* regardless of the server policies.
Windows performs preliminary checks and sends the password to the server where they are then evaluated 
with enabled policies.

Notification language
=====================

Default language for notifications is English.
If you wish to change it, use the following command: ::

  config setprop sysconfig DefaultLanguage <lang>

Example for Italian: ::

  config setprop sysconfig DefaultLanguage it_IT.utf8

Import users
============

The system can import a list of users from a CSV file.
The file must contain a line per user, each line must have TAB-separated fields and must respect following format: ::

 username    firstName    lastName    email    password

Example: ::

  mario   Mario   Rossi   mario@example.org       112233


Make sure the mail server is installed, then execute: ::

  /usr/share/doc/nethserver-directory-<ver>/import_users <youfilename>

For example, if the user's file is :file:`/root/users.csv`, execute following command: ::

  /usr/share/doc/nethserver-directory-`rpm --query --qf "%{VERSION}" nethserver-directory`/import_users /root/users.csv


The command can be executed multiple times: already existing users will be skipped. 

.. note:: The command will fail if mail server module is not installed

