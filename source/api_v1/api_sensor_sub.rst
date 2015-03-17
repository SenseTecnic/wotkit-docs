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
	  - **200 OK** if successful. A JSON object in the response body containing sensors subscribed by the user.

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
	  - **204 No Content** if successful.

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
	  - **204 No Content** if successful.

|
