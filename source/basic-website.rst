======================
 Building a Website With Redact
======================

This section describes in detail how to build a simple website with "Redacted"
data. The Redact codebase is currently in alpha, and supports simple data types
such as strings and numbers. Sharing data between Redact users is under
development and planned for a future release, but is included in this document
because it is a crucial component not only for Redact, but also for
Redact-enabled websites. 

Static Web Page
---------------
Let's begin by building a web page that statically displays "Redacted" user
data. The website will be a contact page where a user can enter their name,
phone number, and social security number, all of which are stored in an
encrypted form by Redact. This is admittedly useless as a user can only view
their own contact info for now, but it serves as a good example before diving
into more advanced topics.

To begin, create an HTML document that contains an iframe pointing to the Redact client.::

     <iframe src="http://localhost:8080/data/.demoapp.name."></iframe>

Start the Redact-client and Redact-storage on your device and open the HTML
document in a browser. Examine the ``<iframe>`` HTML contents and you should see
that it has been populated by the client::
    
    <iframe src="http://localhost:8080/data/.demoapp.name.">    <!-- The <iframe> from the source HTML document -->
        <html>    <!-- The response from the Redact-client for the unauthenticated request -->
            ...
            <iframe id="data-frame" src="" title="secure"> 
                <html>    <!-- the response from the Redact-client for the authenticated request -->
                    ...
                    <p></p>
                    ...
            </iframe>
            ...
        </html>
    </iframe>

The data requested at the path ``.demoapp.name.`` does not yet exists, so let's
go ahead and create a form field so the user can add their name. Modify the
iframe ``src`` to retreive an editable form field for ``.demoapp.name.``::

     <iframe src="http://localhost:8080/data/.demoapp.name.?edit=true"></iframe>

``edit=true`` has been added as a query parameter to the Redact request, which
requests an editable field from the client. The form field is pre-populated with
the existing data at the given path, and the submit button triggers a secure
POST request to the client, which then updates the data in the Redact-storage.

The form field we have created is not visually appealing. It shows the iframes
as two large bordered boxes, but we can fix the appearance by passing in custom
CSS to the Redact request and styling the outer iframe.  Add a CSS stylesheet to
the HTML page and style the outer iframe to have no border::

    iframe {
        border: none;
        height: 66px;
        width: 500px;
    }

Next, add a CSS query parameter to the Redact request to instruct the Client to
apply the CSS to the response::

     <iframe src="http://localhost:8080/data/.demoapp.name.?edit=true&css=iframe{border:none;height:50px}"></iframe>

Now we can simply add a few more form fields to represent the user's phone number and
social security number, and add labels to the page so the user knows what each field represents.::

    <html>
        <body>
	        <p>Name:</p>
	        <iframe src="http://localhost:8080/data/.demoapp.name?edit=true&css=iframe{border:none;height:50px;}"></iframe>

	        <p>Phone Number:</p>
	        <iframe src="http://localhost:8080/data/.demoapp.phonenumber?edit=true&css=iframe{border:none;height:50px;}"></iframe>

	        <p>Social Security Number:</p>
	        <iframe src="http://localhost:8080/data/.demoapp.socialsecuritynumber?edit=true&css=iframe{border:none;height:50px;}"></iframe>
        </body>
    </html>

Now we have a simple HTML page which loads user data from the given paths, and
allows the user to edit and update this data on their Redact-storage.  To create
a non-editable, display only verison of the page, copy the contents of the
existing HTML page, and remove the ``edit=true`` query parameter from each
iframe.  Add a link or button which directs the user from the view-only page to
the editable page, and vice-versa.

Modern Web Application
----------------------
Modern web applications use javascript to respond to user actions and modify the
page, and a backend server which responds to HTTP requests for data retrieval
and updates.  Because Redact data is stored and operated on in a manner which is
opaque to the website it is displayed on, the flow of data must be modified to
provide a web application the information it needs on the frontend (javascript)
as well as on the backend (HTTP server).

Imagine a website that presents an `alert` to a user when they submit data on a
form. Normally, the submit button could have an event listener to do this.::

    <button onclick="alert("Form Submitted")>Submit</button>

If the submit button is within a Redact iframe, the web page does not have
access to the ``<button>`` element, and cannot add an event listener in this
manner. To solve this limitation, Redact uses JS messaging to securely inform a
parent web page that changes have been made to a Redact data field.

Now imagine a website maintains a list of entries made by the user. A form field
allows the user to create a new entry, which will be sent to the backend HTTP
server on submission.  The entry will then be added to a database, and will be
retrieved from the database whenever the user loads their list of entries. With
Redact, data entries cannot be directly sent to the HTTP server. They are
instead sent to the Redact-client, which encrypts them and stores them in the
Redact-storage.  In order to support backend server functionality Redact uses
"data relays".  Data relays instruct the Redact-client to securely send
information about a data entry's Redact path to an arbitrary HTTP server.


JS Messaging
~~~~~~~~~~~~
The JS Messaging features allows a Redacted form field to emit information to
the parent page when data within Redact is updated via the page. JS messaging
utilizes the `postMessage()`_ API.  Passing in a ``js_message`` query parameter
to a Redact-client request instructs an editable Redact field to send a message
to the parent page after data is successfully updated. The contents of the
message are simply the value of the ``js_message`` query parameter.

.. _postMessage(): https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage

.. note:: The ``js_message`` query paramter must be base64, then URL encoded.

To accomplish something with the same affect as the following HTML code within a
Redact iframe, the ``js_message`` query parameter must be used::

    <button onclick="alert("Form Submitted")>Submit</button>

Use the ``js_message`` query parameter when retrieving an editable field::

    <iframe src="http://localhost:8080/data/.demoapp.name?edit=true&js_message=c3VibWl0"></iframe>

The Redact-client response will contain a form which posts the message ``"submit"`` to the parent 
page when the submit button is clicked. Listen for the ``window:message`` `event`_ to trigger
the alert javascript::

    window.onmessage = (event) => {
        try {
            decodedMessage = atob(event.data);
            if (decodedMessage === 'submit') {
                alert("Form Submitted");
            }
        } catch (error) {
            // expected when the event.message is not a base64 encoded string
        }
    };

.. _event: https://developer.mozilla.org/en-US/docs/Web/API/Window/message_event

Data Relaying
~~~~~~~~~~~~~
Data relaying allows a Redact-enabled website to link an API endpoint to a
Redact data field such that the API endpoint receives a HTTP POST request
containing the path of the data when an edit is submitted. When the data at a
given path is created or updated, the client orchestrates a request to the
endpoint at the given relay URL. If the request succeeds, it is transparent to
the user.  If the request fails, an error is presented to the user to inform
them that, although the data in their Redact-storage was updated, the action was
not entirely successfully as the backing server did not acknowledge the relay.

To configure a Redact relay, add an endpoint to the backend HTTP server which
will receive POST requests with a JSON body representing the path of the data
that was updated.  A user's Redact-client will send an HTTP POST request with a
request body in the form::

    {
        "path": "<DATA PATH>"
    }

Next, add the relay_url query parameter to the Redact client request within an
iframe::

    <iframe src="http://localhost:8080/data/.demoapp.name?edit=true&relay_url=https%3A%2F%2Ffoo.bar%2Fredact%2Frelay"></iframe>

When this data is submitted, a POST request will be made to
``https://foo.bar/redact/relay`` with the JSON body::

    {
        "path": ".demoapp.name"
    }

Note how the request does not have any information identifying a user. Redact
users identify themselves using certificates, and relays are no different. The
recommended approach for differentiating between users is to establish a mutual
TLS connection with incoming relay requests, and use a attribute of the of the
certificate itself to represent a unique user ID.  This value should be constant
for a given user, meaning that they will have the same identifier regardless of
which device they use, assuming the device is linked to the same Redact
instance.

User Sessions
~~~~~~~~~~~~~ 
Data relays allow a backend server to identify which user is updating their data
on a page, but this is not very useful if a website cannot identifiy which user
is visiting the page and making non-relay requests to the server (for example,
to retrieve all Redact data entry paths that have been relayed for a given
user). The server needs an authenticated method by which to identify a user.
This is where user sessions come in handy.  They provide a way for a redact
enabled website to make HTTP requests on behalf of a user identified by their
certificate.

User sessions provide a JWT token for a website's frontend to be passed along
with HTTP requests to the backend server. The JWT tokens are generated and
signed by the website's own backend server upon establishing a mutual TLS
connection with the Redact-client.  This way, the server can validate that a
request coming from the UI is coming from the same user that established a
mutual TLS between their client and the server on the same device.

This approach to sessions with Redact utilizes the Redact-client's proxy endpoint.
The proxy endpoint accepts requests directly to the client (as opposed to all other 
requests which must be double buffered within iframes), and forwards the request
as a GET request to a given endpoint. This request is optionally performed with
mutual TLS, allowing the given endpoint to uniquely identify the user. The
response from the endpoint is then passed back as the response to the proxy
request. By responding to this request with a signed JWT token that contains the
information needed to identify a user (such as a hash of the user certificate),
the server can verify that subsequent requests with the JWT token are being made on
behalf of the same Redact user that is represented in the JWT payload.