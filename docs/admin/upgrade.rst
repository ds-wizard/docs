******************
Upgrade Guidelines
******************

Upgrading DSW
=============

.. Warning::

   Backup database and other imporant data (e.g., configuration) before upgrade!

Using Docker
------------

In case of using Docker, just use the tag in :ref:`docker-compose.yml` or pull the new Docker image and restart using down/up:

.. code-block:: shell

   $ docker pull datastewardshipwizard/wizard-server
   $ docker pull datastewardshipwizard/wizard-client
   $ docker pull datastewardshipwizard/document-worker
   $ docker-compose down
   $ docker-compose up -d


Other setup (using Git)
-----------------------

All you need to do is download or checkout new version from our repositories and rebuild the application (according to guidelines above):

.. code-block:: shell

   $ git checkout vX.Y.Z

Dependencies
------------

If you need to upgrade MongoDB version, follow the official instructions in their `documentation <https://docs.mongodb.com/manual/release-notes/4.2/#upgrade-procedures>`_.

Upgrade process
===============

Usually, nothing special is required for upgrade. Internal structure changes are migrated automatically using DB migrations and Metamodel migrations *(since 1.8.0)*. See below the changes that needs to be done by you *(since 1.10.0)*:

2.3.X to 2.4.0
--------------

- To unify configuration, document-worker now supports and prefers YAML configuration files.


2.2.X to 2.3.0
--------------

(nothing)

2.1.X to 2.2.0
--------------

- Configuration of client and several features is now moved from ``application.yml`` file to in-app :ref:`config-settings`; therefore, it must be reconfigured during upgrade process. Additional ``secret`` must be configured in ``application.yml`` for encryption and JWT tokens (*JWT.secret* section has been removed), see :ref:`config-server` configuration. It is recommended to first add *general.secret* (32 chars secret), start DSW, migrate options from ``application.yml`` to :ref:`config-settings` and then optionally clean up ``application.yml`` file.
- User fiels ``name`` and ``surname`` has been renamed to ``firstName`` and ``lastName`` - it needs be updated if used in **custom** mail or document templates.
- Recommended version of MongoDB is updated to 4.2.3.

2.0.X to 2.1.0
--------------

- There is a significant change related to new *Document Worker* that handles generation of documents from templates and filled questionnaires. You need to run RabbitMQ and document-worker with correct configuration according to server, see :ref:`docker-compose.yml` and :ref:`configuration` for details.

1.10.X to 2.0.0
---------------

- Changing the major version actually does not mean any problem in migration, it has been made due to significant internal changes (restructuring, new repositories, etc.)
- If you are using Docker for running DSW, you need to change it according to new documentation of :ref:`docker-compose.yml` and :ref:`configuration`.
- Crontab image is no longer needed, see :ref:`config-feedback-sync`.
- A DMP template configuration file must contain list of ``allowedKMs`` (see the default *root* template).

1.9.X to 1.10.0
---------------

- Custom DMP templates needs to be upgraded to a new structure (see the default *root* template).


Compatibility
=============

.. Important::

   DS Wizard components (server, client, document worker, registry) should always use matching version (compatibility is assured)!


The DS Wizard is compatible with all recent versions of web browsers Chrome, Opera, Firefox, and Edge. We do not recomment use of Internet Explorer. Internally, there are components between is are following compatibility of versions:

+------------------+--------------+-----------+
| DS Wizard        | KM Metamodel | Registry  |
+==================+==============+===========+
| 2.3.0            |            5 |     2.3.0 |
+------------------+--------------+-----------+
| 2.2.0            |            5 |     2.2.0 |
+------------------+--------------+-----------+
| 2.1.0            |            5 |     2.1.0 |
+------------------+--------------+-----------+
| 2.0.0            |            5 |     2.0.0 |
+------------------+--------------+-----------+
| 1.10.0           |            4 |     1.2.0 |
+------------------+--------------+-----------+
| 1.9.0            |            3 |     1.1.0 |
+------------------+--------------+-----------+
| 1.8.0            |            3 |     1.0.0 |
+------------------+--------------+-----------+
| 1.7.0            |            2 |        -- |
+------------------+--------------+-----------+
| 1.6.0            |            1 |        -- |
+------------------+--------------+-----------+
| 1.5.0 (or lower) |           -- |        -- |
+------------------+--------------+-----------+
