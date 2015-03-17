.. _api_tags:

.. _tags-label:

Tags
=====

Sensors can specify several tags that can be used to organize them. You can get
a list of tags, either the most used by public sensors or by a particular 
sensor query.

.. _get_tags:

.. index:: Tags 
	seealso: Tags; Sensors

Querying Sensor Tags
---------------------

A list of matching tags. The query parameters are as follows:

.. list-table::
	:widths: 15, 50
	:header-rows: 1
	
	* - Name
	  - Value Description
	* - scope
	  - | **all**-all tags used by sensors that the current user has access to; 
		| **subscribed**-tags for sensors the user has subscribed to; 
		| **contributed**-tags for sensors the user has contributed to the system.
	* - `private` 
	  - **DEPRECATED**, use visibility instead. (true - private sensors only; false - public only)
	* - visibility
	  - filter by the visibility of the sensors, either of **public**, **organization** or **private**
	* - text
	  - text to search in the sensors's name, long name and description
	* - active
	  - when true, only returns tags for sensors that have been updated in the last 15 minutes.
	* - offset
	  - offset into list of tags for paging
	* - limit
	  - limit to show for paging. The maximum number of tags to display is 1000.
	* - location
	  - geo coordinates for a bounding box to search within. 
		Format is **yy.yyy,xx.xxx:yy.yyy,xx.xxx**, the order of the coordinates are North,West:South,East. 
		Example: **location=56.89,-114.55:17.43,-106.219**
	
|

To query for tags, add query parameters after the tags URL as follows:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`tags?{query}`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - **200 OK** on success. A JSON object in the response body containing a list of tag count objects matching the query.
	  
|

To query for all tags that contain the text *bicycles* use the URL:

.. admonition:: example

	.. parsed-literal::
	
		curl --user {id}:{password} 
		":wotkit-api-v1:`tags?text=bicycles`"


Output:

.. code-block:: python

	[
		{
			'name': 'bicycle',
			'count': 3,
			'lastUsed': 1370887340845
		},{
			'name': 'bike',
			'count': 3,
			'lastUsed': 1350687440754
		},{
			'name': 'montreal',
			'count': 1,
			'lastUsed': 1365857340341
		}
	]

The *lastUsed* field represents the creation date of the newest sensor that uses this tag.
