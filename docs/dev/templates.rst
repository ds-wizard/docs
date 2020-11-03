.. _template-development:

********************
Template Development
********************

Requirements
============

- Your favorite text editor or IDE
- DSW Template Development Kit (TDK)
- DSW instance (recommended to have local one) with your admin account
- Python 3.6+ (with pip) or Docker

Template Development Kit
========================

Our Template Development Kit (TDK) provides a simple way how to work with templates locally. It is a CLI tool written in Python.

Installation
------------

You can install it easily using `pip <https://pip.pypa.io/en/stable/installing/>`_ from `Python Package Index (PyPI) <https://pypi.org/project/dsw_tdk/>`_.
Optionally, you can use virtual environment or other installation option described in the `TDK repository <https://github.com/ds-wizard/dsw-tdk>`_.

:: 

   $ pip install dsw-tdk
   $ dsw-tdk --help


It is also possible to use `datastewardshipwizard/dsw-tdk <https://hub.docker.com/repository/docker/datastewardshipwizard/dsw-tdk>`_ Docker image when you don't have Python locally:

:: 

   $ docker run datastewardshipwizard/dsw-tdk --help


Commands
--------

There are these basic commands:

- ``new`` = create a new template project, it launches a simple interactive wizard for template metadata
- ``list`` = list all templates (latest versions) from configured DSW
- ``get`` = download a template project with specified template ID from DSW
- ``put`` = upload the local template project to DSW (once or continually on-change when ``--watch`` flag is used)
- ``verify`` = check the metadata of the local template project
- ``package`` = create a ZIP distribution package from the local template project (ZIP is importable to DSW via its web interface)

Default template directory is current one for ``put``, ``verify``, and ``package``. But ``new`` and ``get`` will create a new folder according to the template ID if not explicitly set in other way.

You can use ``--help`` to find out details:

::

   $ dsw-tdk new --help


Environment variables and .env file
-----------------------------------

To avoid entering API URL and credentials every time, you can use environment variables:

- ``DSW_API`` = URL of DSW API with which you want to communicate
- ``DSW_USERNAME`` = your login (email address) to that DSW instance, must be administrator account
- ``DSW_PASSWORD`` = corresponding password 

To make this even easier, you can store those in ``.env`` file in the project root and it will be loaded automatically. Or you can specify the path to a ``.env`` file:

::

   $ dsw-tdk --dot-env /path/to/.env list


Template Metadata
=================

Each document template in DSW has metadata stored in ``template.json`` file:

- ``id`` = composed full ID of the template (``organizationId:templateId:version``)
- ``organizationId`` = identifier of organization developing the template (lowercase, numerics, dot)
- ``templateId`` = identifier of template (lowercase, numerics, dash)
- ``version`` = version (semver) in X.Y.Z format where X, Y, and Z are non-negative numbers
- ``name`` = name of the template
- ``description`` = short description of the template
- ``license`` = name of the used license
- ``readme`` = longer description usually containing changelog
- ``metamodelVersion`` = supported version of template metamodel, it affects with which DSW version is can be used
- ``recommendedPackageId`` = identifier of recommended package (if any, ``null`` otherwise)
- ``allowedPackages`` = list of package filters (see below :ref:`template-package-filter`) to specify supported packages
- ``formats`` = list of available formats (see below :ref:`template-format`) with specified steps for generation
- ``_tdk`` = TDK configuration for local development (not stored in DSW, see below :ref:`template-tdkconfig`)

DSW TDK handles ``id`` and ``readme`` for you, so you can skip then and naturally use ``README.md`` file separately.


.. _template-package-filter:

Package Filter
--------------

For filtering, the ``null`` value serves as wildcard, i.e., filter with all ``null`` values means that all packages are allowed.

- ``orgId``: identifier of organization (e.g. ``dsw``)
- ``kmId``: identifier of knowledge model (e.g. ``root``)
- ``minVersion``: minimal package version (in format X.Y.Z, inclusive)
- ``maxVersion``: maximal package version (in format X.Y.Z, inclusive)

.. _template-format:

Format
------

A template can describe how to produce several formats, each with these metadata:

- ``uuid``: UUID of the format (within template)
- ``name``: display name of the format
- ``shortName``: short name (ideally extension) for the format, it can be used for example to be displayed in icons
- ``icon``: icon style (CSS classes), preferably `Font Awesome <https://fontawesome.com/icons?d=gallery>`_, e.g. ``fas fa-file-word``
- ``steps``: list of steps for document worker to produce the document with this format, each step has ``name`` and ``options`` (see below :ref:`template-steps`)

.. _template-tdkconfig:

TDK Config
----------

Those are local-only metadata used for development of the template. You can use them in versioned ``template.json`` but those are never stored directly in DSW.

- ``version``: metadata version for needs of migrations
- ``readmeFile``: files used to get content for ``readme`` of the template, usually ``README.md``
- ``files``: list of patterns to specify files that are part of the document template (it uses Git's wildmatch patterns, so you can also exclude files or directories)

Document Context
================

Document context is an object that carries all information related to a DSW questionnaire in order to produce a document. To investigate it, it is the best to use *Questionnaire Report* template with *JSON* format. The core fields are:

- ``config`` = object with DSW configuration related to documents, e.g., ``clientUrl`` for referring to the DSW instance
- ``createdAt`` = timestamp when the document was created
- ``createdBy`` = object describing author of the document
- ``knowledgeModel`` = object describing used KM for the questionnaire

  - ``chapterUuids`` = list of UUIDs for chapters
  - ``integrationUuids``  = list of UUIDs for integrations
  - ``tagUuids``  = list of UUIDs for tags
  - ``entities`` = contains ``questions``, ``answers``, and other maps with UUID-entity pairs
  - ``name`` = name of the knowledge model
  - ``uuid`` = UUID of the knowledge model

- ``level`` = current desirability level selected for the questionnaire
- ``levels`` = list of desirability levels possible
- ``metrics`` = list of available metrics
- ``organization`` = object describing organization that runs the DSW instance
- ``package`` = object with metadata about the KM package such as ``version``, ``name``, or ``description``
- ``questionnaireName`` = name of the questionnaire
- ``questionnaireReplies`` = map of replies with path-reply pairs, each reply has ``type`` and ``value``
- ``questionnaireUuid`` = UUID of the questionnaire
- ``report`` = object that contains report for the questionnaire that contains computed information about number of answered questions as well as metric values
- ``updatedAt`` = timestamp when the document was last updated
- ``uuid`` = UUID of the document

Document Worker
===============

`Document Worker <https://github.com/ds-wizard/document-worker>`_ component is used for document generation by supplying context to a specific template based on users demands. It retrieves a job to generate document, based on desired template and format it processed the input. This processing may be composed of several steps, usually some generation using Jinja2 and then optionally transformations. For processing Jinja2, we add several custom filters to those `builtin directly in Jinja2 <https://jinja.palletsprojects.com/en/master/templates/#builtin-filters>`_. 

.. _template-steps:

Steps
-----

Each step of template produces output based on its (optional) input and options. For the first step, the input is the document context, for other steps, the output of the previous step is used.

- ``json`` = produces a JSON as simply dump of document context

  - *no options*

- ``jinja2`` = produces a document by supplying document context to specified Jinja2 template and renders it

  - options:
    
    - ``template`` = path of the template entry file (POSIX style, relative from ``template.json``, e.g. ``template/index.html.j2``)
    - ``content-type`` = resulting content type of the rendered document (e.g. ``text/html``)
    - ``extension`` = file extensions for the rendered document (e.g. ``html``)

- ``pandoc`` = runs `Pandoc <https://pandoc.org/index.html>`_ for automatic conversion between document formats, it must follow the step where document with ``from`` format is created (usually ``jinja2`` step)

  - options:
    
    - ``from`` = source format according to `possibilities of Pandoc <https://pandoc.org/index.html>`_, e.g. ``html``
    - ``to`` = target format (as above), e.g. ``docx``

- ``wkhtmltopdf`` = runs `wkhtmltopdf <https://wkhtmltopdf.org>`_ to transform HTML from the previous step to PDF document

  - *no options*

- ``rdflib-convert`` = converts between RDF formats using `rdflib <https://rdflib.readthedocs.io/en/stable/index.html>`_
    
    - ``from`` = source format (one of: ``rdf`` (XML), ``n3``, ``nt``, ``ttl``, ``trig``, ``jsonld``)
    - ``to`` = target format (as above)

Jinja2 filters
--------------

To make template development easier, the document worker provides several additional filters:

- ``any`` = check if any value of iterable is true
- ``all`` = check if all values of iterable are true
- ``datetime_format`` = formats datetime given in ISO format according to the given `format string <https://docs.python.org/3/library/datetime.html#strftime-and-strptime-format-codes>`_
- ``extract`` = for object/map and list of keys it returns list of corresponding values from that object/map
- ``of_alphabet`` = transforms a numer to letter of alphabet (e.g. ``0`` to ``a``)
- ``roman`` = transforms given number to Roman numerals
- ``markdown`` = transforms Markdown into HTML
- ``dot`` = ends string with ``.`` if it does not already end with it nor is empty
- ``reply_str_value`` = gets string value from given reply (if valid, otherwise empty string)
- ``reply_int_value`` = gets integer value from given reply (if valid, otherwise ``0``)
- ``reply_float_value`` = gets float value from given reply (if valid, otherwise ``0``)
- ``reply_items`` = gets list of items (their UUIDs) from given list-question reply (if valid, otherwise empty list)
- ``reply_path`` = joins given list of UUIDs into reply path

Graphics and scripts
====================

If you want to include some graphics or JavaScript, we recommend you to put it directly into the HTML template file. In case of graphics, use base64 encoded content (suitable for smaller images like icons and logos):

.. code-block:: HTML

   <img src="data:image/png;base64, iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFCAYAAACNbyblAAAAHElEQVQI12P4//8/w38GIAXDIBKE0DHxgljNBAAO9TXL0Y4OHwAAAABJRU5ErkJggg==" alt="Red dot" />

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


Template Metamodels
===================

Version 2 (since 2.6.0)
-----------------------

- Changed ``questionnaireReplies`` to use path-reply map and removed then redundant ``questionnaireRepliesMap`` from document context.
- Replies for list question represented as list of UUIDs instead of size used for numeric indexing.

Version 1 (since 2.5.0)
-----------------------

- Initial version of metamodel, introduced in DSW 2.5.0 as start of versioning.

Further Reading
===============

- `Jinja2: Template Designer Documentation <https://jinja.palletsprojects.com/en/master/templates/>`_
- Examples

  - `ds-wizard/questionnaire-report-template <https://github.com/ds-wizard/questionnaire-report-template>`_
  - `ds-wizard/madmp-template <https://github.com/ds-wizard/madmp-template>`_
