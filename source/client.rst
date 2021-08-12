========
 Client
========

.. _client:

The purpose of the :ref:`Client<client>` is to host a small server on the
user's device (e.g. phone, laptop) to respond to requests for private
data. It listens on a local port, currently defaulted to
``::8080``, and responds to these requests with a secure ``<iframe>``
buffer that displays the data without allowing the website to read
it back to itself.

.. note:: For more information on how the secure ``<iframe>`` buffers
   work see :ref:`Secure \<iframe\> <secure-iframe>`.

API
---

``GET /data/<path>``
^^^^^^^^^^^^^^^^^^^^

An unsecure request to the client for data at a given path. The response is not
the data itself, but an HTML document with an iframe which makes a secure
request to retrieve the data.

Path Parameters
~~~~~~~~~~~~~~~

.. list-table::
   :widths: 20 15 45 20
   :header-rows: 1

   * - Parameter
     - Required?
     - Description
     - Example
   * - ``path``
     - Required
     - A jsonpath-style string prepended and appended by a period which represents the path of the data
     - ``.someObj.someVal.``
    
Query Parameters
~~~~~~~~~~~~~~~~

.. list-table::
   :widths: 10 15 45 10 30
   :header-rows: 1

   * - Parameter
     - Required?
     - Description
     - Default
     - Example
   * - ``css``
     - Optional
     - A URL-encoded CSS block meant to style the displayed data. The HTML to style can be found `here`_.
     -
     - ``iframe%7Bborder%3Anone%3B%7D``
   * - ``edit``
     - Optional
     - A boolean indicating whether the data will be displayed in an editable form field.  If ``true``, the value will be displayed in a submittable input box appropriate for its data type.
     - ``false``
     - ``true``
   * - ``data_type``
     - Required
     - specifies the type of data to expect; this is particularly useful when creating new data that does not yet have a type. The value can be one of:

          * ``Bool``
          * ``U64``
          * ``I64``
          * ``F64``
          * ``String``
     -
     - ``String``
   * - ``relay_url``
     - Optional
     - the URL endpoint to which a ``POST`` HTTP request will be sent upon submission of editable data. This would typically be a URL controlled by the host of the Redact-enabled website and used for internal bookkeeping.
     -
     - ``https://foo.com/redact/relay``
   * - ``js_message``
     - Optional
     - A base64-encoded and URL-encoded message which the editable Redact field will send to the parent page after data is successfully submitted. Refer to :ref:`JS Messaging` for more details.
     -
     - ``Y3JlYXRlZA%3D%3D``
   * - ``js_height_msg_prefix``
     - Optional
     - A base64-encoded and URL-encoded message which a displayed Redact field will prepend to the pixel height of the rendered data, then send to the parent page. This can be used to dynamically adjust the height of a redact iframe on a web page based on the size of the rendered data.
     -
     - ``aGVpZ2h0Oi5hYmMuOg%3D%3D``

.. _here: https://github.com/pauwels-labs/redact-client/tree/main/static/secure.handlebars

``GET /data/<path>/<token>``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

An secure request to the client for data at a given path. The response is and 
HTML document displaying the contents of the data.

Header Parameters
~~~~~~~~~~~~~~~~~

.. list-table::
   :widths: 20 20 60
   :header-rows: 1

   * - Header Name
     - Required?
     - Description
   * - ``Cookie sid``
     - Required
     - The session ID is used internally by the :ref:`Client<client>` to associate the request with a session in its session store.

Path Parameters
~~~~~~~~~~~~~~~

.. list-table::
   :widths: 20 15 45 20
   :header-rows: 1

   * - Parameter
     - Required?
     - Description
     - Example
   * - ``path``
     - Required
     - A jsonpath-style string prepended and appended by a period which represents the path of the data
     - ``.someObj.someVal.``
   * - ``token``
     - Required
     - A random, 256-bit, upper-case alphanumeric CSRF token that is generated and used internally by the :ref:`Client<client>`
     -

Query Parameters
~~~~~~~~~~~~~~~~

.. note:: These query parameters are identical to those of ``GET /data/<path>``
   and are typically automatically included in this request by the :ref:`Client<client>`.

.. list-table::
   :widths: 10 15 45 10 30
   :header-rows: 1

   * - Parameter
     - Required?
     - Description
     - Default
     - Example
   * - ``css``
     - Optional
     - A URL-encoded CSS block meant to style the displayed data. The HTML to style can be found `here`_.
     -
     - ``iframe%7Bborder%3Anone%3B%7D``
   * - ``edit``
     - Optional
     - A boolean indicating whether the data will be displayed in an editable form field.  If ``true``, the value will be displayed in a submittable input box appropriate for its data type.
     - ``false``
     - ``true``
   * - ``data_type``
     - Required
     - specifies the type of data to expect; this is particularly useful when creating new data that does not yet have a type. The value can be one of:

          * ``Bool``
          * ``U64``
          * ``I64``
          * ``F64``
          * ``String``
     -
     - ``String``
   * - ``relay_url``
     - Optional
     - the URL endpoint to which a ``POST`` HTTP request will be sent upon submission of editable data. This would typically be a URL controlled by the host of the Redact-enabled website and used for internal bookkeeping.
     -
     - ``https://foo.com/redact/relay``
   * - ``js_message``
     - Optional
     - A base64-encoded and URL-encoded message which the editable Redact field will send to the parent page after data is successfully submitted. Refer to :ref:`JS Messaging` for more details.
     -
     - ``Y3JlYXRlZA%3D%3D``

.. _here: https://github.com/pauwels-labs/redact-client/tree/main/static/secure.handlebars

``POST /data/<token>``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A secure request to the client to update existing data or create new data at a 
given path.

Header Parameters
~~~~~~~~~~~~~~~~~

.. list-table::
   :widths: 20 20 60
   :header-rows: 1

   * - Header Name
     - Required?
     - Description
   * - ``Cookie sid``
     - Required
     - The session ID is used internally by the :ref:`Client<client>` to associate the request with a session in its session store.
   * - ``Content-Type``
     - Required
     - Must be: ``x-www-form-urlencoded``

Path Parameters
~~~~~~~~~~~~~~~

.. list-table::
   :widths: 20 15 45 20
   :header-rows: 1

   * - Parameter
     - Required?
     - Description
     - Example
   * - ``token``
     - Required
     - A random, 256-bit, upper-case alphanumeric CSRF token that is generated and used internally by the :ref:`Client<client>`
     -
    
Query Parameters
~~~~~~~~~~~~~~~~

.. list-table::
   :widths: 10 15 45 10 30
   :header-rows: 1

   * - Parameter
     - Required?
     - Description
     - Default
     - Example
   * - ``css``
     - Optional
     - A URL-encoded CSS block meant to style the displayed data. The HTML to style can be found `here`_.
     -
     - ``iframe%7Bborder%3Anone%3B%7D``
   * - ``edit``
     - Optional
     - A boolean indicating whether the data will be displayed in an editable form field.  If ``true``, the value will be displayed in a submittable input box appropriate for its data type.
     - ``false``
     - ``true``

Body Parameters
~~~~~~~~~~~~~~~

.. list-table::
   :widths: 10 15 45 30
   :header-rows: 1

   * - Parameter
     - Required?
     - Description
     - Example
   * - ``path``
     - Required
     - a jsonpath-style string prepended and appended by a period
     - ``.someObj.someVal.``
   * - ``value``
     - Required
     - The value of the data being submitted
     - ``String``
   * - ``value_type``
     - Required
     - Specifies the type of data to expect; this is particularly useful when creating new data that does not yet have a type. The value can be one of:

          * ``Bool``
          * ``U64``
          * ``I64``
          * ``F64``
          * ``String``
     - ``String``
   * - ``relay_url``
     - Optional
     - the URL endpoint to which a ``POST`` HTTP request will be sent upon submission of editable data. This would typically be a URL controlled by the host of the Redact-enabled website and used for internal bookkeeping.
     - ``https://foo.com/redact/relay``

.. _here: https://github.com/pauwels-labs/redact-client/tree/main/static/secure.handlebars

.. _client proxy endpoint:

``POST /proxy``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Retrieves the response of a GET request to a given URL, which is made via the 
client with mutual TLS. The root domain of the URL requested must match the 
root domain of the request's ``Origin`` header value.  For more information on
how to use the Proxy API, see :ref:`User Sessions`.

Header Parameters
~~~~~~~~~~~~~~~~~

.. list-table::
   :widths: 20 20 60
   :header-rows: 1

   * - Header Name
     - Required?
     - Description
   * - ``Origin``
     - Required
     -
   * - ``Content-Type``
     - Required
     - Must be: ``application/json``

Body Parameters
~~~~~~~~~~~~~~~

.. list-table::
   :widths: 10 15 45 30
   :header-rows: 1

   * - Parameter
     - Required?
     - Description
     - Example
   * - ``host_url``
     - Required
     - The URL to which to make a GET request
     - ``https://foo.com/redact/session_create``