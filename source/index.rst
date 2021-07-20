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

Redact is a set of applications that allows developers to build end-to-end
encrypted websites and users to use these websites. When leveraging Redact,
website owners are able to display secure user data on their webpages without
being able to know or read that data back to themselves. Securing websites in
this way is beneficial to both the user and the website owner as it protects
from data leaks, allows complete control and knowledge over who has acces to
data, and standardizes secure authentication mechanisms that don't rely on
passwords.

.. note:: The two applications that enable this functionality are
   `redact-client`_ and `redact-store`_.
   
   The client application runs on the user's device and handles requests from
   the browser for secure data display. The storage application is a server that
   fronts some backing database and provides a stable API for CRUD operations on
   encrypted data.

   The last important repository is `redact-crypto`_, which is a library, not an
   application, and provides all important cryptographic and storage
   abstractions.

   .. _redact-client: https://github.com/pauwels-labs/redact-client
   .. _redact-store: https://github.com/pauwels-labs/redact-store
   .. _redact-crypto: https://github.com/pauwels-labs/redact-crypto

Redact protects from data leaks as it removes the website's need to store data
entirely. In a traditional website, the website owner will maintain a database
which allows them to store relevant information that their users submit. When a
user visits the website, this information is fetched from the database and
displayed. In a Redact-enabled website, the information stored in the website's
database is only a reference to data. For example, in the traditional website,
there might be a database field called "name" with the value "Alice Doe". In the
Redact-enabled website, the database field would still be called "name" but its
value would be something like ".profile.name.". This value is picked up by the
Redact application installed on the user's device, and is translated into the
actual data in a way that makes it impossible for the website owner to read it
back to themselves. With no data to steal, the website owner no longer has to
worry about the liability of data leaks, and users can rest assured in knowing
that the potential attack surface for their data is greatly reduced to a single,
higly-secure, encrypted location.

This tight control over data storage then empowers users to make explicit
decisions as to who has access to their data. For example, imagine the
Redact-enabled website in question is a portal used by healthcare providers to
share test results, schedule appointments, refill prescriptions, or message your
doctor.  You would want that data to be shared with all treating medical
professionals, and you'd want to make sure they all have access to the same, and
latest, set of data. With Redact, all of a user's data is stored in one place,
so all requests for data will be seen and either approved or denied by the user.
Furthermore, it's all updated in one place, so everyone always gets the same
copy.

In order to identify different users to each other and secure this data storage,
Redact has to be paired with a strong authentication and authorization
framework. It achieves this by employing a public-key infrastructure, assigning
keypairs to individual users and certificate authorities to organizations. At
the lowest level, users can authenticate themselves anonymously to any Redact
service using mutual-TLS requests with a self-signed client
certificate. Depending on the authorization requirements of the request, this
request could be accepted, or it could be denied. By augmenting the certificate
with metadata and having it be signed by a certificate authority recognized by
the server, any arbitrary authorization check can be performed to further
approve or deny the request. The management of certificates and secret keys is
entirely handled by the Redact application and eliminates the need for passwords
or user-initiated login procedures.

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
