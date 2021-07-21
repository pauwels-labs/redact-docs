.. redact documentation master file, created by sphinx-quickstart on Mon Jul 12
   18:15:43 2021.  You can adapt this file completely to your liking, but it
   should at least contain the root `toctree` directive.

==============
 Introduction
==============

.. toctree::
   :maxdepth: 1
   :caption: Introduction
   :hidden:

   getting-started

.. toctree::
   :maxdepth: 1
   :caption: Architecture
   :hidden:

   overview
   client
   storage
   cryptography

.. toctree::
   :maxdepth: 1
   :caption: Build With Redact
   :hidden:

   basic-website
   advanced-features

Redact is a set of applications that allows developers to build websites with
end-to-end encrypted data. When leveraging Redact, website owners are able to
display secure user data on their webpages without having access to that data
themselves. Instead, users store sensitive data in their personal Redact storage
and retain the ability to grant and revoke access to other users of
applications. Securing data in this way protects from data leaks and gives users
control of their own data by using secure authentication mechanisms that don't
rely on passwords.

.. note:: The two applications that enable a user to store and retrieve encrypted data are
   `redact-client`_ and `redact-store`_.
   
   The Redact-client runs on the user's device and handles requests from
   the browser for secure data display. The Redact-storage is a server that
   fronts some backing database and provides a stable API for CRUD operations on
   encrypted data.

   The `redact-crypto`_ library provides all important cryptographic and storage
   abstractions.

   .. _redact-client: https://github.com/pauwels-labs/redact-client
   .. _redact-store: https://github.com/pauwels-labs/redact-store
   .. _redact-crypto: https://github.com/pauwels-labs/redact-crypto

Redact protects from data leaks as it removes the website's need to store
sensitive data entirely. In a traditional website, the website owner maintains a
database which allows them to store relevant information that their users submit
(such as a phone number or address). When a user visits the website, this
information is fetched from the database and displayed. By storing the data of
many users in a centralized database, a single breach can expose the data of all
users of a site.  A user must trust that the website has implemented appropriate
protection measures.

In a Redact-enabled website, a user can guarantee that "Redacted" data on a
webpage has been secured with strong encryption, is only accessible to those who
are explicitly granted access, and can be deleted or updated at any time.  In
order to operate on the "Redacted" data, websites maintain references to it.
For example, in a traditional website, there might be a database field called
"name" with the value "Alice Doe". In the Redact-enabled website, the database
field would still be called "name" but its value would be something like
".profile.name.". This value is interpreted by the Redact-client (installed on
the user's device), and is translated into the readable data in a way that makes
it impossible for the website owner to read it unless granted access by the
user. With no data to steal, the website owner no longer has to worry about the
liability of data leaks, and users can rest assured in knowing that the
potential attack surface for their data is greatly reduced to a single,
higly-secure, encrypted location.

This tight control over data storage then empowers users to make explicit
decisions as to who has access to their data. For example, imagine the
Redact-enabled website in question is a portal used by healthcare providers to
share test results and track prescriptions. A user may want that data to be
shared with all treating medical professionals, and would want to make sure they
all have access to the most up-to-date set of data. With Redact, all of a user's
data can be stored in their personal Redact-storage while still being accessible
on the health portal. Not only can a user see their own data, they can also
grant access to health professionals or institutions so they can view the data
in a Redact-enabled portal as well. Furthermore, it's all updated in one place,
so everyone always gets the same copy.

In order to identify different users to each other and secure this data storage,
Redact is paired with a strong authentication and authorization framework. It
employes a public-key infrastructure and assigns keypairs to individual users,
and certificate authorities to organizations. At the lowest level, users can
authenticate themselves anonymously to any Redact service using mutual-TLS
requests with a self-signed Redact-client certificate. Depending on the
authorization requirements of the request, this request could be accepted, or it
could be denied. By augmenting the certificate with metadata and having it be
signed by a certificate authority recognized by the server, any arbitrary
authorization check can be performed to further approve or deny the request. The
management of certificates and secret keys is entirely handled by Redact and
eliminates the need for passwords or user-initiated login procedures.

When put together, the components of Redact represent a method for storing and
handling user data that fundamentally changes the model for data ownership that
has existed since the beginning of the internet.  The expectation has always
been for users to generate data that is then stored and managed by the website
owner. This creates numerous liabilities for both the website owner and user as
this data is valuable and prone to theft. In the Redact model, user-generated
data remains owned by the user but organized into a coherent interface by the
website. The result is the ability to create rich user interfaces and
applications without having to implement time-consuming and expensive data
protection and authentication systems.
