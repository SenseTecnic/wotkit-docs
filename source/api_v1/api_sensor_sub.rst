.. _api_sensor_subs:


.. _sensor-subscriptions-label:

Sensor Subscriptions
=====================

Sensor subscriptions are handled using the ``/subscribe`` URL.

.. _get-sub-label:

.. index:: Sensor Subscriptions

Get Sensor Subscriptions
----------------------

To view sensors that the current user is subscribed to:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`subscribe`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - Appropriate HTTP status code; OK 200 - if successful. A JSON object containing sensors subscribed by the user will be returned in the body of the response.

|


.. index:: Subscribe to a Sensor

.. _sensor-subscribe-label:

Subscribe
---------
To subscribe to a non-private sensor or private sensor owned by the current user:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`subscribe/{sensorname}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - PUT
	* - **Returns**
	  - HTTP status code; No Content 204 if successful.

|


.. index:: Unsubscribe from a Sensor
	pair: Subscribe to a Sensor; Unsubscribe from a Sensor

.. _sensor-unsubscribe-label:

Unsubscribe
-----------
To unsubscribe from a sensor:

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api-v1:`subscribe/{sensorname}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - DELETE
	* - **Returns**
	  - HTTP status code; No Content 204 if successful

|
