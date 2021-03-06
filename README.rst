Webdavclient
============

|PyPI version| |Requirements Status| |PullReview stats|

Пакет Webdavclient обеспечивает легкую и удобную работу с
WebDAV-серверами (Яндекс.Диск, Dropbox, Google Диск, Box, 4shared и
т.д.). В данный пакет включены следующие компоненты: webdav API,
resource API и wdc.

Исходный код проекта можно посмотреть
`здесь <https://github.com/designerror/webdavclient>`_ |Github|

Установка и обновление
======================

**Установка**

    Linux

.. code:: bash

    $ sudo apt-get install libxml2-dev libxslt-dev python-dev
    $ sudo apt-get install libcurl4-openssl-dev python-pycurl 
    $ sudo easy_install webdavclient

    OS X

.. code:: bash

    curl https://bootstrap.pypa.io/ez_setup.py -o - | python
    python setup.py install --prefix=/opt/setuptools
    sudo easy_install webdavclient

**Обновление**

.. code:: bash

    $ sudo pip install -U webdavclient

Webdav API
==========

Webdav API - представляет из себя набор webdav-методов работы с
облачными хранилищами. В этот набор входят следующие методы: check,
free, info, list, mkdir, clean, copy, move, download, upload, publish и
unpublish.

**Настройка клиента**

Обязательными ключами для настройки соединения клиента с WevDAV-сервером
являются webdav\_hostname и webdav\_login, webdav\_password.

.. code:: python

    import webdav.client as wc
    options = {
        'webdav_hostname': "https://webdav.server.ru",
        'webdav_login': "login",
        'webdav_password': "password"
    }
    client = wc.Client(options)

При наличие прокси-сервера необходимо указать настройки для подключения
через него.

.. code:: python

    import webdav.client as wc
    options = {
        'webdav_hostname': "https://webdav.server.ru",
        'webdav_login': "w_login",
        'webdav_password': "w_password",
        'proxy_hostname': "http://127.0.0.1:8080",
        'proxy_login': "p_login",
        'proxy_password': "p_password"
    }
    client = wc.Client(options)

При необходимости использования сертификата, путь к сертификату и
приватному ключу задается следующим образом:

.. code:: python

    import webdav.client as wc
    options = {
        'webdav_hostname': "https://webdav.server.ru",
        'webdav_login': "w_login",
        'webdav_password': "w_password",
        'cert_path': "/etc/ssl/certs/certificate.crt",
        'key_path': "/etc/ssl/private/certificate.key"
    }
    client = wc.Client(options)

**Синхронные методы**

.. code:: python

    //Проверка существования ресурса

    client.check("dir1/file1")
    client.check("dir1")

.. code:: python

    //Получение информации о ресурсе

    client.info("dir1/file1")
    client.info("dir1/")

.. code:: python

    //Проверка свободного места

    free_size = client.free()

.. code:: python

    //Получение списка ресурсов

    files1 = client.list()
    files2 = client.list("dir1")

.. code:: python

    //Создание директории

    client.mkdir("dir1/dir2")

.. code:: python

    //Удаление ресурса

    client.clean("dir1/dir2")

.. code:: python

    //Копирование ресурса

    client.copy(remote_path_from="dir1/file1", remote_path_to="dir2/file1")
    client.copy(remote_path_from="dir2", remote_path_to="dir3")

.. code:: python

    //Перемещения ресурса

    client.move(remote_path_from="dir1/file1", remote_path_to="dir2/file1")
    client.move(remote_path_from="dir2", remote_path_to="dir3")

.. code:: python

    //Перемещения ресурса

    client.download_sync(remote_path="dir1/file1", local_path="~/Downloads/file1")
    client.download_sync(remote_path="dir1/dir2/", local_path="~/Downloads/dir2/")

.. code:: python

    //Выгрузка ресурса

    client.upload_sync(remote_path="dir1/file1", local_path="~/Documents/file1")
    client.upload_sync(remote_path="dir1/dir2/", local_path="~/Documents/dir2/")

.. code:: python

    //Публикация ресурса

    link = client.publish("dir1/file1")
    link = client.publish("dir2")

.. code:: python

    //Отмена публикации ресурса

    client.unpublish("dir1/file1")
    client.unpublish("dir2")

.. code:: python

    //Обработка исключений

    from webdav.client import WebDavException
    try:
        ...
    except WebDavException as exception:
        ...

.. code:: python

    //Получение недостающих файлов

    client.pull(remote_directory='dir1', local_directory='~/Documents/dir1')

.. code:: python

    //Отправка недостающих файлов

    client.push(remote_directory='dir1', local_directory='~/Documents/dir1')

**Асинхронные методы**

.. code:: python

    //Загрузка ресурса

    kwargs = {
        'remote_path': "dir1/file1",
        'local_path': "~/Downloads/file1",
        'callback': callback
    }
    client.download_async(**kwargs)

    kwargs = {
        'remote_path': "dir1/dir2/",
        'local_path': "~/Downloads/dir2/",
        'callback': callback
    }
    client.download_async(**kwargs)

.. code:: python

    //Выгрузка ресурса

    kwargs = {
        'remote_path': "dir1/file1",
        'local_path': "~/Downloads/file1",
        'callback': callback
    }
    client.upload_async(**kwargs)

    kwargs = {
        'remote_path': "dir1/dir2/",
        'local_path': "~/Downloads/dir2/",
        'callback': callback
    }
    client.upload_async(**kwargs)

Resource API
============

Resource API - используя концепцию ООП, обеспечивает работу с облачными
хранилищами на уровне ресурсов.

.. code:: python

    //Получение ресурса

    res1 = client.resource("dir1/file1")

.. code:: python

    //Работа с ресурсом

    res1.rename("file2")
    res1.move("dir1/file2")
    res1.copy("dir2/file1")
    info = res1.info()
    res1.read_from(buffer)
    res1.read(local_path="~/Documents/file1")
    res1.read_async(local_path="~/Documents/file1", callback)
    res1.write_to(buffer)
    res1.write(local_path="~/Downloads/file1")
    res1.write_async(local_path="~/Downloads/file1", callback)

wdc
===

wdc - кросплатформенная утилита, обеспечивающая удобную работу с
WebDAV-серверами прямо из вашей консоли. Помимо полной реализации
методов из webdav API, также добавлены методы синхронизации содержимого
локальной и удаленной директорий.

**Аутентификация**

-  *Basic-аутентификация*

.. code:: bash

    $ wdc login https://wedbav.server.ru -p http://127.0.0.1:8080
    webdav_login: w_login
    webdav_password: w_password
    proxy_login: p_login
    proxy_password: p_password
    success

-  *Авторизация приложения с помощью OAuth-токена*

.. code:: bash

    $ wdc login https://wedbav.server.ru -p http://127.0.0.1:8080 --token xxxxxxxxxxxxxxxxxx
    proxy_login: p_login
    proxy_password: p_password
    success

Также имеются дополнительные ключи ``--root[-r]``, ``--cert-path[-c]`` и
``--key-path[-k]``.

**Пример работы с утилитой**

.. code:: bash

    $ wdc check
    success
    $ wdc check file1
    not success
    $ wdc free
    245234120344
    $ wdc ls dir1
    file1
    ...
    fileN
    $ wdc mkdir dir2
    $ wdc copy dir1/file1 -t dir2/file1
    $ wdc move dir2/file1 -t dir2/file2
    $ wdc download dir1/file1 -t ~/Downloads/file1
    $ wdc download dir1/ -t ~/Downloads/dir1/
    $ wdc upload dir2/file2 -f ~/Documents/file1
    $ wdc upload dir2/ -f ~/Documents/
    $ wdc publish di2/file2
    https://yadi.sk/i/vWtTUcBucAc6k
    $ wdc unpublish dir2/file2
    $ wdc pull dir1/ -t ~/Documents/dir1/
    $ wdc push dir1/ -f ~/Documents/dir1/
    $ wdc info dir1/file1
    {'name': 'file1', 'modified': 'Thu, 23 Oct 2014 16:16:37 GMT',
    'size': '3460064', 'created': '2014-10-23T16:16:37Z'}

WebDAV-сервера
==============

К наиболее популярным облачным хранилищам, поддерживающих протокол
WevDAV можно отнести Яндекс.Диск, Dropbox, Google Диск, Box и 4shared.
Доступ к данным хранилищам, осуществлется при наличии доступа к сети
Интернет. В случае необходимости локального расположения облачного
хранилища, можно развернуть собственный WebDAV-сервер.

**Локальный WebDAV-сервер**

Развернуть локальный WebDAV-сервер, с использование Docker-контейнеров
довольно легко и быстро. Ознакомиться с примером развертывания
локального WebDAV-сервера можно на проекте
`webdav-server-docker <https://github.com/designerror/webdav-server-docker>`_.

**Поддерживаемые методы**

+----------------+--------+--------+--------+---------+---------+--------+--------+------------+----------+
| Сервиры        | free   | info   | list   | mkdir   | clean   | copy   | move   | download   | upload   |
+================+========+========+========+=========+=========+========+========+============+==========+
| Яндекс.Диск    |   \+   |   \+   |   \+   |   \+    |   \+    |   \+   |   \+   |   \+       |   \+     |
+----------------+--------+--------+--------+---------+---------+--------+--------+------------+----------+
| Dropbox        |   \-   |   \+   |   \+   |   \+    |   \+    |   \+   |   \+   |   \+       |   \+     |
+----------------+--------+--------+--------+---------+---------+--------+--------+------------+----------+
| Google Диск    |   \-   |   \+   |   \+   |   \+    |   \+    |   \-   |   \-   |   \+       |   \+     |
+----------------+--------+--------+--------+---------+---------+--------+--------+------------+----------+
| Box            |   \+   |   \+   |   \+   |   \+    |   \+    |   \+   |   \+   |   \+       |   \+     |
+----------------+--------+--------+--------+---------+---------+--------+--------+------------+----------+
| 4shared        |   \-   |   \+   |   \+   |   \+    |   \+    |   \-   |   \-   |   \+       |   \+     |
+----------------+--------+--------+--------+---------+---------+--------+--------+------------+----------+
| Webdavserver   |   \-   |   \+   |   \+   |   \+    |   \+    |   \-   |   \-   |   \+       |   \+     |
+----------------+--------+--------+--------+---------+---------+--------+--------+------------+----------+

Методы publish и unpublish поддерживает только Яндекс.Диск.

**Настройка подключения**

Для работы с облачными хранилащами Dropbox и Google Диск по протоколу
WebDAV необходимо использовать WebDAV-сервера DropDAV и DAV-pocket
соответственно.

Список настроек для WebDAV - серверов:

.. code::

    webdav-servers:
      - yandex
        hostname: https://webdav.yandex.ru
        login:    #login_for_yandex
        password: #pass_for_yandex
      - dropbox
        hostname: https://dav.dropdav.com
        login:    #login_for dropdav
        password: #pass_for_dropdav
      - google
        hostname: https://dav-pocket.appspot.com
        root:     docso
        login:    #login_for_dav-pocket
        password: #pass_for_dav-pocket
      - box
        hostname: https://dav.box.com
        root:     dav
        login:    #login_for_box
        password: #pass_for_box
      - 4shared
        hostname: https://webdav.4shared.com
        login:    #login_for_4shared
        password: #pass_for_4shared

Автодополнение
==============

Для OS X или старых Unix-систем необходимо обновить bash.

.. code:: bash

    brew install bash
    chsh
    brew install bash-completion

Автодополнение можно включить глобально

.. code:: bash

    sudo activate-global-python-argcomplete

или локально

.. code:: bash

    #.bashrc
    eval "$(register-python-argcomplete wdc)"

Благодарности
=============

Спасибо компании\ ``JetBrains`` за

|PyCharm|

.. |PyPI version| image:: https://badge.fury.io/py/webdavclient.svg
   :target: http://badge.fury.io/py/webdavclient
.. |Requirements Status| image:: https://requires.io/github/designerror/webdav-client-python/requirements.svg?branch=master&style=flat
   :target: https://requires.io/github/designerror/webdav-client-python/requirements/?branch=master&style=flat
.. |PullReview stats| image:: https://www.pullreview.com/github/designerror/webdavclient/badges/master.svg?
   :target: https://www.pullreview.com/github/designerror/webdavclient/reviews/master
.. |Github| image:: https://github.com/favicon.ico
.. |PyCharm| image:: http://s28.postimg.org/o1td9nf3t/rsz_intellij_pycharm.png
   :target: https://www.jetbrains.com/pycharm/
