
.. index::
	single: Authentication

.. _api_authentication:

Authentication
==============	

The WoTKit API supports three forms of authentication to control access to a user's sensors
and other information on the WoTKit.

#. Basic authentication using the user's name and password
#. Basic authentication with *Keys* (key id and key password)
#. OAuth2 authorization of server-based *Applications*

Using the WoTKit portal, developers can create *keys* for use by one or more sensor gateways
or scripts.  Users can also register new server side applications and then authorize
these applications to allow them to access a user's sensors on their behalf.

.. only:: smartstreets

	Note that in Smart Streets, OAuth2 authorization is not supported. Instead, authentication using the Smart Streets developer key is included.
	See :ref:`api-smartstreets-label` for more information.

.. note::

	Most examples in this document use basic authentication with keys or WoTKit username and passwords. However,
	OAuth2 authorization is also possible by removing the id and password and by appending an access_token parameter.
	See :ref:`apps-oauth-label` for details.

.. _methods-privacy-label:

.. index:: API Permissions, Methods Privacy
	
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

.. _keys-basic-auth-label:

.. index:: Keys

Keys and Basic Authentication
------------------------------
*Keys* are created on the WoTKit UI (:wotkit:`keys`) and are unique to each user. 

To grant a client access to your sensors, you can create a *key*. The client can then be supplied the auto-generated
'key id' and 'key password'. These will act as username and password credentials, using basic authentication to access
sensors on the user's behalf.

For instance, the following curl command uses a 'key id' and 'key password' to get information about the sensor **sensetecnic.mule1**.  

(Please replace the ``{key_id}`` and ``{key_password}`` in the code with appropriate values copied from the WoTKit UI.)

.. admonition:: example

	.. parsed-literal::

		curl --user {key_id}:{key_password} ":wotkit-api:`sensors/sensetecnic.mule1`"


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

.. _apps-oauth-label:

.. index:: Applications
	single: OAuth2
	
Registered Applications and OAuth2
----------------------------------

The WoTKit supports the OAuth2 authorization framework as described in RFC 6749.

*Applications* are registered on the WoTKit UI (:wotkit:`apps`) by an application developer. Applications can be installed by many users, but the application credentials called the client_id and client_secret are unique to that application.

Once registered, the client application then uses its  'Client ID' and generated 'Application Secret' as credentials in the OAuth2 authorization process to request permission to access WoTKit resources on a user's behalf by generating an access token.  Using an access token, the application can them make API calls to the WoTKit on behalf of the user -- no further id/passwords are needed.

For example, the following curl command uses an access token to get information about the sensor **sensetecnic.mule1**. 

.. admonition:: example

	.. parsed-literal::

		curl ":wotkit-api:`sensors/sensetecnic.mule1?access_token={access_token}`"


The first step of OAuth 2 is to get authorization from the user. The WoTKit supports two grant types:

* **Authorization Code** for apps running on a web server
* **Password** for logging in with a username and password

Authorization Code Grant
----------------------------------------

In order to obtain an access token for a web server application using the Authorization Code grant, the following needs to be done:

#. Request an authorization code by providing the 'Client ID' as follows:

	``http://wotkit.sensetecnic.com/api/oauth/authorize?client_id={application client id}
	&response_type=code&redirect_uri={redirect uri}``
	
#. If the user has not previously logged into the WoTKit authorization server, a login page will be presented. A WoTKit user must first log in. 
#. Once logged in, a prompt will ask the user to authorize the 'application client id' to act on their behalf. Once authorized, an 'authorization code' is provided. 
#. Using the application credentials, this authorization code is exchanged for an access token used to access the WoTKit API.

Example: PHP file pointed to by ``{redirect_uri}``

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

Password Grant
--------------

The password grant type can be used to exchange the user name and password for an access token directly.  This is generally used by applications that are part of the WoTKit service, since they need to collect the user's name and password.

To use the password grant type, you simply POST the name and password along with the client id and secret directly, in response you will receive an access token.

.. admonition:: example

	.. parsed-literal::
			curl -d "grant_type=password&username={username}&password={password}&client_id={clientid}&client_secret={clientsecret}&scope=all" https://wotkit.sensetecnic.com/api/oauth/token

returns:

.. code-block:: javascript

	{
		"access_token":"{access_token}",
		"token_type":"bearer",
		"refresh_token":"{refresh_token}",
		"expires_in":43199,
		"scope":"all"
	}


.. _access-token-label:		

.. index:: Access Token
		
Access Token Facts
------------------
When obtaining an access token, the 'response' field holds the  following useful information:

* ``response->access_token``
* ``response->expires_in``

	* default value is approx. 43200 seconds (or 12 hrs)