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
   
Path Parameters
~~~~~~~~~~~~~~~

* ``<path>`` is a jsonpath-style string prepended and appended by a period,
  e.g.
  
  * ``.someKey.``
  * ``.someObj.someVal.``
  * ``.someArr[0].``
    
Query Parameters
~~~~~~~~~~~~~~~~

* ``css`` (optional) is a URL-encoded CSS block meant to style the displayed data.

  * The HTML to style can be found `here`_.

* ``edit`` (optional) is a boolean indicating whether the data will be displayed in an
editable form field.

  * ``true``: The value will be displayed in a submittable input box
    appropriate for its data type.
  * ``false``: The value will simply be displayed as a string.
  * Default: ``false``

* ``data_type`` (required) specifies the type of data to expect; this is particularly
  useful when creating new data that does not yet have a type. The value can be
  one of:

  * ``Bool``
  * ``U64``
  * ``I64``
  * ``F64``
  * ``String``

* ``relay_url`` (optional) is the URL endpoint which will receive a ``POST``
  HTTP request upon submission of editable data.

  * This would typically be a URL controlled by the host of the Redact-enabled
    website and used for internal bookkeeping.

.. _here: https://github.com/pauwels-labs/redact-client/tree/main/static/secure.handlebars

``GET /data/<path>/<token>``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   
Header Parameters
~~~~~~~~~~~~~~~~~

* ``Cookie sid=<session ID>``

  * The session ID is used internally by the :ref:`Client<client>` to associate the request
    with a session in its session store

Path Parameters
~~~~~~~~~~~~~~~

* ``<path>`` is a jsonpath-style string prepended and appended by a period,
  e.g.
  
  * ``.someKey.``
  * ``.someObj.someVal.``
  * ``.someArr[0].``

* ``<token>`` is a random, 256-bit, upper-case alphanumeric CSRF token that is
  generated and used internally by the :ref:`Client<client>`
    
Query Parameters
~~~~~~~~~~~~~~~~

.. note:: These query parameters are identical to those of ``GET /data/<path>``
   and are typically automatically included in this request by the :ref:`Client<client>`.

* ``css`` (optional) is a URL-encoded CSS block meant to style the displayed data.

  * The HTML to style can be found `here`_.

* ``edit`` (optional) is a boolean indicating whether the data will be displayed in an
editable form field.

  * ``true``: The value will be displayed in a submittable input box
    appropriate for its data type.
  * ``false``: The value will simply be displayed as a string.
  * Default: ``false``

* ``data_type`` (required) specifies the type of data to expect; this is particularly
  useful when creating new data that does not yet have a type. The value can be
  one of:

  * ``Bool``
  * ``U64``
  * ``I64``
  * ``F64``
  * ``String``

* ``relay_url`` (optional) is the URL endpoint which will receive a ``POST``
  HTTP request upon submission of editable data.

  * This would typically be a URL controlled by the host of the Redact-enabled
    website and used for internal bookkeeping.

.. _here: https://github.com/pauwels-labs/redact-client/tree/main/static/secure.handlebars

``POST /data/<token>``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Header Parameters
~~~~~~~~~~~~~~~~~

* ``Content-Type application/x-www-form-urlencoded``

* ``Cookie sid=<session ID>``

  * The session ID is used internally by the :ref:`Client<client>` to associate the request
    with a session in its session store

Path Parameters
~~~~~~~~~~~~~~~

* ``<token>`` is a random, 256-bit, upper-case alphanumeric CSRF token that is
  generated and used internally by the :ref:`Client<client>`.
    
Query Parameters
~~~~~~~~~~~~~~~~

* ``css`` (optional) is a URL-encoded CSS block meant to style the displayed data.

  * The HTML to style can be found `here`_

* ``edit`` (optional) is a boolean indicating whether the data will be displayed in an
editable form field.

  * ``true``: The value will be displayed in a submittable input box
    appropriate for its data type.
  * ``false``: The value will simply be displayed as a string.
  * Default: ``false``

Body Parameters
~~~~~~~~~~~~~~~

* ``<path>`` (required) is a jsonpath-style string prepended and appended by a period,
  e.g.
  
  * ``.someKey.``
  * ``.someObj.someVal.``
  * ``.someArr[0].``

* ``value`` (required) is the actual value of the data being submitted

* ``value_type`` (required) specifies the type of data to expect; this is particularly
  useful when creating new data that does not yet have a type. The value can be
  one of:

  * ``Bool``
  * ``U64``
  * ``I64``
  * ``F64``
  * ``String``

* ``relay_url`` (optional) is the URL endpoint which will receive a ``POST``
  HTTP request upon submission of editable data.
