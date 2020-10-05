************
Installation
************

.. Attention::

   If you've deployed a local instance of the Wizard (Docker or build from source), we kindly request you to fill out this `DS Wizard instance registration <https://airtable.com/shrku49AYPem5afBq>`_.

Public Instances
================

The application is currently deployed on a server provided by `CESNET <https://www.cesnet.cz/?lang=en>`_. Here are the addresses of running applications:

- `Landing page <https://ds-wizard.org>`_ with additional information
- `Demo instance <https://demo.ds-wizard.org>`_ (free to use, for trying out all the features, unstable)
- `Researchers instance <https://researchers.ds-wizard.org>`_ (free to use, to build own DMPs, prepared for serious work)

.. Tip::

   You are free to register and test out the Wizard within the `ds-wizard.org <https://ds-wizard.org>`_. Then you can decide if you want a local instance for you or your organization. Eventually you can contact us about *DSW Cloud* service where we can host and maintain your DSW instance, see our `Get Started page <https://ds-wizard.org/get-started.html>`_.


.. _installation-docker:

Via Docker
==========

The simplest way is to use `Docker Compose <https://docs.docker.com/compose/>`_. Requirements are just to have Docker installed, privileges for current user and the Docker daemon started.

1. Create a folder (e.g.,  ``/dsw``, all commands in this manual are from this working directory)  
2. Prepare all config files described in :ref:`Configuration` (especially ``application.yml``), paths are visible from the :ref:`docker-compose.yml`
3. Copy (and adjust) :ref:`docker-compose.yml` provided below
4. Run the DSW with Docker compose ``docker-compose up -d``
5. After starting up, you will be able to open the Wizard in your browser on http://localhost
6. You can use ``docker-compose logs`` to see the logs and ``docker-compose down`` to stop all the services

.. _docker-compose.yml:

.. literalinclude:: docker-compose.yml
   :caption: docker-compose.yml
   :language: yaml
   :linenos:

.. Tip::

   You can take a look at https://github.com/ds-wizard/dsw-deployment-example

Locally without Docker
======================

We highly recommend using Docker, but you are open to compile and run everything directly on your device. It is tested on Ubuntu 16.04 and you might encounter problems when using other plaforms and Linux distributions.

General Requirements
--------------------

- `The Haskell tool Stack <https://docs.haskellstack.org>`_ (for server side)
- `NPM <https://www.npmjs.com/get-npm>`_ (for client side)
- `MongoDB <https://www.mongodb.com>`_ (database, needs to be running)
- `RabbitMQ <https://www.rabbitmq.com>`_ (messaging, needs to be running)
- `Python  <https://www.python.org>`_ (>= 3.7, document worker)
- `wkhtmltopdf <https://github.com/wkhtmltopdf/wkhtmltopdf/releases>`_ (>= 0.12.5, for DMP exports in PDF)
- `Pandoc <https://github.com/jgm/pandoc/releases>`_ (>= 2.6, for DMP exports in other formats aside HTML, PDF, and JSON)

Server
------

1. Get the server app (dsw-server)

   ``git clone https://github.com/ds-wizard/engine-backend.git``

2. Copy and edit configuration (see :ref:`configuration`)

   ``cp engine-wizard/config/application.yml.example engine-wizard/config/application.yml``

3. Build (may take some time to download & build dependencies)

   ``stack build engine-wizard``

4. Run (requires MongoDB according to configuration)

   ``stack exec engine-wizard``

.. NOTE::

   Be aware that running engine-wizard requires its assets (e.g. ``templates/`` and ``config/``) to be present in the working directory or where configured.

Client
------

1. Get the client app (dsw-client)

   ``git clone https://github.com/ds-wizard/engine-frontend.git``

2. Install the app (dependencies)

   ``npm install``

3. Change configuration if the server is not running on http://localhost:3000 (see :ref:`configuration`)
4. Run the app

   ``npm run start:wizard``

5. Open app in your favorite browser
6. For minified production-ready version, use

   ``npm run build:wizard``


Document Worker
---------------

1. Get the document-worker

   ``git clone https://github.com/ds-wizard/document-worker.git``

2. Install it using ``pip`` (or using ``setup.py``)

   ``pip install .``

3. Adjust configuration for your setup (see :ref:`configuration`)

4. Run the app

   ``docworker config.yml /path/to/templates``

If you need to upgrade MongoDB version, follow the official instructions in their `documentation <https://docs.mongodb.com/manual/release-notes/4.2/#upgrade-procedures>`_.


Default Users
=============

Initially, migrations will fill the database with predefined data needed including three users, all with password "password":

* albert.einstein@example.com (:ref:`usage-administrator`)
* nikola.tesla@example.com (:ref:`usage-datasteward`)
* isaac.newton@example.com (:ref:`usage-researcher`)

You can use those accounts for testing or to initially made your own account admin and then delete them.

.. DANGER::

   Having public instance with default accounts is a **security risk**. Delete or change default accounts (mainly Albert Einstein) if your DSW instance is public as soon as possible.

.. _installation-registry:

Registry
========

When you have your own self-hosted instance, it is essential for you to register within the `Registry service <https://registry.ds-wizard.org>`_. It is source of shared knowledge models and can support your deployment. After registration of your organization with unique ID and email verification, you will get your **token**. This token is then used in :ref:`config-settings`. Then your instance is connected automatically to the Registry service for specific functionality such as accessing shared knowledge models. 

Other "Setups"
==============

Initial Knowledge Model
-----------------------

When you have a fresh installation, there are just the default users and no knowledge models. You are free to create a new one from scratch if you want. Other option is to import existing KM ``dsw:root:X.Y.Z`` from the `Registry <https://registry.ds-wizard.org>`_. It is the core knowledge model for general data stewardship. The specific latest version (or other version that is the best for you) as well as other shared knowledge models can be found on the landing page of the `Registry service <https://registry.ds-wizard.org>`_. Other option is to import it from file if you have any (according to :ref:`usage`)

Database Backup
---------------

If you want to regularly backup your database (and you should!), all you need to do is to set-up simple script as cronjob:

.. literalinclude:: dsw-backup.sh
   :caption: dsw-backup.sh
   :language: shell
   :linenos:

Make it executable (chmod a+x dsw-backup.sh) and add it as cronjob with crontab -e:

.. code-block:: none

   0 4 * * * /dsw/dsw-backup.sh

(This will do the backup every day at 4:00 AM. For more information, see `crontab.guru <https://crontab.guru/>`_.)
