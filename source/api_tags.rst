.. _api_tags:

Tags
=====

You can get a list of tags, either the most used by public sensors or by a sensor query.

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
	  - | **all**: all tags used by sensors that the current user has access to
	    | **subscribed**: tags for sensors the user has subscribed to
	    | **contributed**: tags for sensors the user has contributed to the system.
	* - :strikethrough:`private` 
	  - :strikethrough:`true - private sensors only; false - public only`
		(Deprecated, use visibility instead)
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

To query for tags, add query parameters after the sensors URL as follows:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`tags?{query}`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - On error, an appropriate HTTP status code; On success, OK 200 and a list of tag count objects matching the query.
	  
|

.. admonition:: example

	.. parsed-literal::
	
		curl --user {id}:{password} 
		":wotkit-api:`tags?text=vancouver`"


Output:

.. code-block:: python

	[
	    {
	        "name": "bc",
	        "count": 138,
	        "lastUsed": "2013-08-15T18:43:13.797Z"
	    },
	    {
	        "name": "air quality",
	        "count": 136,
	        "lastUsed": "2013-08-15T18:43:13.797Z"
	    },
	    {
	        "name": "vancouver",
	        "count": 2,
	        "lastUsed": "2013-07-23T21:50:18.357Z"
	    },
	    {
	        "name": "weather",
	        "count": 1,
	        "lastUsed": "2013-07-23T21:50:18.357Z"
	    }
	]

The *lastUsed* field represents the creation date of the newest sensor that uses this tag.