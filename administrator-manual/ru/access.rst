.. _access-section:

.. index::
   single: Server Manager
   single: web interface

============================
Доступ к Server Manager
============================

|product| может быть настроен с помощью :dfn:`Server Manager`. 
Для доступа к нему потребуется веб-браузер, к примеру Mozilla Firefox или Google Chrome. Веб интерфейс доступен по адресу (URL) 
``https://a.b.c.d:980`` или ``https://server_name:980``, где *a.b.c.d* и *server_name* соответственно и настраиваются в процессе установки системы.

If the web server module is installed, you can also access the web interface using this address ``https://server_name/server-manager``.

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
