.. _api_sensors:

.. index:: Sensor Fields

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
	* - thingType
	  - the entity type, this is set to "SENSOR" on sensor creation 
	* - description **
	  - a description of the sensor for text searches.
	* - longName **
	  - longer display name of the sensor.
	* - :strikethrough:`url`
	  - deprecated
	* - imageUrl
	  - the url of the sensor image 
	* - latitude
	  - the latitude location of the sensor in degrees.
		This is a static location used for locating sensors on a map and for location-based queries.
		(Dynamic location (e.g. for mobile sensors) is in the *lat* and *lng* fields of sensor data.)
	* - longitude
	  - the longitude location of the sensor in degrees.
		This is a static location used for locating sensors on a map and for location-based queries.
		(Dynamic location (e.g. for mobile sensors) is in the *lat* and *lng* fields of sensor data.)
	* - lastUpdate
	  - last update time in ISO8601 UTC format. 
		This is the last time sensor data was recorded, or an actuator script polled for control messages.
	* - created
	  - the sensor creation datetime
	* - visibility
	  - | **PUBLIC**: The sensor is publicly visible
	    | **ORGANIZATION**: The sensor is visible to everyone in the same organization as the sensor
	    | **PRIVATE**: The sensor is only visible to the owner. In any case posting *data* to the sensor is restricted to the sensor's owner.
	* - owner
	  - the user id of the sensor owner
	* - organization
	  - the organization name that a sensor belongs to
	* - fields
	  - the expected data fields, their type (number or string), units and if available, last update time and value.
	* - tags
	  - the list of tags for the sensor
	* - subscriberNames
	  - the list of users that have subscribed to the sensor
	* - data
	  - sensor data (not shown yet)
	* - metadata
	  - the list of sensor metadata keys and values (keys in each sensor must be unique)
** Required when creating a new sensor.

|

.. _query-sensor-label:

.. index:: Sensors

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
	  - :strikethrough:`**true** - private sensors only; **false** - public only`
		deprecated, use **visibility** instead
	* - visibility
	  - filter by the visibility of the sensors, either of **public**, **organization**, or **private**
	* - text
	  - text to search for in the name, long name and description
	* - active
	  - when true, only returns sensors that have been updated in the last 15 minutes.
	* - offset
	  - offset into list of sensors for paging
	* - limit
	  - limit to show for paging.  The maximum number of sensors to display is 1000.
	* - location
	  - geo coordinates for a bounding box to search within. 
		| Format is **yy.yyy,xx.xxx:yy.yyy,xx.xxx**, and the order of the coordinates are North,West:South,East. 
		| Example: **location=56.89,-114.55:17.43,-106.219**

|

To query for sensors, add query parameters after the sensors URL as follows:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors?{query}`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - On error, an appropriate HTTP status code; On success, OK 204 and a list of sensor descriptions matching the query.

|

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} 
		":wotkit-api:`sensors?tags=canada`"

Output:

.. code-block:: python

	[
	    {
	        "id": 40,
	        "name": "api-data-test-1",
	        "longName": "api-data-test-1",
	        "description": "api-data-test-1 description",
	        "tags": ["canada","data","vancouver"],
	        "subscriberNames": ["mike"],
	        "visibility": "PUBLIC",
	        "created": "2013-06-27T00:00:00.000Z",
	        "latitude": 48.86471476180277,
	        "longitude": -122.958984375,
	        "thingType": "SENSOR",
	        "lastUpdate": "2013-06-27T00:00:00.000Z",
	        "fields": [
	            {"name": "lat", "longName": "latitude",
	                "type": "NUMBER","required": false,
	                "value": 0,"index": 0},
	            {"name": "lng","longName": "longitude",
	                "type": "NUMBER","required": false,
	                "value": 0, "index": 1},
	            {"name": "value","longName": "Data",
	                "type": "NUMBER","required": true,
	                "value": 0,"index": 2},
	            {"name": "message","longName": "Message",
	                "type": "STRING","required": false, "index": 3},
	            {"name": "new-field","longName": "",
	                "type": "STRING","units": "","required": false,"index": 4}],
	        "owner": "mike"
	    },
	    {
	        "id": 41,
	        "name": "api-data-test-2",
	        "longName": "api-data-test-2",
	        "description": "api-data-test-2 description",
	        "tags": ["canada","data","edmonton"],
	        "subscriberNames": [ "mike"],
	        "visibility": "PUBLIC",
	        "created": "2013-06-27T00:00:00.000Z",
	        "latitude": 53.225768435790194,
	        "longitude": -113.818359375,
	        "thingType": "SENSOR",
	        "lastUpdate": "2013-06-27T00:00:00.000Z",
	        "fields": [
	            {"name": "lat","longName": "latitude",
	                "type": "NUMBER","required": false,
	                "value": 0,"index": 0},
	            {"name": "lng","longName": "longitude",
	                "type": "NUMBER","required": false,
	                "value": 0,"index": 1},
	            {"name": "value","longName": "Data",
	                "type": "NUMBER","required": true,
	                "value": 0,"index": 2},
	            {"name": "message","longName": "Message",
	                "type": "STRING","required": false,"index": 3}],
	        "owner": "mike"
	    }
	]

.. _view-sensor-label:
	
Viewing a Single Sensor
-----------------------
To view a single sensor, query the sensor by sensor name or id as follows:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors/{sensorname or sensor id}`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - Appropriate HTTP status code; OK 200 - if successful
	  
|

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password}
		":wotkit-api:`sensors/sensetecnic.mule1`"

Output:

.. code-block:: python

	{
		"name":"mule1",
		"fields":[
			{"name":"lat","value":49.20532,"type":"NUMBER","index":0,
			 "required":true,"longName":"latitude",
			 "lastUpdate":"2012-12-07T01:47:18.639Z"},
			{"name":"lng","value":-123.1404,"type":"NUMBER","index":1,
			 "required":true,"longName":"longitude",
			 "lastUpdate":"2012-12-07T01:47:18.639Z"},
			{"name":"value","value":58.0,"type":"NUMBER","index":2,
			 "required":true,"longName":"Data",
			 "lastUpdate":"2012-12-07T01:47:18.639Z"},
			{"name":"message","type":"STRING","index":3,
			 "required":false,"longName":"Message"}
		],
		"id":1,
		"visibility":"PUBLIC",
		"owner":"sensetecnic",
		"description":"A big yellow taxi that travels 
		               from Vincent's house to UBC and then back.",
		"longName":"Big Yellow Taxi",
		"latitude":51.060386316691,
		"longitude":-114.087524414062,
		"lastUpdate":"2012-12-07T01:47:18.639Z"}
	}

.. _create-sensor-label:

.. index:: Sensor Registration

Creating/Registering a Sensor
------------------------------

To register a sensor, you POST a sensor resource to the url ``/sensors``.

* The sensor resources is a JSON object.
* The "name", "longName", and "description" fields are required when creating a sensor.
* The "latitude" and "longitude" fields are optional and will default to 0 if not provided. 
* The "visibility" field is optional and will default to "PUBLIC" if not provided.
* The "tags", "fields", "metadata", "imageUrl" and "organization" information are optional.
* If "visibility" is set to ORGANIZATION, a valid "organization" must be supplied.
* The sensor name must be at least 4 characters long, contain only lowercase letters, numbers, dashes and underscores, and can start with a lowercase letter or an underscore only.

To create a sensor:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - POST
	* - **Returns**
	  - HTTP status code; Created 201 if successful; Bad Request 400 if sensor is invalid; Conflict 409 if sensor with the same name already exists

|

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} --request POST --header "Content-Type: application/json" 
		--data-binary @test-sensor.txt ':wotkit-api:`sensors`'


For this example, the file *test-sensor.txt* contains the following.  This is the minimal information needed to
register a sensor resource.

.. code-block:: python

	{
		"visibility":"PUBLIC",
		"name":"taxi-cab",
		"description":"A big yellow taxi.",
		"longName":"Big Yellow Taxi",
		"latitude":51.060386316691,
		"longitude":-114.087524414062
	}

.. _create-multiple-sensors-label:

.. index:: Multiple Sensor Registration
	pair: Sensor Registration; Multiple Sensor Registration
	
Creating/Registering multiple Sensors
-------------------------------------
To register multiple sensors, you PUT a list of sensor resources to the url ``/sensors``.

* The sensor resources is a JSON list of objects as described in Creating/Registering a Sensor.
* Limited to 100 new sensors per call. (subject to change)

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - PUT
	* - **Returns**
	  - HTTP status code; Created 201 if successful; Bad Request 400 if sensor is invalid; Conflict 409 if sensor with the same name already exists ; On Created 201 or some errors (not all) you will receive a JSON dictionary where the keys are the sensor names and the values are true/false depending on whether creating the sensor succeeded. For Created 201 all values will be true.

.. _update-sensor-label:

.. index:: Update Sensors

Updating a Sensor
-----------------
Updating a sensor is the same as registering a new sensor other than PUT is used and the sensor name or id is included in the URL.

Note that all top level fields supplied will be updated.

* You may update any fields except "id", "name", "created" and "owner".
* Only fields that are present in the JSON object will be updated.
* If "visibility" is set to ORGANIZATION, a valid "organization" must be supplied.
* If "tags" list or "fields" list are included, they will replace the existing lists.
* If "visibility" is hardened (that is, the access to the sensor becomes more restrictive) then all currently subscribed users are automatically unsubscribed, regardless of whether they can access the sensor after the change.

To update a sensor owned by the current user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors/{sensorname}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - PUT
	* - **Returns**
	  - HTTP status code; No Content 204 if successful

|

For instance, to update a sensor description and add tags:

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} --request PUT --header "Content-Type: application/json" 
		--data-binary @update-sensor.txt ':wotkit-api:`sensors/taxi-cab`'


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

.. _delete-sensor-label:

.. index:: Delete Sensor

Deleting a Sensor
------------------
Deleting a sensor is done by deleting the sensor resource.

To delete a sensor owned by the current user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors/{sensor name or sensor id}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - not applicable
	* - **Method**
	  - DELETE
	* - **Returns**
	  - HTTP status code; No Response 204 if successful

|

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} --request DELETE 
		':wotkit-api:`sensors/test-sensor`'
