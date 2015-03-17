.. _api_alerts:

.. index:: Alerts
	seealso: News; Statistics

.. _alerts-label:

Alerts
======

An alert is set up by an user for notification purpose. Multiple conditions can be attached to an alert. Each condition is associated with a sensor field. An alert fires and sends a message to the owner's inbox and email (if email functionality is enabled) when all of its attached conditions are satisfied. Currently, each user is limited to have a maximum of 20 alerts. 

An alert has the following attributes:

.. list-table::
	:widths: 15, 50
	:header-rows: 1

	* - Name
	  - Value Description
	* - id
	  - the numeric id of the alert. It is automatically assigned when alert is created. 
	* - name **
	  - the name of the alert.  
	* - longName
	  - longer display name of the alert.
	* - description **
	  - a description of the alert.
	* - owner
	  - the user that creates the alert. The value of this field is automatically assigned as a user creates an alert. 
	* - disabled
	  - the on/off state of the alert. 
	  	| - If 'disabled' is 'true', the alert is switched off; it switches on if otherwise. 
	* - inProgress
	  - whether conditions are still true after an alert has fired. 
		| - inProgress is 'true' if all alert conditions remain true after an alert has fired. It becomes 'false' when any condition turns false. An alert gets fired when its inProgress state changes from false to true. 
	* - template **
	  - The message template that is sent to the inbox when alert is fired. 
	* - sendEmail **
	  - A boolean to enable/disable send email functionaity. 
	* - conditions
	  - the list of alert conditions 

** Required when creating a new alert.

An alert condition is composed of a sensor field, an operator for evaluation, and a value. It has the following attributes:

.. list-table::
	:widths: 15, 50
	:header-rows: 1

	* - Name
	  - Value Description
	* - sensorId
	  - the ID of the sensor associated with the condition
	* - field
	  - the field name to be compared of the chosen sensor
	* - operator
	  - the conditon operator, its value can be one of following:
	  	| 'LT': Less Than
	  	| 'LE': Less Than Or Equal To
	  	| 'GT': Greater Than
	  	| 'GE': Greater Than Or Equal To
	  	| 'EQ': Equal	
	  	| 'NEQ': Not Equal 
	  	| 'NULL': Is Null
	  	| 'NOTNULL': Is Not Null

	* - value
	  - value that the operator compares with


.. index:: Alerts Query

.. _get_alerts-label:

Listing Alerts of an User
-------------------------

To view a list of "alerts" created by an user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v2:`alerts`
	* - **Privacy**
	  - Private
	* - **Format**
	  - JSON
	* - **Method**
	  - GET
	* - **Returns**
	  - Appropriate HTTP status code; OK 200 - if successful
	  
|

.. admonition:: example

	.. parsed-literal::
	
		curl --user {id}:{password} ":wotkit-api-v2:`alerts`"



Sample Output:

.. code-block:: python

	[{
        "id": 6,
        "owner": "crysng",
        "name": "temperature-alert",
        "longName": "Temperature Alert",
        "description": "This alert notifies user when Hydrogen Sulfide content and Wind speed is too high at Burnaby Burmount. ",
        "disabled": false,
        "inProgress": false,
        "template": "Hydrogen Sulfide and wind speed is high!",
        "sendEmail": true,
        "email": "rottencherries@hotmail.com",
        "conditions": [
            {
                "sensorId": 241,
                "field": "h2s",
                "operator": "GT",
                "value": 10
            },
            {
                "sensorId": 241,
                "field": "wspd",
                "operator": "GE",
                "value": 50
            }
        ]
    },
    {
        "id": 5,
        "owner": "crysng",
        "name": "test",
        "longName": "Moisture Sensor Alert",
        "description": "This alert fires when moisture level is too low. ",
        "disabled": false,
        "inProgress": false,
        "template": "Moisture level is too low, water the plant now!",
        "sendEmail": true,
        "email": "someone@email.com",
        "conditions": [
            {
                "sensorId": 504,
                "field": "value",
                "operator": "LT",
                "value": 3
            }
        ]
    }]


.. index:: Alerts Query by ID

.. _get_alerts_by_id-label:

Viewing an Alert
----------------
To view an alert, query the alert by its id as followed:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v2:`alerts/{alert id}`
	* - **Privacy**
	  - Private
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
		":wotkit-api-v2:`alerts/5`"

Output:

.. code-block:: python

	{
        "id": 5,
        "owner": "crysng",
        "name": "test",
        "longName": "Moisture Sensor Alert",
        "description": "This alert fires when moisture level is too low. ",
        "disabled": false,
        "inProgress": false,
        "template": "Moisture level is too low, water the plant now!",
        "sendEmail": true,
        "email": "someone@email.com",
        "conditions": [
            {
                "sensorId": 504,
                "field": "value",
                "operator": "LT",
                "value": 3
            }
        ]
    }


.. index:: Create Alert

.. _create_alert-label:

Creating Alerts
---------------
To create an alert, you POST an alert resource to the url ``/v2/alerts``.

* The alert resource is a JSON object.
* The "name", "description", "template", and "sendEmail" fields are required when creating an alert.
* The alert name must be at least 4 characters long, contain only lowercase letters, numbers, dashes and underscores, and can start with a lowercase letter or an underscore only.

To create an alert:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v2:`alerts`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - POST
	* - **Returns**
	  - HTTP status code; Created 201 if successful; Bad Request 400 if sensor is invalid; Conflict 409 if alert with the same name already exists

.. admonition:: example1

	.. parsed-literal::

		curl --user {id}:{password} --request POST --header "Content-Type: application/json" 
		--data-binary @test-alert.txt ':wotkit-api-v2:`alerts`'


For this example, the file *test-alert.txt* contains the following.  This is the minimal information needed to create an alert.

.. code-block:: python

	{
		"name":"test alert",
		"description":"A test alert.",
		"template":"Template for test alert",
		"sendEmail":false
	}

.. admonition:: example2

	
		Now, let's create an alert with additional information and conditions. The file *test-alert.txt* contains the following.

.. code-block:: python

	{
		"name": "test alert 2",
		"longName": "Test Alert 2",
		"description": "This is test 2. ",
		"disabled": false,
		"template": "The alert test 2 has fired!! ",
		"sendEmail": true,
		"email": "someone@email.com",
		"conditions": [
		{
			"sensorId": 504,
			"field": "value",
			"operator": "LT",
			"value": 3
		},
		{
			"sensorId": 24,
			"field": "data",
			"operator": "NOTNULL"
		}
		]
	}


.. index:: Update Alert

.. _update_alert-label:

Updating Alerts
---------------
Updating an alert is the same as creating a new alert other than PUT is used and the alert id is included in the URL.

Note that all top level fields supplied will be updated.

* You may update any fields except "id", and "owner".
* Only fields that are present in the JSON object will be updated.

To update an alert owned by the current user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v2:`v2/alerts/{alert id}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - PUT
	* - **Returns**
	  - HTTP status code; No Content 204 if successful

|

For instance, to update an alert:

.. admonition:: example

	.. parsed-literal::

		curl --user {id}:{password} --request PUT --header "Content-Type: application/json" 
		--data-binary @update-alert.txt ':wotkit-api-v2:`alerts/{alert id}`'


The file *update-alert.txt* would contain the following:

.. code-block:: python

	{
		"longName": "New Alert Name",
		"description":"Updated Description"
	}



.. index:: Delete Alert

.. _delete_alert-label:

Deleting Alerts
---------------
Deleting an alert is done by deleting the alert resource.

To delete an alert owned by the current user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v2:`alerts/{alert id}`
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
		':wotkit-api-v2:`alerts/{alert id}`'

