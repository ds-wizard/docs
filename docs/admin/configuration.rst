.. _configuration:

*************
Configuration
*************

.. _config-settings:

Settings
********

**Settings** are in-app administration interface for changing various behavior and look. An adminitrator can navigate to settings from the main (left) menu. *(since 2.2.0)*

It allows to configure (among others):

- toggle optional DSW features including registration or configurable questionnaire visibility by users
- external authentication using `OpenID standard <https://openid.net>`_
- set up information texts and dashboard shown in the client (before login, after login, etc.)
- connection to `registry <https://registry.ds-wizard.org>`_
- feedback and support links and repository
- organization details (such as name, ID, and pre-configured affiliations)

.. _config-openid:

OpenID
======

To configure OpenID service for external authentication, you need to have the following

- *Client ID* and *Client secret* for the service
- Root URL of the service (you can verify it that this returns valid JSON: ``<URL>/.well-known/openid-configuration``

You can test the OpenID service using official `OpenID Connect Playground <https://openidconnect.net>`_

Configuration files
*******************

Files are used for setting the server-side configuration.

.. _config-server:

Server
======

Server configuration is done using `YAML <https://en.wikipedia.org/wiki/YAML>`_ format. We provide examples directly in ``config`` folder of the repository `<https://github.com/ds-wizard/engine-backend>`_. For tests there exist special versions suffixed by ``-test``.

Build Info
----------

A build configuration (``build-info.yml`` file) contains information for the build of the application. This configuration can be created simply by running a prepared script ``build-info.sh``. Normally this script is run by a CI Tool (Travis CI) during build process. 

.. Important::

   If you build server app locally, you should also run ``build-info.sh`` otherwise your app will show bad build information and version.

Application
-----------

The mail configuration file that provide a lot of options and contains necessary settings for server to be running properly. You can see the example :ref:`config-server-application-example` below. Next, all options are described in detail.

.. _config-server-application-example:

.. literalinclude:: application.yml
   :caption: application.yml
   :language: yaml
   :linenos:

Section aside *General* may be omitted and default values will be used for whole sections (typically disabled, see below).

General
~~~~~~~

Configuration related to general operations of the server application.

.. confval:: environment

   :type: Enumeration [Production, Staging, Development, Test]
   :default: ``Production``

   Environment that your deployment is using. This affects which migrations are used and other minor things can be different in various environments.

.. confval:: serverPort

   :type: Integer [0-65535]
   :default: ``3000``

   Port that will be the web server listening on.

.. confval:: secret

   :type: String

   Secret string of 32 characters for encrypting configuration in database and JWT tokens.

.. confval:: clientUrl

   :type: URI
   :default: ``""`` (empty)

   Address of client application (e.g. ``https://localhost:8080``).

.. confval:: serviceToken

   :type: String
   :default: ``""`` (empty)

   Randomly generated string for API authentication of service endpoints.

.. confval:: integrationConfig

   :type: String
   :default: ``"engine-wizard/config/integration.yml"``

   Filename or whole path of integration configuration file (see :ref:`config-server-integration`). The path is relative to the working directory from where the Wizard runs.


Database
~~~~~~~~

Configuration of connection to MongoDB database.

.. confval:: host

   :type: String
   :default: ``"mongo"``

   Hostname or IP address of the server running MongoDB.

.. confval:: port

   :type: Integer [0-65535]
   :default: ``27017``

   Port that is used for MongoDB on the server (usually ``27017``).

.. confval:: databaseName

   :type: String
   :default: ``"dsw-server"``

   Name of the database for DS Wizard within MongoDB.

.. confval:: authEnabled

   :type: Boolean
   :default: ``false``

   Whether authentication is enabled on MongoDB server and is required to connect to the database.

.. confval:: username

   :type: String
   :default: ``""`` (empty)

   Username for authentication to database connection (if :confval:`authEnabled` is ``true``).

.. confval:: password

   :type: String
   :default: ``""`` (empty)

   Password for authentication to database connection (if :confval:`authEnabled` is ``true``).

RabbitMQ
~~~~~~~~

Configuration of connection to RabbitMQ.

.. confval:: host

   :type: String
   :default: ``"rabbitmq"``

   Hostname or IP address of the server running RabbitMQ.

.. confval:: port

   :type: Integer [0-65535]
   :default: ``5672``

   Port that is used for RabbitMQ on the server (usually ``5672``).

.. confval:: username

   :type: String
   :default: ``"guest"``

   Username for authentication to RabbitMQ connection.

.. confval:: password

   :type: String
   :default: ``"guest"``

   Password for authentication to RabbitMQ connection.

.. confval:: vhost

   :type: String
   :default: ``"/"``

   Virtual host on RabbitMQ server (see `RabbitMQ docs <https://www.rabbitmq.com/vhosts.html>`_).


Mail
~~~~

Configuration for sending emails (such as registration activation or for forgotten password mechanism) from the DS Wizard using SMTP connection.

.. Tip::

   You should enable emails unless you test the application for yourself only. It is used for email confirmations and mechanism of resetting password.

.. confval:: enabled

   :type: Boolean
   :default: ``true``

   If emails will be sent and SMTP configured.

.. confval:: name

   :type: String

   Name of the DS Wizard instance that will be used as "senders name" in email headers.

.. confval:: email

   :type: String
   :default: ``""`` (empty)

   Email address from which the emails will be sent.

.. confval:: host

   :type: String
   :default: ``""`` (empty)

   Hostname or IP address of SMTP server.

.. confval:: port

   :type: Integer [0-65535]
   :default: ``465`` (or ``25`` based on ``ssl`` option)

   Port that is used for SMTP on the server (usually ``25`` for plain or ``465`` for SSL).

.. confval:: ssl

   :type: Boolean
   :default: ``false``

   If SMTP connection is encrypted via SSL (we highly recommend this).

.. confval:: authEnabled

   :type: Boolean
   :default: ``false``

   If authentication using username and password should be used for SMTP.

.. confval:: username

   :type: String
   :default: ``""`` (empty)

   Username for the SMTP connection.

.. confval:: password

   :type: String
   :default: ``""`` (empty)

   Password for the SMTP connection.

.. _config-server-application-analytics:

Analytics
~~~~~~~~~

Configuration of analytic/informational emails for administrators, e.g., that a new user registered into the DS Wizard.

.. confval:: enabled

   :type: Boolean
   :default: ``false``

   If analytic emails should be sent.

.. confval:: email

   :type: String
   :default: ``""`` (empty)

   Target email address where analytics to which will be sent.

.. _config-server-integration:

Integration
-----------

Integrations in the DS Wizard are using external APIs and you might need some configured variables such as API keys or endpoints. For example, integration with ID ``dbase`` might use following configuration.

.. _config-server-integration-example:

.. literalinclude:: integration.yml
   :caption: integration.yml
   :language: yaml
   :linenos:

There can be multiple integrations configured in single file. These can be used then when setting up the integration in the Editor as ``${apiKey}``, ``${apiUrl}``, etc. More about integrations can be found in separate :ref:`integrations` documentation.

.. NOTE::

   Different knowledge models may use different variable naming, please read the information in README to find out what is required. We recommend authors to stick with ``apiKey`` and ``apiUrl`` variables as our convention.

.. _config-client:

Client
======

If you are running the client app using ":ref:`installation-docker`", the all you need is to specify `API_URL` environment variable inside :ref:`docker-compose.yml`. In case you want to run the client locally, you need to create a :ref:`config-client-clientjs` file in the project root:

.. _config-client-clientjs:

.. literalinclude:: config.js
   :caption: config.js
   :language: JavaScript
   :linenos:

Client also provides wide variety of style customizations using `SASS <https://sass-lang.com>`_ variables. Then you can mount it as volumes in case Docker as well:

.. code-block:: yaml

    volumes:
      # mount SCSS file
      - /path/to/extras.scss:/src/scss/customizations/_extras.scss
      - /path/to/overrides.scss:/src/scss/customizations/_overrides.scss
      - /path/to/variables.scss:/src/scss/customizations/_variables.scss
      # mount other assets, you can then refere them from scss using '/assets/...'
      - /path/to/assets:/usr/share/nginx/html/assets

- ``_extras.scss`` = This file is loaded before all other styles. You can use it, for example, to define new styles or import fonts.
- ``_overrides.scss`` = This file is loaded after all other styles. You can use it to override existing styles.
- ``_variables.scss`` = A lot of values related to styles are defined as variables. The easiest way to customize the style is to define new values for these variables using this file.

For more information about variables and assets, visit `Theming Bootstrap <https://getbootstrap.com/docs/4.0/getting-started/theming/>`_. The color of illustrations can be adjusted using ``$illustrations-color`` variable.


.. _config-docworker:

Document Worker
===============

Configuration of document worker must match with server configuration. In docker, the default moint point is set to ``/app/config.yml`` but it can be overriden using environment variable ``DOCWORKER_CONFIG``. Similarly, working directory can be changed from the default ``/tmp/docworker`` using ``DOCWORKER_WORKDIR``.

.. _config-docworker-example:

.. literalinclude:: docworker.yml
   :caption: docworker.yml
   :language: yaml
   :linenos:

mongo
-----

.. confval:: host

   :type: String
   :default: ``"localhost"``

   Hostname or IP address of the server running MongoDB.

.. confval:: port

   :type: Integer [0-65535]
   :default: ``27017``

   Port that is used for MongoDB on the server (usually ``27017``).

.. confval:: database

   :type: String

   Name of the database for DS Wizard within MongoDB.

.. confval:: collection

   :type: String
   :default: ``documents`` (optional)

   Name of the collection for documents.

.. confval:: fs_collection

   :type: String
   :default: ``documentFs`` (optional)

   Name of the collection for files.

.. confval:: templates_collection

   :type: String
   :default: ``templates`` (optional)

   Name of the collection for templates.

.. confval:: assets_fs_collection

   :type: String
   :default: ``templateAssetFs`` (optional)

   Name of the collection for template assets.

.. confval:: auth.username

   :type: String
   :default: ``None`` (optional)

   Username for authentication to database connection (will be used if set).

.. confval:: auth.password

   :type: String
   :default: ``None`` (optional)

   Password for authentication to database connection (will be used if set).

.. confval:: auth.database

   :type: String
   :default: ``<database>`` (optional)

   Authentication database used for MongoDB, defaults to the same value provided in ``database`` option.

.. confval:: auth.mechanism

   :type: String
   :default: ``"SCRAM-SHA-256"`` (optional)

   Authentication mechanism used for MongoDB.

mq
---

.. confval:: host

   :type: String
   :default: ``"localhost"``

   Hostname or IP address of the server running RabbitMQ.

.. confval:: port

   :type: Integer [0-65535]
   :default: ``5672``

   Port that is used for RabbitMQ on the server (usually ``5672``).

.. confval:: vhost

   :type: String
   :default: ``"/"``

   Virtual host on RabbitMQ server (see `RabbitMQ docs <https://www.rabbitmq.com/vhosts.html>`_).

.. confval:: queue

   :type: String

   Name of queue used for passing document jobs (typically ``document.generation``).

.. confval:: auth.username

   :type: String
   :default: ``None`` (optional)

   Username for authentication to RabbitMQ connection (if any).

.. confval:: auth.password

   :type: String
   :default: ``None`` (optional)

   Password for authentication to RabbitMQ connection (if any).


logging
-------

.. confval:: level

   :type: String
   :default: ``"WARNING"``

   Name of logging level (use names of `Python logging levels <https://docs.python.org/3/library/logging.html#levels>`_).

pandoc
------

.. confval:: executable

   :type: String
   :default: ``"pandoc"``

   Executable command for running Pandoc (can be aliased or defined with absolute path).

.. confval:: args

   :type: String
   :default: ``"--standalone"``

   Default arguments used for all Pandoc calls.

.. confval:: timeout

   :type: Float
   :default: ``None`` (optional)

   Timeout for Pandoc subprocess call.

wkhtmltopdf
-----------

.. confval:: executable

   :type: String
   :default: ``"pandoc"``

   Executable command for running WkHtmlToPdf (can be aliased or defined with absolute path).

.. confval:: args

   :type: String
   :default: ``""`` (empty)

   Default arguments used for all WkHtmlToPdf calls.

.. confval:: timeout

   :type: Float
   :default: ``None`` (optional)

   Timeout for WkHtmlToPdf subprocess call.

.. _config-dmptemplates:

DMP templates
*************

You can freely customize and style templates of documents (DMPs). HTML and CSS knowledge is required and for doing more complex templates that use some conditions, loops, or macros, knowledge of `Jinja templating language <http://jinja.pocoo.org/>`_ (pure Python implementation) is useful. 

For further information, read :ref:`template-development`. 


Email templates
***************

Similarly to :ref:`config-dmptemplates`, you can customize templates for emails sent by the Wizard located in ``templates/mail`` folder. It also uses `Jinja templating language <http://jinja.pocoo.org/>`_ (for email templates we use its implementation called `Ginger <https://ginger.tobiasdammers.nl/guide/>`_). And you can create HTML template, Plain Text template, add attachments, and add inline images (which can be used inside the HTML using `Content-ID <https://en.wikipedia.org/wiki/MIME#Related>`_ equal to the filename).

Templates structure
===================

The structure is following:

- ``templates/mail/_common`` = layout, styles, common files
- ``templates/mail/_common/attachments`` = attachments for all emails
- ``templates/mail/_common/images`` = inline images for all emails
- ``templates/mail/<name>`` = templates specific for this email type, should contain ``message.html.j2`` and ``message.txt.j2`` files (or at least one of them, `mail servers prefer both variants <https://litmus.com/blog/reach-more-people-and-improve-your-spam-score-why-multi-part-email-is-important>`_)
- ``templates/mail/<name>/attachments`` = attachments specific for email type
- ``templates/mail/<name>/images`` = inline images specific for email type

All attachments are loaded from the template-specific and common folders and included to email with detected `MIME type <https://en.wikipedia.org/wiki/Media_type>`_. It similarly works for inline images but those are not displayed as attachments just as `related part <https://en.wikipedia.org/wiki/MIME#Related>`_ to HTML part (if present). We highly recommend to use ASCII-only names without whitespaces and with standard extensions. Also, sending minimum amount of data via email is suggested.

Templates variables
===================

All templates are provided also with variables:

- ``appTitle`` = from the configuration :confval:`appTitle`
- ``clientAddress`` = from the configuration :confval:`clientUrl`
- ``mailName`` = from the configuration :confval:`name`
- ``user`` = user (subject of an email), structure with attributes accessible via . (dot, e.g. ``user.name``)

Email types
===========

Currently, there are following types of mail:

- ``registrationConfirmation`` = email sent to user after registration to verify email address, contains ``activationLink`` variable
- ``registrationCreatedAnalytics`` = email sent to address specified in the configuration about registration of a new user (see :ref:`config-server-application-analytics` config)
- ``resetPassword`` = email sent to user when requests resetting a password, contains ``resetLink`` variable


Docker deployment
=================

Including own email templates while using dockerized Wizard is practically the same as for DMP templates. You can also bind whole ``templates/mail`` folder (or even ``templates`` if want to change both):

.. code-block:: yaml

   server:
     image: datastewardshipwizard/wizard-server
     restart: always
     ports:
       - 3000:3000
     volumes:
       - /dsw/application.yml:/application/engine-wizard/config/application.yml
       - /dsw/templates/mail:/application/engine-wizard/templates/mail:ro
   # ... (continued)
