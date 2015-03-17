.. _api_news:


.. index:: News
	seealso: News; Statistics

.. _news-label:

News
======

The "news" resource provides a list of interesting and recent activities in the WoTKit.

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`news`
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
	
		curl ":wotkit-api-v1:`news`"


Output:

.. code-block:: python

	[{
		'timestamp': 1370910428123,
		'title': u'The sensor "Light Sensor" has updated data.',
		'url': u'/sensors/5/monitor'
	},{
		'timestamp': 1370910428855,
		'title': u'The sensor "api-data-test-1" has updated data.',
		'url': u'/sensors/40/monitor'
	}]


