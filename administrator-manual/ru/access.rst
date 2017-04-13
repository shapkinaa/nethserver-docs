.. _access-section:

.. index::
   single: Server Manager
   single: web interface

============================
Доступ к Server Manager
============================

|product| можно настроить с помощью веб интерфейса :dfn:`Server Manager`. 
Для этого потребуется веб браузер, такие как Mozilla Firefox или Google Chrome. Доступ к веб интерфейсу осуществляется с помощью
адресной строки (URL) ``https://a.b.c.d:980`` или ``https://server_name:980``, где *a.b.c.d* и *server_name* IP адрес и имя сервера, выданные ему при установке.

Если установлен модуль web server, то доступ к веб интерфейсу можно получить с помощью адреса ``https://server_name/server-manager``.

The Server Manager uses self-signed SSL certificates.
You should explicitly accept them the first time you access the server.
The connection is safe and encrypted.

Login
=====

The login page will gave you a trusted access to the web interface.
Use following credentials:

* :index:`Default user` name: **root**
* :index:`Default password`: **Nethesis,1234**

.. warning:: Change the root's password as soon as possible, by
             picking a secure one, composed of a random sequence of
             mixed-case letters, digits and symbols.
  
If the File server, Email server or any other module requiring Users
and groups module is installed from the Software center, the ``admin``
user is also available to access the web interface with same
privileges as the ``root`` user. See :ref:`admin_user-section` for
details.
