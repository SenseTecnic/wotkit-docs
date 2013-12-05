===========
Sensor Data
===========

In the WoTKit, *sensor data* elements consist of a timestamp followed by one or more
named fields. By default, sensor data consists of the following fields.

.. list-table::
  :widths: 15, 50
  :header-rows: 1

  * - Name
    - Value Description
  * - timestamp
    - the time that the sensor data was collected.  This is an integer
      representing the number of milliseconds since Jan 1, 1970 UTC.
      Optional; if not supplied, a server-supplied timestamp will be used.
  * - sensor_id
    - the globally unique sensor id that produced the data.
  * - sensor_name
    - the globally unique sensor name, in the form ``{username}.{sensorname}``
  * - lat
    - the latitude location of the sensor in degrees (number).  Needed for map
      visualizations.
  * - lng
    - the longitude location of the sensor in degrees (number).  Needed for map
      visualizations.
  * - value
    - the primary value of the sensor data collected (number).  The default scalar value of the sensor used by visualizations.
  * - message
    - a text message, for example a twitter message (text) or a status message.

In addition to these default fields, additional fields can be added by updating
the *sensor fields* in the WoTKit UI or :ref:`api_sensor_fields` in the API.

.. note::
  Python's ``time.time()`` function generates the system time in *seconds*, not
  milliseconds. To convert this to an integer in milliseconds use
  ``int(time.time()*1000)``.

  In Javascript: ``var d = new Date(); d.getTime();``

  In Java: ``System.currentTime()``.

.. _send-data-label:

.. index:: Sensor Data Creation

Sending New Data
----------------

To send new data to a sensor, POST name value pairs corresponding to the data
fields to ``/sensors/{sensorname}/data``.  There is no need to supply the sensor id, or sensor name fields since the sensor
is specified in the URL.

If a timestamp is not provided in the request body, it will be set to the current time by the
the server.

To send new data:

.. list-table::
  :widths: 10, 50

  * - **URL**
    - :wotkit-api:`v2/sensors/{sensorname}/data`
  * - **Privacy**
    - Private
  * - **Format**
    - not applicable
  * - **Method**
    - POST
  * - **Returns**
    - HTTP status code; No Response 201 (Created) if successful


.. admonition:: Example

  .. parsed-literal::

      curl --user {id}:{password} --request POST -d value=5 -d lng=6 -d lat=7
      ':wotkit-api:`sensors/test-sensor/data`'


.. _send-bulk-data-label:

.. index:: Bulk Sensor Data
  pair: Sensor Data Creation; Bulk Sensor Data

Updating a Range of Historical Data
----------------------------------

To insert or update a range of historical data, you PUT data (rather than POST) data into the system.
Note that data PUT into the WoTKit will not be processed in real time, since it
occurred in the past.

* The request body must be a list of JSON objects containing a timestamp value.
* Any existing data within this timestamp range will be
  deleted and replaced by the data supplied.

To update data:

.. list-table::
  :widths: 10, 50

  * - **URL**
    - :wotkit-api:`v2/sensors/{sensorname}/data`
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

.. _api-v2-get-single-data:

Retrieving a Single Data Item
-----------------------------
If you know the data element's id, you can query for a single data element using
the following query.

.. list-table::
  :widths: 10, 50

  * - **URL**
    - :wotkit-api:`v2/sensors/{sensor-name}/data/{data_id}`
  * - **Privacy**
    - Public or Private, depending on sensor privacy
  * - **Format**
    - json
  * - **Method**
    - GET
  * - **Returns**
    - On success, OK 200 with a list of timestamped data records.


.. _api-v2-data-query:

Retrieving Data Using Query
---------------------------
To retrive sensor data over a time range you can use the following endpoint. An
interactive guide on how to use this endpoint is available at:
:doc:`../guides/sensor_data_query`.


.. list-table::
  :widths: 10, 50

  * - **URL**
    - :wotkit-api:`v2/sensors/{sensor-name}/data`
  * - **Privacy**
    - Public or Private, depending on sensor privacy
  * - **Format**
    - json
  * - **Method**
    - GET
  * - **Returns**
    - On success, OK 200 with a list of timestamped data records.

The query parameters supported are the following. They can only be used
together if they appear in the same ``Grouping``.


.. list-table::
  :widths: 15, 10, 15, 40
  :header-rows: 1

  * - Parameter
    - Group
    - Type
    - Description
  * - ``recent_t``
    - 1
    - integer
    - Gets the elements up to recent_t milliseconds ago
  * - ``recent_n``
    - 2
    - integer
    - Gets the n recent elements
  * - ``start``
    - 3
    - timestamp
    - The absolute starting point (in milliseconds since Jan 1, 1970).
  * - ``start_id``
    - 3
    - id
    - The starting id of sensor_data at timestamp ``start``. Used for paging.
  * - ``end``
    - 3
    - timestamp
    - The absolute ending timestamp (in milliseconds since Jan 1, 1970)
  * - ``end_id``
    - 3
    - timestamp
    - The end id of sensor_data with timestamp ``end``. Used for paging.
  * - ``limit``
    - [2,3]
    - integer
    - specifies how many datapoints to see on each response
  * - ``offset``
    - 3
    - integer
    - controls paging of elements in conjunction with

Delete Data by Id
-----------------
Same as :ref:`api-v2-get-single-data` instead using HTTP Delete.

.. list-table::
  :widths: 10, 50

  * - **URL**
    - :wotkit-api:`v2/sensors/{sensorname}/data/{data_id}`
  * - **Privacy**
    - Private
  * - **Format**
    - not applicable
  * - **Method**
    - DELETE
  * - **Returns**
    - HTTP status code; No Response 204 if successful

Delete Data using Data Query
----------------------------
Can delete using query parameters in :ref:`api-v2-data-query` with the
restriction on only using **group 3** parameters.

.. list-table::
  :widths: 10, 50

  * - **URL**
    - :wotkit-api:`v2/sensors/{sensorname}/data`
  * - **Privacy**
    - Private
  * - **Format**
    - not applicable
  * - **Method**
    - DELETE
  * - **Returns**
    - HTTP status code; No Response 204 if successful


