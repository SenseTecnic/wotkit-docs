.. _api_sensors:


.. index:: Sensor Fields

.. _sensors-label:

Sensors
===========

A sensor represents a physical or virtual sensor or actuator.  It contains a data stream made up of *fields*. 

A sensor has the following attributes:

.. list-table::
	:widths: 15, 50
	:header-rows: 1

	* - Name
	  - Value Description
	* - id
	  - the numeric id of the sensor.  This may be used in the API in place of the sensor name.
	* - name **
	  - the name of the sensor.  
		| Note that the global name is ``{username}.{sensorname}``.  
		| When logged in as a the owner, you can refer to the sensor using only ``{sensorname}``. 
		| To access a public sensor created by another user, you can refer to it by its numeric id or the global name, ``{username}.{sensorname}``.

	* - description **
	  - a description of the sensor for text searches.
	* - longName **
	  - longer display name of the sensor.
	* - `url`
	  - **DEPRECATED**
	* - latitude
	  - the latitude location of the sensor in degrees.
		This is a static location used for locating sensors on a map and for location-based queries.
		(Dynamic location (e.g. for mobile sensors) is in the *lat* and *lng* fields of sensor data.)
	* - longitude
	  - the longitude location of the sensor in degrees.
		This is a static location used for locating sensors on a map and for location-based queries.
		(Dynamic location (e.g. for mobile sensors) is in the *lat* and *lng* fields of sensor data.)
	* - lastUpdate
	  - last update time in milliseconds.
		This is the last time sensor data was recorded, or an actuator script polled for control messages.
	* - visibility
	  - | **PUBLIC**: The sensor is publicly visible
	    | **ORGANIZATION**: The sensor is visible to everyone in the same organization as the sensor
	    | **PRIVATE**: The sensor is only visible to the owner. In any case posting *data* to the sensor is restricted to the sensor's owner.
	* - owner
	  - the owner of the sensor
	* - fields
	  - the expected data fields, their type (number or string), units and if available, last update time and value. (For more info: :ref:`sensor-fields-label` )
	* - tags 
	  - the list of tags for the sensor (For more info: :ref:`tags-label`)
	* - data
	  - sensor data (not shown yet)

** Required when creating a new sensor.

|


.. index:: Sensors

.. _query-sensor-label:

Querying Sensors
----------------
A list of matching sensors may also be queried by the system.  

The current query parameters are as follows:

.. list-table::
	:widths: 15, 50
	:header-rows: 1

	* - Name
	  - Value Description
	* - scope
	  - | **all** - all sensors the current user has access to
	    | **subscribed** - the sensors the user has subscribed to
		| **contributed** - the sensors the user has contributed to the system.
	* - tags
	  - list of comma separated tags
	* - orgs
	  - list of comma separated organization names
	* - :strikethrough:`private`
	  - **DEPRECATED**, use **visibility** instead. (*true* - private sensors only; *false* - public only`).
	* - visibility
	  - filter by the visibility of the sensors, either of **public**, **organization**, or **private**
	* - text
	  - text to search for in the name, long name and description
	* - active
	  - when true it returns sensors that have been updated in the last 15 minutes; when false it returns sensors that have *not* been updated in the last 15 minutes.
	* - offset
	  - offset into list of sensors for paging
	* - limit
	  - limit to show for paging.  The maximum number of sensors to display is 1000.
	* - location
	  - geo coordinates for a bounding box to search within. 
		| Format is **yy.yyy,xx.xxx:yy.yyy,xx.xxx**, and the order of the coordinates are North,West:South,East. 
		| Example: **location=56.89,-114.55:17.43,-106.219**

|

.. note:: If active is ommited the query will not evaluate if a sensor has, or has not, been updated in the last 15 minutes.

To query for sensors, add query parameters after the sensors URL as follows:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`sensors?{query}`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - **200 OK** if successful. A JSON object in the response body containing a list of sensor descriptions matching the query.

|

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} 
		":wotkit-api-v1:`sensors?tags=canada`"

Output:

.. code-block:: python

	[
	 {
	  "id": 71,
	  "name": "api-data-test",
	  "longName": "api-data-test",
	  "description": "api-data-test",
	  "tags": [
	    "canada",
	    "data",
	    "winnipeg"
	  ],
	  "latitude": 0,
	  "longitude": 0,
	  "visibility": "PUBLIC",
	  "owner": "sensetecnic",
	  "lastUpdate": "2013-03-09T03:12:35.438Z",
	  "created": "2013-07-01T23:17:37.000Z",
	  "subscriberNames": [],
	  "fields": [
	    {
	      "name": "lat",
	      "longName": "latitude",
	      "type": "NUMBER",
	      "index": 0,
	      "required": false,
	      "value": 0
 	    },
	    {
	      "name": "lng",
	      "longName": "longitude",
	      "type": "NUMBER",
	      "index": 1,
	      "required": false,
	      "value": 0
	    },
	    {
	      "name": "value",
	      "longName": "Data",
	      "type": "NUMBER",
	      "index": 2,
	      "required": true,
	      "value": 5,
	      "lastUpdate": "2013-03-09T03:12:35.438Z"
	    },
	    {
	      "name": "message",
	      "longName": "Message",
	      "type": "STRING",
	      "index": 3,
	      "required": false,
	      "value": "hello",
	      "lastUpdate": "2013-03-09T03:12:35.438Z"
 	    }
	  ],
	  "publisher": "sensetecnic",
	  "thingType": "SENSOR"
	 }
	]


.. _view-sensor-label:
	
Viewing a Single Sensor
-----------------------
To view a single sensor, query the sensor by sensor name or id as follows:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`sensors/{sensorname}`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - **200 OK** if successful. A JSON object in the response body describing a sensor.
	  
|

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password}
		":wotkit-api-v1:`sensors/sensetecnic.mule1`"

Output:

.. code-block:: python

	{
	  "id": 1,
	  "name": "mule1",
	  "longName": "Yellow Taxi 2",
	  "description": "A big yellow taxi that travels from Vincent's house to UBC and then back.",
	  "tags": [
	    "gps",
	    "taxi"
	  ],
	  "imageUrl": "",
	  "latitude": 51.06038631669101,
	  "longitude": -114.087524414062,
	  "visibility": "PUBLIC",
	  "owner": "sensetecnic",
	  "lastUpdate": "2014-06-19T22:45:36.556Z",
	  "created": "2013-07-01T23:17:37.000Z",
	  "subscriberNames": [
	    "mike",
	    "fred",
	    "nhong",
	    "smith",
	    "roseyr",
	    "mitsuba",
	    "rymndhng",
	    "lchyuen",
	    "test",
	    "lesula"
	  ],
	  "metadata": {},
	  "fields": [
	    {
	      "name": "lat",
	      "longName": "latitude",
	      "type": "NUMBER",
	      "index": 0,
	      "units": "degrees",
	      "required": false,
	      "value": 49.22288,
	      "lastUpdate": "2014-04-28T16:20:23.891Z"
	    },
	    {
	      "name": "lng",
	      "longName": "longitude",
	      "type": "NUMBER",
	      "index": 1,
	      "units": "degrees",
	      "required": false,
	      "value": -123.16246,
	      "lastUpdate": "2014-04-28T16:20:23.891Z"
	    },
	    {
	      "name": "value",
	      "longName": "Speed",
	      "type": "NUMBER",
	      "index": 2,
	      "units": "km/h",
	      "required": true,
	      "value": 10,
	      "lastUpdate": "2014-06-19T22:45:36.281Z"
	    },
	    {
	      "name": "message",
	      "longName": "Message",
	      "type": "STRING",
	      "index": 3,
	      "required": false
	    }
	  ],
	  "publisher": "sensetecnic",
	  "thingType": "SENSOR"
	}

.. index:: Sensor Registration

.. _create-sensor-label:

Creating/Registering a Sensor
------------------------------

The sensor resource is a JSON object. To register a sensor, you POST a sensor resource to the url ``/sensors``.

To create a sensor the API end-point is:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`sensors`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - POST
	* - **Returns**
	  -  **201 Created** if successful; **400 Bad Request** if sensor is invalid; **409 Conflict** if sensor with the same name already exists.

The JSON object has the following fields: 

.. list-table::
	:widths: 25, 15, 50
	:header-rows: 1
	
	* - 
	  - Field Name
	  - Information	
	* - (*REQUIRED*)
	  - name 
	  - The unique name for the sensor field. It is required when creating/updating/deleting a field and cannot be changed. The sensor name must be at least 4 characters long, contain only lowercase letters, numbers, dashes and underscores, and can start with a lowercase letter or an underscore only.
	* - (*REQUIRED*)
	  - longName 
	  - The display name for the field. It is required when creating/updating/deleting a field and can be changed.
	* - (*OPTIONAL*)
	  - latitude 
	  - The GPS latitude position of the sensor, it will default to 0 if not provided.
	* - (*OPTIONAL*)
	  - longitude 
	  - The GPS longitude position of the sensor, it will default to 0 if not provided.
	* - (*OPTIONAL*)
	  - visibility 
	  - It will default to "PUBLIC" if not provided. If visibility is set to ORGANIZATION, a valid "organization" must be provided.
	* - (*OPTIONAL*)
	  - tags 
	  - A list of tags for the sensor (For more info: :ref:`tags-label`)
	* - (*SEMI-OPTIONAL*)
	  - organization 
	  - If a visibility key is set an organization is required
	* - (*OPTIONAL*)
	  - fields 
	  - A fields object in the format ``{"name":"test-field","type":"STRING"}`` (For more info: :ref:`sensor-fields-label`)	

| 

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} --request POST --header "Content-Type: application/json" 
		--data-binary @test-sensor.txt ':wotkit-api-v1:`sensors`'


For this example, the file *test-sensor.txt* contains the following.

.. code-block:: python

	{
		"visibility":"PUBLIC",
		"name":"taxi-cab",
		"longName":"taxi-cab"
		"description":"A big yellow taxi.",
		"longName":"Big Yellow Taxi",
		"latitude":51.060386316691,
		"longitude":-114.087524414062
	}



.. index:: Multiple Sensor Registration
	pair: Sensor Registration; Multiple Sensor Registration

.. _create-multiple-sensors-label:
	
Creating/Registering multiple Sensors
--------------------------------------
To register multiple sensors, you PUT a list of sensor resources to the url ``/sensors``.

* The sensor resources is a JSON list of objects as described in :ref:`create-sensor-label`.
* Limited to 100 new sensors per call. (subject to change)

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`sensors`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - PUT
	* - **Returns**
	  - **201 Created** if successful; **400 Bad Request** if sensor is invalid; **409 Conflict** if sensor with the same name already exists ; **201 Created** and a JSON object in the response body describing a dictionary where the keys are the sensor names and the values are true/false depending on whether creating the sensor succeeded.


.. index:: Update Sensors

.. _update-sensor-label:

Updating a Sensor
-----------------
Updating a sensor is the same as registering a new sensor other than PUT is used and the sensor name or id is included in the URL.

Note that all top level fields supplied will be updated.

* You may update any fields except "id", "name" and "owner".
* Only fields that are present in the JSON object will be updated.
* If "visibility" is set to ORGANIZATION, a valid "organization" must be supplied.
* If "tags" list or "fields" list are included, they will replace the existing lists.
* If "visibility" is hardened (that is, the access to the sensor becomes more restrictive) then all currently subscribed users are automatically unsubscribed, regardless of whether they can access the sensor after the change.

To update a sensor owned by the current user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`sensors/{sensorname}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - PUT
	* - **Returns**
	  - **204 No Content** if successful.

|

For instance, to update a sensor description and add tags:

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} --request PUT 
		--header "Content-Type: application/json"
		--data-binary @update-sensor.txt
		':wotkit-api-v1:`sensors/taxi-cab`'

The file *update-sensor.txt* would contain the following:

.. code-block:: python

	{
	   "visibility":"PUBLIC",
	   "name":"taxi-cab",
	   "description":"A big yellow taxi. Updated description",
	   "longName":"Big Yellow Taxi",
	   "latitude":51.060386316691,
	   "longitude":-114.087524414062,
	   "tags": ["big", "yellow", "taxi"]
	}


.. index:: Delete Sensor

.. _delete-sensor-label:

Deleting a Sensor
------------------
Deleting a sensor is done by deleting the sensor resource through a DELETE request.

To delete a sensor owned by the current user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`sensors/{sensorname}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - not applicable
	* - **Method**
	  - DELETE
	* - **Returns**
	  - **204 No Content** if successful.

|

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} --request DELETE 
		':wotkit-api-v1:`sensors/test-sensor`'
