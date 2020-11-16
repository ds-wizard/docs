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


Own Instance
============

.. WARNING::

   For production use, consult deployment and other operations with your sysadmin or contact us for `professional services <https://ds-wizard.org/services.html>`_. These instructions are starter hints for local deployment and testing of DSW.


.. _installation-docker:

With Docker
-----------

The simplest way is to use `Docker Compose <https://docs.docker.com/compose/>`_. Requirements are just to have Docker installed, privileges for current user and the Docker daemon started.

1. Visit and clone https://github.com/ds-wizard/dsw-deployment-example
2. Check config files (described in :ref:`Configuration`), especially ``application.yml`` and ``docker-compose.yml``
3. Run the DSW with Docker compose ``docker-compose up -d``
4. After starting up, you will be able to open the Wizard in your browser on http://localhost:8080
5. You can use ``docker-compose logs`` to see the logs and ``docker-compose down`` to stop all the services

.. WARNING::

   The provided example is **not intended for production** use!


Without Docker
--------------

We highly recommend using Docker, but you are open to compile and run everything directly on your device. It is tested and distributed using Ubuntu 18.04. Additional expertise related to programming and building/running Elm, Haskell, Python, and others is required.

The related code and instructions are available:

- https://github.com/ds-wizard/engine-backend
- https://github.com/ds-wizard/engine-frontend
- https://github.com/ds-wizard/document-worker


Default Users
-------------

Initially, migrations will fill the database with predefined data needed including three users, all with password "password":

* albert.einstein@example.com (:ref:`usage-administrator`)
* nikola.tesla@example.com (:ref:`usage-datasteward`)
* isaac.newton@example.com (:ref:`usage-researcher`)

You can use those accounts for testing or to initially made your own account admin and then delete them.

.. DANGER::

   Having public instance with default accounts is a **security risk**. Delete or change default accounts (mainly Albert Einstein) if your DSW instance is public as soon as possible.


.. _installation-registry:

Registry
--------

When you have your own self-hosted instance, it is essential for you to register within the `Registry service <https://registry.ds-wizard.org>`_. It is source of shared knowledge models and can support your deployment. After registration of your organization with unique ID and email verification, you will get your **token**. This token is then used in :ref:`config-settings`. Then your instance is connected automatically to the Registry service for specific functionality such as accessing shared knowledge models.


Initial Knowledge Model
-----------------------

When you have a fresh installation, there are just the default users and no knowledge models. You are free to create a new one from scratch if you want. Other option is to import existing KM ``dsw:root:X.Y.Z`` from the `Registry <https://registry.ds-wizard.org>`_. It is the core knowledge model for general data stewardship. The specific latest version (or other version that is the best for you) as well as other shared knowledge models can be found on the landing page of the `Registry service <https://registry.ds-wizard.org>`_. Other option is to import it from file if you have any (according to :ref:`usage`). Same principle works for document templates (if you need to update them or get other).


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

.. DANGER::

   This is just a very simple example. For production use you need to consider some more advances backup techniques including storing backups encrypted on other servers (or even other geographic location).
