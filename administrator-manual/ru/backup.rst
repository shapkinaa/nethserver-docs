======
Резервное копирование
======

:index:`Резервное копирование` - один из возможных вариантов восстановления данных компьютера после катастрофы.
Система предлагает два вида резервного копирования:

* :index:`конфигурации сервера`
* :index:`данных сервера`

Резервное копирование конфигурации сервера включает конфигурационные файлы Системы. 
Оно запускается каждую ночь, архив (:file:`/var/lib/nethserver/backup/backup-config.tar.xz`) будет создан только в случае если за последние 24 часа был изменен какой-либо файл.
В процессе резервного копирования конфигурации сервера также сохраняется список установленных модулей. Все модули могут быть переустановлены в процессе восстановления конфигурации из резервной копии.
Целью этого типа резервного копирования является быстрое восстановление системы после сбоя. Функциональность сервисов может быть восстановлена при работающем сервере.

Для резервного копирование данных требуется установленный и разрешенный к использованию модуль "backup", в котором должен быть настроено копирование всех пользовательских данных, таких как домашние директории и электронная почта. Резервное копирование данных запускается каждую ночь и может быть полным или инкриментальным, на основе еженедельных полных копий.
В архив с еженедельной резервной копией включается резервная копия конфигурации сервера.

Резервное копирование данных может быть сохранено на три разных носителя:

* USB: диск подключенный к локальному USB порту (подробнее см.: :ref:`backup_usb_disk-section`)
* CIFS: общая папка Windows, которая доступна на любом NAS (Network Attached Storage)
* NFS: общая папка Linux, которая доступна на любом NAS, как правило это быстрее чем CIFS

Результат процедуры резервного копирования может быть отправлен на электронную почту системному администратору, либо на любой другой внешний адрес.

.. note:: Определение целевой директории для резервного копирования происходит на основе имени сервера, на котором она располагается. К примеру, при смене FQDN сервера, администратор должен аккуратно перенести резервные копии на новый сервер.

Восстановление данных
============

Удостовериться, что дректория назначения доступна для записи (к примеру, USB диск должен быть подключен).

Командная строка
------------

Вывод списка файлов
^^^^^^^^^^^^^

Для вывода списка всех файлов, находщихся в последней резервной копии необходимо ввеста следующую команду: ::

 backup-data-list

В зависимости от размера резервной копии, выполнение команды займет какое-то время.

Файлы и директории
^^^^^^^^^^^^^^^^^^

Файлы, находящиеся в директории :file:`/var/lib/nethserver/`, размещаются в соответствующих поддиректориях:

* Почта: :file:`/var/lib/nethserver/vmail/<user>`
* Общие папки: :file:`/var/lib/nethserver/ibay/<name>`
* Домашние директории: :file:`/var/lib/nethserver/home/<user>`

Для восставновления файла/директории используется команда: ::

  restore-file <position> <file>

Например, восстановление почты учетной записи *test* в директорию :file:`/tmp`: ::

  restore-file /tmp /var/lib/nethserver/vmail/test

Например, восстановление почты учетной записи *test* в оригинальную директорию: ::

  restore-file / /var/lib/nethserver/vmail/test


Система позволяет восстановить предыдущую версию директории (или файла).

Например, восстановление 15-ти дневной версии файла: ::

  restore-file -t 15D /tmp "/var/lib/nethserver/ibay/test/myfile" 

Флаг ``-t`` позволяет указать количество дней (15 в этом примере).

Графический интерфейс
-----------------

В секции меню :menuselection:`Восстановление данных` возможен поиск, выбор и восстановление
одной или нескольких директорий. Навигация осуществляется с помощью графического дерева каталогов, содержащихся в резервной копии.

Два возможных пути восстановления:

* Восстановление данных происходит в оригинальное место, текущие файлы в файловой системе будут переписаны восстанавливаемыми из резервной копии.
* Восстановление данных происходит в оригинальное место, но восстанавливаемые файлы будут помещены в новую директорию (файлы переписаны не будут) этого пути: ::

  /complete/path/of/file_YYYY-MM-DD (YYYY-MM-DD, где дата восстановления данных)

Для поиска следует ввести не менее 3 символов в поле поиска и подпадающие под условие директории будут подсвечены автоматически.

Восставноление выбранных данных осуществляется с помощью кнопки **Restore**.

.. note:: Множественный выбор восстанавливаемых данных можно сделать с помощью клавиши Ctrl.


Восстановление после сбоя
=================

Восстановление системы осуществляется в два этапа: первый - восстановление конфигурации, второй - данных. 
Сразу после восстановления конфигурации система готова к использованию, если установлены все необходимые пакеты. 
Вы можете установить дополнительные пакеты до восстановления или после.
К примеру, если почтовый сервер установлен, то система может отправлять и получать письма.

Другие восстанавливаемые данные конфигурации:

* пользователи и группы
* SSL сертификаты

.. note:: Пароль пользователя root/admin не восстанавливается.

Шаги для выполнения восстановления:

1. Установите новый сервер с тем же именем, что и старый
2. Настройте резервное копирование, чтобы система могла видеть сохраненные резервные копии
3. Если старый сервер выступал в роли сетевого шлюза, то не забудьте установить модуль межсетевого экрана
4. Восстановите резервную копию конфигурации с помощью страницы :guilabel:`Резервное копирование
   (конфигурация) > Восстановление` в веб интерфейсе Server Manager, или запустите команду из консоли:
   :command:`restore-config`
5. Перенастройте сетевые роли, если этого требует предупреждающее сообщение. См. ниже :ref:`restore-roles-section`.
6. Проверьте функционал системы
7. Восстановите данные, запустив команду: :command:`restore-data`


.. _restore-roles-section:
   
Восстановление сетевых ролей
---------------------

В случае если в конфигурации описан отсутствующий сетевой интерфейс, на страницах
:guilabel:`Dashboard`, :guilabel:`Резервное копирование (конфигурация) > Восстановление`
и :guilabel:`Сеть` будут показаны предупреждающие сообщения. Это происходит в следующих случаях:

* резервная копия конфигурации восстановлена на новое аппаратное обеспечение
* одна или несколько сетевых карт заменены
* системный диск установлен на новый сервер

The warning points to a page that lists the network cards present in
the system, highlighting those not having an assigned :ref:`role
<network-section>`. Such cards have a drop down menu where to select a
role available for restoring.

For instance, if a card with the *orange* role has been replaced, the
drop down menu will list an element ``orange``, near the new
network card.

The same applies if the old card was a component of a logical
interface, such as a bridge or bond.

By picking an element from the drop down menu, the old role is
transferred to the new physical interface.

Click the :guilabel:`Submit` button to apply the changes.

.. warning:: Choose carefully the new interfaces assignment: doing a mistake
             here could lead to a system isolated from the network!

If the missing role is ``green`` an interactive procedure asks to fix
the configuration at boot-time, to ensure a minimal network
connectivity and login again on the Server Manager.

.. _backup_config_rpms:

Restore installed modules
-------------------------

By default the process of configuration restore will also restore all previously installed modules.

To avoid the reinstallation, execute this command before the restore: ::

  config setprop backup-config reinstall disabled
     
.. _backup_customization-section:

Data backup customization
=========================

If additional software is installed, the administrator can edit
the list of files and directories included (or excluded).

Inclusion
---------

If you wish to add a file or directory to data backup, add a line to the file :file:`/etc/backup-data.d/custom.include`.

For example, to backup a software installed inside :file:`/opt` directory, add this line: ::

  /opt/mysoftware

Exclusion
---------

If you wish to exclude a file or directory from data backup, add a line to the file :file:`/etc/backup-data.d/custom.exclude`.

For example, to exclude all directories called *Download*, add this line: ::

  **Download**

To exclude a mail directory called *test*, add this line: ::

  /var/lib/nethserver/vmail/test/ 


Same syntax applies to configuration backup. Modification should be done inside the file :file:`/etc/backup-config.d/custom.exclude`.


.. note:: Make sure not to leave empty lines inside edited files.


Configuration backup customization
==================================

In most cases it is not necessary to change the configuration backup. 
But it can be useful, for example, if you have installed a custom SSL certificate. 
In this case you can add the file that contains the certificate to the list of files to backup.

Inclusion
---------

If you wish to add a file or directory to configuration backup, add a line to the file :file:`/etc/backup-config.d/custom.include`.

For example, to backup :file:`/etc/pki/mycert.pem` file , add this line: ::

  /etc/pki/mycert.pem

Do not add big directories or files to configuration backup.

Exclusion
---------

If you wish to exclude a file or directory from configuration backup, add a line to the file :file:`/etc/backup-config.d/custom.exclude`.

.. note:: 
   Make sure not to leave empty lines inside edited files.
   The syntax of the configuration backup supports only simple file and directory paths.

.. _backup_usb_disk-section:

USB disk configuration
======================

The best filesystem for USB backup disks is EXT3. FAT filesystem is supported but *not recommended*,
while NTFS is **not supported**.

Before formatting the disk, attach it to the server and find the device name: ::

 # dmesg | tail -20

 Apr 15 16:20:43 mynethserver kernel: usb-storage: device found at 4
 Apr 15 16:20:43 mynethserver kernel: usb-storage: waiting for device to settle before scanning
 Apr 15 16:20:48 mynethserver kernel:   Vendor: WDC WD32  Model: 00BEVT-00ZCT0     Rev:
 Apr 15 16:20:48 mynethserver kernel:   Type:   Direct-Access           ANSI SCSI revision: 02
 Apr 15 16:20:49 mynethserver kernel: SCSI device sdc: 625142448 512-byte hdwr sectors (320073 MB)
 Apr 15 16:20:49 mynethserver kernel: sdc: Write Protect is off
 Apr 15 16:20:49 mynethserver kernel: sdc: Mode Sense: 34 00 00 00
 Apr 15 16:20:49 mynethserver kernel: sdc: assuming drive cache: write through
 Apr 15 16:20:49 mynethserver kernel: SCSI device sdc: 625142448 512-byte hdwr sectors (320073 MB)
 Apr 15 16:20:49 mynethserver kernel: sdc: Write Protect is off
 Apr 15 16:20:49 mynethserver kernel: sdc: Mode Sense: 34 00 00 00
 Apr 15 16:20:49 mynethserver kernel: sdc: assuming drive cache: write through
 Apr 15 16:20:49 mynethserver kernel:  sdc: sdc1
 Apr 15 16:20:49 mynethserver kernel: sd 7:0:0:0: Attached scsi disk sdc
 Apr 15 16:20:49 mynethserver kernel: sd 7:0:0:0: Attached scsi generic sg3 type 0
 Apr 15 16:20:49 mynethserver kernel: usb-storage: device scan complete
 
Another good command could be: ::

 lsblk -io KNAME,TYPE,SIZE,MODEL

In this scenario, the disk is accessibile as *sdc* device.

* Create a Linux partition on the whole disk: ::

    echo "0," | sfdisk /dev/sdc

* Create the filesystem on *sdc1* partition with a label named *backup*: ::

    mke2fs -v -T largefile4 -j /dev/sdc1 -L backup

* Detach and reconnect the USB disk:

  You can simulate it with the following command: ::

    blockdev --rereadpt /dev/sdc

* Now the *backup* label will be displayed inside the :guilabel:`Backup (data)` page.

