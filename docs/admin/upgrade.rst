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
   $ docker-compose down
   $ docker-compose up -d


Other setup (using Git)
-----------------------

All you need to do is download or checkout new version from our repositories and rebuild the application (according to guidelines above):

.. code-block:: shell

   $ git checkout vX.Y.Z

Dependencies
------------

If you need to upgrade MongoDB version, follow the official instructions in their `documentation <https://docs.mongodb.com/manual/release-notes/4.0/#upgrade-procedures>`_.

Upgrade process
===============

Usually, nothing special is required for upgrade. Internal structure changes are migrated automatically using DB migrations and Metamodel migrations *(since 1.8.0)*. See below the changes that needs to be done by you *(since 1.10.0)*:

1.10.X to 2.0.0
---------------

- Changing the major version actually does not mean any problem in migration, it has been made due to significant internal changes (restructuring, new repositories, etc.)

1.9.X to 1.10.0
---------------

- Custom DMP templates needs to be upgraded to a new structure (see the default *root* template).


Compatibility
=============

.. Important::

   DSW Client and Server should always use matching version (compatibility is assured)!


The DS Wizard is compatible with all recent versions of web browsers Chrome, Opera, Firefox, and Edge. We do not recomment use of Internet Explorer. Internally, there are components between is are following compatibility of versions:

+------------------+--------------+-----------+
| DS Wizard        | KM Metamodel | Registry  |
+==================+==============+===========+
| 2.0.0            |            5 |     1.3.0 |
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
