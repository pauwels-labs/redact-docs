=========
 Storage
=========

The Redact-storage's purpose is to provide a public interface for the
Redact-client to perform CRUD operations on encrypted data. It provides a stable,
public API, along with an authentication and authorization layer that allows
clients to request or modify the stored data.

An important note is that the owner of the Redact-storage server does not need to be
trusted. The Redact-client encrypts stored data before sending it to storage, meaning
that the storage server only ever handles ciphertexts (unless the Redact-client is
purposefully storing public, plaintext information). This allows operation of a
multi-tenant storage service to be delegated to a third-party, reducing the
burden on users.

.. warning:: Currently, the storage interface is only implemented for
             MongoDB. In the future, other database types will be supported.

API
---

``GET /<path>``
^^^^^^^^^^^^^^^^^^^^
   
Path Parameters
~~~~~~~~~~~~~~~

* ``<path>`` is a jsonpath-style string prepended and appended by a period,
  e.g.
  
  * ``.someKey.``
  * ``.someObj.someVal.``
  * ``.someArr[0].``

``POST /``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Header Parameters
~~~~~~~~~~~~~~~~~

* ``Content-Type application/json``
    
Body Parameters
~~~~~~~~~~~~~~~

The body of the POST request should be a JSON-serialized ``Entry`` struct. The definition
of an ``Entry`` can be found `here`_.

.. _here: https://github.com/pauwels-labs/redact-crypto/blob/main/src/entry.rs
