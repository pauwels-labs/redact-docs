=========
 Storage
=========

.. _storage:

The :ref:`Storage<storage>`'s purpose is to provide a public interface for the
:ref:`Client<client>` to perform CRUD operations on encrypted data. It provides a stable,
public API, along with an authentication and authorization layer that allows
clients to request or modify the stored data.

An important note is that the owner of the :ref:`Storage<storage>` server does not need to be
trusted. The :ref:`Client<client>` encrypts stored data before sending it to storage, meaning
that the storage server only ever handles ciphertexts (unless the :ref:`Client<client>` is
purposefully storing public, plaintext information). This allows operation of a
multi-tenant storage service to be delegated to a third-party, reducing the
burden on users.

.. warning:: Currently, the storage interface is only implemented for
             MongoDB. In the future, other database types will be supported.

API
---

``GET /<path>``
^^^^^^^^^^^^^^^^^^^^

Retrieve data at a given path.

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

``POST /``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Upsert data at a given path.

Header Parameters
~~~~~~~~~~~~~~~~~

.. list-table::
   :widths: 20 20 60
   :header-rows: 1

   * - Header Name
     - Required?
     - Description
   * - ``Content-Type``
     - Required
     - Must be: ``application/json``
    
Body Parameters
~~~~~~~~~~~~~~~

The body of the POST request should be a JSON-serialized ``Entry`` struct. The definition
of an ``Entry`` can be found `here`_.

.. _here: https://github.com/pauwels-labs/redact-crypto/blob/main/src/entry.rs
