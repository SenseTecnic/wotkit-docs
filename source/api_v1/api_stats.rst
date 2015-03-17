.. _api_stats:

.. index:: Statistics
	seealso: Statistics; News

.. _statistics-label:

Statistics
===========

The "stats" resource provides statistics, like number of public sensors, active sensors, or new sensors. It can be accessed via:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`stats`
	* - **Privacy**
	  - Public
	* - **Format**
	  - not applicable
	* - **Method**
	  - GET
	* - **Returns**
	  - **200 OK** if successful. A JSON object in the response body containing describing statistics.
	  
|

.. admonition:: example

	.. parsed-literal::
	
		curl ":wotkit-api-v1:`stats`"


Output:

.. code-block:: python

	{
		'total': 65437,
		'active': 43474,
		'new': {
			'day': 53,
			'week': 457,
			'month': 9123,
			'year': 40532
		}
	}
