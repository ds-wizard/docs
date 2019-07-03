.. _usage:

*****
Usage
*****

Usage is role-dependent and there are three roles of authorized users: :ref:`usage-researcher`, :ref:`usage-datasteward`, and :ref:`usage-administrator`. Higher-level role can do the same as lower-level role and also something more, so let's describe it from the simplest one...

.. _usage-anonymous:

Anonymous
=========

User of DSW that is not logged in yet is able to register, log in, or recover forgotten password via confirmed email address.

If configured, anonymous user can test out :guilabel:`Questionnaire Demo` (next to :guilabel:`Log In` and :guilabel:`Sign Up` buttons in the navigation bar. That is an example of our **Questionnaire** without any ability to save or export the answers, only to try out and learn. This functionality is always accessible in our public instance.

.. _usage-researcher:

Researcher
==========

Researcher is a user who works on a scientific project and has the knowledge about the specific project. His/her goal is to get high-quality FAIR Data Management Plan and learn how to work with data in the project.

Questionnaire
-------------

Data Stewardship Planner provides simple way to create questionnaire from KM package and fill it in smart way - only relevant questions for your case will be shown.

- :guilabel:`Create` = create new questionnaire from certain version of certain KM package
- For each questionnaire:

  - :guilabel:`Fill questionnaire` = starts interactive questionnaire that guides you through answering relevant questions for specific project
  - :guilabel:`Export` = export plan in selected format and template
  - :guilabel:`Edit`
  - :guilabel:`Delete`

.. _usage-datasteward:

Data Steward
============

Data Steward is a user who has good knowledge of *DS domain* (how to deal with data) and puts this knowledge into a **knowledge model**. The knowledge model is then used by scientists to create the DMP with **Questionnaire**.

Knowledge Models
----------------

Knowledge Models are collections of DS knowledge. Each package has own unique identifier consisting of organization ID and km ID (and then also version). It stores all the knowledge units = changes of "zero" knowledge (add, delete, edit - chapter, question, answer, reference, etc.).

- :guilabel:`Import` = import new KM package or new version of KM package from file or from :ref:`installation-registry`
- For each KM package:

  - :guilabel:`Delete`
  - :guilabel:`View detail` = shows detail with versions and basic information about the KM and for each version:

    - :guilabel:`Export` = export specific version of KM, that can be then imported (e.g. in different instance of DSW)
    - :guilabel:`Delete`
    - :guilabel:`Fork Knowledge Model` = shortcut to create editor from specific version of KM
    - :guilabel:`Create Questionnaire` = shortcut to create planner from specific version of KM

KM Editor
---------

Knowledge Model Editor allows to create new knowledge models:

1. from scratch (i.e. totally new root KM package)
2. as new version of existing KM package (i.e. some improvements needs to be done)
3. as a customization of an existing KM package (i.e., extension for specific subdomain - can be based on organizational, geographical, legal or other expertise)

- :guilabel:`Create` = create editor with specific name and KM ID, optionally based on some parent KM.
- For each KM editor:

  - :guilabel:`Open Editor` = shows editor that allows to go through the all parts of KM, create new parts, edit or delete them.
  - :guilabel:`Delete`
  - :guilabel:`Publish` (*if changes are made*) = create KM with specific version and description of changes
  - :guilabel:`Upgrade` (*if newer version of parent KM*) = migrate to newer version of parent KM in interactive migration tool

.. _usage-administrator:

Administrator
=============

Administrator manages overall settings of the Data Stewardship Wizard instance and has the highest privileges.

Organization
------------

Administrator can set two things in organization settings:

- **Organization name** = visible name of the organization that uses DSW instance
- **Organization ID** = unique identifier of the organization, it is then used in identifier of created Knowledge Models

Users
-----

User management is also quite simple. Administrator can see table with registered users, :guilabel:`Delete` or :guilabel:`Edit` single one of them, or :guilabel:`Create User` directly. When editing the user, it is possible to change all the attributes from registration and also manually change the "Active" status.
