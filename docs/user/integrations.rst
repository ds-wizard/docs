.. _integrations:

************
Integrations
************

The DS Wizard support integrations of external services with API responding with JSON result containing a list of items. Such a list can be used for type hints in special type of questions - Integration question.

Integration
===========

You can create a new integration in the root of any KM by clicking on **Add integration** in **Integrations** section. A form that might seem to be a bit complicated on the first sight will appear. But filling it up is very simple. All you need to know is how the API of what you want to integrate works. Every API should have its documentation that you can use and we recommend also to try it with `Postman`_ or `curl`_.

-  **Id** = internal identifier of integration used in the Wizard, not visible in questionnaire
-  **Name** = name of the integration, typically name of the integrated service
-  **Logo** = BASE64 encoded logo of the service (we recommend PNG with width around 100px, there are services that can encode the logo from file for you, for example, `Base64 Image Encoder`_
-  **Props** = variables for building the request specific to integration questions (for example, ``filter`` when you want to filter the results based on some attributes specific to a question
   and the API allows that)
-  **Item URL** = URL that will be displayed when item from integration is selected, it should lead the user to more information about that item, you can use ``${id}`` variable to build the URL
-  Request

   -  **Request Method** = HTTP method used to request result from API (typically ``GET``)
   -  **Request URL** = target of the request, you should use ``${q}`` variable that contains what the user fills in the field (to search), then you may use variables specified in **Props** (e.g. ``${filter}``) and also variables from the configuration file
   -  **Request Headers** = various headers specified by API (usually ``Accept: application/json`` or some authentication header)
   -  **Request Body** = content of the request (for ``GET`` it should be empty)

-  Response

   -  **Response List Field** = path in the JSON response to list of results (dot notation can be used to navigate in complex structures)
   -  **Response Id Field** = name of an identifier field in the item of results list (dot notation allowed), used then in ``${id}`` variable
   -  **Response Name Field** = name of a name field in the item of results list (dot notation allowed), used as possible answer visible to the user

Dot notation is used to navigate in more complex JSON structures when the list of items or attributes of items that needs to be extracted are hidden inside. For example you would use ``result.list`` together with ``attributes.id`` and ``attributes.data.name`` in case of:

.. code-block:: js
   
   { 
     "result": {
       "list": [
         { "attributes": { "id": "id01", 
                           "data": { "name": "Foo", 
                                     //... 
                                   }
                         }
         }, //...
       ], //...
     }, //...
   }

Configuration file
==================

There is a special configuration file for integrations (typically ``integrations.yml``, more in documentation of :ref:`config-server-integration` configuation) that you can use to keep some settings out of the KM (e.g. URL of the API endpoint or API key that you don’t want to share). The file can contain for each integration (using its **Id**) a list of key-values.

.. DANGER::

   We highly recommend to store sensitive data such as API keys in the configuration file raher than directly as text in the knowledge model that can be exported to file and distributed easily by mistake.

Integration question
====================

When you have some integration(s) configured, you can create questions of type **Integration**, then you have to select which integration should be used and if has any **Props** defined, you can fill them as well. Using the **Preview** functionality, you can easily verify if it works. If *Unable to get type hints* error appears, your configuration is not correct (looking at the server log can be helpful if you have access to it).


FAIRsharing.org
===============

We recognize `FAIRsharing.org <https://fairsharing.org>`_ as high-quality service for curated, informative and educational resource on data and metadata **standards**, inter-related to **databases** and data **policies**. It is recommended to use FAIRsharing integration (for more, see :ref:`integrations`) when need a hints for **standards**, **databases**, **collections**, and **policies**. In case that requested item is not present there, use `add/claim content <https://fairsharing.org/new/>`_ rather than another integration service that does not provide such level of confidency.

.. _Postman: https://www.getpostman.com
.. _curl: https://curl.haxx.se
.. _Base64 Image Encoder: https://www.base64-image.de
.. _docs: https://docs.ds-wizard.org
