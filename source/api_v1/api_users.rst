.. _api_users:

.. NOTE: This Section has been removed from the public Docs, as it's only relevant for admins.

.. _users-label:

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
	  - :wotkit-api-v1:`users?{query}`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - **200 OK** on success. A JSON object in the response body containing a list of users matching the query.
	  
|

.. _view_user-label:

Viewing a Single User
----------------------

To view a single user, query by username or id as follows:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`users/{username}`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - **200 OK** if successful. A JSON object in the response body describing a user.
	  
|

.. admonition:: example

	.. parsed-literal::
		
		curl --user {id}:{password} 
		":wotkit-api-v1:`users/1`"


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


.. index:: User Creation

.. _create_user-label:

Creating/Registering a User
----------------------------

To register a user, you POST a user resource to the url `/users`. The user resource
is a JSON object with the following fields:

.. list-table::
	:widths: 10, 15, 50
	:header-rows: 1

	* - 
	  - Field Name
	  - Information	
	* - (*REQUIRED*)
	  - username 
	  - The username of the user. Must be at least 4 characters long.
	* - (*REQUIRED*)
	  - firstname 
	  - First name of the user. Can be updated.
	* - (*REQUIRED*)
	  - lastname 
	  - Last name of the user. Can be updated.
	* - (*REQUIRED*)
	  - email
	  - Email of the user. Can be updated.
	* - (*REQUIRED*)
	  - password 
	  - Password of the user. Can be updated.
        * - (*REQUIRED*)
	  - timeZone 
	  - A timezone must be provided, for example ``UTC``. Can be updated.
|

To create a user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`users`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - json
	* - **Method**
	  - POST
	* - **Returns**
	  - **201 Created** if successful; **400 Bad Request** if user is invalid; **409 Conflict** if user with the same username already exists.
	  
|


.. index:: User Updating

.. _update_user-label:

Updating a User
-----------------

* You may only update the following fields: "firstname", "lastname", "email", "timeZone" and "password".
* Only fields that will be present in the JSON object will be updated. The rest will remain unchanged.  

To update a user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`users/{username}`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - json
	* - **Method**
	  - PUT
	* - **Returns**
	  - **204 No Content** if successful.
	  
|


.. index:: User Deletion

.. _delete_user-label:

Deleting a User
----------------

Deleting a user is done by deleting the user resource.

To delete a user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`users/{username}`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - not applicable
	* - **Method**
	  - DELETE
	* - **Returns**
	  - **204 No Content** if successful.
	  
|
