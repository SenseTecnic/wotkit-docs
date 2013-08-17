.. _api_orgs:

Organizations
===============

All users can see all organizations, and admins can manipulate them.

.. _get_orgs:

.. index:: Organization Query

List/Query Organizations
------------------------

A list of matching organizations may be queried by the system. The optional query parameters are as follows:

.. list-table::
	:widths: 10, 50
	:header-rows: 1
	
	* - Name
	  - Value Description
	* - text
	  - text to search for in the name, long name and/or description
	* - offset
	  - offset into list of organizations for paging
	* - limit
	  - limit to show for paging. The maximum number of organizations to display is 1000.
  
|

To query for organizations, add query parameters after the sensors URL as follows:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`orgs?{query}`
	* - **Privacy**
	  - Public
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - On error, an appropriate HTTP status code; On success, OK 200 and a list of organizations matching the query from newest to oldest.
	  
|

.. _get_org:

Viewing a Single Organization
-----------------------------

To view a single organization, query by name:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`orgs/{org-name}`
	* - **Privacy**
	  - Public
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - Appropriate HTTP status code; OK 200 - if successful
	  
|

.. admonition:: example

	.. parsed-literal::

		curl ":wotkit-api:`orgs/electric-inc`"


Output:

.. code-block:: python

	{
		"id": 4764,
		"name": "electric-inc",
		"longName": "Electric, Inc.",
		"description": "Electric, Inc. was established in 1970.",
		"imageUrl": "http://www.example.com/electric-inc-logo.png"
	}


.. _create_org:

.. index:: Organization Creation
	
Creating/Registering an Organization
------------------------------------

To register a new organization, you POST an organization resource to the url `/org`.

* The organization resources is a JSON object.
* The "name" and "longName" fields are **required** and must both be at least 4 characters long.
* The "imageUrl" and "description" fields are **optional**.

To create an organization:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`orgs`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - json
	* - **Method**
	  - POST
	* - **Returns**
	  - HTTP status code; Created 201 if successful; Bad Request 400 if organization is invalid; Conflict 409 if an organization with the same name already exists

|

.. _update_org:

.. index:: Organization Updating

Updating an Organization
------------------------

* You may update any fields except "id" and "name".
* Only fields that are present in the JSON object will be updated.

To update an organization:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`orgs/{org-name}`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - json
	* - **Method**
	  - PUT
	* - **Returns**
	  - HTTP status code; No Content 204 if successful

|

.. delete_org:

.. index:: Organization Deletion

Deleting an Organization
------------------------

Deleting an organization is done by deleting the organization resource.

To delete a user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`orgs/{org-name}`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - not applicable
	* - **Method**
	  - DELETE
	* - **Returns**
	  - HTTP status code; No Content 204 if successful

|

.. _org_memebers:

Organization Membership
---------------------

.. _get_org_members:

.. index:: Organization Members

List all members of an Organization
####################################

To query for organization members:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`orgs/{org-name}/members`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - not applicable
	* - **Method**
	  - GET
	* - **Returns**
	  - On error, an appropriate HTTP status code; On success, OK 200 and a list of organization members.
	  
|

.. _create_org_members:

.. index:: Organization Member Creation

Add new members to an Organization
###################################

To add new members to an organization, post a JSON array of usernames:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`orgs/{org-name}/members`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - json
	* - **Method**
	  - POST
	* - **Returns**
	  - On error, an appropriate HTTP status code; On success, OK 204.
	  
|

Usernames that are already members, or usernames that do not exist, will be ignored.

For instance, to add the users "abe", "beth", "cecilia" and "dylan" to the organization "electric-inc":

.. admonition:: example

	.. parsed-literal::
	
		curl --user {id}:{password} --request POST 
		--header "Content-Type: application/json" --data-binary @users-list.txt 
		':wotkit-api:`orgs/electric-inc/members`'


The file *users-list.txt* would contain the following.

.. code-block:: python
	
	["abe", "beth", "cecilia", "dylan"]

.. _remove_org_member: 

.. index:: Organization Member Removal

Remove members from an Organization
###################################

To remove members from an organization, DELETE a JSON array of usernames:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`orgs/{org-name}/members`
	* - **Privacy**
	  - Admin
	* - **Format**
	  - json
	* - **Method**
	  - DELETE
	* - **Returns**
	  - On error, an appropriate HTTP status code; On success, OK 204.
	  
|

Usernames that are not members, or usernames that do not exist, will be ignored.