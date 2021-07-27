=================
 Getting Started
=================

Broadly speaking, the steps to get started with Redact are as follows:

1. Get access to a `redact-store`_ instance
2. Install the `redact-client`_ locally on a device
3. Point your browser to a Redact-enabled website

.. _redact-store: https://github.com/pauwels-labs/redact-store
.. _redact-client: https://github.com/pauwels-labs/redact-client

Setup Redact-store
------------------
The storage service can be either self-hosted or provided by a third-party.
Since it only stores encrypted data, the provider of the storage service does
not need to be trusted, but should provide a reasonable level of protection
against unauthorized requests.

A self-hosted storage is fairly easy to setup, and primarily involves procuring
a database (currently only MongoDB is supported) and standing up the
redact-store server to connect to it.

A third-party storage will simply provide a URL for the :ref:`Client<client>` to connect to.

Self-hosted Storage
~~~~~~~~~~~~~~~~~~~
1. Get access to a MongoDB instance
   
   * Sign up for a free, fully-managed instance at `mongodb.com`_ (easy, quick)
   * Set up an instance on your local device or host your own instance (harder,
     time-consuming, more customizable)

2. Install Rust: https://www.rust-lang.org/tools/install 
3. ``git clone https://github.com/pauwels-labs/redact-store.git``
4. ``echo "export REDACT_DB_URL=\"<mongo connection string>\"" >>
   config/config.env``
5. ``echo "export REDACT_DB_NAME=\"<db name>\"" >> config/config.env``
6. ``source config/config.env``
7. ``cargo r``

The port and address listened on by the storage server will be provided to the
client.

.. _mongodb.com: https://mongodb.com

Install redact-client
---------------------
1. ``git clone https://github.com/pauwels-labs/redact-client.git``
2. Provide the :ref:`Storage<storage>` URL in ``config/config.yaml#storage.url``
3. ``cargo r``

Visit Redact-enabled website
----------------------------
Once the :ref:`Client<client>` is
setup locally and points to a working storage instance, Redact-enabled websites
will work. The :ref:`Client<client>` handles generation and coordination of
cryptographic material with no further input.

.. warning:: Redact currently only supports storing keys unencrypted on the file
   system. Support for hardware and software key vaults is upcoming.
