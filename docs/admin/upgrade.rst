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

2.5.X to 2.6.0
--------------

- The document templates including the default ``questionnaire-report`` must be updated from `https://registry.ds-wizard.org/templates <Registry>`_.
- Upgraded template metamodel version 2 requires manual migration of custom templates:

  - `questionnaireRepliesMap` (map path:Reply) is no longer present in the context
  - `questionnaireReplies` is now map with path:ReplyValue, provided filters (such as ``reply_str_value``) are adjusted but wherever you used ``reply.value.value`` it should be ``reply.value`` with this change.
  - Reply for item question is no longer an integer (number of answers) but a list of UUIDs representing the answers instead of integers. We added ``reply_items`` to extract the list from a ReplyValue.

- Since 2.6.0, we are using `WebSockets <https://en.wikipedia.org/wiki/WebSocket>`_ (for live collaboration). If you are using a proxy, you need to configure it accordingly. For example, in case of Nginx:

.. code-block:: nginx

server { 
    # ...

    location / {
        # ...
        
        # required for websockets
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_read_timeout 86400;
        proxy_send_timeout 86400;
    }
}


2.4.X to 2.5.0
--------------

- Document templates have been moved from FS to database. To simplify the transition for custom templates, we added to the Docker image a script that loads templates from FS to the database via DSW API. But there are several new information that you need to provide in ``template.json`` file: ``id`` (instead of ``uuid``), ``templateId``, ``organizationId``, ``version`` (semver), ``license``, ``readme`` (Markdown). The ``id`` should be in format ``organizationId:templateId:version`` - see :ref:`config-dmptemplates-json`. Please note that this applies only for custom templates, default template can be removed from FS as it is added to the database automatically. The script must be enabled by setting envvar ``ENABLE_TEMPLATE_LOAD `` to ``1`` and ``SERVICE_TOKEN`` according to the configuration.
- Cron is no longer needed for the feedback synchronization (environment variables in ``docker-compose.yml``) as DSW schedules synchronization internally.

2.3.X to 2.4.0
--------------

- To unify configuration, document-worker now supports and prefers YAML configuration files.
- Local/custom ``template.json`` files must be updated (renamed ``allowedKMs`` to ``allowedPackages``, and several new attributes: ``description`` for template and ``shortName`` + ``color`` for each format) - see the example :ref:`config-dmptemplates-json`.


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
- Crontab image is no longer needed.
- A DMP template configuration file must contain list of ``allowedKMs`` (see the default *root* template).

1.9.X to 1.10.0
---------------

- Custom DMP templates needs to be upgraded to a new structure (see the default *root* template).


Compatibility
=============

.. Important::

   DS Wizard components (server, client, document worker, registry) should always use matching version (compatibility is assured)!


The DS Wizard is compatible with all recent versions of web browsers Chrome, Opera, Firefox, and Edge. We do not recomment use of Internet Explorer. Internally, there are components between is are following compatibility of versions:

+------------------+--------------|--------------------+-----------+
| DS Wizard        | KM Metamodel | Template Metamodel | Registry  | 
+==================+==============+====================+===========+
| 2.6.0            |            5 |                  2 |     2.6.0 |
+------------------+--------------+--------------------+-----------+
| 2.5.0            |            5 |                  1 |     2.5.0 |
+------------------+--------------+--------------------+-----------+
| 2.4.0            |            5 |                 -- |     2.4.0 |
+------------------+--------------+--------------------+-----------+
| 2.3.0            |            5 |                 -- |     2.3.0 |
+------------------+--------------+--------------------+-----------+
| 2.2.0            |            5 |                 -- |     2.2.0 |
+------------------+--------------+--------------------+-----------+
| 2.1.0            |            5 |                 -- |     2.1.0 |
+------------------+--------------+--------------------+-----------+
| 2.0.0            |            5 |                 -- |     2.0.0 |
+------------------+--------------+--------------------+-----------+
| 1.10.0           |            4 |                 -- |     1.2.0 |
+------------------+--------------+--------------------+-----------+
| 1.9.0            |            3 |                 -- |     1.1.0 |
+------------------+--------------+--------------------+-----------+
| 1.8.0            |            3 |                 -- |     1.0.0 |
+------------------+--------------+--------------------+-----------+
| 1.7.0            |            2 |                 -- |        -- |
+------------------+--------------+--------------------+-----------+
| 1.6.0            |            1 |                 -- |        -- |
+------------------+--------------+--------------------+-----------+
| 1.5.0 (or lower) |           -- |                 -- |        -- |
+------------------+--------------+--------------------+-----------+
