==============
 Cryptography
==============

In order to secure data and provide authentication and authorization services,
Redact leverages several cryptographic systems. Asymmetric keys are used to
create unique identities that can be used to identify users or machines across
the Redact ecosystem. Symmetric keys are used to encrypt and decrypt user data.
By combining these two concepts, Redact can create portable encrypted data that
can be consumed by any device owned by a particular user.

.. note:: All cryptographic algorithms are currently provided by
   libsodium. Symmetric keys use its xsalsa20poly1305 algorithm. Asymmetric keys
   use its curve25519xsalsa20poly1305 algorithm.

   In the future, other backing cryptographic libraries and algorithms will be
   supported.

Identity
--------

Core to the Redact architecture is the concept of identity. In order for a user
to give or deny access to a particular piece of data, they must at all times be
capable of identifying who is requesting their data and what permissions this
user has. In order to avoid centralization and dependance on a managed service
to keep track of who has access to what, Redact uses self-sovereign identities
in the form of asymmetric keypairs.

At the most basic level, all users have their own keypair with a self-signed
Redact-client certificate. They can use this keypair and certificate to make anonymous
requests to services that support anonymity. These requests are made using
mutual TLS to share the identity of the user. For example, when the Redect-client
requests data from a storage server, or relays a message to a website host, it
does so using this client certificate.

In many cases, however, a service may require some form of further
authentication. For example, an internal company website may require that only
employees of the company be capable of viewing the site. To indicate that the
user belongs to a particular group, the keypair can be signed by a different
certificate authority. This certificate can also have custom metadata added that
can further identify and authorize the user.

Encryption
----------

Redact leverages encryption to minimize the amount of time and places that your
data is available in plaintext. Its encryption framework relies on two crucial
invariants:

1. Private data is only decrypted by the Redact-client.
2. Private data never exits the Redact-client in plaintext.

By following these two rules, Redact ensures that the attack surface for
exfiltrating private data remains as small as possible.
