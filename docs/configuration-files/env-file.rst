.. _env_file:

*********
.env file
*********

All docker-compose configuration is done inside the ``.env`` file which simply defines key-value
variables parsed to docker-compose.yml.

.. note::
   what is the `.env <https://docs.docker.com/compose/env-file/>`_ file?

.. note::
   Use your browsers search function to quickly find the desired variable name.

.. important::
   Any change of ``.env`` requires a restart of the Devilbox.



**Table of Contents**

.. contents:: :local:


Core settings
=============

DEBUG_COMPOSE_ENTRYPOINT
------------------------

This variable controls the docker-compose log verbosity during service startup.
When set to ``1`` verbose output as well as executed commands are shown.
When set to ``0`` only warnings and errors are shown.

+------------------------------+----------------+---------------+
| Name                         | Allowed values | Default value |
+==============================+================+===============+
| ``DEBUG_COMPOSE_ENTRYPOINT`` | ``0`` or ``1`` | ``1``         |
+------------------------------+----------------+---------------+


.. _env_docker_logs:

DOCKER_LOGS
-----------

This variable controls the output of logs. Logs can either go to file and will be available
under ``./log/`` inside the Devilbox git directory or they can be forwarded to Docker logs
and will then be send to stdout and stderr.

+-------------------+----------------+---------------+
| Name              | Allowed values | Default value |
+===================+================+===============+
| ``DOCKER_LOGS``   | ``1`` or ``0`` | ``0``         |
+-------------------+----------------+---------------+

When ``DOCKER_LOGS`` is set to ``1``, output will go to Docker logs, otherwise if it is set to
``0`` the log output will go to files under ``./log/``.

The ``./log/`` directory itself will contain subdirectories in the form ``<service>-<version>``
which will then hold all available log files.

.. note::
    Log directories do not exist until you start the Devilbox and will only be created for
    the service versions you have enabled in ``.env``.

The log directory structure would look something like this:

.. code-block:: bash

    host> cd path/to/devilbox
    host> tree log

    log/
    ├── nginx-stable/
    │   ├── nginx-stable/
    │   ├── defaultlocalhost-access.log
    │   ├── defaultlocalhost-error.log
    │   ├── <project-name>-access.log    # Each project has its own access log
    │   ├── <project-name>-error.log     # Each project has its own error log
    ├── mariadb-10.1/
    │   ├── error.log
    │   ├── query.log
    │   ├── slow.log
    ├── php-fpm-7.1/
    │   ├── php-fpm.access
    │   ├── php-fpm.error

When you want to read logs sent to Docker logs, you can do so via the following command:

.. code-block:: bash
    :emphasize-lines: 2

    host> cd path/to/devilbox
    host> docker-compose logs

When you want to continuously watch the log output (such as ``tail -f``), you need to append ``-f``
to the command.

.. code-block:: bash
    :emphasize-lines: 2

    host> cd path/to/devilbox
    host> docker-compose logs -f

When you only want to have logs displayed for a single service, you can also append the service
name (works with or without ``-f`` as well):

.. code-block:: bash
    :emphasize-lines: 2

    host> cd path/to/devilbox
    host> docker-compose logs php -f

.. important::
    Currently this is only implemented for PHP-FPM and HTTPD Docker container.
    MySQL will always output its logs to file and all other official Docker container
    always output to Docker logs.


DEVILBOX_PATH
-------------

This specifies a relative or absolute path to the Devilbox git directory and will be used as a
prefix for all Docker mount paths.

* Relative path: relative to the devilbox git directory (Must start with ``.``)
* Absolute path: Full path (Must start with ``/``)

The only reason you would ever want change this variable is when you are on MacOS and relocate
your project files onto an NFS volume due to performance issues.

.. warning::
   :ref:`remove_stopped_container`
     Whenever you change this value you have to stop the Devilbox and also remove the stopped
     container via
     ``docker-compose rm``.

+-------------------+----------------+---------------+
| Name              | Allowed values | Default value |
+===================+================+===============+
| ``DEVILBOX_PATH`` | valid path     | ``.``         |
+-------------------+----------------+---------------+


.. _env_local_listen_addr:

LOCAL_LISTEN_ADDR
-----------------

This variable specifies you host computers listening IP address for exposed container ports.
If you leave this variable empty, all exposed ports will be bound to all network interfaces on
your host operating system, which is also the default behaviour.
If you only want the exposed container ports to be bound to a specific IP address (such as
``127.0.0.1``), you can add this IP address here, but note, in this case you must add a trailing
colon (``:``).


+-----------------------+----------------+---------------+
| Name                  | Allowed values | Default value |
+=======================+================+===============+
| ``LOCAL_LISTEN_ADDR`` | IP address     | empty         |
+-----------------------+----------------+---------------+

**Examples:**

+------------------+-------------------------------------------------------------------------------+
| Value            | Meaning                                                                       |
+==================+===============================================================================+
| ``127.0.0.1:``   | only expose ports on your host os on ``127.0.0.1``. Note the trailing ``:``   |
+------------------+-------------------------------------------------------------------------------+
| ``192.168.0.1:`` | only expose ports on your host os on ``192.168.0.1``. Note the trailing ``:`` |
+------------------+-------------------------------------------------------------------------------+
| ``0.0.0.0:``     | listen on all host computer interfaces / IP addresses                         |
+------------------+-------------------------------------------------------------------------------+
|                  | listen on all host computer interfaces / IP addresses                         |
+------------------+-------------------------------------------------------------------------------+

.. note::
   When using ``Docker Toolbox``, you must leave this variable empty, in order to have the exposed
   ports available on the external interface of the virtual machine.


.. _env_tld_suffix:

TLD_SUFFIX
----------

This variable controls all of your projects domain suffix.

+----------------+------------------+---------------+
| Name           | Allowed values   | Default value |
+================+==================+===============+
| ``TLD_SUFFIX`` | alpha-num string | ``loc``       |
+----------------+------------------+---------------+

Your project domains are built together out of the project directory name and the ``TLD_SUFFIX``.
The formula is like this: ``http://<project-dir>.<TLD_SUFFIX>``.

You can even use official tld's and have your nameserver point to an internal LAN id, to make
this project visible to everyone in your corporate LAN.

**How does it look?**

+-------------+----------------+---------------------------+
| Project dir | ``TLD_SUFFIX`` | Project URL               |
+=============+================+===========================+
| my-test     | ``loc``        | ``http://my-test.loc``    |
+-------------+----------------+---------------------------+
| example     | ``loc``        | ``http://example.loc``    |
+-------------+----------------+---------------------------+
| www.test    | ``loc``        | ``http://www.test.loc``   |
+-------------+----------------+---------------------------+
| my-test     | ``local``      | ``http://my-test.local``  |
+-------------+----------------+---------------------------+
| example     | ``local``      | ``http://example.local``  |
+-------------+----------------+---------------------------+
| www.test    | ``local``      | ``http://www.test.local`` |
+-------------+----------------+---------------------------+
| my-test     | ``net``        | ``http://my-test.net``    |
+-------------+----------------+---------------------------+
| example     | ``com``        | ``http://example.com``    |
+-------------+----------------+---------------------------+
| www.test    | ``org``        | ``http://www.test.org``   |
+-------------+----------------+---------------------------+

.. warning::
    Do not use ``dev`` as a domain suffix (I know, it's tempting).
    It has been registered by
    `Google <https://icannwiki.org/.dev>`_ and they advertise the
    `HSTS header <https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security>`_
    which makes your browser redirect every http request to https.

    **See also:** `This blog post <https://ma.ttias.be/chrome-force-dev-domains-https-via-preloaded-hsts>`_

.. warning::
    Do not use ``localhost`` as a domain suffix.
    There is an RFC draft to make sure all localhost requests, including their sub domains
    should be redirected to the systems loopback interface.
    Docker has already released a commit preventing the use of ``localhost`` on MacOS.


    **See also:** `RFC Draft <https://tools.ietf.org/html/draft-west-let-localhost-be-localhost-06>`_
    and
    `Docker Release notes <https://docs.docker.com/docker-for-mac/release-notes/#docker-community-edition-17120-ce-mac46-2018-01-09>`_

.. _env_new_uid:

NEW_UID
-------

This setting controls one of the core concepts of the Devilbox. It overcomes the problem of
syncronizing file and directory permissions between the Docker container and your host operating
system.

You should set this value to the user id of your host operating systems user you actually work with.
How do you find out your user id?

.. code-block:: bash

    host> id -u
    1000

In most cases (on Linux and MacOS), this will be ``1000`` if you are the first and only user on
your system, however it could also be a different value.

+-----------------------+----------------+---------------+
| Name                  | Allowed values | Default value |
+=======================+================+===============+
| ``NEW_UID``           | valid uid      | ``1000``      |
+-----------------------+----------------+---------------+

The Devilbox own containers will then pick up this value during startup and change their internal
user id to the one specified. Services like PHP-FPM, Apache and Nginx will then do read and write
operation of files with this uid, so all files mounted will have permissions as your local user
and you do not have to fix permissions afterwards.

.. seealso::
   :ref:`syncronize_container_permissions`
      Read up more on the general problem of trying to have syncronized permissions between
      the host system and a running Docker container.


.. _env_new_gid:

NEW_GID
-------

This is the equivalent to user id for groups and addresses the same concept. See :ref:`env_new_uid`.

How do you find out your group id?

.. code-block:: bash

    host> id -g
    1000

In most cases (on Linux and MacOS), this will be ``1000`` if you are the first and only user on
your system, however it could also be a different value.

+-----------------------+----------------+---------------+
| Name                  | Allowed values | Default value |
+=======================+================+===============+
| ``NEW_GID``           | valid gid      | ``1000``      |
+-----------------------+----------------+---------------+

.. seealso::
   :ref:`syncronize_container_permissions`
      Read up more on the general problem of trying to have syncronized permissions between
      the host system and a running Docker container.


.. _env_timezone:

TIMEZONE
--------

This variable controls the system as well as service timezone for the Devilbox's own containers.
This is especially useful to keep PHP and database timezones in sync.

+-----------------------+----------------+-------------------+
| Name                  | Allowed values | Default value     |
+=======================+================+===================+
| ``TIMEZONE``          | valid timezone | ``Europe/Berlin`` |
+-----------------------+----------------+-------------------+

Have a look at Wikipedia to get a list of valid timezones: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

.. note::
   It is always a good practice not to assume a specific timezone anyway and store all values
   in UTC (such as time types in MySQL).


Intranet settings
=================


DNS_CHECK_TIMEOUT
-----------------

The Devilbox intranet validates if every project has a corresponding DNS record (either an official
DNS record, one that came from its own Auto-DNS or an ``/etc/hosts`` entry). By doing so it queries
the DNS record based on ``<project-dir>.<TLD_SUFFIX>``. In case it does not exist, the query itself
might take a while and the intranet page will be unresponsive during that time. In order to avoid
long waiting times, you can set the DNS query time-out in seconds after which the query should stop
and report as unsuccessful. The default is ``1`` second, wich should be fairly sane for all use-cases.

+-----------------------+----------------+-------------------+
| Name                  | Allowed values | Default value     |
+=======================+================+===================+
| ``DNS_CHECK_TIMEOUT`` | integers       | ``1``             |
+-----------------------+----------------+-------------------+


.. _env_devilbox_ui_protect:

DEVILBOX_UI_PROTECT
-------------------

By setting this variable to ``1``, the Devilbox intranet will be password protected.
This might be useful, if you share your running Devilbox instance accross a LAN, but do not want
everybody to have access to the intranet itself, just to the projects you actually provide.

+-------------------------+----------------+-------------------+
| Name                    | Allowed values | Default value     |
+=========================+================+===================+
| ``DEVILBOX_UI_PROTECT`` | ``0`` or ``1`` | ``0``             |
+-------------------------+----------------+-------------------+

.. note::
   Also pay attention to the next env var, which will control the password for the login:
   ``DEVILBOX_UI_PASSWORD``.


.. _env_devilbox_ui_password:

DEVILBOX_UI_PASSWORD
--------------------

When the devilbox intranet is password-protected via ``DEVILBOX_UI_PROTECT``, this is the actual
password by which it will be protected.

+--------------------------+----------------+-------------------+
| Name                     | Allowed values | Default value     |
+==========================+================+===================+
| ``DEVILBOX_UI_PASSWORD`` | any string     | ``password``      |
+--------------------------+----------------+-------------------+


.. _env_devilbox_ui_disable:

DEVILBOX_UI_DISABLE
-------------------

In case you want to completely disable the Devilbox intranet, such as when running it on production,
you need to set this variable to ``1``.

By disabling the intranet, the webserver will simply remove the default virtual host and redirect
all IP-based requests to the first available virtual host, which will be you first project when
ordering their names alphabetically.

+-------------------------+----------------+-------------------+
| Name                    | Allowed values | Default value     |
+=========================+================+===================+
| ``DEVILBOX_UI_DISABLE`` | ``0`` or ``1`` | ``0``             |
+-------------------------+----------------+-------------------+


Docker image versions
=====================

The following settings reflect one of the main goals of the Devilbox: being able to run any
combination of all container versions.

.. note::
   Any change for those settings requires a restart of the devilbox.


.. _env_php_server:

PHP_SERVER
----------

This variable choses your desired PHP-FPM version to be started.

+-------------------------+--------------------------------------------------------------------------------------------------------------------------+-----------------+
| Name                    | Allowed values                                                                                                           | Default value   |
+=========================+==========================================================================================================================+=================+
| ``PHP_SERVER``          | ``php-fpm-5.4`` |br| ``php-fpm-5.5`` |br| ``php-fpm-5.6`` |br| ``php-fpm-7.0`` |br| ``php-fpm-7.1`` |br| ``php-fpm-7.2`` | ``php-fpm-7.1`` |
+-------------------------+--------------------------------------------------------------------------------------------------------------------------+-----------------+

All values are already available in the ``.env`` file and just need to be commented or uncommented. If multiple values are uncommented, the last uncommented variable one takes precedences:

.. code-block:: bash
   :caption: .env
   :name: .env
   :emphasize-lines: 7

   host> grep PHP_SERVER .env

   #PHP_SERVER=php-fpm-5.4
   #PHP_SERVER=php-fpm-5.5
   #PHP_SERVER=php-fpm-5.6
   #PHP_SERVER=php-fpm-7.0
   PHP_SERVER=php-fpm-7.1
   #PHP_SERVER=php-fpm-7.2
   #PHP_SERVER=php-fpm-7.3
   #PHP_SERVER=hhvm-latest


.. _env_httpd_server:

HTTPD_SERVER
------------

This variable choses your desired web server version to be started.

+-------------------------+----------------------------------------------------------------------------------+------------------+
| Name                    | Allowed values                                                                   | Default value    |
+=========================+==================================================================================+==================+
| ``HTTPD_SERVER``        | ``apache-2.2`` |br| ``apache-2.4`` |br| ``nginx-stable`` |br| ``nginx-mainline`` | ``nginx-stable`` |
+-------------------------+----------------------------------------------------------------------------------+------------------+

All values are already available in the ``.env`` file and just need to be commented or uncommented. If multiple values are uncommented, the last uncommented variable one takes precedences:

.. code-block:: bash
   :caption: .env
   :name: .env
   :emphasize-lines: 5

   host> grep HTTPD_SERVER .env

   #HTTPD_SERVER=apache-2.2
   #HTTPD_SERVER=apache-2.4
   HTTPD_SERVER=nginx-stable
   #HTTPD_SERVER=nginx-mainline


.. _env_mysql_server:

MYSQL_SERVER
------------

This variable choses your desired MySQL server version to be started.

+-------------------------+------------------------------------------------------------------------------------------------+------------------+
| Name                    | Allowed values                                                                                 | Default value    |
+=========================+================================================================================================+==================+
| ``MYSQL_SERVER``        | ``mysql-5.5`` |br| ``mysql-5.6`` |br| ``mariadb-10.2`` |br| ``percona-5.7`` |br| and many more | ``mariadb-10.1`` |
+-------------------------+------------------------------------------------------------------------------------------------+------------------+

All values are already available in the ``.env`` file and just need to be commented or uncommented. If multiple values are uncommented, the last uncommented variable one takes precedences:

.. code-block:: bash
   :caption: .env
   :name: .env
   :emphasize-lines: 9

   host> grep MYSQL_SERVER .env

   #MYSQL_SERVER=mysql-5.5
   #MYSQL_SERVER=mysql-5.6
   #MYSQL_SERVER=mysql-5.7
   #MYSQL_SERVER=mysql-8.0
   #MYSQL_SERVER=mariadb-5.5
   #MYSQL_SERVER=mariadb-10.0
   MYSQL_SERVER=mariadb-10.1
   #MYSQL_SERVER=mariadb-10.2
   #MYSQL_SERVER=mariadb-10.3
   #MYSQL_SERVER=percona-5.5
   #MYSQL_SERVER=percona-5.6
   #MYSQL_SERVER=percona-5.7


.. _env_pgsql_server:

PGSQL_SERVER
------------

This variable choses your desired PostgreSQL server version to be started.

+-------------------------+-------------------------------------------------------------------+------------------+
| Name                    | Allowed values                                                    | Default value    |
+=========================+===================================================================+==================+
| ``PGSQL_SERVER``        | ``9.1`` |br| ``9.2`` |br| ``9.3`` |br| ``9.4`` |br| and many more | ``9.6``          |
+-------------------------+-------------------------------------------------------------------+------------------+

All values are already available in the ``.env`` file and just need to be commented or uncommented. If multiple values are uncommented, the last uncommented variable one takes precedences:

.. code-block:: bash
   :caption: .env
   :name: .env
   :emphasize-lines: 8

   host> grep PGSQL_SERVER .env

   #PGSQL_SERVER=9.1
   #PGSQL_SERVER=9.2
   #PGSQL_SERVER=9.3
   #PGSQL_SERVER=9.4
   #PGSQL_SERVER=9.5
   PGSQL_SERVER=9.6
   #PGSQL_SERVER=10.0

.. note::
   This is the official PostgreSQL server which might already have other tags available,
   check their official website for even more versions.
   https://hub.docker.com/_/postgres/


.. _env_redis_server:

REDIS_SERVER
------------

This variable choses your desired Redis server version to be started.

+-------------------------+-------------------------------------------------------------------+------------------+
| Name                    | Allowed values                                                    | Default value    |
+=========================+===================================================================+==================+
| ``REDIS_SERVER``        | ``2.8`` |br| ``3.0`` |br| ``3.2`` |br| ``4.0`` |br| and many more | ``4.0``          |
+-------------------------+-------------------------------------------------------------------+------------------+

All values are already available in the ``.env`` file and just need to be commented or uncommented. If multiple values are uncommented, the last uncommented variable one takes precedences:

.. code-block:: bash
   :caption: .env
   :name: .env
   :emphasize-lines: 6

   host> grep REDIS_SERVER .env

   #REDIS_SERVER=2.8
   #REDIS_SERVER=3.0
   #REDIS_SERVER=3.2
   REDIS_SERVER=4.0

.. note::
   This is the official Redis server which might already have other tags available,
   check their official website for even more versions.
   https://hub.docker.com/_/redis/


.. _env_memcd_server:

MEMCD_SERVER
------------

This variable choses your desired Memcached server version to be started.

+-------------------------+-------------------------------------------------------------------------------+------------------+
| Name                    | Allowed values                                                                | Default value    |
+=========================+===============================================================================+==================+
| ``MEMCD_SERVER``        | ``1.4.21`` |br| ``1.4.22`` |br| ``1.4.23`` |br| ``1.4.24`` |br| and many more | ``1.5.2``        |
+-------------------------+-------------------------------------------------------------------------------+------------------+

All values are already available in the ``.env`` file and just need to be commented or uncommented. If multiple values are uncommented, the last uncommented variable one takes precedences:

.. code-block:: bash
   :caption: .env
   :name: .env
   :emphasize-lines: 24

   host> grep MEMCD_SERVER .env

   #MEMCD_SERVER=1.4.21
   #MEMCD_SERVER=1.4.22
   #MEMCD_SERVER=1.4.23
   #MEMCD_SERVER=1.4.24
   #MEMCD_SERVER=1.4.25
   #MEMCD_SERVER=1.4.26
   #MEMCD_SERVER=1.4.27
   #MEMCD_SERVER=1.4.28
   #MEMCD_SERVER=1.4.29
   #MEMCD_SERVER=1.4.30
   #MEMCD_SERVER=1.4.31
   #MEMCD_SERVER=1.4.32
   #MEMCD_SERVER=1.4.33
   #MEMCD_SERVER=1.4.34
   #MEMCD_SERVER=1.4.35
   #MEMCD_SERVER=1.4.36
   #MEMCD_SERVER=1.4.37
   #MEMCD_SERVER=1.4.38
   #MEMCD_SERVER=1.4.39
   #MEMCD_SERVER=1.5.0
   #MEMCD_SERVER=1.5.1
   MEMCD_SERVER=1.5.2
   #MEMCD_SERVER=latest

.. note::
   This is the official Memcached server which might already have other tags available,
   check their official website for even more versions.
   https://hub.docker.com/_/memcached/


.. _env_mongo_server:

MONGO_SERVER
------------

This variable choses your desired MongoDB server version to be started.

+-------------------------+-------------------------------------------------------------------+------------------+
| Name                    | Allowed values                                                    | Default value    |
+=========================+===================================================================+==================+
| ``MONGO_SERVER``        | ``2.8`` |br| ``3.0`` |br| ``3.2`` |br| ``3.4`` |br| and many more | ``3.4``          |
+-------------------------+-------------------------------------------------------------------+------------------+

All values are already available in the ``.env`` file and just need to be commented or uncommented. If multiple values are uncommented, the last uncommented variable one takes precedences:

.. code-block:: bash
   :caption: .env
   :name: .env
   :emphasize-lines: 6

   host> grep MONGO_SERVER .env

   #MONGO_SERVER=2.8
   #MONGO_SERVER=3.0
   #MONGO_SERVER=3.2
   MONGO_SERVER=3.4
   #MONGO_SERVER=3.5

.. note::
   This is the official MongoDB server which might already have other tags available,
   check their official website for even more versions.
   https://hub.docker.com/_/mongo/


Docker host mounts
==================

The Docker host mounts are directory paths on your host operating system that will be mounted into
the running Docker container. This makes data persistent accross restarts and let them be available
on both sides: Your host operating system as well as inside the container.

This also gives you the choice to edit data on your host operating system, such as with your
favourite IDE/editor and also inside the container, by using the bundled tools, such as
downloading libraries with ``composer`` and others.

Being able to do that on both sides, removes the need to install any development tools (except your
IDE/editor) on your host and have everything fully encapsulated into the containers itself.


.. _env_httpd_datadir:

HOST_PATH_HTTPD_DATADIR
-----------------------

This is an absolute or relative path (relative to Devilbox git directory) to your data directory.

.. seealso::
   :ref:`getting_started_directory_overview_datadir`

By default, all of your websites/projects will be stored in that directory. If however you want
to separate your data from the Devilbox git directory, do change the path to a place where you
want to store all of your projects on your host computer.

* Relative path: relative to the devilbox git directory (Must start with ``.``)
* Absolute path: Full path (Must start with ``/``)

+------------------------------+----------------+----------------+
| Name                         | Allowed values | Default value  |
+==============================+================+================+
| ``HOST_PATH_HTTPD_DATADIR``  | valid path     | ``./data/www`` |
+------------------------------+----------------+----------------+

Example
^^^^^^^

If you want to move all your projects to ``/home/myuser/workspace/web/`` for example, just set it
like this:

.. code-block:: bash
   :caption: .env
   :name: .env

    HOST_PATH_HTTPD_DATADIR=/home/myuser/workspace/web

Mapping
^^^^^^^

No matter what path you assign, inside the PHP and the web server container your data dir will
always be ``/shared/httpd/``.

.. warning::
   Do not create any symlinks inside your project directories that go outside the data dir.
   Anything which is outside this directory is not mounted into the container.

.. warning::
   :ref:`remove_stopped_container`
     Whenever you change this value you have to stop the Devilbox and also remove the stopped
     container via
     ``docker-compose rm``.


.. _env_mysql_datadir:

HOST_PATH_MYSQL_DATADIR
-----------------------

This is an absolute or relative path (relative to Devilbox git directory) to your MySQL data directory.

* Relative path: relative to the devilbox git directory (Must start with ``.``)
* Absolute path: Full path (Must start with ``/``)

+------------------------------+----------------+------------------+
| Name                         | Allowed values | Default value    |
+==============================+================+==================+
| ``HOST_PATH_MYSQL_DATADIR``  | valid path     | ``./data/mysql`` |
+------------------------------+----------------+------------------+

Each MySQL, MariaDB or PerconaDB version will have its own subdirectory, so when first running MySQL 5.5
and then starting MySQL 5.6, you will have a different database with different data.

Having each version separated from each other makes sure that you don't accidently upgrade
from a lower to a higher version which might not be reversable. (MySQL auto-upgrade certain older
data files to newer, but this process does not necessarily work the other way round and could result in failues).

The directory structure will look something like this:

.. code-block:: bash

    host> ls -l ./data/mysql/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mariadb-10.0/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mariadb-10.1/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mariadb-10.2/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mariadb-10.3/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mysql-5.5/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mysql-5.6/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mysql-5.7/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 mysql-8.0/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 percona-5.5/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 percona-5.6/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 percona-5.7/

.. warning::
   :ref:`remove_stopped_container`
     Whenever you change this value you have to stop the Devilbox and also remove the stopped
     container via
     ``docker-compose rm``.


.. _env_pgsql_datadir:

HOST_PATH_PGSQL_DATADIR
-----------------------

This is an absolute or relative path (relative to Devilbox git directory) to your PostgreSQL data directory.

* Relative path: relative to the devilbox git directory (Must start with ``.``)
* Absolute path: Full path (Must start with ``/``)

+------------------------------+----------------+------------------+
| Name                         | Allowed values | Default value    |
+==============================+================+==================+
| ``HOST_PATH_PGSQL_DATADIR``  | valid path     | ``./data/pgsql`` |
+------------------------------+----------------+------------------+

Each PostgreSQL version will have its own subdirectory, so when first running PostgreSQL 9.1
and then starting PostgreSQL 10.0, you will have a different database with different data.

Having each version separated from each other makes sure that you don't accidently upgrade
from a lower to a higher version which might not be reversable.

The directory structure will look something like this:

.. code-block:: bash

    host> ls -l ./data/pgsql/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 9.1/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 9.2/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 9.3/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 9.4/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 9.5/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 9.6/

.. warning::
   :ref:`remove_stopped_container`
     Whenever you change this value you have to stop the Devilbox and also remove the stopped
     container via
     ``docker-compose rm``.


.. _env_mongo_datadir:

HOST_PATH_MONGO_DATADIR
-----------------------

This is an absolute or relative path (relative to Devilbox git directory) to your MongoDB data directory.

* Relative path: relative to the devilbox git directory (Must start with ``.``)
* Absolute path: Full path (Must start with ``/``)

+------------------------------+----------------+------------------+
| Name                         | Allowed values | Default value    |
+==============================+================+==================+
| ``HOST_PATH_MONGO_DATADIR``  | valid path     | ``./data/mongo`` |
+------------------------------+----------------+------------------+

Each MongoDB version will have its own subdirectory, so when first running MongoDB 2.8
and then starting MongoDB 3.5, you will have a different database with different data.

Having each version separated from each other makes sure that you don't accidently upgrade
from a lower to a higher version which might not be reversable.

The directory structure will look something like this:

.. code-block:: bash

    host> ls -l ./data/mongo/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 2.8/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 3.0/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 3.2/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 3.4/
    drwxrwxr-x 6 48 48 4096 Jun 21 08:47 3.5/

.. warning::
   :ref:`remove_stopped_container`
     Whenever you change this value you have to stop the Devilbox and also remove the stopped
     container via
     ``docker-compose rm``.


Docker host ports
=================

All describned host ports below are ports that the Docker container expose on your host operating
system. By default each port will be exposed to all interfaces or IP addresses of the host
operating system. This can be controlled with :ref:`env_local_listen_addr`.

**How to list used ports on Linux and MacOS**

Open a terminal and type the following:

.. code-block:: bash

    host> netstat -an | grep 'LISTEN\s'
    tcp        0      0 127.0.0.1:53585    0.0.0.0:*     LISTEN
    tcp        0      0 127.0.0.1:37715    0.0.0.0:*     LISTEN
    tcp        0      0 127.0.0.1:58555    0.0.0.0:*     LISTEN
    tcp        0      0 127.0.0.1:48573    0.0.0.0:*     LISTEN
    tcp        0      0 127.0.0.1:34591    0.0.0.0:*     LISTEN
    tcp        0      0 127.0.0.1:8000     0.0.0.0:*     LISTEN

**How to list used ports on Windows**

Open the command prompt and type the following:

.. code-block:: bash

    C:\WINDOWS\system32> netstat -an
    Proto  Local Address       Foreign Address      State
    TCP    0.0.0.0:80          0.0.0.0:0            LISTENING
    TCP    0.0.0.0:145         0.0.0.0:0            LISTENING
    TCP    0.0.0.0:445         0.0.0.0:0            LISTENING
    TCP    0.0.0.0:1875        0.0.0.0:0            LISTENING

.. warning::
   :ref:`docker_toolbox`
      When using Docker Toobox ensure that ports are exposed to all interfaces.
      See :ref:`env_local_listen_addr`

.. warning::
   Before setting the ports, ensure that they are not already in use on your host operating
   system by other services.


HOST_PORT_HTTPD
---------------

The port to expose for the web server (Apache or Nginx). This is usually 80. Set it to something
else if 80 is already in use on your host operating system.

+----------------------+-------------------+------------------+
| Name                 | Allowed values    | Default value    |
+======================+===================+==================+
| ``HOST_PORT_HTTPD``  | ``1`` - ``65535`` | ``80``           |
+----------------------+-------------------+------------------+


HOST_PORT_MYSQL
---------------

The port to expose for the MySQL server (MySQL, MariaDB or PerconaDB). This is usually 3306. Set it
to something else if 3306 is already in use on your host operating system.

+----------------------+-------------------+------------------+
| Name                 | Allowed values    | Default value    |
+======================+===================+==================+
| ``HOST_PORT_MYSQL``  | ``1`` - ``65535`` | ``3306``         |
+----------------------+-------------------+------------------+


HOST_PORT_PGSQL
---------------

The port to expose for the PostgreSQL server. This is usually 5432. Set it
to something else if 5432 is already in use on your host operating system.

+----------------------+-------------------+------------------+
| Name                 | Allowed values    | Default value    |
+======================+===================+==================+
| ``HOST_PORT_PGSQL``  | ``1`` - ``65535`` | ``5432``         |
+----------------------+-------------------+------------------+


HOST_PORT_REDIS
---------------

The port to expose for the Redis server. This is usually 6379. Set it
to something else if 6379 is already in use on your host operating system.

+----------------------+-------------------+------------------+
| Name                 | Allowed values    | Default value    |
+======================+===================+==================+
| ``HOST_PORT_REDIS``  | ``1`` - ``65535`` | ``5432``         |
+----------------------+-------------------+------------------+


HOST_PORT_MEMCD
---------------

The port to expose for the Memcached server. This is usually 11211. Set it
to something else if 11211 is already in use on your host operating system.

+----------------------+-------------------+------------------+
| Name                 | Allowed values    | Default value    |
+======================+===================+==================+
| ``HOST_PORT_MEMCD``  | ``1`` - ``65535`` | ``11211``        |
+----------------------+-------------------+------------------+


HOST_PORT_MONGO
---------------

The port to expose for the MongoDB server. This is usually 27017. Set it
to something else if 27017 is already in use on your host operating system.

+----------------------+-------------------+------------------+
| Name                 | Allowed values    | Default value    |
+======================+===================+==================+
| ``HOST_PORT_MONGO``  | ``1`` - ``65535`` | ``27017``        |
+----------------------+-------------------+------------------+


.. _env_host_port_bind:

HOST_PORT_BIND
--------------

The port to expose for the BIND DNS server. This is usually ``53``. Set it
to something else if ``53`` is already in use on your host operating system.

+----------------------+-------------------+------------------+
| Name                 | Allowed values    | Default value    |
+======================+===================+==================+
| ``HOST_PORT_BIND``   | ``1`` - ``65535`` | ``1053``         |
+----------------------+-------------------+------------------+

.. warning::
   As you might have noticed, BIND is not set to its default port ``53`` by default, but rather
   to ``1053``. This is because some operating system already have a local DNS resolver running
   on port ``53`` which would result in a failure when this BIND server is starting.

   You only need to set BIND to port ``53`` when you want to use the ``Auto-DNS`` feautre of the
   Devilbox. When doing so, read this article with care: :ref:`global_configuration_auto_dns`.


Container settings
==================

PHP
---

Custom variables
^^^^^^^^^^^^^^^^

The PHP container itself does not offer any variables, however you can add any key-value pair
variable into the ``.env`` file which will automatically be available to the started PHP container
and thus in any of your PHP projects.

If your application requires a variable to determine if it is run under development or
production, for example: ``APPLICATION_ENV``, you can just add this to the ``.env`` file:

.. code-block:: bash
   :caption: .env
   :name: .env
   :emphasize-lines: 3

   host> grep APPLICATION_ENV .env

   APPLICATION_ENV=development

Within your php application/file you can then access this variable via the ``getenv`` function:

.. code-block:: php
   :caption: index.php
   :name: index.php
   :emphasize-lines: 3

   <?php
   // Example use of getenv()
   echo getenv('APPLICATION_ENV');
   ?>

This will then output ``development``.


.. note::
   Add as many custom environment variables as you require.

.. seealso:: :ref:`tutorial_custom_environment_variables`


Web server
----------

HTTPD_DOCROOT_DIR
^^^^^^^^^^^^^^^^^

This variable specifies the name of a directory within each of your project directories from which
the web server will serve the files.

Together with the :ref:`env_httpd_datadir` and your project directory, the ``HTTPD_DOCROOT_DIR``
will built up the final location of a virtual hosts document root.

+-----------------------+-------------------+------------------+
| Name                  | Allowed values    | Default value    |
+=======================+===================+==================+
| ``HTTPD_DOCROOT_DIR`` | valid dir name    | ``htdocs``       |
+-----------------------+-------------------+------------------+

**Example 1**

* devilbox git directory location: ``/home/user-1/repo/devilbox``
* HOST_PATH_HTTPD_DATADIR: ``./data/www`` (relative)
* Project directory: ``my-first-project``
* HTTPD_DOCROOT_DIR: ``htdocs``

The location from where the web server will serve files for ``my-first-project`` is then:
``/home/user-1/repo/devilbox/data/www/my-first-project/htdocs``

**Example 2**

* devilbox git directory location: ``/home/user-1/repo/devilbox``
* HOST_PATH_HTTPD_DATADIR: ``/home/user-1/www`` (absolute)
* Project directory: ``my-first-project``
* HTTPD_DOCROOT_DIR: ``htdocs``

The location from where the web server will serve files for ``my-first-project`` is then:
``/home/user-1/www/my-first-project/htdocs``

**Directory structure: default**

Let's have a look how the directory is actually built up:

.. code-block:: bash
   :emphasize-lines: 4

    # Project directory
    host> ls -l data/www/my-first-project/
    total 4
    drwxr-xr-x 2 cytopia cytopia 4096 Mar 12 23:05 htdocs/

    # htdocs directory inside your project directory
    host> ls -l data/www/my-first-project/htdocs
    total 4
    -rw-r--r-- 1 cytopia cytopia 87 Mar 12 23:05 index.php

By calling your proect url, the ``index.php`` file will be served.


**Directory structure: nested symlink**

Most of the time you would clone or otherwise download a PHP framework, which in most cases has
its own `www` directory somewhere nested. How can this be linked to the ``htdocs`` directory?

Let's have a look how the directory is actually built up:

.. code-block:: bash
   :emphasize-lines: 5

    # Project directory
    host> ls -l data/www/my-first-project/
    total 4
    drwxr-xr-x 2 cytopia cytopia 4096 Mar 12 23:05 cakephp/
    lrwxrwxrwx 1 cytopia cytopia   15 Mar 17 09:36 htdocs -> cakephp/webroot/

    # htdocs directory inside your project directory
    host> ls -l data/www/my-first-project/htdocs
    total 4
    -rw-r--r-- 1 cytopia cytopia 87 Mar 12 23:05 index.php

As you can see, the web server is still able to server the files from the ``htdocs`` location,
this time however, ``htdocs`` itself is a symlink pointing to a much deeper and nested location
inside an actual framework directory.


.. _env_httpd_template_dir:

HTTPD_TEMPLATE_DIR
^^^^^^^^^^^^^^^^^^

This variable specifies the directory name (which is just in your project directory, next to the
HTTPD_DOCROOT_DIR directory) in which you can hold custom web server configuration files.

**Every virtual host (which represents a project) can be fully customized to its own needs,
independently of other virtual hosts.**

This directory does not exist by default and you need to create it. Additionally you will also
have to populate it with one of three yaml-based template files.

+------------------------+-------------------+------------------+
| Name                   | Allowed values    | Default value    |
+========================+===================+==================+
| ``HTTPD_TEMPLATE_DIR`` | valid dir name    | ``.devilbox``    |
+------------------------+-------------------+------------------+

Let's have a look at an imaginary project directory called ``my-first-project``:

.. code-block:: bash

    # Project directory
    host> ls -l data/www/my-first-project/
    total 4
    drwxr-xr-x 2 cytopia cytopia 4096 Mar 12 23:05 htdocs/

Inside this your project directory you will need to create another directory which is called
``.devilbox`` by default. If you change the ``HTTPD_TEMPLATE_DIR`` variable to something else,
you will have to create a directory by whatever name you chose for that variable.

.. code-block:: bash
   :emphasize-lines: 3,6

    # Project directory
    host> cd data/www/my-first-project/
    host> mkdir .devilbox
    host> ls -l
    total 4
    drwxr-xr-x 2 cytopia cytopia 4096 Mar 12 23:05 .devilbox/
    drwxr-xr-x 2 cytopia cytopia 4096 Mar 12 23:05 htdocs/

Now you need to copy the ``vhost-gen`` templates into the ``.devilbox`` directory. The templates
are available in the Devilbox git directory under ``templates/vhost-gen/``.

By copying those files into your project template directory, nothing will change, these are the
default templates that will create the virtual host exactly the same way as if they were not
present.

.. code-block:: bash
   :emphasize-lines: 5

    # Navigate into the devilbox directory
    host> cd path/to/devilbox

    # Copy templates to your project directory
    host> cp templates/vhost-gen/* data/www/my-first-project/.devilbox/


Let's have a look how the directory is actually built up:

.. code-block:: bash
   :emphasize-lines: 4,8

    # Project directory
    host> ls -l data/www/my-first-project/
    total 4
    drwxr-xr-x 2 cytopia cytopia 4096 Mar 12 23:05 .devilbox/
    drwxr-xr-x 2 cytopia cytopia 4096 Mar 12 23:05 htdocs/

    # template directory inside your project directory
    host> ls -l data/www/my-first-project/htdocs/.devilbox
    total 4
    -rw-r--r-- 1 cytopia cytopia 87 Mar 12 23:05 apache22.yml
    -rw-r--r-- 1 cytopia cytopia 87 Mar 12 23:05 apache24.yml
    -rw-r--r-- 1 cytopia cytopia 87 Mar 12 23:05 nginx.yml

The three files ``apache22.yml``, ``apache24.yml`` and ``nginx.yml`` let you customize your web
servers virtual host to anything from adding rewrite rules, overwriting directory index to even
changing the server name or adding locations to other assets.

.. seealso::
    The whole process is based on a project called `vhost-gen <https://github.com/devilbox/vhost-gen>`_.
    A virtual host generator for Apache 2.2, Apache 2.4 and any Nginx version.

.. seealso::
    **Customize your virtual host**
      When you want to find out more how to actually customize each virtual host to its own need,
      read up more on :ref:`custom_vhost`.
    **Tutorials**
      Also have a look at this tutorial which is a walk-through showing you how to modify
      a virtual host and make it serve all files for multiple sub domains (server names):
      :ref:`tutorial_adding_sub_domains`


MySQL
-----

.. _env_mysql_root_password:

MYSQL_ROOT_PASSWORD
^^^^^^^^^^^^^^^^^^^

If you start a MySQL container for the first time, it will setup MySQL itself with this specified
password. If you do change the root password to something else, make sure to also set it
accordingly in ``.env``, otherwise the devilbox will not be able to connect to MySQL and will not
be able to display information inside the bundled intranet.

+-------------------------+-------------------+---------------------+
| Name                    | Allowed values    | Default value       |
+=========================+===================+=====================+
| ``MYSQL_ROOT_PASSWORD`` | any string        | empty (no password) |
+-------------------------+-------------------+---------------------+

.. warning::
    Keep this variable in sync with the actual MySQL root password.


MYSQL_GENERAL_LOG
^^^^^^^^^^^^^^^^^

This variable controls the logging behaviour of the MySQL server (MySQL, MariaDB and PerconaDB).
As the Devilbox is intended to be used for development, this feature is turned on by default.

+-------------------------+-------------------+---------------------+
| Name                    | Allowed values    | Default value       |
+=========================+===================+=====================+
| ``MYSQL_GENERAL_LOG``   | ``0`` or ``1``    | ``1``               |
+-------------------------+-------------------+---------------------+

**MySQL documentation:**
    "The general query log is a general record of what mysqld is doing. The server writes information to this log when clients connect or disconnect, and it logs each SQL statement received from clients. The general query log can be very useful when you suspect an error in a client and want to know exactly what the client sent to mysqld."

    -- https://dev.mysql.com/doc/refman/5.7/en/query-log.html

PostgreSQL
----------


PGSQL_ROOT_USER
^^^^^^^^^^^^^^^

If you start a PostgreSQL container for the first time, it will setup PostgreSQL itself with a
specified username and password. If you do change the root username or password to something else,
make sure to also set it accordingly in .``env,`` otherwise the devilbox will not be able to
connect to PostgreSQL and will not be able to display information inside the bundled intranet.

+-------------------------+---------------------+---------------------+
| Name                    | Allowed values      | Default value       |
+=========================+=====================+=====================+
| ``PGSQL_ROOT_USER``     | alphabetical string | ``postgres``        |
+-------------------------+---------------------+---------------------+

.. warning::
    Keep this variable in sync with the actual PostgreSQL username.


PGSQL_ROOT_PASSWORD
^^^^^^^^^^^^^^^^^^^

If you start a PostgreSQL container for the first time, it will setup PostgreSQL itself with a
specified username and password. If you do change the root username or password to something else,
make sure to also set it accordingly in .``env,`` otherwise the devilbox will not be able to
connect to PostgreSQL and will not be able to display information inside the bundled intranet.

+-------------------------+---------------------+---------------------+
| Name                    | Allowed values      | Default value       |
+=========================+=====================+=====================+
| ``PGSQL_ROOT_PASSWORD`` | any string          | empty (no password) |
+-------------------------+---------------------+---------------------+

.. warning::
    Keep this variable in sync with the actual PostgreSQL password.


Bind
----

BIND_DNS_RESOLVER
^^^^^^^^^^^^^^^^^

This variable holds a comma separated list of IP addresses of DNS servers.
By default using Google's DNS server as they are pretty fast.

+-------------------------+--------------------------------------+---------------------+
| Name                    | Allowed values                       | Default value       |
+=========================+======================================+=====================+
| ``BIND_DNS_RESOLVER``   | comma separated list of IP addresses | ``8.8.8.8,8.8.4.4`` |
+-------------------------+--------------------------------------+---------------------+

The devilbox is using its own DNS server internally (your host computer can also use it for
Auto-DNS) in order to resolve custom project domains defined by ``TLD_SUFFIX``.
To also be able to reach the internet from within the Container there must be some kind of
upstream DNS server to ask for queries.

.. note::
    If you don't trust the Google DNS server, then set it to something else.
    If you already have a DNS server inside your LAN and also want your custom DNS (if any)
    to be available inside the containers, set the value to its IP address.


.. |br| raw:: html

   <br />