.. _api_stats:

.. index:: Statistics
	seealso: Statistics; News

Statistics
===========
To get some statistics (eg. number of public sensors, active sensors, new sensors, etc...):

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`stats`
	* - **Privacy**
	  - Public
	* - **Format**
	  - not applicable
	* - **Method**
	  - GET
	* - **Returns**
	  - Appropriate HTTP status code; OK 200 - if successful
	  
|

.. admonition:: example

	.. parsed-literal::
	
		curl ":wotkit-api:`stats`"


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