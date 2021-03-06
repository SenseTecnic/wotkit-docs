.. _api_sensor_fields:


.. _sensor-fields-label:

Sensor Fields
==============

Sensor fields are the fields of data saved in a sensor stream.  Together they 
make up the sensor schema. Sensor data objects must follow declared fields.

.. index:: Default Sensor Fields

Each sensor has the following default fields:

.. list-table::
	:widths: 10, 15, 50
	:header-rows: 1
	
	* - 
	  - Field Name
	  - Information	
	* - (*OPTIONAL*)
	  - value 
	  - The numerical data for the sensor.
	* - (*OPTIONAL*)
	  - lat 
	  - The latitude of the sensor.
	* - (*OPTIONAL*)
	  - lng 
	  - The longitude of the sensor.
	* - (*OPTIONAL*)
	  - message 
	  - The string message for the sensor.
|

.. index:: Sensor Sub-Fields

Additional fields can be added. Each new field consists of the following: 

.. list-table::
	:widths: 15, 50
	:header-rows: 1
	
	* - Field Name
	  - Information	
	* - name (*REQUIRED*)
	  - The unique name for the sensor field. Required when creating/updating/deleting a field and cannot be changed.
	* - longName (*REQUIRED*)
	  - The display name for the field. Required when creating/updating/deleting a field and can be changed.
	* - type (*REQUIRED*)
	  - Can be "NUMBER" or "STRING". Required when creating/updating a field. 
	* - required (*OPTIONAL*)
	  - Is a boolean field. If true, data sent to a sensor must include this field or an error will result.
	* - units (*OPTIONAL*)
	  - A string to identify the units to represent data.
	* - index (*READ-ONLY*)
	  - The numerical index of the field used to maintain ordering.  This field is automatically generated by the system and is read only.
	* - value (*READ-ONLY*)
	  - The last value of this sensor field received by the sensor when sending data.  This is a read only field set when the sensor receives data for this field.
	* - lastUpdate (*READ-ONLY*)
	  - The time stamp of the last value sent to the field. This is a read only field set when the sensor receives data for this field.

|


.. index:: Sensor Fields Query

.. _get-sensor-fields-label:

Querying Sensor Fields
------------------------
To retrieve the sensor fields for a specific sensor:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`sensors/{sensorname}/fields`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - **200 OK** if successful. A JSON object in the response body containing the fields of the sensor is returned in the body of the response.

|

To query a single sensor field for a specific sensor:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`sensors/{sensorname}/fields/{fieldName}`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - **200 OK** if successful. A JSON object in the response body describing the field is returned in the body of the response.

|


.. index:: Sensor Field Update

.. _update-sensor-field-label:

Updating a Sensor Field
------------------------

You can update or add a sensor field by performing a PUT operation to the specified field.  The field information is supplied in a JSON format. 

If the sensor already has a field with the given name, it will be updated with new information. Otherwise, a new
field with that name will be created. 

**Notes:**

* When inputting field data, the sub-fields "name" and "type" are required-both for adding a new field or updating an existing one.
* Read only sub-fields such as index, value and lastUpdate should not be supplied.
* The "name" sub-field of an existing field cannot be updated. 
* For user defined fields, the "longName", "type", "required", and "units" sub-fields may be updated. 
* You cannot change the index of a field. If a field is deleted, the index of the following fields will be adjusted to maintain the field order.

To update/add a sensor field:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`sensors/{sensorname}/fields/{fieldname}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - PUT
	* - **Returns**
	  - **204 No Content** if successful.

|

For instance, to create a new field called "test-field": 

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} --request PUT 
		--header "Content-Type: application/json" --data-binary @field-data.txt 
		':wotkit-api-v1:`sensors/test-sensor/fields/test-field`'

The file *field-data.txt* could contain the following.  (Note that this is the minimal information needed to create a new field.)

.. code-block:: python

	{
		"name":"test-field",
		"type":"STRING"
	} 

To then update "test-field" sub-fields, the curl command would be used to send a PUT request.

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} --request PUT
		--header "Content-Type: application/json" --data-binary @field-data.txt 
		':wotkit-api-v1:`sensors/test-sensor/fields/test-field`'


And ''field-data.txt'' could now contain the following.

.. code-block:: python

	{
		"name":"test-field",
		"type":"NUMBER",
		"longName":"Test Field",
		"required":true,
		"units":"mm"
	}	


.. index:: Sensor Field Deletion

.. _delete-sensor-field-label:

Deleting a Sensor Field
-------------------------
You can delete an existing sensor field by performing a DELETE and including the field name in the URL. 

To delete a sensor field:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`sensors/{sensorname}/fields/{fieldname}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - n/a
	* - **Method**
	  - DELETE
	* - **Returns**
	  - **204 No Content** if successful.

|
