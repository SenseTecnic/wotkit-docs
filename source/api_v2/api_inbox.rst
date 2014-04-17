.. _api_inbox:

.. index:: Inbox
	seealso: Alerts

Inbox
=====
The Inbox is the storage place for inbox messages that are sent by an alert firing event. 

An inbox message has the following attributes:

.. list-table::
	:widths: 15, 50
	:header-rows: 1

	* - Name
	  - Value Description
	* - id
	  - the numeric id of the message. It is automatically generated.
	* - timestamp
	  - the time that the message is sent to inbox.
	* - title
	  - title of the inbox message
	* - message
	  - the message content
	* - sendEmail
	  - the boolean variable of whether email functionality is enabled
	* - read
	  - the flag of whether the message is read 
	* - sent
	  - the flag of whether an email is sent
	
.. _get_inbox:

.. index:: Inbox Message Query

Listing Inbox Messages of an User
---------------------------------

To view a list of "inbox messages" of an user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`v2/inbox`
	* - **Privacy**
	  - Private
	* - **Format**
	  - JSON
	* - **Method**
	  - GET
	* - **Returns**
	  - Appropriate HTTP status code; OK 200 - if successful
	  
|

.. admonition:: example

	.. parsed-literal::
	
		curl --user {id}:{password} ":wotkit-api:`v2/inbox`"



Sample Inbox Messages Output:

.. code-block:: python

	[
		{
		"id": 5,
		"timestamp": "2014-04-17T00:51:41.701Z",
		"title": "Moisture Sensor Alert",
		"message": "Moisture level is too low, water the plant now!",
		"sendEmail": true,
		"email": "someone@email.com",
		"read": false,
		"sent": false
		}
	]
