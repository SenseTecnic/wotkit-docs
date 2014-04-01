.. _api_users:

Users
=======

Admins can list, create and delete users from the system.

.. _get_users:

.. index:: User Queries

List/Query Users
------------------

A list of matching user may be queried by the system. The optional query parameters are as follows:

.. list-table::
	:widths: 10, 50
	:header-rows: 1
	
	* - Name
	  - Value Description
	* - text
	  - text to search for in the username, first name and/or last name
	* - reverse
	  - **true** to get the oldest users first; **false** (default) to get newest first 
	* - offset
	  - offset into list of users for paging
	* - limit
	  - limit to show for paging. The maximum number of users to display is 1000.
  
|

To query for users, add query parameters after the sensors URL as follows:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`users?{query}`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - On error, an appropriate HTTP status code; On success, OK 200 and a list of users matching the query.
	  
|

.. _get_user:

Viewing a Single User
----------------------

To view a single user, query by username or id as follows:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`users/{username}`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - Appropriate HTTP status code; OK 200 - if successful
	  
|

.. admonition:: example

	.. parsed-literal::
		
		curl --user {id}:{password} 
		":wotkit-api:`users/1`"


Output:

.. code-block:: python

	{
		'id': 1,
		'username': 'sensetecnic',
		'email': 'info@sensetecnic.com',
		'firstname': 'Sense',
		'lastname': 'Tecnic',
		'enabled': True,
		'accountNonExpired': True,
		'accountNonLocked': True,
		'credentialsNonExpired': True
	}


.. _create_user:

.. index:: User Creation

Creating/Registering a User
----------------------------

To register a user, you POST a user resource to the url `/users`.

* The user resources is a JSON object.
* The "username", "firstname", "lastname", "email", and "password" fields are required when creating a user.
* The "timeZone" field is optional and defaults to UTC.
* The username must be at least 4 characters long.

To create a user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`users`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - json
	* - **Method**
	  - POST
	* - **Returns**
	  - HTTP status code; Created 201 if successful; Bad Request 400 if user is invalid; Conflict 409 if user with the same username already exists
	  
|

.. _update_user:

.. index:: User Updating

Updating a User
-----------------

* You may only update the following fields: "firstname", "lastname", "email", "timeZone" and "password".
* Only fields that will be present in the JSON object will be updated. The rest will remain unchanged.  

To update a user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`users/{username}`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - json
	* - **Method**
	  - PUT
	* - **Returns**
	  - HTTP status code; No Content 204 if successful
	  
|

.. _delete_user:

.. index:: User Deletion

Deleting a User
----------------

Deleting a user is done by deleting the user resource.

To delete a user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`users/{username}`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - not applicable
	* - **Method**
	  - DELETE
	* - **Returns**
	  - HTTP status code; No Response 204 if successful
	  
|