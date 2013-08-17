.. _api_sensor_fields:

Sensor Fields
==============

.. index:: Default Sensor Fields

Each sensor has the following default fields:

.. list-table::
	:widths: 15, 50
	:header-rows: 1
	
	* - Field Name
	  - Information	
	* - value
	  - The numerical data for the sensor. Required.
	* - lat
	  - The latitude of the sensor. Required.
	* - lng
	  - The longitude of the sensor. Required.
	* - message
	  - The string message for the sensor. Not Required.

|

.. index:: Sensor Sub-Fields

Each pieces of sensor field data has the following sub-fields: 

.. list-table::
	:widths: 15, 50
	:header-rows: 1
	
	* - Sub-Field Name
	  - Information	
	* - name
	  - The unique identifier for the field. It is required when creating/updating a field and cannot be changed.
	* - longName
	  - The display name for the field.
	* - type
	  - Can be "NUMBER" or "STRING". It is required when creating/updating a field. 
	* - required
	  - Is a boolean field. If true, data sent to a sensor must include this field or an error will result. Optional.
	* - units
	  - Is a string. Optional.
	* - index
	  - The numerical identifier of the field. Automatically populated.
	* - value
	  - The last value sent to the field. Automatically populated.
	* - lastUpdate
	  - The time stamp of the last value sent to the field. Automatically populated.

|

.. _get-sensor-fields-label:

.. index:: Sensor Fields Query

Querying Sensor Fields
------------------------
To query all sensor fields for a specific sensor:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors/{sensorname}/fields`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - Appropriate HTTP status code; OK 200 - if successful

|

To query a single sensor field for a specific sensor:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors/{sensorname}/fields/{fieldName}`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - Appropriate HTTP status code; OK 200 - if successful

|

.. _update-sensor-field-label:

.. index:: Sensor Field Update

Updating a Sensor Field
------------------------

You can update an existing sensor field or add a new sensor field by performing a PUT and including the field name in
the URL. The field information is supplied in a JSON format. 

If the sensor already has a field with the given "fieldname", it will be updated with new information. Otherwise, a new
field will be created. 

* When inputting field data, the sub-fields "name" and "type" are required-both for adding a new field or updating an existing one.
* The "name" sub-field of an existing field cannot be updated. 
* For user defined fields, the "longName", "type", "required", and "units" sub-fields may be updated. 
* For the default fields (lat, lng, value, message), only the "longName" and "unit" sub-fields may be updated. 

To update/add a sensor field:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors/{sensorname}/fields/{fieldname}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - PUT
	* - **Returns**
	  - HTTP status code; No Content 204 if successful

|

For instance, to create a new field called "test-field": 

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} --request POST 
		--header "Content-Type: application/json" --data-binary @field-data.txt 
		':wotkit-api:`sensors/test-sensor/fields/test-field`'

The file *field-data.txt* could contain the following.  (This is the minimal information needed to create a new field.)

.. code-block:: python

	{
		"name"=>"test-field",
		"type"=>"STRING"
	} 

To then update "test-field" sub-fields, the same curl command would be used, and ''field-data.txt'' could now contain
the following.

.. code-block:: python

	{
		"name"=>"test-field",
		"type"=>"NUMBER"
		"longName"=>"Test Field",
		"required"=>true,
		"units"=>"mm"
	}	

.. _delete-sensor-field-label:

.. index:: Sensor Field Deletion

Deleting a Sensor Field
-------------------------
You can delete an existing sensor field by performing a DELETE and including the field name in the URL. 

None of the existing default fields (lat, lng, value, message) can be deleted. 

To delete a sensor field:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors/{sensorname}/fields/{fieldname}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - n/a
	* - **Method**
	  - DELETE
	* - **Returns**
	  - HTTP status code; No Content 204 if successful

|