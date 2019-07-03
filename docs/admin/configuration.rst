.. _configuration:

*************
Configuration
*************


.. _config-server:

Server
======

Server configuration is done using `YAML <https://en.wikipedia.org/wiki/YAML>`_ format. We provide examples directly in ``config`` folder of the repository `<https://github.com/ds-wizard/dsw-server>`_. For tests there exist special versions suffixed by ``-test``.

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

Section aside *General* and *JWT* may be omitted and default values will be used for whole sections (typically disabled, see below).

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

.. confval:: clientUrl

   :type: URI
   :default: ``""`` (empty)

   Address of client application (e.g. ``https://localhost:8080``).

.. confval:: serviceToken

   :type: String
   :default: ``""`` (empty)

   Randomly generated string that matches configuration of :ref:`config-worker` component.

.. confval:: integrationConfig

   :type: String
   :default: ``"integration.yml"``

   Filename or whole path of integration configuration file (see :ref:`config-server-integration`). In case of path, it is relative to the ``config`` folder.

.. confval:: registrationEnabled

   :type: Boolean
   :default: ``true``

   If registrations within the DS Wizard instance are enabled or disabled.

.. confval:: publicQuestionnaireEnabled

   :type: Boolean
   :default: ``false``

   If public questionnaire (i.e. questionnaire demo without registration) functionality within the DS Wizard instance is enabled or disabled.

.. confval:: levelsEnabled

   :type: Boolean
   :default: ``true``

   If questions can be related to certain desirability levels/phases.

.. confval:: itemTitleEnabled

   :type: Boolean
   :default: ``false``

   If list questions require specific title per item or are just groups of subquestions.

.. confval:: questionnaireAccessibilityEnabled

   :type: Boolean
   :default: ``true``

   If questionnaires can be set private, public read-only, or public. When disabled (i.e. value is set to ``false``), all questionnaires are private.


Client
~~~~~~

Configuration related to parts displayed in the client application.

.. confval:: appTitle

   :type: String
   :default: (nothing)

   Full name of the DS Wizard instance (displayed, for example, in tab title or before login).

.. confval:: appTitleShort

   :type: String
   :default: (nothing)

   Short name of the DS Wizard instance (displayed, for example, on the top of the navigation bar)

   .. Tip::

      Use consistent :confval:`appTitle` and :confval:`appTitleShort`.

.. confval:: welcomeWarning

   :type: String
   :default: (nothing)

   Warning text for users that displays after login (if any).

.. confval:: welcomeInfo

   :type: String
   :default: (nothing)

   Info text for users that displays after login (if any).

.. confval:: privacyUrl

   :type: String (URL)
   :default: ``"https://ds-wizard.org/privacy.html"``

   URL to page with privacy policy of the service.

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

JWT
~~~

`JSON Web Token <https://jwt.io>`_ configuration for communication.

.. confval:: secret

   :type: String
   :default: ``""`` (empty)

   Secret string used for JWT signing and validation (we recommend to generate some randomly).

.. confval:: version

   :type: Integer
   :default: ``1``

   Our internal version of JWT Payload for DS Wizard (only ``1`` at the moment).

.. confval:: expiration

   :type: Integer
   :default: ``14``

   Number of days until a token expires.

Roles
~~~~~

Basic configuration of roles and privileges within the DS Wizard instance. All roles and permission use capitalized names. There are three roles: :ref:`usage-researcher`, :ref:`usage-datasteward`, and :ref:`usage-administrator`. We recommend to leave the permission as in example :ref:`config-server-application-example`, otherwise our :ref:`usage` documentation may not match your configuration.

.. confval:: defaultRole

   :type: Role (Enumeration [``RESEARCHER``, ``DATASTEWARD``, ``ADMIN``])
   :default: `DATASTEWARD`

   Role that will be assigned to newly registered user.

.. confval:: admin

   :type: Permissions
   :default: ``[ UM_PERM, ORG_PERM, KM_PERM, KM_UPGRADE_PERM, KM_PUBLISH_PERM, PM_READ_PERM, PM_WRITE_PERM, QTN_PERM, DMP_PERM]``

   List of permissions for :ref:`usage-administrator`/``ADMIN``.

.. confval:: dataSteward

   :type: Permissions
   :default: ``[KM_PERM, KM_UPGRADE_PERM, KM_PUBLISH_PERM, PM_READ_PERM, PM_WRITE_PERM, QTN_PERM, DMP_PERM]``

   List of permissions for :ref:`usage-datasteward`/``DATASTEWARD``.

.. confval:: researcher

   :type: Permissions
   :default: ``[PM_READ_PERM, QTN_PERM, DMP_PERM]``

   List of permissions for :ref:`usage-researcher`/``RESEARCHER``.


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
   :default: ``465``

   Port that is used for SMTP on the server (usually ``25`` for plain or ``465`` for SSL).

.. confval:: ssl

   :type: Boolean
   :default: ``true``

   If SMTP connection is encrypted via SSL (we highly recommend this).

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


.. _config-registry:

Registry
~~~~~~~~

.. confval:: enabled

   :type: Boolean
   :default: ``false``

   If connection with Registry should be made.

.. confval:: token

   :type: String
   :default: ``""`` (empty)

   Your organization token aquired by registration within the `Registry service <https://registry.ds-wizard.org>`_. More information can be found in :ref:`installation-registry` section of installation documentation.


Feedback
~~~~~~~~

Configuration for feedback functionality within questionnaires via GitHub issues.

.. confval:: enabled

   :type: Boolean
   :default: ``true``

   If feedback functionality will be used.

.. confval:: token

   :type: String
   :default: ``""`` (empty)

   `GitHub personal access token <https://github.blog/2013-05-16-personal-api-tokens/>`_ with permission to create issues in the selected repository.

.. confval:: owner

   :type: String
   :default: ``""`` (empty)

   GitHub username or organization under which is the repository for feedback created.

.. confval:: repo

   :type: String
   :default: ``""`` (empty)

   Name of the repository (without owner name).

.. confval:: issueurl

   :type: URI
   :default: ``"https://github.com/:owner/:repo/issues/:issueId"``

   Template URL for feedback issue.

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
      # mount variables.scss file
      - /path/to/customizations.scss:/customizations/variables.scss
      # mount other assets, you can then refere them from scss using '/assets/...'
      - /path/to/assets:/usr/share/nginx/html/assets
      
For more information about variables and assets, visit `Theming Bootstrap <https://getbootstrap.com/docs/4.0/getting-started/theming/>`_.

.. _config-worker:

Worker
======

For running scheduled service tasks, there is a (optional) server worker component. Its configuration is done with ``API_URL`` which is the same as when configuring :ref:`config-client` but also ``SERVICE_TOKEN`` that must correspond with server configuration :confval:`serviceToken`.

.. code-block:: bash

   API_URL=https://api.dsw.example.org
   SERVICE_TOKEN=<secret_token>

.. _config-dmptemplates:

DMP Templates
=============

You can freely customize and style templates of DMPs (filled questionnaires). HTML and CSS knowledge is required and for doing more complex templates that use some conditions, loops, or macros, knowledge of `Jinja templating language <http://jinja.pocoo.org/>`_ (we use its implementation called `Ginger <https://ginger.tobiasdammers.nl/guide/>`_) is useful.

Template files
--------------

We provide currently basic ``root`` template but it is possible to get inpired and create more or edit it:

- ``templates/dmp/root.json`` = metadata about the template
- ``templates/dmp/root.html.j2`` = main template file
- ``templates/dmp/root.css`` = stylesheet file included in the main file

Templates allow you to iterate through questions and answers and find what you need to compose some output. For example, you can generate longer text based on answers of various questions by knowing its texts or UUIDs. To the template, object ``dmp`` is injected and can be used as variable - for information about its structure, browse current default template or `visit source code <https://github.com/ds-wizard/dsw-server/blob/develop/lib/Api/Resource/DataManagementPlan/DataManagementPlanDTO.hs>`_.

You can have multiple DMP templates and users will be able to pick one of them when exporting a filled questionnaire. Each template must have its metadata JSON file that contain random and unique UUID, name to be displayed when picking a template, and relative path to root file of the template:

.. code-block:: json

   {
     "uuid": "43a3fdd1-8535-42e0-81a7-5edbff296e65",
     "name": "Common DSW Template",
     "rootFile": "root.html.j2"
   }

Graphics and scripts
--------------------

If you want to include some graphics or JavaScript, we recommend you to put it directly into the HTML template file. In case of graphics, use base64 encoded content (suitable for smaller images like icons and logos):

.. code-block:: HTML

   <img src="data:image/png;base64, iVBORw0KGgoAAAANSU
    hEUgAAAAUAAAAFCAYAAACNbyblAAAAHElEQVQI12P4//8/w38GI
    AXDIBKE0DHxgljNBAAO9TXL0Y4OHwAAAABJRU5ErkJggg==" 
    alt="Red dot" />

Alternatively, you can of course reference picture that is accessible online. For JavaScript, again you can put there directly some script or reference it, for example, from some CDN:

.. code-block:: none

   <style type="text/javascript" src="https://code.jquery.com/jquery-3.3.1.min.js"></style>
   <style type="text/javascript">
     jQuery(".btn").click(function(){
       jQuery(this).toggleClass(".clicked");
     });
   </style>

You can split your template code into multiple files and the use include directive that opens the file and inserts its content where the directive is placed - like we do for including CSS style in HTML template (only one complex HTML file is generated in the end):

.. code-block:: jinja

   <head>
     <title>Data Management Plan</title>
     <meta charset="utf-8">
     <style>{% include "root.css" %}</style>
   </head>

Docker deployment
-----------------

If you deploy the DS Wizard using Docker, you can mount custom files to templates/dmp folder and overwrite default template within :ref:`docker-compose.yml`:

.. code-block:: yaml

    dsw_server:
    image: datastewardshipwizard/server
    restart: always
    ports:
      - 3000:3000
    volumes:
      - /dsw/app-config.cfg:/dsw/config/app-config.cfg
      - /dsw/root.json:/dsw/templates/dmp/root.json
      - /dsw/root.html.j2:/dsw/templates/dmp/root.html.j2
      - /dsw/root.css:/dsw/templates/dmp/root.css
    external_links:
      - mongo_mongo_1:mongo
    networks:
      - default
      - mongo_default

Email Templates
===============

Similarly to :ref:`config-dmptemplates`, you can customize templates for emails sent by the Wizard located in ``templates/mail`` folder. It also uses `Jinja templating language <http://jinja.pocoo.org/>`_. And you can create HTML template, Plain Text template, add attachments, and add inline images (which can be used inside the HTML using `Content-ID <https://en.wikipedia.org/wiki/MIME#Related>`_ equal to the filename).

Templates structure
-------------------

The structure is following:

- ``templates/mail/_common`` = layout, styles, common files
- ``templates/mail/_common/attachments`` = attachments for all emails
- ``templates/mail/_common/images`` = inline images for all emails
- ``templates/mail/<name>`` = templates specific for this email type, should contain ``message.html.j2`` and ``message.txt.j2`` files (or at least one of them, `mail servers prefer both variants <https://litmus.com/blog/reach-more-people-and-improve-your-spam-score-why-multi-part-email-is-important>`_)
- ``templates/mail/<name>/attachments`` = attachments specific for email type
- ``templates/mail/<name>/images`` = inline images specific for email type

All attachments are loaded from the template-specific and common folders and included to email with detected `MIME type <https://en.wikipedia.org/wiki/Media_type>`_. It similarly works for inline images but those are not displayed as attachments just as `related part <https://en.wikipedia.org/wiki/MIME#Related>`_ to HTML part (if present). We highly recommend to use ASCII-only names without whitespaces and with standard extensions. Also, sending minimum amount of data via email is suggested.

Templates variables
-------------------

All templates are provided also with variables:

- ``clientAddress`` = from the configuration :confval:`clientUrl`
- ``mailName`` = from the configuration :confval:`name`
- ``user`` = user (subject of an email), structure with attributes accessible via . (dot, e.g. ``user.name``)

Email types
-----------

Currently, there are following types of mail:

- ``registrationConfirmation`` = email sent to user after registration to verify email address, contains ``activationLink`` variable
- ``registrationCreatedAnalytics`` = email sent to address specified in the configuration about registration of a new user (see :ref:`config-server-application-analytics` config)
- ``resetPassword`` = email sent to user when requests resetting a password, contains ``resetLink`` variable


Docker deployment
-----------------

Including own email templates while using dockerized Wizard is practically the same as for DMP templates. You can also bind whole ``templates/mail`` folder (or even ``templates`` if want to change both):

.. code-block:: yaml

   dsw_server:
     image: datastewardshipwizard/server
     restart: always
     ports:
       - 3000:3000
     volumes:
       - /dsw/app-config.cfg:/dsw/config/app-config.cfg
       - /dsw/templates/mail:/dsw/templates/mail
   # ... (continued)
