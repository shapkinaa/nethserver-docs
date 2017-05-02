.. _base_system-section:

===========
Основная система
===========

Эта глава описывает все установленные после инсталляции модули.
Все модули, не описанные в этой главе, должны быть установлены с помощью :ref:`package_manager-section`, включая модули
резервного копирования и поддержки пользователей.

.. _dashboard-section:

Dashboard
=========

Страница :index:`Dashboard` показывается первой после входа пользователя.
На этой странице отображаются :index:`статус` и настройки системы.

.. _duc-section:

Анализатор диска
-------------

Данная утилита используется для визуализации :index:`использования диска` в простом и красивом графическом виде, с помощью которого
вы можете, кликнув мышкой, просмотреть структуру директорий.

После инсталляции на страницах :guilabel:`Dashboard` и :guilabel:`Использование диска` следует нажать на :guilabel:`Update`
для индексирования корневой директории и построения графиков. Этот процесс может занять несколько минут, длительность зависит
от объема занятого пространства на диске.

Хорошо известные папки:

* Папки общего доступа: :file:`/var/lib/nethserver/ibay`
* Домашние директории пользователей: :file:`/var/lib/nethserver/home`
* Перемещаемые профили Windows: :file:`/var/lib/nethserver/profile`
* Электронная почта: :file:`/var/lib/nethserver/vmail`
* Факсы: :file:`/var/lib/nethserver/fax`
* Базы данных MySQL: :file:`/var/lib/mysql`


.. index::
   единичные: сеть
   парные: сетевой интерфейс; роль

.. _network-section:

Сеть
=======

Страница :guilabel:`Сеть` позволяет настроить подключение сервера к локальной сети (LAN) и к другим сетям (например - Интернет).

Если на серверу назначены функционал межсетевого экрана и сетевого шлюза, то он будет иметь 
дополнительные сети со специальным функционалом, таким как демилиторизованные зоны и гостевые сети.

|product| поддерживает не ограниченное количество сетевых интерфейсов.
Любая сеть управляемая системой должна соответствовать следующим правилам:

* сети должны быть физически разделены (множество сетей не может быть подключено к одному коммутатору/концентратору)
* сети должны быть логически разделены: каждая сеть дожна иметь собственное адресное пространство
* адресация частных сетей, таких как LAN, должна следовать документу RFC1918. Подробнее см.:ref:`RFC1918-section`

.. index:: зоны, роль

Каждый сетевой интерфейс имеет специальную *роль*, которая означает его поведение. Каждая роль идентифицируется цветом и соответствует заранее заданной *зоне* со специальными правилами прохождения сетевого трафика:

* *green*: локальная сеть. Серверы этой сети могут получить доступ к серверам любой другой имеющейся сети
* *blue*: гостевая сеть. Серверы этой сети могут получить доступ к серверам сетей *orange* и *red*, но не могут - к *green*
* *orange*: сеть DMZ. Серверы этой сети могут получить доступ к серверам сети *red*, но не могут - к *blue*, *orange* и *green*
* *red*: публичная сеть. Серверы этой сети могут получить доступ только к серверам своей сети

Для более подробной информации о ролях и правилах межсетевого экрана, см. :ref:`policy-section`.

.. note:: Сервер должен иметь, как минимум один сетевой интерфейс. Когда у сервера установлен только один интерфейс, то ему будет назначена ролько *green*.

Если сервер установлен на VPS (Virtual Private Server), то его интерфейс должен быть настроен в *green* сети.
Все критичные сервисы должны быть закрыты с помощью панели :ref:`network_services-section`.

.. _alias_IP-section:

Псевдонимы IP
--------

Используя псевдонимы IP адреса можно назначить более одного IP на одну физическую сетевую карту.

Наиболее частое использование, совместно с *красным* интерфейсом, когда провайдер интернет услуг предоставляется публичный IP адрес (или подсеть), вы можете добавить его (или подсеть) в *красный* интерфейс и управлять ими индивидуально (например, в настройках перенаправления портов).

Раздел псевдонимов IP адреса располагается в выпадающем меню соответствующего сетевого интерфейса.

.. note:: Псевдонимы IP адресов для интерфейсов PPPoE могут не работать, это зависит от реализации сервиса, предоставляемого интернет провайдером.

.. _logical_interfaces-section:

Логические интерфейсы
------------------

На странице :guilabel:`Сеть` нажав кнопку :guilabel:`Новый интерфейс` можно создать новый логический интерфейс.
Поддерживаемые типы интерфейсов:

* :index:`bond`: объединяет два или более логических интерфейса, предоставляя балансировку нагрузки и отказоустойчивость
* :index:`bridge`: соединяет две различные сети, часто используется для соединения VPN и виртуальной машины
* :index:`VLAN` (Virtual Local Area Network, виртуальная локальная сеть) позволяет создать две или более логических разделенных сети используя один интерфейс
* :index:`PPPoE` (Point-to-Point Protocol over Ethernet): подключение к сети Интернет с использованием DSL модема

Тип **Bond** позволяет агреггировать полосу пропускания или обеспечить отказоустойчивость сетевых подключений, и может быть настроен в многонодовой конфигурации.

Режимы предоставляющие балансировку и отказоустойчивость:

* Balance Round Robin (recommended)
* Balance XOR
* 802.3ad (LACP): этот режим должен поддерживаться на уровне драйвера, а также должен быть включен IEEE 802.3ad (Dynamic link aggregation)
* Balance TLB: этот режим должен поддерживаться на уровне драйвера
* Balance ALB

Режимы предоставляющие только отказоустойчивость:

* Active backup (рекомендован)
* Broadcast policy

С помощью **bridge** можно соединить различные сегменты сети, к примеру, подсоединять виртуальные машины или подключенных пользователей, использующих VPN, к локальной сети (*green*).

When it is not possible to physically separate two different networks, you can use a tagged **VLAN**. The traffic of the two networks can
be transmitted on the same cable, but it will be handled as if it were sent and received on separate network cards.
The use of VLAN, requires properly configured switches.

.. warning:: The **PPPoE** logical interface must be assigned the red
             role, thus requires the gateway functionality. See
             :ref:`firewall-section` for details.

.. _RFC1918-section:

Address for private networks (RFC1918)
--------------------------------------

TCP/IP private networks not directly connected to Internet should use special addresses selected by
Internet Assigned Numbers Authority (IANA).

===============   ===========   =============================
Private network   Subnet mask   IP addresses interval
===============   ===========   =============================
10.0.0.0          255.0.0.0     10.0.0.1 - 10.255.255.254
172.16.0.0        255.240.0.0   172.16.0.1 - 172.31.255.254
192.168.0.0       255.255.0.0   192.168.0.1 - 192.168.255.254
===============   ===========   =============================





.. _network_services-section:

Network services
================

A :index:`network service` is a service running on the firewall itself.

These services are always available to hosts on green network (local network).
Access policies can be modified from :guilabel:`Network services` page.

Available policies are:

* Access only from green networks (private): all hosts from green networks and from VPNs
* Access from green and red networks (public): any host from green networks, VPNs and external networks. But not guests (blue) and DMZ (orange) networks
* Access only from the server itself (none): no host can connect to selected service

Custom access
-------------

If selected policy is private or public, it’s possible to add hosts and networks which are always allowed (or blocked)
using :guilabel:`Allow hosts` and :guilabel:`Deny hosts`.
This rule also apply for blue and orange networks.

Example
^^^^^^^

Given the following configuration:

* Orange network: 192.168.2.0/24
* Access for NTP server set to private

If hosts from DMZ must access NTP server, add 192.168.2.0/24 network inside the :guilabel:`Allow hosts` field.

.. index:: trusted networks

.. _trusted_networks-section:

Trusted networks
================

Trusted networks are special networks (local, VPNs or remote)
allowed to access special server's services.

For example, hosts inside trusted networks can access to:

* Server Manager
* Shared folders (SAMBA)

If the remote network is reachable using a router, remember to add a
static route inside :ref:`static_routes-section` page.



.. _static_routes-section:

Static routes
==============

This page allow to create special :index:`static routes` which will use the specified gateway.
These routes are usually used to connect private network.

Remember to add the network to :ref:`trusted_networks-section`, if you wish to allow remote hosts to access local services.


.. _organization_contacts-section:

Organization contacts
=====================

The :guilabel:`Organization contacts` page fields are used as default
values for user accounts.  The organization name and address are also
displayed on the Server Manager login screen.

.. index::
   pair: Certificate; SSL   

.. _server_certificate-section:

Server certificate
==================

The :guilabel:`Server certificate` page shows the currently installed
SSL certificate that is provided by all system services.

The :guilabel:`Generate certificate` button allows generating a new
self-signed SSL certificate.  When a new certificate is generated, all
SSL services are restarted and network clients will be required to
accept the new certificate.

.. note::
   To avoid problems while importing the certificate in Internet Explorer,
   the Common Name (CN) field should match the server FQDN. 


.. _custom_certificate-section:

Install a custom certificate
----------------------------

:index:`Custom certificates` should be placed inside the following standard directories:

* :file:`/etc/pki/tls/certs`: public key
* :file:`/etc/pki/tls/private`: private key


Set the private key and certificate file paths:

::

    db configuration setprop pki CrtFile '/path/to/cert/pem-formatted.crt'
    db configuration setprop pki KeyFile '/path/to/private/pem-formatted.key'

You can also set a SSL certificate chain file:

::

    db configuration setprop pki ChainFile '/path/to/cert/pem-formatted-chain.crt'

Notify registered daemons about certificate update:

::

    signal-event certificate-update

Custom certificate backup
-------------------------
   
Always remember to add custom certificates to configuration backup.
Just add the paths inside :file:`/etc/backup-config.d/custom.include` file.

For example, if the certificate is :file:`/etc/pki/tls/certs/mycert.crt`, simply execute: ::

 echo "/etc/pki/tls/certs/mycert.crt" >> /etc/backup-config.d/custom.include

Let's Encrypt certificate
-------------------------

Let's Encrypt is a free, automated, and open certificate authority brought to you by the non-profit Internet Security Research Group (ISRG).
It can create free valid SSL certificate for you server.

From https://letsencrypt.readthedocs.org:

  The Let’s Encrypt Client is a fully-featured, extensible client for the Let’s Encrypt CA 
  (or any other CA that speaks the ACME protocol) that can automate 
  the tasks of obtaining certificates and configuring webservers to use them.


Prerequisites
^^^^^^^^^^^^^

1. The server must be reachable from outside at port 80.

   Make sure your port 80 is open to the public Internet, you can check with sites like http://www.canyouseeme.org/

2. The fully qualified name (FQDN) of the server must be a public domain name associated to its own public IP.

   Make sure you have a public DNS name pointing to your server, you can check with sites like http://viewdns.info/

How it works
^^^^^^^^^^^^

The system will release a single certificate for server FQDN (Fully Qualified Domain Name).

When you want to access your server, you MUST always use the FQDN, but sometimes the server has multiple aliases.
Let's Encrypt can add extra valid names to the FQDN certificate, so you will be able to access the server with other names.


**Example**

The server FQDN is: ''server.nethserver.org'' with public IP ''1.2.3.4''.
But you want to access the server also using this names (aliases):'' mail.nethserver.org'' and ''www.nethserver.org''.

The server must:

* have the port 80 open to the public internet: if you access http://1.2.3.4 from a remote site you must see NethServer landing page
* have a DNS public record for ''server.nethserver.org'', ''mail.nethserver.org'' and ''www.nethserver.org''. All DNS records must point to the same server (it may have multiple public IP addresses, though)

Installation
^^^^^^^^^^^^

Install the package from command line: ::

    yum install nethserver-letsencrypt

Configuration
^^^^^^^^^^^^^

Let's Encrypt configuration must be done from command line using the root user.
Access the server with a monitor or connect to it with SSH.

Certificate for FQDN
~~~~~~~~~~~~~~~~~~~~

Enable Let's Encrypt globally, this will automatically enable the generation of a certificate for the FQDN.
Execute: ::

  config setprop pki LetsEncrypt enabled
  signal-event nethserver-letsencrypt-update

Certificate for server alias (optional)

The FQDN certificate can be extended to be valid also for extra domains configured as server alias.
This feature is called SubjectAltName (SAN): https://en.wikipedia.org/wiki/SubjectAltName

Create a server alias inside the DNS page, then enable Let's Encrypt on the newly created record.

Example for ''alias.mydomain.com'' alias: ::

    db hosts setprop alias.mydomain.com LetsEncrypt enabled


Options
~~~~~~~

You can customize the following options by using config command:

* ``LetsEncryptMail``: if set, Let's Encrypt will send notification about your certificate to this mail address (this must be set before executing the letsencrypt-certs script for the first time!)
* ``LetsEncryptRenewDays``: minimum days before expiration to automatically renew certificate (default: 30)

Example: ::

  config setprop pki LetsEncryptMail admin@mydomain.com
  signal-event nethserver-letsencrypt-update

Test certificate creation
^^^^^^^^^^^^^^^^^^^^^^^^^

Since you can request the certificate maximum 5 times per week, make sure the configuration is correct by requesting a fake certificate. 
Execute: ::

  /usr/libexec/nethserver/letsencrypt-certs -v -t

This command will try to generate a fake certificate using Let's Encrypt server. If everything goes well, the output should be something like this: ::

  INFO: Using main config file /tmp/3XhzEPg7Dt
  + Generating account key...
  + Registering account key with letsencrypt...
  Processing test1.neth.eu
  + Signing domains...
  + Creating new directory /etc/letsencrypt.sh/certs/test1.neth.eu ...
  + Generating private key...
  + Generating signing request...
  + Requesting challenge for test1.neth.eu...
  + Responding to challenge for test1.neth.eu...
  + Challenge is valid!
  + Requesting certificate...
  + Checking certificate...
  + Done!
  + Creating fullchain.pem...
  + Done!

Verify the presented certificate has been signed by Let's Encrypt CA on all SSL-enabled services like:
If something goes wrong, please make sure all prerequisites are met.

Obtaining a valid certificate
-----------------------------

If your configuration has been validated by the testing step, you're ready to request a new valid certificate.
Execute the following script against the real Let's Encrypt server: ::

   /usr/libexec/nethserver/letsencrypt-certs -v


Access your http server and check your certificate is valid.


.. _user_profile-section:

Change user password
====================

All users can login to Server Manager using their own credentials and access the :index:`user profile`.

After login, a user can :index:`change the password` and information about the account, like:

* Name and surname
* External mail address

The user can also overwrite fields set by the administrator:

* Company
* Office
* Address
* City

Shutdown
========

The machine where |product| is installed can be rebooted or halted from the :menuselection:`Shutdown` page.
Choose an option (reboot or halt) then click on submit button.

Always use this module to avoid bad shutdown which can cause data damages.

Log viewer
==========

All services will save operations inside files called :dfn:`logs`.
The :index:`log` analysis is the main tool to find and resolve problems.
To analyze log files click in :menuselection:`Log viewer`.

This module allows to:

* start search on all server's logs
* display a single log
* follow the content of a log in real time

Date and time
=============

After installation, make sure the server is configured with the correct timezone.
The machine clock can be configured manually or automatically using public NTP servers (preferred).

The machine clock is very important in many protocols. To avoid problems, all hosts in LAN can be configured to use the server as NTP server.


Inline help
===========

All packages inside the Server Manager contain an :index:`inline help`.
The inline help explains how the module works and all available options.

These help pages are available in all Server Manager's languages.

A list of all available inline help pages can be found at the address: ::

 https://<server>:980/<language>/Help

**Example**

If the server has address ``192.168.1.2``, and you want to see all English help pages, use this address: ::

 https://192.168.1.2:980/en/Help


