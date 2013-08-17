.. _api_actuators:

.. index:: Actuators	

Sensor Control Channel: Actuators
=================================

An actuator is a sensor that uses a control channel to actuate things.  Rather than POSTing data to the WoTKit, an
actuator script or gateway polls the control URL for messages to affect the actuator, to do things like move a servo
motor, turn a light on or off, or display a message on a screen.  

To demonstrate actuators, the control visualization that comes with the WoTKit sends three type of events to the sensor control channel:

.. list-table::
	:widths: 10, 50
	:header-rows: 1
	
	* -
	  -
	* - **button**
	  - 'on' or 'off' to control a light, or switch.
	* - **message**
	  - text message for use by the actuator, for example to be shown on a message board or display.
	* - **slider**
	  - a numeric value to affect the position of something, such as a server motor.
  
|

Any name/value pair can be sent to an actuator in a message, these are just the names sent by the visualization. 


.. _send_actuator:

.. index:: Send Actuators Messages, Send Control Messages	

Sending Actuator Messages
-------------------------

To send a control message to a sensor (actuator), POST name value pairs corresponding to the data fields 
to the ``/sensors/{sensorname}/message`` URL.

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`sensors/{sensorname}/message`
	* - **Privacy**
	  - Public or Private
	* - **Format**
	  - json
	* - **Method**
	  - POST
	* - **Returns**
	  - On success, OK 200 (no content).
	  
|

.. _receive_actuator:

.. index:: Actuator Messages, Control Messages	
	seealso: Actuator Messages; Actuator Subscription
	seealso: Actuator Messages; Actuator Polling
	seealso: Control Messages; Controller Subscription
	seealso: Control Messages; Controller Polling

Receiving Actuator Messages
-----------------------------

In order to receive messages from an actuator, you must own that actuator.

.. _sub_actuator:

.. index:: Actuator Subscription, Controller Subscription

Subscribing to an Actuator Controller
#####################################

First, subscribe to the controller by POSTing to ``/api/control/sub/{sensor-name}``.
In return, we receive a json object containing a subscription id.


.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`control/sub/{sensor-name}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - POST
	* - **Returns**
	  - On success, OK 200 with JSON containing subscription id.
	  
|

Example subscription id returned:

.. code-block:: python

	{
		"subscription":1234
	}

.. _get_actuator:

.. index:: Actuator Polling, Controller Polling

Query an Actuator
###################
	
Using the subscription id, then poll the following resource:
``/api/control/sub/{subscription-id}?wait=10``. 
The ``wait`` specifies the time to wait in seconds for a control message.  
If unspecified, a default wait time of 10 seconds is used. The maximum wait time is 20 seconds.  
The server will respond on timeout, or when a control messages is received.

.. list-table::
	:widths: 10, 50

	* - **URL**
	  - :wotkit-api:`control/sub/{subscription-id}?wait={wait-time}`
	* - **Privacy**
	  - Private
	* - **Format**
	  - json
	* - **Method**
	  - GET
	* - **Returns**
	  - On success, OK 200 with JSON containing control messages.
	  
|

.. index:: Acuator Example

To illustrate, the following code snippet uses HTTP client libraries to subscribe and get actuator messages from 
the server, and then print the data.  Normally, the script would change the state of an actuator like a servo or a 
switch based on the message received.

.. code-block:: python

	# sample actuator code
	import urllib
	import urllib2
	import base64
	import httplib

	try:
		import json
	except ImportError:
		import simplejson as json 

	#note trailing slash to ensure .testactuator is not dropped as a file extension
	actuator="mike.testactuator/"

	# authentication setup
	conn = httplib.HTTPConnection("wotkit.sensetecnic.com")
	base64string = base64.encodestring('%s:%s' % ('{id}', '{password}'))[:-1]
	authheader =  "Basic %s" % base64string
	headers = {'Authorization': authheader}
		   
	#subscribe to the controller and get the subscriber ID
	conn.request("POST", "/api/control/sub/" + actuator, headers=headers)
	response = conn.getresponse()
	data = response.read()

	json_object = json.loads(data)
	subId = json_object['subscription']

	#loop to long poll for actuator messages
	while 1:
		print "request started for subId: " + str(subId)
		conn.request("GET", "/api/control/sub/" + str(subId) + "?wait=10", headers=headers)
		response = conn.getresponse()
		data = response.read()

		json_object = json.loads(data)

			# change state of actuator based on json message received
		print json_object

