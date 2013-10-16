.. _api_sensor_data:

.. index:: Sensor Data

Sensor Data
==============

In the WoTKit, *sensor data* consists of a timestamp followed by one or more named fields. There are a number of
reserved fields supported by the WoTKit:

.. list-table::
	:widths: 15, 50
	:header-rows: 1
	
	* - Name
	  - Value Description
	* - timestamp
	  - the time that the sensor data was collected.  This is a long integer representing the number of milliseconds from Jan 1, 1970 UTC.  
		Optional; if not supplied, a server-supplied timestamp will be used.
	* - sensor_id
	  - the globally unique sensor id that produced the data.
	* - sensor_name
	  - the globally unique sensor name, in the form ``{username}.{sensorname}``
	* - lat
	  - the current latitude location of the sensor in degrees (number).  Needed for map visualizations.
	* - lng
	  - the current longitude location of the sensor in degrees (number).  Needed for map visualizations.
	* - value
	  - the primary value of the sensor data collected (number).  Needed for most visualizations.
	* - message
	  - a text message, for example a twitter message (text).  Needed for text/newsfeed visualizations.

|

In addition to these reserved fields, additional fields can be added by updating the *sensor fields* in the WoTKit UI
or :ref:`sensor_fields` in the API.

.. note:: \* Python's ``time.time()`` function generates the system time in *seconds*, not milliseconds.  
	
	To convert this to an integer in milliseconds use ``int(time.time()*1000)``. 
	Using Java: ``System.currentTime()``.

.. _send-data-label:	

.. index:: Sensor Data Creation
	
Sending New Data
-----------------

To send new data to a sensor, POST name value pairs corresponding to the data fields
to the ``/sensors/{sensorname}/data`` URL.

There is no need to provide a timestamp since it will be assigned by the server.  Data posted to the system
will be processed in real time.

To send new data:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors/{sensorname}/data`
	* - **Privacy**
	  - Private
	* - **Format**
	  - not applicable
	* - **Method**
	  - POST
	* - **Returns**
	  - HTTP status code; No Response 201 (Created) if successful

|

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} --request POST 
		-d value=5 -d lng=6 -d lat=7 ':wotkit-api:`sensors/test-sensor/data`'

|
	
.. _send-bulk-data-label:	

.. index:: Bulk Sensor Data
	pair: Sensor Data Creation; Bulk Sensor Data

Sending Bulk Data
------------------

To send a range of data, you PUT data (rather than POST) data into the system.
Note that data PUT into the WoTKit will not be processed in real time, since it occurred in the past

* The data sent must contain a list of JSON objects containing a timestamp and a value.
* If providing a single piece of data, existing data with the provided timestamp will be deleted and replaced. Otherwise, the new data will be added.
* If providing a range of data, the list must be ordered from earliest to most recent timestamp. Any existing data within this timestamp range will be deleted and replaced by the new data.

To update data:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors/{sensorname}/data`
	* - **Privacy**
	  - Private
	* - **Format**
	  - JSON
	* - **Method**
	  - PUT
	* - **Returns**
	  - HTTP status code; No Response 204 if successful
	  
|

Example of valid data:
 
.. code-block:: python

	[{"timestamp":"2012-12-12T03:34:28.626Z","value":67.0,"lng":-123.1404,"lat":49.20532},
	{"timestamp":"2012-12-12T03:34:28.665Z","value":63.0,"lng":-123.14054,"lat":49.20554},
	{"timestamp":"2012-12-12T03:34:31.621Z","value":52.0,"lng":-123.14063,"lat":49.20559},
	{"timestamp":"2012-12-12T03:34:35.121Z","value":68.0,"lng":-123.14057,"lat":49.20716},
	{"timestamp":"2012-12-12T03:34:38.625Z","value":51.0,"lng":-123.14049,"lat":49.20757},
	{"timestamp":"2012-12-12T03:34:42.126Z","value":55.0,"lng":-123.14044,"lat":49.20854},
	{"timestamp":"2012-12-12T03:34:45.621Z","value":56.0,"lng":-123.14215,"lat":49.20855},
	{"timestamp":"2012-12-12T03:34:49.122Z","value":55.0,"lng":-123.14727,"lat":49.20862},
	{"timestamp":"2012-12-12T03:34:52.619Z","value":59.0,"lng":-123.14765,"lat":49.20868}]

|

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} --request PUT --data-binary @data.txt 
		':wotkit-api:`sensors/test-sensor/data`'

where *data.txt* contains JSON data similar to the above JSON array.

.. _delete-data-label:	

.. index:: Sensor Data Deletion

Deleting Data
--------------

Currently you can only delete data by timestamp, where timestamp is in numeric or ISO form. 
Note that if more than one sensor data point has the same timestamp, they all will be deleted.

To delete data:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors/{sensorname}/data/{timestamp}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - not applicable
	* - **Method**
	  - DELETE
	* - **Returns**
	  - HTTP status code; No Response 204 if successful
	  
|


.. _raw-data-label:	

.. index:: Raw Sensor Data, Sensor Data Retrieval
	seealso: Sensor Data Retrieval; Formatted Sensor Data

Raw Data Retrieval
----------------------
To retrieve raw data use the following:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors/{sensor-name}/data?{query-params}`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - On success, OK 200 with a list of timestamped data records.

|

Where the data {query-params} are defined below.

The data returned will look something like the following:

.. code-block:: python

	[{"id":43485114,"timestamp":"2013-10-09T23:37:45.055Z","sensor_id":1,"sensor_name":"sensetecnic.mule1","value":58.0,"lng":-123.2223,"lat":49.24024},
	{"id":43485118,"timestamp":"2013-10-09T23:37:48.560Z","sensor_id":1,"sensor_name":"sensetecnic.mule1","value":60.0,"lng":-123.22432,"lat":49.24073},
	{"id":43485121,"timestamp":"2013-10-09T23:37:52.056Z","sensor_id":1,"sensor_name":"sensetecnic.mule1","value":69.0,"lng":-123.22651,"lat":49.24143},
	{"id":43485124,"timestamp":"2013-10-09T23:37:55.555Z","sensor_id":1,"sensor_name":"sensetecnic.mule1","value":67.0,"lng":-123.22798,"lat":49.24202},
	{"id":43485128,"timestamp":"2013-10-09T23:37:59.055Z","sensor_id":1,"sensor_name":"sensetecnic.mule1","value":61.0,"lng":-123.2292,"lat":49.24256},
	{"id":43485131,"timestamp":"2013-10-09T23:38:02.555Z","sensor_id":1,"sensor_name":"sensetecnic.mule1","value":55.0,"lng":-123.23022,"lat":49.2431},
	{"id":43485139,"timestamp":"2013-10-09T23:38:06.055Z","sensor_id":1,"sensor_name":"sensetecnic.mule1","value":69.0,"lng":-123.23496,"lat":49.24577},
	{"id":43485143,"timestamp":"2013-10-09T23:38:09.556Z","sensor_id":1,"sensor_name":"sensetecnic.mule1","value":69.0,"lng":-123.23718,"lat":49.24682},
	{"id":43485147,"timestamp":"2013-10-09T23:38:13.056Z","sensor_id":1,"sensor_name":"sensetecnic.mule1","value":51.0,"lng":-123.23851,"lat":49.24752},
	{"id":43485150,"timestamp":"2013-10-09T23:38:16.556Z","sensor_id":1,"sensor_name":"sensetecnic.mule1","value":63.0,"lng":-123.24513,"lat":49.25118}]

|

The query parameters supported are the following:

.. list-table::
	:widths: 15, 50
	:header-rows: 1

	* - Name
	  - Value Description
	* - start
	  - specifies the absolute start time of a range of data selected in milliseconds.  When start is not specified, it is set to the current time.  It may only be used in combination with another range parameter: end, after, afterE, before, and beforeE.
	* - startE
	  - specifies the id of the specific data item to start with.  This is needed to disambiguate data items in a sensor stream with the same timestamp.  This parameter may only be used with beforeE and afterE currently.
	* - end
	  - Specify the absolute end time in milliseconds of a range of data after a specified start time.  The end parameter MUST be greater than the start time (start).
	* - after
	  - specifies a relative time after the start time, e.g. after=300000 would be 5 minutes after the start time. A start time (start) MUST also be specified since there will be no data after the current time.
	* - afterE
	  - specifies the number of elements after the start element or time. The start time (start) or start item (startE)
	  MUST also be provided.)
	* - before
	  - specifies a relative time before the start time.  E.g. data from the last hour would be before=3600000. (If not provided, start time defaults to current time.)
	* - beforeE
	  - specifies number of data items before the start time.  E.g. to get the last 1000 items, use beforeE=1000 (If not provided, start time defaults to current time.)
	* - reverse
	  - **true**: order the data from newest to oldest; **false** (default):order from oldest to newest

|

.. note:: These queries looks for timestamps > "start" and timestamps <= "end"


.. _formatted-data-label:

.. index:: Formatted Sensor Data	
	seealso: Formatted Sensor Data; Sensor Data Retrieval

Formatted Data Retrieval
---------------------------

To retrieve data in a format that can be consumed directly by Google Visualizations, we support an additional
resource for retrieving data called the *dataTable*.

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors/{sensor-name}/dataTable?{query-params}`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - On success, OK 200 with a list of timestamped data records.
	  
|

In addition to the above query parameters, the following parameters are also supported:

.. list-table::
	:widths: 5, 50
	:header-rows: 1
	
	* -
	  -
	* - tqx
	  - A set of colon-delimited key/value pairs for standard parameters, `defined here <http://code.google.com/apis/visualization/documentation/dev/implementing_data_source.html>`_.
	* - tq
	  - A SQL clause to select and process data fields to return, `explained here <http://code.google.com/apis/visualization/documentation/querylanguage.html>`_.
	
|

.. note:: When using tq sql queries, they must be url encoded. When using tqx name/value pairs, the reqId parameter is necessary.

|

For instance, the following would take the "test-sensor", select all data where value was greater than 20, and display
the output as an html table. 

.. admonition:: example

	.. parsed-literal::
	
		curl --user {id}:{password} :wotkit-api:`sensors/test-sensor/
		dataTable?tq=select%20*%20where%20value%3E20&reqId=1&out=html`

|
	
.. _aggregated-data-label:	

.. index:: Aggregated Sensor Data	
	seealso: Aggregated Sensor Data; Sensor Data	

Aggregated Data Retrieval
--------------------------
Aggregated data retrieval allows one to receive data from multiple sensors, queried using the same parameters as when
searching for sensors or sensor data.
The following parameters may be added to the ``/data`` url:

* scope
* tags
* :strikethrough:`private` (deprecated, use visibility instead)
* visibility
* text
* active

* start
* end
* after
* afterE
* before
* beforeE

* orderBy
	* **sensor**: which groups data by sensor_id 
	* **time** (default): which orders data by timestamp, regardless of the sensor it comes from.

To receive data from more that one sensor, use the following:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`data?{query-param}={query-value}&{param}={value}...`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - On success, OK 200 with a list of timestamped data records.
	  
|

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} 
		":wotkit-api:`data?subscribed=all&beforeE=20&orderBy=sensor`"
