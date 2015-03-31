.. _api_authentication:


.. index:: single: Authentication

.. _authentication-label:

Authentication
==============	

The WoTKit API supports three forms of authentication to control access to a user's
sensors and other information on the WoTKit.

1) Basic authentication using the user's name and password
2) Basic authentication with *Keys* (key id and key password)
3) OAuth2 authorization of server-based *Applications*

Using the WoTKit portal, developers can create *keys* for use by one or more sensor gateways
or scripts.  Users can also register new server side applications and then authorize
these applications to allow them to access a user's sensors on their behalf.

.. only:: smartstreets

	Note that in Smart Streets, OAuth2 authorization is not supported. Instead, authentication using the Smart Streets developer key is included.
	See :ref:`api-smartstreets-label` for more information.

.. note::

	Most examples in this document use basic authentication with keys or WoTKit username and passwords. However, OAuth2 authorization is also possible by 
	removing the id and password and by appending an access_token parameter. 
	See :ref:`apps-oauth-label` for details.


.. index:: API Permissions, Methods Privacy

.. _methods-privacy-label:
	
Methods privacy
----------------
.. TODO public methods described here - confusing

Some API methods are private and will return an HTTP status code of 403 Forbidden if accessed without authenticating the
request, while others are completely private or are restricted to certain users.
(Currently only system administrators have access to ALL methods),

Every method has a description of its private level in one of the following forms:

* **Public** accessible to all
* **Private** accessible only to authenticated users
* **Public or Private** accessible to all, but might return different results to authenticated users. 

	* Example of different results is the "get sensors" method, which might return a user's private sensors when the method is called as an authenticated user.
	
* **Admin** accessible only to authenticated admin users


.. index:: Keys

.. _keys-basic-auth-label:

Keys and Basic Authentication
------------------------------
*Keys* are created on the WoTKit UI (:wotkit:`keys`) and are unique to each user. 

To grant a client access to your sensors, you can create a *key*. The client can then be supplied the auto-generated
'key id' and 'key password'. These will act as username and password credentials, using basic authentication to access
sensors on the user's behalf.

For instance, the following curl command uses a 'key id' and 'key password' to get information about the sensor **sensetecnic.mule1**.  

.. note::

	Replace the ``{key_id}`` and ``{key_password}`` in the code below with your own generated keys. To generate them visit the WoTKit UI at :wotkit:`keys`, click on `New API Key`, after filling form with *Key Name* and *Key Description* to track your
	keys you will be presented with the values you can use.

.. admonition:: example

	.. parsed-literal::

		curl --user {key_id}:{key_password}
		":wotkit-api-v1:`sensors/sensetecnic.mule1`"

This returns:

.. code-block:: json
	
	{
		"name":"mule1",
		"fields":[
		{"name":"lat","value":49.20532,"type":"NUMBER","index":0,
		 "required":true,"longName":"latitude","lastUpdate":"2012-12-07T01:47:18.639Z"},
		{"name":"lng","value":-123.1404,"type":"NUMBER","index":1,
		 "required":true,"longName":"longitude","lastUpdate":"2012-12-07T01:47:18.639Z"},
		{"name":"value","value":58.0,"type":"NUMBER","index":2,
		 "required":true,"longName":"Data","lastUpdate":"2012-12-07T01:47:18.639Z"},
		{"name":"message","type":"STRING","index":3,
		 "required":false,"longName":"Message"}
			],
		"id":1,
		"visibility":PUBLIC,
		"owner":"sensetecnic",
		"description":"A big yellow taxi that travels from 
		               Vincent's house to UBC and then back.",
		"longName":"Big Yellow Taxi",
		"latitude":51.060386316691,
		"longitude":-114.087524414062,
		"lastUpdate":"2012-12-07T01:47:18.639Z"}
	}


.. index:: Applications
	single: OAuth2

.. _apps-oauth-label:

Registered Applications and OAuth2
------------------------------------

*Applications* registered with the WoTKit UI (:wotkit:`apps`) provide an easy way to allow several clients access to a WoTKit's user data. A common scenario is when a developer creates an application that publishes data *on 
behalf* of other WoTKit users.

For example, to grant a third-party client access to your sensors, you first register an *application*. The client can then be supplied the 'application client id' and auto-generated 'application secret'. These will act as credentials, allowing clients to access the WoTKit on your behalf, using OAuth2 authorization. You can always delete the application
and revoke access to any clients using the generated oauth credentials.

In more detail, an OAuth2 authorization will ask the user's permission for a client to utilize the application credentials on the user's behalf. If the user allows this, an access token is generated. This access token can then be appended to the end of each WoTKit URL. In this case no further id/passwords are needed. 

For instance, the following curl command uses an access token to get information about the sensor **sensetecnic.mule1**. 

.. note::
	Replace the ``{access_token}`` the request below with your own generated access token as explained below

.. admonition:: example

	.. parsed-literal::

		curl ":wotkit-api-v1:`sensors/sensetecnic.mule1?access_token={access_token}`"


In order to obtain an access token a client must follow the following steps, which
follow the oauth2 specification (http://oauth.net/2/).

	1) An attempt to access the WoTKit is made by providing an 'application client id' and requesting a code. This can be obtained  

		``http://wotkit.sensetecnic.com/api/oauth/authorize?client_id={application client id}
	&response_type=code&redirect_uri={redirect_uri}``
	
	2) If no user is currently logged in to the WoTKit, a login page will be presented. A WoTKit user must log in to continue. 
	3) A prompt asks the user to authorize the 'application client id' to act on their behalf. Once authorized, a code is provided. 
	4) The user is redirected to a *redirect_uri* that obtains an access token that can be appended to the end of each URL to perform queries on behalf of the user.

.. Note:: 
	An application's *Client ID* and *Application Secret* can be found at after you have created an application in the WoTKit UI:
	:wotkit:`apps/`{application-id}`

The following example in PHP exemplifies the flow explained above. The example below
is deployed at a *{redirect_uri}* that is pointed to by the WoTKit after the request 
in (1) above is made.

.. code-block:: php

	<?php
		$code = $_GET['code'];
		$access_token = "none";
		$ch = curl_init();
		
		if(isset($code)) {
			// try to get an access token
			$params = array("code" => $code,
				"client_id"=> {application client id},
				"client_secret" => {application secret},
				"redirect_uri" => {redirect uri},
				"grant_type" => "authorization_code");			
			$data = ArraytoNameValuePairs ($params);
				
			curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
			curl_setopt($ch, CURLOPT_URL, "http://wotkit.sensetecnic.com/api/oauth/token");
			curl_setopt($ch, CURLOPT_POST, TRUE);
			curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
			
			$access_token = json_decode($response)->access_token;	
		}	
	?>


.. index:: Access Token

.. _access-token-label:		
		
Access Token Facts
------------------
When obtaining an access token, the 'response' field holds the  access token required
by an application to make future requests on behalf of a user: 

* ``response->access_token``
* ``response->expires_in``

.. Note:: 	

	The default value of response->expires_in is approx. 43200 seconds (or 12 hrs)


.. index:: SmartStreets

.. _api-smartstreets-label:

Smart Streets Authentication
----------------------------

The WoTKit API for Smart Streets supports basic authentication using user name and password, WoTKit keys, as well as a developer key.  Note that Smart Streets does not support OAuth2.

..   Authenticating using Smart Streets Developer Keys
..   -------------------------------------------------
..   More on this to come





