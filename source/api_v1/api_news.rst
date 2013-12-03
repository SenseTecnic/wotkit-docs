.. _api_news:

.. index:: News
	seealso: News; Statistics

News
======

To get "news" (a list of interesting recent things that happened in the system):

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`news`
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
	
		curl ":wotkit-api:`news`"


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


