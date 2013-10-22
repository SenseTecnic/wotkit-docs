.. _api_smartstreets:

.. index::
	single: SmartStreets

.. _api-smartstreets-label:

Smart Streets Authentication
============================	

The WoTKit API for Smart Streets supports basic authentication using user name and password, WoTKit keys, as well as a developer key.  Note that Smart Streets does not support OAuth2.

Authenticating using Smart Streets Developer Keys
-------------------------------------------------
The developer key is assigned when a new user account is created. Developer keys are unique to each user. To obtain your developer key, please login to the SmartStreets Hub and go to your profile page. The developer key is located in the user profile under the field "API Key".

To authenticate with Smart Streets Developer Keys when sending private API requests, please supply the HTTP headers with either of the following formats:

.. list-table::
	:widths: 15, 50
	:header-rows: 1

	* - Header Name
	  - Header Value
	* - x-api-key
	  - api_key
	* - Authorization
	  - base64(api_key:"")



For example, the following curl command uses 'x-api-key' as header name and the developer key 'api_key' to get a list of user subscribed sensors.

(Please replace the ``{api_key}`` in the code with appropriate developer key copied from the SmartStreets UI.)


.. admonition:: example

	.. parsed-literal::

		curl --header "x-api-key:{api_key}"
		":wotkit-api:`sensors/scope=subscribed`"


Similar to the above example, the following curl command uses 'Authorization' as header name and the developer key in base64 format to retrieve a list of user subscribed sensors.

(Please replace the ``{base64_api_key}`` in the code with appropriate developer key encoded in base64 format.)


.. admonition:: example

	.. parsed-literal::

		curl --header "Authorization:{base64_api_key}"
		":wotkit-api:`sensors/scope=subscribed`"